# Condition（条件）

## 役割

Operationの実行結果に対する分岐評価器。  
LLMに判断させると再現性が下がる分岐ルールをグラフに閉じ込め、ルールベースで確実に評価する。  
評価結果が成立の場合は`proceeds_when`でProcedureに進み、不成立の場合は`escalates_when`でEscalationReasonを記録して人間引継に移行する。

## プロパティ

| プロパティ名 | 型 | 必須 | 説明 | 例 |
|---|---|---|---|---|
| `id` | string | 必須 | 一意識別子 | `"cond_identity_check"` |
| `label` | string | 必須 | 条件名 | `"本人確認条件"` |
| `condition_type` | enum | 必須 | 評価タイプ。下記参照 | `"binary"` |
| `rule` | string | 必須 | 評価ルールの識別子 | `"identity_match"` |
| `threshold` | float | 条件付き | `condition_type="threshold"`のとき必須 | `0.75` |
| `metric` | string | 条件付き | `condition_type="threshold"`のとき必須。評価対象の指標名 | `"rag_score"` |
| `created_at` | datetime | 必須 | 登録日時 | |
| `updated_at` | datetime | 必須 | 最終更新日時 | |

### `condition_type` の値

| 値 | 意味 | 対応するOperationのop_type |
|---|---|---|
| `binary` | 二値評価。成立/不成立がルールで判定できる | `deterministic` |
| `threshold` | 閾値評価。連続値が閾値以上のとき成立とみなす | `generative` |

## 接続するエッジ

| エッジ | 方向 | 接続先 |
|---|---|---|
| [requires](../edges/requires.md) | Operation → Condition | このConditionの評価を要求するOperation |
| [proceeds_when](../edges/proceeds_when.md) | Condition → Procedure | 成立時に進むProcedure |
| [escalates_when](../edges/escalates_when.md) | Condition → EscalationReason | 不成立時に記録するEscalationReason |

## 設計上の注意

- `threshold`はチューニングパラメータ。運用フェーズで実績データをもとに調整する。
- `rule`はConditionの評価ロジックを実装側で解決するための識別子。グラフは評価結果を受け取るだけで、評価ロジック自体はアプリケーション層が担う。

## インスタンス例

```json
[
  {
    "id": "cond_identity_check",
    "label": "本人確認条件",
    "condition_type": "binary",
    "rule": "identity_match",
    "threshold": null,
    "metric": null,
    "created_at": "2025-01-01T00:00:00Z",
    "updated_at": "2025-01-01T00:00:00Z"
  },
  {
    "id": "cond_rag_confidence",
    "label": "確信度条件",
    "condition_type": "threshold",
    "rule": "rag_score_check",
    "threshold": 0.75,
    "metric": "rag_score",
    "created_at": "2025-01-01T00:00:00Z",
    "updated_at": "2025-01-01T00:00:00Z"
  }
]
```
