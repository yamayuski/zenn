---
title: "Babylon.js にはエディタがない！？"
---

2024 年現在、 Babylon.js には、 Unity や Unreal Engine のような **「エディタ」** と呼ばれる存在はありません。

正確には、[あるにはある](https://github.com/BabylonJS/Editor)のですが、ほとんどメンテナンスされていない状態です。

![Empty Project](/images/books/entering-babylonjs/editor/empty_project.png)

こちらの画像は[リポジトリに存在するもの](https://github.com/BabylonJS/Editor/blob/master/website/img/home/empty_project.png)で、 Unity/Unreal Engine like な GUI が確認できます。 [Electron](https://www.electronjs.org/ja/) 製です。

しかし、こちらは Babylon.js の最新バージョンで動作しない状態となっています。[公式ドキュメント](https://doc.babylonjs.com/communityExtensions/editor) や [Limes さんの記事](https://www.crossroad-tech.com/entry/babylonjs-editor-4beta2-review) の紹介が存在しますが、実際にこのエディタを利用して作成されたプロダクトなどはほとんど観測されていないのが現状です。

そもそも Babylon.js は **「ゲームエンジンではなく、 3D レンダリングフレームワーク」** です。ゲームエンジンに搭載されるべき「アセット管理」や「データオーサリング」のようなシステムは同梱されていません。

## じゃあどうやって作る？

現状は **「全て TypeScript(JavaScript) でアセットやロジックを管理する」** ことになります。 3D モデルやテクスチャなどのアセットは Blender など別のツールで用意し、それを TypeScript でインポートして、アニメーションや材質表現を TypeScript で記述して実行する、という形になります。

幸い TypeScript は [Vite](https://vitejs.dev/) などのツールを利用することで、 **「ファイルを保存したら即座にシーンをリロードする」** 仕組みがあります。ディスプレイの左側にソースコードを置き、右側にブラウザを置いて、 Playground のような環境ですぐにロジックを検証することが可能です。

ゲーム開発において、確認->修正->再確認のイテレーション速度は非常に重要です。エディタはそのイテレーション速度を最大化させるために様々な機能を搭載することが多いです。

Babylon.js のエコシステムでは、現状そのイテレーション速度は Unity/Unreal Engine に勝てません。しかし、だからといってゲームが作れないわけではありません。

## Vite で高速なイテレーションを得る

[create-babylon-app](https://www.npmjs.com/package/create-babylon-app) パッケージを使うことで、 node.js さえインストールしていれば簡単にローカル開発環境を作成することが可能です。

```sh
$ npm create babylon-app --name your-babylon-app --template simple-ts --install
```

これだけで `./your-babylon-app` ディレクトリ以下に開発環境が整います。後は `cd your-babylon-app && npm run dev` で開発サーバーが立ち上がります。

[GitHub Pages](https://vitejs.dev/guide/static-deploy.html#github-pages) を使うことで、 GitHub に `git push` するだけで自動的にプロジェクトを公開することも可能です。

## Babylon.js によるゲーム開発の今後

エディタは **公式が提供しなければならないという規則はありません** 。著者は Babylon.js のための **エディタやアセットマーケットを 1 から作成するプロジェクト** を少しずつ進めています。オープンソースでなくても構いません。欲しい人が作るのが最も効率的だと筆者は考えています。 Babylon.js を使っていて、「こういう機能があれば」「こういうシステムだったら」という願望が出たら、それを用意するにはどうすればいいか、ということを考えてみると新しい道が見えるかもしれません。

また、 Babylon.js 自体は WebGPU 対応を強く進めていて、今後も活発に進化していくことが期待できます。執筆現在では日本語のドキュメントは少ないですが、コア API は理解しやすいものとなっていますので、日本語資料をどんどん増やしていって日本人が Babylon.js をより使いやすいエコシステムにしていきたいと考えています。
