---
title: "JSON でワークフローを定義する - Azure Logic Apps | Microsoft Docs"
description: "JSON でロジック アプリのワークフロー定義を記述する方法"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/29/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: e94837bf79e42602e2f72cda747ea629eed45a20
ms.openlocfilehash: 920940d8ebe23d24216d3e886bd8ae58be12ce34
ms.lasthandoff: 03/01/2017


---
# <a name="create-workflow-definitions-for-logic-apps-using-json"></a>JSON を使用してロジック アプリのワークフロー定義を作成する

単純な宣言型の JSON 言語を使用して、[Azure Logic Apps](logic-apps-what-are-logic-apps.md) のワークフロー定義を作成できます。 まだ作成したことがない場合は、最初に、[ロジック アプリ デザイナーで初めてのロジック アプリを作成する方法](logic-apps-create-a-logic-app.md)に関する記事を確認してください。 また、[ワークフロー定義言語の詳細](http://aka.ms/logicappsdocs)に関する記事も参照してください。

## <a name="repeat-steps-over-a-list"></a>リストに対してステップを繰り返す

最大 10,000 個の項目を含む配列に対して反復処理を行い、各項目に対してアクションを実行するには、[foreach タイプ](logic-apps-loops-and-scopes.md)を使用します。

## <a name="handle-failures-if-something-goes-wrong"></a>問題が発生した場合にエラーを処理する

一般的には、"*修復ステップ*" を含めます。これは、1 つ以上の呼び出しが失敗した "*場合に限り*" 実行されるいくつかのロジックです。 この例では、さまざまな場所からデータを取得しますが、呼び出しに失敗した場合は、後でそのエラーを追跡できるように、どこかにメッセージを POST する必要があります。  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "postToErrorMessageQueue": {
      "type": "ApiConnection",
      "inputs": "...",
      "runAfter": {
        "readData": [
          "Failed"
        ]
      }
    }
  },
  "outputs": {}
}
```

`readData` が `Failed` になった後のみに `postToErrorMessageQueue` が実行されるよう指定するには、`runAfter` プロパティを使用します。たとえば、指定できる値のリストを指定するには、`runAfter` は `["Succeeded", "Failed"]` になります。

最後に、この例ではエラーが処理されるため、実行は `Failed` としてマークされなくなります。 この例ではこのエラーを処理するステップを追加したため、1 つのステップは `Failed` となりましたが、実行は `Succeeded` となっています。

## <a name="execute-two-or-more-steps-in-parallel"></a>複数のステップを並列実行する

複数のアクションを並列実行するには、その `runAfter` プロパティが実行時に統一されている必要があります。 

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "kind": "http",
      "type": "Request"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

この例では、`branch1` と `branch2` の両方が `readData` の後で実行されるように設定されています。 その結果、この&2; つの分岐は並列実行されます。 両方の分岐のタイムスタンプが一致します。

![並列](media/logic-apps-author-definitions/parallel.png)

## <a name="join-two-parallel-branches"></a>2 つの並列分岐の結合

並列実行するように設定された&2; つのアクションは、前の例のように、`runAfter` プロパティに項目を追加することで結合できます。

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {}
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "join": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "branch1": [
          "Succeeded"
        ],
        "branch2": [
          "Succeeded"
        ]
      }
    }
  },
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "Http",
      "inputs": {
        "schema": {}
      }
    }
  },
  "contentVersion": "1.0.0.0",
  "outputs": {}
}
```

![並列](media/logic-apps-author-definitions/join.png)

## <a name="map-list-items-to-a-different-configuration"></a>リスト項目を別の構成にマップする

次に、プロパティの値に基づいて異なるコンテンツを取得する必要があるとします。 取得先に対する値のマップをパラメーターとして作成できます。  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "http://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

この場合、最初に記事のリストを取得します。 2 番目のステップでは、パラメーターとして定義されたカテゴリに基づき、マップを使用して、コンテンツの取得元である URL を検索します。

ここでは、次のことに注意してください。 

*    [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) 関数は、定義済みの既知のカテゴリのいずれかとそのカテゴリが一致するかどうかを確認します。

*    カテゴリを取得したら、`parameters[...]` のように、角かっこを使用してマップの項目を取り出すことができます。

## <a name="process-strings"></a>文字列を処理する

文字列の操作には、さまざまな関数を使用できます。 たとえば、システムに渡す必要がある文字列があっても、文字エンコードが正しく処理されるかどうかについて確信を持てない場合があるとします。 1 つのオプションとして、この文字列に Base64 エンコードを使用します。 ただし、URL 内のエスケープを回避するために、いくつかの文字を置き換えます。 

また、最初の&5; 文字は使用されないため、注文の名前の部分文字列も必要になります。

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "orderer": "NAME=St�ph�n__�?�i?ian�"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

次のように、内側から外側に向かって処理が行われます。

1. 注文者の名前の [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) を取得します。これにより、文字数の合計が返されます。

2. 文字列を短くする必要があるため、5 を引きます。

3. 実際には [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring) を使用します。 ここでは、インデックス `5` から開始し、文字列の残りの部分を取得します。

4. この部分文字列を [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) 文字列に変換します。

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) で、すべての `+` 文字を `-` 文字に置き換えます。

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) で、すべての `/` 文字を `_` 文字に置き換えます。

## <a name="work-with-date-times"></a>日付と時刻を処理する

日付と時刻は、特に、"*トリガー*" を本来サポートしていないデータ ソースからデータを取り出すときに、役立つ場合があります。 また、日付と時刻を使用すると、さまざまなステップにかかる時間を確認することもできます。

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

この例では、前のステップから `startTime` を抽出しています。 その後、現在の時刻を取得し、1 秒引いています。

[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) 

このとき、`minutes` や `hours` のような他の時間単位も使用できます。 最後に、この&2; つの値を比較できます。 最初の値が&2; 番目の値より小さい場合、最初に注文が実行されてから&2; 秒以上経過しています。

日付の書式を設定するには、文字列フォーマッタを使用できます。 たとえば、RFC1123 を取得するには、[`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) を使用します。 日付の書式設定については、「[Workflow Definition Language (ワークフロー定義言語)](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow)」を参照してください。

## <a name="deployment-parameters-for-different-environments"></a>さまざまな環境のデプロイ パラメーター

一般に、デプロイのライフサイクルには、開発環境、ステージング環境、運用環境があります。 たとえば、これらすべての環境で同じ定義を使用する一方で、異なるデータベースを使用する場合があります。 同様に、高可用性を確保するためにさまざまなリージョン間で同じ定義を使用する一方で、各ロジック アプリのインスタンスではそのリージョンのデータベースと通信することが必要な場合もあります。
このシナリオは、前の例で説明したように `trigger()` 関数を使用する必要があるため、"*実行時*" にパラメーターを受け取る方法とは違います。

次の例のように、基本的な定義から始めることができます。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

ロジック アプリの実際の `PUT` 要求で、`uri` パラメーターを指定できます。 既定値は存在しないため、ロジック アプリのペイロードではこのパラメーターが必要です。

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

それぞれの環境で、`connection` パラメーターに異なる値を指定できます。 

ロジック アプリの作成と管理用に用意されているすべてのオプションについては、[REST API のドキュメント](https://msdn.microsoft.com/library/azure/mt643787.aspx)を参照してください。 

