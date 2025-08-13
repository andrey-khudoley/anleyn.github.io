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

## Подготовка










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
