# R VEIL — "UNISON" トップページ 設計書 (top.md)

> このファイルは**トップページ（`src/pages/index.astro`）固有**の構成・文言・実装詳細をまとめたもの。全ページ共通のルール（カラー・タイポグラフィ・レイアウトの基本方針・共通コンポーネント仕様等）は[design.md](design.md)を参照。新しいセッションで作業を始める前に、design.mdとこのファイルの両方を必ず読むこと。

## 1. ページ概要

| 項目 | 内容 |
|---|---|
| プロダクト/コレクション名 | UNISON |
| コピー | 同じ素材、同じ機能、同じ表情。境界線を越える、タクティカル・ドッグウェア。 |
| 商材 | 撥水加工ドッグコート（愛犬用）＋ リバーシブル・ハンズフリーバッグ（飼い主用） |
| ページ種別 | 縦長1カラムの商品ストーリーテリング型LP（予約販売告知） |
| 現状のフェーズ | 予約販売の告知フェーズ。CTAは「予約 (Purchase >>)」表記。リンク先（予約フォーム/EC）は未確定 |
| 実装 | `src/pages/index.astro`にセクション本体を集約（単一ファイル） |

### 1.1 ブランド公式コピーとの実装差異メモ

design.md 2.3の「4つの美学」公式コピーと、実装との差異について（確認済み）：

- concept セクションの本文（7段落）は、ユーザーより再度共有された文章と一字一句比較し、**内容は完全に一致していることを確認済み**（微小なスペース差は改行→スペース変換の貼り付けアーティファクトのみ）。
- ただし、design.md 2.2 にあるブランドコンセプト文の最終段落「流行を追わず、一着一着に職人の息吹を…特別な一着がここにあります。」は、**まだconceptセクションの本文に含まれていない**（8段落目として追加するかどうかは要相談・未着手）。
- detailed information の4カード文言・並び順も、design.md 2.3 の公式コピーとは完全には一致していない（「大量生産をしないこと」の文言表現や並び順が異なる）。
- **新規の下層ページを実装する際は、design.md 2.3 の文言を正としてそのまま使用すること**（このページの実装がまだ揃っていないことの言い訳にしない）。
- **Header.astro（共通コンポーネント）のロゴが黒背景で見えなくなる不具合を修正**：`logo.png`が黒一色のアセットのため`filter: invert(1)`を追加（トップページのヘッダーにも影響する共通修正。詳細はdesign.md 4章「Header.astro」を参照）。

---

## 2. トップページ固有のデザイントークン

### 2.1 カラー（セクション固有・一回限りの背景色）

> **文字色の変更**：`.section__copy`（商品詳細の本文コピー等）・`.material-detail__icon-caption`は、以前の`var(--color-body)`/`var(--color-caption)`から**`color:#fff`に統一変更済み**。⚠️ `.section__copy`はbenefit.astro側にも同名のコピーされたルールがあるが（design.md 4.1参照）、そちらは`var(--color-body)`のまま未変更のため、2ページ間でわずかに色が異なる状態（`#fff` vs `#E8E8E8`）。統一するかどうかは要確認・未着手。

| 用途 | HEX / 値 | 使用箇所 |
|---|---|---|
| 撥水加工素材パネル背景 / detailed information背景 | `#1B232A` | 撥水加工素材（コート・バッグ両方）・detailed informationセクションの背景（`.hassui-bg`） |
| conceptテキストパネル背景 | `#121517` | conceptセクションのテキストパネルのみ（top.png実測。memoriesは`#1B2326`＝ニュースバーと共通のまま） |
| メンズウェア紹介の円背景 | `rgba(65, 75, 82, 0.52)`（`#414B52`の52%） | `.menswear__copy`（フーディー紹介の合成写真に重なる円形コピー）専用 |
| Size Guide背景 | `#191B24` | Size Guideセクションの背景 |

