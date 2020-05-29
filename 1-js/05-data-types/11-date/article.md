# Дата і час

Зустрічайте новий вбудований об'єкт: [Date](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date). Він зберігає дату/час, та  надає методи управління ними

Наприклад, його можна використовувати для зберігання часу створення/зміни, для вимірювання часу або просто для виведення поточної дати.

## Создание

Для створення нового об'єкта `Date` потрібно викликати конструктор `new Date()` з одним з таких аргументів:

`new Date()`
: Без аргументів -- створити об'єкт `Date` з поточною датою/часом:

    ```js run
    let now = new Date();
    alert( now ); // показує поточну дату/час
    ```

`new Date(milliseconds)`
: Створити об'єкт `Date` з часом, рівним кількості мілісекунд (1/1000 секунди), що пройшла після 1 січня 1970 року UTC + 0.    

    ```js run
    // 0 відповідає 01.01.1970 UTC+0
    let Jan01_1970 = new Date(0);
    alert( Jan01_1970 );

    // тепер додамо 24 години і отримаємо 02.01.1970 UTC+0
    let Jan02_1970 = new Date(24 * 3600 * 1000);
    alert( Jan02_1970 );
    ```

    Ціле число, яке представляє собою кількість мілісекунд, що пройшли з початку 1970 року, називається *timestamp*.

    Це - легковаге чисельне представлення дати. З timestamp завжди можна отримати дату за допомогою  `new Date(timestamp)` і перетворити існуючий об'єкт `Date` в timestamp, використовуючи метод `date.getTime()` (див. нижче).

    Дати до 1 січня 1970 будуть відповідно від'ємні timestamp, наприклад:
    ```js run
    // 31 декабря 1969 года
    let Dec31_1969 = new Date(-24 * 3600 * 1000);
    alert( Dec31_1969 );
    ```
    
`new Date(datestring)`
: Якщо аргумент всього один, і це рядок,то він аналізується автоматично. Алгоритм розбору - такий же, як в `Date.parse`, який ми розглянемо пізніше.

    ```js run
    let date = new Date("2017-01-26");
    alert(date);
    // Час не вказано, тому воно ставиться опівночі за Гринвічем  і
    // змінюється відповідно до часового поясу місця виконання коду
    // Так що в результаті можна отримати
    // Thu Jan 26 2017 11:00:00 GMT+1100 (східно-австралійське час)
    // или
    // Wed Jan 25 2017 16:00:00 GMT-0800 (тихоокеанський час)
    ```

`new Date(year, month, date, hours, minutes, seconds, ms)`
: Створити об'єкт `Date` із заданими компонентами в місцевому часовому поясі. Обов'язкові тільки перші два аргументи.

    - `year` повинен складатися з чотирьох цифр: значення `2020` коректно, `91` -- ні.
    - `month` починається з `0` (січень) по `11` (грудень).
    - Параметр `date` тут являє собою день місяця. Якщо параметр не заданий, то приймається значення `1`.
    - Якщо параметри `hours/minutes/seconds/ms` відсутні, їх значенням стає`0`.

    Наприклад:

    ```js
    new Date(2011, 0, 1, 0, 0, 0, 0); // // 1 Січня 2011, 00:00:00
    new Date(2011, 0, 1); // те ж саме, так як годинни та ін. рівні 0
    ```

    Мінімальна точність – 1 мс (1/1000 секунди):

    ```js run
    let date = new Date(2011, 0, 1, 2, 3, 4, 567);
    alert( date ); // 1.01.2011, 02:03:04.567
    ```

## Отримання компонентів дати

Існують методи отримання року, місяця і т.д. з об'єкта `Date`:

[getFullYear()](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/getFullYear)
: Отримати рік (4 цифри)

[getMonth()](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/getMonth)
: Отримати місяць, ** від 0 до 11 **.

[getDate()](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/getDate)
: Отримати день місяця, від 1 до 31, що дещо суперечить назві методу.

[getHours()](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/getHours), [getMinutes()](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/getMinutes), [getSeconds()](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/getSeconds), [getMilliseconds()](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/getMilliseconds)
: Отримати, відповідно, години, хвилини, секунди або мілісекунди.

```warn header="Ніякого `getYear()`. Тільки `getFullYear()`"
Багато інтерпретаторів JavaScript реалізують нестандартний і застарілий метод `getYear()`, який інколи повертає рік у вигляді двох цифр. Будь ласка, обходьте його стороною. Якщо потрібно значення року, використовуйте `getFullYear()`.
```

