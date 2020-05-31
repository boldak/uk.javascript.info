# Destructuring assignment
Две наиболее часто используемые структуры данных в JavaScript - это Objectи Array.

Объекты позволяют нам создавать единый объект, который хранит элементы данных по ключам, а массивы позволяют нам собирать элементы данных в упорядоченную коллекцию.

Но когда мы передаем их функции, ей может понадобиться не объект / массив в целом, а отдельные части.

Разрушающее присваивание - это специальный синтаксис, который позволяет нам «распаковывать» массивы или объекты в кучу переменных, что иногда бывает удобнее. Разрушение также прекрасно работает со сложными функциями, которые имеют много параметров, значений по умолчанию и так далее.

## Array destructuring

Пример того, как массив разбит на переменные:

// у нас есть массив с именем и фамилией 

let arr = ["Ilya", "Kantor"]

*!*
// деструктурирующее назначение
// устанавливает firstName = arr[0]
// и фамилию = arr[1]
let [firstName, surname] = arr;
*/!*

alert(firstName); // Илья
alert(surname);  // Kantor
```

Теперь мы можем работать с переменными вместо членов массива.

Он отлично смотрится в сочетании с splitили другими методами, возвращающими массив:

```js
let [firstName, surname] = "Ilya Kantor".split(' ');
```

````smart header="\"Destructuring\" does not mean \"destructive\"."
Это называется «деструктурирующим присваиванием», потому что оно «деструктурирует» путем копирования элементов в переменные. Но сам массив не изменяется.

Это просто более короткий способ написать:
```js
// let [firstName, surname] = arr;
let firstName = arr[0];
let surname = arr[1];
```
````

````smart header="Ignore elements using commas"
Нежелательные элементы массива также могут быть выброшены через запятую:

```js run
*!*
// второй элемент не нужен
let [firstName, , title] = ["Julius", "Caesar", "Consul", "of the Roman Republic"];
*/!*

alert( title ); // Consul
```

В приведенном выше коде второй элемент массива пропущен, третий элемент назначен на `title`, а остальные элементы массива также пропущены (поскольку для них нет переменных).
````

````smart header="Works with any iterable on the right-side"

...На самом деле, мы можем использовать его с любыми повторяемыми, не только массивами:
```js
let [a, b, c] = "abc"; // ["a", "b", "c"]
let [one, two, three] = new Set([1, 2, 3]);
```

````


````smart header="Assign to anything at the left-side"

Мы можем использовать любые «назначаемые объекты» на левой стороне.

Например, свойство объекта:
```js run
let user = {};
[user.name, user.surname] = "Ilya Kantor".split(' ');

alert(user.name); // Ilya
```

````

````smart header="Looping with .entries()"

В предыдущей главе мы видели метод [Object.entries (obj)] (mdn: js / Object / records).

Мы можем использовать его с деструктуризацией для циклического перебора ключей и значений объекта:
```js run
let user = {
  name: "John",
  age: 30
};

// цикл по ключам и значениям
*!*
for (let [key, value] of Object.entries(user)) {
*/!*
  alert(`${key}:${value}`); // name:John, then age:30
}
```

...И то же самое для карты:

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

Если мы хотим не просто получить первые значения, но и собрать все, что следует, - мы можем добавить еще один параметр, который получает «остальное», используя три точки `" ... "`:
```js run
let [name1, name2, *!*...rest*/!*] = ["Julius", "Caesar", *!*"Consul", "of the Roman Republic"*/!*];

alert(name1); // Julius
alert(name2); // Caesar

*!*
// Обратите внимание, что типом `rest` является Array.
alert(rest[0]); // Consul
alert(rest[1]); // of the Roman Republic
alert(rest.length); // 2
*/!*
```

Значение `rest` - это массив оставшихся элементов массива. Мы можем использовать любое другое имя переменной вместо `rest`, просто убедитесь, что перед ним три точки и оно идет последним в назначении деструктуризации.
### Default values

Если в массиве меньше значений, чем переменных в присваивании, ошибки не будет. Отсутствующие значения считаются неопределенными:

```js run
*!*
let [firstName, surname] = [];
*/!*