### 2.2 タイポグラフィ（固有の数値）

design.md 3.2で定義した「見出しはclamp()で可変にする」という方針に基づく、トップページ固有の具体的な係数：

| 要素 | サイズ |
|---|---|
| 商品見出し・その他全セクション見出し（`.section-heading__title--h2`, `.size-guide__title`） | `clamp(22px, 2vw + 1rem, 28px)`（SP実測22〜23.5px／PC実測28px） |
| コレクション名「UNISON」（`.intro__title`、`<p>`タグ・見出しタグではない） | `clamp(22px, 1.6vw + 1rem, 36px)`（SP実測22px／PC実測36px。他の見出しより最大値が大きい特別枠） |

> **経緯**：`.section-heading__title--h2`・`.size-guide__title`は元々固定28px（メディアクエリなし）で、SP幅で見出しが大きすぎる見た目になっていた。`BaseLayout.astro`のグローバル`h2`ルール（design.md 3.2）を基準に、上記2箇所は同じclamp値を個別クラスとしても明示指定している（クラス指定がグローバルh2ルールより優先されるため）。「UNISON」だけは元々36px固定という別基準だったため、専用の係数にしてSPは他の見出しと揃えつつPCでは36pxを維持している。

### 2.3 見出し罫線の伸長方向（セクション別一覧）

design.md 3.3の「見出し罫線を画面端まで伸ばすテクニック」を使っている、PC限定・トップページ固有の具体的な対象セクション：

| 方向 | 対象セクション（スコープクラス） |
|---|---|
| 右伸長 | 立体裁断・ショルダーストラップ仕様（`.cutting`）、リフレクターライン（`.split--reverse`） |
| 左伸長 | 撥水加工素材コート/バッグ共通（`.hassui-bg`）、他のこだわり（`.material-detail`）、両面の表情を持つ（`.reversible`） |

---

## 3. モーション/インタラクション（トップページ固有の配置詳細）

| 要素 | 挙動 |
|---|---|
| ヒーロー動画 | `hero.mp4`（1920×1080, 16:9）を`<video autoplay muted loop playsinline>`で自動再生。下部のカスタムUI（再生/一時停止・シークバー・再生時間・音量ミュート切替・フルスクリーン）は全て実際の`<video>`要素と連動。**SPは`aspect-ratio: 4/5`の縦長コンテナ＋`object-fit: cover; object-position: 50% center;`で左右をトリミングし、被写体（人物・犬）が画面いっぱいに収まるようにしている**（16:9のまま表示すると被写体の上下に余白ができてしまうため）。PCは`height: 100svh; aspect-ratio: auto;`で全画面表示に戻す（クロップなし） |
| ニュースバー | 横固定の告知帯、リンク付き（`詳細はこちら >>`）。リンク先は`#bag`から`${base}/benefit/`（ご予約特典ページ）に変更済み |
| 商品画像ギャラリー | ドットインジケーター＋サムネイルグリッド（コート・バッグとも5列に統一）付きカルーセル。4秒間隔で自動ループ再生。ドット/サムネイルをクリックすると即座にそのスライドへ移動し、自動再生タイマーをリスタートする |
| Purchaseボタンの配置 | コート・バッグ商品詳細それぞれのテキスト列の**右下に絶対配置**（`position:absolute; right:0; bottom:0;`）、120×120px。上の要素（価格・スウォッチ）と被らないよう、親コンテナに`padding-bottom:140px`を確保（ボタン自体の色・挙動はdesign.md 3.4参照） |

---

## 4. ページ構成（セクション別ワイヤーフレーム）

> セクション番号は`index.astro`内の実装順。以下は初期実装からの**主な変更点のみ**を記載（変更のない詳細は簡潔に留める。過去の全文コピーはgit履歴を参照）。Headerコンポーネント自体の仕様はdesign.md 4章参照。

