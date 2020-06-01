# Destructuring assignment
Дві найбільш часто використовувані структури даних в JavaScript - це Objectі Array.

Об'єкти дозволяють нам створювати єдиний об'єкт, який зберігає елементи даних по ключам, а масиви дозволяють нам збирати елементи даних в впорядковану колекцію.

Але коли ми передаємо їх функції, їй може знадобитися не об'єкт / масив в цілому, а окремі частини.

Руйнівна присвоювання - це спеціальний синтаксис, який дозволяє нам «розпаковувати» масиви або об'єкти в купу змінних, що іноді буває зручніше. Руйнування також прекрасно працює зі складними функціями, які мають багато параметрів, значень за замовчуванням і так далі.

## Array destructuring

Приклад того, як масив розбитий на змінні:

// у нас є масив з ім'ям і прізвищем

let arr = ["Ilya", "Kantor"]

*!*
// деструктурірующее призначення
// встановлює firstName = arr[0]
// і прізвище = arr[1]
let [firstName, surname] = arr;
*/!*

alert(firstName); // Илья
alert(surname);  // Kantor
```

Тепер ми можемо працювати зі змінними замість членів масиву.

Він відмінно виглядає в поєднанні з splitілі іншими методами, які повертають масив:

`` `Js
let [firstName, surname] = "Ilya Kantor".split(' ');
```

````smart header="\"Destructuring\" does not mean \"destructive\"."
Це називається «деструктурірующім привласненням», тому що воно «деструктурірует» шляхом копіювання елементів в змінні. Але сам масив не змінюється.

Це просто більш короткий спосіб написати:
```js
// let [firstName, surname] = arr;
let firstName = arr[0];
let surname = arr[1];
```
````

````smart header="Ignore elements using commas"
Небажані елементи масиву також можуть бути викинуті через кому:
```js run
*!*
// другий елемент не потрібен
let [firstName, , title] = ["Julius", "Caesar", "Consul", "of the Roman Republic"];
*/!*

alert( title ); // Consul
```

У наведеному вище коді другий елемент масиву пропущений, третій елемент призначений на `title`, а інші елементи масиву також пропущені (оскільки для них немає змінних).
`` ``

````smart header="Works with any iterable on the right-side"

...Насправді, ми можемо використовувати його з будь-якими повторюваними, не тільки масивами:
```js
let [a, b, c] = "abc"; // ["a", "b", "c"]
let [one, two, three] = new Set([1, 2, 3]);
```

````


````smart header="Assign to anything at the left-side"

Ми можемо використовувати будь-які «призначаються об'єкти» на лівій стороні.

Наприклад, властивість об'єкта:
```js run
let user = {};
[user.name, user.surname] = "Ilya Kantor".split(' ');

alert(user.name); // Ilya
```

````

````smart header="Looping with .entries()"

У попередньому розділі ми бачили метод [Object.entries (obj)] (mdn: js / Object / records).

Ми можемо використовувати його з деструктуризація для циклічного перебору ключів і значень об'єкта:
`` `Js run
let user = {
  name: "John",
  age: 30
};

// цикл по ключам і значенням
*!*
for (let [key, value] of Object.entries(user)) {
*/!*
  alert(`${key}:${value}`); // name:John, then age:30
}
```

...І те ж саме для карти:

```js run
let user = new Map();
user.set("name", "John");
user.set("age", "30");

*!*
for (let [key, value] of user) {
*/!*
  alert(`${key}:${value}`); // name:John, then age:30
}
```
````
### The rest '...'

Якщо ми хочемо не просто отримати перші значення, а й зібрати все, що слід, - ми можемо додати ще один параметр, який отримує «інше», використовуючи три точки `" ... "`:
```js run
let [name1, name2, *!*...rest*/!*] = ["Julius", "Caesar", *!*"Consul", "of the Roman Republic"*/!*];

alert(name1); // Julius
alert(name2); // Caesar

