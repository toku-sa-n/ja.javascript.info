<<<<<<< HEAD
# Proxy と Reflect

`Proxy` オブジェクトは別のオブジェクトをラップし、プロパティやその他の読み取り/書き込みなどの操作をインターセプトします。必要に応じて、それらを独自に処理したり、オブジェクトが透過的にそれらを処理できるようにします。

Proxy は多くのライブラリや一部のブラウザフレームワークで使われています。このチャプターでは、多くの実践的なアプリケーションを紹介します。

構文:
=======
# Proxy and Reflect

A `Proxy` object wraps another object and intercepts operations, like reading/writing properties and others, optionally handling them on its own, or transparently allowing the object to handle them.

Proxies are used in many libraries and some browser frameworks. We'll see many practical applications in this chapter.

The syntax:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js
let proxy = new Proxy(target, handler)
```

<<<<<<< HEAD
- `target` -- ラップするオブジェクトです。関数を含め何でもOKです。
- `handler` -- プロキシ設定: 操作をインターセプトするメソッド "traps" をもつオブジェクトです。例: `get` トラップは `target` のプロパティの読み取り用、`set` トラップは、`target` へのプロパティ書き込み用、など。

`proxy` の操作では、`handler` に対応するトラップがある場合はそれが実行されます。それ以外の場合は、操作は `target` で実行されます。

最初の例として、トラップなしで proxy を作ってみましょう。:

```js run
let target = {};
let proxy = new Proxy(target, {}); // 空のハンドラ

proxy.test = 5; // プロキシへの書き込み (1)
alert(target.test); // 5, プロパティが target で現れました!

alert(proxy.test); // 5, proxy からの読み取ることができます (2)

for(let key in proxy) alert(key); // test, イテレーションも機能します (3)
```

traps がないので、`proxy` 上のすべての操作は `target` に転送されます。

1. 書き込み操作 `proxy.test=` は `target` に値を設定します。
2. 読み込み操作 `proxy.test` は `target` からの値を返します。
3. `proxy` のイテレートは、`target` からの値を返します。

ご覧の通り、traps がない場合は `proxy` は `target` に対する透過的なラッパーです。

![](proxy.svg)  

`Proxy` は特別な "エキゾチックオブジェクト(exotic object)" です。`Proxy` は独自のプロパティは持っていません。空の `handler` の場合は、透過的に `target` へ操作を転送します。

さらに機能を有効にするために、traps を追加しましょう。

これによって、何がインターセプトできるでしょう？

オブジェクトに対するほとんどの操作に対しては、JavaScript の仕様で いわゆる "内部メソッド" と呼ばれるものがあり、仕様ではそれらがどのように動作するかを最も低レベルで説明しています。例えば、 `[[Get]]` は、プロパティを読み取るための内部メソッドで、`[[Set]]` はプロパティを書き込むための内部メソッド、などです。これらのメソッドは仕様でのみ使用されており、名前を使ってそれらを直接使用することはできません。

Proxy traps はこれらのメソッドの呼び出しをインターセプトします。これらのメソッドは[Proxy specification](https://tc39.es/ecma262/#sec-proxy-object-internal-methods-and-internal-slots) 及び以下の表にリストされています。

内部メソッドと trap(操作をインターセプトするために `new Proxy` の `handler` パラメータに追加するメソッド名)の対応表です。

| 内部メソッド | ハンドラメソッド | いつ発生するか |
|-----------------|----------------|-------------|
| `[[Get]]` | `get` | プロパティ読み取り時 |
| `[[Set]]` | `set` | プロパティ書き込み時 |
| `[[HasProperty]]` | `has` | `in` 演算子 |
| `[[Delete]]` | `deleteProperty` | `delete` 演算子 |
| `[[Call]]` | `apply` | 関数呼び出し |
| `[[Construct]]` | `construct` | `new` 演算子 |
=======
- `target` -- is an object to wrap, can be anything, including functions.
- `handler` -- proxy configuration: an object with "traps": methods that intercept operations., e.g. `get` trap is for reading a property of `target`, `set` trap - for writing a property into `target`, etc.

For operations on `proxy`, if there's a corresponding trap in `handler`, then it runs, and the proxy has a chance to handle it, otherwise the operation is performed on `target`.

As a starting example, let's create a proxy without any traps:

```js run
let target = {};
let proxy = new Proxy(target, {}); // empty handler

proxy.test = 5; // writing to proxy (1)
alert(target.test); // 5, the property appeared in target!

alert(proxy.test); // 5, we can read it from proxy too (2)

for(let key in proxy) alert(key); // test, iteration works (3)
```

As there are no traps, all operations on `proxy` are forwarded to `target`.

1. A writing operation `proxy.test=` sets the value on `target`.
2. A reading operation `proxy.test` returns the value from `target`.
3. Iteration over `proxy` returns values from `target`.

As we can see, without any traps, `proxy` is a transparent wrapper around `target`.

![](proxy.svg)  

`Proxy` is a special "exotic object". It doesn't have own properties. With an empty `handler` it transparently forwards operations to `target`.

To activate more capabilities, let's add traps.

What can we intercept with them?

For most operations on objects, there's a so-called "internal method" in JavaScript specificaiton, that describes on the lowest level, how it works. For instance, `[[Get]]` - the internal method to read a property, `[[Set]]` -- the internal method to write a property, and so on. These methods are only used in the specification, we can't call them directly by name.

Proxy traps inercept invocations of these methods. They are listed in [Proxy specification](https://tc39.es/ecma262/#sec-proxy-object-internal-methods-and-internal-slots) and in the table below.

For every internal method, there's a trap in this table: the name of the method that we can add to `handler` parameter of `new Proxy` to intercept the operation:

| Internal Method | Handler Method | Triggers when... |
|-----------------|----------------|-------------|
| `[[Get]]` | `get` | reading a property |
| `[[Set]]` | `set` | writing to a property |
| `[[HasProperty]]` | `has` | `in` operator |
| `[[Delete]]` | `deleteProperty` | `delete` operator |
| `[[Call]]` | `apply` | function call |
| `[[Construct]]` | `construct` | `new` operator |
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
| `[[GetPrototypeOf]]` | `getPrototypeOf` | [Object.getPrototypeOf](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf) |
| `[[SetPrototypeOf]]` | `setPrototypeOf` | [Object.setPrototypeOf](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf) |
| `[[IsExtensible]]` | `isExtensible` | [Object.isExtensible](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/isExtensible) |
| `[[PreventExtensions]]` | `preventExtensions` | [Object.preventExtensions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/preventExtensions) |
| `[[DefineOwnProperty]]` | `defineProperty` | [Object.defineProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty), [Object.defineProperties](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperties) |
| `[[GetOwnProperty]]` | `getOwnPropertyDescriptor` | [Object.getOwnPropertyDescriptor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptor), `for..in`, `Object.keys/values/entries` |
| `[[OwnPropertyKeys]]` | `ownKeys` | [Object.getOwnPropertyNames](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames), [Object.getOwnPropertySymbols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols), `for..in`, `Object/keys/values/entries` |

```warn header="Invariants"
<<<<<<< HEAD
JavaScript にはいくつかの不変条件(内部メソッドと traps によって満たされるべき条件)があります。

