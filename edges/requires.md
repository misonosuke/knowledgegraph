# requires

## 述語の意味

このOperationを実行するにあたり、このConditionを評価しなければならない。

## 定義

| 項目 | 内容 |
|---|---|
| 主語 | Operation |
| 目的語 | Condition |
| 多重度 | 1:N |
| 逆関係 | `is_required_by` |

## ふるまい

- Operationが起動されると、紐づくConditionの評価が実行される
- 1つのOperationが複数のConditionを持てる（評価順序が重要な場合はConditionにorder属性を追加する）
- Conditionの評価結果によって`proceeds_when`または`escalates_when`に分岐する