*!*
// Зверніть увагу, що типом `rest` є Array.
alert(rest[0]); // Consul
alert(rest[1]); // of the Roman Republic
alert(rest.length); // 2
*/!*
```

Значення `rest` - це масив елементів, що залишилися масиву. Ми можемо використовувати будь-яке інше ім'я змінної замість `rest`, просто переконайтеся, що перед ним три точки і воно йде останнім в призначенні деструктуризации.
### Default values

Якщо в масиві менше значень, ніж змінних в привласненні, помилки не буде. Відсутні значення вважаються невизначеними:
```js run
*!*
let [firstName, surname] = [];
*/!*

alert(firstName); // undefined
alert(surname); // undefined
```

Якщо ми хочемо, щоб значення «за замовчуванням» замінило відсутнє, ми можемо надати його за допомогою `=`:
```js run
*!*
// default values
let [name = "Guest", surname = "Anonymous"] = ["Julius"];
*/!*

alert(name);    // Julius (from array)
alert(surname); // Anonymous (default used)
```

Значення за замовчуванням можуть бути більш складними виразами або навіть викликами функцій. Вони оцінюються, тільки якщо значення не вказано.

Наприклад, тут ми використовуємо функцію `prompt` для двох значень за замовчуванням. Але він буде працювати тільки для зниклого:
```js run
// runs only prompt for surname
let [name = prompt('name?'), surname = prompt('surname?')] = ["Julius"];

alert(name);    // Julius (from array)
alert(surname); // whatever prompt gets
```



## Object destructuring

Призначення деструктурірованіе також працює з об'єктами.

Основний синтаксис:

```js
let {var1, var2} = {var1:…, var2:…}
```

У нас є існуючий об'єкт з правого боку, який ми хочемо розділити на змінні. Ліва сторона містить «шаблон» для відповідних властивостей. У простому випадку це список імен змінних в `{...}`.
For instance:

```js run
let options = {
  title: "Menu",
  width: 100,
  height: 200
};

*!*
let {title, width, height} = options;
*/!*

alert(title);  // Menu
alert(width);  // 100
alert(height); // 200
```

Властивості `options.title`,` options.width` і `options.height` присвоюються відповідним змінним. Порядок не має значення. Це теж працює:

`` `JS
// змінив порядок вlet {...}
let {height, width, title} = { title: "Menu", height: 200, width: 100 }
```

Шаблон з лівого боку може бути більш складним і визначати відповідність між властивостями і змінними.

Якщо ми хочемо привласнити властивість змінної з іншим ім'ям, наприклад, `options.width`, щоб перейти в змінну з іменем` w`, то ми можемо встановити його за допомогою двокрапки:
```js run
let options = {
  title: "Menu",
  width: 100,
  height: 200
};

*!*
// { sourceProperty: targetVariable }
let {width: w, height: h, title} = options;
*/!*

// width -> w
// height -> h
// title -> title

alert(title);  // Menu
alert(w);      // 100
alert(h);      // 200
```

Двокрапка показує «що: куди йде». У наведеному вище прикладі властивість `width` переходить К` w`, властивість` height` переходить К` h`, а для `title` присвоюється той же ім'я.

Для потенційно відсутніх властивостей ми можемо встановити значення за замовчуванням, використовуючи `" = "`, наприклад так:
```js run
let options = {
  title: "Menu"
};

*!*
let {width = 100, height = 200, title} = options;
*/!*

alert(title);  // Menu
alert(width);  // 100
alert(height); // 200
```

Як і в разі масивів або параметрів функцій, значеннями за замовчуванням можуть бути будь-які вирази або навіть виклики функцій. Вони будуть оцінені, якщо значення не вказано.

У наведеному нижче коді `prompt` запрашівает` width`, але не для` title`:

```js run
let options = {
  title: "Menu"
};

*!*
let {width = prompt("width?"), title = prompt("title?")} = options;
*/!*

