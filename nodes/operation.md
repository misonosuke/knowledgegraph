# Operation（実行単位）

## 役割

Intentが確定した後に起動される実行単位。業務拡張のポイント。  
新しい業務オペレーションを追加するときは、このノードと対応するIntentへの`routes_to`エッジを追加するだけでルーティングが機能する。コードを変更せずグラフ更新のみで業務拡張できることを設計上の目標とする。

## プロパティ

| プロパティ名 | 型 | 必須 | 説明 | 例 |
|---|---|---|---|---|
| `id` | string | 必須 | 一意識別子 | `"op_pw_reset"` |
| `label` | string | 必須 | オペレーション名 | `"PASSWORD_RESET_OP"` |
| `op_type` | enum | 必須 | 実行タイプ。下記参照 | `"deterministic"` |
| `description` | string | 任意 | 処理の説明 | |
| `is_active` | boolean | 必須 | このOperationが現在有効かどうか | `true` |
| `created_at` | datetime | 必須 | 登録日時 | |
| `updated_at` | datetime | 必須 | 最終更新日時 | |

### `op_type` の値

| 値 | 意味 | Conditionの性質 |
|---|---|---|
| `deterministic` | 手順が決まっている実行処理。パスワードリセット等 | `binary`型Condition。成功/失敗が二値で判定できる |
| `generative` | 回答が事前に決まっていない生成処理。問い合わせ回答等 | `threshold`型Condition。確信度の閾値評価が必要 |

## 接続するエッジ

| エッジ | 方向 | 接続先 |
|---|---|---|
| [routes_to](../edges/routes_to.md) | Intent → Operation | このOperationを起動するIntent |
| [requires](../edges/requires.md) | Operation → Condition | このOperationが評価を要求するCondition |

## 設計上の注意

- 新Operationを追加するとき、`op_type`を最初に決めることで、対応するConditionの型（binary/threshold）が決まる。
- `is_active=false`にすることで、グラフ構造を壊さずにOperationを無効化できる。

## インスタンス例

```json
[
  {
    "id": "op_pw_reset",
    "label": "PASSWORD_RESET_OP",
    "op_type": "deterministic",
    "description": "本人確認後にパスワードをリセットする",
    "is_active": true,
    "created_at": "2025-01-01T00:00:00Z",
    "updated_at": "2025-01-01T00:00:00Z"
  },
  {
    "id": "op_knowledge_inquiry",
    "label": "KNOWLEDGE_INQUIRY_OP",
    "op_type": "generative",
    "description": "RAGを用いてFAQ・手順を検索し回答を生成する",
    "is_active": true,
    "created_at": "2025-01-01T00:00:00Z",
    "updated_at": "2025-01-01T00:00:00Z"
  },
  {
    "id": "op_human_handoff",
    "label": "HUMAN_HANDOFF_OP",
    "op_type": "deterministic",
    "description": "人間オペレーターに引き継ぐ",
    "is_active": true,
    "created_at": "2025-01-01T00:00:00Z",
    "updated_at": "2025-01-01T00:00:00Z"
  }
]
```
