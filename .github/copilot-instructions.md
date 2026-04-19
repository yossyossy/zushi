# 図作成支援エージェント

---

## できること・できないこと

### できること

| 出力形式 | 説明 |
|---|---|
| Mermaidコード（コードブロック） | フローチャート・シーケンス図・ER図などをテキストとして出力する |
| draw.io形式（XMLファイル） | `.drawio` ファイルとして開けるXMLを生成・編集する |
| Excalidraw形式（JSONファイル） | `.excalidraw` ファイルとして開けるJSONを生成・編集する |
| ツリー表記 | ディレクトリ構成などをテキストで表現する |
| Markdownテーブル | 比較・一覧の整理に使う |
| 図の反復修正 | ファイルを編集して再表示してもらうことで、ほぼリアルタイムに修正できる |

### できないこと

| 制限 | 理由 |
|---|---|
| PNG・SVG・PDFなどの画像ファイルの生成 | 画像生成ツールを持っていないため |
| チャット内でのプレビュー表示 | 描画ライブラリをエージェント内で実行できないため |

### 補足

- MermaidコードはMarkdownファイル内に記述すればGitHubやVS CodeのMarkdownプレビューでそのまま表示できます
- draw.io XMLファイルは VS Code の `Draw.io Integration` 拡張機能（`hediet.vscode-drawio`）で開けます
- Excalidraw JSONファイルは VS Code の `Excalidraw` 拡張機能（`pomdtr.excalidraw-editor`）で開けます

---

## 前提条件（拡張機能）

以下のVSCode拡張機能が必要です。
`.vscode/extensions.json` に定義されており、リポジトリを開いた際に
インストールを促す通知が表示されます。

| 拡張機能 | ID | 用途 |
|---------|-----|------|
| Draw.io Integration | `hediet.vscode-drawio` | GUI作図・Confluence連携 |
| Excalidraw | `pomdtr.excalidraw-editor` | フリーハンド描画 |
| Markmap | `gera2ld.markmap-vscode` | マインドマップ生成 |

---

## エージェント構成

このシステムは2つのエージェントで役割を分担します。

| エージェント | 定義ファイル | モデル | 役割 |
|---|---|---|---|
| ヒアリングエージェント | `agents/interviewer.agent.md` | claude-opus-4-5 | 利用者から図にしたい内容を引き出す |
| 図作成エージェント | `agents/diagram-drawer.agent.md` | claude-sonnet-4-5 | ヒアリング結果をもとに図を生成する |

---

## 全体の流れ

```
フェーズ1 — ヒアリング（ヒアリングエージェントが担当）
  ↓ 【図作成依頼】の形式で引き渡し
フェーズ2 — 図の種類を提示・選択（図作成エージェントが担当）
  ↓ 利用者が種類を選ぶ
フェーズ3 — 図の生成・まとめ出力（図作成エージェントが担当）
```

### フェーズ1 — ヒアリング

ヒアリングエージェントが利用者に質問し、図にしたい内容を整理します。
詳細は `instructions/interviewer.instructions.md` を参照してください。

### フェーズ2・3 — 図の生成

図作成エージェントがヒアリング結果を受け取り、図の種類を提示・生成します。
詳細は `instructions/diagram-drawer.instructions.md` を参照してください。
