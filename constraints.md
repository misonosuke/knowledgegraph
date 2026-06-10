# オントロジー制約

## 概要

ノードおよびエッジの定義に加え、グラフ全体の整合性を保つための制約ルール。  
新しいノード・エッジを追加する際は、これらの制約に違反していないことを確認する。

---

## 制約一覧

### 制約1：`routes_to`の一意性

```
1つのIntentは、必ず1つのOperationにしかroutes_toエッジを持てない。
```

**理由**：ルーティングの一意性を保証するため。1つのIntentから複数のOperationに分岐すると、どのOperationを起動するかが曖昧になる。分岐が必要な場合はIntentを分割する。

---

### 制約2：フォールバックIntentへのtriggers禁止

```
is_fallback=true のIntentへのtriggersエッジは存在してはならない。
```

**理由**：UNKNOWN_INTENTはEntityからtriggerされるのではなく、意図解釈LLMが「該当なし」と判断したときに選ばれる特殊なIntentであるため。

---

### 制約3：通常IntentへのtriggersはN≧1

```
is_fallback=false のIntentは、必ず1つ以上のtriggersエッジを持たなければならない。
```

**理由**：triggersエッジを持たない通常Intentは、どの発話からも活性化されないデッドノードになる。新しいIntentを追加する際は必ず対応するEntityとtriggersエッジを同時に定義する。

---

### 制約4：Procedureの非循環

```
followsエッジで構成されるProcedureのチェーンに循環参照が存在してはならない。
```

**理由**：循環があると手順の実行が無限ループになる。

---

### 制約5：Conditionのプロパティ整合性

```
condition_type="threshold" のConditionは、thresholdとmetricの両方を持たなければならない。
condition_type="binary" のConditionは、thresholdとmetricをnullとする。
```

**理由**：型とプロパティの不整合による実行時エラーを防ぐ。

---

### 制約6：op_typeとConditionのcondition_typeの対応

```
op_type="deterministic" のOperationが requires するConditionは condition_type="binary" でなければならない。
op_type="generative" のOperationが requires するConditionは condition_type="threshold" でなければならない。
```

**理由**：確定型Operationにthreshold評価、生成型Operationにbinary評価を割り当てることは設計上の矛盾になる。

---

## 制約違反チェックリスト

新しいノード・エッジを追加するときに確認する項目。

| # | チェック項目 |
|---|---|
| 1 | 追加するIntentに複数のroutes_toを張っていないか |
| 2 | UNKNOWN_INTENTにtriggersエッジを張っていないか |
| 3 | 通常Intentに少なくとも1つのtriggersエッジを持たせているか |
| 4 | followsチェーンに循環がないか |
| 5 | threshold型ConditionにthresholdとMetricを設定しているか |
| 6 | op_typeとcondition_typeの組み合わせが正しいか |
