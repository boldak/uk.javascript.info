# Рекурсія і стек

Давайте повернемось до функцій та вивчимо їх більш детально.

Нашою першою темою буде *рекурсія*.

Якщо Ви не новачок у програмуванні, тоді, можливо, уже знайомі з цим і пропустите даний розділ.

Рекурсія - це патерн в програмуванні, корисний у ситуації, коли одна задача може бути розділена на декілька такого ж типу, але простіших. Або коли задачу можна спростити до простих дій в спрощеному варіанті цієї задачі. Або, як побачимо пізніше, для роботи з певними структурами даних.

Коли функція вирішує задачу, в процесі вона може викликати багато інших функцій. Частковий випадок цього - це коли функція викликає *себе*. Це називають *рекурсією*.

## Два підходи

Щоб почати із чогось просто, давайте напишемо функцію `pow(x, n)`, яка підносить число `x` до натурального степеня `n. Іншими словами, множить `x` саме на себе `n` разів. 
```js
pow(2, 2) = 4
pow(2, 3) = 8
pow(2, 4) = 16
```

Є два способи реалізувати це.

1. Ітеративний підхід: цикл `for`:

    ```js run
    function pow(x, n) {
      let result = 1;

      // multiply result by x n times in the loop
      for (let i = 0; i < n; i++) {
        result *= x;
      }

      return result;
    }

    alert( pow(2, 3) ); // 8
    ```

2. Рекурсивний підхід: спростити задачу і викликати функцію всередині самої себе:

    ```js run
    function pow(x, n) {
      if (n == 1) {
        return x;
      } else {
        return x * pow(x, n - 1);
      }
    }

    alert( pow(2, 3) ); // 8
    ```

Зауважте, наскільки фундаментально відрізняються ці підходи.

Коли викликаємо `pow(x, n)`, виклик розгалужується на дві вітки:

```js
              if n==1  = x
             /
pow(x, n) =
             \       
              else     = x * pow(x, n - 1)
```

1. Якщо `n == 1`, тоді все просто. Це називається *базою* рекурсії, тому що вона відразу обчислює очевидний результат: `pow(x, 1)` дорівнює `x`.
2. Інакше, ми можемо представити `pow(x, n)` як `x * pow(x, n - 1)`. В математиці це записується як <code>x<sup>n</sup> = x * x<sup>n-1</sup></code>. Це називають *кроком рекурсії*: ми зводимо задачу до простої дії (множення на `x`) в спрощеному завданні (`pow` з меншим `n`). На наступних кроках це продовжується, аж поки `n` не досягне `1`.

Також можна сказати, що `pow` *рекурсивно себе викликає* допоки `n == 1`.

![рекурсивна діаграма pow](recursion-pow.svg)


До прикладу, для обчислення `pow(2, 4)` рекурсивний спосіб матиме наступні кроки:

1. `pow(2, 4) = 2 * pow(2, 3)`
2. `pow(2, 3) = 2 * pow(2, 2)`
3. `pow(2, 2) = 2 * pow(2, 1)`
4. `pow(2, 1) = 2`

Отже, рекурсія спрощує виклик функції до простішого, потім до ще більш простого, і так далі, поки не досягне мети.

````smart header="Рекурсія зазвичай коротша"
Рекурсивний спосіб зазвичай коротший за ітеративний.

Тепер ми може переписати те саме, використовуючи умовний оператор `?` замість `if`, щоб зробити `pow(x, n)` лаконічнішим, залишивши таким же читабельним:

```js run
function pow(x, n) {
  return (n == 1) ? x : (x * pow(x, n - 1));
}
```
````

Максимальна кількість вкладених викликів (включаючи перший) називається *глибиною рекурсії*. В нашому випадку, вона рівна `n`.

Максимальна глибина рекурсії обмежена рушієм JavaScript. Можна вважати, що це число рівне 10000, деякі рушії мають більше, але 100000 -- це, напевно, за межами можливостей для більшості з них. Існують автоматичні оптимізації, що дозволяє пом'якшити це ("оптимізації хвостової рекурсії"), але вони досі не всюди підтримуються та працюють лише в простих випадках.

Це обмежує сферу використання рекурсії, але вона все ще залишається досить широкою. Існує багато задач, де рекурсивний підхід дає простіший код, що легше застосовувати та підтримувати.

## Контекст виконання та стек

Давайте з'ясуємо, як рекурсивні виклики працюють. Для цього ми подивимося "під капот" функцій.

Інформація про процес виконання функції зберігається в її *контексті виконання*.

[Контекст виконання](https://tc39.github.io/ecma262/#sec-execution-contexts) -- це внутрішня структура даних, что містить подробиці про виконання функції: у якому місці в коді знаходиться інтерпретатор, локальні змінні функції, значення `this` (ми його не використовуємо в даному прикладі) та деякі інші внутрішні деталі.

Один виклик функції має лише один контекст виконання, що з ним пов'язаний.

Коли функція робить вкладений виклик, відбувається наступне:

- Виконання даної функції призупиняється.
- Контекст виконання, пов'язаний із нею, записується в особливу структуру даних -- *стек контексту виконання*.
- Виконується вкладений виклик.
- Коли він закінчується, попередній контекст виконання дістається зі стеку, і виконання зовнішньої функції відновлюється на тому місці, де воно призупинилася. 

Давайте подивиось, що відбувається під час виклику `pow(2, 3)`.

### pow(2, 3)

Спочатку, після виклику `pow(2, 3)` контекст виконання зберігає змінні: `x = 2, n = 3`, виконання знаходиться на рядку `1` функції.

Можна зобразити це як:

<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Context: { x: 2, n: 3, at line 1 }</span>
    <span class="function-execution-context-call">pow(2, 3)</span>
  </li>
</ul>

Це тільки початок виконання функції. Умова `n == 1` не справджується, тому виконання переходить на другу гілку `if`:

```js run
function pow(x, n) {
  if (n == 1) {
    return x;
  } else {
*!*
    return x * pow(x, n - 1);
*/!*
  }
}

