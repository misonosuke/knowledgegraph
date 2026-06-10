# Intent（意図）

## 役割

ユーザーが「何をしたいか」のカテゴリ。  
EntityとOperationを直接つなげず、Intentを挟むことで「1つのEntityが複数の意図を持ち得る」という多義性を吸収する。  
意図解釈LLMがEntityの候補群と発話テキストを入力としてIntentを確定し、対応するOperationへのルーティング根拠を説明可能にする。

## プロパティ

| プロパティ名 | 型 | 必須 | 説明 | 例 |
|---|---|---|---|---|
| `id` | string | 必須 | 一意識別子 | `"intent_pw_reset"` |
| `label` | string | 必須 | 意図カテゴリ名 | `"PW_RESET_INTENT"` |
| `description` | string | 任意 | 意図の説明文 | `"パスワードをリセットしたい"` |
| `confidence_min` | float | 条件付き | LLMがこのIntentと判定するための最低確信度閾値。`is_fallback=false`のとき必須 | `0.8` |
| `is_fallback` | boolean | 必須 | `true`のときtriggersエッジを持たない特殊Intent（UNKNOWN_INTENT用） | `false` |
| `created_at` | datetime | 必須 | 登録日時 | |
| `updated_at` | datetime | 必須 | 最終更新日時 | |

## 接続するエッジ

| エッジ | 方向 | 接続先 |
|---|---|---|
| [triggers](../edges/triggers.md) | Entity → Intent | このIntentを候補として活性化するEntity（`is_fallback=false`のみ） |
| [routes_to](../edges/routes_to.md) | Intent → Operation | このIntentが確定したとき起動するOperation |
| [answered_by](../edges/answered_by.md) | Intent → Procedure | 問い合わせ系IntentにおけるRAG検索の入口Procedure |

## 設計上の注意

- `routes_to`は1:1制約。1つのIntentが複数のOperationに接続することは禁止。分岐が必要な場合はIntentを分割する。
- `is_fallback=true`のIntentは`triggersエッジ`を持ってはならない（制約2）。
- `is_fallback=false`のIntentは必ず1つ以上の`triggersエッジ`を持たなければならない（制約3）。
- `confidence_min`はチューニングパラメータとして運用フェーズで調整する。

## インスタンス例

```json
[
  {
    "id": "intent_pw_reset",
    "label": "PW_RESET_INTENT",
    "description": "パスワードをリセットしたい",
    "confidence_min": 0.8,
    "is_fallback": false,
    "created_at": "2025-01-01T00:00:00Z",
    "updated_at": "2025-01-01T00:00:00Z"
  },
  {
    "id": "intent_unknown",
    "label": "UNKNOWN_INTENT",
    "description": "いずれのIntentにも該当しない発話",
    "confidence_min": null,
    "is_fallback": true,
    "created_at": "2025-01-01T00:00:00Z",
    "updated_at": "2025-01-01T00:00:00Z"
  }
]
```
