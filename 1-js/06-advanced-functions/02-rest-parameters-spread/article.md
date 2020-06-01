# Залишкові параметри і синтакс розширення

Багато вбудованих функцій JavaScript підтримують довільну кількість аргументів. 

Наприклад:

- `Math.max(arg1, arg2, ..., argN)` -- повертає найбільший з аргументів.
- `Object.assign(dest, src1, ..., srcN)` -- копіює властивості з `src1..N` в `dest`.
- ...і т.д.

У цьому розділі ми розберемо як робити те саме з нашими власними функціями і як передавати масиви до них у якості параметрів.

## Залишкові параметри `...`

Функцію можна викликати використовуючи будь-яку кількість аргументів, незалежно від того, як вона визначена.

Як-от тут:
```js run
function sum(a, b) {
  return a + b;
}

alert( sum(1, 2, 3, 4, 5) );
```

Помилки через "перевищену" кількість аргументів не буде. Але, звісно, зрештою лише перші два з них будуть використані.

Інші параметри можуть бути включені до визначення функції за допомогою `...`, за якими слідує ім'я масиву, що їх містить. Крапки буквально означають "зібрати решту параметрів в масив".

До прикладу, щоб помістити усі аргументи в масив `args`:

```js run
function sumAll(...args) { // args -- ім'я масиву
  let sum = 0;

  for (let arg of args) sum += arg;

  return sum;
}

alert( sumAll(1) ); // 1
alert( sumAll(1, 2) ); // 3
alert( sumAll(1, 2, 3) ); // 6
```

Ми можемо взяти перші змінні в якості параметрів, а решту зібрати в масив.

Ось два аргументи беремо в якості змінних, а решту поміщаємо в масив `titles`:

```js run
function showName(firstName, lastName, ...titles) {
  alert( firstName + ' ' + lastName ); // Julius Caesar

  // решта -- до масиву titles
  // i.e. titles = ["Consul", "Imperator"]
  alert( titles[0] ); // Consul
  alert( titles[1] ); // Imperator
  alert( titles.length ); // 2
}

showName("Julius", "Caesar", "Consul", "Imperator");
```

````warn header="The rest parameters must be at the end"
Залишкові параметри збирають усі інші аргументи, тому немає сенсу писали що-небудь після них. Це спровокує помилку:

```js
function f(arg1, ...rest, arg2) { // arg2 after ...rest ?!
  // error
}
```

`...rest` повинен завжди стояти останнім.
````

## Змінна "arguments"

Існує також спеціальний масив-подібний об'єкт під назвою `arguments`, який містить усі аргументи по індексу.

Наприклад:

```js run
function showName() {
  alert( arguments.length );
  alert( arguments[0] );
  alert( arguments[1] );

  // ітерабельний
  // for(let arg of arguments) alert(arg);
}

// shows: 2, Julius, Caesar
showName("Julius", "Caesar");

// shows: 1, Ilya, undefined (no second argument)
showName("Ilya");
```

Раніше залишкових параметрів у мові не існувало, і використання `arguments` було єдиним способом отримати всі аргументи функції. І це досі працює, ми можемо побачити це у старому коді.

Але мінус полягає в тому, що хоча `arguments` є одначасно масив-подібним та ітерабельним, це не масив. Він не підтримує жоден метод для масивів, тому не можна викликати, наприклад, `arguments.map(...)`.

Також, він завжди містить усі аргументи. Ми не можемо частково їх захопити, як ми робили із залишковими параметрами.

Тому коли нам це необхідно, залишкові члени стануть у пригоді.

````smart header="Стрілочні функції `\"arguments\"`"
Якщо ми отримаємо об'єкт `arguments` із стрілочної функції, вона візьме їх із зовнішньої функції.

Приклад:

```js run
function f() {
  let showArg = () => alert(arguments[0]);
  showArg();
}

f(1); // 1
```

Як Ви можете пам'ятати, стрілочні функції не мають власного `this`. Зараз ми знаємо, що також немає свого об'єкта `arguments`.
````


## Spread syntax [#spread-syntax]

Тільки що ми бачили, як отримати масив із списку параметрів.

Але іноді нам необхідно зробити навпаки.

До прикладу, існує вбудована функція [Math.max](mdn:js/Math/max), що повертає найбільше число зі списку:

```js run
alert( Math.max(3, 5, 1) ); // 5
```

Тепер, скажімо, ми маємо масив `[3, 5, 1]`. Як у цьому випадку викликати `Math.max`?

Передати його "як є" неможливо, адже `Math.max` потребує числовий список, а не один масив:

