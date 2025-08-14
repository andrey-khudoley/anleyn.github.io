---
layout: default
title:  "Интеграция GetCourse и Salebot"
date:   2025-08-13 15:00:00 +0300
categories: [getcourse, salebot]
tags: [gc, sb, api, chatium]
---
Подробная заметка о том, почему интегрировать сервисы между собой важно.

# Введение

## Почему?

Итак, пользователь сделал заказ. Потом пришёл ещё один заказ и ещё. Пришло время строить дашборд с показателями эффективности. Что мы можем посмотреть?

Допустим, мы прокидывали UTM-метки через параметры к ссылкам. Допустим, что мы смогли составить регламент по UTM-меткам грамотно. Но всё, что это даёт нам - понимание, откуда этот лид пришёл изначально. Каким был его первый переход к нам.

Современные системы гораздо сложнее тех, что были 2-3 года назад и требует горздо большей деталиации входящих данных уже хотя бы потому, что трафик дорожает и расход рекламных ресурсов требует оптимизации. 

## Что?

И здесь вступает в игру сквозная аналитика. 

По сути, когда мы говорим «сквозная аналитика» - мы подразумеваем передачу единого ключа через весь путь клиента независимо от того, через какие сервисы этот путь проходит. 

Имея на руках этот ключ/id/email мы можем сделать запрос к любому сервису по пути, запросив историю действий, взаимодействий с его элементами, любую другую информацию, которая в нём хранится.

## Как?

На примере далее я хочу показать, каким образом можно выбрать ключевое поле и связать между собой Salebot и Getcourse.

Для этого я планирую использовать Chatium IDE, который доступен в любом аккаунте на GC бесплатно. Однако то, что я сделаю, можно повторить, используя функционал модуля «Воронки», не прибегая к использованию кода.

# Реализация

## Выбор подхода

Для начала нам надо определиться, что и с чем мы связываем. Здесь есть несколько вариантов:
 * ID пользователя в Telegram;
 * ID пользователя в Salebot;
 * ID пользователя в GetCourse;
 * EMail;
 * Телефон.
Как выбрать тот, что нам подходит лучше всего?

Во-первых ключ должен прокидываться в минимальное количество шагов. 

Например, можно добавить telegram-id в параметрах к каждой ссылке. 
Или сделать то же самое с ID пользователя в Salebot.

Здесь важно подумать о том, что будет, если пользователь попытается передать несколько ключей в один GC-аккаунт. Я подобной вероятностью пренебрёг и не сферических коней в вакууме не рассматриваю.

В обратную сторону можно передавать email или ID пользователя в Getcourse, по которым и пробрасывать данные.

Я выбру для себя вариант с email, т.к. в контексте моей задачи пользователи будут обращаться в службу поддержки, но не будут иметь большого количества ссылок для перехода из бота в GC.

## Создание интеграции из GC в SB

Итак, шаг первый. Пользователь обращается к боту.