### Section 01 — ヒーロー（動画）
- `hero.mp4`を実装済み（3章参照）。テキストプレースホルダーは撤去済み
- **SPは4:5クロップ**（3章参照）。`object-position: 50% center`（中央固定）。以前は`42% center`だったが、動画ループ中の主要シーン（森を歩く後ろ姿・都市の横顔ショット等）を複数フレーム実測した結果、被写体はいずれも画面中央（48〜52%付近）に位置しており、42%だと右側の被写体（人物の身体・犬の鼻先）がわずかに見切れていたため中央寄せに修正済み

### Section 03 — UNISON イントロ
- 見出し「UNISON」は`<p>`タグ（h1ではない。h1はヘッダーロゴのみのため）
- 背景写真：**PC**は実寸（3840/2000）に一致させクロップなし。**SP**はヒーロー動画（3章参照）と同じ考え方で`aspect-ratio: 4/5; object-fit: cover; object-position: 60% center;`の縦長クロップにし、被写体（人物・犬）の上下の余白を解消（元画像は横長のため、SPでそのまま表示すると被写体が小さく余白が目立っていた）

### Section 04 — ビジュアルインターリュード
- 背景写真：**PC**は実寸3840/2640に一致させクロップなし、テキストは左上オーバーレイ＋gradientパネルで可読性確保。**SP**はSection 03と同じ考え方で`aspect-ratio: 4/5; object-fit: cover; object-position: 45% center;`の縦長クロップにする
- `.interlude__copy`の`max-width`：**SP**はなし（`max-width`指定を削除し、画面幅いっぱいまで広げて2行に収める）。**PC**は`480px`のまま変更なし
- `.interlude__copy`の`padding`：**SP**は`16px 20px`→`24px 0px`に変更（左右paddingをなくし、テキストパネルの背景グラデーションを画面幅いっぱいに広げる意図。PC側の`padding:24px 32px`は変更なし）

### Section 05 — 全天候型対応サイトハウンドアウター（コート）
- メイン商品写真カルーセル：**`coat-01.png`〜`coat-10.png`の10枚**、サムネイルグリッドは**5列×2段**
- 画像解像度：`coat-01`〜`coat-10`は全て高解像度化済み（880×880 or 612×612）。低解像度の問題は解消（`coat-11.png`は未使用のため153×153のままだが問題なし）。`coat-02.png`は途中でユーザーにより差し替え済み（マネキン全身ショット、612×612）
- 見出し下の罫線（下線）は**非表示**（`.coat-product`スコープで`.section-heading__rule { display:none; }`）
- fabric/colorのラベル表記は小文字（`text-transform: lowercase`）
- Purchaseボタンは右下絶対配置（3章参照）
- **Product Galleryの角丸**：メイン画像（`.carousel__slide--square`）・サムネイル（`.thumb-grid__item`）とも`border-radius:10px`（以前は20px）。**親要素（`overflow:hidden`+`border-radius`）だけでなく`<img>`自身にも同じ`border-radius`を明示**している（Safari/WebKit系でobject-fit:coverのimgがGPU合成レイヤーに昇格し親のクリップが効かないことがあるための保険。Chromeでは親imgどちらか一方の指定だけでも見た目は変わらない＝相互に冗長だが、どちらも残して問題ない）

### Section 06 — 立体裁断（`.cutting`パターン、13・14と共通）
- 見出し下の罫線：**右端だけ画面の真の端まで伸びる**（左端はコンテンツ幅で止まる。2.3参照）
  - CSS: `width: calc(100% + max(40px, (100vw - 1200px) / 2 + 40px));`（`margin-right`の負値では`width:100%`要素は伸びないため、`width`自体を拡張する方式を採用）
  - 過去に`.cutting :global(.section-heading) { margin: 0 auto; }`という中央寄せ用ショートハンドが**意図せず`margin-bottom`（罫線と写真の間の余白）も0にリセットしてしまい**、罫線が写真に直接くっついて実質見えなくなっていた不具合があった。`margin: 0 auto 24px;`に修正済み