alert( pow(2, 3) );
```


Змінні ті самі, але рядок змінився, тож контекст тепер:

<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Context: { x: 2, n: 3, at line 5 }</span>
    <span class="function-execution-context-call">pow(2, 3)</span>
  </li>
</ul>

Щоб обчислити `x * pow(x, n - 1)`, необхідно виконати підвиклик `pow` з новими аргументами: `pow(2, 2)`.

### pow(2, 2)

Щоби зробити вкладений виклик, JavaScript "запам'ятовує" актуальний контекст виконання в *стек*.

Тут ми викликаємо цю ж функцію `pow`, але це не має значення. Процес аналогічний для всіх функцій:

1. Актуальний контекст "запам'ятовується" на вершині стеку.
2. Створюється новий контекст для підвиклику.
3. По закінченню підвиклику попередній контекст дістається зі стеку, і його виконання продовжується.

Ось стек у момент підквиклику `pow(2, 2)`:

<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Context: { x: 2, n: 2, at line 1 }</span>
    <span class="function-execution-context-call">pow(2, 2)</span>
  </li>
  <li>
    <span class="function-execution-context">Context: { x: 2, n: 3, at line 5 }</span>
    <span class="function-execution-context-call">pow(2, 3)</span>
  </li>
</ul>

Новий контекст виконання тепер знаходиться зверху (і виділений), в той час як попередні збережені контексти -- знизу.

По завершенню підвиклику легко відновити виконання попереднього контексту, тому що він зберігає змінні і точне місце в коді, де він призупинився.

```smart
На картинці можемо бачити слово "line". В нашому коді є лише один підвиклик в рядку, але загалом один рядом коду може містити декілька підвикликів, як `pow(…) + pow(…) + somethingElse(…)`.

