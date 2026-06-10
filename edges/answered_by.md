# answered_by

## 述語の意味

このIntentへの回答を構成する手順の入口を示す。問い合わせ系Operationのみで使用する。

## 定義

| 項目 | 内容 |
|---|---|
| 主語 | Intent |
| 目的語 | Procedure |
| 多重度 | N:1 |
| 逆関係 | `is_answer_for` |

## ふるまい

- 複数のIntentが同じProcedureを入口とすることができる（N:1）
- RAGが検索結果をマッピングする際の参照先となる
- `KNOWLEDGE_INQUIRY_OP`が起動されたとき、対応するIntentの`answered_by`先Procedureを起点にRAG検索を実行する

## 設計上の注意

- `deterministic`型Operationには使用しない。問い合わせ系（`generative`型）のみ。
