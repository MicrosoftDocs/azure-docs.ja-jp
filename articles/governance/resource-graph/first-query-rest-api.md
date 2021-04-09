---
title: クイック スタート:自分の初めての REST API クエリ
description: このクイックスタートでは、手順に従い、REST API 用に Resource Graph エンドポイントを呼び出し、ご自分の初めてのクエリを実行します。
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 670ceba95d937be278c44c34704cb844eead480d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920073"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-rest-api"></a>クイック スタート:REST API を使用して自分の初めて Resource Graph クエリを実行する

REST API で Azure Resource Graph を使用するには、まず使用可能な REST API を呼び出すツールがあることを確認します。 このクイックスタートでは、その後の、クエリを実行して、Azure Resource Graph REST API エンドポイントを呼び出して結果を取得するプロセスを説明します。

このプロセスを完了すると、REST API エンドポイントを呼び出すツールがあり、ご自分の初めての Resource Graph クエリを実行できるようになります。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>REST API で作業を開始する

REST API に慣れていない場合は、最初に「[Azure REST API Reference](/rest/api/azure/)」(Azure REST API リファレンス) を読んで、REST API の基本を理解してください (具体的には要求 URI と要求の本文)。 この記事では、これらの概念を使用して Azure Resource Graph を操作する方法を説明しており、それらの実践的な知識があることを前提としています。 [ARMClient](https://github.com/projectkudu/ARMClient) などのツールは認可を自動的に処理できるので、初めての方にお勧めします。

Azure Resource Graph の仕様については、[Azure Resource Graph REST API](/rest/api/azure-resourcegraph/) に関するページを参照してください。

### <a name="rest-api-and-powershell"></a>REST API と PowerShell

REST API の呼び出しを行うためのツールがまだない場合は、PowerShell を使うことを検討してください。 次のコード サンプルでは、Azure で認証を行うためのヘッダーを取得します。 認証ヘッダー (**ベアラー トークン** と呼ばれることもあります) を生成し、パラメーターまたは **要求本文** で接続する REST API の URI を提供します。

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

`$restUri` 変数の `{subscriptionId}` を置き換えて、サブスクリプションに関する情報を取得します。
`$response` 変数には `Invoke-RestMethod` コマンドレットの結果が保持されており、[ConvertFrom-json](/powershell/module/microsoft.powershell.utility/convertfrom-json) などのコマンドレットを使用して解析できます。 REST API サービス エンドポイントで **要求本文** が必要な場合は、JSON 形式の変数を `Invoke-RestMethod` の `-Body` パラメーターに提供します。

## <a name="run-your-first-resource-graph-query"></a>最初の Resource Graph クエリを実行する

ご自分の選択した環境に REST API ツールが追加されたので、簡単な Resource Graph クエリを試してみましょう。 このクエリでは、各リソースの **名前** と **リソースの種類** を使用して、最初の 5 つの Azure リソースが返されます。

各 REST API 呼び出しの要求本文には、自分固有の値に置き換える必要がある変数が使用されています。

- `{subscriptionID}` - サブスクリプション ID で置き換えます

1. REST API と `resources` エンドポイントを使用して、ご自分の初めての Azure Resource Graph クエリを実行します。

   - REST API URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - 要求本文

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5"
     }
     ```

   > [!NOTE]
   > このクエリ例では、`order by` などの並べ替え修飾子を指定していません。そのため、このクエリを複数回実行すると、要求ごとに、得られる一連のリソースが異なる可能性があります。

1. `resouces` エンドポイントへの呼び出しを更新し、**Name** プロパティで並べ替えられる (`order by`) ように **クエリ** を変更します。

   - REST API URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - 要求本文

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5 | order by name asc"
     }
     ```

   > [!NOTE]
   > 最初のクエリと同様に、このクエリを複数回実行すると要求あたり異なる一連のリソースを生成する可能性があります。 クエリ コマンドの順序が重要です。 この例では、`limit` の後に `order by` がきます。 このコマンドの順序によって、まずクエリ結果が制限され、次にその結果が並べ替えられます。

1. `resources` への呼び出しを更新し、最初にクエリを **Name** プロパティで並べ替え (`order by`)、次に上位 5 件の結果に制限 (`limit`) するように **クエリ** を変更します。

   - REST API URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - 要求本文

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | order by name asc | limit 5"
     }
     ```

最後のクエリを複数回実行した場合、環境内で何も変更がないと仮定すると、返される結果は変わらず、**Name** プロパティで並べ替えられますが、引き続き上位 5 件の結果に制限されます。

Azure Resource Graph に対する REST API 呼び出しの追加の例については、[Azure Resource Graph の REST の例](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#examples)に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

REST API では、ライブラリまたはモジュールをアンインストールする必要がありません。 呼び出しを行うためにインストールした、_ARMClient_ や _Postman_ などのツールが不要になった場合は、ツールをすぐにアンインストールできます。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Resource Graph REST API エンドポイントを呼び出し、ご自分のクエリを初めて実行しました。 Resource Graph 言語の詳細については、クエリ言語の詳細ページに進んでください。

> [!div class="nextstepaction"]
> [クエリ言語に関する詳細情報を入手します](./concepts/query-language.md)
