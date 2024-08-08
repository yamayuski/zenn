---
title: "esm.sh を使って超モダンなフロントエンドを体験しよう"
emoji: "☁"
type: "tech"
topics: ["javascript","typescript","esmsh","frontend","npm"]
published: true
---

こんにちは。やまゆです。

## トランスパイルとバンドル

皆さんは Web フロントエンドを構築する際、何を使っていますか？ **node.js? webpack? Vite?** 他にもたくさん候補はありますが、何かしらを使っているのではないでしょうか？

何故ブラウザで動作する Web フロントエンドに、 Linux や Windows ネイティブで動作する node.js が必要なのでしょうか。それは、 **「これまでの Web ブラウザ単体では、フロントエンドを作る機能が不足していたから」** です。

現在一般化されている Web フロントエンドは、 React や Vue など様々なフレームワークを用いて、 TypeScript や jsx, Single File Component などの記法を使って書かれています。しかし、 Web ブラウザは TypeScript を直接理解することは出来ませんし、 jsx 記法を理解することも出来ません。 Web ブラウザが理解出来るコードに **トランスパイル(コンパイル)** する必要があります。

そのトランスパイルを行うために、 Web ブラウザ以外が必要になりますね。それが今は **node.js** が担っている役割です。

また、 HTTP/1.1 通信は、同時に通信出来る数が制限されていたり、通信の度にコネクションを張ったりするので非常に非効率です。そのため、 **「バンドル」** と呼ばれる作業を事前に行うことが非常に多いです。

これは、たくさんある JavaScript や CSS ファイルを、必要に応じて 1 ファイルにまとめあげ、 **通信の回数を減らす** 役割を担っています。通信回数が減れば、その分ブラウザに表示するまでの時間が短縮出来るという話です。

さらに歴史をさかのぼると、画像ファイルも 1 ファイルにまとめて、 CSS 等で画像の一部を切り取って表示する、などをしていることもありました。懐かしいですね。

## ES モジュール

