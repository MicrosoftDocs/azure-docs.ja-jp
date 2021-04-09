---
title: テレメトリ プロセッサの例 - Azure Monitor Application Insights for Java
description: Azure Monitor Application Insights for Java のテレメトリ プロセッサを示す例を調べます。
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 0978bd669855d264ed6dfa5eeddc45ad499aa2a5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734589"
---
# <a name="telemetry-processor-examples---azure-monitor-application-insights-for-java"></a>テレメトリ プロセッサの例 - Azure Monitor Application Insights for Java

この記事では、Application Insights for Java のテレメトリ プロセッサの例を示します。 include と exclude の構成のサンプルを紹介します。 また、属性プロセッサとスパン プロセッサのサンプルも紹介します。
## <a name="include-and-exclude-samples"></a>include と exclude のサンプル

このセクションでは、スパンを含める方法と除外する方法について説明します。 また、複数のスパンを除外し、選択的処理を適用する方法についても説明します。
### <a name="include-spans"></a>スパンを含める

このセクションでは、属性プロセッサに対してスパンを含める方法について説明します。 プロパティに一致しないスパンは、プロセッサによって処理されません。

一致が成立するためには、スパン名が `spanA` または `spanB` と同じである必要があります。 

次のスパンは include プロパティと一致し、プロセッサ アクションが適用されます。
* Span1 名前: 'spanA' 属性: {env: dev, test_request:123, credit_card:1234}
* Span2 名前: 'spanB' 属性: {env: dev, test_request: false}
* Span3 名前: 'spanA' 属性: {env:1, test_request: dev, credit_card:1234}

次のスパンは include プロパティと一致しないため、プロセッサ アクションは適用されません。
* Span4 名前: 'spanC' 属性: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans"></a>スパンを除外する

このセクションでは、属性プロセッサに対してスパンを除外する方法を示します。 プロパティと一致するスパンは、このプロセッサによって処理されません。

一致が成立するためには、スパン名が `spanA` または `spanB` と同じである必要があります。

次のスパンは exclude プロパティと一致するため、プロセッサ アクションは適用されません。
* Span1 名前: 'spanA' 属性: {env: dev, test_request:123, credit_card:1234}
* Span2 名前: 'spanB' 属性: {env: dev, test_request: false}
* Span3 名前: 'spanA' 属性: {env:1, test_request: dev, credit_card:1234}

次のスパンは exclude プロパティと一致しないため、プロセッサ アクションが適用されます。
* Span4 名前: 'spanC' 属性: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans-by-using-multiple-criteria"></a>複数の条件を使用してスパンを除外する

このセクションでは、属性プロセッサに対してスパンを除外する方法を示します。 プロパティと一致するスパンは、このプロセッサによって処理されません。

一致が成立するためには、次の条件が満たされる必要があります。
* 属性 (`env` や `dev` など) がスパン内に存在する必要があります。
* スパンには、キー `test_request` を持つ属性が必要です。

次のスパンは exclude プロパティと一致するため、プロセッサ アクションは適用されません。
* Span1 名前: 'spanB' 属性: {env: dev, test_request:123, credit_card:1234}
* Span2 名前: 'spanA' 属性: {env: dev, test_request: false}

次のスパンは exclude プロパティと一致しないため、プロセッサ アクションが適用されます。
* Span3 名前: 'spanB' 属性: {env:1, test_request: dev, credit_card:1234}
* Span4 名前: 'spanC' 属性: {env: dev, dev_request: false}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="selective-processing"></a>選択的処理

このセクションでは、どのスパンに対してこのプロセッサを適用するかを示す、スパン プロパティのセットを指定する方法について説明します。 include プロパティでは、処理する必要があるスパンを示します。 exclude プロパティでは、処理しないスパンが除外されます。

以下の構成の場合、次のスパンはプロパティと一致するため、プロセッサ アクションが適用されます。

* Span1 名前: 'spanB' 属性: {env: production, test_request:123, credit_card:1234, redact_trace: "false"}
* Span2 名前: 'spanA' 属性: {env: staging, test_request: false, redact_trace: true}

次のスパンは include プロパティと一致しないため、プロセッサ アクションは適用されません。
* Span3 名前: 'spanB' 属性: {env: production, test_request: true, credit_card:1234, redact_trace: false}
* Span4 名前: 'spanC' 属性: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "attributes": [
            {
              "key": "redact_trace",
              "value": "false"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "duplicate_key",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```
## <a name="attribute-processor-samples"></a>属性プロセッサのサンプル

### <a name="insert"></a>挿入

次の例では、キー `attribute1` が存在しないスパンに新しい属性 `{"attribute1": "attributeValue1"}` を挿入します。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>別のキーから挿入する

次の例では、属性 `anotherkey` からの値を使用して、キー `newKey` が存在しないスパンに新しい属性 `{"newKey": "<value from attribute anotherkey>"}` を挿入します。 属性 `anotherkey` が存在しない場合、新しい属性はスパンに挿入されません。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>更新

次の例では、属性を `{"db.secret": "redacted"}` に更新します。 属性 `foo` からの値を使用して、属性 `boo` を更新します。 属性 `boo` を持たないスパンは変更されません。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>削除

次の例では、キー `credit_card` を持つ属性を削除する方法が示されています。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>ハッシュ インデックス

次の例では、既存の属性値をハッシュする方法が示されています。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

### <a name="extract"></a>Extract

次の例では、正規表現 (regex) を使用して、別の属性の値に基づいて新しい属性を作成する方法が示されています。
たとえば、`http.url = http://example.com/path?queryParam1=value1,queryParam2=value2` が指定された場合、以下の属性が挿入されます。
* httpProtocol: `http`
* httpDomain: `example.com`
* httpPath: `path`
* httpQueryParams: `queryParam1=value1,queryParam2=value2`
* http.url: 変更 "*なし*"

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
          }
        ]
      }
    ]
  }
}
```

次の例では、正規表現パターンに一致するスパン名を持つスパンを処理する方法を示します。
このプロセッサでは、`token` 属性が削除されます。 スパン名が `auth.*` と一致し、スパン名が `login.*` と一致しないスパンの `password` 属性は難読化されます。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```


## <a name="span-processor-samples"></a>スパン プロセッサのサンプル

### <a name="name-a-span"></a>スパンに名前を付ける

次の例では、属性 `db.svc`、`operation`、および `id` の値を指定しています。 これらの属性をこの順番で使用し、値 `::` で区切ることによって、スパンの新しい名前が形成されます。
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-a-span-name"></a>スパン名から属性を抽出する

入力スパン名が `/api/v1/document/12345678/update` であると仮定しましょう。 次の例では、`/api/v1/document/{documentId}/update` という出力スパン名が生成されます。 スパンに新しい属性 `documentId=12345678` が追加されます。
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-a-span-name-by-using-include-and-exclude"></a>include および exclude を使用してスパン名から属性を抽出する

次の例では、スパン名を `{operation_website}` に変更する方法を示します。 スパンが次のプロパティを持つ場合に、キーが `operation_website`、値が `{oldSpanName}` の属性が追加されます。
- スパン名の文字列の任意の場所に `/` が含まれる。
- スパン名が `donot/change` でない。
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```
