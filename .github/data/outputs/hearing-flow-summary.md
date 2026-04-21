# AIリード型ヒアリングフロー まとめ

## 概要

インフラ作業依頼（AAP / postfix / メール送信検証）を例に、
AIがヒアリングをリードして設計まで進めた流れを整理する。

---

## 全体の流れ

```
STEP 1  作業内容の把握
STEP 2  入力値の洗い出しと検証
STEP 3  手順の明確化
STEP 4  受け入れ基準の定義
  ↓
STEP 5  AAP構成の設計
STEP 6  Playbook設計
STEP 7  AAP登録設定の整理
```

---

## STEP 1 — 作業内容の把握

**AIが確認したこと（1問1答）**

| # | 質問 | 得られた回答 |
|---|---|---|
| 1 | 何をしたいか（目的・ゴール） | postfixを予備機に切り替え、確認し、メール送信を検証する |
| 2 | 実行タイミング | 障害発生時に手動で起動 |
| 3 | 期限・優先度 | 今すぐ必要 |

**ポイント:** 「丸投げ」でも受け付け、一問一答でゴールを引き出した。

---

## STEP 2 — 入力値の洗い出しと検証

**AIが判断して確認した入力値**

| 確認した値 | 内容 | 判明した事実 |
|---|---|---|
| 設定ファイルパス | `/etc/postfix/transport` | ✅ 確認済み |
| 本番機・予備機ファイル | `transport_honban` / `transport_yobi` | 別ファイルで管理 |
| 切り替えコマンド | `postmap` → `postfix reload` | ✅ 確認済み |
| テスト送信先 | `hogehoge@gmail.com` | ✅ 確認済み |
| テスト送信元（5件） | `info@aozora.com` など5アドレス | ✅ 確認済み |
| 件名フォーマット | `セルフメニュー送信 YYYYMMDDhhmm` | ✅ 確認済み |

**ポイント:** 「メールアドレスだけ聞く」のではなく、作業内容から必要な値をAIが判断して確認した。

---

## STEP 3 — 手順の明確化

**AIが引き出した情報**

- 「予備機用ファイルをコピー」という依頼の説明が曖昧だったが、ヒアリングで実態が判明した
  - **依頼者の説明:** 予備機用設定ファイルをコピーして切り替える
  - **実態:** `transport_honban` / `transport_yobi` という別ファイルが存在し、コピーで切り替える

**ポイント:** 依頼者が「当然知っているだろう」と思い込んでいる前提を丁寧に引き出した。

---

## STEP 4 — 受け入れ基準の定義

| 項目 | 内容 |
|---|---|
| 自動化できる範囲 | transportファイルのコピー → postmap → reload → ファイル確認 → メール送信 |
| 自動化できない範囲 | メールの受信確認（目視） |
| 成功判定 | 受信トレイにテストメールが届いていること（オペレーターが確認） |

**ポイント:** 「何をもって完了とするか」を合意し、自動化の境界を明確にした。

---

## STEP 5 — AAP構成の設計

**AIが提案し、確認した内容（1問1答）**

| 質問 | 決定内容 |
|---|---|
| WTは1つか複数か | 3つ（切り替え・確認・メール送信）に独立分割 |
| WTの実行方法 | 独立して手動実行 |
| JTの分け方 | メイン処理 + 成功通知 + 失敗通知 |
| 通知方法 | Teams（Webhook） |
| 通知JTは共通か専用か | 各WTに専用JTを作成 |
| 成功・失敗は別JTか | 別JTに分ける |

**確定した構成**

```
WT1: PRD_SV\WF_POSTFIX_SWITCH
  └─ JT: PRD_SV_POSTFIX_SWITCH
       ├─ success → JT: PRD_SV_POSTFIX_SWITCH_NOTIFY_SUCCESS
       └─ failure → JT: PRD_SV_POSTFIX_SWITCH_NOTIFY_FAILURE

WT2: PRD_SV\WF_POSTFIX_VERIFY
  └─ JT: PRD_SV_POSTFIX_VERIFY
       ├─ success → JT: PRD_SV_POSTFIX_VERIFY_NOTIFY_SUCCESS
       └─ failure → JT: PRD_SV_POSTFIX_VERIFY_NOTIFY_FAILURE

WT3: PRD_SV\WF_TESTMAIL_SEND
  └─ JT: PRD_SV_TESTMAIL_SEND
       ├─ success → JT: PRD_SV_TESTMAIL_SEND_NOTIFY_SUCCESS
       └─ failure → JT: PRD_SV_TESTMAIL_SEND_NOTIFY_FAILURE
```

---

## STEP 6 — Playbook設計

**AIが確認した内容（1問1答）**

| 質問 | 決定内容 |
|---|---|
| ファイル名の規則 | `<動詞>_<目的語>.yml`（yaml不可） |
| Roleの区分 | 汎用 → `rhel_common` / 特化 → 処理名ベース |
| 汎用Roleに入れる処理 | Teams通知のみ |
| postfix関連Roleの統合 | 3つのPlaybookを同一の`postfix`ロールに統合 |
| 変数ファイルの有無 | なし（すべてAAP Extra Variablesで渡す） |

**確定したファイル構成**

```
switch_postfix.yml          → roles/postfix/tasks/switch_transport.yml
verify_postfix.yml          → roles/postfix/tasks/verify_transport.yml
send_testmail.yml           → roles/postfix/tasks/send_testmail.yml
notify_teams.yml            → roles/rhel_common/tasks/notify_teams.yml
```

---

## STEP 7 — AAP登録設定の整理

**確認した共通設定**

| 項目 | 値 |
|---|---|
| Organization | `PRD_SoR_SV` |
| Project | `maintenance_rhel` |
| Inventory | `dbpr_rhel` |
| Credential | `dev_rhel_cred` |
| Execution Environment | デフォルト |

**命名規則**

| 対象 | プレフィックス | 例 |
|---|---|---|
| ジョブテンプレート | `PRD_SV` | `PRD_SV_POSTFIX_SWITCH` |
| ワークフローテンプレート | `PRD_SV\WF` | `PRD_SV\WF_POSTFIX_SWITCH` |

---

## AIリードの特徴（ふりかえり）

| 工夫 | 効果 |
|---|---|
| 1問1答 | 依頼者が迷わずに答えられた |
| 依頼者を責めない | 「丸投げ」でも整理を引き受けた |
| AIが必要な値を判断して質問 | 依頼者が気づいていない入力値の不足を引き出せた |
| 手順の実態を確認 | 「コピーして切り替え」という説明の裏にある実態を明確化できた |
| 自動化の境界を明示 | 目視確認が必要な箇所を事前に合意できた |
| 設計をフェーズに分けた | AAP構成 → Playbook → 登録設定の順に迷わず進められた |
