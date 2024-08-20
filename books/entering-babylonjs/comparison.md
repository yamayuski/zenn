---
title: "他のフレームワークとの比較"
---

## three.js

<https://threejs.org/>

WebGL 世界で現在最も人気のある OSS は [mrdoob](https://github.com/mrdoob) さんが主に開発されている [three.js](https://github.com/mrdoob/three.js) です。最初のバージョンは [2010 年 7 月 3 日](https://github.com/mrdoob/three.js/releases/tag/r1)に公開されています。 examples ディレクトリ以下に様々なサンプルが実装されていて、それらを利用することで幅広い 3D インタラクティブコンテンツを開発することが可能です。開発は執筆現在でも非常に活発です。

three.js の特徴は、「コア機能と拡張機能の分離」と「破壊的なバージョニング」「豊富なエコシステム」にあると思います。

### コア機能と拡張機能の分離

three.js は、 src ディレクトリ内にあるコア機能と、 examples ディレクトリ内にある拡張機能が明確に分離されています。どちらも同じリポジトリで MIT License で公開されていますが、例えば glTF 2.0 アセットのローダーはコア機能ではなく拡張機能(examples)側に実装されています。

これは three.js のコア機能をシンプルに保つ戦略でしょう。 WebGL や WebGPU の API をラップし使いやすくしたコア機能と、そのコア機能を活用して拡張する機能を分離するのは面白い戦略です。

ただ、この戦略により少し難しい問題があります。その一つが ES Modules による tree-shaking のしづらさです。

現在の JavaScript では、ランタイムで不要なコードを振り落とし、必要なコードだけを 1 ファイルにまとめて(bundleして)配布するスタイルがよくあるケースです。

例えば `import * as THREE from "three";` と記述してしまうと、 `"three"` パッケージのどのファイルが本当に利用されるかわからないため、全てのファイルをまとめる必要があります。

ES Modules と tree-shaking は比較的最近導入されたシステムで、 three.js が生まれた後に普及したものです。そのため、これまでのコードも動作しつつ、 ES Modules 対応を進める必要がありました。

実際のプロジェクトでは、コア機能でも使わないファイルがあったり、拡張機能で必要なファイルがあったりすると思います。それを適切に `import` することが現在の three.js では少し煩雑になっています。 [こちらの issue](https://github.com/mrdoob/three.js/issues/19986) をベースに、 tree-shaking 出来るクラス化するプロジェクトが動いていて、現在は ES Modules 対応がなされているみたいです。

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

## 8th-wall

## PlayCanvas