Тож, біль правильно сказати, что виконання відновлюється "відразу після підвиклику".
```

### pow(2, 1)

Процес повторюється: новий підвиклик виконується в рядку `5`, тепер з аргументами `x=2`, `n=1`.

Створено новий контекст, старий "виштовхнуто" на вершину стеку:

<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Context: { x: 2, n: 1, at line 1 }</span>
    <span class="function-execution-context-call">pow(2, 1)</span>
  </li>
  <li>
    <span class="function-execution-context">Context: { x: 2, n: 2, at line 5 }</span>
    <span class="function-execution-context-call">pow(2, 2)</span>
  </li>
  <li>
    <span class="function-execution-context">Context: { x: 2, n: 3, at line 5 }</span>
    <span class="function-execution-context-call">pow(2, 3)</span>
  </li>
</ul>

Є 2 старих контексти і 1 запущено в даний момент для `pow(2, 1)`.

### Вихід

Під час виконання `pow(2, 1)`, на відміну від того, як було раніше, умова `n == 1` справджується, тож перша гілка `if` виконується:

```js
function pow(x, n) {
  if (n == 1) {
*!*
    return x;
*/!*
  } else {
    return x * pow(x, n - 1);
  }
}
```

Більше немає вкладених викликів, тож виконання функції завершується, повертаючи, returning `2`.

Коли функцію завершего, її контекст виконання більше не потрібен, тому він видаляється з пам'яті. У той час як попередній відновлюється з вершини стеку:


<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Context: { x: 2, n: 2, at line 5 }</span>
    <span class="function-execution-context-call">pow(2, 2)</span>
  </li>
  <li>
    <span class="function-execution-context">Context: { x: 2, n: 3, at line 5 }</span>
    <span class="function-execution-context-call">pow(2, 3)</span>
  </li>
</ul>

Виконання `pow(2, 2)` відновлено. Воно містить результат підвиклику `pow(2, 1)`, тож може також завершити обчислення `x * pow(x, n - 1)`, повернувши `4`.

Далі відновлюється попередній контекст:

<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Context: { x: 2, n: 3, at line 5 }</span>
    <span class="function-execution-context-call">pow(2, 3)</span>
  </li>
</ul>

Коли виконання завершується, маємо результат `pow(2, 3) = 8`.

Глибина рекурсії в цьому випадку становить: **3**.

Як можемо бачити з ілюстрації зверху, глибина рекурсії дорівнює найбільшому числу контекстів в стеку.

Зауважте вимоги до пам'яті. Контексти потребують її. В нашому випадку, піднесення до степеня `n` потребує пам'яті для `n` контекстів, для усіх нижчих `n`.

Алгоритм на основі циклу більш заощадливий в контексті пам'яті:

```js
function pow(x, n) {
  let result = 1;

  for (let i = 0; i < n; i++) {
    result *= x;
  }

  return result;
}
```

Ітеративний `pow` використовує один контекст для зміни `i` та `result` в процесі. Вимоги до пам'яті нижчі, фіксовані та не залежать від `n`.

**Будь яка рекурсія може бути переписана як цикл. Цей варіант зазвичай є більш ефективним.**

...Але бувають особливі випадки, надміру коли функція використовує різні рекурсивні підвиклики залежно від умов та поглинає результати, або коли маємо заплутане розгалуження. Оптимізація може бути непотрібна та не верта витрачених сил.

Рекурсія може дати коротший код, який легше розуміти та підтримувати. Оптимізації не є необхідними всюди, але часто нам потрібен хороший код, для отримання якого це і використовується.

## Рекурсивні обходи

Інше чудове застосування рекурсії -- рекурсивні обходи.

Уявімо, ми маємо компанію. Структура персоналу представлена об'єктом:

```js
let company = {
  sales: [{
    name: 'John',
    salary: 1000
  }, {
    name: 'Alice',
    salary: 1600
  }],

  development: {
    sites: [{
      name: 'Peter',
      salary: 2000
    }, {
      name: 'Alex',
      salary: 1800
    }],

    internals: [{
      name: 'Jack',
      salary: 1300
    }]
  }
};
```

Іншими словами, в компанії є відділи.

- Відділ може мати масив працівників. Наприклад, відділ `sales` містить 2 робітників: John and Alice.
- Або відділ може бути розділений на департаменти, як-от `development` має 2 гілки: `sites` і `internals`. Кожен містить своїх робітників.
- Також можливо, що коли департамент зростає, він розділяється на субдепартаменти (команди).

    Наприклад, відділ `sites` в майбутньому може бути розділено на команди для `siteA` та `siteB`. І вони потенційно можуть розгалудитись і далі. Цього немає на картинці, просто запам'ятайте.

Тепер, скажімо, ми хочемо, щоб функція розрахувала суму усіх зарплат. Як можна це зробити?

Ітеративний підхід не є простим через складну структуру. Перша ідея -- зробити цикл `for` по `company` із вкладеним циклом по першому рівню відділів. Але тоді необхідно більше вкладених циклів для ітерації по робітниками департаментів другого рівня як `sites`... І ще один вкладений цикл для третього рівня, що може з'явитися у майбутньому? Якщо створити 3-4 вкладених цикли в код для роботи з одним об'єктом, це виглядатиме жахливо.

Використаймо рекурсію.

Як можемо бачити, коли функція отримує відділ для підрахунку суми, є 2 можливих випадки:

1. Або це "простий" відділ з *масивом* людей -- тоді можна знайти суму за домогою простого циклу.
2. Або це *об'єкт* з `N` департаментів -- тоді можемо виконати `N` рекурсивних викликів, щоб отримати суму по кожному субдепартаменту, та поєднати результати.

Перший випадок -- це база рекурсії, простий випадок, коли ми отримуємо масив.

Другий випадок, коли ми отримуємо об'єкт, -- рекурсивний крок. Складна задача розбивається на підзадачі для менших департаметів. Це може продовжуватись, доки рано чи пізно розбиття закінчиться на (1).

Можливо, алгоритм навіть простіше зрозуміти через код:


```js run
let company = { // Той самий об'єкт, але скорочений для лаконічності
  sales: [{name: 'John', salary: 1000}, {name: 'Alice', salary: 1600 }],
  development: {
    sites: [{name: 'Peter', salary: 2000}, {name: 'Alex', salary: 1800 }],
    internals: [{name: 'Jack', salary: 1300}]
  }
};