- **`.cutting__media img`はSPで`object-fit: contain; height: 65vw;`**（以前は`cover; height:120vw;`で犬・人物の全身が見切れていたため変更。レターボックス方式で全身を収める）
- **「着用シーン（森）」（`bag-bg03.png`）・「ショルダーストラップ仕様」（`strap-bg.png`）はSP幅のみ専用画像に差し替え**：`<picture>`で`bag-bg03_sp.png`（750×1020、PC/SPとも`.cutting__media img`共通の`object-fit:contain`ではなく、この2枚だけ`img[src*='bag-bg03']`/`img[src*='strap-bg']`の属性セレクタでSP限定`object-fit:cover`＋固有の`aspect-ratio`（bag-bg03は`4/5`、strap-bgは`4/3`）を上書きしている。PC側は既存の`img[src*='bag-bg03|strap-bg']`（実寸に一致させたaspect-ratio）のまま変更なし

### Section 08 — リフレクターライン（旧「夜行反射機能」）
- **タイトルを「夜行反射機能」→「リフレクターライン」に変更済み**（コード内コメントも含めて統一）
- 見出し下の罫線：**PCで右端だけ画面の真の端まで伸びる**（`.split--reverse`スコープ、`.cutting`と同じ右伸長パターン）
- `.split__media img`はSPで`object-fit: contain`に変更（heightは120vwのまま）。他の`.split`系セクション（撥水加工素材等）も同様

### Section 09 — 他のこだわり
- 円形サムネイル：PC幅**150px→165px**、gap **24px→40px**に変更
- キャプションは**中央揃え**（以前は左揃え）
- 見出し下の罫線：**PCで左端だけ画面の真の端まで伸びる**（`.material-detail`スコープ、`.hassui-bg`と同じ左伸長パターン）
- `.material-detail__icon-caption`の文字色を`#fff`に変更（以前は`var(--color-caption)`）

### Section 10 — 職人の手元バナー
- 写真に`rgba(27, 35, 42, 0.6)`の色オーバーレイを追加（`.craft__media::after`、`position:relative`を`.craft__media`に追加）
- **`.craft__media img`のaspect-ratioはSPのみ`3840/1990`に変更**（PCは元の`3840/950`のまま維持。SP/PCで別々の値になるのは初めてのケースで、PC用のオーバーライドを`@media(min-width:768px)`側に追加した）。⚠️ 現在の`banner.png`実ファイル（750×422）はこの`3840/1990`と実寸比率が一致しないため、SP幅では上下がわずかにクロップされる。加えて未コミットの`banner_sp.png`は`banner.png`と完全に同一内容（バイト単位で一致）で、実質SP専用の別クロップにはなっていない。**要フォローアップ**（7章TODO参照）
- **本文コピーにSP限定の改行を追加**：「限られた数だけを／丁寧に仕立てる…」「深く／よりスタイリッシュに…」の2箇所に`<br class="sp-break" />`を挿入し、SPのみ5行に分割（PCは元の3行のまま）。`.sp-break`は`:global(.sp-break){display:none}`をPCメディアクエリ内に追加して実現（次項5章「SP限定改行の実装パターン」参照）

### Section 11 — Size Guide
- 見出しは`<h2>`
- 背景色`#191B24`を画面幅いっぱいに拡張（design.md 3.3の全幅背景パターン使用、`clip-path`込みで隣接セクションへの色漏れなし）