alert(firstName); // undefined
alert(surname); // undefined
```

Если мы хотим, чтобы значение «по умолчанию» заменило отсутствующее, мы можем предоставить его с помощью `=`:

```js run
*!*
// default values
let [name = "Guest", surname = "Anonymous"] = ["Julius"];
*/!*

alert(name);    // Julius (from array)
alert(surname); // Anonymous (default used)
```

Значения по умолчанию могут быть более сложными выражениями или даже вызовами функций. Они оцениваются, только если значение не указано.

Например, здесь мы используем функцию `prompt` для двух значений по умолчанию. Но он будет работать только для пропавшего:

```js run
// runs only prompt for surname
let [name = prompt('name?'), surname = prompt('surname?')] = ["Julius"];

alert(name);    // Julius (from array)
alert(surname); // whatever prompt gets
```



## Object destructuring

Назначение деструктурирования также работает с объектами.

Основной синтаксис:

```js
let {var1, var2} = {var1:…, var2:…}
```

У нас есть существующий объект с правой стороны, который мы хотим разделить на переменные. Левая сторона содержит «шаблон» для соответствующих свойств. В простом случае это список имен переменных в `{...}`.
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

Свойства `options.title`,` options.width` и `options.height` присваиваются соответствующим переменным. Порядок не имеет значения. Это тоже работает:

`` `JS
// изменил порядок в let {...}
let {height, width, title} = { title: "Menu", height: 200, width: 100 }
```

Шаблон с левой стороны может быть более сложным и определять соответствие между свойствами и переменными.

Если мы хотим присвоить свойство переменной с другим именем, например, `options.width`, чтобы перейти в переменную с именем` w`, то мы можем установить его с помощью двоеточия:
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

Двоеточие показывает «что: куда идет». В приведенном выше примере свойство `width` переходит к` w`, свойство `height` переходит к` h`, а для `title` присваивается то же имя.

Для потенциально отсутствующих свойств мы можем установить значения по умолчанию, используя `" = "`, например так:
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

Как и в случае массивов или параметров функций, значениями по умолчанию могут быть любые выражения или даже вызовы функций. Они будут оценены, если значение не указано.

В приведенном ниже коде `prompt` запрашивает` width`, но не для `title`:

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

Мы также можем объединить двоеточие и равенство:

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

Если у нас есть сложный объект со многими свойствами, мы можем извлечь только то, что нам нужно:

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

Что если объект имеет больше свойств, чем у нас есть переменные? Можем ли мы взять немного, а затем назначить «отдых» где-нибудь?

Мы можем использовать шаблон отдыха так же, как и с массивами. Он не поддерживается некоторыми старыми браузерами (например, для его заполнения используется Babel), но работает в современных.

Это выглядит так:
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
В приведенных выше примерах переменные были объявлены прямо в присваивании: `let {…} = {…}`. Конечно, мы можем использовать и существующие переменные без `let`. Но есть подвох.

Это не сработает:
```js run
let title, width, height;

// error in this line
{title, width, height} = {title: "Menu", width: 200, height: 100};
```

The problem is that JavaScript treats `{...}` in the main code flow (not inside another expression) as a code block. Such code blocks can be used to group statements, like this:

```js run
{
  // блок кода
  let message = "Hello";
  // ...
  alert( message );
}
```

Здесь JavaScript предполагает наличие блока кода, поэтому возникает ошибка. Мы хотим вместо этого деструктурировать.

Чтобы показать JavaScript, что это не блок кода, мы можем заключить выражение в скобки `(...)`:

```js run
let title, width, height;

// хорошо сейчас
*!*(*/!*{title, width, height} = {title: "Menu", width: 200, height: 100}*!*)*/!*;

alert( title ); // Menu
```
````

## Nested destructuring

Если объект или массив содержат другие вложенные объекты и массивы, мы можем использовать более сложные левые шаблоны для извлечения более глубоких частей.

В приведенном ниже коде `options` имеет другой объект в свойстве` size` и массив в свойстве `items`. Шаблон в левой части назначения имеет ту же структуру для извлечения значений из них:
```js run
let options = {
  size: {
    width: 100,
    height: 200
  },
  items: ["Cake", "Donut"],
  extra: true   
};

