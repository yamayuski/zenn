---
title: "Playground ですぐ試す"
---

## Playground で遊んでみる

<https://playground.babylonjs.com/>

[こちら](https://playground.babylonjs.com/)にアクセスしてみてください。左側にソースコード、右側に球体と地面が表示されたと思います。これは Playground と呼ばれる環境で、非常に簡単に Babylon.js を体験してみることが出来ます。 Playground なので **「遊び場」** ですね。

![Overview](/images/books/entering-babylonjs/playground/overview.png)

Playground では、 1 ファイルのソースコードを使ってリアルタイムに Babylon.js を使ったレンダリングを体験することが出来ます。まず最初は、この Playground を使ってみるのが一番手っ取り早いです。

ここで書いたコードはクラウド上に保存することができ、公式、非公式含め大量の Playground が登録されています。

![Screen Space Reflections](/images/books/entering-babylonjs/playground/ssr.png)

外部にモデルファイルやテクスチャを置いて、それを読み込むことも可能です。例えば、[水たまりの反射が綺麗な「スクリーンスペースリフレクション」機能のデモ Playground](https://playground.babylonjs.com/#PIZ1GK#1116) です。このような写実的な表現を数十行のコードで実現可能なのが、 Babylon.js の特徴です(事前にモデルの用意が必要ではありますが)。

また、簡単なテクスチャは API として提供されており、自分でファイルを公開しなくても利用することが可能です。

[こちら](https://playground.babylonjs.com/#8WLDZY#6)は私が作成した Playground で、 `Assets.textures` 以下に存在するテクスチャを読み込み、一覧で表示するシーンとなっています。

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

![Console](/images/books/entering-babylonjs/playground/console.png)

コンソールに注意書きのようなものがいくつか出てくることがありますが、赤いエラー文以外は無視してもらって構いません。

### メニューバー

![Menu bar](/images/books/entering-babylonjs/playground/menubar.png)

左から順に説明していきます。

- `7.19.1` は現在動作している Babylon.js のバージョンです。基本的に最新リリース版が利用されます。
- JS / TypeScript と書いてある部分は、 `JS` を押すことで JavaScript 版に変更することが出来ます(この画像では、 JavaScript 版になっています)。 JavaScript 版を使っている場合は `TS` を押すことで TypeScript 版に変更することが出来ます。  **これを書き換えるとコードが初期化されるので注意！**
- ➤ 左のコードを右のシーンを初期化して反映するボタンです。 Alt+Enter がショートカットとなっています。最もよく使うボタンだと思います。
- 💾 現在のコードをクラウドに保存するボタンです。 Ctrl+S がショートカットとなっています。現在の状態を保存して公開したい場合や、一旦バックアップする場合に使います。
- ⚙ Inspector という 3D シーンの状態をデバッグするためのツールが起動します。下記で解説します。
- ↓ コードをローカルに保存します。 Shift+Ctrl+S がショートカットとなっています。 HTML/JS がダウンロードされるので、開くことですぐシーンを再現できます。
- 📜 現在のコードを破棄して新しいシーンを作成します。 **保存していないコードは削除されるので注意！**
- <> テンプレートコードを生成してコードに挿入します。よく使われる項目がリストアップされています。
- 🚮 現在のコードを完全に削除します。あまり使うことはないかな...?
- ⚙ エディタの状態を設定します。エディタを非表示にしたり、コードフォーマットを行ったりできます。
- `WEBGL2` 利用するエンジンのバージョンを決めます。 WEBGL2 がスタンダードで、 WEBGPU は実験的実装となっています。
- `7.19.1` を押すと古いメジャーバージョンを使うことが出来ます。
- 📚 は公式の Examples の一覧が出ます。非常にたくさんの事例がありますので、これを見ていくだけでもどんなことが出来るかわかるので非常に面白いです。

### Inspector(インスペクタ)

![Inspector](/images/books/entering-babylonjs/playground/inspector.png)

インスペクタは Babylon.js の 3D シーンの詳細情報を閲覧したり、編集することが出来るツールです。

シーンのツリーと、クリックしたアイテムのパラメータが閲覧できます。 Unity や Unreal Engine のエディタのようなものですね。

ただ、インスペクタで編集した項目は **シーンには反映されますが、左側のコードには反映されない** ので注意が必要です。

ここで編集出来る項目が全てではなく、コードからしか修正出来ないものもありますので、あくまで参考程度にとどめておくのがよいでしょう。

## 実際にいじってみる

デフォルトで用意されているコードから、例えば下記の部分をコメントアウト(行の最初に `//` を入力)してみてください。

```js
    // Our built-in 'sphere' shape.
    var sphere = BABYLON.MeshBuilder.CreateSphere("sphere", {diameter: 2, segments: 32}, scene);

    // Move the sphere upward 1/2 its height
    sphere.position.y = 1;
```

```js
    // Our built-in 'sphere' shape.
    // var sphere = BABYLON.MeshBuilder.CreateSphere("sphere", {diameter: 2, segments: 32}, scene);

    // Move the sphere upward 1/2 its height
    // sphere.position.y = 1;
```

そして Alt+Enter を押して反映すると、右側のシーンから球体が消えると思います。この 2 行が球体を表示するコードということですね。

Babylon.js の API は `BABYLON` という変数にほとんど格納されています。これは古い記述形式なのですが、 Playground では現在も使われている手法です。

例えば最初に書かれている `var scene = new BABYLON.Scene(engine);` は、このシーン全体を初期化するコードです。 `engine` という変数はグローバルに定義されています。

この `Scene` には最低でも `Camera` と `Light` が必要です。 `Camera` がなければどこからどうやって 3D モデルを表示すれば良いかわかりませんし、 `Light` がなければ 3D モデルは真っ黒になってしまいます。

デフォルトでは `FreeCamera` と `HemisphericLight` が定義されていますね。カメラやライトの詳細については別の章で紹介します。

ということで、この Playground では、下記のことを行ってから自分のコードを加えていく、という形になります。

基本的には、[公式のドキュメント](https://doc.babylonjs.com/features/featuresDeepDive)でどのような機能があるかを見ながら書いていくことになると思います。

1. `Scene` を初期化する
1. `Camera` を初期化する
1. `Light` を初期化する
1. 自分の書きたいコードを書く
1. `Alt+Enter` で反映する
1. 良い所まで作れたら `Ctrl+S` で保存する(専用の URL を生成する)

![Save](/images/books/entering-babylonjs/playground/save.png)

保存する時にこのようなダイアログが出ます。内容は適当で構いませんが、適切な内容にしておくと Playground の検索にヒットしやすくなります。

OK を押すと、 URL が発行されます。

![New URL](/images/books/entering-babylonjs/playground/newurl.png)

この URL をブックマークにしておくことをお勧めします。

**※ここからコードを編集して、再度保存すると URL が変更になります(バージョンが上がった URL が生成されます)。ブックマークの変更をお忘れなく！**

では、次の章から具体的な Babylon.js の API について紹介していきます。