### Section 09 — メンズウェア（フーディー）紹介（`.menswear`、バッグ側フローで番号が再度09から始まる。コード内コメント準拠）
- 2枚の写真（`bag-bg01`/`02`系）の継ぎ目に重なる**円形（フロストガラス風、`backdrop-filter:blur(6px)`）**にコピーを乗せるレイアウト。テキストは「両手を自由にする、革新のカラビナフック。DOGウェアと美しく同調する、撥水加工リバーシブル・ハンズフリーバッグ。」
- 円の背景色：`rgba(65, 75, 82, 0.52)`（2.1参照）
- 円のサイズ：**SP `min(320px, 78%)`／PC `330px`固定**
- **SP専用画像を`<picture>`で出し分け**：SPは`bag-bg_sp01.png`/`bag-bg_sp02.png`（750×1020、SP幅に合わせて別途トリミング支給されたアセット）を縦に隙間なく2枚積む。PC（768px〜）は`<source media="(min-width:768px)">`で従来の`bag-bg01.png`/`bag-bg02.png`（1920×2012）に切り替え、横並びにする。画像ごとに比率が異なるため`.menswear__grid img`の`aspect-ratio`もSP（750/1020）とPC（1920/2012）で出し分けている
  - `<picture>`はflexアイテムとして扱わせないよう`display:contents`を指定（中の`img`がそのままflexアイテムになるようにするため）
  - ⚠️ devサーバーで**ビューポートをリサイズしただけ**だと`<picture>`の画像選択が再評価されないことがある（ブラウザの`<picture>`仕様）。確認時は必ずページを再読み込みすること

### Section 13 — エッセンシャル リバーシブルバッグ
- サムネイルグリッド：4列→**5列**に統一（コートと同じパターン）
- 見出しは**右揃え**＋罫線**非表示**（`.bag-product`スコープ）
- Purchaseボタン・fabric/color小文字化はコートと同様
- 画像解像度：全て高解像度化済み（`bag-01.png`は1760×1760、`bag-02.png`〜`bag-10.png`は612×612）
- サムネイル下の注記（`.note`）：「※実機と印刷物では、光の当たり方により色味が異なって見える場合がございます。」→「※掲載画像は試作品をもとにしたイメージです。実際の商品とは、色味・仕様・ロゴの配置などが異なる場合があります。」＋「※製造工程の特性上、商品ごとに多少の誤差（1 ~ 3 cm程度）が生じる場合がございます。予めご了承ください。」の2行に変更
- **見出し「エッセンシャル リバーシブルバッグ」はSP幅のみ改行**：`<SectionHeading title={`エッセンシャル <br class="sp-break" />リバーシブルバッグ`} .../>`。⚠️**重要な実装上の注意**：`SectionHeading.astro`の`title`は元々`{title}`（プレーンテキスト、HTMLエスケープされる）で出力していたため、`<br>`を効かせるには`set:html={title}`に変更する必要があった（`SectionHeading.astro`本体を修正済み）。ただし`set:html`で注入される要素はAstroのスコープ付きCSS（`data-astro-cid-*`自動付与）の対象外になるため、`.sp-break{display:none}`をこのファイル（index.astro）のスコープ付き`<style>`にそのまま書いても効かない不具合が発生した。**`:global(.sp-break){display:none}`に変更して解決**（5章参照。今後`SectionHeading`の`title`にHTMLを埋め込む全てのケースで同じ注意が必要）

### Section 13 — ショルダーストラップ仕様（`.cutting`、`.cutting--gap-top`修飾クラス）
- 直前が「撥水加工素材（バッグ）」（`hassui-bg`、標準の`.section`パディングのみ）で他の`.cutting`（立体裁断・着用シーン、Purchaseボタン分の余分な余白がある商品セクションの直後）より上の余白が狭く見えたため、`.cutting--gap-top`修飾クラスを追加し標準の`.section`上パディング（SP 64px/PC 96px）を補った
- テキスト（`.cutting__copy`）の位置：top.png実測の結果、画像上端から**約33%の位置**（他の`.cutting`インスタンスの`top:12%`とは異なる）。このセクションのみ`.cutting--gap-top .cutting__copy { top: 33%; }`で個別に補正
- テキスト背景色は他の`.cutting__copy`と共通の`#0303051c`（ほぼ黒・11%程度の淡いオーバーレイ）のまま変更なし

