---
title: Azure API Management を使用して OpenAPI で関数を公開する
description: 他のアプリやサービスが Azure で関数を呼び出せるようにする OpenAPI 定義を作成します。
ms.topic: tutorial
ms.date: 04/21/2020
ms.reviewer: sunayv
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47, references_regions
ms.openlocfilehash: 9083ff7d8f65c68ce8d173973a4eda650ac355aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88212907"
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

この関数は、修復コストと、タービンによってもたらされる 24 時間あたり収益を計算します。 HTTP によってトリガーされる関数を [Azure portal](https://portal.azure.com) で作成するには:

1. 関数アプリの左側のメニューから **[関数]** を選択し、上部のメニューから **[追加]** を選択します。

1. **[新しい関数]** ウィンドウで **[Http トリガー]** を選択します。

1. **[新しい関数]** に「`TurbineRepair`」と入力します。 

1. **[[承認レベル]](functions-bindings-http-webhook-trigger.md#http-auth)** ドロップダウン リストから **[Function]** を選択し、 **[関数の作成]** を選択します。

    :::image type="content" source="media/functions-openapi-definition/select-http-trigger-openapi.png" alt-text="OpenAPI 用の HTTP 関数を作成する":::

1. **[Code + Test]\(コード + テスト\)** を選択し、ドロップダウン リストから **[run.csx]** を選択します。 この run.csx C# スクリプト ファイルの内容を次のコードに置き換えて、 **[保存]** を選択します。

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

    この関数コードは、応急修復のコスト効果が高いかどうかを示す "`Yes`" または "`No`" のメッセージを返します。 また、タービンによって表される収益機会とタービンの修復コストも返されます。

1. 関数をテストするには、 **[テスト]** を選択し、 **[入力]** タブを選択します。 **[Body]\(本体\)** に次の入力を入力し、 **[実行]** を選択します。

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    :::image type="content" source="media/functions-openapi-definition/test-function.png" alt-text="Azure portal で関数をテストする":::

    次の出力が **[出力]** タブに返されます。

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

これで、応急修復のコスト効率性を確認する関数が作成されました。 次は、関数アプリの OpenAPI 定義を生成します。

## <a name="generate-the-openapi-definition"></a>OpenAPI 定義を生成する

OpenAPI 定義を生成するには:

1. 関数アプリを選択した後、左側のメニューから **[API Management]** を選択し、 **[API Management]** で **[新規作成]** を選択します。

    :::image type="content" source="media/functions-openapi-definition/select-all-settings-openapi.png" alt-text="[API Management] を選択する":::


1. 次の表に指定されている API Management の設定を使用してください。

    | 設定      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名前** | グローバルに一意の名前 | お使いの関数アプリの名前に基づいて名前が生成されます。 |
    | **サブスクリプション** | 該当するサブスクリプション | この新しいリソースが作成されるサブスクリプション。 |  
    | **[リソース グループ](../azure-resource-manager/management/overview.md)** |  myResourceGroup | お使いの関数アプリと同じリソース。自動的に設定されます。 |
    | **場所** | 米国西部 | 場所には [米国西部] を選びます。 |
    | **組織名** | Contoso | 開発者ポータルとメール通知で使用する組織の名前。 |
    | **管理者のメール アドレス** | ご自分のメール アドレス | API Management からのシステム通知を受信したメール アドレス。 |
    | **価格レベル** | 従量課金 | 従量課金レベルはすべてのリージョンで利用できるわけではありません。 価格の詳細については、[API Management の価格に関するページ](https://azure.microsoft.com/pricing/details/api-management/)を参照してください。 |

    ![新しい API Management サービスを作成する](media/functions-openapi-definition/new-apim-service-openapi.png)

1. **[作成]** を選択して、API Management インスタンスを作成します。これには数分かかる場合があります。

1. Azure によってインスタンスが作成されると、ページ上の **[Application Insights を有効にする]** オプションが有効になります。 これを選択して、ログを関数アプリケーションと同じ場所に送信し、 **[API のリンク]** を選択します。

1. **[Azure Functions のインポート]** が開き、**TurbineRepair** 関数が強調表示されます。 **[選択]** を選択して続行します。

    ![API Management に Azure Functions をインポートする](media/functions-openapi-definition/import-function-openapi.png)

1. **[関数アプリから作成する]** ページで、既定値をそのまま使用して **[作成]** を選択します。

    :::image type="content" source="media/functions-openapi-definition/create-function-openapi.png" alt-text="[関数アプリから作成する]":::

    Azure によって、関数の API が作成されます。

## <a name="test-the-api"></a>API をテストする

OpenAPI 定義を使用する前に、API が動作することを確認する必要があります。

1. 関数アプリのページで、 **[API Management]** を選択します。 **[テスト]** タブを選択し、 **[POST TurbineRepair]\(TurbineRepair を POST\)** を選択します。 

1. **[要求本文]** に次のコードを入力します。

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. **[送信]** を選択した後、**HTTP 応答** を確認します。

    :::image type="content" source="media/functions-openapi-definition/test-function-api-openapi.png" alt-text="関数 API をテストする":::

## <a name="download-the-openapi-definition"></a>OpenAPI 定義をダウンロードする

API が意図したとおりに動作する場合は、OpenAPI 定義をダウンロードできます。

1. ページの上部にある **[OpenAPI 定義をダウンロードする]** を選択します。
   
   ![OpenAPI 定義のダウンロード](media/functions-openapi-definition/download-definition.png)

2. ダウンロードした JSON ファイルを保存し、これを開きます。 定義を確認します。

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次のステップ

API Management の統合を使って、関数の OpenAPI 定義を生成しました。 ポータルの API Management で定義を編集できるようになります。 また、[API Management についてさらに学習する](../api-management/api-management-key-concepts.md)こともできます。

> [!div class="nextstepaction"]
> [API Management で OpenAPI 定義を編集する](../api-management/edit-api.md)
