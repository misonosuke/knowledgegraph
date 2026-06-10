# Procedure（手順）

## 役割

実際の処理ステップ。RAGが検索する対象はここ。  
手順をテキストの塊として持つのではなくノードとして持つことで、「どのステップで詰まったか」「この手順の前後関係は何か」という構造的な問合せが可能になる。

## プロパティ

| プロパティ名 | 型 | 必須 | 説明 | 例 |
|---|---|---|---|---|
| `id` | string | 必須 | 一意識別子 | `"proc_send_pw_sms"` |
| `label` | string | 必須 | 手順名 | `"PW送信手順"` |
| `content` | string | 必須 | 手順の内容テキスト（RAGの検索対象） | |
| `exec_type` | enum | 必須 | 実行タイプ。下記参照 | `"api_call"` |
| `params` | object | 条件付き | `exec_type="api_call"`のときのパラメータ定義 | |
| `created_at` | datetime | 必須 | 登録日時 | |
| `updated_at` | datetime | 必須 | 最終更新日時 | |

### `exec_type` の値

| 値 | 意味 |
|---|---|
| `rule_based` | ルールに従って実行する手順 |
| `rag` | RAGで検索して回答を構成する手順 |
| `api_call` | 外部APIを呼び出して実行する手順 |

## 接続するエッジ

| エッジ | 方向 | 接続先 |
|---|---|---|
| [proceeds_when](../edges/proceeds_when.md) | Condition → Procedure | このProcedureに進むCondition |
| [answered_by](../edges/answered_by.md) | Intent → Procedure | このProcedureを入口とするIntent |
| [follows](../edges/follows.md) | Procedure → Procedure | このProcedureの次に実行するProcedure |

## 設計上の注意

- 末端のProcedure（最後のステップ）は`follows`エッジを持たない。
- `follows`の循環参照は禁止。
- `content`はRAGのインデックス対象。検索精度に直結するため、業務ナレッジを正確に記述する。

## インスタンス例

```json
[
  {
    "id": "proc_identity_verify",
    "label": "本人確認手順",
    "content": "社員番号と氏名の一致を確認する。一致しない場合はエスカレーション。",
    "exec_type": "rule_based",
    "params": null,
    "created_at": "2025-01-01T00:00:00Z",
    "updated_at": "2025-01-01T00:00:00Z"
  },
  {
    "id": "proc_send_pw_sms",
    "label": "PW送信手順",
    "content": "登録済みの携帯電話番号にSMSでリセットURLを送信する。",
    "exec_type": "api_call",
    "params": {
      "api": "sms_gateway",
      "template": "pw_reset_sms"
    },
    "created_at": "2025-01-01T00:00:00Z",
    "updated_at": "2025-01-01T00:00:00Z"
  },
  {
    "id": "proc_faq_search",
    "label": "FAQ検索手順",
    "content": "ユーザーの発話をクエリとしてRAGで関連手順・FAQを検索する。",
    "exec_type": "rag",
    "params": null,
    "created_at": "2025-01-01T00:00:00Z",
    "updated_at": "2025-01-01T00:00:00Z"
  },
  {
    "id": "proc_answer_generate",
    "label": "回答生成手順",
    "content": "RAG検索結果をもとにLLMが回答テキストを生成する。引用元Procedureを付記する。",
    "exec_type": "rag",
    "params": null,
    "created_at": "2025-01-01T00:00:00Z",
    "updated_at": "2025-01-01T00:00:00Z"
  }
]
```