さて、 2024 年現在、 HTTP/1.1 の通信はかなり減りました。 [Cloudflare のブログ記事](https://blog.cloudflare.com/http3-usage-one-year-on-ja-jp) にある通り、 Cloudflare という CDN で観測された通信は、昨年の段階で **約 60% が HTTP/2** を利用し、 **約 30 % が HTTP/3** を利用しています。先ほど HTTP/1.1 は通信出来る数が制限されていると言いましたが、これを緩和したのが HTTP/2 、そしてそれでも緩和しきれなかった要因をさらに排除し現在最高の効率で通信出来るようになったのが HTTP/3 です。今回このプロトコルの変遷について詳しくは述べませんが、ブログ記事を見るととても勉強になると思います。

つまり、現在は「通信の回数を減らす」ことはブラウザとプロトコルの進化により **最早不要** になっているわけです。実は webpack はなくても良いというわけですね。極論。

もちろん、それは極論すぎていて、完全に不要になったというわけではありません。大きなフレームワークを利用する際に、そのフレームワークの全てのコードを利用する可能性は少ないでしょう。 jQuery の全ての関数を使っているプロジェクトがいくらあるでしょうか？不要なコードはダウンロードする必要がないので、 webpack が tree-shaking と呼ばれる機能を使って、不要なコードを削除してくれています。ありがとう webpack 。

ここで tree-shaking という機能を使うためには、下記のコードでは問題があります(例として [Babylon.js](https://babylonjs.com) という WebGL フレームワークを使っています)。

```html:index.html
<script src="https://cdn.babylonjs.com/babylon.js"></script>
<script>
  const engine = new BABYLON.Engine(document.getElementById("canvas"));
  const scene = new BABYLON.Scene(engine);
  engine.runRenderLoop(() => scene.render());
</script>
```

```js:index.js
import * as BABYLON from "@babylonjs/core";

const engine = new BABYLON.Engine(document.getElementById("canvas"));
const scene = new BABYLON.Scene(engine);
engine.runRenderLoop(() => scene.render());
```

このように **「別ファイルとしてダウンロードしてグローバル変数に展開」** したり、 **「全てのモジュールを import 」** してしまうと、 webpack などのツールが **「どのファイルが実際に使われていて、どのファイルが使われていないのか」** を判断することが出来なくなってしまいます。 Babylon.js は執筆現在のバージョンだと **1.6 MB** もあるので、それを全てダウンロードしてパースし解釈した後でないと処理を続けられません。

tree-shaking を有効にするには、下記のようにする必要があります。

```js:index.js
import { Engine } from "@babylonjs/core/Engines/engine";
import { Scene } from "@babylonjs/core/scene";

const engine = new Engine(document.getElementById("canvas"));
const scene = new Scene(engine);
engine.runRenderLoop(() => scene.render());
```

このように、どのファイルから何を import するか明記することで、 tree-shaking が有効になります。もちろん、ここでいう `Engine` や `Scene` オブジェクトが要求する依存関係も全て解決してくれます。

このような書き方は **ES モジュール** と一般的に呼ばれます。全てのパッケージがこの形式に対応しているわけではないので注意です(例えば jQuery は[バージョン 4.0 メジャーアップグレードの際に ES モジュール対応をリストアップしています](https://github.com/jquery/jquery/issues/5365))。

## esm.sh とは？

前置きが長くなってしまいましたが、この **ES モジュール** を最大限活用して、バンドル作業を行わずにブラウザだけで依存関係を解決し動作する環境を整えられるのが [esm.sh](https://esm.sh/) という CDN（コンテンツデリバリネットワーク） サービスです。

> Create modern(es2015+) web apps easily with NPM packages in browser/deno.
> No build tools needed!

と書かれています。ちなみにこのサービスはバックエンドに Cloudflare が利用されているみたいです。

この ES モジュール機能は、 [IE を除くモダンなブラウザで既に利用可能](https://caniuse.com/es6-module)です。 [MDN の JavaScript モジュール](https://developer.mozilla.org/ja/docs/Web/JavaScript/Guide/Modules) のページも参考になるのでご覧ください。

このサービスを利用すると、例えば webpack や Vite(=esbuild+Rollup) を利用せず、 tree-shaking の恩恵を受けることが出来ます。

```html:index.html
<script type="module">
import { Engine } from "https://esm.sh/@babylonjs/core/Engines/engine";
import { Scene } from "https://esm.sh/@babylonjs/core/scene";

const engine = new Engine(document.getElementById("canvas"));
const scene = new Scene(engine);
engine.runRenderLoop(() => scene.render());
</script>
```

`script` タグに `type="module"` を使うのが必須のポイントとなります。これで ES モジュールモードで動作します。

そして from に esm.sh の URL を記載することで、自動的にブラウザが esm.sh から必要なパッケージをダウンロードしてきます。

下記の画像は esm.sh を使わない例です。

![old](/images/articles/entering-esmsh/entering-esmsh-old.png)

下記の画像は esm.sh を使った例です。

![esm.sh](/images/articles/entering-esmsh/entering-esmsh-esmodule.png)

ここでレスポンス速度は CDN が違うので比較することが出来ません。注目して欲しいのはその **サイズ** です。

esm.sh を使わない場合は **1.6 MB** の `babylon.js` ファイルがダウンロードされています。 esm.sh を使った場合は 101 kB の `scene.js` と 43.9 kB の `engine.js` がダウンロードされているのが確認できると思います。つまり **約 150 kB** ですね。大きくファイルサイズが小さくなったと思います。 tree-shaking が機能しているのがわかりますね。

この画像は、 **ローカルに保存した html ファイルをブラウザで読み込んだだけ** です。 webpack も使っていませんし、 node.js も不要です。

esm.sh を使った方は、初回の読み込みこそトランスパイルが入るので遅くなりますが、 2 回目以降は非常に高速にファイルがダウンロードされ描画されます。

これは、 CDN サービスの方で npm から ES Modules の読み込みを行い、 tree-shaking をした上でレスポンスしている形となっています。

```html
<link rel="stylesheet" href="https://esm.sh/monaco-editor?css">
```

JavaScript だけでなく、 `CSS files in JS` も対応しているみたいです。

## Deno で利用する

node.js とは別の JavaScript/TypeScript 動作環境として、 [Deno](https://deno.com/) というものが開発されています。 npm や esm.sh と同じような ES module のホスティングサービスとして [jsr](https://jsr.io/) というものもあり、 Deno ではこちらからサードパーティモジュールを読み込むことがドキュメントでオススメされていますが、 esm.sh を利用することも可能です。

```ts
import { useContext } from "https://esm.sh/react";
```

このように書けば、 TypeScript の型定義も一緒に読み込んでくれます。これは `X-TypeScript-Types` ヘッダーがあることで、 LSP が型情報を認識出来る仕組みのようです。詳しくは[こちら](https://docs.deno.com/runtime/manual/advanced/typescript/types/#using-x-typescript-types-header)。

逆に、 node.js で esm.sh をそのまま使うことは今の所出来ませんが、 `--experimental-network-imports` というフラグを使って実験的にインポートすることは可能なようです。

## ランタイムで TypeScript をビルドする

esm.sh はモジュールの提供機能だけでなく、 JavaScript や TypeScript のコードをビルドする機能も実験的に提供されています。

- npm や GitHub のパッケージを import する
- TypeScript や jsx 記法のサポート
- 複数のモジュールを 1 ファイルにバンドル

する機能があります。

```js
import build from "https://esm.sh/build";

const ret = await build({
  dependencies: {
    "preact": "^10.13.2",
    "preact-render-to-string": "^6.0.2",
  },
  code: `
    /* @jsx h */
    import { h } from "preact";
    import { renderToString } from "preact-render-to-string";
    export function render(): string {
      return renderToString(<h1>Hello world!</h1>);
    }
  `,
  // for types checking and LSP completion
  types: `
    export function render(): string;
  `,
});

// import module
const { render } = await import(ret.url);
// import bundled module
const { render } = await import(ret.bundleUrl);

render(); // "<h1>Hello world!</h1>"
```

これは公式のサンプルをそのまま持ってきましたが、 `esm.sh/build` モジュールを使うことで簡単にビルドが可能なようです。実際に試してみたところ、ちょっと時間はかかりますが想定通りビルドしてくれました。これは便利。

## まとめ

ということで、 webpack などのバンドルツールや tsc などのトランスパイルツールを用いずに、 **ブラウザだけで TypeScript やサードパーティモジュールを利用する方法** として esm.sh を紹介しました。今後この手法が主流になるかどうかは分かりませんが、新しい Web フロントエンドの可能性として覚えておくと良いかなと思います。