// деструктуризация назначения разбита на несколько строк для ясности
let {
  size: { // укажите размер здесь
    width,
    height
  },
  items: [item1, item2], // назначить предметы здесь
  title = "Menu" // отсутствует в объекте (используется значение по умолчанию)
} = options;

alert(title);  // Menu
alert(width);  // 100
alert(height); // 200
alert(item1);  // Cake
alert(item2);  // Donut
```

Все свойства объекта `options` кроме` extra`, отсутствующего в левой части, присваиваются соответствующим переменным:
![](destructuring-complex.svg)

Наконец, у нас есть `width`,` height`, `item1`,` item2` и `title` из значения по умолчанию.

Обратите внимание, что для `size` и` items` нет переменных, так как вместо них мы берем их содержимое.
## Smart function parameters

Есть моменты, когда функция имеет много параметров, большинство из которых являются необязательными. Это особенно верно для пользовательских интерфейсов. Представьте себе функцию, которая создает меню. Он может иметь ширину, высоту, заголовок, список элементов и так далее.

Вот плохой способ написать такую ​​функцию:

```js
function showMenu(title = "Untitled", width = 200, height = 100, items = []) {
  // ...
}
```

В реальной жизни проблема заключается в том, как запомнить порядок аргументов. Обычно IDE пытаются нам помочь, особенно если код хорошо документирован, но все же ... Другая проблема - как вызвать функцию, когда большинство параметров в порядке по умолчанию.

Нравится?

```js
// undefined where default values are fine
showMenu("My Menu", undefined, undefined, ["Item1", "Item2"])
```

Это безобразно И становится нечитаемым, когда мы имеем дело с большим количеством параметров.

На помощь приходит разрушение!

Мы можем передать параметры как объект, и функция немедленно разложит их на переменные:
```js run
// we pass object to function
let options = {
  title: "My menu",
  items: ["Item1", "Item2"]
};

// ...и он сразу же расширяет его до переменных
function showMenu(*!*{title = "Untitled", width = 200, height = 100, items = []}*/!*) {
  // title, items – taken from options,
  // width, height – defaults used
  alert( `${title} ${width} ${height}` ); // Мое меню 200 100
  alert( items ); // Item1, Item2
}

showMenu(options);
```

Мы также можем использовать более сложную деструктуризацию с вложенными объектами и отображениями двоеточия:

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
  items: [item1, item2] // items первый элемент переходит в item1, второй в item2
}) {
*/!*
  alert( `${title} ${w} ${h}` ); // My Menu 100 200
  alert( item1 ); // Item1
  alert( item2 ); // Item2
}

showMenu(options);
```

Полный синтаксис такой же, как и для деструктурирующего присваивания:
```js
function({
  incomingProperty: varName = defaultValue
  ...
})
```

Затем, для объекта параметров, будет переменная `varName` для свойства` InputProperty`, с `defaultValue` по умолчанию.

Обратите внимание, что такая деструктуризация предполагает, что у showMenu () есть аргумент. Если нам нужны все значения по умолчанию, то нам следует указать пустой объект:

```js
showMenu({}); // ok, all values are default

showMenu(); // this would give an error
```

Мы можем исправить это, сделав `{}` значением по умолчанию для всего объекта параметров:

```js run
function showMenu({ title = "Menu", width = 100, height = 200 }*!* = {}*/!*) {
  alert( `${title} ${width} ${height}` );
}

showMenu(); // Menu 100 200
```

В приведенном выше коде весь объект arguments по умолчанию равен `{}`, поэтому всегда есть что-то, что можно разрушить.
## Summary

- Разрушающее присваивание позволяет мгновенно отображать объект или массив на множество переменных.
- Полный синтаксис объекта:
    ```js
    let {prop : varName = default, ...rest} = object
    ```

    Это означает, что свойство `prop` должно входить в переменную` varName` и, если такого свойства не существует, следует использовать значение `default`.

    Свойства объекта, которые не имеют сопоставления, копируются в объект `rest`.

- Полный синтаксис массива:
    ```js
    let [item1 = default, item2, ...rest] = array
    ```

    Первый элемент отправляется в `item1`; вторая переходит в `item2`, все остальное делает массив` rest`.

- Можно извлечь данные из вложенных массивов / объектов, для этого левая сторона должна иметь ту же структуру, что и правая.
