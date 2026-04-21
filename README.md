# インフラ作業支援リポジトリ

## ディレクトリ構造

```
project-root/
│
├── .github/
│   ├── copilot-instructions.md        # Copilotエージェントの指示定義
│   ├── agents/
│   │   ├── interviewer.agent.md       # 図作成ヒアリングエージェント
│   │   ├── diagram-drawer.agent.md    # 図作成エージェント
│   │   └── operation-intake.agent.md  # 作業依頼ヒアリング・設計エージェント
│   ├── instructions/
│   │   ├── interviewer.instructions.md
│   │   ├── diagram-drawer.instructions.md
│   │   └── operation-intake.instructions.md
│   └── data/
│       ├── library/                   # Excalidrawライブラリ
│       └── outputs/                   # 生成された図・ドキュメント
│
├── .vscode/
│   └── extensions.json               # 推奨拡張機能の定義
│                                      # ・Mermaid Chart（図生成）
│                                      # ・Draw.io Integration（Confluence連携）
│                                      # ・Excalidraw（フリーハンド描画）
│                                      # ・Markmap（マインドマップ）
│                                      # ・GitHub Copilot / Copilot Chat
│
└── README.md                         # このファイル
```

---

## エージェント一覧

| エージェント | ファイル | 用途 |
|---|---|---|
| 図作成ヒアリング | `agents/interviewer.agent.md` | 図にしたい内容を引き出し、図作成エージェントに渡す |
| 図作成 | `agents/diagram-drawer.agent.md` | ヒアリング結果をもとにMermaid・Excalidraw等の図を生成する |
| 作業依頼ヒアリング・設計 | `agents/operation-intake.agent.md` | インフラ作業依頼を整理し、AAP/Playbook設計まで進める |

---

## 使い方

### 1. リポジトリをVSCodeで開く
推奨拡張機能のインストール通知が表示されます。
「インストール」を選択してすべて入れてください。

### 2. Copilot Chatを開く
`Ctrl+Alt+I` でCopilot Chatを開きます。

### 3. 作業内容を話しかける
エージェントが自動的にヒアリングを開始します。

```
例（図作成）:
「postfixにドメインを追加する作業のレビュー準備を手伝って」
「この設定ファイルの変更内容を図にして」
「squidの設定を変えたんだけど影響範囲を確認したい」

例（作業依頼ヒアリング・設計）:
「作業依頼のヒアリングを手伝って」
「postfixの設定変更を頼まれたんだけど整理したい」
「依頼が来たけど情報が曖昧で困っている」
```

### 4. Confluenceに貼る
生成されたMarkdownをそのままConfluenceに貼れます。

---

## 作業依頼ヒアリング・設計フロー

作業依頼を受けた際に、AIがリードして整理・設計まで進めるフローです。

```
フェーズ1  作業内容の把握
           （目的・対象システム・トリガー・期限）
    ↓
フェーズ2  入力値の洗い出しと検証
           （AIが作業内容から必要な値を判断して確認）
    ↓
フェーズ3  手順の明確化
           （手動 / 自動化済み / Ansible化の検討）
    ↓
フェーズ4  受け入れ基準の定義
           （完了の定義・確認者・部分失敗の扱い）
    ↓
フェーズ5  自動化設計（Ansible / AAPが対象の場合）
           AAP構成   → WTとJTの分け方・通知の設計
           Playbook  → ファイル名・Role・変数の扱い
           AAP登録   → Organization・Project・Inventory・命名規則
    ↓
  出力
  ・作業チケットドラフト（Markdown）
  ・シーケンス図 / フロー図（Excalidraw）
  ・AAP登録設定一覧（Markdown）
```

### 各フェーズのポイント

| フェーズ | AIの動き |
|---|---|
| フェーズ1〜4 | 1問1答で情報を引き出す。「丸投げ」でも受け付ける |
| フェーズ2 | 作業内容からAIが必要な値を判断して質問する（依頼者任せにしない） |
| フェーズ3 | 手順の実態（手動か自動か）を確認し、思い込みを排除する |
| フェーズ4 | 自動化できる範囲と手動が残る範囲を明確に分ける |
| フェーズ5 | AAP構成 → Playbook → 登録設定の順に1問1答で決めていく |

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
