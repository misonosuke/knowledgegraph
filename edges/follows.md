# follows

## 述語の意味

この手順が完了したとき、次に実行すべき手順を示す。

## 定義

| 項目 | 内容 |
|---|---|
| 主語 | Procedure |
| 目的語 | Procedure |
| 多重度 | 1:1 |
| 逆関係 | `is_followed_by` |

## ふるまい

- 1つのProcedureの次は必ず1つのProcedureのみ（分岐はConditionが担う）
- 末端のProcedure（最後のステップ）は`follows`エッジを持たない
- 循環参照は禁止

## 制約

- Procedure間の`follows`チェーンに循環が存在してはならない（[制約4](../constraints.md)）