Крім того, можна отримати певний день тижня:

[getDay()](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/getDay)
: Повернути день тижня від `0` (неділя) до `6` (суботи). Незважаючи на те, що в ряді країн за перший день тижня прийнятий понеділок, в JavaScript початок тижня припадає на неділю.

**Всі вищеперелічені способи возвращают значень у відповідність з місцевим часовим поясом.**

Однак існують і їх UTC-варіанти, які повертають день, місяць, рік для тимчасової зони UTC+0: [getUTCFullYear()](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCFullYear), [getUTCMonth()](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCMonth), [getUTCDay()](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCDay). Для їх використання потрібно після `"get"` підставити `"UTC"`.

Якщо ваш місцевий часовий пояс зміщений відносно UTC, то наступний код покаже різні години:

```js run
// поточна дата
let date = new Date();

// година в вашому поточному часовому поясі
alert( date.getHours() );

// година в часовому поясі UTC + 0 (лондонське час без переходу на літній час)
alert( date.getUTCHours() );
```

Крім вищенаведених методів, існують два особливих методу без UTC-варіанту:

[getTime()](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/getTime)
: Для заданої дати повертає timestamp - кількість мілісекунд, що пройшли з 1 січня 1970 року UTC + 0.

[getTimezoneOffset()](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/getTimezoneOffset)
: Повертає різницю у хвилинах між місцевим часовим поясом і UTC

    ```js run
    // якщо ви в часовому поясі UTC-1, то виводиться 60
    // якщо ви в часовому поясі UTC + 3, виводиться -180
    alert( new Date().getTimezoneOffset() );

    ```

## Встановлення компонентів дати

Наступні методи дозволяють встановити компоненти дати/часу:

- [`setFullYear(year, [month], [date])`](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/setFullYear)
- [`setMonth(month, [date])`](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/setMonth)
- [`setDate(date)`](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/setDate)
- [`setHours(hour, [min], [sec], [ms])`](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/setHours)
- [`setMinutes(min, [sec], [ms])`](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/setMinutes)
- [`setSeconds(sec, [ms])`](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/setSeconds)
- [`setMilliseconds(ms)`](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/setMilliseconds)
- [`setTime(milliseconds)`](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/setTime) (встановлює дату у вигляді цілої кількості мілісекунд, що пройшли з 01.01.1970 UTC)

У всіх цих методів, крім `setTime()`, є UTC-варіант, наприклад: `setUTCHours()`.

Як ми бачимо, деякі методи можуть встановлювати відразу кілька компонентів дати, наприклад: `setHours`. Компоненти, які не згадуються, вони не змінюються.

Наприклад:

```js run
let today = new Date();

today.setHours(0);
alert(today); // виводиться сьогоднішня дата, але значення години буде 0

today.setHours(0, 0, 0, 0);
alert(today); // виводиться сьогоднішня дата, але час буде рівно 00:00:00.
```

## Автокорекція

*Автокорекція* -- дуже зручна особливість об'єктів `Date`. Ми можемо встановити значення поза діапазоном, і воно автоматично виправить.

Наприклад:

```js run
let date = new Date(2013, 0, *!*32*/!*); // 32 Січня 2013 ?!?
alert(date); // ...1st Лютого 2013!
```

Компоненти дати, що перебувають поза діапазоном, розподіляються автоматично.

Скажімо, нам потрібно збільшити дату "28 лютого 2016 року" на 2 дні. Це може бути "2 березня" або "1 березня" у разі високосного року. Нам не потрібно думати про це. Просто додайте 2 дні. Об'єкт `Date` подбає про остальне:

```js run
let date = new Date(2016, 1, 28);
*!*
date.setDate(date.getDate() + 2);
*/!*

alert( date ); // 1 березня 2016
```

Цю можливість часто використовують, щоб отримати дату після заданого відрізка часу. Наприклад, отримаємо дату "через 70 секунд з поточного моменту":

```js run
let date = new Date();
date.setSeconds(date.getSeconds() + 70);

alert( date ); // виводить правильну дату
```

Також можна встановити нульові або навіть від'ємні значення. наприклад:

```js run
let date = new Date(2016, 0, 2); // 2 січня 2016

date.setDate(1); // задати перший день місяця
alert( date );

date.setDate(0); // перший день місяця -- це 1, так що виводиться останнє число попереднього місяця 
alert( date ); // 31 грудня 2015
```

## Перетворення до числа, різниця дат

Якщо об'єкт  `Date` перетворити в число, то отримаємо timestamp за аналогією з `date.getTime()`:

