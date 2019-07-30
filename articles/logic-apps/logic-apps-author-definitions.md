---
title: ロジック アプリ定義の JSON の作成、編集、拡張 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps でロジック アプリ定義の JSON を作成および拡張します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 121e2d2595b63a313d9307f7d47f90adacc30fc2
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296117"
---
# <a name="create-edit-or-extend-json-for-logic-app-definitions-in-azure-logic-apps"></a>Azure Logic Apps でのロジック アプリ定義の JSON の作成、編集、拡張

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) で自動ワークフローを使用してエンタープライズ統合ソリューションを作成する場合、基になるロジック アプリ定義では、[ワークフロー定義言語 (WDL) スキーマ](../logic-apps/logic-apps-workflow-definition-language.md)と共に単純な宣言型の JavaScript Object Notation (JSON) を記述と検証に使用します。 これらの形式を使用することで、コードに関する詳しい知識がなくても、ロジック アプリ定義を理解しやすくなります。 ロジック アプリの作成とデプロイを自動化するときには、[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-overview.md#template-deployment)に [Azure リソース](../azure-resource-manager/resource-group-overview.md)としてロジック アプリ定義を含めることができます。 ロジック アプリを作成、管理、デプロイするときは、[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp)、[Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)、または [Azure Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/) を使用できます。

JSON でロジック アプリ定義を操作するには、Azure Portal または Visual Studio で作業するときにコード ビュー エディターを開くか、任意のエディターに定義をコピーします。 ロジック アプリを初めて使用する場合は、[初めてのロジック アプリを作成する方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事をご覧ください。

> [!NOTE]
> ロジック アプリ定義のパラメーターや複数のトリガーの定義など、Azure Logic Apps の一部の機能は JSON でのみ使用でき、Logic Apps デザイナーでは使用できません。
> したがって、これらのタスクについては、コード ビューまたは別のエディターで作業する必要があります。

## <a name="edit-json---azure-portal"></a>JSON を編集する - Azure Portal

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> にサインインします。

2. 左側のメニューの **[すべてのサービス]** を選択します。 検索ボックスで "logic apps" を検索し、検索結果からロジック アプリを選択します。

3. ロジック アプリのメニューで、 **[開発ツール]** の **[ロジック アプリ コード ビュー]** を選択します。

   コード ビュー エディターが開き、JSON 形式でロジック アプリ定義が表示されます。

## <a name="edit-json---visual-studio"></a>JSON を編集する - Visual Studio

Visual Studio でロジック アプリ定義を操作する前に、[必要なツールがインストール](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)されていることを確認します。 Visual Studio でロジック アプリを作成するときは、「[クイック スタート:Azure Logic Apps を使用してタスクとプロセスを自動化する - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)」をご覧ください。

Visual Studio では、Azure Portal から直接デプロイされているか、Visual Studio から Azure Resource Manager プロジェクトとしてデプロイされている作成済みのロジック アプリを開くことができます。

1. ロジック アプリを含む、Visual Studio ソリューションまたは [Azureリソース グループ](../azure-resource-manager/resource-group-overview.md) プロジェクトを開きます。

2. ロジック アプリの定義を見つけて開きます。既定では、**LogicApp.json** という名前の [Resource Manager テンプレート](../azure-resource-manager/resource-group-overview.md#template-deployment)に表示されます。 このテンプレートを使用し、さまざまな環境へのデプロイ用にカスタマイズできます。

3. ロジック アプリ定義とテンプレートのショートカット メニューを開きます。 **[Open With Logic App Designer]\(ロジック アプリ デザイナーで開く\)** を選択します。

   ![Visual Studio ソリューションのロジック アプリを開く](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > このコマンドが Visual Studio 2019 にない場合は、Visual Studio の最新の更新プログラムが適用されていることを確認してください。

4. デザイナーの下部で **[コード ビュー]** を選択します。 

   コード ビュー エディターが開き、JSON 形式でロジック アプリ定義が表示されます。

5. デザイナー ビューに戻るには、コード ビュー エディターの下部で **[デザイン]** を選択します。

## <a name="parameters"></a>parameters

パラメーターを使用すると、ロジック アプリ全体にわたって値を再利用できるので、頻繁に変更する可能性のある値の置き換えに適しています。 たとえば、複数の場所で使用する電子メール アドレスがある場合、その電子メール アドレスをパラメーターとして定義します。

パラメーターは、さまざまな環境でパラメーターをオーバーライドする必要がある場合にも役立ちます。[デプロイのパラメーター](#deployment-parameters)の詳細と、[Azure Logic Apps 用 REST API のドキュメント](https://docs.microsoft.com/rest/api/logic)をご覧ください。

> [!NOTE]
> パラメーターはコード ビューでのみ使用できます。

[最初のロジック アプリの例](../logic-apps/quickstart-create-first-logic-app-workflow.md)では、Web サイトの RSS フィードに 新しい投稿が表示されたときに電子メールを送信するワークフローを作成しました。 フィードの URL はハードコードされているので、この例では、フィードの URL をより簡単に変更できるように、クエリ値をパラメーターに置き換える方法を説明します。

1. コード ビューで、`parameters : {}` オブジェクトを見つけて、`currentFeedUrl` オブジェクトを追加します。

   ``` json
   "currentFeedUrl" : {
      "type" : "string",
      "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
   }
   ```

2. `When_a_feed-item_is_published` アクションで、`queries` セクションを見つけて、クエリ値を `"feedUrl": "#@{parameters('currentFeedUrl')}"` に置き換えます。

   **変更前**
   ``` json
   }
      "queries": {
          "feedUrl": "https://s.ch9.ms/Feeds/RSS"
       }
   },
   ```

   **変更後**
   ``` json
   }
      "queries": {
          "feedUrl": "#@{parameters('currentFeedUrl')}"
       }
   },
   ```

   2 つ以上の文字列を結合する場合、`concat` 関数を使用することもできます。 
   たとえば、`"@concat('#',parameters('currentFeedUrl'))"` は上記の例と同様に動作します。

3.  完了したら、 **[保存]** を選択します。

これで、`currentFeedURL` オブジェクトから別の URL を渡すことで、Web サイトの RSS フィードを変更できるようになりました。

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>さまざまな環境のデプロイ パラメーター

通常、デプロイのライフサイクルには、開発、ステージング、運用の各環境があります。 たとえば、これらのすべての環境で同じロジック アプリ定義を使用する一方で、異なるデータベースをご利用いただけます。 同様に、高可用性を確保するためにさまざまなリージョンで同じ定義を使用し、各ロジック アプリ インスタンスではそのリージョンのデータベースを使用することもできます。

> [!NOTE]
> このシナリオは、`trigger()` 関数を使用する必要があるため、"*実行時*" にパラメーターを受け取る方法とは異なります。

基本的な定義を次に示します。

``` json
{
    "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
ロジック アプリの実際の `PUT` 要求で、`uri` パラメーターを指定できます。 それぞれの環境で、`connection` パラメーターに異なる値を指定できます。 既定値は存在しないため、ロジック アプリのペイロードではこのパラメーターが必要です。

``` json
{
    "properties": {},
        "definition": {
          /// Use the definition from above here
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

詳細については、[Azure Logic Apps 用 REST API のドキュメント](https://docs.microsoft.com/rest/api/logic/)をご覧ください。

## <a name="process-strings-with-functions"></a>関数を使用して文字列を処理する

Logic Apps には、文字列を操作するためのさまざまな関数があります。 たとえば、注文の会社名を別のシステムに渡すとします。 しかし、文字エンコードの適切な処理がわかりません。 この場合、この文字列の Base64 エンコードを実行できますが、URL のエスケープを回避するために、いくつかの文字を置き換えることができます。 また、最初の 5 文字は使用されないため、会社名の部分文字列のみが必要です。

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

次の手順は、この例でこの文字列がどのように処理されるのかを示しています。内側から外側に向かって処理が行われます。

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. 会社名の [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) を取得します。これにより、文字数の合計を取得します。

2. 取得する文字列を短くするために、`5` を減算します。

3. [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md) を取得します。 インデックス `5` から開始し、文字列の残りの部分に移動します。

4. この部分文字列を [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) 文字列に変換します。

5. 次に、[`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) で、すべての `+` 文字を `-` 文字に置き換えます。

6. 最後に、[`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) で、すべての `/` 文字を `_` 文字に置き換えます。

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>リスト項目をプロパティ値にマップし、マップをパラメーターとして使用する

プロパティの値に基づいて異なる結果を取得するには、各プロパティ値を結果に対応付けるマップを作成し、そのマップをパラメーターとして使用します。

たとえば、このワークフローでは、いくつかのカテゴリをパラメーターとして定義し、それらのカテゴリを特定の URL に対応付けるマップを定義します。 ワークフローでは、まず、記事のリストを取得します。 次に、マップを使用して、各記事のカテゴリに一致する URL を検索します。

*   [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) 関数は、そのカテゴリが定義済みの既知のカテゴリと一致するかどうかを確認します。

*   一致するカテゴリを取得したら、`parameters[...]` のように角かっこを使用して、マップから項目を取り出します。

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
        "science": "https://www.nasa.gov",
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
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
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

## <a name="get-data-with-date-functions"></a>日付関数を使用してデータを取得する

"*トリガー*" をネイティブにサポートしていないデータ ソースからデータを取得するには、代わりに日付関数を使用して日付と時刻を処理します。 たとえば、次の式では、このワークフローのステップの所要時間を確認します。内側から外側に向かって処理が行われます。

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. `order` アクションから `startTime` を抽出します。
2. `utcNow()` を使用して現在の時刻を取得します。
3. 1 秒減算します。

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   このとき、`minutes` や `hours` のような他の時間単位も使用できます。

3. これで、この 2 つの値を比較できます。 

   最初の値が 2 番目の値より小さい場合、最初に注文が実行されてから 2 秒以上経過しています。

日付を書式設定するには、文字列フォーマッタを使用します。 たとえば、RFC1123 を取得するには、[`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md) を使用します。 日付の書式設定の詳細については、[こちら](../logic-apps/logic-apps-workflow-definition-language.md)をご覧ください。

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
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
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
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
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
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

## <a name="next-steps"></a>次の手順

* [条件に基づいてステップを実行する (条件付きステートメント)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [さまざまな値に基づいてステップを実行する (switch ステートメント)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [ステップを実行して繰り返す (ループ)](../logic-apps/logic-apps-control-flow-loops.md)
* [並列ステップを実行または結合する (分岐)](../logic-apps/logic-apps-control-flow-branches.md)
* [グループ化されたアクションの状態に基づいてステップを実行する (スコープ)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Azure Logic Apps のワークフロー定義言語スキーマ](../logic-apps/logic-apps-workflow-definition-language.md)の詳細を確認する
* [Azure Logic Apps のワークフローのアクションとトリガー](../logic-apps/logic-apps-workflow-actions-triggers.md)の詳細を確認する
