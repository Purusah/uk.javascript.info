# Експорт та імпорт

Директиви експорт та імпорт мають декілька варіантів синтаксису.

В попередній статті ми вже бачили приклад простого використання, тому давайте розглянемо ще декілька прикладів.

## Експорт перед оголошенням

Будь-яке оголошення змінної, функції чи класу можна позначати попереду оператором `export`.

Наприклад, всі наступні експорти валідні:

```js
// експорт масиву
*!*export*/!* let months = ['Січ', 'Лют', 'Бер','Квіт', 'Серп', 'Вер', 'Жов', 'Лист', 'Груд'];

// експорт константи
*!*export*/!* const MODULES_BECAME_STANDARD_YEAR = 2015;

// експорт класу
*!*export*/!* class User {
  constructor(name) {
    this.name = name;
  }
}
```

````smart header="Не потрібно ставити крапку з комою після експорту класу чи функції"
Зверніть увагу, `export` перед класом чи функцією не перетворює її в [функціональний вираз](info:function-expressions). Це все ще оголошення функції, хоч і експортованої.

Більшість JavaScript стилів коду не рекомендують ставити крапку з комою після оголошення функції та класу.

Тому не потрібно додавати крапку з комою в кінці `export class` та `export function`:

```js
export function sayHi(user) {
  alert(`Привіт, ${user}!`);
} *!* // знак ; відсутній вкінці */!*
```

````

## Експорт поза оголошенням

Також `export` можна використовувати окремо.

В прикладі ми спочатку оголошуємо функцію, а потім експортуємо:

```js
// 📁 say.js
function sayHi(user) {
  alert(`Привіт, ${user}!`);
}

function sayBye(user) {
  alert(`Бувай, ${user}!`);
}

*!*
export {sayHi, sayBye}; // список експортованих змінних
*/!*
```

...Чи, технічно, ми можемо використати `export` вище оголошення функції.

## Імпорт *

Зазвичай, список того, що потрібно імпортувати розташовують у фігурні дужки `import {...}`, як у прикладі:

```js
// 📁 main.js
*!*
import {sayHi, sayBye} from './say.js';
*/!*

sayHi('Іван'); // Привіт, Іван!
sayBye('Іван'); // Бувай, Іван!
```

But if there's a lot to import, we can import everything as an object using `import * as <obj>`, for instance:
Якщо потрібно імпортувати дуже багато сутностей, ми можемо імпортувати все, як об'єкт з використанням `import * as <obj>`, наприклад:

```js
// 📁 main.js
*!*
import * as say from './say.js';
*/!*

say.sayHi('Іван');
say.sayBye('Іван');
```

З першого погляду, "імпортувати все" виглядає цікавим та зручним у використанні, тоді навіщо нам явно виписувати список того, що потрібно імпортувати?

На це є декілька причин.