```js run
let date = new Date();
alert(+date); // кількість мілісекунд, те ж саме, що і в date.getTime()
```

Важливий побічний ефект: дати можна вичитати, в результаті отримуємо різницю в мілісекундах.

Цей прийом можна використовувати для вимірювання часу:

```js run
let start = new Date(); // починаємо відлік часу

//  виконуємо певні дії
for (let i = 0; i < 100000; i++) {
  let doSomething = i * i * i;
}

let end = new Date(); // закінчуємо відлік часу

alert( `Цикл відпрацював за ${end - start} міллісекунд` );
```

## Date.now()

Якщо потрібно просто виміряти час, об'єкт `Date` нам не потрібен.

Існує особливий метод `Date.now()`, який повертає поточну мітку часу (timestamp).

Семантично він еквівалентний `new Date().getTime()`, але не створює проміжний об'єкт `Date`. Так що цей спосіб працює швидше і не навантажує збирач сміття(англ. garbage collection).

Він використовується в основному для зручності або коли важлива швидкодія. Наприклад, при розробці ігор на JavaScript, або інших спеціалізованих додатків.

Ймовірно, попередній приклад краще переписати так:

```js run
*!*
let start = Date.now(); // кількість мілісекунд з 1 січня 1970 року
*/!*

// виконуємо деякі дії
for (let i = 0; i < 100000; i++) {
  let doSomething = i * i * i;
}

*!*
let end = Date.now(); // закінчуємо відлік часу
*/!*

alert( `Цикл відпрацював за ${end - start} міллісекунд` ); // віднімаються числа, а не дати
```

## Бенчмаркінг

Будьте уважні, якщо хочете точно протестувати продуктивність функції, яка залежить від процесора.

Наприклад, порівняємо дві функції, що обчислюють різницю між двома датами: яка спрацює швидше?

Подібні обчислення які, заміряють продуктивність, також називають "бенчмарками" (англ. benchmark).

```js
// є date1 і date2, яка функція швидше поверне різницю між ними в мілісекундах?
function diffSubtract(date1, date2) {
  return date2 - date1;
}

// або
function diffGetTime(date1, date2) {
  return date2.getTime() - date1.getTime();
}
```

Обидві функції роблять буквально одне і те ж, тільки одна використовує явний метод `date.getTime()` для отримання дати в мілісекундах, а інша покладається на перетворення дати в число. Результат їхньої роботи завжди один і той же.

Але яка функція швидше?

Для початку можна запустити їх багато разів поспіль і засікти різницю. У нашому випадку функції дуже прості, так що буде потрібно хоча б 100000 повторень.

Проведемо вимірювання:

```js run
function diffSubtract(date1, date2) {
  return date2 - date1;
}

function diffGetTime(date1, date2) {
  return date2.getTime() - date1.getTime();
}

function bench(f) {
  let date1 = new Date(0);
  let date2 = new Date();

  let start = Date.now();
  for (let i = 0; i < 100000; i++) f(date1, date2);
  return Date.now() - start;
}

alert( 'Время diffSubtract: ' + bench(diffSubtract) + 'мс' );
alert( 'Время diffGetTime: ' + bench(diffGetTime) + 'мс' );
```

Метод `getTime()` працює відчутно швидше. Все тому, що не відбувається перетворення типів, і інтерпретаторів таке набагато легше оптимізувати.

Чудово, це вже щось. Але до хорошого бенчмарка нам ще далеко.

Уявіть, що при виконанні `bench(diffSubtract)` процесор паралельно робив щось ще, також споживає ресурси. А до початку виконання `bench(diffGetTime)` він це вже завершив.

Досить реалістичний сценарій в сучасних багатопроцесорних операційних системах.

Як результат, перший показник матиме менше ресурсів процесора, ніж другий. Це може призвести до неправильних результатів.

**Для отримання найбільш достовірних результатів тестування продуктивності весь набір бенчмарків потрібно запускати по декілька разів.**

Наприклад, так:

```js run
function diffSubtract(date1, date2) {
  return date2 - date1;
}

function diffGetTime(date1, date2) {
  return date2.getTime() - date1.getTime();
}

function bench(f) {
  let date1 = new Date(0);
  let date2 = new Date();

  let start = Date.now();
  for (let i = 0; i < 100000; i++) f(date1, date2);
  return Date.now() - start;
}

let time1 = 0;
let time2 = 0;

*!*
// bench(upperSlice) и bench(upperLoop) по черзі запускаються 10 раз
for (let i = 0; i < 10; i++) {
  time1 += bench(diffSubtract);
  time2 += bench(diffGetTime);
}
*/!*

alert( 'Підсумковий час diffSubtract: ' + time1 );
alert( 'Підсумковий час diffGetTime: ' + time2 );
```

