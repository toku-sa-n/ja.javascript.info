
<<<<<<< HEAD

`async` 呼び出しを Promise として扱い、それに `.then` をつけるだけです。

=======
That's the case when knowing how it works inside is helpful.

Just treat `async` call as promise and attach `.then` to it:
>>>>>>> 9e3fa1351f80cfd6353a778a55b2c86bca9e895f
```js run
async function wait() {
  await new Promise(resolve => setTimeout(resolve, 1000));

  return 10;
}

function f() {
<<<<<<< HEAD
  // 1秒後に 10を表示
=======
  // shows 10 after 1 second
>>>>>>> 9e3fa1351f80cfd6353a778a55b2c86bca9e895f
*!*
  wait().then(result => alert(result));
*/!*
}

f();
```