### Section 14 — 両面の表情を持つ（`.reversible`）
- 見出しは**右揃え**（top.png実測。`.bag-product`と同じ扱い）。PCのみ`.reversible :global(.section-heading) { text-align: right; }`
- 見出し下の罫線：**PCで左端だけ画面の真の端まで伸びる**（右揃えの見出しテキストに対して、罫線は逆に左へ伸ばす）
- 写真2枚の対角配置（48%/52%継ぎ目一致、写真2下寄せ）

### Section 15 — 撥水加工素材（バッグ）
- 背景色`#1B232A`を画面幅いっぱいに拡張（コート側の「撥水加工素材」と共通、`.hassui-bg`クラス）
- 見出し下の罫線：**PCで左端だけ画面の真の端まで伸びる**（`.hassui-bg`スコープ、コート側の同名見出しにも同時適用）

### Section 18 — detailed information
- 見出しは`<h2>`、`margin-bottom`は**40px**
- **仕様変更（アイコン→ナンバーバッジ）**：`information-01`〜`04`の画像アイコンは廃止。top.png再実測の結果、カード左上にオーバーラップする円形の「01」〜「04」ナンバーバッジに変更された。バッジは塗り潰し円（`#494949`）、文字は白・**Roboto Condensed（weight 300）**。フォントは`BaseLayout.astro`の`<head>`でGoogle Fontsから読み込み（`Roboto+Condensed:wght@300;400`）
- カード自体も枠線のみ→**塗り潰し背景**（`#07080a`、ほぼ黒）の角丸パネルに変更。border-radius 10px
- 画像アイコンが完全に不要になったため、`Placeholder.astro`コンポーネントは削除済み（他に使用箇所なし）
- **セクション背景**：top.png実測により`#1B232A`（`hassui-bg`クラス）を追加。カードの塗り潰し背景（`#07080a`）とのコントラストでtop.png通りの見た目になる
- **カードグリッドのgap**：SPは`gap:30px`（縦積み1カラム）。PCは`display:grid`の2カラムで**column-gap:24px / row-gap:40px**と縦横で差をつけている（以前はflexboxの`flex-wrap`を使っており、2行目ぶんコンテナの高さがreveal未発火時に不足する潜在バグがあったためgridに変更。実害はopacity:0の間だけだったが念のため修正）

### Section 19 — concept
- 見出しラベル「concept」は`<h2>`
- **レイアウトの結論**：写真とテキストパネルの**背景は画面の真の端まで届く**（`.section`のmax-width:1200px・左右paddingを`.concept`のみ打ち消し）。**テキストの本文だけ**はコンテンツ幅の基準線（`max(40px, calc((100vw - 1200px)/2 + 40px))`）に合わせて右側にpaddingを取り、他セクションと揃える
  - ※このレイアウトは1往復のやり直しを経て確定した仕様。「行全体を1200pxに収めて背景だけ画面幅に広げる」バージョンを一度実装したが、これは誤りで、正しくは「行（写真＋パネル）ごと画面幅に広がり、テキストのみコンテンツ幅で止まる」が正解だった（現在の実装はこちらで確定）
- 写真に`rgba(27, 35, 42, 0.6)`の色オーバーレイを追加。ラベル「concept」文字はオーバーレイより手前に見えるよう`z-index:2`（オーバーレイ側は`z-index:1`）
- **SP幅でも左右フルブリード化**：以前はSPのみ`.section`の左右paddingが残ったままで、画像・パネル背景の左右に余白ができていた。`.concept`にSP/PCとも`padding-left:0; padding-right:0;`を追加して解消
- **テキストパネル背景色**：top.png実測により`#121517`に変更（2.1参照。以前は`var(--color-newsbar-bg)` = `#1B2326`）
- **上の余白を撤去**：直前のdetailed informationと隙間なく連続させるため、SP/PCとも`padding-top:0`を追加

