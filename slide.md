---
lang: ja
title: Vivliostyle.jsにおけるWeb標準、CSSサポートの大改善
author: Shinyu Murakami
---

# Vivliostyle.jsにおけるWeb標準、CSSサポートの大改善 {.cover}

2022-11-20 \
Shinyu Murakami \
Vivliostyle Foundation

# 目次 {.toc role="doc-toc"}

1.  [Web標準CSSサポートの大改善の取り組み](#web標準cssサポートの大改善の取り組み)
    1.  [CSS組版ならWeb標準CSSは使えて当然](#css組版ならweb標準cssは使えて当然)
    1.  [Vivliostyle.jsはブラウザのCSS機能を拡張](#vivliostylejsはブラウザのcss機能を拡張)
    1.  [最新のCSS機能が使えなかった問題を解消](#最新のcss機能が使えなかった問題を解消)
    1.  [ブラウザで使えるCSSプロパティと値は基本的にすべて利用可能に (v2.17~)](#ブラウザで使えるcssプロパティと値は基本的にすべて利用可能に-v217)
    1.  [CSS変数をサポート (v2.17~)](#css変数をサポート-v217)
    1.  [:has()、:is()、:not()、:where() 擬似クラスをフルサポート (v2.19~)](#hasisnotwhere-擬似クラスをフルサポート-v219)
    1.  [まだ残っているCSS標準サポートの課題](#まだ残っているcss標準サポートの課題)
1.  [組版に役に立つCSS機能の追加実装](#組版に役に立つcss機能の追加実装)
    1.  [これまで実装してきたCSS組版機能](#これまで実装してきたcss組版機能)
    1.  [まだできてないCSS組版機能と改善課題](#まだできてないcss組版機能と改善課題)
1.  [Vivliostyleとそれ以外のCSS組版ツールの比較](#vivliostyleとそれ以外のcss組版ツールの比較)

# Web標準CSSサポートの大改善の取り組み

## CSS組版ならWeb標準CSSは使えて当然

- CSS組版のメリット
  - Webの標準のCSSの知識があれば、自分でスタイルシートを作れること。
- 標準のCSSを学ぶための材料はたくさんある。
  - [MDNサイトのCSS入門](https://developer.mozilla.org/ja/docs/Web/CSS)などお薦め。

## Vivliostyle.jsはブラウザのCSS機能を拡張

- Vivliostyle.jsは、標準のWebブラウザ上で動く。
- CSSでのレイアウトの機能の多くはブラウザでサポートされているCSSの機能を利用。
  - それだけでは足りないCSSの組版機能を追加実装
  - この方式だから基本的に<br>
    Vivliostyle.jsのCSS機能 ⊃ ブラウザのCSS機能<br>
    （Vivliostyleのほうがスーパーセット）
    - しかし、これまで必ずしもそうなってなかった…

## 最新のCSS機能が使えなかった問題を解消

- Vivliostyle.jsでは、CSSの機能を拡張するため、CSSの構文解析やカスケーディングの処理、CSSプロパティやその値の指定が正しいかどうかを判定する処理などを自前でやってる。
  - そのベースになってるのは[Peter Sorotokin氏によるEPUB Adaptive Layout実装](https://github.com/sorotokin/adaptive-layout/)のCSS処理。
    - Vivliostyle.jsではそのしくみを引き継いで、CSS組版に必要なCSSの機能を拡充させてきた。
    - しかし、最新のWeb標準のCSS機能をすぐに反映させられず、ギャップが生じてた。➡︎ その解消の取り組みへ

## ブラウザで使えるCSSプロパティと値は基本的にすべて利用可能に (v2.17~)

- CSSの処理を見直して、ブラウザで使えるCSSプロパティと値は基本的にすべて使えるように改良。
  - Vivliostyle.jsの中で定義されていないCSSプロパティや値でも、ブラウザでサポートされているなら有効にした。（以前は無効にしてた）
  - 今後は、ブラウザでサポートされているCSSプロパティが機能しなかったらそれはバグなので[バグ報告ください！](https://github.com/vivliostyle/vivliostyle.js/issues)

### これでCSSグリッドレイアウトが使える

[CSS Grid Layout](https://developer.mozilla.org/ja/docs/Learn/CSS/CSS_layout/Grids) の例：

```css
.wrapper {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-auto-rows: 50px;
} …
```

<style>
.wrapper {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-auto-rows: 50px;
  border: 2px solid #f76707;
  border-radius: 5px;
  background-color: #fff4e6;
  line-height: 1.5;
  font-size: 18px;
}
.wrapper > div {
  border: 2px solid #ffa94d;
  border-radius: 5px;
  background-color: #ffd8a8;
  padding: 0.5em;
  color: maroon;
}
.box1 {
  grid-column: 1 / 4;
}
.box2 {
  grid-row: 2 / 4;
}  
</style>
<div class="wrapper">
  <div class="box1">.box1 { grid-column: 1 / 4; }</div>
  <div class="box2">.box2 { grid-row: 2 / 4; }</div>
  <div class="box3">.box3</div>
  <div class="box4">.box4</div>
  <div class="box5">.box5</div>
</div>

## CSS変数をサポート (v2.17~)

- [CSS変数（カスタムプロパティ）](https://developer.mozilla.org/ja/docs/Web/CSS/Using_CSS_custom_properties)は、スタイルシートをカスタマイズしやすくするのにとても便利。
- 任意のスタイルルールの中で使用できて、また、ページを定義する `@page {...}` の中でも使用可能。

```css
:root {
  --page-width: 148mm;
  --page-height: 210mm;
}
@page {
  size: var(--page-width) var(--page-height);
  …
}
```

## :has()、:is()、:not()、:where() 擬似クラスをフルサポート (v2.19~)

- とくに[:has() 擬似クラス](https://developer.mozilla.org/ja/docs/Web/CSS/:has)は画期的に便利。
  - 子要素や後につづく要素が何であるかによる選択が可能に。
  
`:has()` 擬似クラスの使用例：

```css
section:has(section) {
  /* 内側にもsectionが存在するsectionのスタイル */
}
h1:has(+ p) {
  /* 段落pがあとに続く見出しh1のスタイル */
}
```

## まだ残っているCSS標準サポートの課題

最新のブラウザで使えるのにVivliostyleでサポートできてないCSSの機能はまだあります。以下のものなど：

- [`::marker`　箇条書きのマークを指定する擬似要素](https://developer.mozilla.org/ja/docs/Web/CSS/::marker)　([#723](https://github.com/vivliostyle/vivliostyle.js/issues/732))
- [`@counter-style`　カウンタースタイルを定義](https://developer.mozilla.org/ja/docs/Web/CSS/@counter-style)　([#731](https://github.com/vivliostyle/vivliostyle.js/issues/731))
- [`@layer`　カスケードレイヤー](https://developer.mozilla.org/ja/docs/Web/CSS/@layer)　([#977](https://github.com/vivliostyle/vivliostyle.js/issues/977))
- [`@container`　コンテナクエリー](https://developer.mozilla.org/en-US/docs/Web/CSS/@container)　([#1046](https://github.com/vivliostyle/vivliostyle.js/issues/1046))

### もうすぐ標準になる最新CSS仕様対応も

- [CSS Nesting](https://drafts.csswg.org/css-nesting/)　([#1032](https://github.com/vivliostyle/vivliostyle.js/issues/1032))
  - CSSルールをネストできる（SCSSと似てる）
- [lh and rlh units](https://drafts.csswg.org/css-values-4/#lh)　([#1035](https://github.com/vivliostyle/vivliostyle.js/issues/1035))
  - `lh` は行高(line-height)を基準にした長さの単位。
  - `rlh` はルート要素のline-heightが基準。
  
```css
p { /* 段落間を1行分あける */
  margin-block: 1lh;
}
header { /* 見出しブロックを3行どりに */
  block-size: 3rlh;
}
```
  
# 組版に役に立つCSS機能の追加実装

## これまで実装してきたCSS組版機能

Vivliostyle.jsでは、ブラウザでは未サポートのページメディア用CSS仕様など組版に必要なCSS機能の実装を進めてきた。以下は主なもの：

- [CSS Text Level 4](https://www.w3.org/TR/css-text-4/)
  - hanging-punctuation: 行末の句読点のぶら下げなど
  - text-spacing: 和欧文間スペースや約物の詰めなど
- [CSS Paged Media Level 3](https://www.w3.org/TR/css-page-3/)
  - ページメディア用CSSの基本仕様
- [CSS Generated Content for Paged Media](https://www.w3.org/TR/css-gcpm-3/)
  - 名前付き文字列（Named strings）：本文中の見出しなどの文字列をページヘッダーに柱として表示するのに使用
  - 脚注（Footnotes）
  - ページセレクター `:nth(An+B)`：n番目のページのスタイル
  - `target-counter()`：クロスリファレンスや目次・索引などの参照先ページ番号を生成するのに使用
- [CSS Fragmentation Level 3](https://www.w3.org/TR/css-break-3/)
  - 改ページや改ページ禁止の制御など
- [CSS Page Floats](https://www.w3.org/TR/css-page-floats-3/)
  - ページの上部や下部に図版などをフロート配置

## まだできてないCSS組版機能と改善課題

以下はページメディア用CSS機能でまだできてない主なもの：

- [margin-breakプロパティ](https://www.w3.org/TR/css-break-4/#break-margins)　([#734](https://github.com/vivliostyle/vivliostyle.js/issues/734))
  - ブロックのマージンがページの先頭になったときに、そのマージンを保持する(keep)か破棄(discard)するか指定。
    - デフォルトの `margin-break: auto` は、なりゆき改ページでページの先頭になったブロックのマージンを破棄
    - `margin-break: keep` はマージンを保持する
    - `margin-break: discard` はマージンを破棄（0にする）
- [Running Elements](https://www.w3.org/TR/css-gcpm-3/#running-elements)　([#424](https://github.com/vivliostyle/vivliostyle.js/issues/424))
  - HTML文書内の要素をページマージン領域に柱（欄外見出し）として表示できるようにする機能
  - 単純な文字列だけなら名前付き文字列（Named strings）で実現できるが、より複雑な要素を柱に表示するのに必要。
- [`@footnote` (footnote area)](https://www.w3.org/TR/css-gcpm-3/#footnote-area)　([#1045](https://github.com/vivliostyle/vivliostyle.js/issues/1045))
  - 脚注エリアのスタイルを指定
- [`leader()` function](https://www.w3.org/TR/css-content-3/#leaders)　([#1027](https://github.com/vivliostyle/vivliostyle.js/issues/1027))
  - 目次の項目とページ番号をつなぐ点線 ...... の出力に便利

<!--
- [Bookmarks](https://www.w3.org/TR/css-gcpm-3/#bookmarks)　([#590](https://github.com/vivliostyle/vivliostyle.js/issues/590))
  - 見出しからPDFのBookmarks（しおり）を作るのに便利。目次自動生成機能に使える。
- [Fixed positioning on paged media](https://drafts.csswg.org/css2/#fixed-positioning)　([#563](https://github.com/vivliostyle/vivliostyle.js/issues/563))
  - CSS2のページメディア仕様でfixed positioningは各ページに繰り返し出力される必要がある。
-->

### 組版処理の改善の課題

- 段組の指定がルート要素またはbody要素以外にあるとき、段組のページ分割処理がうまくいかない　([#579](https://github.com/vivliostyle/vivliostyle.js/issues/579))
- テーブル内で脚注が機能しない　([#438](https://github.com/vivliostyle/vivliostyle.js/issues/438))
- ページフロートの改良　([#543](https://github.com/vivliostyle/vivliostyle.js/issues/543))
- Mac以外の環境でハイフネーションが無効になる　([#569](https://github.com/vivliostyle/vivliostyle.js/issues/569))
  - Vivliostyle CLIをWindows、LinuxあるいはDockerで使う場合（Chromiumの問題）。


# Vivliostyleとそれ以外のCSS組版ツールの比較

## CSS組版ツールを比較評価してるサイト

[print-css.rocks](https://beta.print-css.rocks/)というサイトで、いくつかのCSS組版ツールをいろいろテストしての比較一覧が公開されている：

- https://print-css.rocks/lessons
- https://beta.print-css.rocks/lessons ←2022秋版ベータ公開。この1年くらいのVivliostyleの進化が反映されている。
- ほかと比較してのVivliostyleの強みと弱みが分かる。
- ここにあるテストサンプルはCSSを学ぶのにも参考になる。

# Vivliostyleまだまだ開発途上なのでどうぞよろしく！
