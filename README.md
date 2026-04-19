# インフラ作業支援リポジトリ

## ディレクトリ構造

```
project-root/
│
├── .github/
│   └── copilot-instructions.md   # Copilotエージェントの指示定義
│                                  # ・ヒアリング→理解確認→アウトプットの3フェーズ
│                                  # ・拡張機能が未インストール時のフォールバック案内
│                                  # ・図の種類の自動判断ルール
│
├── .vscode/
│   └── extensions.json           # 推奨拡張機能の定義
│                                  # リポジトリを開いた際に自動でインストール通知が出る
│                                  # ・Mermaid Chart（図生成・@mermaid-chart）
│                                  # ・Draw.io Integration（Confluence連携）
│                                  # ・Excalidraw（フリーハンド描画）
│                                  # ・Markmap（マインドマップ）
│                                  # ・GitHub Copilot / Copilot Chat
│
└── README.md                     # このファイル
```

## 使い方

### 1. リポジトリをVSCodeで開く
推奨拡張機能のインストール通知が表示されます。
「インストール」を選択してすべて入れてください。

### 2. Copilot Chatを開く
`Ctrl+Alt+I` でCopilot Chatを開きます。

### 3. 作業内容を話しかける
エージェントが自動的にヒアリングを開始します。

```
例:
「postfixにドメインを追加する作業のレビュー準備を手伝って」
「この設定ファイルの変更内容を図にして」
「squidの設定を変えたんだけど影響範囲を確認したい」
```

### 4. Confluenceに貼る
フェーズ3で生成されたMarkdownをそのままConfluenceに貼れます。

---

## 図作成フロー

```
Excalidrawでフリーハンドに描く
        ↓
スクリーンショットをCopilot Chatに貼る
        ↓
「Mermaid図にして」と依頼
        ↓
@mermaid-chartでプレビュー確認
        ↓
Confluenceに貼る
```
