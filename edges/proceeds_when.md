# proceeds_when

## 述語の意味

このConditionが成立したとき、対応するProcedureに処理を進める。

## 定義

| 項目 | 内容 |
|---|---|
| 主語 | Condition |
| 目的語 | Procedure |
| 多重度 | 1:1 |
| 逆関係 | `is_proceeded_by` |

## ふるまい

- `condition_type="binary"`の場合：評価結果がTrueのとき成立
- `condition_type="threshold"`の場合：評価値が`threshold`以上のとき成立
- 成立したConditionは`proceeds_when`で指定されたProcedureの先頭ステップに処理を渡す
