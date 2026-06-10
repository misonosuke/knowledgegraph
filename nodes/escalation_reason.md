# EscalationReason（引継理由）

## 役割

人間に引き継ぐ理由を列挙・分類しておくノード。  
これがないと「なぜ引き継いだか」がログに残らず、SLAの説明責任が取れない。  
引継パターンを蓄積することで、将来のIntent/Operation追加の優先順位判断にも使える。

## プロパティ

| プロパティ名 | 型 | 必須 | 説明 | 例 |
|---|---|---|---|---|
| `id` | string | 必須 | 一意識別子 | `"esc_identity_failed"` |
| `label` | string | 必須 | 引継理由名 | `"本人確認失敗"` |
| `reason_type` | enum | 必須 | 理由の分類。下記参照 | `"rule_violation"` |
| `description` | string | 任意 | 理由の詳細説明 | |
| `occurrence_count` | integer | 任意 | 発生回数（蓄積ログ） | `42` |
| `created_at` | datetime | 必須 | 登録日時 | |
| `updated_at` | datetime | 必須 | 最終更新日時 | |

### `reason_type` の値

| 値 | 意味 | 主な発生元 |
|---|---|---|
| `rule_violation` | ルール評価が不成立 | binary型Conditionの不成立（例：本人確認失敗） |
| `low_confidence` | 確信度が閾値を下回った | threshold型Conditionの不成立（例：RAGスコア低） |
| `out_of_scope` | いずれのIntentにも該当しない | UNKNOWN_INTENTからのルーティング |
| `user_request` | ユーザーが人間対応を明示的に要求 | 発話中に「人に繋いでほしい」等の表現を検出 |

## 接続するエッジ

| エッジ | 方向 | 接続先 |
|---|---|---|
| [escalates_when](../edges/escalates_when.md) | Condition → EscalationReason | このEscalationReasonを記録するCondition |

## 設計上の注意

- `occurrence_count`は運用フェーズで蓄積するログ集計値。頻度の高い`out_of_scope`や`low_confidence`は、新Operationの追加やRAGのチューニング判断材料として使う。
- `user_request`はConditionから発生するのではなく、発話検出によって直接トリガーされる特殊ケース。実装上の処理はアプリケーション層が担う。

## インスタンス例

```json
[
  {
    "id": "esc_identity_failed",
    "label": "本人確認失敗",
    "reason_type": "rule_violation",
    "description": "社員番号と氏名の照合に失敗した",
    "occurrence_count": 0,
    "created_at": "2025-01-01T00:00:00Z",
    "updated_at": "2025-01-01T00:00:00Z"
  },
  {
    "id": "esc_low_confidence",
    "label": "低確信度",
    "reason_type": "low_confidence",
    "description": "RAGの検索スコアが閾値を下回り、回答品質を保証できない",
    "occurrence_count": 0,
    "created_at": "2025-01-01T00:00:00Z",
    "updated_at": "2025-01-01T00:00:00Z"
  },
  {
    "id": "esc_out_of_scope",
    "label": "スコープ外",
    "reason_type": "out_of_scope",
    "description": "いずれのIntentにも該当しない発話",
    "occurrence_count": 0,
    "created_at": "2025-01-01T00:00:00Z",
    "updated_at": "2025-01-01T00:00:00Z"
  },
  {
    "id": "esc_user_request",
    "label": "ユーザー要求",
    "reason_type": "user_request",
    "description": "ユーザーが人間オペレーターへの引継を明示的に要求した",
    "occurrence_count": 0,
    "created_at": "2025-01-01T00:00:00Z",
    "updated_at": "2025-01-01T00:00:00Z"
  }
]
```
