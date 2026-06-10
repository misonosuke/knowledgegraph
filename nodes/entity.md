# Entity（発話概念）

## 役割

ユーザーの発話テキストに含まれる概念を正規化するレイヤー。  
「PW」「パスワ」「passward」など表記揺れをすべて同一ノードに束ねることで、後段の意図解釈LLMへ渡す前にルールベースで安定した正規化を行う。

## プロパティ

| プロパティ名 | 型 | 必須 | 説明 | 例 |
|---|---|---|---|---|
| `id` | string | 必須 | 一意識別子 | `"entity_password"` |
| `label` | string | 必須 | 正規化後の概念名 | `"パスワード"` |
| `synonyms` | string[] | 必須 | 表記揺れ・同義語リスト | `["PW","パスワ","passward"]` |
| `language` | string | 任意 | 言語コード | `"ja"` |
| `created_at` | datetime | 必須 | 登録日時 | |
| `updated_at` | datetime | 必須 | 最終更新日時 | |

## 接続するエッジ

| エッジ | 方向 | 接続先 |
|---|---|---|
| [triggers](../edges/triggers.md) | Entity → Intent | このEntityが検出されたとき候補となるIntent |

## 設計上の注意

- `synonyms`はルールベースで管理する。LLMの学習データに依存しない。
- 同一概念の別表現はすべて同一Entityノードに集約する。新しい表記揺れが発見されたら`synonyms`に追加する。
- `is_fallback=true`のIntentと異なり、Entityにフォールバック概念は存在しない。

## インスタンス例

```json
{
  "id": "entity_password",
  "label": "パスワード",
  "synonyms": ["PW", "pw", "パスワ", "ぱすわーど", "passward", "pass"],
  "language": "ja",
  "created_at": "2025-01-01T00:00:00Z",
  "updated_at": "2025-01-01T00:00:00Z"
}
```