そのほとんどは戻り値に関してです:
- `[[Set]]` は値が正常に書き込まれた場合には `true` を、そうでなければ `false` を返す必要があります。
- `[[Delete]]` は値が正常に削除された場合には `true` を、そうでなければ `false` を返す必要があります。
- ...などです。以下の例で詳しく見ていきます。

他にも以下のようないくつかの不変条件があります:
- proxy オブジェクトに適用される `[[GetPrototypeOf]]` は proxy オブジェクトのターゲットオブジェクトに適用される `[[GetPrototypeOf]]` と同じ値を返さなければなりません。つまり、proxy のプロトタイプを参照すると、常にターゲットオブジェクトのプロトタイプが返却される必要があります。

traps はこれらの操作をインターセプトできますが、これらのルールには従う必要があります。

不変条件は、言語機能の正しさと一貫した動作を保証するものです。完全な不変条件のリストは [仕様]
(https://tc39.es/ecma262/#sec-proxy-object-internal-methods-and-internal-slots)にありますが、変なことをしない限りは違反することはないでしょう。
```

実際の例でそれがどのように動作するのかを見てみましょう。

## "get" トラップでのデフォルト値

最も一般的なトラップ(traps)はプロパティの読み書きです。

読み取りをインターセプトするには、`handler` に `get(target, property, receiver)` が必要です。

これはプロパティが読み取られたとき、以下の引数で実行されます。:

- `target`: `new Proxy` の最初の引数として渡されるターゲットオブジェクトです。
- `property` -- プロパティ名,
- `receiver` --ターゲットプロパティが getter の場合、`receiver` はその呼び出しの中で `this` として使われるオブジェクトです。通常、これは `proxy` オブジェクト自身(あるいは、proxy から継承している場合は、継承したオブジェクト)です。現時点ではこの引数は不要です。詳細については後ほど説明します。

オブジェクトのデフォルト値を実装するのに `get` を使ってみましょう。

存在しない値の場合 `0` を返す数値配列を作ります。

通常、存在しない値を取得しようとすると `undefined` になりますが、ここでは通常の配列に対して、プロパティが存在しない場合に `0` を返すプロキシでラップします。:
=======
JavaScript enforces some invariants -- conditions that must be fulfilled by internal methods and traps.

Most of them are for return values:
- `[[Set]]` must return `true` if the value was written successfully, otherwise `false`.
- `[[Delete]]` must return `true` if the value was deleted successfully, otherwise `false`.
- ...and so on, we'll see more in examples below.

There are some other invariants, like:
- `[[GetPrototypeOf]]`, applied to the proxy object must return the same value as `[[GetPrototypeOf]]` applied to the proxy object's target object. In other words, reading prototype of a proxy must always return the prototype of the target object.

Traps can intercept these operations, but they must follow these rules.

Invariants ensure correct and consistent behavior of language features. The full invariants list is in [the specification](https://tc39.es/ecma262/#sec-proxy-object-internal-methods-and-internal-slots), you probably won't violate them, if not doing something weird.
```

Let's see how that works on practical examples.

## Default value with "get" trap

The most common traps are for reading/writing properties.

To intercept the reading, the `handler` should have a method `get(target, property, receiver)`.

It triggers when a property is read, with following arguments:

- `target` -- is the target object, the one passed as the first argument to `new Proxy`,
- `property` -- property name,
- `receiver` -- if the target property is a getter, then `receiver` is the object that's going to be used as `this` in its call. Usually that's the `proxy` object itself (or an object that inherits from it, if we inherit from proxy). Right now we don't need this argument, will be explained in more details letter.

Let's use `get` to implement default values for an object.

We'll make a numeric array that returns `0` for non-existant values.

Usually when one tries to get a non-existing array item, they get `undefined`, but we'll wrap a regular array into proxy that traps reading and returns `0` if there's no such property:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js run
let numbers = [0, 1, 2];

numbers = new Proxy(numbers, {
  get(target, prop) {
    if (prop in target) {
      return target[prop];
    } else {
<<<<<<< HEAD
      return 0; // デフォルト値
=======
      return 0; // default value
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
    }
  }
});

*!*
alert( numbers[1] ); // 1
<<<<<<< HEAD
alert( numbers[123] ); // 0 (このような項目はなし)
*/!*
```

ご覧の通り、`get` トラップを使用するのは非常に簡単です。

`Proxy` を利用すると、任意の "デフォルト値" 用のロジックを組むことができます。

想像してください、フレーズと一緒に翻訳を持つ辞書があるとします:
=======
alert( numbers[123] ); // 0 (no such item)
*/!*
```

As we can see, it's quite easy to do with `get` trap.

We can use `Proxy` to implement any logic for "default" values.

Imagine, we have a dictionary with phrases along with translations:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js run
let dictionary = {
  'Hello': 'Hola',
  'Bye': 'Adiós'
};

alert( dictionary['Hello'] ); // Hola
alert( dictionary['Welcome'] ); // undefined
```

<<<<<<< HEAD
現在、フレーズがない場合、`dictionary` の読み取りは `undefined` を返します。しかし、実際には `undefined` よりも未翻訳のままのフレーズを残すほうがよいです。なので、このような場合に `undefined` ではなく、未翻訳のフレーズを返すようにしましょう。

そのためには、`directory` を読み取り操作をインターセプトするプロキシでラップします。:
=======
Right now, if there's no phrase, reading from `dictionary` returns `undefined`. But in practice, leaving a phrase non-translated is usually better than `undefined`. So let's make it return a non-translated phrase in that case instead of `undefined`.

To achieve that, we'll wrap `dictionary` in a proxy that intercepts reading operations:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js run
let dictionary = {
  'Hello': 'Hola',
  'Bye': 'Adiós'
};

dictionary = new Proxy(dictionary, {
*!*
<<<<<<< HEAD
  get(target, phrase) { // 辞書(dictionary)からのプロパティ読み取りをインターセプト
*/!*
    if (phrase in target) { // 辞書の中にある場合
      return target[phrase]; // 翻訳を返します
    } else {
      // そうでなければフレーズをそのまま返します
=======
  get(target, phrase) { // intercept reading a property from dictionary
*/!*
    if (phrase in target) { // if we have it in the dictionary
      return target[phrase]; // return the translation
    } else {
      // otherwise, return the non-translated phrase
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
      return phrase;
    }
  }
});

<<<<<<< HEAD
// 辞書で任意のフレーズを検索します
// 辞書にない場合は翻訳されません
alert( dictionary['Hello'] ); // Hola
*!*
alert( dictionary['Welcome to Proxy']); // Welcome to Proxy
=======
// Look up arbitrary phrases in the dictionary!
// At worst, they are not translated.
alert( dictionary['Hello'] ); // Hola
*!*
alert( dictionary['Welcome to Proxy']); // Welcome to Proxy (no translation)
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
*/!*
```

````smart
<<<<<<< HEAD
プロキシがどのように変数を上書きするかに注意してください。:
=======
Please note how the proxy overwrites the variable:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js
dictionary = new Proxy(dictionary, ...);
```

<<<<<<< HEAD
プロキシはどこでもターゲットオブジェクトを完全に置き換える必要があります。プロキシされた後はターゲットオブジェクトを参照しないでください。参照すると、簡単に台無しになります。
````

## "set" トラップでのバリデーション

数値専用の配列がほしいとしましょう。別の型の値が追加された場合、エラーにする必要があります。

`set` トラップはプロパティが書き込まれたときに発生します。

`set(target, property, value, receiver)`:

- `target`: `new Proxy` の最初の引数として渡されるターゲットオブジェクトです。
- `property`: プロパティ名
- `value`: プロパティ値,
- `receiver`: `get` と同様で、setter プロパティに関係します。

`set` トラップは設定が成功すると `true` を、それ以外の場合は `false` (`TypeError` が発生)を返す必要があります。

新しい値を検証するのに使って見ましょう:
=======
The proxy should totally replace the target object everywhere. No one should ever reference the target object after it got proxied. Otherwise it's easy to mess up.
````

## Validation with "set" trap

Let's say we want an array exclusively for numbers. If a value of another type is added, there should be an error.

The `set` trap triggers when a property is written.

`set(target, property, value, receiver)`:

- `target` -- is the target object, the one passed as the first argument to `new Proxy`,
- `property` -- property name,
- `value` -- property value,
- `receiver` -- similar to `get` trap, matters only for setter properties.

The `set` trap should return `true` if setting is successful, and `false` otherwise (triggers `TypeError`).

Let's use it to validate new values:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js run
let numbers = [];

numbers = new Proxy(numbers, { // (*)
*!*
<<<<<<< HEAD
  set(target, prop, val) { // プロパティの書き込みをインターセプト
=======
  set(target, prop, val) { // to intercept property writing
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
*/!*
    if (typeof val == 'number') {
      target[prop] = val;
      return true;
    } else {
      return false;
    }
  }
});

<<<<<<< HEAD
numbers.push(1); // 追加成功
numbers.push(2); // 追加成功
alert("Length is: " + numbers.length); // 2

*!*
numbers.push("test"); // TypeError (プロキシの 'set' が false を返却)
=======
numbers.push(1); // added successfully
numbers.push(2); // added successfully
alert("Length is: " + numbers.length); // 2

*!*
numbers.push("test"); // TypeError ('set' on proxy returned false)
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
*/!*

alert("This line is never reached (error in the line above)");
```

<<<<<<< HEAD
注目してください: 配列の組み込みの機能は依然として動作します! 値は `push` により追加されました。`length` プロパティは値が追加されたときにオートインクリメントされます。プロキシは何も破壊していません。

我々はチェック処理を追加するのに `push` や `unshift` のような、値を追加する配列メソッドを上書きする必要はありません。なぜなら、それらは内部的には `[[Set]]` 操作を使用しており、プロキシによりインターセプトされるからです。

したがって、コードはクリーンであり簡潔です。

```warn header="`true` を返すのを忘れないでください"
上記のように、維持すべき条件があります。

`set` の場合、書き込みの成功に対しては `true` を返さなければなりません。

それを忘れたり false を返すと、操作は `TypeError` をトリガーします。
```

## "ownKeys" と "getOwnPropertyDescriptor" によるイテレーション

`Object.keys`, `for..in` ループ及びオブジェクトプロパティをイテレートする他のほとんどのメソッドは `[[OwnPropertyKeys]]` 内部メソッド(`ownKeys` トラップによりインターセプトされる)を使用してプロパティのリストを取得しています。

このようなメソッドの詳細は異なります:
- `Object.getOwnPropertyNames(obj)` は "非" シンボルキーを返します。
- `Object.getOwnPropertySymbols(obj)` はシンボルキーを返します。
- `Object.keys/values()` は `enumerable` フラグ(プロパティフラグについては、チャプター <info:property-descriptors> に説明があります)を持つ非シンボルのキー/バリュー値を返します。
- `for..in` は `enumerable` フラグを持つ非シンボルキーとプロトタイプキーをループします。

...しかし、これらはすべてその内部メソッドで得られたリストから始まります。

以下の例では、`ownKeys` トラップを使用して `user` に対する `for..in` ループを行い、また `Object.keys` や `Object.values` を行っています。これらはアンダースコア `_` で始まるプロパティをスキップします。:
=======
Please note: the built-in functionality of arrays is still working! Values are added by `push`. The `length` property auto-increases when values are added. Our proxy doesn't break anything.

We don't have to override value-adding array methods like `push` and `unshift`, and so on, to add checks in there, because internally they use `[[Set]]` operation, that's intercepted by the proxy.

So the code is clean and concise.

```warn header="Don't forget to return `true`"
As said above, there are invariants to be held.

For `set`, it must return `true` for a successful write.

If we forget to do it or return any falsy value, the operation triggers `TypeError`.
```

## Iteration with "ownKeys" and "getOwnPropertyDescriptor"

`Object.keys`, `for..in` loop and most other methods that iterate over object properties use `[[OwnPropertyKeys]]` internal method (intercepted by `ownKeys` trap) to get a list of properties.

Such methods differ in details:
- `Object.getOwnPropertyNames(obj)` returns non-symbol keys.
- `Object.getOwnPropertySymbols(obj)` returns symbol keys.
- `Object.keys/values()` returns non-symbol keys/values with `enumerable` flag (property flags were explained in the chapter <info:property-descriptors>).
- `for..in` loops over non-symbol keys with `enumerable` flag, and also prototype keys.

...But all of them start with that list.

In the example below we use `ownKeys` trap to make `for..in` loop over `user`, and also `Object.keys` and `Object.values`, to skip properties starting with an underscore `_`:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js run
let user = {
  name: "John",
  age: 30,
  _password: "***"
};

user = new Proxy(user, {
*!*
  ownKeys(target) {
*/!*
    return Object.keys(target).filter(key => !key.startsWith('_'));
  }
});

<<<<<<< HEAD
// "ownKeys" は _password を除外します
for(let key in user) alert(key); // name, then: age

// これらのメソッドへも同じ影響があります:
=======
// "ownKeys" filters out _password
for(let key in user) alert(key); // name, then: age

// same effect on these methods:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
alert( Object.keys(user) ); // name,age
alert( Object.values(user) ); // John,30
```

<<<<<<< HEAD
これまでのところ、期待通り動作しています。

ですが、もしオブジェクトに存在しないキーを返した場合、`Object.keys` はそれをリストしません:
=======
So far, it works.

Although, if we return a key that doesn't exist in the object, `Object.keys` won't list it:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js run
let user = { };

user = new Proxy(user, {
*!*
  ownKeys(target) {
*/!*
    return ['a', 'b', 'c'];
  }
});

alert( Object.keys(user) ); // <empty>
```

<<<<<<< HEAD
なぜでしょう？理由は簡単です。: `Object.keys` は `enumerable` フラグを持つプロパティだけを返すからです。それを確かめるため、すべてのメソッドに対し内部メソッド `[[GetOwnProperty]]` を呼び出し,
[ディスクリプタ](info:property-descriptors) を取得します。すると、ここではプロパティがないので、そのディスクリプタは空であり、`enumerable` フラグがありません。そのため、スキップされます。

`Object.keys` がプロパティを返すには、`enumerable` 付きでオブジェクトに存在するか、`[[GetOwnProperty]]`(トラップは `getOwnPropertyDescriptor`)の呼び出しをインターセプトし、`enumerable: true` を持つディスクリプタを返します。

これはそのコードです:
=======
Why? The reason is simple: `Object.keys` returns only properties with `enumerable` flag. To check for it, it calls the internal method `[[GetOwnProperty]]` for every property to get [its descriptor](info:property-descriptors). And here, as there's no property, its descriptor is empty, no `enumerable` flag, so it's skipped.

For `Object.keys` to return a property, we need it either exist in the object, with `enumerable` flag, or we can intercept calls to `[[GetOwnProperty]]` (the trap `getOwnPropertyDescriptor` does it), and return a descriptor with `enumerable: true`.

Here's a working code:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js run
let user = { };

user = new Proxy(user, {
<<<<<<< HEAD
  ownKeys(target) { // プロパティのリストを取得するために一度だけ呼ばれます
    return ['a', 'b', 'c'];
  },

  getOwnPropertyDescriptor(target, prop) { // プロパティ毎に呼ばれます
=======
  ownKeys(target) { // called once to get a list of properties
    return ['a', 'b', 'c'];
  },

  getOwnPropertyDescriptor(target, prop) { // called for every property
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
    return {
      enumerable: true,
      configurable: true
      /* ...other flags, probable "value:..."" */
    };
  }

});

alert( Object.keys(user) ); // a, b, c
```

<<<<<<< HEAD
改めて留意してください: `[[GetOwnProperty]]` をインターセプトする必要があるのは、プロパティがオブジェクトにない場合のみです。

## "deleteProperty" 及び他のトラップで保護されたプロパティ

アンダースコア `_` で始まるプロパティやメソッドは内部的なものであるということは、広く知られた慣習です。それらはオブジェクトの外からアクセスされるべきではありません。

ですが、技術的には可能です:
=======
Let's note once again: we only need to intercept `[[GetOwnProperty]]` if the property is absent in the object.

## Protected properties with "deleteProperty" and other traps

There's a widespread convention that properties and methods prefixed by an underscore `_` are internal. They shouldn't be accessed from outside the object.

Technically, that's possible though:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js run
let user = {
  name: "John",
  _password: "secret"
};

<<<<<<< HEAD
alert(user._password); // secret
```

プロキシを使用して、`_` で始まるプロパティへのアクセスを防ぎましょう。

次のトラップが必要です:
- `get`: そのようなプロパティの読み込み時にエラーをスロー,
- `set`: 書き込み時にエラーをスロー,
- `deleteProperty`: 削除時にエラーをスロー,
- `ownKeys`: `for..in` や `Object.keys` のようなメソッドから `_` で始まるプロパティを除外

これがそのコードです:
=======
alert(user._password); // secret  
```

Let's use proxies to prevent any access to properties starting with `_`.

We'll need the traps:
- `get` to throw an error when reading such property,
- `set` to throw an error when writing,
- `deleteProperty` to throw an error when deleting,
- `ownKeys` to exclude properties starting with `_` from `for..in` and methods like `Object.keys`.

Here's the code:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js run
let user = {
  name: "John",
  _password: "***"
};

user = new Proxy(user, {
*!*
  get(target, prop) {
*/!*
    if (prop.startsWith('_')) {
      throw new Error("Access denied");
    }
    let value = target[prop];
    return (typeof value === 'function') ? value.bind(target) : value; // (*)
  },
*!*
<<<<<<< HEAD
  set(target, prop, val) { // プロパティの書き込みをインターセプト
=======
  set(target, prop, val) { // to intercept property writing
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
*/!*
    if (prop.startsWith('_')) {
      throw new Error("Access denied");
    } else {
      target[prop] = val;
      return true;
    }
  },
*!*
<<<<<<< HEAD
  deleteProperty(target, prop) { // プロパティの削除をインターセプト
=======
  deleteProperty(target, prop) { // to intercept property deletion
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
*/!*  
    if (prop.startsWith('_')) {
      throw new Error("Access denied");
    } else {
      delete target[prop];
      return true;
    }
  },
*!*
<<<<<<< HEAD
  ownKeys(target) { // プロパティのリストをインターセプト
=======
  ownKeys(target) { // to intercept property list
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
*/!*
    return Object.keys(target).filter(key => !key.startsWith('_'));
  }
});

<<<<<<< HEAD
// "get" は _password の読み込みを許可しません
=======
// "get" doesn't allow to read _password
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
try {
  alert(user._password); // Error: Access denied
} catch(e) { alert(e.message); }

<<<<<<< HEAD
// "set" は _password の書き込みを許可しません
=======
// "set" doesn't allow to write _password
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
try {
  user._password = "test"; // Error: Access denied
} catch(e) { alert(e.message); }

<<<<<<< HEAD
// "deleteProperty" は _password の削除を許可しません
=======
// "deleteProperty" doesn't allow to delete _password
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
try {
  delete user._password; // Error: Access denied
} catch(e) { alert(e.message); }

<<<<<<< HEAD
// "ownKeys" は _password を除外します
for(let key in user) alert(key); // name
```

`(*)` 行の `get` トラップの重要な点に注意してください:
=======
// "ownKeys" filters out _password
for(let key in user) alert(key); // name
```

Please note the important detail in `get` trap, in the line `(*)`:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js
get(target, prop) {
  // ...
  let value = target[prop];
*!*
  return (typeof value === 'function') ? value.bind(target) : value; // (*)
*/!*
}
```

<<<<<<< HEAD
なぜ関数の場合に `value.bind(target)` を呼び出す必要があるのでしょうか？

理由は `user.checkPassword()` のようなオブジェクトメソッドは `_password` へアクセスできる必要があるからです。:
=======
Why do we need for a function to call `value.bind(target)`?

The reason is that object methods, such as `user.checkPassword()`, must be able to access `_password`:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js
user = {
  // ...
  checkPassword(value) {
<<<<<<< HEAD
    // オブジェクトメソッドは _password へアクセスできなければいけません
=======
    // object method must be able to read _password
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
    return value === this._password;
  }
}
```

<<<<<<< HEAD
`user.checkPassword()` の呼び出しはプロキシされた `user` を `this` (ドットの前のオブジェクトが `this` になります)として取得するため、`this._password` へのアクセスを試みると `get` トラップが機能(これはあらゆるプロパティ読み取りでトリガーされます)し、エラーをスローします。

そのため、`(*)` の通りオブジェクトメソッドのコンテキストを元のオブジェクトである `target` でバインドします。以降、その呼び出しでは `this` としてトラップのない `target` を使用します。

この解決策はたいてい動作しますが、メソッドがプロキシされていないオブジェクトを別の場所に渡す可能性があるため理想的ではありません。これは混乱のもとになります: どこにオリジナルのオブジェクトがあり、どれがプロキシされたものなのか。

さらに、オブジェクトが何度もプロキシされる可能性もあります(複数のプロキシがそれぞれ異なる "微調整" をオブジェクトにする場合があります)。また、メソッドにラップされていないオブジェクトを渡した場合、予期しない結果になる可能性もあります。

したがって、このようなプロキシは使用しないことを推奨します。

```smart header="クラスの private プロパティ"
モダンな JavaScript エンジンはクラスの private プロパティをネイティブにサポートします(`#` から始まります)。これについてはチャプター <info:private-protected-properties-methods> で記載しています。プロキシは必要ありません。

ただし、このようなプロパティにも問題はあります。特にこれらは継承されません。
```

## "has" トラップを使用した "範囲内"

他の例を見てみましょう。

範囲を持つオブジェクトがあります:
=======

A call to `user.checkPassword()` call gets proxied `user` as `this` (the object before dot becomes `this`), so when it tries to access `this._password`, the `get` trap activates (it triggers on any property read) and throws an error.

So we bind the context of object methods to the original object, `target`, in the line `(*)`. Then their future calls will use `target` as `this`, without any traps.

That solution usually works, but isn't ideal, as a method may pass the unproxied object somewhere else, and then we'll get messed up: where's the original object, and where's the proxied one.

Besides, an object may be proxied multiple times (multiple proxies may add different "tweaks" to the object), and if we pass an unwrapped object to a method, there may be unexpected consequences.

So, such proxy shouldn't be used everywhere.

```smart header="Private properties of a class"
Modern JavaScript engines natively support private properties in classes, prefixed with `#`. They are described in the chapter <info:private-protected-properties-methods>. No proxies required.

Such properties have their own issues though. In particular, they are not inherited.
```

## "In range" with "has" trap

Let's see more examples.

We have a range object:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js
let range = {
  start: 1,
  end: 10
};
```

<<<<<<< HEAD
`in` 演算子を使って、 数値が `range` の範囲内にあるかを確認します。

`has` トラップは `in` 呼び出しをインターセプトします。

`has(target, property)`

- `target` -- `new Proxy` への最初の引数として渡されるターゲットオブジェクト
- `property` -- プロパティ名

デモです:
=======
We'd like to use `in` operator to check that a number is in `range`.

The `has` trap intercepts `in` calls.

`has(target, property)`

- `target` -- is the target object, passed as the first argument to `new Proxy`,
- `property` -- property name

Here's the demo:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js run
let range = {
  start: 1,
  end: 10
};

range = new Proxy(range, {
*!*
  has(target, prop) {
*/!*
    return prop >= target.start && prop <= target.end
  }
});

*!*
alert(5 in range); // true
alert(50 in range); // false
*/!*
```

<<<<<<< HEAD
良い糖衣構文ですね。それに実装もとても簡単です。

## Wrapping functions: "apply"

関数の周りに対しても同様に proxy をラップすることができます。

`apply(target, thisArg, args)` トラップはプロキシを関数として呼び出すよう処理をします:

- `target` はターゲットオブジェクトです(JavaScript では関数はオブジェクトです),
- `thisArg` は `this` の値です
- `args` は引数のリストです

例えば、チャプター <info:call-apply-decorators> で行った `delay(f, ms)` デコレータを思い出してください。

そのチャプターでは、proxy を使わずに実現しました。`delay(f, ms)` の呼び出しは、`ms` ミリ秒後に `f` の呼び出しを行う関数を返しました。

これは以前の関数ベースの実装です:

```js run
function delay(f, ms) {
  // タイムアウト後に f への呼び出しを渡すラッパー関数を返します
=======
A nice syntactic sugar, isn't it? And very simple to implement.

## Wrapping functions: "apply"

We can wrap a proxy around a function as well.

The `apply(target, thisArg, args)` trap handles calling a proxy as function:

- `target` is the target object (function is an object in JavaScript),
- `thisArg` is the value of `this`.
- `args` is a list of arguments.

For example, let's recall `delay(f, ms)` decorator, that we did in the chapter <info:call-apply-decorators>.

In that chapter we did it without proxies. A call to `delay(f, ms)` returned a function that forwards all calls to `f` after `ms` milliseconds.

Here's the previous, function-based implementation:

```js run
function delay(f, ms) {
  // return a wrapper that passes the call to f after the timeout
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
  return function() { // (*)
    setTimeout(() => f.apply(this, arguments), ms);
  };
}

function sayHi(user) {
  alert(`Hello, ${user}!`);
}

<<<<<<< HEAD
// このラップをすると、sahHi 呼び出しは 3秒間遅延します
sayHi = delay(sayHi, 3000);

sayHi("John"); // Hello, John! (3秒後)
```

すでにご覧になったように、こはほぼほぼ機能します。ラッパー関数 `(*)` はタイムアウト後に呼び出しを実行します。

しかし、ラッパー関数はプロパティの読み書き操作などは転送しません。ラップした後、`name` や `length` などの元の関数のプロパティへのアクセスは失われます。:
=======
// after this wrapping, calls to sayHi will be delayed for 3 seconds
sayHi = delay(sayHi, 3000);

sayHi("John"); // Hello, John! (after 3 seconds)
```

As we've seen already, that mostly works. The wrapper function `(*)` performs the call after the timeout.

But a wrapper function does not forward property read/write operations or anything else. After the wrapping, the access is lost to properties of the original functions, such as `name`, `length` and others:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js run
function delay(f, ms) {
  return function() {
    setTimeout(() => f.apply(this, arguments), ms);
  };
}

function sayHi(user) {
  alert(`Hello, ${user}!`);
}

*!*
<<<<<<< HEAD
alert(sayHi.length); // 1 (function.length は宣言された関数の引数の数を返します)
=======
alert(sayHi.length); // 1 (function length is the arguments count in its declaration)
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927
*/!*

sayHi = delay(sayHi, 3000);

*!*
<<<<<<< HEAD
alert(sayHi.length); // 0 (ラッパー後は引数は 0 です)
*/!*
```

`Proxy` はすべてをターゲットオブジェクトに転送するので、はるかに強力です。

関数ラッピングの代わりに `Proxy` を使って見ましょう:
=======
alert(sayHi.length); // 0 (in the wrapper declaration, there are zero arguments)
*/!*
```

`Proxy` is much more powerful, as it forwards everything to the target object.

Let's use `Proxy` instead of a wrapping function:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js run
function delay(f, ms) {
  return new Proxy(f, {
    apply(target, thisArg, args) {
      setTimeout(() => target.apply(thisArg, args), ms);
    }
  });
}

function sayHi(user) {
  alert(`Hello, ${user}!`);
}

sayHi = delay(sayHi, 3000);

*!*
<<<<<<< HEAD
alert(sayHi.length); // 1 (*) プロキシは length 操作をターゲットに転送します
*/!*

sayHi("John"); // Hello, John! (3秒後)
```

結果は同じですが、呼び出しだけでなく、プロキシ上のすべての操作は元の関数に転送されます。そのため、行 `(*)` で `sayHi.length` はラッピング後も正しい値を返します。

これで "よりリッチな" ラッパーを手に入れました。

他にもトラップはあります: 完全なリストはこのチャプーの最初にのせています。それらの使用パターンは上記と同じです。
=======
alert(sayHi.length); // 1 (*) proxy forwards "get length" operation to the target
*/!*

sayHi("John"); // Hello, John! (after 3 seconds)
```

The result is the same, but now not only calls, but all operations on the proxy are forwarded to the original function. So `sayHi.length` is returned correctly after the wrapping in the line `(*)`.

We've got a "richer" wrapper.

There exist other traps: the full list is in the beginning of this chapter. Their usage pattern is similar to the above.
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

## Reflect

`Reflect` is a built-in object that simplifies creation of `Proxy`.

It was said previously that internal methods, such as `[[Get]]`, `[[Set]]` and others are specifiction only, they can't be called directly.

`Reflect` object makes that somewhat possible. Its methods are minimal wrappers around the internal methods.

Here are examples of operations and `Reflect` calls that do the same:

| Operation |  `Reflect` call | Internal method |
|-----------------|----------------|-------------|
| `obj[prop]` | `Reflect.get(obj, prop)` | `[[Get]]` |
| `obj[prop] = value` | `Reflect.set(obj, prop, value)` | `[[Set]]` |
| `delete obj[prop]` | `Reflect.deleteProperty(obj, prop)` | `[[HasProperty]]` |
| `new F(value)` | `Reflect.construct(F, value)` | `[[Construct]]` |
| ... | ... | ... |

For example:

```js run
let user = {};

Reflect.set(user, 'name', 'John');

alert(user.name); // John
```

In particular, `Reflect` allows to call operators (`new`, `delete`...) as functions (`Reflect.construct`, `Reflect.deleteProperty`, ...). That's an interesting capability, but here another thing is important.

**For every internal method, trappable by `Proxy`, there's a corresponding method in `Reflect`, with the same name and arguments as `Proxy` trap.**

So we can use `Reflect` to forward an operation to the original object.

In this example both traps `get` and `set` transparently (as if they didn't exist) forward reading/writing operations to the object, showing a message:

```js run
let user = {
  name: "John",
};

user = new Proxy(user, {
  get(target, prop, receiver) {
    alert(`GET ${prop}`);
*!*
    return Reflect.get(target, prop, receiver); // (1)
*/!*
  },
  set(target, prop, val, receiver) {
    alert(`SET ${prop}=${val}`);
*!*
    return Reflect.set(target, prop, val, receiver); // (2)
*/!*
  }
});

let name = user.name; // shows "GET name"
user.name = "Pete"; // shows "SET name=Pete"
```

Here:

- `Reflect.get` reads an object property.
- `Reflect.set` writes an object property and returns `true` if successful, `false` otherwise.

That is, everything's simple: if a trap wants to forward the call to the object, it's enough to call `Reflect.<method>` with the same arguments.

In most cases we can do the same without `Reflect`, for instance, reading a property `Reflect.get(target, prop, receiver)` can be replaced by `target[prop]`. There are important nuances though.

### Proxying a getter

Let's see an example that demonstrates why `Reflect.get` is better. And we'll also see why `get/set` have the fourth argument `receiver`, that we didn't use before.

We have an object `user` with `_name` property and a getter for it.

Here's a proxy around it:

```js run
let user = {
  _name: "Guest",
  get name() {
    return this._name;
  }
};

*!*
let userProxy = new Proxy(user, {
  get(target, prop, receiver) {
    return target[prop];
  }
});
*/!*

alert(userProxy.name); // Guest
```

The `get` trap is "transparent" here, it returns the original property, and doesn't do anything else. That's enough for our example.

Everything seems to be all right. But let's make the example a little bit more complex.

After inheriting another object `admin` from `user`, we can observe the incorrect behavior:

```js run
let user = {
  _name: "Guest",
  get name() {
    return this._name;
  }
};

let userProxy = new Proxy(user, {
  get(target, prop, receiver) {
    return target[prop]; // (*) target = user
  }
});

*!*
let admin = {
  __proto__: userProxy,
  _name: "Admin"
};

// Expected: Admin
alert(admin.name); // outputs: Guest (?!?)
*/!*
```

Reading `admin.name` should return `"Admin"`, not `"Guest"`!

What's the matter? Maybe we did something wrong with the inheritance?

But if we remove the proxy, then everything will work as expected.

The problem is actually in the proxy, in the line `(*)`.

1. When we read `admin.name`, as `admin` object doesn't have such own property, the search goes to its prototype.
2. The prototype is `userProxy`.
3. When reading `name` property from the proxy, its `get` trap triggers and returns it from the original object as `target[prop]` in the line `(*)`.

    A call to `target[prop]`, when `prop` is a getter, runs its code in the context `this=target`. So the result is `this._name` from the original object `target`, that is: from `user`.

To fix such situations, we need `receiver`, the third argument of `get` trap. It keeps the correct `this` to be passed to a getter. In our case that's `admin`.

How to pass the context for a getter? For a regular function we could use `call/apply`, but that's a getter, it's not "called", just accessed.

`Reflect.get` can do that. Everything will work right if we use it.

Here's the corrected variant:

```js run
let user = {
  _name: "Guest",
  get name() {
    return this._name;
  }
};

let userProxy = new Proxy(user, {
  get(target, prop, receiver) { // receiver = admin
*!*
    return Reflect.get(target, prop, receiver); // (*)
*/!*
  }
});


let admin = {
  __proto__: userProxy,
  _name: "Admin"
};

*!*
alert(admin.name); // Admin
*/!*
```

Now `receiver` that keeps a reference to the correct `this` (that is `admin`), is passed to the getter using `Reflect.get` in the line `(*)`.

We can rewrite the trap even shorter:

```js
get(target, prop, receiver) {
  return Reflect.get(*!*...arguments*/!*);
}
```


`Reflect` calls are named exactly the same way as traps and accept the same arguments. They were specifically designed this way.

So, `return Reflect...` provides a safe no-brainer to forward the operation and make sure we don't forget anything related to that.

## Proxy limitations

Proxies provide a unique way to alter or tweak the behavior of the existing objects at the lowest level. Still, it's not perfect. There are limitations.

### Built-in objects: Internal slots

Many built-in objects, for example `Map`, `Set`, `Date`, `Promise` and others make use of so-called "internal slots".

These are like properties, but reserved for internal, specification-only purposes. For instance, `Map` stores items in the internal slot `[[MapData]]`. Built-in methods access them directly, not via `[[Get]]/[[Set]]` internal methods. So `Proxy` can't intercept that.

Why care? They are internal anyway!

Well, here's the issue. After such built-in object gets proxied, the proxy doesn't have these internal slots, so built-in methods will fail.

For example:

```js run
let map = new Map();

let proxy = new Proxy(map, {});

*!*
proxy.set('test', 1); // Error
*/!*
```

Internally, a `Map` stores all data in its `[[MapData]]` internal slot. The proxy doesn't have such slot. The [built-in method `Map.prototype.set`](https://tc39.es/ecma262/#sec-map.prototype.set) method tries to access the internal property `this.[[MapData]]`, but because `this=proxy`, can't find it in `proxy` and just fails.

Fortunately, there's a way to fix it:

```js run
let map = new Map();

let proxy = new Proxy(map, {
  get(target, prop, receiver) {
    let value = Reflect.get(...arguments);
*!*
    return typeof value == 'function' ? value.bind(target) : value;
*/!*
  }
});

proxy.set('test', 1);
alert(proxy.get('test')); // 1 (works!)
```

Now it works fine, because `get` trap binds function properties, such as `map.set`, to the target object (`map`) itself.

Unlike the previous example, the value of `this` inside `proxy.set(...)` will be not `proxy`, but the original `map`. So when the internal implementation of `set` tries to access `this.[[MapData]]` internal slot, it succeeds.

```smart header="`Array` has no internal slots"
A notable exception: built-in `Array` doesn't use internal slots. That's for historical reasons, as it appeared so long ago.

So there's no such problem when proxying an array.
```

### Private fields

The similar thing happens with private class fields.

For example, `getName()` method accesses the private `#name` property and breaks after proxying:

```js run
class User {
  #name = "Guest";

  getName() {
    return this.#name;
  }
}

let user = new User();

user = new Proxy(user, {});

*!*
alert(user.getName()); // Error
*/!*
```

The reason is that private fields are implemented using internal slots. JavaScript does not use `[[Get]]/[[Set]]` when accessing them.

In the call `getName()` the value of `this` is the proxied `user`, and it doesn't have the slot with private fields.

Once again, the solution with binding the method makes it work:

```js run
class User {
  #name = "Guest";

  getName() {
    return this.#name;
  }
}

let user = new User();

user = new Proxy(user, {
  get(target, prop, receiver) {
    let value = Reflect.get(...arguments);
    return typeof value == 'function' ? value.bind(target) : value;
  }
});

alert(user.getName()); // Guest
```

That said, the solution has drawbacks, explained previously: it exposes the original object to the method, potentially allowing it to be passed further and breaking other proxied functionality.

### Proxy != target

Proxy and the original object are different objects. That's natural, right?

So if we use the original object as a key, and then proxy it, then the proxy can't be found:

```js run
let allUsers = new Set();

class User {
  constructor(name) {
    this.name = name;
    allUsers.add(this);
  }
}

let user = new User("John");

alert(allUsers.has(user)); // true

user = new Proxy(user, {});

*!*
alert(allUsers.has(user)); // false
*/!*
```

As we can see, after proxying we can't find `user` in the set `allUsers`, because the proxy is a different object.

```warn header="Proxies can't intercept a strict equality test `===`"
Proxies can intercept many operators, such as `new` (with `construct`), `in` (with `has`), `delete` (with `deleteProperty`) and so on.

But there's no way to intercept a strict equality test for objects. An object is strictly equal to itself only, and no other value.

So all operations and built-in classes that compare objects for equality will differentiate between the object and the proxy. No transparent replacement here.
```

## Revocable proxies

A *revocable* proxy is a proxy that can be disabled.

Let's say we have a resource, and would like to close access to it any moment.

What we can do is to wrap it into a revocable proxy, without any traps. Such proxy will forward operations to object, and we can disable it at any moment.

The syntax is:

```js
let {proxy, revoke} = Proxy.revocable(target, handler)
```

The call returns an object with the `proxy` and `revoke` function to disable it.

Here's an example:

```js run
let object = {
  data: "Valuable data"
};

let {proxy, revoke} = Proxy.revocable(object, {});

// pass the proxy somewhere instead of object...
alert(proxy.data); // Valuable data

// later in our code
revoke();

// the proxy isn't working any more (revoked)
alert(proxy.data); // Error
```

A call to `revoke()` removes all internal references to the target object from the proxy, so they are no more connected. The target object can be garbage-collected after that.

We can also store `revoke` in a `WeakMap`, to be able to easily find it by a proxy object:

```js run
*!*
let revokes = new WeakMap();
*/!*

let object = {
  data: "Valuable data"
};

let {proxy, revoke} = Proxy.revocable(object, {});

revokes.set(proxy, revoke);

// ..later in our code..
revoke = revokes.get(proxy);
revoke();

alert(proxy.data); // Error (revoked)
```

The benefit of such approach is that we don't have to carry `revoke` around. We can get it from the map by `proxy` when needeed.

Using `WeakMap` instead of `Map` here, because it should not block garbage collection. If a proxy object becomes "unreachable" (e.g. no variable references it any more), `WeakMap` allows it to be wiped from memory together with its `revoke` that we won't need any more.

## References

- Specification: [Proxy](https://tc39.es/ecma262/#sec-proxy-object-internal-methods-and-internal-slots).
- MDN: [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy).

## Summary

`Proxy` is a wrapper around an object, that forwards operations on it to the object, optionally trapping some of them.

It can wrap any kind of object, including classes and functions.

The syntax is:

```js
let proxy = new Proxy(target, {
  /* traps */
});
```

...Then we should use `proxy` everywhere instead of `target`. A proxy doesn't have its own properties or methods. It traps an operation if the trap is provided, otherwise forwards it to `target` object.

We can trap:
- Reading (`get`), writing (`set`), deleting (`deleteProperty`) a property (even a non-existing one).
- Calling a function (`apply` trap).
- The `new` operator (`construct` trap).
- Many other operations (the full list is at the beginning of the article and in the [docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)).

That allows us to create "virtual" properties and methods, implement default values, observable objects, function decorators and so much more.

We can also wrap an object multiple times in different proxies, decorating it with various aspects of functionality.

The [Reflect](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect) API is designed to complement [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy). For any `Proxy` trap, there's a `Reflect` call with same arguments. We should use those to forward calls to target objects.

Proxies have some limitations:

- Built-in objects have "internal slots", access to those can't be proxied. See the workaround above.
- The same holds true for private class fields, as they are internally implemented using slots. So proxied method calls must have the target object as `this` to access them.
- Object equality tests `===` can't be intercepted.
- Performance: benchmarks depend on an engine, but generally accessing a property using a simplest proxy takes a few times longer. In practice that only matters for some "bottleneck" objects though.