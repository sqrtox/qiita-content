---
title: JavaScriptでPythonのrange コンストラクタを実装する
tags:
  - Python
  - JavaScript
  - Range
private: false
updated_at: '2021-10-10T14:16:12+09:00'
id: efae08b33259bb631ed9
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに

これらと同じことをJavaScriptでやる。

```py
for i in range(10):
    print(i)
```

```py
# [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
list(range(10))

# [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
list(range(1, 11))

# [0, 5, 10, 15, 20, 25]
list(range(0, 30, 5))

# [0, 3, 6, 9]
list(range(0, 10, 3))

# [0, -1, -2, -3, -4, -5, -6, -7, -8, -9]
list(range(0, -10, -1))

# []
list(range(0))

# []
list(range(1, 0))
```


# 仕様

 - すべて数値型の range(end) と range(start, end[, step]) の引数を受け取る。
 - 渡された引数がない場合はエラーを投げる。
 - step引数に0が渡された場合はエラーを投げる。
 - 最初に載せたPythonのコードと同じ挙動をする。


# 実装

範囲などは事前に計算し、Symbol.iterator メソッドを実装したイテラブルなオブジェクトを返す。

```js
/**
 * @param {number} start
 * @param {number} end
 * @param {number} [step = 1]
 * @returns {{ start: number, end: number, step: number, size: number, [Symbol.iterator](): Generator<number, void, unknown> }}
 */
const range = (start, end, step = 1) => {
  // range()
  if (typeof start === 'undefined' && typeof end === 'undefined') {
    throw new TypeError('range expected at least 1 argument, got 0')
  }

  // range(start, end, 0)
  if (step === 0) {
    throw new RangeError('range() arg 3 must not be zero')
  }

  // overload range(end)
  if (typeof end === 'undefined') {
    end = start
    start = 0
  }

  /**
   * @type {number}
   */
  const size = Math.max(0, Math.ceil((end - start) / step))

  return {
    start,
    end,
    step,
    size,
    *[Symbol.iterator]() {
      for (let i = 0; i < size; i++) {
        yield start + step * i
      }
    }
  }
}
```


# 使い方

以下のように使用する。

```js
for (const i of range(10)) {
  console.log(i)
}
```

```js
// [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
const array = [...range(10)] // Array.from(range(10))
```