// Функція, що виконує поставлену задачу
*!*
function sumSalaries(department) {
  if (Array.isArray(department)) { // case (1)
    return department.reduce((prev, current) => prev + current.salary, 0); // sum the array
  } else { // case (2)
    let sum = 0;
    for (let subdep of Object.values(department)) {
      sum += sumSalaries(subdep); // recursively call for subdepartments, sum the results
    }
    return sum;
  }
}
*/!*

alert(sumSalaries(company)); // 7700
```

Код короткий та простий для розуміння. В цьому сила рекурсії. Це так само працює для будь-якого рівня вкладених субдепартаментів.

Діаграма викликів:

![Рекурсивні зарплати](recursive-salaries.svg)

Можна легко побачити принцип: для об'єкта `{...}` робляться підвиклики, в той час як масиви -- це "листочки" на дереві рекурсії, вони дають окремі результати.

Зауважте, що код використовує корисні прийоми, згадані вище:

- Метод `arr.reduce`, пояснений в розділі <info:array-methods>, використовується для обчислення суми членів масиву.
- Цикл `for(val of Object.values(obj))` для ітерації по значенням об'єкту: `Object.values` повертає масив значень.


## Рекурсивні структури

Рекурсивна (рекурсивно-визначена) структура даних -- це структура, що відтворює себе по частинах.

Ми бачили це на прикладі структури компанії.

*Відділ* компанії -- це:
- Або масив людей.
- Або об'єкт з *департаментами*.

Для веб-розробників існуєть більш відомі приклади: HTML і XML документи.

У HTML документі, *HTML-тег* може містити список з:
- Частин тексту.
- HTML-коментарів.
- Інших *HTML-тегів* (які в свою чергу можуть містити частини тексту/коментарі або інші теги і т.д.).

Знову маємо рекурсивне визначення.

Для кращого розуміння, ми розглянемо ще одну рекурсивну структуру під назвою "Зв'язний список", яка може бути кращою альтернативою масивам у деяких випадках.

### Зв'язний список

Уявімо, ми хочемо зберігати впорядкований список об'єктів.

Природно, нашим вибором буде масив:

```js
let arr = [obj1, obj2, obj3];
```

...But there's a problem with arrays. The "delete element" and "insert element" operations are expensive. For instance, `arr.unshift(obj)` operation has to renumber all elements to make room for a new `obj`, and if the array is big, it takes time. Same with `arr.shift()`.

The only structural modifications that do not require mass-renumbering are those that operate with the end of array: `arr.push/pop`. So an array can be quite slow for big queues, when we have to work with the beginning.

Alternatively, if we really need fast insertion/deletion, we can choose another data structure called a [linked list](https://en.wikipedia.org/wiki/Linked_list).

The *linked list element* is recursively defined as an object with:
- `value`.
- `next` property referencing the next *linked list element* or `null` if that's the end.

For instance:

```js
let list = {
  value: 1,
  next: {
    value: 2,
    next: {
      value: 3,
      next: {
        value: 4,
        next: null
      }
    }
  }
};
```

Graphical representation of the list:

![linked list](linked-list.svg)

An alternative code for creation:

```js no-beautify
let list = { value: 1 };
list.next = { value: 2 };
list.next.next = { value: 3 };
list.next.next.next = { value: 4 };
list.next.next.next.next = null;
```

Here we can even more clearly see that there are multiple objects, each one has the `value` and `next` pointing to the neighbour. The `list` variable is the first object in the chain, so following `next` pointers from it we can reach any element.

The list can be easily split into multiple parts and later joined back:

```js
let secondList = list.next.next;
list.next.next = null;
```

![linked list split](linked-list-split.svg)

To join:

```js
list.next.next = secondList;
```

And surely we can insert or remove items in any place.

For instance, to prepend a new value, we need to update the head of the list:

```js
let list = { value: 1 };
list.next = { value: 2 };
list.next.next = { value: 3 };
list.next.next.next = { value: 4 };

