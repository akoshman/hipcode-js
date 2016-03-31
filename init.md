### Scope

```js
(function() {
   var a = b = 5;
})();

console.log(b);
```

Что выведется в консоль?

Пояснение: нюанс в том, что с помощью ключевого слова **var** переменная **a** объявлена как локальная, в то время как переменная **b** относится к глобальной области видимости. В консоль выведется **5**.

Ещё один нюанс заключается в том, что данный код работает потому, что в функции не используется *strict mode*. В случае его использования, будет ошибка доступа к переменной (*ReferenceError: assignment to undeclared variable b*). При использовании *strict mode* нужно явно указывать ссылку на глобальный объект:

```js
(function() {
   'use strict';
   var a = window.b = 5;
})();

console.log(b);
```

### Hoisting

```js
function test() {
   console.log(a);
   console.log(foo());
   
   var a = 1;
   function foo() {
      return 2;
   }
}

test();
```

Каков результат выполнения данного кода?

Пояснение: объявления переменной **a** и функции **foo** "всплывают" вверх тела функции **test**, но на момент вывода перменной **a** она будет неинициализирована. В консоль выведется **undefined** и **2**.

Иными словами, данный код эквивалентен следующему:

```js
function test() {
   var a;
   function foo() {
      return 2;
   }

   console.log(a);
   console.log(foo());
   
   a = 1;
}

test();
```

### Как работает this

Каков результат выполнения следующего кода?

```js
var fullname = 'John Doe';
var obj = {
   fullname: 'Colin Ihrig',
   prop: {
      fullname: 'Aurelio De Rosa',
      getFullname: function() {
         return this.fullname;
      }
   }
};

console.log(obj.prop.getFullname());

var test = obj.prop.getFullname;

console.log(test());
```

Пояснение: значение контекста функции, обозначаемого с помощью **this**, зависит от способа вызова функции, а не её объявления. В первом случае функция **getFullname** вызывается как метод объекта **obj.prop**, поэтому она вернёт значение свойства **fullname** этого объекта. Во втором случае, когда функция **getFullname** присваивается переменной **test**, контекст **this** указывает на глобальный объект (**window**). Это происходит потому, что **test** является свойством **window** и вызывается в его контексте. Следовательно, вернётся значение свойства **fullname** глобального объекта. В консоль выведется **Aurelio De Rosa** и **John Doe**.

### apply(), call() и bind()


```js
var name = 'Alice';
var obj = {
   name: 'Zoey',
   getName: function() {
      return this.name;
   }
};

var test = obj.getName;
console.log(test());    /* Как вывести 'Zoey', используя test? */
```

**Вопрос**: Как вывести 'Zoey', используя test?

**Пояснение**: при вызове **test()** контекст будет указывать на **window**. Чтобы изменить контекст вызова функции, можно применить одну из вспомогательных функций.

**Способ 1**. **test.apply(obj)** либо **test.call(obj)**. Различия между **Function.prototype.apply** и **Function.prototype.call** в способе передачи аргументов:

**fun.call(thisArg[, arg1[, arg2[, ...]]])** и **fun.apply(thisArg, [argsArray])**

**Способ 2**. **test.bind(obj)()**. Функция **Function.prototype.bind** оборачивает вызываемую функцию в контекст, который она принимает первым аргументом и возвращает функцию-обёртку, пригодную для запуска в нужном контексте. Данная функция доступна, начиная с **ES5**.
