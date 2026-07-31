# R VEIL — 「ご予約特典」ページ 設計書 (benefit.md)

> このファイルは**下層ページ「ご予約特典」（`src/pages/benefit.astro`）固有**の構成・文言・実装方針をまとめたもの。全ページ共通のルール（カラー・タイポグラフィ・レイアウトの基本方針・共通コンポーネント仕様等）は[design.md](design.md)を参照。トップページの実装パターンを多数流用するため、[top.md](top.md)もあわせて参照する。

> **ステータス**：実装済み（`src/pages/benefit.astro`）。dev serverで表示・リンク遷移を確認済み。

## 1. ページ概要

| 項目 | 内容 |
|---|---|
| ページ名 | ご予約特典（pre-order benefit） |
| URL | `/benefit/`（独立した別ページ。トップページの一部ではない） |
| デザインカンプ | `benefit.png`（プロジェクトルート、1920×3600）。`top.png`と同様に`.gitignore`対象に追加済み（`/benefit.png`） |
| 目的・ゴール | 予約特典（先着20名限定のプレゼント）の内容を訴求し、トップページからの遷移先として**予約の後押し（コンバージョン強化）**をすることが目的。単体の商品ページというより、トップページの「予約特典」訴求を補強するサブページという位置づけ |
| 遷移元 | トップページのnewsセクション1件目（`news-04.png`「ご予約のお客様先着20名に断熱素材、撥水加工小物入れをプレゼント。詳細はこちら>>」）、およびフッターリンク一覧「pre-order benefit」から`/benefit/`へ遷移するよう実装済み |
| 遷移先（close） | ページ下部の円形ボタン「close」はトップページ（`/`）へ戻るリンク。モーダルではなく**独立したページ遷移**として実装（確認済み） |

---

## 2. デザイン参照・既存パターンの流用方針

`benefit.png`を実測した結果、新規デザインではなく**トップページで確立済みのコンポーネント・パターンを流用する構成**であることを確認した。新規に考案するスタイルはほぼなく、以下の対応関係で実装できる見込み：

| benefit.pngの要素 | 流用元（top.md / design.md） |
|---|---|
| ヘッダー「pre-order benefit / ご予約特典」＋下線罫 | `SectionHeading.astro`（design.md 4章）そのまま |
| 告知パネル（見出し＋本文＋注記2行＋写真） | パネル自体は**フラットな単色背景**`#1B232A`（写真をCSS背景として敷くのではない）。テキストは他の`.section`と同じコンテンツ幅に収まり、さらにパネル自体も画面の真の端から左右20pxだけ内側に収まる（`margin:0 20px`）。`public/images/benefit.png`（ポーチ+サングラス写真、1778×1040）は**パネルの外側・下に続く独立した写真ブロック**として配置する。**写真スタック（2枚重ね）演出は使わない**（後述、3章B2参照：`benefit.png`実測の結果、そもそも1枚の写真がすでに「左に無地の面／右にポーチ」の構図を持っていることが判明し、2枚重ねの解釈は誤りと判明・撤回した）。noteの2行注記パターンは先日のバッグ商品ページ注記と同じ形式 |
| 商品写真カルーセル（メイン画像＋ドットインジケーター＋5枚サムネイル、隣のスライドが左端にわずかに覗く） | Product Gallery（top.md 5章、コート/バッグと共通パターン）。実素材`benefit-01〜05.png`支給済み |
| 商品名見出し「断熱マルチポーチ / Insulated Multi-Pouch」 | `SectionHeading`のsubtitleパターン（コート/バッグの商品見出しと同じ`.section-heading__title--h2`扱い） |
| size / fabric / colorのスペックリスト＋スウォッチバー | 商品詳細セクションのspec-list・`.swatch-bar`パターン |
| 円形「close」ボタン | `.btn-circle`（design.md 3.4のPurchase/Back to Topと同じ共通circleボタン基盤）を流用し、背景色のみ`rgba(76, 55, 100, 0.7)`（`#4C3764`70%）の専用トークンに変更、遷移先はトップページへの通常リンク |

