---
title: "Playground で超簡単に試す"
---

## Playground で遊んでみる

https://playground.babylonjs.com/

こちらにアクセスしてみてください。左側にソースコード、右側に球体と地面が表示されたと思います。これは Playground と呼ばれる環境で、非常に簡単に Babylon.js を体験してみることが出来ます。 Playground なので **「遊び場」** ですね。

![](/images/books/entering-babylonjs/playground.png)

Playground では、 1 ファイルのソースコードを使ってリアルタイムに Babylon.js を使ったレンダリングを体験することが出来ます。まず最初は、この Playground を使ってみるのが一番手っ取り早いです。

ここで書いたコードはクラウド上に保存することができ、公式、非公式含め大量の Playground が登録されています。

https://playground.babylonjs.com/#PIZ1GK#1116

外部にモデルファイルやテクスチャを置いて、それを読み込むことも可能です。上記は水たまりの反射が綺麗な「スクリーンスペースリフレクション」機能のデモ Playground です。このような写実的な表現を数十行のコードで実現可能なのが、 Babylon.js の特徴です(事前にモデルの用意が必要ではありますが)。

また、簡単なテクスチャは API として提供されており、自分でファイルを公開しなくても利用することが可能です。

https://playground.babylonjs.com/#8WLDZY#6

こちらは私が作成した Playground で、 `Assets.textures` 以下に存在するテクスチャを読み込み、一覧で表示するシーンとなっています。

クリックすることで画像の URL を取得出来るので、それを `new BABYLON.Texture("https://assets.babylonjs.com/textures/amiga.jpg", scene);` のようにして読み込むことが可能です。

書いたコードは上部メニューバーのセーブボタンで保存することができ、専用の URL が生成されます。バージョン管理もされるので、他人の書いたコードを間違って上書きしてしまうこともありません。

`https://playground.babylonjs.com/#8WLDZY#6`

`#8WLDZY` が各 Playground のユニークな ID で、 `#6` は 6 回目に保存した内容のことを指します。試しに `#6` の部分を `#1` にして読み込んでみると、少し違うシーンが読み込まれるのではないでしょうか。

公式のサンプル Playground は右上の `Examples` からアクセスできるので、色々見てみると面白いでしょう。

## Playground の使い方

Playground は、基本的に左側でコードを書いて、再生ボタンを押すことで右側に反映されます。毎回シーンはリセットされるので注意が必要です。

コードは JavaScript でも TypeScript でも、どちらでも選ぶことが可能です。上のメニューバーのタブから選択できます。

JavaScript の場合は `createScene` 関数が、 TypeScript の場合は `Playground` クラスの `CreateScene` メソッドがエントリポイントになります。この名前を変えると動かなくなってしまうので注意しましょう。

エラーなどはブラウザの開発者ツール(F12)のコンソールタブで確認することが可能です。コンソールは常に開いておくと良いと思います。
