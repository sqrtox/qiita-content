---
title: AviUtlプラグインのサンプルをビルドする
tags:
  - AviUtl
  - プラグイン
  - C++
private: false
updated_at: ''
id: f5127a162cae8639387d
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに

先日AviUtlのプラグインを作ろうとして、C++ド素人だったためにAviUtl Plugin SDKのサンプルのビルドにすら苦労したので備忘録的に手順を書き記しておきます。

この記事では、連番BMP画像での出力オプションを追加する出力プラグイン `bmp_output` のサンプルをビルドします。

## 準備

### プラグインSDKの入手

今回は練習としてサンプルをビルドしたいので、まず[AviUtlのお部屋](https://spring-fragrance.mints.ne.jp/aviutl/)のダウンロードから `aviutl_plugin_sdk.zip` を入手します。

![AviUtlのお部屋ダウンロードセクション](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/663039/139ec986-84ab-442b-d517-a23f151a8608.png)

ダウンロードが終わったら適当なフォルダに解凍しておきます。

### Visual Studio

ビルドはVisual Studio 2022を使用します。
世の中には多くのC++コンパイラがありますが、C++初心者の自分がコマンドラインからコンパイルしたりMakeなどをセットアップするのは大変なので、今回はIDEに丸投げします。
なお、Visual Studioのインストール方法はこの記事では解説しません。

## プロジェクトの作成

次に空のC++プロジェクトを作成します。
まずVisual Studioを開き、新しいプロジェクトの作成をクリックします。

![VisualStudio起動画面](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/663039/f9350b24-3edf-096d-b0c0-80d09a495cc5.png)

空のプロジェクトを選択して「次へ」をクリックします。

![VisualStudioテンプレート選択画面](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/663039/53a0544c-b912-ae2f-e754-9a70eacd247f.png)

最後にプロジェクトの名前と位置を決めます。

この辺はデフォルトで大丈夫です。
自分は好みで「ソリューションとプロジェクトを同じディレクトリに配置する」にチェックを入れています。

設定が終わったら「作成」をクリックします。

![VisualStudio作成画面](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/663039/cd7720ff-1ed5-24d3-aa87-1f5620289e2a.png)

まっさらなプロジェクトが開きます。
次に、ソースファイルやヘッダファイルなどをプロジェクトに追加していきます。

ソリューションエクスプローラーを表示して、プロジェクト名を右クリックし、メニューの「エクスプローラーでフォルダーを開く」項目をクリックします。

![ソリューションエクスプローラー内のプロジェクト名](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/663039/a5a41f8f-48eb-d2fa-60da-fbdbe3086816.png)

プロジェクトのフォルダが開くので、ここに解凍した `aviutl_plugin_sdk` から `.auf` 以外の `bmp_output` から始まるファイルと `output.h` をコピーまたは移動します。

![ソースファイルのコピー](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/663039/7e380aac-29c7-b2a8-be61-b4269c65c0ec.png)

コピーが終わったらVisual Studioへ戻り、ソリューションエクスプローラーから以下のようにファイルを設定します。
<font color="#999">不安：リソースファイルカテゴリって何を入れればいいんですかね。`.rc` もここでいいのかな…</font>

![ソースファイルの構成](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/663039/3f82ae45-1c35-a931-3efd-b84a8c8d75fc.png)

これでプロジェクトの作成は完了です。

## ビルド

次にビルドのためにプロジェクトを設定していきます。
`bmp_output.cpp` をIDEで開いてみると文字列がエラーを起こしているのでまずはこれを黙らせます。

![文字コードエラー](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/663039/f6685b7f-c1cb-5794-38f0-167e89c8ab36.png)

ソリューションエクスプローラーからプロジェクト名を右クリックして、メニューから「プロパティ」をクリックします。
「構成プロパティ」→「詳細」から `文字セット` を `マルチ バイト文字セットを使用する` に設定します。
次に「構成プロパティ」→「C/C++」→「言語」から `準拠モード` を `既定` に設定します。

![文字コード設定](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/663039/c2986abb-3717-1b5f-7359-9adf9bf073b5.png)

エラーが消えました。

![文字コードエラー解消](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/663039/6b2265f8-10be-3698-17bc-29c918225c01.png)

次にコンパイラやリンカーの設定をしていきます。
まず、先ほど設定したときと同じようにプロジェクトのプロパティを開きます。

「構成プロパティ」→「全般」から `構成の種類` を `ダイナミック ライブラリ (.dll)` に設定します。
次に同カテゴリ内「詳細」から `ターゲット ファイルの拡張子` を `.auo` に設定します。

「構成プロパティ」→「リンカー」→「入力」に移動して `モジュール定義ファイル` に `bmp_output.def`（`.def` ファイルのパス）を設定します。

![コンパイラリンカー設定](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/663039/fe1a0c0a-ae8f-bf30-1dd1-5d248063acf8.png)

最後にIDE上部リボンの構成を `Release` と `x86` に設定します。

![アーキテクチャ設定](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/663039/2f1a8e05-7331-ebfd-fc8d-067d00ce5dc8.png)

これでビルドのための設定は終わりです。
ソリューションエクスプローラーからプロジェクト名を右クリックしてメニューからビルドすると `<プロジェクトのフォルダ>/Release/<プロジェクト名>.auo` が生成されています。

これでサンプルをビルドできました。

## 動作確認

前のセクションでサンプルをビルドするという目的自体は果たされましたが、正しくビルドできているかを確認するために動作確認は大事です。
適当な `aviutl.exe` と同じ階層に `plugins` フォルダを作成してそこに生成した `.auo` ファイルを入れてAviUtlを起動します。

生成された `.auo` (DLL)が悪ければ、この時点でAviUtlがクラッシュしたりします。
ほとんどメモリ読み取り違反が原因なので、プラグインからエクスポートされる構造体が変ではないか（語彙力）確認してみてください。
サンプルから何もいじってなければこれは起きないはずですが…。

起動できたら、ツールバー →「その他」→「出力プラグイン情報」をクリックしてください。
ここで生成したプラグインが表示されていればもう正常に動いたようなものです。

![出力プラグイン情報](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/663039/a895cc9b-041d-ef67-282a-8e857540c181.png)

もし、表示されていなければ異なったアーキテクチャでビルドしていることが原因の可能性があります。
AviUtlのプラグインはx86である必要があります。（要検証）

実際に、出力できるかも確認するために適当な動画を作成します。
本当に動画の内容はなんでもいいのですが、自分は加減速移動を使用してボールが跳ねるアニメーションを作ってみました。

![AviUtl編集画面](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/663039/29904af0-d78a-85c6-806f-4946bcaed7bf.png)

ツールバー →「ファイル」→「プラグイン出力」→「連番BMP出力」をクリックします。
ファイル名に名前を入れて、適当なフォルダを選択し「保存」をクリックします。

![出力された連番BMPファイル](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/663039/76ddd84b-b350-82ce-f8a3-f5caba3752e8.png)

出力が始まり、フォルダに連番BMPファイルが出力されていることが確認できます。
無事ビルドしたサンプルの動作を確認できました。

## おわり

長文でしたが、最後まで読んでいただきありがとうございました。
ここまで長い記事や画像を使用した記事の執筆ははじめてで、大変読みにくくなってしまいました。

次回はRustでプラグイン作成に挑戦しようと思っています。
