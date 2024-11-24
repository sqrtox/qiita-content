---
title: JavaScriptで文字列のformat()
tags:
  - JavaScript
  - TypeScript
private: false
updated_at: '2024-06-23T16:45:34+09:00'
id: a8121c44e1db0831e163
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに

こういう風に文字列をテンプレート化して後から値を埋め込みたいことがあると思います。

```js
const greetTemplate = "Hello, {name}!";

format(greetTemplate, { name: "world" }); // -> "Hello, world!"
```

何番煎じかわかりませんが、JavaScriptでこれをする方法を思い付いたので記事として残しておきます。

## コード

TypeScriptで書かれていますが、型情報を取り除けばJavaScriptでも使えます。

```ts
const format = (
  template: string,
  placeholders: Record<string, string> = {},
): string =>
  template.replaceAll(/([{}])\1|\{([^{}]+)\}/g, (_, braces, name) =>
    braces ?? placeholders[name] ?? "",
  );
```

### 使い方

基本的な使い方は`{フィールド名}`を含む文字列を第1引数に渡して、対応するフィールド名を持つオブジェクトを第2引数に渡すだけです。

```js
format("Hello, {name}!", { name: "world" }); // -> "Hello, world!"
```

また、`{{name}}`という風に書くと波括弧をそのまま出力することができます。

```js
format("Hello, {{name}}!", { name: "world" }); // -> "Hello, {name}!"
```

何番煎じかわからないと最初に書きましたが、波括弧のエスケープ処理までできる記事は少ないかもしれません。

### 解説

基本的にreplaceを使用して正規表現で置換していくだけです。
まず`([{}])\1|\{([^{}]+)\}`という正規表現は`([{}])\1`と`\{([^{}]+)\}`の二つを組み合わせたものであり、それぞれ`{{`や`}}`のような波括弧が連続する場合と`{name}`の場合にマッチします。
前者にマッチした場合は`(_, braces, name) =>`のbracesにマッチした波括弧が入り、後者にマッチした場合はnameのほうにマッチしたフィールド名が入ります。
つまり`braces ?? placeholders[name] ?? ""`はマッチした波括弧がある場合はそのまま波括弧を返し、フィールド名にマッチした場合はplaceholdersから対応フィールドの値を取り出して返し、対応するフィールドがない場合などで取り出せなかった場合は空文字に置換するコードです。
