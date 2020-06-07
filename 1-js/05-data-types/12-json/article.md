# Формат JSON, метод toJSON

Скажімо, у нас є складний об’єкт, і ми хотіли б перетворити його в рядок, щоб надіслати його по мережі або просто вивести для логування.

Звісно, такий рядок повинен містити всі важливі властивості.

Ми могли б реалізувати перетворення так:

```js run
let user = {
  name: "John",
  age: 30,

*!*
  toString() {
    return `{name: "${this.name}", age: ${this.age}}`;
  }
*/!*
};

alert(user); // {name: "John", age: 30}
```

...Але в процесі розробки додаються нові властивості, старі властивості перейменовуються і видаляються. Оновлення таких `toString` що разу може стати проблемою. Ми можемо спробувати перебрати властивості в ньому, але що як об'єкт складний і в його властивостях містяться вкладені об'єкти? Нам потрібно було б здійснити їх конверсію також.

На щастя, не потрібно писати код, щоб обробити все це. Завдання вже має рішення.

## JSON.stringify

[JSON](http://uk.wikipedia.org/wiki/JSON) (JavaScript Object Notation) є загальним форматом для представлення значень та об’єктів. Його опис задокументовано в стандарті [RFC 4627](http://tools.ietf.org/html/rfc4627). Спочатку він робився для JavaScript, але багато інших мов також мають бібліотеки, які можуть працювати з ним.  Тому JSON легко використовувати для обміну даними, коли клієнт використовує JavaScript, а сервер написаний на Ruby/PHP/Java або будь-якій іншій мові.

JavaScript надає методи:

- `JSON.stringify` для перетворення об’єктів у JSON.
- `JSON.parse` для перетворення JSON назад в об’єкт.

Наприклад, тут ми перетворимо через `JSON.stringify` дані студента:
```js run
let student = {
  name: 'John',
  age: 30,
  isAdmin: false,
  courses: ['html', 'css', 'js'],
  wife: null
};

*!*
let json = JSON.stringify(student);
*/!*

alert(typeof json); // ми отримали рядок!

alert(json);
*!*
/* виведе об’єкт в форматі JSON:
{
  "name": "John",
  "age": 30,
  "isAdmin": false,
  "courses": ["html", "css", "js"],
  "wife": null
}
*/
*/!*
```

Метод `JSON.stringify(student)` бере об’єкт і перетворює його в рядок.

Отриманий рядок `json` зветься *JSON-форматований* або *серіалізований* об'єкт. Ми готові надіслати його по мережі або помістити в звичайне сховище даних.


Зауважте, що JSON-encoded об’єкт має кілька важливих відмінностей від об’єктного літерала:

- Рядки використовують подвійні лапки. У JSON немає жодних одинарних чи зворотних лапок. Тому `'John'` стає `"John"`.
- Імена властивостей об’єкта також у подвійних лапках. Це обов’язково. Тому `age:30` стає `"age":30`.

`JSON.stringify` може застосовуватись і до примітивів.

JSON підтримує наступні типи даних:

- Об’єкти `{ ... }`
- Масиви `[ ... ]`
- Примітиви:
    - рядки,
    - числа,
    - булеві значення `true/false`,
    - `null`.

Наприклад:

```js run
// число в JSON залишається числом
alert( JSON.stringify(1) ) // 1

// рядок у JSON все ще є рядком, але у подвійних лапках
alert( JSON.stringify('test') ) // "test"

alert( JSON.stringify(true) ); // true

alert( JSON.stringify([1, 2, 3]) ); // [1,2,3]
```

JSON є не залежною від мови специфікацєю для даних, тому `JSON.stringify` пропускає деякі специфічні властивості об'єктів JavaScript.

А саме:

- Властивості-функції (методи).
- Символічні властивості.
- Властивості, які зберігають `undefined`.

```js run
let user = {
  sayHi() { // ігнорується
    alert("Hello");
  },
  [Symbol("id")]: 123, // ігнорується
  something: undefined // ігнорується
};

alert( JSON.stringify(user) ); // {} (порожній об’єкт)
```

Зазвичай це нормально. Якщо це не те, що ми хочемо, то незабаром ми побачимо, як налаштувати процес.

Чудова річ, що вкладені об’єкти підтримуються та перетворюються автоматично.

Наприклад:

```js run
let meetup = {
  title: "Conference",
*!*
  room: {
    number: 23,
    participants: ["john", "ann"]
  }
*/!*
};

alert( JSON.stringify(meetup) );
/* Вся структура преретворена в рядок:
{
  "title":"Conference",
  "room":{"number":23,"participants":["john","ann"]},
}
*/
```

Важливе обмеження: не повинно бути кругових посилань.

Наприклад:

```js run
let room = {
  number: 23
};

let meetup = {
  title: "Conference",
  participants: ["john", "ann"]
};

meetup.place = room;       // meetup посилається на room
room.occupiedBy = meetup; // room посилається на meetup

*!*
JSON.stringify(meetup); // Помилка: Перетворення кругової структури у JSON
*/!*
```

Тут конверсія не вдається через кругові посилання: `room.occupiedBy` посилається на `meetup`, і `meetup.place` посилається на `room`:

![](json-meetup.svg)


## Виключаєм та перетворюємо: replacer

Повний синтаксис `JSON.stringify`:

```js
let json = JSON.stringify(value[, replacer, space])
```

value
: Значення для кодування.

replacer
: Масив властивостей для кодування або функції відображення `function(key, value)`.

space
: Кількість місця, яке буде використано для форматування

У більшості випадків, `JSON.stringify` використовується лише з першим аргументом. Але якщо нам потрібно налагодити процес заміни, як от відфільтрувати кругові посилання, ми можемо використовувати другий аргумент `JSON.stringify`.

Якщо ми передамо йому масив властивостей, кодуються лише ці властивості.

Наприклад:

```js run
let room = {
  number: 23
};

let meetup = {
  title: "Conference",
  participants: [{name: "John"}, {name: "Alice"}],
  place: room // meetup посилається на room
};

room.occupiedBy = meetup; // room посилається на meetup

alert( JSON.stringify(meetup, *!*['title', 'participants']*/!*) );
// {"title":"Conference","participants":[{},{}]}
```

Тут ми, мабуть, занадто суворі. Список властивостей застосовується до всієї структури об’єкта. Тож об’єкти в `participants` порожні, оскільки `name` немає в списку.

Додамо до списку всі властивості, за винятком `room.occupiedBy` яка призвела б до кругового посилання:

```js run
let room = {
  number: 23
};

let meetup = {
  title: "Conference",
  participants: [{name: "John"}, {name: "Alice"}],
  place: room // meetup посилається на room
};

room.occupiedBy = meetup; // room посилається на meetup

alert( JSON.stringify(meetup, *!*['title', 'participants', 'place', 'name', 'number']*/!*) );
/*
{
  "title":"Conference",
  "participants":[{"name":"John"},{"name":"Alice"}],
  "place":{"number":23}
}
*/
```

Тепер все, окрім `occupiedBy` серіалізовано. Але перелік властивостей досить довгий.

На щастя, в якості `replacer` ми можемо використовувати функцію, а не масив.

Функція буде викликана для кожної пари `(key, value)`, і повинна повернути значення "replaced", яке буде використовуватися замість початкового. Або `undefined`, якщо значення потрібно буде пропустити.

У нашому випадку ми можемо повернути `value` "як є" для всього, крім `occupiedBy`. Щоб ігнорувати `occupiedBy`, наведений код нижче повертає `undefined`:

```js run
let room = {
  number: 23
};

let meetup = {
  title: "Conference",
  participants: [{name: "John"}, {name: "Alice"}],
  place: room // meetup посилається на room
};

room.occupiedBy = meetup; // room посилається на meetup

alert( JSON.stringify(meetup, function replacer(key, value) {
  alert(`${key}: ${value}`);
  return (key == 'occupiedBy') ? undefined : value;
}));

/* пари ключ:значення, які приходят в replacer:
:             [object Object]
title:        Conference
participants: [object Object],[object Object]
0:            [object Object]
name:         John
1:            [object Object]
name:         Alice
place:        [object Object]
number:       23
*/
```

Будь ласка, зверніть увагу, що `replacer` функція отримує кожну пару ключ/значення, включаючи вкладені об’єкти та елементи масиву. Вона застосовується рекурсивно. Значення `this` всередині `replacer` - це об’єкт, який містить поточну властивість.

Перший виклик є особливим. Він виготовляється за допомогою спеціального "wrapper object": `{"": meetup}`. Іншими словами, перша `(key, value)` пара має порожній ключ, а значенням є цільовим об’єктом у цілому. Ось чому перший рядок з прикладу вище буде `":[object Object]"`.

Ідея полягає в тому, щоб дати якомога більше можливостей для `replacer` - у нього є можливість проаналізувати і замінити/пропустити навіть весь об’єкт, якщо це необхідно.


## Форматування: space

Третій аргумент `JSON.stringify(value, replacer, space)` - це кількість пропусків, які використовуються для зручного форматування.

Раніше, у всіх JSON-форматованих об’єктах не було відступів чи зайвих пропусків. Це нормально, якщо ми хочемо надіслати об’єкт по мережі. Аргумент `space` використовується виключно для приємного виводу.

Нижче `space = 2` каже JavaScript показувати вкладені об’єкти в декількох рядках з відступом у 2 пропуски всередині об’єкта:

```js run
let user = {
  name: "John",
  age: 25,
  roles: {
    isAdmin: false,
    isEditor: true
  }
};

alert(JSON.stringify(user, null, 2));
/* відступ у 2 пропуски:
{
  "name": "John",
  "age": 25,
  "roles": {
    "isAdmin": false,
    "isEditor": true
  }
}
*/

/* for JSON.stringify(user, null, 4) результат містить більше відступів:
{
    "name": "John",
    "age": 25,
    "roles": {
        "isAdmin": false,
        "isEditor": true
    }
}
*/
```

Параметр `space` використовується виключно для ведення логування та приємного виводу.

## Користувальницький "toJSON"

Як і `toString` для перетворення рядків, об’єкт може надати метод `toJSON` для перетворення в JSON. `JSON.stringify` автоматично виклече його, якщо він є.

Наприклад:

```js run
let room = {
  number: 23
};

let meetup = {
  title: "Conference",
  date: new Date(Date.UTC(2017, 0, 1)),
  room
};

alert( JSON.stringify(meetup) );
/*
  {
    "title":"Conference",
*!*
    "date":"2017-01-01T00:00:00.000Z",  // (1)
*/!*
    "room": {"number":23}               // (2)
  }
*/
```

Тут ми можемо побачити, що `date` `(1)` став рядком. Це тому, що всі об’єкти типу `dates` мають вбудований метод `toJSON`, який повертає такий тип рядка.

Тепер додамо власну реалізацію методу `toJSON` в наш об’єкт `room` `(2)`:

```js run
let room = {
  number: 23,
*!*
  toJSON() {
    return this.number;
  }
*/!*
};

let meetup = {
  title: "Conference",
  room
};

*!*
alert( JSON.stringify(room) ); // 23
*/!*

alert( JSON.stringify(meetup) );
/*
  {
    "title":"Conference",
*!*
    "room": 23
*/!*
  }
*/
```

Як ми бачимо, `toJSON` використовується як для прямого виклику `JSON.stringify(room)` і коли `room` вкладено в інший кодований об’єкт.


## JSON.parse

Щоб розшифрувати JSON-рядок, нам потрібен інший метод з назвою [JSON.parse](mdn:js/JSON/parse).

Синтаксис:
```js
let value = JSON.parse(str, [reviver]);
```

str
: JSON для перетворення в об’єкт.

reviver
: Необов’язкова функція, яка буде викликана для кожної пари `(key, value)` і можуть перетворити значення.

Наприклад:

```js run
// рядковий масив
let numbers = "[0, 1, 2, 3]";

numbers = JSON.parse(numbers);

alert( numbers[1] ); // 1
```

Або для вкладених об’єктів:

```js run
let userData = '{ "name": "John", "age": 35, "isAdmin": false, "friends": [0,1,2,3] }';

let user = JSON.parse(userData);

alert( user.friends[1] ); // 1
```

JSON може бути настільки складним, наскільки це необхідно, об’єкти та масиви можуть включати інші об'єкти та масиви. Але вони повинні відповідати тому ж формату JSON.

Ось типові помилки в написаному від руки JSON (іноді доводиться писати його для цілей налагодження):

```js
let json = `{
  *!*name*/!*: "John",                     // Помилка: ім’я властивості без лапок
  "surname": *!*'Smith'*/!*,               // Помилка: одиничні лапки в значенні (повинні бути подвійні)
  *!*'isAdmin'*/!*: false                  // Помилка: одиничні лапки в ключі (повинні бути подвійні)
  "birthday": *!*new Date(2000, 2, 3)*/!*, // Помилка: не допускається конструктор "new", тільки значення
  "friends": [0,1,2,3]              // тут все добре
}`;
```

Крім того, JSON не підтримує коментарів. Додавання коментаря до JSON робить його недійсним.

Є ще один формат [JSON5](http://json5.org/), який підтримує ключі без лапок, коментарі і т.д. Але це окрема бібліотека, а не специфікація мови.

Звичайний JSON такий суворий не тому, що його розробники ледачі, а тому, що дозволяє легко, надійно та дуже швидко реалізовувати алгоритм кодування і читання.

## Використання reviver

Уявіть, що ми отримали об'єкт `meetup` з сервера у вигляду рядка даних.

Це виглядає приблизно так:

```js
// title: (meetup title), date: (meetup date)
let str = '{"title":"Conference","date":"2017-11-30T12:00:00.000Z"}';
```

...А тепер нам потрібно *десеріалізувати* її, тобто повернути назад в об’єкт JavaScript.

Давайте це зробимо, викликавши `JSON.parse`:

```js run
let str = '{"title":"Conference","date":"2017-11-30T12:00:00.000Z"}';

let meetup = JSON.parse(str);

*!*
alert( meetup.date.getDate() ); // Помилка!
*/!*
```

Ой! Помилка!

Значенням `meetup.date` є рядок, а не `Date` об'єкт. Як `JSON.parse` міг знати що він повинен перетворити цей рядок в `Date`?

Давайте передамо `JSON.parse` функцію відродження як другий аргумент, яка повертає всі значення "як є", але `date` стане `Date`:

```js run
let str = '{"title":"Conference","date":"2017-11-30T12:00:00.000Z"}';

*!*
let meetup = JSON.parse(str, function(key, value) {
  if (key == 'date') return new Date(value);
  return value;
});
*/!*

alert( meetup.date.getDate() ); // тепер працює!
```

До речі, це працює і для вкладених об'єктів:

```js run
let schedule = `{
  "meetups": [
    {"title":"Conference","date":"2017-11-30T12:00:00.000Z"},
    {"title":"Birthday","date":"2017-04-18T12:00:00.000Z"}
  ]
}`;

schedule = JSON.parse(schedule, function(key, value) {
  if (key == 'date') return new Date(value);
  return value;
});

*!*
alert( schedule.meetups[1].date.getDate() ); // Працює!
*/!*
```



## Підсумок

- JSON - це формат даних, який має власний незалежний стандарт і бібліотеки для більшості мов програмування.
- JSON підтримує звичайні об'єкти, масиви, рядки, числа, булеві значення і `null`.
- JavaScript надає методи [JSON.stringify](mdn:js/JSON/stringify) для серіалізації в JSON і [JSON.parse](mdn:js/JSON/parse) для читання з JSON.
- Обидва способи підтримують функції перетворення для розумного читання/запису.
- Якщо об’єкт має метод `toJSON`, то його викликають через `JSON.stringify`.
