---
title: "他のフレームワークとの比較"
---

## three.js

<https://threejs.org/>

WebGL 世界で現在最も人気のある OSS は [mrdoob](https://github.com/mrdoob) さんが主に開発されている [three.js](https://github.com/mrdoob/three.js) です。最初のバージョンは [2010 年 7 月 3 日](https://github.com/mrdoob/three.js/releases/tag/r1)に公開されています。 examples ディレクトリ以下に様々なサンプルが実装されていて、それらを利用することで幅広い 3D インタラクティブコンテンツを開発することが可能です。開発は執筆現在でも非常に活発です。

three.js の特徴は、 **「コア機能と拡張機能の分離」と「破壊的なバージョニング」「豊富なエコシステム」** にあると思います。

### コア機能と拡張機能の分離

three.js は、 src ディレクトリ内にある **コア機能** と、 examples ディレクトリ内にある **拡張機能** が明確に分離されています。どちらも同じリポジトリで MIT License で公開されていますが、例えば glTF 2.0 アセットのローダーはコア機能ではなく拡張機能(examples)側に実装されています。

これは three.js のコア機能をシンプルに保つ戦略でしょう。 WebGL や WebGPU の API をラップし使いやすくしたコア機能と、そのコア機能を活用して拡張する機能を分離するのは面白い戦略です。

ただ、この戦略により少し難しい問題があります。その一つが ES Modules による tree-shaking のしづらさです。

現在の JavaScript では、ランタイムで不要なコードを振り落とし、必要なコードだけを 1 ファイルにまとめて(bundleして)配布するスタイルがよくあるケースです。

例えば `import * as THREE from "three";` と記述してしまうと、 `"three"` パッケージのどのファイルが本当に利用されるかわからないため、全てのファイルをまとめる必要があります。

ES Modules と tree-shaking は比較的最近導入されたシステムで、 three.js が生まれた後に普及したものです。そのため、これまでのコードも動作しつつ、 ES Modules 対応を進める必要がありました。

実際のプロジェクトでは、コア機能でも使わないファイルがあったり、拡張機能で必要なファイルがあったりすると思います。それを適切に `import` することが現在の three.js では少し煩雑になっています。 [こちらの issue](https://github.com/mrdoob/three.js/issues/19986) をベースに、 tree-shaking 出来るクラス化するプロジェクトが動いていて、現在は ES Modules 対応がなされたみたいです。

Babylon.js は、コア機能と拡張機能を別のリポジトリで管理していて、必要に応じて `import` することができ、また ES Modules にもフル対応しています。 three.js よりも分かりやすい戦略になっていると思います。

### 破壊的なバージョニング

three.js は [Semantic Versioning](https://semver.org/lang/ja/) を採用していません。 [Releases](https://github.com/mrdoob/three.js/releases) ページを見ると、毎バージョンごとに様々な API が変わっているのが見てとれると思います。そして毎バージョンごとに[マイグレーションガイド](https://github.com/mrdoob/three.js/wiki/Migration-Guide)が更新され、バージョンアップ時はこのマイグレーションガイドとにらめっこしながら慎重にアプリケーションを更新する必要があります。

Babylon.js は Semantic Versioning をサポートし、年1回程度のメジャーバージョンアップ以外で破壊的な API の変更は行わないことが **約束されています** 。そのため、 1 年間は安心してバージョンアップの恩恵(新規 API の追加や高速化)を受けることが可能です。個人的にはここが非常に大きい違いと考えています。商用プロダクトを長期的にメンテナンスする場合、ライブラリのバージョンアップは非常にコストがかかる作業です。

### 豊富なエコシステム

three.js は利用者が非常に多いため、様々な依存プロダクトが作成されています。後述する A-Frame も three.js をベースとして作られています。

サンプルや拡張機能も多く、自前で検索したり実装したりする量を減らすことが可能です。 Babylon.js は採用事例が少ないため、自前で色々調べたり実装したりする必要があることに注意が必要です。

### その他

three.js にも Unity/Unreal Engine のような「エディタ」は成熟していません。[一応あります](https://threejs.org/editor/)が、自分の作りたいものをこのエディタで作れるかというと少し機能不足に感じます。

## A-Frame

<https://aframe.io/>

A-Frame は WebXR 機能を中心に、 HTML を書くような感覚で 3D シーンを記述することが出来るフレームワークです。 three.js がベースのレンダリングエンジンとなっています。

WebXR は、ブラウザと HMD(ヘッドマウントディスプレイ) を使って AR/MR/VR 体験が出来る機能です。

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Entity-Component - Registry</title>
    <meta name="description" content="Entity-Component - Registry">
    <script src="https://aframe.io/releases/0.5.0/aframe.min.js"></script>
    <script src="https://unpkg.com/aframe-animation-component@3.2.1/dist/aframe-animation-component.min.js"></script>
    <script src="https://unpkg.com/aframe-particle-system-component@1.0.x/dist/aframe-particle-system-component.min.js"></script>
    <script src="https://unpkg.com/aframe-extras.ocean@%5E3.5.x/dist/aframe-extras.ocean.min.js"></script>
    <script src="https://unpkg.com/aframe-gradient-sky@1.0.4/dist/gradientsky.min.js"></script>
    <script src="https://unpkg.com/aframe-extras.tube@%5E3.5.x/dist/aframe-extras.tube.min.js"></script>
  </head>
  <body>
    <a-scene>
      <a-entity id="rain" particle-system="preset: rain; color: #24CAFF; particleCount: 5000"></a-entity>

      <a-entity id="sphere" geometry="primitive: sphere"
                material="color: #EFEFEF; shader: flat"
                position="0 0.15 -5"
                light="type: point; intensity: 5"
                animation="property: position; easing: easeInOutQuad; dir: alternate; dur: 1000; to: 0 -0.10 -5; loop: true"></a-entity>

      <a-entity id="ocean" ocean="density: 20; width: 50; depth: 50; speed: 4"
                material="color: #9CE3F9; opacity: 0.75; metalness: 0; roughness: 1"
                rotation="-90 0 0"></a-entity>

      <a-entity id="sky" geometry="primitive: sphere; radius: 5000"
                material="shader: gradient; topColor: 235 235 245; bottomColor: 185 185 210"
                scale="-1 1 1"></a-entity>

      <a-entity id="light" light="type: ambient; color: #888"></a-entity>
    </a-scene>
  </body>
</html>
```

これは Glitch にある[サンプルプロジェクト](https://glitch.com/@dyl4n-foley/a-frame/play/aframe-registry)のソースコードです。 JavaScript は 1 行も書かれていません。 HTML にコンポーネントとプロパティを記述することで、 3D シーンを簡単に表現することが出来ます。

![A-Frame](/images/books/entering-babylonjs/comparison/a-frame.png)

このコードだけで、水面に浮かぶ球体を表現しています。このように、 A-Frame は非常にシンプルなコードで 3D シーンをレンダリングし、それを HMD で見れる便利なフレームワークとなっています。

Babylon.js でも WebXR のサポートは手厚く、 API も非常に洗練されています。[ドキュメント](https://doc.babylonjs.com/features/featuresDeepDive/webXR/introToWebXR) にも記載がありますが、 `await scene.createDefaultXRExperienceAsync()` という関数を一つ呼ぶだけで、作成した 3D シーンを HMD で確認することが可能です。最近発売された Apple Vision Pro でも動作することが確認されています([Limes さんの記事](https://www.crossroad-tech.com/entry/apple-vision-pro-Babylonjs-WebXR-grab-teleport))。

## 8th Wall

<https://www.8thwall.com/>

こちらも WebXR を前提にした、 Niantic 社のサービス＆エンジンです。プロダクションレディな XR(特に AR)体験を簡単なコードで実現可能です。どのようなアプリケーションが作られているかは、公式サイトにたくさんの事例が載っていますのでそちらをご確認ください。 8th Wall では、エンジンだけでなくアプリケーションページの publishing サービスなどたくさんのサービスが展開されています。

最近 [Cloud Editor](https://www.8thwall.com/products/cloud-editor) を搭載し、より簡単にプロジェクトを作成できるようになったようです。

AR 分野の第一人者である Niantic 社製のプロダクトということで、商用プロダクトとしての採用事例が多いのが特徴です。

Babylon.js は API こそ優れているものの、 8th Wall のような様々なサポート体制が整っているわけではないので、今すぐプロダクションレディで高品質な AR 技術を採用したい場合は 8th Wall を検討するのが良いかもしれません。

## PlayCanvas

<https://playcanvas.com/>

PlayCanvas は、 Web ベースのゲームエンジンです。ブラウザだけで高品質な 3D ゲームを作成することが出来ます。エディタと publishing 機能をサービスとして提供しているため、簡単に導入することが可能です。

PlayCanvas はオープンソースの WebGL ランタイムエンジンと、商用の Web エディタで構成されています。ブラウザ上で Unity / Unreal Engine のような GUI を使ってプロダクトを作成することが出来ます。

![Play Canvas](/images/books/entering-babylonjs/comparison/playcanvas.png)

恐らく Unity や Unreal Engine を使ったことのあるユーザーが最もとっつきやすい環境でしょう。ただ、オープンソースでない場合は有料なので注意が必要です。

Babylon.js はエディタは弱いですが、全てオープンソースで無料で利用出来るのがメリットです。