**参考にしたい既存ページ／サイト**：ご指定なし。上記の通り、実質的には**トップページ自身が参照元**（同一ブランド内での一貫性を保つため、新しい参照サイトは探さずトップページのパターンを踏襲する方針で進める想定）。外部サイトの参考があれば教えてください。

---

## 3. セクション構成（ワイヤーフレーム）

`benefit.png`実測に基づく。セクション番号は仮。

### Section B1 — ヘッダー／フッター
- **トップページと共通**（`Header.astro`のロゴ、`Footer.astro`のリンク一覧＋Back to Top）をそのまま使う（確認済み）
- ページタイトル「pre-order benefit」＋サブラベル「ご予約特典」＋下線罫は、Header直下に`SectionHeading`で独自に追加する（右伸長ありの罫線）
- `.benefit-heading-section`の`padding-bottom`は`60px`（SP/PC共通。以前は`0`だった）

### Section B2 — 予約特典イントロ（告知パネル）
- 見出し：「ご予約いただいた先着20名様にマルチポーチをプレゼント」（`.benefit-intro__title`。**`.section__copy`と同じフォントサイズ**＝独自のfont-size指定を持たず本文の16pxを継承。色は`#797D83`）
- 本文：「ミニマルでエッジの効いたスクエアデザイン。ガジェットや小物を収納するマルチポーチとしてお使いいただけます。内側には保温・保冷シートを使用し、冷たいドリンクや温かい飲み物の持ち運びにも便利です。」
- 注記（2行、`.note`パターン。**色は`#fff`**、`margin: 24px 0`＝上下24px）：
  - 「※プレゼントは小物入れ本体のみです。サングラスは含まれません。」
  - 「※掲載画像は試作品をもとにしたイメージです。実際の商品とは、色味・仕様・ロゴの配置などが異なる場合があります。」
- **パネル背景はフラットな単色`#1B232A`**（`.benefit-intro`）。写真をCSS背景として敷くものではない（当初、`public/images/benefit.png`を背景画像＋`rgba(27,35,42,0.82)`オーバーレイとして実装していたが、これはユーザー指摘により誤りと判明し撤回した）。ページ全体の背景色は`#030305`のまま
- パネル自体は画面いっぱいには伸びず、他の`.section`と同じコンテンツ幅（左右padding）の内側に収まる。**さらに`benefit.png`実測の結果、パネル背景自体も画面の真の端から左右20pxだけ内側に収まっている**ことが判明したため、`.benefit-intro`に`margin:0 20px`を追加（PCは既存の`.section{margin:0 auto}`が後勝ちするため中央寄せのまま、影響なし）
- **縦のpadding**：`padding-top`はSP `120px`（PCは`.section`共通ルールの`96px`にフォールバック。以前入れていたPC専用の`180px`指定は撤回・削除）。`padding-bottom`は写真の重なり量に合わせた可変値（次項参照）
- **写真は「2枚重ねスタック」ではなく1枚**：`public/images/benefit.png`（1778×1040）実物を確認したところ、画像自体がすでに「左に無地の暗い面、右にポーチ+サングラス」という構図を持つ1枚の横長写真だった。当初design.md 3.3の「写真スタック（2枚重ね）演出」を誤って適用していたが、これは誤りと判明し撤回・**back側の`<img>`を削除**。**`.benefit-intro`セクションの外に出した独立ブロック**（`.benefit-intro__visual` > `.benefit-intro__photo`）とし、左はコンテンツ幅の基準線に揃え、右は画面の真の端まで伸ばす（design.md 3.3「見出し罫線を画面端まで伸ばすテクニック」と同じ計算式`max(40px, calc((100vw - 1200px)/2 + 40px))`を`margin-left`/`width`に応用）。`aspect-ratio:1778/1040; object-fit:cover;`で実寸比率のままクロップ
- **パネルと写真は重ねて配置（`negative margin-top`）**：`benefit.png`をピクセル単位で再測定した結果、パネル背景（`#1B232A`）は本文終了後もかなり下まで続いており、写真はその途中から重なって配置され、**写真の高さの約90%がパネルに重なり、残り10%がパネル下端からはみ出る**構成だった（デザイナー意図として忠実に再現）。`.benefit-intro__visual`の`margin-top`を`calc((100vw - 20px) * -0.5264)`（SP）/ 同様の式（PC）にして写真を引き上げ、`.benefit-intro`の`padding-bottom`を同じ式の正の値にして重なり分の高さを確保している。`position:absolute`ではなく通常のドキュメントフロー上でnegative marginを使う理由：写真はPCで`.benefit-intro`（`max-width:1200px`制約あり）の外に出た兄弟要素のままにする必要があり（画面の真の端まで伸ばすため）、`position:absolute`にすると親の1200px制約内に収まってしまうため

