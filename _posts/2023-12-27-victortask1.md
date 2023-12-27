---
layout: default
title:  "Задание 1 викторины Техногуру 2023"
date:   2023-12-27 01:00:00 +0300
categories: victorina
---

Пошаговый план реализации

## ТЗ:

> 🔹 ЗАДАЧА
>
> Настроить своевременную выдачу доступов с сохранением ограничений. Распределить автоматически участников по чатам Телеграм так, чтобы по завершении курса чат для них закрылся через 10 дней.
> 
> 🔹ДАНО
> 
> Тренинг, в котором есть 4 подтренинга:
> 1. Ранний старт
> 2. Модуль 1
> 3. Модуль 2
> 4. Модуль 3
>
> В каждом модуле по 7 уроков.
> 
> 🔹О ДОСТУПАХ
>
> В курсе есть 2 пакета:
>
> ➡️ “Без обратной связи” — не может отвечать и не видит ответов других
>
> ➡️ “С обратной связью”
> 
> Ученик получает доступы в следующем порядке:
> 1. До старта курса после оплаты — доступ к блоку “Ранний старт”
> 2. В день старта курса открывается доступ к Модулю 1
> 3. Доступ к Модулю 2 выдается в том случае, когда куратором принят крайний стоп-урок в Модуле 1.
> 4. Доступы к Модулю 3 выдаются по той же схеме, что и доступ к Модулю 2 - после принятия крайнего стоп-урока в предшествующем модуле.
>
> ➡️ Для тарифа "Без обратной связи" доступы к следующим модулям открываются только при прохождении предыдущего, т.е. все уроки не должны быть доступны сразу. Все уроки в модуле пройдены — открывается следующий. 
> 
> Старт курса — 12 января. Уроки открываются в день старта.
> 
> Доступ к материалам курса ограничены: 
>
> 🔵Для пакета “Без обратной связи” — 1 месяц. Доступа к чатам Телеграм нет.
> 
> 🔵Для пакета “С обратной связью” - 3 месяца. Есть чат на 3 месяца + 10 дней.
> 
> При распределении в чат есть ограничения — максимум 15 участников. Всего купивших тренинг "С обратной связью" будет 120 человек. В каждом чате есть куратор. После распределения он должен получить уведомление с составом своей группы.
> 
> 🔹ЗАДАНИЕ
>
> ➡️ Настроить своевременную выдачу доступов с сохранением ограничений (с обратной связью и без). Условие: тренинг один, никаких дублей делать нельзя. 
>
> ➡️ Настроить распределение в чат участников.
> 
> 🔹ФОРМАТ СДАЧИ ОТВЕТОВ
>
> Полная описанная схема решения задания отправляется админам чата.
>
> Результат и баллы будут начислены и оглашены в финале. Админ только даст ответ, принято задание или нет.
> 
> 🗓 ДЕДЛАЙН
>
> Четверг, 28 декабря, 12:00











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
