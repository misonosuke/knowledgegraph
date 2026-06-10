# triggers

## 述語の意味

この発話概念（Entity）が検出されたとき、対応するIntentを候補として活性化する。

## 定義

| 項目 | 内容 |
|---|---|
| 主語 | Entity |
| 目的語 | Intent |
| 多重度 | N:N |
| 逆関係 | `is_triggered_by` |

## ふるまい

- 1つのEntityが複数のIntentを活性化できる（例：「パスワード」はPW_RESET_INTENTにもINQUIRY_INTENTにも繋がる）
- 1つのIntentが複数のEntityから活性化される（例：「ログイン」「認証」どちらもINQUIRY_INTENTを活性化する）
- 活性化はIntent**確定**ではない。複数候補が立った場合の確定は意図解釈LLMが行う

## 制約

- `is_fallback=true`のIntentへのtriggersエッジは禁止（[制約2](../constraints.md)）