### Section B3 — 商品写真ギャラリー
- メインカルーセル画像＋ドットインジケーター＋5枚サムネイル（Product Galleryパターンをそのまま流用）
- **写真スタック演出は適用しない**：各スライドは単一の商品写真のみ（`.carousel__slide--square { overflow:hidden; border-radius:10px; }`、`object-fit:cover`。角丸はtop.mdに合わせて10pxに統一済み）。当初B3のメイン画像に誤って写真スタック効果（2枚重ね）を適用してしまったが、ユーザー指摘により撤回・B2側の演出として実装し直した（上記参照）
- 実素材は`benefit-01.png`〜`benefit-05.png`として支給済み（`benefit-01`は880×880、`benefit-02`〜`05`は612×612。coat/bagと同じ解像度基準で問題なし）
- なお`public/images/benefit.png`（1778×1040、横長）は別素材で、Section B2専用に使用する（上記参照）。ファイル名が似ているため混同注意

### Section B4 — 商品詳細
- 商品名見出し：「断熱マルチポーチ」／サブタイトル「Insulated Multi-Pouch」
- スペックリスト：
  - size：H22cm / W11.5cm
  - fabric：100% nylon
  - color：dark gray
- **スウォッチバーはcoat/bagと同じ3トーン・グラデーション表現**：`.swatch-bar--benefit`は当初単色`#45464a`で実装していたが、デザインカンプ実測により誤りと判明。coat/bagの`.swatch-bar--coat`等と同じ3色ハードストップ・グラデーションに変更：`linear-gradient(to right, #443e3d 0% 68%, #5c5a57 68% 85%, #bab6a0 85% 100%)`（色・比率は添付画像からの近似値。実際の生地見本と異なる場合は要調整）
- Purchase相当のボタンはナシ（プレゼント品のため購入導線は不要という理解。要最終確認）
- **PC幅は商品ギャラリーと商品情報の左右を入れ替え**：`.product-detail__body`に`flex-direction: row-reverse`（PC限定）を指定し、商品情報（テキスト・スペック）が左・ギャラリーが右になるよう変更（coat/bag商品ページとは逆順）。SP幅は縦積みのため表示順に影響なし

### Section B5 — close CTA
- 円形ボタン「close」→ トップページ（`/`）へ戻る通常リンク（確認済み）
- `.btn-circle`（design.md 3.4）を流用し、背景色は**`rgba(76, 55, 100, 0.7)`（`#4C3764`の70%透過）専用トークン**として新規追加（確認済み。Purchase/Back to Topとは別の3つ目のcircleボタン配色になる）

---

## 4. 未確定事項・要相談

1. **スペック値の確定**：size/fabric/colorの記載値はカンプ転記のまま。確定情報かどうかの最終確認が必要（top.mdの他ページと同様の運用。実装のブロッカーにはしない想定）。
2. ~~**Section B2の写真スタック配置**~~：解決済み。`benefit.png`実測により2枚重ねスタックではなく1枚の横長写真と判明し、3章B2の通り実装し直した。
3. ~~**Product Galleryの角丸がtop.mdと不一致**~~：解決済み。`border-radius:10px`に統一（img自身にも冗長指定、design.md 4章参照）。
4. ~~**`.section__copy`の文字色がtop.mdと不一致**~~：解決済み。`#fff`に統一。

