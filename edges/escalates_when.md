# escalates_when

## 述語の意味

このConditionが不成立のとき、対応するEscalationReasonを記録して人間引継に移行する。

## 定義

| 項目 | 内容 |
|---|---|
| 主語 | Condition |
| 目的語 | EscalationReason |
| 多重度 | 1:1 |
| 逆関係 | `is_escalation_cause_of` |

## ふるまい

- `condition_type="binary"`の場合：評価結果がFalseのとき不成立
- `condition_type="threshold"`の場合：評価値が`threshold`未満のとき不成立
- 不成立時にEscalationReasonノードをログとして記録し、HUMAN_HANDOFF_OPを起動する
- EscalationReasonの`occurrence_count`をインクリメントする

## 設計上の注意

- EscalationReasonの記録はSLAの説明責任のために必須。理由なしの引継は禁止。
