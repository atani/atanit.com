---
post_title: 【もう忘れない】ギガファイル便の保存期間を自動設定するChrome拡張機能を作った
post_status: publish
post_excerpt: ギガファイル便の保存期間を毎回変更するのが面倒、うっかり短い期間で送ってしまう...そんな悩みを解決するChrome拡張機能「GigaFile Default Setter」を開発しました。無料で使えます。
post_category:
  - 開発
  - ツール
tags:
  - Chrome拡張機能
  - JavaScript
  - 個人開発
  - 自動化
  - 効率化
  - ギガファイル便
  - GigaFile
  - ファイル転送
---

## 作ったもの

ギガファイル便の保存期間を自動で設定してくれるChrome拡張機能「**GigaFile Default Setter**」を作りました。

[Chrome Web Storeからインストール](https://chromewebstore.google.com/detail/gigafile-default-setter/ekmhmeomjelamfiidhmdnlhfdholcnjp)

[GitHub](https://github.com/atani/gigafile-default-setter)

## 開発のきっかけ

ギガファイル便は無料で使える大容量ファイル転送サービスで、仕事でもプライベートでもよく使っています。

ただ、ひとつ不満がありました。**保存期間のデフォルト値が短い**んです。

ギガファイル便では3日〜100日の保存期間を選べるのですが、デフォルトが短めに設定されているため、毎回手動で長い期間に変更していました。

そして何度かやらかしました。

- 設定を変え忘れて短い期間で送ってしまい、相手がダウンロードする前に消えてしまった
- 「もう一回送って」と言われる気まずさ

この「毎回変更するのが面倒」「うっかり忘れる」という2つの課題を解決するために、Chrome拡張機能を作ることにしました。

## 機能

機能は非常にシンプルです。

1. 拡張機能のポップアップで保存期間のデフォルト値を設定（3日〜100日）
2. ギガファイル便にアクセスすると、自動的にその値が選択される

これだけです。一度設定すれば、あとは何も考えなくていい。

## 技術的なポイント

### Manifest V3

Chrome拡張機能は2024年以降Manifest V3が必須になっています。今回は最初からV3で作成しました。

```json
{
  "manifest_version": 3,
  "name": "GigaFile Default Setter",
  "permissions": ["storage"],
  "host_permissions": ["*://gigafile.nu/*"],
  "content_scripts": [
    {
      "matches": ["*://gigafile.nu/*"],
      "js": ["content/content.js"],
      "run_at": "document_end"
    }
  ]
}
```

### Content Scriptで自動クリック

ギガファイル便のページ構造を解析し、保存期間のボタンを見つけてクリックする処理を実装しました。

```javascript
function findAndClickDaysButton(targetDays) {
  const targetText = `${targetDays}日`;
  const elements = document.querySelectorAll('a, button, span, div, li');

  for (const el of elements) {
    if (el.textContent.trim() === targetText) {
      el.click();
      return true;
    }
  }
  return false;
}
```

### 動的要素への対応

ギガファイル便は一部の要素が動的に読み込まれるため、`MutationObserver`を使って要素の追加を監視しています。

```javascript
const observer = new MutationObserver(() => {
  if (!applied) {
    tryApply();
  }
});

observer.observe(document.body, {
  childList: true,
  subtree: true
});
```

また、タイミングの問題に対応するため、複数回のリトライも入れています。

### chrome.storage.syncで設定を同期

設定値は`chrome.storage.sync`を使って保存しています。これにより、同じGoogleアカウントでログインしているChromeブラウザ間で設定が自動同期されます。

```javascript
// 保存
chrome.storage.sync.set({ defaultDays: value });

// 読み込み
chrome.storage.sync.get({ defaultDays: 100 }, (result) => {
  // result.defaultDaysを使用
});
```

## ファイル構成

```
gigafile-default-setter/
├── manifest.json       # 拡張機能の設定
├── content/
│   └── content.js      # ギガファイル便で実行されるスクリプト
├── popup/
│   ├── popup.html      # 設定画面のUI
│   ├── popup.css       # スタイル
│   └── popup.js        # 設定の保存・読み込み
└── icons/              # 各サイズのアイコン
```

全体で100行程度の小さな拡張機能です。

## 使い方

1. [Chrome Web Store](https://chromewebstore.google.com/detail/gigafile-default-setter/ekmhmeomjelamfiidhmdnlhfdholcnjp)からインストール
2. ツールバーの拡張機能アイコンをクリック
3. 保存期間のデフォルト値を選択（3日〜100日）

あとはギガファイル便にアクセスするだけで、設定した保存期間が自動選択されます。

## まとめ

「毎回同じ操作をしている」「うっかり忘れてしまう」という小さなストレスは、ちょっとしたツールで解消できます。

Chrome拡張機能はManifest V3になってもそこまで難しくなく、このくらいのシンプルな機能であれば数時間で作れます。

同じ悩みを持っている方はぜひ使ってみてください。

**Chrome Web Store**: https://chromewebstore.google.com/detail/gigafile-default-setter/ekmhmeomjelamfiidhmdnlhfdholcnjp

**GitHub**: https://github.com/atani/gigafile-default-setter
