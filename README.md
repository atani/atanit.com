# atanit.com Blog Content

個人ブログ [atanit.com](https://atanit.com) の記事を管理するリポジトリです。

[Git it Write](https://wordpress.org/plugins/git-it-write/) プラグインを使用して、GitHubからWordPressへ自動同期します。

## ディレクトリ構造

```
atanit.com/
├── posts/           # ブログ記事（Markdown）
│   └── *.md
└── README.md
```

## 記事のフォーマット

各記事はMarkdown形式で、以下のfrontmatterを使用します：

```markdown
---
post_title: 記事のタイトル
post_status: draft または publish
post_excerpt: 記事の抜粋（メタディスクリプション）
post_category:
  - カテゴリ名
tags:
  - タグ1
  - タグ2
---

記事本文...
```

### frontmatterの説明

| フィールド | 説明 | 必須 |
|-----------|------|------|
| `post_title` | 記事のタイトル | Yes |
| `post_status` | `draft`（下書き）または `publish`（公開） | Yes |
| `post_excerpt` | 記事の抜粋・説明文 | No |
| `post_category` | カテゴリ（配列形式） | No |
| `tags` | タグ（配列形式） | No |

## WordPress側のセットアップ

### 1. プラグインのインストール

1. WordPress管理画面 → プラグイン → 新規追加
2. 「Git it Write」で検索
3. インストール → 有効化

### 2. リポジトリの連携

1. 設定 → Git it Write
2. 「Add a new repository」をクリック
3. 以下を設定：
   - **Repository URL**: `https://github.com/atani/atanit.com`
   - **Branch**: `main`
   - **Folder in repository**: `posts`
   - **Post type**: `post`（または使用する投稿タイプ）
   - **Post status**: `Use front matter`
4. 保存

### 3. Webhookの設定（自動同期）

GitHubにプッシュしたら自動で同期させるには：

1. Git it Writeの設定画面でWebhook URLをコピー
2. GitHubリポジトリ → Settings → Webhooks → Add webhook
3. Payload URLに上記URLを貼り付け
4. Content typeは `application/json`
5. 「Just the push event」を選択
6. 保存

## 記事の追加・更新

1. `posts/` ディレクトリにMarkdownファイルを作成
2. frontmatterを記述
3. 記事本文を書く
4. コミット & プッシュ
5. WordPressに自動同期（Webhook設定済みの場合）

手動同期する場合は、WordPress管理画面のGit it Write設定から「Pull the posts」をクリック。

## ライセンス

記事の内容は著作権で保護されています。

---
Last updated: 2026-01-01