```js run
let arr = [3, 5, 1];

*!*
alert( Math.max(arr) ); // NaN
*/!*
```

І, звісно, ми не можемо вручну вводити числа як `Math.max(arr[0], arr[1], arr[2])`, бо ми не знаємо, скільки їх. Коли наш скрипт виконується, їх може бути багато, а може не бути взагало. І це не круто.

Тут *оператор розширення* і приходить на допомогу. Він виглядає подібно до решти параметрів, так само використовуючи `...`, але роблячи протилежне.

When `...arr` is used in the function call, it "expands" an iterable object `arr` into the list of arguments.

For `Math.max`:

```js run
let arr = [3, 5, 1];

alert( Math.max(...arr) ); // 5 (spread turns array into a list of arguments)
```

We also can pass multiple iterables this way:

```js run
let arr1 = [1, -2, 3, 4];
let arr2 = [8, 3, -8, 1];

alert( Math.max(...arr1, ...arr2) ); // 8
```

We can even combine the spread syntax with normal values:


```js run
let arr1 = [1, -2, 3, 4];
let arr2 = [8, 3, -8, 1];

alert( Math.max(1, ...arr1, 2, ...arr2, 25) ); // 25
```

Also, the spread syntax can be used to merge arrays:

```js run
let arr = [3, 5, 1];
let arr2 = [8, 9, 15];

*!*
let merged = [0, ...arr, 2, ...arr2];
*/!*

alert(merged); // 0,3,5,1,2,8,9,15 (0, then arr, then 2, then arr2)
```

In the examples above we used an array to demonstrate the spread syntax, but any iterable will do.

For instance, here we use the spread syntax to turn the string into array of characters:

```js run
let str = "Hello";

alert( [...str] ); // H,e,l,l,o
```

The spread syntax internally uses iterators to gather elements, the same way as `for..of` does.

So, for a string, `for..of` returns characters and `...str` becomes `"H","e","l","l","o"`. The list of characters is passed to array initializer `[...str]`.

For this particular task we could also use `Array.from`, because it converts an iterable (like a string) into an array:

```js run
let str = "Hello";

// Array.from converts an iterable into an array
alert( Array.from(str) ); // H,e,l,l,o
```

The result is the same as `[...str]`.

But there's a subtle difference between `Array.from(obj)` and `[...obj]`:

- `Array.from` operates on both array-likes and iterables.
- The spread syntax works only with iterables.

So, for the task of turning something into an array, `Array.from` tends to be more universal.


## Get a new copy of an object/array

Remember when we talked about `Object.assign()` [in the past](https://javascript.info/object#cloning-and-merging-object-assign)?

It is possible to do the same thing with the spread operator!

```js run
let arr = [1, 2, 3];
let arrCopy = [...arr]; // spread the array into a list of parameters
                        // then put the result into a new array

// do the arrays have the same contents?
alert(JSON.stringify(arr) === JSON.stringify(arrCopy)); // true

// are the arrays equal?
alert(arr === arrCopy); // false (not same reference)

// modifying our initial array does not modify the copy:
arr.push(4);
alert(arr); // 1, 2, 3, 4
alert(arrCopy); // 1, 2, 3
```

Note that it is possible to do the same thing to make a copy of an object:

```js run
let obj = { a: 1, b: 2, c: 3 };
let objCopy = { ...obj }; // spread the object into a list of parameters
                          // then return the result in a new object

// do the objects have the same contents?
alert(JSON.stringify(obj) === JSON.stringify(objCopy)); // true

// are the objects equal?
alert(obj === objCopy); // false (not same reference)

// modifying our initial object does not modify the copy:
obj.d = 4;
alert(JSON.stringify(obj)); // {"a":1,"b":2,"c":3,"d":4}
alert(JSON.stringify(objCopy)); // {"a":1,"b":2,"c":3}
```

This way of copying an object is much shorter than `let objCopy = Object.assign({}, obj);` or for an array `let arrCopy = Object.assign([], arr);` so we prefer to use it whenever we can.


## Summary

When we see `"..."` in the code, it is either rest parameters or the spread syntax.

There's an easy way to distinguish between them:

- When `...` is at the end of function parameters, it's "rest parameters" and gathers the rest of the list of arguments into an array.
- When `...` occurs in a function call or alike, it's called a "spread syntax" and expands an array into a list.

Use patterns:

- Rest parameters are used to create functions that accept any number of arguments.
- The spread syntax is used to pass an array to functions that normally require a list of many arguments.

Together they help to travel between a list and an array of parameters with ease.

All arguments of a function call are also available in "old-style" `arguments`: array-like iterable object.
