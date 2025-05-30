# TechFeed — README

最新技術のナレッジを **Markdown ファイル**で追加するだけで Web サイトに自動反映される “TechFeed” のセットアップ手順です。  
ロリポップ！レンタルサーバー（PHP 8.1 以上）で動作し、記事メタデータは **JSON** で管理します。

---

## 1. 仕組み概要

| レイヤ       | 役割                                                                                  |
| ------------ | ------------------------------------------------------------------------------------- |
| **Markdown** | `/content/*.md` に配置。YAML フロントマター（タイトル・日付・タグ等）を記述。         |
| **PHP**      | `index.php` が Parsedown で MD → HTML 変換。`ArticleRepository.php` が JSON を CRUD。 |
| **JSON**     | `/data/articles.json` に記事のメタ情報をキャッシュ。ビルドレスで高速表示。            |
| **フロント** | Tailwind CSS + Alpine.js。サーバーレスで軽量。テーマ調整は `/assets/css` で可能。     |

---

## 2. フォルダー構成

```

techfeed/
├── content/               # Markdown 記事を置く場所
│   └── 2025-05-25-sample.md
├── data/
│   └── articles.json      # 自動生成されるメタデータ
├── public/
│   ├── assets/            # 画像や CSS, JS
│   └── uploads/           # アップロード済み画像（任意）
├── src/
│   ├── Parsedown.php      # MD パーサー (Composer で取得)
│   ├── Article.php        # ドメインモデル
│   ├── ArticleRepository.php
│   └── helpers.php
├── index.php              # 記事一覧・ルーティング
├── article.php            # 個別記事ページ
└── composer.json

```

---

## 3. 必要ソフトウェア

| ソフト     | バージョン例 | 備考                               |
| ---------- | ------------ | ---------------------------------- |
| PHP        | 8.1 以上     | ロリポップ！ハイスピードプラン推奨 |
| Composer   | 2.x          | 依存関係管理                       |
| Git (任意) | latest       | FTP 配置でも可                     |

---

## 4. ローカル開発 ― 初期セットアップ

```bash
git clone https://github.com/morihoumatu/techfeed.git
cd techfeed
composer install           # Parsedown 等を取得
php -S localhost:8000      # ローカル確認 → http://localhost:8000
```

> **ポイント**
>
> - 初回アクセス時に `data/articles.json` が生成されます。
> - 変更を検知したい場合は `php artisan refresh-json` 相当の自作 CLI（任意）を用意するか、デプロイ後に `?refresh_cache=1` で再生成できます。

---

## 5. 記事追加手順

1. `/content` 配下に Markdown ファイルを置く
   例: `2025-05-25-launch.md`

2. 冒頭に YAML フロントマターを記述

   ```yaml
   ---
   title: "サイト公開のお知らせ"
   date: "2025-05-25"
   tags: ["announcement", "php", "markdown"]
   summary: "TechFeed を公開しました。"
   ---
   ```

3. 本文を Markdown で執筆し、画像は `/public/uploads` に置いて
   `![alt](../public/uploads/diagram.png)` のように参照。

4. コミット & プッシュ → FTP / Git-FTP / GitHub Actions などでロリポップへ反映。

> **自動反映のしくみ**
> デプロイ時に `ArticleRepository.php` が `/content` をスキャン → フロントマターを抽出 → `articles.json` を再生成 → 一覧ページに即反映。

---

## 6. ロリポップ！へのデプロイ

### 6-1. Git 利用（推奨）

1. ロリポップ！マネージド GIT を有効化 → リポジトリ URL を取得
2. `git remote add lolipop <repo-url>`
3. `git push lolipop main`
4. サーバ側の `composer install --no-dev --optimize-autoloader` を自動実行する

   - **設置ディレクトリ**：ドキュメントルート直下 (例 `/techfeed`)
   - `.htaccess` で `RewriteEngine On` → すべて `index.php` へフォールバック

### 6-2. FTP のみ

1. ローカルで `composer install --no-dev`
2. `techfeed` フォルダーごとアップロード
3. ブラウザで `/techfeed/?refresh_cache=1` にアクセスして JSON 再生成

---

## 7. JSON スキーマ

```jsonc
[
  {
    "slug": "2025-05-25-launch",
    "title": "サイト公開のお知らせ",
    "date": "2025-05-25",
    "tags": ["announcement", "php", "markdown"],
    "summary": "TechFeed を公開しました。"
  },
  ...
]
```

- `slug` はファイル名から自動生成
- 一覧ページは公開日降順で表示

---

## 8. カスタマイズ

| 目的                     | 方法                                           |
| ------------------------ | ---------------------------------------------- |
| デザイン変更             | `/public/assets/css/tailwind.css` を編集       |
| ページ追加（About など） | `/pages/about.md` を用意＋`PageController.php` |
| OG タグ・SEO 最適化      | `/src/helpers.php` の `htmlHead()` を修正      |
| キャッシュ TTL 変更      | `ArticleRepository::$cacheTtl` (秒) を調整     |

---

## 9. ライセンス

MIT License. 詳細は `LICENSE` を参照してください。

---

## 10. クイックスタート TL;DR

```bash
# 1. リポジトリを取得
git clone <this-repo>
cd techfeed && composer install

# 2. 記事を書く
nano content/2025-05-25-my-first-post.md

# 3. サーバーへデプロイ
git push lolipop main         # or FTP

# 4. 反映確認
open https://<your-domain>/techfeed/
```

Happy Coding! 🚀