### Section 20 — memories
- Section 19と同様の考え方（左右ミラー）。見出しラベル「memories」は`<h2>`、写真にも同じ色オーバーレイ
- SP幅でも左右フルブリード化（Section 19と同様、`padding-left:0; padding-right:0;`をSP/PCとも追加）
- **下の余白を撤去**：直後の「Follow us」セクションと隙間なく連続させるため、SP/PCとも`padding-bottom:0`を追加
- **`.memories__sign`（サイン画像）はSPのみ`width:100px`に変更**（以前はSP/PC共通で140px固定だったため、PC側に`width:140px`のオーバーライドを追加して既存の見た目を維持）

### Section 21 — Follow us
- SNSアイコンは**5個→3個に削減**。当初はFacebook/X/Instagramの構成だったが、画像差し替え後は**Facebook/Instagram/TikTok**の3つに変更（`icon-01`〜`03.png`自体は同じファイル名のまま中身を差し替え。`aria-label`を実際のロゴに合わせて修正済み）
- **`.sns__media img`のaspect-ratioはSPのみ`4/5`に変更**（PCは元の`3840/2640`のまま維持、PC用オーバーライドを追加）。実写真（`sns-bg.png`、3840×2640）は横長で被写体（人物・犬）の顔が画面中央に大きく写っているため、`object-position`を個別調整しなくても中央クロップで両方の顔が収まることを実機で確認済み

### Section 22 — news
- 見出しは`<h2>`
- news項目1件目（「ご予約のお客様先着20名に...」）の`href`は`#`から`${base}/benefit/`に変更済み。ご予約特典ページ（[benefit.md](benefit.md)）への実際のリンクとして機能する。他3件は`#`のまま未確定

### Footer
- Back To Topボタンの枠線を削除（`border: none`。挙動自体はdesign.md 3.4参照）
- リンク一覧「pre-order benefit」の`href`も`#`から`${base}/benefit/`に変更済み（`Footer.astro`の`links`配列を`[{label, href}, ...]`形式に変更し、リンクごとに実URLを持てるようにした。他7項目は`href: '#'`のまま未確定）

---

## 5. トップページ固有のコンポーネント/CSSクラス

| クラス | 説明 |
|---|---|
| `.cutting`（立体裁断・着用シーン・ショルダーストラップ仕様で共通） | 見出し罫線の右端伸長・SP `object-fit:contain`。`.cutting--gap-top`修飾クラスでショルダーストラップ仕様のみ上パディングとテキスト位置（top:33%）を個別補正 |
| `.hassui-bg`（撥水加工素材コート/バッグ・detailed information共通） | 背景色`#1B232A`＋全幅背景パターン＋見出し罫線の左端伸長 |
| `.material-detail`（他のこだわり） | 見出し罫線の左端伸長 |
| `.reversible`（両面の表情を持つ） | 見出し右揃え＋罫線左端伸長、写真2枚の対角配置 |
| `.split--reverse`（リフレクターライン） | 見出し罫線の右端伸長 |
| `.menswear__grid`（メンズウェア紹介） | SP専用画像（`bag-bg_sp01/02.png`）を`<picture>`で出し分け |
| Product Gallery（コート・バッグ共通） | サムネイル列数を5列に統一。メイン画像（`.carousel__slide--square`）・サムネイル（`.thumb-grid__item`）とも角丸`border-radius: 10px`（以前は20px） |

---

## 6. アセット準備リスト（現状ステータス）

