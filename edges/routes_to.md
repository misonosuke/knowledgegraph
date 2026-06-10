# routes_to

## 述語の意味

このIntentが確定したとき、対応するOperationを一意に起動する。

## 定義

| 項目 | 内容 |
|---|---|
| 主語 | Intent |
| 目的語 | Operation |
| 多重度 | N:1 |
| 逆関係 | `is_routed_from` |

## ふるまい

- Intentが確定した瞬間に、対応するOperationが起動される
- 複数のIntentが同一のOperationにroutes_toすることは可能（N:1）
- 1つのIntentから複数のOperationへの接続は禁止

## 制約

- 1つのIntentは必ず1つのOperationにしかroutes_toできない（[制約1](../constraints.md)）
- 分岐が必要な場合はIntentを分割して対処する
