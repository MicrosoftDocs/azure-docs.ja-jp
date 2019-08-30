---
title: Azure API Management を使用してサーバーレス API の OpenAPI 定義を作成する
description: 他のアプリやサービスが Azure で関数を呼び出せるようにする OpenAPI 定義を作成します。
keywords: OpenAPI, Swagger, クラウド アプリ, クラウド サービス,
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: glenga
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 54a4c6eba094231e8e73cdef87b911dfba20f657
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533545"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Azure API Management を使用してサーバーレス API の OpenAPI 定義を作成する

REST API は、多くの場合、OpenAPI 定義を使用して記述されます。 この定義には、API で使用できる操作の情報と、API の要求データと応答データを構造化する方法に関する情報が含まれています。

このチュートリアルでは、風力タービンの応急修復がコスト効率に優れているかどうかを確認する関数を作成します。 その後、その関数を他のアプリやサービスから呼び出せるように、[Azure API Management](../api-management/api-management-key-concepts.md) を使用して関数アプリの OpenAPI 定義を作成します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure で関数を作成する
> * Azure API Management を使用して OpenAPI 定義を生成する
> * 関数を呼び出して定義をテストする
> * OpenAPI 定義をダウンロードする

## <a name="create-a-function-app"></a>Function App を作成する

関数の実行をホストするための Function App が存在する必要があります。 関数アプリを使用すると、リソースの管理、デプロイ、スケーリング、および共有を容易にするための論理ユニットとして関数をグループ化できます。

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>関数を作成する

このチュートリアルでは、2 つのパラメーターを受け取り、HTTP によってトリガーされる関数を使用します。

* タービン修復の推定所要時間 (時間単位)。
* タービンの容量 (キロワット単位)。 

この関数は、修復コストと、タービンによってもたらされる 24 時間あたり収益を計算します。 HTTP によってトリガーされる関数を [Azure portal](https://portal.azure.com) 内で作成します。

1. Function App を展開し、 **[関数]** の横にある **[+]** ボタンを選択します。 **[ポータル内]**  >  **[続行]** の順に選択します。

1. **[その他のテンプレート]** を選択した後、 **[テンプレートの完了と表示]** を選択します。

1. [HTTP トリガー] を選択し、関数の**名前**として「`TurbineRepair`」と入力し、 **[[認証レベル]](functions-bindings-http-webhook.md#http-auth)** で [`Function`] を選択し、 **[作成]** を選択します。  

    ![OpenAPI 用の HTTP 関数を作成する](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. この run.csx C# スクリプト ファイルの内容を次のコードに置き換えて、 **[保存]** を選択します。

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    この関数コードは、応急修復がコスト効率に優れているかを示すメッセージ `Yes` または `No` のほか、タービンによって創出される収益機会とタービンの修復コストを返します。

1. 関数をテストするには、一番右の **[テスト]** をクリックして、テスト タブを展開します。**要求本文**で次の値を入力し、 **[実行]** をクリックします。

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Azure Portal で関数をテストする](media/functions-openapi-definition/test-function.png)

    応答本文で次の値が返されます。

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

これで、応急修復のコスト効率性を確認する関数が作成されました。 次は、関数アプリの OpenAPI 定義を生成します。

## <a name="generate-the-openapi-definition"></a>OpenAPI 定義を生成する

OpenAPI 定義を生成する準備ができています。

1. 関数アプリを選択した後、 **[プラットフォーム機能]** で **[API Management]** を選択し、 **[API Management]** で **[新規作成]** を選択します。

    ![プラットフォーム機能で API Management を選択する](media/functions-openapi-definition/select-all-settings-openapi.png)

1. 画像の下の表で指定されている API Management の設定を使用してください。

    ![新しい API Management サービスを作成する](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Setting      | 推奨値  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | グローバルに一意の名前 | お使いの関数アプリの名前に基づいて名前が生成されます。 |
    | **サブスクリプション** | 該当するサブスクリプション | この新しいリソースが作成されるサブスクリプション。 |  
    | **[リソース グループ](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | お使いの関数アプリと同じリソース。自動的に設定されます。 |
    | **Location** | 米国西部 | 場所には [米国西部] を選びます。 |
    | **組織名** | Contoso | 開発者ポータルとメール通知で使用する組織の名前。 |
    | **管理者のメール アドレス** | ご自分のメール アドレス | API Management からのシステム通知を受信したメール アドレス。 |
    | **[価格レベル]** | Consumption (プレビュー) | 従量課金レベルはプレビュー段階であり、すべてのリージョンで使うことはできません。 価格の詳細については、[API Management の価格に関するページ](https://azure.microsoft.com/pricing/details/api-management/)を参照してください。 |

1. **[作成]** を選択して、API Management インスタンスを作成します。これには数分かかる場合があります。

1. **[Application Insights を有効にする]** を選択して、関数アプリケーションと同じ場所にログを送信します。次に、残りの既定値をそのまま使用し、 **[API のリンク]** を選択します。

1. **[Azure Functions のインポート]** が開き、**TurbineRepair** 関数が強調表示されます。 **[選択]** を選択して続行します。

    ![API Management に Azure Functions をインポートする](media/functions-openapi-definition/import-function-openapi.png)

1. **[Function App から作成する]** ページで、既定値をそのまま使用して **[作成]** を選択します。

    ![Function App から作成する](media/functions-openapi-definition/create-function-openapi.png)

これで、関数の API が作成されました。

## <a name="test-the-api"></a>API をテストする

OpenAPI 定義を使用する前に、API が動作することを確認する必要があります。

1. 関数の **[テスト]** タブで、**POST** 操作を選択します。

1. **hours** と **capacity** の値を入力します。

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. **[送信]** をクリックして HTTP 応答を確認します。

    ![関数 API をテストする](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>OpenAPI 定義をダウンロードする

API が意図したとおりに動作する場合は、OpenAPI 定義をダウンロードできます。

1. ページの上部にある **[OpenAPI 定義をダウンロードする]** を選択します。
   
   ![OpenAPI 定義のダウンロード](media/functions-openapi-definition/download-definition.png)

2. ダウンロードした JSON ファイルを開き、定義を確認します。

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次の手順

API Management の統合を使って、関数の OpenAPI 定義を生成しました。 ポータルの API Management で定義を編集できるようになります。 また、[API Management についてさらに学習する](../api-management/api-management-key-concepts.md)こともできます。

> [!div class="nextstepaction"]
> [API Management で OpenAPI 定義を編集する](../api-management/edit-api.md)