alert(title);  // Menu
alert(width);  // (whatever the result of prompt is)
```

Ми також можемо об'єднати двокрапка і рівність:

```js run
let options = {
  title: "Menu"
};

*!*
let {width: w = 100, height: h = 200, title} = options;
*/!*

alert(title);  // Menu
alert(w);      // 100
alert(h);      // 200
```

Якщо у нас є складний об'єкт з багатьма властивостями, ми можемо витягти тільки те, що нам потрібно:
```js run
let options = {
  title: "Menu",
  width: 100,
  height: 200
};

// only extract title as a variable
let { title } = options;

alert(title); // Menu
```

### The rest pattern "..."

Що якщо об'єкт має більше властивостей, ніж у нас є змінні? Чи можемо ми взяти трохи, а потім призначити «відпочинок» де-небудь?

Ми можемо використовувати шаблон відпочинку так само, як і з масивами. Він не підтримується деякими старими браузерами (наприклад, для його заповнення використовується Babel), але працює в сучасних.

Це виглядає так:
```js run
let options = {
  title: "Menu",
  height: 200,
  width: 100
};

*!*
// title = property named title
// rest = object with the rest of properties
let {title, ...rest} = options;
*/!*

// now title="Menu", rest={height: 200, width: 100}
alert(rest.height);  // 200
alert(rest.width);   // 100
```

````smart header="Gotcha if there's no `let`"
У наведених вище прикладах змінні були оголошені прямо в присвоєнні: `let {...} = {...}`. Звичайно, ми можемо використовувати і існуючі змінні без `let`. Але є підступ.

Це не спрацює:
```js run
let title, width, height;

// error in this line
{title, width, height} = {title: "Menu", width: 200, height: 100};
```

Проблема полягає в тому, що JavaScript трактує `{...}` в основному потоці коду (не всередині іншого виразу) як блок коду. Такі блоки коду можуть бути використані для групування операторів, таких як:
```js run
{
  // блок кода
  let message = "Hello";
  // ...
  alert( message );
}
```

Тут JavaScript передбачає наявність блоку коду, тому виникає помилка. Ми хочемо натомість деструктуріровать.

Щоб показати JavaScript, що це не блок коду, ми можемо зробити висновок вираз в дужки `(...)`:

```js run
let title, width, height;

// хорошо сейчас
*!*(*/!*{title, width, height} = {title: "Menu", width: 200, height: 100}*!*)*/!*;

alert( title ); // Menu
```
````

## Nested destructuring

Якщо об'єкт або масив містять інші вкладені об'єкти і масиви, ми можемо використовувати більш складні ліві шаблони для вилучення глибших частин.

У наведеному нижче коді `options` має інший об'єкт в свойстве` size` і масив у властивості` items`. Шаблон в лівій частині призначення має ту ж структуру для вилучення значень з них:
```js run
let options = {
  size: {
    width: 100,
    height: 200
  },
  items: ["Cake", "Donut"],
  extra: true   
};

// деструктуризація призначення розбита на кілька рядків для ясності
let {
  size: { // вкажіть розмір тут
    width,
    height
  },
  items: [item1, item2], // призначити предмети тут
  title = "Menu" // відсутня в об'єкті (використовується значення за замовчуванням)
} = options;