- [x] ロゴ画像：支給済み（`public/images/logo.png`, 550×356）
- [x] ヒーロー用動画：支給済み（`public/videos/hero.mp4`, 1920×1080, 16:9, 約31MB）
- [x] コート商品写真：`coat-01`〜`coat-10`は全て高解像度化済み
- [x] バッグ商品写真：`bag-01`〜`bag-10`は全て高解像度化済み（`bag-01`1760×1760、他612×612）
- [x] メンズウェア紹介用SP専用画像：支給済み（`bag-bg_sp01.png`/`bag-bg_sp02.png`, 750×1020）
- [x] SNSアイコン（`icon-01`〜`icon-03`）：支給済み。中身がFacebook/Instagram/TikTokに差し替わった（`icon-04`/`icon-05`は未使用のまま）
- [x] detailed informationアイコン：仕様変更によりそもそも画像アイコンが不要になった（円形ナンバーバッジ「01〜04」+ Roboto Condensedに置き換え済み。`Placeholder.astro`は削除）
- [x] フッター「pre-order benefit」・newsセクション1件目の遷移先：`${base}/benefit/`に確定・実装済み（ご予約特典ページ新設に伴い対応）
- [x] フッター「reservation」「contact」の遷移先：HubSpotフォームのURLに確定・実装済み
- [ ] 残りの各リンク（ヘッダーロゴ／フッター「privacy policy」「concept」「contact us」／newsセクション残り3項目／SNSアイコン3個）の実際の遷移先：`#`のまま、要確定
- [ ] conceptセクション本文への「シリアル生産／日本製」段落の追加是非（1.1参照）
- [ ] detailed informationの4カード文言・並び順を、design.md 2.3の公式コピーに合わせるかどうか（要相談、未着手）

---

## 7. 次セッションTODO（優先度順）

1. **`banner.png`の高解像度版が失われている可能性**：`public/images/banner.png`（職人の手元バナー、Section 10）の中身が、直前のコミット時点（3840×950の高解像度）から750×422の低解像度画像に書き換わっており、新規追加された`banner_sp.png`と完全に同一ファイルだった（バイト単位で一致）。bag-bg03/strap-bgと同じ「元画像はそのまま残し`_sp`版を別途追加」というパターンを意図していたなら、`banner.png`のオリジナル高解像度版が上書きで失われている。gitの履歴（コミット`a10af39`時点）には元の3840×950版が残っているため、復元してsaidan等と同様に`<picture>`で出し分ける対応が必要か、ユーザーに確認する。
2. **残りのリンクの遷移先確定**：フッター「pre-order benefit」・「question and answer」・「payment」・「reservation」・「contact」・ニュースバー・newsセクション1件目は確定済み。ヘッダーロゴ／フッター「privacy policy」「concept」「contact us」／newsセクション残り3項目／SNSアイコン3個は、すべて`#`のまま。実際のURLが決まったら差し替え。
3. **conceptセクションの本文の扱い**：ブランドコンセプト公式文（design.md 2.2）にある最終段落（シリアル生産／日本製）を追加するか要相談。
4. **detailed informationの文言統一**：4カードの文言・並び順をdesign.md 2.3の公式コピーに揃えるか要相談（現状は初期のカンプ転記のまま。ナンバーバッジ化は完了済み）。
5. **和文コピー・価格・スペック値の最終確認**：本文は全てカンプから転記済みだが、確定情報かどうかの最終チェックが必要。
6. **`.section__copy`の文字色統一**：index.astro側は`#fff`に変更済みだが、benefit.astro側は`var(--color-body)`（`#E8E8E8`）のまま。2ページで統一するか確認（2.1参照）。
7. ~~**ヒーロー動画SPのobject-position微調整**~~：解決済み。主要フレームの実測により`50% center`（中央固定）に修正済み（4章 Section 01参照）。
8. **見出しのclamp化に伴う見え方の再確認**：`.section-heading__title--h2`・`.size-guide__title`・`.intro__title`をclamp化したことでSP/タブレット幅の見え方が変わっている。特にタブレット幅（768px前後）は係数のなだらかな遷移域に入るため、実機で違和感がないか確認するとよい。
