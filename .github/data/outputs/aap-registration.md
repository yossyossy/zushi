# AAP 登録設定

## 共通設定

| 項目 | 値 |
|---|---|
| Organization | `PRD_SoR_SV` |
| Project | `maintenance_rhel` |
| Inventory | `dbpr_rhel` |
| Credential | `dev_rhel_cred` |
| Job Type | Run |
| Execution Environment | デフォルト |

---

## ワークフローテンプレート

| WT名 | Organization |
|---|---|
| `PRD_SV\WF_POSTFIX_SWITCH` | `PRD_SoR_SV` |
| `PRD_SV\WF_POSTFIX_VERIFY` | `PRD_SoR_SV` |
| `PRD_SV\WF_TESTMAIL_SEND` | `PRD_SoR_SV` |

---

## ジョブテンプレート

### メイン処理

| JT名 | Playbook | Extra Variables |
|---|---|---|
| `PRD_SV_POSTFIX_SWITCH` | `switch_postfix.yml` | `target_hosts` |
| `PRD_SV_POSTFIX_VERIFY` | `verify_postfix.yml` | `target_hosts` |
| `PRD_SV_TESTMAIL_SEND` | `send_testmail.yml` | `target_hosts`<br>`mail_to` |

### 通知処理

| JT名 | Playbook | Extra Variables |
|---|---|---|
| `PRD_SV_POSTFIX_SWITCH_NOTIFY_SUCCESS` | `notify_teams.yml` | `teams_webhook_url`<br>`notify_message: "postfix設定切り替えが成功しました"` |
| `PRD_SV_POSTFIX_SWITCH_NOTIFY_FAILURE` | `notify_teams.yml` | `teams_webhook_url`<br>`notify_message: "postfix設定切り替えが失敗しました"` |
| `PRD_SV_POSTFIX_VERIFY_NOTIFY_SUCCESS` | `notify_teams.yml` | `teams_webhook_url`<br>`notify_message: "postfix切り替え確認が成功しました"` |
| `PRD_SV_POSTFIX_VERIFY_NOTIFY_FAILURE` | `notify_teams.yml` | `teams_webhook_url`<br>`notify_message: "postfix切り替え確認が失敗しました"` |
| `PRD_SV_TESTMAIL_SEND_NOTIFY_SUCCESS` | `notify_teams.yml` | `teams_webhook_url`<br>`notify_message: "テストメール送信が成功しました"` |
| `PRD_SV_TESTMAIL_SEND_NOTIFY_FAILURE` | `notify_teams.yml` | `teams_webhook_url`<br>`notify_message: "テストメール送信が失敗しました"` |

---

## ワークフロー ノード構成

### PRD_SV\WF_POSTFIX_SWITCH

| ノード | JT名 | 条件 | 次のノード |
|---|---|---|---|
| 1 | `PRD_SV_POSTFIX_SWITCH` | success | `PRD_SV_POSTFIX_SWITCH_NOTIFY_SUCCESS` |
| 1 | `PRD_SV_POSTFIX_SWITCH` | failure | `PRD_SV_POSTFIX_SWITCH_NOTIFY_FAILURE` |

### PRD_SV\WF_POSTFIX_VERIFY

| ノード | JT名 | 条件 | 次のノード |
|---|---|---|---|
| 1 | `PRD_SV_POSTFIX_VERIFY` | success | `PRD_SV_POSTFIX_VERIFY_NOTIFY_SUCCESS` |
| 1 | `PRD_SV_POSTFIX_VERIFY` | failure | `PRD_SV_POSTFIX_VERIFY_NOTIFY_FAILURE` |

### PRD_SV\WF_TESTMAIL_SEND

| ノード | JT名 | 条件 | 次のノード |
|---|---|---|---|
| 1 | `PRD_SV_TESTMAIL_SEND` | success | `PRD_SV_TESTMAIL_SEND_NOTIFY_SUCCESS` |
| 1 | `PRD_SV_TESTMAIL_SEND` | failure | `PRD_SV_TESTMAIL_SEND_NOTIFY_FAILURE` |