alert(title);  // Menu
alert(width);  // 100
alert(height); // 200
alert(item1);  // Cake
alert(item2);  // Donut
```

Всі властивості об'єкта `options` кроме` extra`, відсутнього в лівій частині, присвоюються відповідним змінним:
![](destructuring-complex.svg)

Нарешті, у нас є `width`,` height`, `item1`,` item2` і `title` з значення за замовчуванням.

Зверніть увагу, що для `size` і` items` немає змінних, так як замість них ми беремо їх вміст.
## Smart function parameters

Є моменти, коли функція має багато параметрів, більшість з яких є необов'язковими. Це особливо вірно для призначених для користувача інтерфейсів. Уявіть собі функцію, яка створює меню. Він може мати ширину, висоту, заголовок, список елементів і так далі.

Ось поганий спосіб написати таку функцію:
```js
function showMenu(title = "Untitled", width = 200, height = 100, items = []) {
  // ...
}
```

У реальному житті проблема полягає в тому, як запам'ятати порядок аргументів. Зазвичай IDE намагаються нам допомогти, особливо якщо код добре документований, але все ж ... Інша проблема - як викликати функцію, коли більшість параметрів в порядку за замовчуванням.

Подобається?

```js
// невизначено, де значення за замовчуванням є нормальними
showMenu("My Menu", undefined, undefined, ["Item1", "Item2"])
```

Це бридко І стає нечитабельним, коли ми маємо справу з великою кількістю параметрів.

На допомогу приходить руйнування!

Ми можемо передати параметри як об'єкт, і функція негайно розкладе їх на змінні:
```js run
// ми передаємо об’єкт у функцію
let options = {
  title: "My menu",
  items: ["Item1", "Item2"]
};

// ...і він відразу ж розширює його до змінних
function showMenu(*!*{title = "Untitled", width = 200, height = 100, items = []}*/!*) {
  // title, items – taken from options,
  // width, height – defaults used
  alert( `${title} ${width} ${height}` ); // Мое меню 200 100
  alert( items ); // Item1, Item2
}

showMenu(options);
```

Ми також можемо використовувати більш складну деструктуризація з вкладеними об'єктами і відображеннями двокрапки:
```js run
let options = {
  title: "My menu",
  items: ["Item1", "Item2"]
};

*!*
function showMenu({
  title = "Untitled",
  width: w = 100,  // width goes to w
  height: h = 200, // height goes to h
  items: [item1, item2] // items перший елемент переходить в item1, другий в item2
}) {
*/!*
  alert( `${title} ${w} ${h}` ); // My Menu 100 200
  alert( item1 ); // Item1
  alert( item2 ); // Item2
}

showMenu(options);
```

Повний синтаксис такий же, як і для деструктурірующего присвоювання:
```js
function({
  incomingProperty: varName = defaultValue
  ...
})
```

Потім, для об'єкта параметрів, буде мінлива `varName` для свойства` InputProperty`, з` defaultValue` за замовчуванням.

Зверніть увагу, що така деструктуризація передбачає, що у showMenu () є аргумент. Якщо нам потрібні всі значення за замовчуванням, то нам слід вказати порожній об'єкт:
```js
showMenu({}); // ок, усі значення за замовчуванням

showMenu(); // це призведе до помилки
```

Ми можемо виправити це, зробивши `{}` значенням за замовчуванням для всього об'єкта параметрів:
```js run
function showMenu({ title = "Menu", width = 100, height = 200 }*!* = {}*/!*) {
  alert( `${title} ${width} ${height}` );
}

showMenu(); // Menu 100 200
```

У наведеному вище коді весь об'єкт arguments за замовчуванням дорівнює `{}`, тому завжди є щось, що можна зруйнувати.
## Summary

- Руйнівна присвоювання дозволяє миттєво відображати об'єкт або масив на безліч змінних.
- Повний синтаксис об'єкта:
    ```js
    let {prop : varName = default, ...rest} = object
    ```

   Це означає, що властивість `prop` має входити в переменную` varName` і, якщо такого властивості не існує, слід використовувати значення` default`.

     Властивості об'єкта, які не мають зіставлення, копіюються в об'єкт `rest`.

- Повний синтаксис масиву:
    ```js
    let [item1 = default, item2, ...rest] = array
    ```

   Перший елемент відправляється в `item1`; друга переходить в `item2`, все інше робить массів` rest`.

- Можна витягти дані з вкладених масивів / об'єктів, для цього ліва сторона повинна мати ту ж структуру, що і права.
