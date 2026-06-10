# OAヘルプ業務 ナレッジグラフ設計

## 概要

OAヘルプ業務のナレッジグラフ設計ドキュメント。  
ユーザーの発話テキストを解釈し、適切なオペレーションにルーティングするためのグラフ構造を定義する。

## 想定オペレーション

| オペレーション | 概要 |
|---|---|
| パスワードリセット | 本人確認後にPWをリセットする |
| 問い合わせ回答 | ナレッジに基づいてFAQ・手順を回答する |
| 人間引継 | いずれにも該当しない場合に人間オペレーターに引き継ぐ |

オペレーションは拡張可能な設計とする。

## 処理フロー概要

```
発話テキスト
  → Entity照合（グラフ・ルールベース）
  → 意図解釈LLM（Intent確定）
  → Operation起動
  → Condition評価（ルールベース）
  → Procedure実行 or EscalationReason記録
```

## ドキュメント構成

| パス | 内容 |
|---|---|
| `nodes/` | ノード種別ごとのプロパティ定義 |
| `edges/` | エッジ種別ごとの述語定義 |
| `constraints.md` | オントロジー制約 |

## ノード種別

| ノード | 役割 |
|---|---|
| [Entity](nodes/entity.md) | 発話概念の正規化レイヤー |
| [Intent](nodes/intent.md) | ユーザーの意図カテゴリ |
| [Operation](nodes/operation.md) | 実行単位（拡張ポイント） |
| [Condition](nodes/condition.md) | 分岐条件の評価器 |
| [Procedure](nodes/procedure.md) | 実際の処理ステップ |
| [EscalationReason](nodes/escalation_reason.md) | 人間引継理由の記録 |

## エッジ種別

| エッジ | 主語 → 目的語 | 意味 |
|---|---|---|
| [triggers](edges/triggers.md) | Entity → Intent | 発話概念がIntentを候補として活性化する |
| [routes_to](edges/routes_to.md) | Intent → Operation | 確定したIntentがOperationを起動する |
| [requires](edges/requires.md) | Operation → Condition | OperationがConditionの評価を要求する |
| [proceeds_when](edges/proceeds_when.md) | Condition → Procedure | Condition成立時にProcedureに進む |
| [escalates_when](edges/escalates_when.md) | Condition → EscalationReason | Condition不成立時に引継理由を記録する |
| [follows](edges/follows.md) | Procedure → Procedure | 手順の実行順序を示す |
| [answered_by](edges/answered_by.md) | Intent → Procedure | 問い合わせIntentの回答入口を示す |

## フェーズ管理

| 項目 | 状態 |
|---|---|
| ノード種別定義 | ✅ 確定 |
| エッジ種別・述語定義 | ✅ 確定 |
| プロパティ定義 | ✅ 確定 |
| オントロジー制約 | ✅ 確定 |
| UNKNOWN_INTENTフォールバック | ✅ 確定（グラフ上に明示） |
| インスタンスデータ投入 | 🔲 実装フェーズ |
| 実装技術選定（Neo4j等） | 🔲 実装フェーズ |
