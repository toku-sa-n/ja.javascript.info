<<<<<<< HEAD
`eval` を使用して数学式を計算してみましょう。
=======
Let's use `eval` to calculate the maths expression:
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927

```js demo run
let expr = prompt("Type an arithmetic expression?", '2*3+2');

alert( eval(expr) );
```

<<<<<<< HEAD
ユーザは任意のテキストあるいはコードが入力できます。

これを安全にし算術のみに制限するために、[regular expression](info:regular-expressions) を使用して `expr` をチェックし、数字と演算子のみを含むようにすることができます。
=======
The user can input any text or code though.

To make things safe, and limit it to arithmetics only, we can check the `expr` using a [regular expression](info:regular-expressions), so that it only may contain digits and operators.
>>>>>>> c4d1987ebc470b30c234dbde6fac6e77b7509927