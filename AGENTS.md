# R VEIL "UNISON" LP — プロジェクト引き継ぎメモ

新しいセッションで作業を始める前に、このファイル・`design.md`・触るページに応じた`top.md`（トップページ）/`benefit.md`（ご予約特典ページ）/`qa.md`（Q&Aページ）/`payment.md`（お支払い方法ページ）を必ず読むこと。

## 1. プロジェクト概要

- **サイト**：ブランド「R VEIL」の商品コレクション「UNISON」を紹介する縦長1カラムのランディングページ（予約販売の告知フェーズ）。
- **商材**：撥水加工ドッグコート（愛犬用）＋ リバーシブル・ハンズフリーバッグ（飼い主用）。
- **ターゲット**：愛犬と過ごす時間を大切にする、上質・ミニマルなデザインを好む層。トーンはダーク・モノトーン・タクティカル・高級感。
- **公開先**：GitHub Pages（`https://nakahara2026.github.io/r-veil-lp/`）。`main`へのpushでGitHub Actionsが自動ビルド・公開。
- **現状のフェーズ**：トップページ・下層ページ3種（ご予約特典／Q&A／お支払い方法）とも実装が完了し、**サイトは概ね完成**。次のフェーズは**GitHub Pagesからレンタルサーバー（FTP）への本番移行**（6章参照）。細部の文言・リンク遷移先の確定など、各`○○.md`の「次セッションTODO」に残る微調整は残っている。
- **設計書の構成**：`design.md`（全ページ共通ルール）と、ページごとの固有ドキュメント（`top.md`＝トップページ、`benefit.md`＝ご予約特典ページ、`qa.md`＝Q&Aページ、`payment.md`＝お支払い方法ページ）に分割済み。**新規に下層ページを作る際は、`design.md`のルールを踏まえたうえで、そのページ専用の`○○.md`（例：`product.md`）を`top.md`・`benefit.md`・`qa.md`・`payment.md`と同じ構成で新規作成すること**。

## 2. 参照すべきファイル

| ファイル | 役割 |
|---|---|
| `design.md` | **最重要**。全ページ共通のブランド背景・コンセプト、デザイントークンの基本ルール、共通コンポーネント仕様、レスポンシブ方針、公開・デプロイ設定をまとめた設計書。どのページを触る場合でも実装前に必ず目を通す |
| `top.md` | トップページ（`index.astro`）固有の構成・文言・セクション別ワイヤーフレーム・アセット状況・次セッションTODOをまとめた設計書。トップページを触る場合は必ず目を通す。作業のたびに更新すること（後述） |
| `benefit.md` | 下層ページ「ご予約特典」（`benefit.astro`）固有の構成・文言・実装方針・未確定事項・次セッションTODOをまとめた設計書。このページを触る場合は必ず目を通す。作業のたびに更新すること（後述） |
| `qa.md` | 下層ページ「Q&A」（`qa.astro`）固有の構成・データ構造・実装方針・未確定事項・次セッションTODOをまとめた設計書。このページを触る場合は必ず目を通す。作業のたびに更新すること（後述） |
| `payment.md` | 下層ページ「お支払い方法」（`payment.astro`）固有の構成・実装方針・未確定事項・次セッションTODOをまとめた設計書。このページを触る場合は必ず目を通す。作業のたびに更新すること（後述） |
| `top.png`（プロジェクトルート） | トップページのデザインカンプの実物（フルページスクリーンショット）。**gitignore対象、リポジトリには含まれない**のでローカルにのみ存在。Pythonの`PIL`でクロップ＋グリッド重ねをして実測するのが基本ワークフロー（design.md 6.2参照）。新規セクション実装・レイアウトの疑義がある場合は必ずこの画像を実測してから着手する（目測で実装すると手戻りが発生しやすい） |
| `benefit.png`（プロジェクトルート） | ご予約特典ページのデザインカンプの実物。`top.png`と同様に**gitignore対象**（`public/images/benefit.png`という同名の実サイトアセットとは別物なので混同注意）。実測ワークフローは`top.png`と同じ |
| `Q&A.png` / `Q&A_アコーディオン開.png`（プロジェクトルート） | Q&Aページのデザインカンプの実物（それぞれ閉じた状態・開いた状態）。`top.png`と同様に**gitignore対象**。実測ワークフローは`top.png`と同じ |
| `AGENTS.md`（＝`CLAUDE.md`のシンボリックリンク先） | このファイル。プロジェクトの技術的な引き継ぎメモ。`CLAUDE.md`を編集する場合は実体である`AGENTS.md`を編集すること |
| `README.md` | Astroテンプレートの標準READMEで、プロジェクト固有の情報は薄い。参照優先度は低い |