---

## 5. 実装メモ

- `src/pages/benefit.astro`として新規作成。`BaseLayout`でラップすることでHeader/Footerはトップページと共通のまま自動的に流用される
- `.section` / `.hassui-bg` / `.note` / `.section__copy` / `.product-gallery`・`.carousel*`・`.thumb-grid*` / `.spec-list*` / `.swatch-bar` は、index.astro内でスコープされたCSSのため`BaseLayout.astro`のグローバルCSSには含まれない。**このファイル専用の`<style>`ブロックにindex.astroと同じ実装値をコピーする形で対応した**（プロジェクト全体でCSSを共通ファイルに切り出す構成にはなっていないため、既存の実装スタイルを踏襲）
- 商品ギャラリーの`initGallery(prefix)`関数もindex.astroから移植し、`initGallery('benefit')`として呼び出し（`#benefit-carousel`/`#benefit-dots`/`#benefit-thumbs`のIDで動作）
- ページ見出し「pre-order benefit / ご予約特典」は、英語タイトルが大きく・日本語が小さく同じ行に並ぶ配置のため`SectionHeading`コンポーネントは使わず専用マークアップ（`.benefit-heading__title`/`.benefit-heading__subtitle`）を実装。PCでの罫線の右伸長のみ`.cutting`等と同じ考え方を流用。**トップページの見出しと同じスクロールフェードイン挙動にするため、ラッパーの`.benefit-heading`に`data-reveal`を付与**（`SectionHeading.astro`をコンポーネントとして使ってはいないが、`data-reveal`＋`.is-visible`はBaseLayout.astroのグローバルCSS/スクリプトなので、クラス名がsection-headingでなくても同じ挙動になる）
- 商品名見出し「断熱マルチポーチ / Insulated Multi-Pouch」は`SectionHeading`（`subtitle`指定でh2相当28pxクラスが自動付与される）をそのまま使用
- 遷移元：トップページのnewsセクション1件目・フッターリンク「pre-order benefit」の`href`を`${base}/benefit/`に設定済み（`Footer.astro`の`links`配列をラベルのみの配列から`{label, href}`の配列に変更）
- Section B2の写真（`.benefit-intro__visual` > `.benefit-intro__photo`）は、当初design.md 3.3の「写真スタック（2枚重ね）演出」を適用していたが撤回済み（3章B2参照）。現在は単一の`<img>`を`.benefit-intro`セクションの外側に配置し、左をコンテンツ幅基準線、右を画面の真の端に揃えるフルブリード（片側だけ）方式

### 共通コンポーネントの再利用状況（Purchase系CTA・Back to Top）

- **Back to Top**：**非表示**。「下層ページにはBack to TopのCTAは設置しない」というユーザー指示により、`<BaseLayout showBackToTop={false} ...>`を指定して`Footer.astro`側で`<button id="back-to-top">`自体をレンダリングしないようにした（design.md 4章参照。以前はそのまま表示していたが撤回）。
- **Purchase系CTA**：**このページでは未使用**。B4（商品詳細）はプレゼント品のため購入導線が存在せず、Purchase系CTA（`rgba(76,66,90,0.8)`）は登場しない。
- **close CTA（新規）**：`.btn-circle`（design.md 3.4の共通circle基盤）は流用しつつ、背景色のみ`rgba(76, 55, 100, 0.7)`という**このページ専用の3つ目のトークン**を新規追加した。Purchase・Back to Topいずれとも異なる配色にしたのは、「購入」でも「先頭に戻る」でもない第三の意味（ページを閉じてトップへ戻る）を持つボタンのため、既存2トークンの使い回しを避けた判断。

---

## 6. 次セッションTODO（優先度順）

1. **スペック値の確定**：size/fabric/colorがカンプ転記のままか、確定情報かの最終チェック（4章1参照）。
2. **残りのリンク遷移先確定**：ヘッダーロゴ・SNSアイコン等、`/benefit/`以外のリンクの実際の遷移先はtop.md側の課題と共通（top.md 7章参照）。