*!*
// prepend the new value to the list
list = { value: "new item", next: list };
*/!*
```

![linked list](linked-list-0.svg)

To remove a value from the middle, change `next` of the previous one:

```js
list.next = list.next.next;
```

![linked list](linked-list-remove-1.svg)

We made `list.next` jump over `1` to value `2`. The value `1` is now excluded from the chain. If it's not stored anywhere else, it will be automatically removed from the memory.

Unlike arrays, there's no mass-renumbering, we can easily rearrange elements.

Naturally, lists are not always better than arrays. Otherwise everyone would use only lists.

The main drawback is that we can't easily access an element by its number. In an array that's easy: `arr[n]` is a direct reference. But in the list we need to start from the first item and go `next` `N` times to get the Nth element.

...But we don't always need such operations. For instance, when we need a queue or even a [deque](https://en.wikipedia.org/wiki/Double-ended_queue) -- the ordered structure that must allow very fast adding/removing elements from both ends, but access to its middle is not needed.

Lists can be enhanced:
- We can add property `prev` in addition to `next` to reference the previous element, to move back easily.
- We can also add a variable named `tail` referencing the last element of the list (and update it when adding/removing elements from the end).
- ...The data structure may vary according to our needs.

## Summary

Terms:
- *Recursion*  is a programming term that means calling a function from itself. Recursive functions can be used to solve tasks in elegant ways.

    When a function calls itself, that's called a *recursion step*. The *basis* of recursion is function arguments that make the task so simple that the function does not make further calls.

- A [recursively-defined](https://en.wikipedia.org/wiki/Recursive_data_type) data structure is a data structure that can be defined using itself.

    For instance, the linked list can be defined as a data structure consisting of an object referencing a list (or null).

    ```js
    list = { value, next -> list }
    ```

    Trees like HTML elements tree or the department tree from this chapter are also naturally recursive: they branch and every branch can have other branches.

    Recursive functions can be used to walk them as we've seen in the `sumSalary` example.

Any recursive function can be rewritten into an iterative one. And that's sometimes required to optimize stuff. But for many tasks a recursive solution is fast enough and easier to write and support.
