# SENDAI OKTOBERFEST 2026 — 20th Anniversary

仙台オクトーバーフェスト2026（20周年）のスクロール演出付き1ページサイト。
ビルド不要の静的サイトで、**Cloudflare Pages の GitHub 連携**で公開します。

## 構成

```
index.html          公開ページ本体（HTML / CSS / JS すべて内包）
404.html            Not Found ページ（Cloudflare Pages が自動で使用）
_headers            Cloudflare Pages 用のキャッシュ / セキュリティヘッダー
assets/             ポスター入稿データから書き出した画像（webp）
  positions.json    各モチーフの配置座標（元データ・参照用）
src/                元プロトタイプ（画像を base64 で埋め込んだ単一HTML・参照用）
```

`index.html` は、元プロトタイプの base64 埋め込み画像を `assets/` の
webp ファイル参照に置き換えたものです（見た目は同一、HTML は約 1.1MB → 約 17KB）。
画像がブラウザ／CDN にキャッシュされ、初回表示も 2 回目以降も速くなります。

## ローカルで確認する

`file://` で直接開いても動きますが、実際の配信に近い形で確認する場合：

```bash
python3 -m http.server 8000
# → http://localhost:8000/
```

## Cloudflare Pages で公開する（GitHub 連携）

1. Cloudflare ダッシュボード → **Workers & Pages** → **Create** → **Pages** →
   **Connect to Git** を選択。
2. GitHub アカウントを認可し、リポジトリ **`lomogoo/okf20th`** を選択。
3. ビルド設定を以下のとおり入力する（**ビルドは不要**）:

   | 項目 | 値 |
   | --- | --- |
   | Framework preset | `None` |
   | Build command | （空欄のまま） |
   | Build output directory | `/`（リポジトリのルート） |
   | Root directory | （空欄のまま） |

4. **Save and Deploy** を押すと `https://<project-name>.pages.dev` で公開されます。
5. 以降、対象ブランチに push するたびに自動でデプロイされます。
   本番ブランチ以外への push は、確認用のプレビューURLが自動発行されます。

### 独自ドメインを使う場合

プロジェクトの **Custom domains** → **Set up a domain** からドメインを追加します。
ドメインが Cloudflare で管理されていれば DNS は自動設定され、
外部管理の場合は表示される CNAME を DNS に登録します。

独自ドメインを設定したら、SNS でのカード表示のために `index.html` の
`og:image` / `og:url` を絶対URL（例: `https://example.com/assets/title.webp`）に
書き換えてください。

## 注意

- ページ下部に固定表示されている「PROTOTYPE — ポスター入稿データ（.ai）から自動生成」の
  帯は元データのまま残しています。本公開時は `index.html` の
  `<div class="proto">…</div>` を削除してください。
- フォントは Google Fonts（Comfortaa / Zen Kaku Gothic New）を CDN から読み込みます。