各ページの「次セッションTODO」章（`top.md`7章／`benefit.md`6章／`qa.md`6章／`payment.md`6章）に、直近の未完了タスクが優先度順でまとめてある。作業を始める際はまずそこを確認する。

## 3. 技術スタック・構成

- **フレームワーク**：[Astro](https://docs.astro.build)（TypeScriptなし、`.astro`ファイルのみ）。静的サイト生成（`output: "static"`）。
- **パッケージマネージャ**：npm。Node.js `v24.18.0`を`nvm`経由で使用（`export NVM_DIR="$HOME/.nvm" && source "$NVM_DIR/nvm.sh" && nvm use v24.18.0`をターミナルセッションごとに実行する必要がある）。
- **フォルダ構成**：
  ```
  src/
    pages/
      index.astro            … トップページ本体（全セクションのHTML+CSS+JS、単一ファイルに集約）
      benefit.astro           … 下層ページ「ご予約特典」本体
      qa.astro                … 下層ページ「Q&A」本体（2階層アコーディオン、JSデータ駆動でHTML生成）
      payment.astro           … 下層ページ「お支払い方法」本体
    layouts/BaseLayout.astro … 共通レイアウト（<head>、グローバルCSS、data-revealのIntersectionObserver、showBackToTopプロパティをFooterへ伝播）
    components/
      Header.astro           … ヘッダー（ロゴ画像、h1）
      Footer.astro            … フッター（リンクリスト、Back To Topボタン。showBackToTopプロパティで表示/非表示を制御）
      SectionHeading.astro    … 見出し+下線罫のコンポーネント（<h2>を出力。titleはset:htmlで描画するためHTML埋め込み可・design.md 4.0参照）
  public/
    images/                 … 全画像アセット（コート/バッグ/ポーチ商品写真、背景写真、アイコン、ロゴ等）
    videos/hero.mp4          … ヒーロー動画
  astro.config.mjs           … site / base（GitHub Pagesのプロジェクトページ用サブパス。レンタルサーバー移行時に要変更、6章参照）
  .github/workflows/deploy.yml … GitHub Actions（push時に自動ビルド・公開。レンタルサーバー移行後は不要になる想定）
  design.md                  … 全ページ共通の設計書
  top.md / benefit.md / qa.md / payment.md … 各ページ固有の設計書
  top.png / benefit.png / Q&A.png / Q&A_アコーディオン開.png … 各ページのデザインカンプ（すべてgitignore対象）
  ```
- **公開設定**：`astro.config.mjs`で`base: '/r-veil-lp'`を指定しているため、**画像・favicon・内部リンクは全て`import.meta.env.BASE_URL`基準の相対パスで参照する**（`const base = import.meta.env.BASE_URL.replace(/\/$/, '')`を各ファイルのfrontmatterで定義し、`` `${base}/images/xxx.png` ``の形で使う）。ルート相対パス（`/images/xxx.png`のような固定文字列）を直接書くと本番で404になる。

## 4. これまでの主要な決定事項・注意点（ハマりやすい罠）

- **画像の`aspect-ratio`は必ず元画像の実寸を確認してから指定する**。実寸と異なる比率を強制すると`object-fit: cover`で意図せず大きくクロップされる（UNISON背景・ビジュアルインターリュード・ヒーロー動画で実際に発生した不具合）。
- **セクション背景色を画面幅いっぱいに広げたい場合**（`.section`のmax-width:1200pxを超えて背景だけ広げたいケース）は、`box-shadow: 0 0 0 100vmax 色;` + `clip-path: inset(0 -100vmax);` の組み合わせを使う。**`clip-path`を忘れるとshadowが上下方向にも広がり、隣接セクションに色が漏れる不具合が起きる**（Size Guide・撥水加工素材で実際に発生）。
- **見出しは全て`<h2>`で統一**（ページ全体のh1はヘッダーロゴのみ）。`SectionHeading.astro`は`<h2>`を出力する。新規セクションでも見出しをh1にしない。
- **devサーバーのHMRが古いキャッシュを保持し、ソースコードを直しても実機に反映されないことがある**（長時間起動したまま多数のコンポーネントファイルを編集した場合に発生しやすい）。反映がおかしいと感じたら以下で再起動して確認する。
  ```
  npx astro dev stop
  npx astro dev --background
  ```
  `npm run build`は毎回フルビルドのためこの問題は起きない。疑わしい時はビルド後のdistで確認するのも有効。
- **デザインカンプの実測は目測でなく必ずPythonで精密に行う**（`top.png`をPIL でクロップし、グリッド線・座標を描画して読み取る）。過去に目測で実装した箇所（画像幅の比率、左右の配置順）で複数回の手戻りが発生した。
- **画像アセットは低解像度のまま支給されることがある**。商品ギャラリーのメイン表示は大きく拡大されるため、新しい画像が届いたら`PIL`で解像度を確認してから差し替え完了とみなすこと（過去にコート/バッグ商品写真で306×306px・153×153pxの低解像度ファイルがそのまま使われ、ブラーが出た事例が複数回あった）。ファイル名の半角スペース混入（`coat- 09.png`など）にも注意。
- **Purchaseボタンと Back To Topボタンは別トークン**（それぞれ`rgba(76,66,90,0.8)`と`#736C7E`）。共通の`--color-accent`を安易に使い回すと両方に影響するため、個別にオーバーライドすること。
- **Astroのスコープ付きCSSは、テンプレートに直接書かれた要素にしか効かない**。`SectionHeading`の`title`を`set:html`で描画する場合や、`<script>`内で`document.createElement`によりクライアントサイドで動的生成した要素（Q&Aページのアコーディオン等）には`data-astro-cid-*`が付与されず、通常のスコープ付き`<style>`のルールが一切マッチしない。対処は`:global()`で個別に囲むか、データ駆動でHTML生成するページなら`<style is:global>`でページ全体をグローバル化する（design.md 4.0参照）。
- **見出し罫線の画面端まで伸ばすテクニックは、親要素が`display:flex`だと効かない**。罫線もflexアイテムとして扱われ、既定の`flex-shrink:1`で`calc(100% + Npx)`の拡張分がコンテナ幅に収まるよう縮小されてしまう。対象要素に`flex-shrink:0`を追加すること（design.md 3.3参照）。
- **下層ページはBack to Topを表示しない運用**。`BaseLayout`の`showBackToTop`プロパティ（既定`true`）を`false`にすると`Footer.astro`が`<button id="back-to-top">`をレンダリングしない。新規に下層ページを作る際は`<BaseLayout showBackToTop={false} ...>`を指定すること（トップページのみ既定のtrueのまま）。

## 5. 作業を始める際に必ず確認してほしいこと

1. `design.md`を通読する（特にセクション2「ブランド背景・コンセプト」）。触るページに応じて`top.md`/`benefit.md`/`qa.md`/`payment.md`も通読する（それぞれの「次セッションTODO」章）。
2. `git status`で未commit/未pushの変更がないか確認する（このプロジェクトはユーザーの明示的な指示があるまでcommit/pushしない運用なので、前回セッションの変更が残っている可能性がある）。
3. 新しいセクションを実装する・既存のレイアウトを修正する場合は、対象ページのデザインカンプ（`top.png`/`benefit.png`/`Q&A.png`等）を該当箇所だけ精密にクロップ・実測してから着手する（目測で進めない。`payment.astro`はデザインカンプなし、テキスト指示書のみで実装済み）。
4. 画像アセットが新規に届いた場合は、解像度・ファイル名（半角スペース等の誤字）を確認する。
5. ブラウザで確認した内容がコードと合わない場合は、devサーバーを再起動してから再確認する（4章参照）。
6. 作業が一区切りついたら、変更内容に応じて`design.md`（全ページ共通ルールの変更）・触ったページの`○○.md`を実装内容に合わせて更新する（実装とドキュメントを常に同期させる運用）。新たに下層ページを作成した場合は、そのページ専用の`○○.md`も同様に作成・更新する。

---

## 6. 本番移行（レンタルサーバー / FTP）について

サイトが概ね完成したため、次のフェーズとして**GitHub Pagesからレンタルサーバーへの本番移行**を予定している。ユーザーからFTPサーバーの接続情報を口頭で共有されたことがあるが、**サーバーホスト名・FTP ID等の具体的な接続情報はこのファイル（公開GitHubリポジトリにpushされる）には記載しない**（セキュリティ上の理由。パスワードは当然ながら一切共有・保存しない）。次セッションで本番移行作業を再開する場合は、ユーザーに接続情報を再度確認すること。

### 移行に必要な作業（判明している範囲）

1. **ドメインのDNS設定**：本番ドメインのネームサーバー/Aレコードが、契約したレンタルサーバーを指すように設定されている必要がある（ドメイン管理画面側の作業、ユーザー本人が行う）。
2. **サーバーパネル側のドメイン追加**：レンタルサーバーの管理画面で本番ドメインを追加し、専用の公開フォルダ（例：`/ドメイン名/public_html/`）を作成する（ユーザー本人が行う）。
3. **SSL証明書の発行**：`https://`でアクセスできるよう、サーバーパネルからSSL（Let's Encrypt等）を発行する（ユーザー本人が行う）。
4. **`astro.config.mjs`の変更**：GitHub Pagesのサブパス公開用に設定している`site`/`base`を、本番ドメインのルート公開用に変更する。
   ```js
   export default defineConfig({
     site: 'https://本番ドメイン',
     base: '/', // または未指定
   });
   ```
   コード側は全て`import.meta.env.BASE_URL`基準の相対パスで書かれているため、この1箇所を変更するだけで全ページに反映される（3章「公開設定」参照）。
5. **ビルド＆アップロード**：`npm run build`でローカルに生成される`dist/`フォルダの**中身**（`dist/`直下の`index.html`・`_astro/`等）をFTPで、上記2で作成した公開フォルダにアップロードする。`src/`や`node_modules/`、`.astro`ソースファイルはアップロード不要。
6. **運用面の変更**：移行後は`.github/workflows/deploy.yml`（GitHub Pages向け）は使わなくなる。Gitでのソース管理自体は継続してよい。毎回`npm run build`→FTPアップロードを手動で行うか、FTPアップロードを自動化する別のGitHub Action（例：`SamKirkland/FTP-Deploy-Action`）に置き換えるかは要検討。

### Claudeが対応できる範囲

- ローカルでの`npm run build`実行、アップロードすべきファイルの説明、`astro.config.mjs`の変更はClaudeが対応可能。
- FTPの実アップロード操作、DNS設定、サーバーパネルでのドメイン追加・SSL発行は、**ユーザー本人が行う必要がある**（認証情報の入力を伴うため、また現状Claude Codeの利用可能ツールにFTPクライアントが含まれていないため）。

---

## Development

When starting the dev server, use background mode:

```
astro dev --background
```

Manage the background server with `astro dev stop`, `astro dev status`, and `astro dev logs`.

## Documentation

Full documentation: https://docs.astro.build

Consult these guides before working on related tasks:

- [Adding pages, dynamic routes, or middleware](https://docs.astro.build/en/guides/routing/)
- [Working with Astro components](https://docs.astro.build/en/basics/astro-components/)
- [Using React, Vue, Svelte, or other framework components](https://docs.astro.build/en/guides/framework-components/)
- [Adding or managing content](https://docs.astro.build/en/guides/content-collections/)
- [Adding styles or using Tailwind](https://docs.astro.build/en/guides/styling/)
- [Supporting multiple languages](https://docs.astro.build/en/guides/internationalization/)