Сучасні інтерпретатори JavaScript починають застосовувати розширені оптимізації лише до "гарячого коду", який часто використовується. Так що в прикладі вище перші запуски не оптимізовані належним чином. Незайвим буде додати попередній запуск для "розігріву":

```js
// додаємо для "розігріву" перед основним циклом
bench(diffSubtract);
bench(diffGetTime);

// а тепер тестуємо продуктивність
for (let i = 0; i < 10; i++) {
  time1 += bench(diffSubtract);
  time2 += bench(diffGetTime);
}
```

```warn header="Будьте осторожны с микробенчмарками"
Сучасні інтерпретатори JavaScript виконують безліч оптимізацій. Вони можуть вплинути на результат "штучних тестів" порівняно з "нормальним використанням", особливо якщо ми тестуємо щось дуже маленьке, наприклад, роботу оператора або вбудованої функції. Тому якщо хочете серйозно розуміти продуктивність, будь ласка, вивчіть, як працюють інтерпретатори JavaScript. І тоді вам, ймовірно, вже не знадобляться мікробенчмарки.

Великий пакет статей про V8 можна знайти за адресою <http://mrale.ph>.
```

## Date.parse з рядка

Метод [Date.parse(str)](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/parse) зчитує дату із рядка.

Формат рядка повинен бути наступним: `YYYY-MM-DDTHH:mm:ss.sssZ`, де:

- `YYYY-MM-DD` -- це дата: рік-місяць-день.
- Символ `"T"` використовується як роздільник.
- `HH:mm:ss.sss` -- час: години, хвилини, секунди і мілісекунди.
- Необов'язкова частина `'Z'` означає часовий пояс в форматі `+-hh:mm`. Якщо вказати просто букву `Z`, то отримаємо UTC+0.

Можливі й більш короткі варіанти, наприклад, `YYYY-MM-DD` або `YYYY-MM`, або навіть `YYYY`.

Виклик `Date.parse(str)` обробляє рядок в заданому форматі і повертає timestamp (кількість мілісекунд з 1 січня 1970 року UTC + 0). Якщо формат неправильний, повертається `NaN`.

Наприклад:

```js run
let ms = Date.parse('2012-01-26T13:51:50.417-07:00');

alert(ms); // 1327611110417 (timestamp)
```

Можна тут же створити об'єкт `new Date` з timestamp:

```js run
let date = new Date( Date.parse('2012-01-26T13:51:50.417-07:00') );

alert(date);  
```

## Підсумок

- Дата і час в JavaScript представлені об'єктом [Date](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date). Не можна створити "тільки дату" або "тільки час": об'єкти `Date` завжди містять і те, і інше.
- Місяці рахуються від нуля (так, січень -- це нульовий місяць).
- Дні тижня в `getDay()` також рахуються від нуля (це неділя).
- Об'єкт `Date` самостійно коригується при введенні значень, що виходять за рамки допустимих. Це корисно для додавання/віднімання днів/місяців/тижнів.
- Дати можна відняти, і різниця повертається в мілісекундах. Це тому, що при перетворенні в число об'єкт `Date` стає timestamp.
- Використовуйте `Date.now()` для швидкого отримання поточного часу в форматі timestamp.

Врахуйте, що, на відміну від деяких інших систем, в JavaScript timestamp в мілісекундах, а не в секундах.

Часом нам потрібно виміряти час з високою точністю. Власними засобами JavaScript виміряти час в мікросекундах (1/1000000 секунди) не можна, але в більшості середовищ така можливість є. Наприклад, в браузерах є метод [performance.now()](https://developer.mozilla.org/ru/docs/Web/API/Performance/now), який повертає кількість мілісекунд з початку завантаження сторінки з точністю до мікросекунд (3 цифри після точки):

```js run
alert(`Завантаження розпочато ${performance.now()}мс назад`);
// Отримуємо щось на зразок: "Завантаження розпочато 34731.26000000001мс назад"
// .26 –- це мікросекунди (260 мікросекунд)
// коректними є тільки перші три цифри після крапки, а решта - це помилка точності
```

У Node.js для цього передбачений модуль `microtime` і ряд інших способів. Технічно майже будь-який пристрій і середовище дозволяють отримати більшу точність, просто не входить в об'єкт `Date`.
