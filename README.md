# SENDAI OKTOBERFEST 2026 — 20th Anniversary

仙台オクトーバーフェスト2026（20周年）のスクロール演出付き1ページサイト。
ビルド不要の静的サイトで、**Cloudflare Workers（静的アセット）の GitHub 連携**で公開します。

## 構成

```
wrangler.jsonc      Cloudflare の設定（公開ディレクトリ = public/）
public/             ★ 公開されるのはこの中だけ
  index.html          ページ本体（HTML / CSS / JS すべて内包）
  404.html            Not Found ページ
  _headers            キャッシュ / セキュリティヘッダー
  assets/             ポスター入稿データから書き出した画像（webp）
    positions.json    各モチーフの配置座標（元データ・参照用）
src/                元プロトタイプ（画像を base64 で埋め込んだ単一HTML・参照用／非公開）
README.md           このファイル（非公開）
```

`public/index.html` は、元プロトタイプの base64 埋め込み画像を `assets/` の
webp ファイル参照に置き換えたものです（見た目は同一、HTML は約 1.1MB → 約 17KB）。
画像がブラウザ／CDN にキャッシュされ、初回表示も 2 回目以降も速くなります。

`public/` の外にあるファイル（`README.md`、`src/`、`wrangler.jsonc`）は
配信対象に含まれないため、URL を直接叩いても 404 になります。

## ローカルで確認する

本番と同じ挙動（`_headers` や 404 ページの扱いを含む）で確認する場合：

```bash
npx wrangler dev      # → http://localhost:8787/
```

HTML の見た目だけ確認したい場合は、`public/` で簡易サーバを立てても構いません。

```bash
cd public && python3 -m http.server 8000
```

## Cloudflare での公開（GitHub 連携）

Cloudflare ダッシュボード → **Workers & Pages** → 対象プロジェクト →
**Settings** → **Build** で、GitHub リポジトリ `lomogoo/okf20th` を接続します。

| 項目 | 値 |
| --- | --- |
| Production branch | `main` |
| Build command | （空欄。ビルド不要） |
| Deploy command | `npx wrangler deploy` |

ビルド時に `wrangler.jsonc` が読まれ、`public/` の中身がそのままアップロードされます。
以降、`main` に push するたびに自動でデプロイされます。

> **注意:** `wrangler.jsonc` の `"name"` は、Cloudflare 側の Worker 名と
> 一致している必要があります。プロジェクト名が `okf20th` 以外の場合は、
> `"name"` をその名前に書き換えてください。

### 独自ドメインを使う場合

プロジェクトの **Settings** → **Domains & Routes** → **Add** からドメインを追加します。
ドメインが Cloudflare で管理されていれば DNS は自動設定され、
外部管理の場合は表示される CNAME を DNS に登録します。

独自ドメインを設定したら、SNS でのカード表示のために `public/index.html` の
`og:image` を絶対URL（例: `https://example.com/assets/title.webp`）に
書き換えてください。

## 注意

- フォントは Google Fonts（Comfortaa / Zen Kaku Gothic New）を CDN から読み込みます。
