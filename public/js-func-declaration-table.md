---
title: 表で見るJavaScriptの4つの関数定義
tags:
  - JavaScript
private: false
updated_at: '2024-11-25T06:04:21+09:00'
id: ed4508eed390fde08583
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに

JavaScriptにいくつかある関数の定義方法を機能の有無を基準に表でまとめてみました。

## 表

### 機能

|                    | 式リテラル | 文宣言  | 関数呼び出し | new呼び出し | generator 宣言 | async 宣言 | argumentsの使用 | thisの束縛 |
| ------------------ | ---------- | ------- | ------------ | ----------- | -------------- | ---------- | --------------- | ---------- |
| function name() {} | yes        | yes[^1] | yes          | yes         | yes            | yes        | yes             | yes        |
| class Name() {}    | yes        | yes[^1] | no           | yes         | no             | no         | yes             | no[^2]     |
| method() {}        | no         | yes     | yes          | no          | yes            | yes        | yes             | yes        |
| () => {}           | yes        | no      | yes          | no          | no             | yes        | no              | no         |
### 宣言

|                    | 再宣言  | 宣言への再代入 | 宣言の巻き上げ | 宣言のスコープ |
| ------------------ | ------- | -------------- | -------------- | -------------- |
| function name() {} | yes     | yes            | yes            | 関数           |
| class Name() {}    | no      | yes            | no             | ブロック       |
| method() {}        | yes[^3] | /              | /              | /              |
| () => {}           | /       | /              | /              | /              |

### 補足

- 引数の宣言部分の構文は投稿者が知る限りではどれも同じ
- Functionなど関数コンストラクタによる定義はコンストラクタを使わずに書いた場合と変わらないはずなので未記載
- 対応する表現がない場合は `/`
- 宣言への再代入というのは、見かけることはないですがこういうやつ
  というかあっては駄目ですこんなコード

  ```js
    class SomethingClass {}
    
    SomethingClass = 0;
    
    console.log(SomethingClass); // 0
    
    function somethingFunction() {}
    
    somethingFunction = 1;
    
    console.log(somethingFunction); // 1
    ```

## 感想

こうして見るとfunctionの何でも感はすごいですね。一方でアロー関数はかなり純粋な関数定義のようです。あと個人的にargumentsはfunctionだけのものかと思っていたのでアロー関数以外の全てで使えるのは意外でした。また、仕様を読んだわけではないですが、functionはvar、classはlet相当で宣言されていそうです。

[^1]: 文による宣言は名前が必須です。ただし例外として `export default function () {}` や `export default class {}` のような無名宣言もあるようです。これらは式ではなく文として解釈されます
[^2]: bind/call/applyなどでthisの束縛はできませんが、thisを参照すると自分自身のインスタンスになっています
[^3]: class宣言内での再宣言です