![First_step](https://files.khudoley.pro/static/img/2025-08-13_165030.png)

Здесь мы проверяем, что сообщение отправлено впервые и предупреждаем юзера о том, что бота надо связать с GetCourse. 

На этом моменте можно добавить ID Телеграма в параметры к URL, чтобы пробросить. Но это не вполне надёжно, т.к. некоторые блокировщики рекламы разделываются с такими метками и они теряются. Поэтому мы используем дополнительный запрос далее для надёжности.

А пока нам понадоятся: прокси-ссылка на Сэйлботе и блок, который запустится при переходе по ней.

![Success_message](https://files.khudoley.pro/static/img/2025-08-13_165401.png)

![Success_page](https://files.khudoley.pro/static/img/2025-08-13_165535.png)

А также понадобится форма, которая прокинет email на эту страницу

![Success_page](https://files.khudoley.pro/static/img/2025-08-13_165843.png)

Готово. Теперь GC отправляет email в SB и SB может работать с ним. Именно то. что мы хотели.

## Создание интеграции из SB в GC

Имея email мы можем обращаться к конкретному пользователю на GC, но GC пока не может обращаться к пользователю на SB в ответ, поскольку ещё не имеет никакой информации для этого.

Чтобы сделать это возможным, нужно отправить в дополнительное поле пользователя ключ пользователя в Salebot. Мы будем для этого использовать telegram id, как нечто более универсальное. Проект может смениться и client_id тоже сменится. Но по ID Телеграма мы по прежнему сможем достучаться до пользователя. 

Для того, чтобы передать данные - нам надо иметь URL, на который мы эти данные отправим. Эндпоинт. Мы создадим его, используя IDE платформы Chatium.

```typescript
// @ts-ignore
import { getUserFields, updateUserFields, setUserCustomFields } from '@getcourse/sdk';
import { Debug } from '../../../lib/debug.lib';

// ---- Конфиг логирования ----
const LOG_LEVEL = 'info' as const;
const LOG_PREFIX = 'telegram_id_update';
Debug.setLogPrefix(LOG_PREFIX);

// ---- Типы входа/выхода ----
interface RequestBody {
  email: string;
  telegram_id: string;
}

type JsonResult = { success: true } | { success: false; error: string };

// ---- Вспомогательные типы ----
interface UserCustomFieldMeta {
  name: string;
  value: unknown;
  type: string;
  units: string | null;
}

type UserCustomFieldsResult = Record<string, UserCustomFieldMeta>;

interface GcUserInfo {
  id: number;
  custom: UserCustomFieldsResult;
}

// ---- Утилиты ----
const norm = (v: unknown) => String(v ?? '').trim();
const lower = (v: unknown) => norm(v).toLowerCase();

// ---- Эндпоинт ----
app.post('/', async (ctx, req): Promise<JsonResult> => {
  try {
    const { email, telegram_id } = (req?.body ?? {}) as Partial<RequestBody>;

    if (!email || !telegram_id) {
      new Debug(ctx, 'email и telegram_id обязательны', LOG_LEVEL, 'warn', 'BAD_REQUEST');
      return { success: false, error: 'email и telegram_id обязательны' };
    }

    new Debug(ctx, `Старт обновления telegram_id для ${email}`, LOG_LEVEL, 'info');

    // 1) Берём поля пользователя по email
    const user = (await getUserFields(ctx, { email })) as GcUserInfo | null;
    if (!user) {
      new Debug(ctx, `Пользователь не найден по email=${email}`, LOG_LEVEL, 'warn', 'USER_NOT_FOUND');
      return { success: false, error: 'user_not_found' };
    }

    // 2) Ищем ID доп.поля с именем "telegram_id"
    const custom = user.custom ?? {};
    const found = Object.entries(custom).find(([, meta]) => lower(meta?.name) === 'telegram_id');

    if (!found) {
      new Debug(ctx, `Доп. поле 'telegram_id' не найдено у user_id=${user.id}`, LOG_LEVEL, 'warn', 'FIELD_NOT_FOUND');
      return { success: false, error: 'field_telegram_id_not_found' };
    }

    const [fieldId] = found;

    // 3) Обновляем значение поля
    await setUserCustomFields(ctx, {
      email,
      fields: { [fieldId]: String(telegram_id) },
    });

    new Debug(ctx, `Обновлено: user_id=${user.id}, field_id=${fieldId}`, LOG_LEVEL, 'info');
    return { success: true };
  } catch (e) {
    new Debug(ctx, e instanceof Error ? e.message : String(e), LOG_LEVEL, 'error', 'INTERNAL_ERROR');
    return { success: false, error: 'internal_error' };
  }
});
```

Этот код регистрирует эндпоинт, который ожидает входящий POST-запрос. В теле пост-запроса ожидаются два ключа: 
 * email - определяет пользователя, для которого мы запишем данные;
 * telegram_id - сам id, который мы собираемся записать в поле на GC.

 Итоговый запрос будет выглядеть следующим образом:
 * URL: https://domain/path/update_telegram_id (путь к tsx-файлу, в котором размещён наш код)
 * Body:
 ```typescript
 {
    "email": "email@domain.mail",
    "telegram_id": "12345678"
 }
 ```

Остаётся направить эти данные прямо из Salebot сразу, как только email появился у пользователя.














====================================
Пост содержит большинство элементов разметки markdown


Text can be **bold**, _italic_, or ~~strikethrough~~.

[Link to another page](./another-page.html).

There should be whitespace between paragraphs.

There should be whitespace between paragraphs. We recommend including a README, or a file with information about your project.

# Header 1

This is a normal paragraph following a header. GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere.

## Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

### Header 3

```js
// Javascript code with syntax highlighting.
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
}
```

```ruby
# Ruby code with syntax highlighting
GitHubPages::Dependencies.gems.each do |gem, version|
  s.add_dependency(gem, "= #{version}")
end
```

#### Header 4

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Small image

![Octocat](https://github.githubassets.com/images/icons/emoji/octocat.png)

### Large image

![Branching](https://zamanilka.ru/wp-content/uploads/2019/03/5120x2880-UHD.jpg)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```
