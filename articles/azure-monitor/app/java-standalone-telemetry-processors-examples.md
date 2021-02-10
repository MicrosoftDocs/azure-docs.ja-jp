---
title: テレメトリ プロセッサの例 - Azure Monitor Application Insights for Java
description: Azure Monitor Application Insights for Java 内のテレメトリ プロセッサを示す例
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 9b29c9611359c97c4097ad0b90ee2673bb28f37c
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696314"
---
# <a name="telemetry-processors-examples---azure-monitor-application-insights-for-java"></a>テレメトリ プロセッサの例 - Azure Monitor Application Insights for Java

## <a name="includeexclude-samples"></a>包含および除外のサンプル

### <a name="1-include-spans"></a>1. スパンを包含する

次に、この属性プロセッサのスパンの包含を示します。 プロパティが一致しない他のすべてのスパンは、このプロセッサによって処理されません。

一致するために満たす必要がある条件は、次のとおりです。
* スパン名は、"spanA" または "spanB" と等しい必要があります 

include プロパティと一致し、プロセッサ アクションが適用されるスパンは次のとおりです。
* Span1 名前: 'spanA' 属性: {env: dev, test_request:123, credit_card:1234}
* Span2 名前: 'spanB' 属性: {env: dev, test_request: false}
* Span3 名前: 'spanA' 属性: {env:1, test_request: dev, credit_card:1234}

次のスパンは include プロパティと一致しておらず、プロセッサ アクションは適用されません。
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

### <a name="2-exclude-spans"></a>2. スパンを除外する

次に、この属性プロセッサのスパンの除外を示します。 プロパティが一致するすべてのスパンは、このプロセッサによって処理されません。

一致するために満たす必要がある条件は、次のとおりです。
* スパン名は、"spanA" または "spanB" と等しい必要があります 

exclude プロパティと一致し、プロセッサ アクションが適用されないスパンは次のとおりです。
* Span1 名前: 'spanA' 属性: {env: dev, test_request:123, credit_card:1234}
* Span2 名前: 'spanB' 属性: {env: dev, test_request: false}
* Span3 名前: 'spanA' 属性: {env:1, test_request: dev, credit_card:1234}

次のスパンは、exclude プロパティと一致せず、プロセッサ アクションが適用されます。
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

### <a name="3-excludemulti-spans"></a>3. 複数のスパンを除外する

次に、この属性プロセッサのスパンの除外を示します。 プロパティが一致するすべてのスパンは、このプロセッサによって処理されません。

一致するために満たす必要がある条件は、次のとおりです。
* 一致するには、スパン内に属性 ('env'、'dev') が存在する必要があります。
* スパン内に、キー 'test_request' を持つ属性がある限り、一致と見なされます。

exclude プロパティと一致し、プロセッサ アクションが適用されないスパンは次のとおりです。
* Span1 名前: 'spanB' 属性: {env: dev, test_request:123, credit_card:1234}
* Span2 名前: 'spanA' 属性: {env: dev, test_request: false}

次のスパンは、exclude プロパティと一致せず、プロセッサ アクションが適用されます。
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

### <a name="4-selective-processing"></a>4. 選択的処理

次の例では、一連のスパン プロパティを指定して、このプロセッサを適用する必要があるスパンを示します。 プロパティの `include` は含める必要があるものを示し、`exclude` プロパティでは処理すべきでないスパンをさらにフィルターで除外します。

以下の構成の場合、次のスパンはプロパティと一致し、プロセッサ アクションが適用されます。

* Span1 名前: 'spanB' 属性: {env: production, test_request:123, credit_card:1234, redact_trace: "false"}
* Span2 名前: 'spanA' 属性: {env: staging, test_request: false, redact_trace: true}

次のスパンは include プロパティと一致しておらず、プロセッサ アクションは適用されません。
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

次では、新しい属性 {"attribute1": "attributeValue1"} を、キー "attribute1" が存在しないスパンに挿入します。

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

次の例では、属性 "anotherkey" の値を使用して、新しい属性 {"newKey": "value from attribute 'anotherkey'} を、キー "newKey" が存在しないスパンに挿入します。 属性 'anotherkey' が存在しない場合、新しい属性はスパンに挿入されません。

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

次の例では、属性を { "db.secret": "redacted"} に更新し、属性 'foo' の値を使用して属性 'boo' を更新します。 属性 'boo' が存在しないスパンは変更されません。

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

次は、キー 'credit_card' を含む属性の削除を示します。

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

次は、既存の属性値のハッシュを示します。

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

次の例は、正規表現を使用し、別の属性の値に基づいて新しい属性を作成する方法を示します。
たとえば、http.url = 'http://example.com/path?queryParam1=value1,queryParam2=value2' を指定すると、次の属性が挿入されます。
* httpProtocol: http
* httpDomain: example.com
* httpPath: path
* httpQueryParams: queryParam1=value1,queryParam2=value2
* http.url 値は変更されません。

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
このプロセッサにより "token" 属性が削除され、スパン名が "auth.\*" に一致し、スパン名が "login.\*" に一致しないスパンの "password" 属性が難読化されます。

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

次の例は、属性 "db. svc"、"operation"、および "id" の値により、スパンの新しい名前がその順序で、値 "::" で区切られて形成されることを示しています。
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

### <a name="extract-attributes-from-span-name"></a>スパン名から属性を抽出する

入力スパン名が /api/v1/document/12345678/update であるとします。 出力スパン名 /api/v1/document/{documentId}/update に次の結果を適用すると、スパンに新しい属性 "documentId"="12345678" が追加されます。
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

### <a name="extract-attributes-from-span-name-with-include-and-exclude"></a>include および exclude を使用してスパン名から属性を抽出する

スパン名を "{operation_website}" に変更し、スパンに次のプロパティがある場合の属性 {Key: operation_website, Value: oldSpanName} を追加する方法を次に示します。
- スパン名の文字列の任意の場所に '/' が含まれています。
- スパン名は 'donot/change' ではありません。
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