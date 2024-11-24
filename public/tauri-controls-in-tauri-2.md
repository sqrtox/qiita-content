---
title: Tauri 2.0でtauri-controlsを使うときに気をつけること
tags:
  - Rust
  - Tauri
  - Tauri2.0
private: false
updated_at: '2024-09-15T23:58:29+09:00'
id: 0623034fdd75e3174706
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに

Tauriには`tauri-controls`というライブラリがあります。これはフレームレスウィンドウでOS固有のコントロールボタンを再現[^window-controls-overlay]してくれるもので、アプリのタイトルバーをカスタマイズしたいときに非常に便利なライブラリです。

[^window-controls-overlay]: いわゆる https://github.com/WICG/window-controls-overlay のやつです。

しかし、Tauri 2.0で使用するときにいくつかハマるポイントがあったので概要と対策方法を書きます。今後のバージョンアップで必要なくなるかもしれません。

## `` error[E0432]: unresolved import `tauri::Icon` ``系

Rustのコンパイルエラーです。これは`tauri-controls`のドキュメントでインストールすると書いてある`tauri-plugin-window`クレートが原因です。

https://github.com/agmmnn/tauri-controls/issues/30#issuecomment-2339206922

### 対処法

```shell
cargo rm tauri-plugin-window
```
```diff_rust
tauri::Builder::default()
    .plugin(tauri_plugin_os::init())
-   .plugin(tauri_plugin_window::init())
    ...
```

依存関係とBuilderのプラグインから削除します。

ドキュメントではインストールすると書いてありますが、Tauri側のアップデートに伴って不要になりました。[^tauri-plugin-window-archived]

[^tauri-plugin-window-archived]: GitHubのリポジトリも[アーカイブされています](https://github.com/tauri-apps/tauri-plugin-window)。

## `Command os_type not found`

WebView側に表示されるエラーです。`tauri-controls`は自動でプラットフォームを検出しますが、このエラーが出る状態では検出できなくなってしまいます。

https://github.com/agmmnn/tauri-controls/issues/30#issuecomment-2268731763

これは`tauri-controls`が依存している`@tauri-apps/plugin-os`とRust側のプラグインである`tauri-plugin-os`クレートのバージョンが合っていない（互換性がない）ことが原因です。ドキュメントにしたがって`cargo add tauri-plugin-os`すると最新バージョンが入ってくるのでこのエラーに遭遇しやすいと思います。

### 対処法

```shell
cargo add tauri-plugin-os@=2.0.0-beta.6
```

Rust側のプラグインのバージョンを`2.0.0-beta.6`に固定してインストールします。`=`がないとすでに他にバージョンがインストールされている場合に置き換わらない可能性があるので注意。

## コントロールが反応しない / `abc not allowed. Permissions associated with this command: abc`[^abc]

[^abc]: この項目に出てくる`abc`という部分は適宜読み替えてください。

これは`src-tauri/capabilities/abc.json`で権限が許可されていない状態でAPIを呼び出したときにWebView側で表示されます。コントロール（ウィンドウのボタン）を押しても反応しないという場合もおそらくこれが原因です。

### 対処法

`src-tauri/capabilities/abc.json`の`permissions`に以下の権限を設定する。

```json
    "core:window:allow-start-dragging",
    "core:window:allow-toggle-maximize",
    "core:window:allow-internal-toggle-maximize",
    "core:window:allow-minimize",
    "core:window:allow-is-maximized",
    "core:window:allow-set-fullscreen",
    "core:window:allow-is-fullscreen",
    "core:window:allow-close",
    "core:event:allow-listen",
    "os:allow-os-type",
```

<details><summary>おまけ：各権限の用途</summary>

コードをすべて読んだわけではないので、確実ではないですが各権限の用途のまとめです。
```jsonc
    // タイトルバーをドラッグしたとき
    "core:window:allow-start-dragging",

    // 最大化/もとに戻すボタンが押されたとき
    "core:window:allow-toggle-maximize",
    
    // タイトルバーをダブルクリックで最大化/もとに戻す用（Windows限定？）
    "core:window:allow-internal-toggle-maximize",
    
    // 最小化ボタンが押されたとき
    "core:window:allow-minimize",
    
    // 最大化されたかどうかの検出用（アイコンが変わる）
    "core:window:allow-is-maximized",
    
    // macosで拡大ボタンが押されたとき
    "core:window:allow-set-fullscreen",
    "core:window:allow-is-fullscreen",
    
    // 閉じるボタンが押されたとき
    "core:window:allow-close",
    
    // リサイズ検知用
    "core:event:allow-listen",
    
    // platformが指定されなかった場合の自動検出用
    "os:allow-os-type",
```
</details>

## さいごに

Tauri 2.0はまだrcバージョンであり、API自体は安定しているものの、`tauri-controls`のようなサードパーティのライブラリ側の対応が追いついていない現状があります。それでも今使いたいという方の助けになれば幸いです。

わからない点や解決しなかった場合は、コメントしていただければ、同じライブラリ利用者として解決できるようにお答えします。