1. Сучасні інструменти збирання ([webpack](http://webpack.github.io) та інші) об'єднують модулі разом, оптимізують їх для пришвидшення завантаження та видаляють невикористані частини.

    Скажімо, ми додали сторонню бібліотеку, з багатьма функціями, `say.js` до нашого проекту:
    ```js
    // 📁 say.js
    export function sayHi() { ... }
    export function sayBye() { ... }
    export function becomeSilent() { ... }
    ```

    Припустимо, ми використовуємо тільки одну функцію з `say.js` в нашому проекті:
    ```js
    // 📁 main.js
    import {sayHi} from './say.js';
    ```
    ...Тоді оптимізатор побачить це та видалить інші функції із об'єднаного коду, що робить зібраний проект меншим. Так званий "tree-shaking".

2. Явний список того, що потрібно імпортувати дає коротші імена: `sayHi()` замість `say.sayHi()`.
3. Явний список того, що потрібно імпортувати дає краще розуміння структури коду: що використано та в якому місці. Також дозволяє підтримувати та рефакторити код легше.

## Імпорт "as"

Для імпорту під іншим іменем можна використовувати `as`.

Наприклад, для спрощення імпортуймо `sayHi` в локальну змінну `hi` та `sayBye` як `bye`:

```js
// 📁 main.js
*!*
import {sayHi as hi, sayBye as bye} from './say.js';
*/!*

hi('Іван'); // Привіт, Іван!
bye('Іван'); // Бувай, Іван!
```

## Експорт "as"

Подібний синтаксис існує і для `export`.

Експортуймо функцію як `hi` та `bye`:

```js
// 📁 say.js
...
export {sayHi as hi, sayBye as bye};
```

Тепер `hi` та `bye` будуть використовуватися зовнішніми модулями при імпорті:

```js
// 📁 main.js
import * as say from './say.js';

say.*!*hi*/!*('Іван'); // Привіт, Іван!
say.*!*bye*/!*('Іван'); // Бувай, Іван!
```

## Експорт за замовчуванням

На практиці існує два головних типи модулів.

1. Модулі, що містять бібліотеку -- набір функцій, як `say.js` вище.
2. Модулі, що визначають єдину сутність, тобто модуль `user.js` експортує тільки `class User`.

Переважно, надавати перевагу потрібно другому підходу, таким чином усі "сутності" знаходяться у власних модулях.

Природно, що це вимагає великої кількості файлів, щоб усе мало власні модулі, але це зовсім не проблема. Насправді, навігація в коді полегшується, якщо всі файли мають вдалу назву та структуровані в теках.

Модулі використовують спеціальний синтаксис `export default` ("експорт за замовчуванням") для створення єдиності сутності та полегшення доступності.

Якщо сутність потрібно експортувати -- попереду потрібно поставити `export default`:

```js
// 📁 user.js
export *!*default*/!* class User { // потрібно додати лише "default"
  constructor(name) {
    this.name = name;
  }
}
```

В кожному файлі може бути тільки одне використання `export default`.

...А потім потрібно виконати імпорт без фігурних дужок:

```js
// 📁 main.js
import *!*User*/!* from './user.js'; // не {User}, а просто User

new User('John');
```

Імпорти без фігурних дужок виглядають краще. Коли починають використовувати модулі, поширеною помилкою є невикористання фігурних дуже взагалі. Отже, `import` потребує фігурних дуже для іменованих імпортів та не потребує, якщо це імпорт за замовчуванням.

| Іменований експорт | Експорт за замовчуванням |
|--------------|----------------|
| `export class User {...}` | `export default class User {...}` |
| `import {User} from ...` | `import User from ...`|

Технічно, ми можемо використовувати іменовані експорти та за замовчуванням в одному модулі, але, на практиці, їх не прийнято змішувати. Модулі мають або іменований експорт, або за замовчуванням.

Оскільки, тільки одна сутність може бути експортованою за замовчування, вона може не мати імені.

В прикладі використано правильні експорти за замовчування:

```js
export default class { // відсутнє ім'я класу
  constructor() { ... }
}
```

```js
export default function(user) { // відсутнє ім'я функції
  alert(`Привіт, ${user}!`);
}
```

```js
// експортовано єдину змінну без оголошення
export default ['Jan', 'Feb', 'Mar','Apr', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];
```

Відсутність імені є абсолютно нормальним, бо може бути тільки одне використання `export default` у файлі. Таким чином `import` без фігурних дужок знає, що імпортувати.

Такий експорт без `default` згенерує помилку:

```js
export class { // Помилка! (експорт за замовчування потребує імені)
  constructor() {}
}
```

### The "default" name

In some situations the `default` keyword is used to reference the default export.

For example, to export a function separately from its definition:

```js
function sayHi(user) {
  alert(`Hello, ${user}!`);
}

// same as if we added "export default" before the function
export {sayHi as default};
```

Or, another situation, let's say a module `user.js` exports one main "default" thing, and a few named ones (rarely the case, but it happens):

```js
// 📁 user.js
export default class User {
  constructor(name) {
    this.name = name;
  }
}

export function sayHi(user) {
  alert(`Hello, ${user}!`);
}
```

Here's how to import the default export along with a named one:

```js
// 📁 main.js
import {*!*default as User*/!*, sayHi} from './user.js';

new User('John');
```

And, finally, if importing everything `*` as an object, then the `default` property is exactly the default export:

```js
// 📁 main.js
import * as user from './user.js';

let User = user.default; // the default export
new User('John');
```

### A word against default exports

Named exports are explicit. They exactly name what they import, so we have that information from them; that's a good thing.

Named exports force us to use exactly the right name to import:

```js
import {User} from './user.js';
// import {MyUser} won't work, the name must be {User}
```

...While for a default export, we always choose the name when importing:

```js
import User from './user.js'; // works
import MyUser from './user.js'; // works too
// could be import Anything... and it'll still work
```

So team members may use different names to import the same thing, and that's not good.

Usually, to avoid that and keep the code consistent, there's a rule that imported variables should correspond to file names, e.g:

```js
import User from './user.js';
import LoginForm from './loginForm.js';
import func from '/path/to/func.js';
...
```

Still, some teams consider it a serious drawback of default exports. So they prefer to always use named exports. Even if only a single thing is exported, it's still exported under a name, without `default`.

That also makes re-export (see below) a little bit easier.

## Re-export

"Re-export" syntax `export ... from ...` allows to import things and immediately export them (possibly under another name), like this:

```js
export {sayHi} from './say.js'; // re-export sayHi

export {default as User} from './user.js'; // re-export default
```

Why would that be needed? Let's see a practical use case.

Imagine, we're writing a "package": a folder with a lot of modules, with some of the functionality exported outside (tools like NPM allow us to publish and distribute such packages, but we don't have to use them), and many modules are just "helpers", for internal use in other package modules.

The file structure could be like this:
```
auth/
    index.js
    user.js
    helpers.js
    tests/
        login.js
    providers/
        github.js
        facebook.js
        ...
```

We'd like to expose the package functionality via a single entry point.

In other words, a person who would like to use our package, should import only from the "main file" `auth/index.js`.

Like this:

```js
import {login, logout} from 'auth/index.js'
```

The "main file", `auth/index.js` exports all the functionality that we'd like to provide in our package.

The idea is that outsiders, other programmers who use our package, should not meddle with its internal structure, search for files inside our package folder. We export only what's necessary in `auth/index.js` and keep the rest hidden from prying eyes.

As the actual exported functionality is scattered among the package, we can import it into `auth/index.js` and export from it:

```js
// 📁 auth/index.js

// import login/logout and immediately export them
import {login, logout} from './helpers.js';
export {login, logout};

// import default as User and export it
import User from './user.js';
export {User};
...
```

Now users of our package can `import {login} from "auth/index.js"`.

The syntax `export ... from ...` is just a shorter notation for such import-export:

```js
// 📁 auth/index.js
// re-export login/logout
export {login, logout} from './helpers.js';

// re-export the default export as User
export {default as User} from './user.js';
...
```

The notable difference of `export ... from` compared to `import/export` is that re-exported modules aren't available in the current file. So inside the above example of `auth/index.js` we can't use re-exported `login/logout` functions.

### Re-exporting the default export

The default export needs separate handling when re-exporting.

Let's say we have `user.js` with the `export default class User` and would like to re-export it:

```js
// 📁 user.js
export default class User {
  // ...
}
```

We can come across two problems with it:

1. `export User from './user.js'` won't work. That would lead to a syntax error.

    To re-export the default export, we have to write `export {default as User}`, as in the example above.

2. `export * from './user.js'` re-exports only named exports, but ignores the default one.

    If we'd like to re-export both named and the default export, then two statements are needed:
    ```js
    export * from './user.js'; // to re-export named exports
    export {default} from './user.js'; // to re-export the default export
    ```

Such oddities of re-exporting a default export are one of the reasons why some developers don't like default exports and prefer named ones.

## Summary

Here are all types of `export` that we covered in this and previous articles.

You can check yourself by reading them and recalling what they mean:

- Before declaration of a class/function/..:
  - `export [default] class/function/variable ...`
- Standalone export:
  - `export {x [as y], ...}`.
- Re-export:
  - `export {x [as y], ...} from "module"`
  - `export * from "module"` (doesn't re-export default).
  - `export {default [as y]} from "module"` (re-export default).

Import:

- Importing named exports:
  - `import {x [as y], ...} from "module"`
- Importing the default export:
  - `import x from "module"`
  - `import {default as x} from "module"`
- Import all:
  - `import * as obj from "module"`
- Import the module (its code runs), but do not assign any of its exports to variables:
  - `import "module"`

We can put `import/export` statements at the top or at the bottom of a script, that doesn't matter.

So, technically this code is fine:
```js
sayHi();

// ...

import {sayHi} from './say.js'; // import at the end of the file
```

In practice imports are usually at the start of the file, but that's only for more convenience.

**Please note that import/export statements don't work if inside `{...}`.**

A conditional import, like this, won't work:
```js
if (something) {
  import {sayHi} from "./say.js"; // Error: import must be at top level
}
```

...But what if we really need to import something conditionally? Or at the right time? Like, load a module upon request, when it's really needed?

We'll see dynamic imports in the next article.
