importance: 5

---

# 関数の軍隊

次のコードは `shooters` の配列を作ります。

すべての関数は、その番号を出力するためのものです。しかし、どこか間違っています...

```js run
function makeArmy() {
  let shooters = [];

  let i = 0;
  while (i < 10) {
    let shooter = function() { // 射手(shooter) 関数
      alert( i ); // その番号を表示するべき
    };
    shooters.push(shooter);
    i++;
  }

  return shooters;
}

let army = makeArmy();

army[0](); // 射手 番号 0 表示 10
army[5](); // また 番号 5 ですが表示は 10...
// ... すべての射手は 0, 1, 2, 3... の代わりに 10 が表示されます
```

<<<<<<< HEAD:1-js/06-advanced-functions/03-closure/8-make-army/task.md
なぜすべての射手(shooters)は同じものが表示されるのでしょう？期待通りに動作するようコードを直してください。
=======
Why do all of the shooters show the same value? Fix the code so that they work as intended.

>>>>>>> 28ed5a3f7df9e015cf81c126423c76c9408d7117:1-js/06-advanced-functions/03-closure/10-make-army/task.md