---
title: システム割り当てマネージド ID を使用して Azure Cosmos DB にアクセスする方法
description: Azure Cosmos DB のキーにアクセスするように Azure Active Directory (Azure AD) システム割り当てマネージド ID (マネージド サービス ID) を構成する方法について説明します。
author: j-patrick
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 4d9845fad8c9013bd20499c45a8d1714e30e9dbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98927403"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>システム割り当てマネージド ID を使用して Azure Cosmos DB データにアクセスする
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、[マネージド ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) を使用して Azure Cosmos DB キーにアクセスするための、"*堅牢でキー ローテーションに依存しない*" ソリューションを設定します。 この記事の例では Azure Functions を使用しますが、マネージド ID をサポートする任意のサービスを使用できます。 

ここでは、Azure Cosmos DB キーをコピーしなくても Azure Cosmos DB データにアクセスできる関数アプリを作成する方法について説明します。 この関数アプリは、1 分ごとにウェイクアップし、これにより水族館の水槽の現在の気温が記録されます。 タイマーでトリガーされる関数アプリの設定方法については、「[Azure でタイマーによってトリガーされる関数を作成する](../azure-functions/functions-create-scheduled-function.md)」の記事を参照してください。

このシナリオを簡略化するために、[有効期限](./time-to-live.md)の設定は、古い温度ドキュメントをクリーンアップするように既に構成されています。 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>関数アプリにシステム割り当てマネージド ID を割り当てる

このステップでは、関数アプリにシステム割り当てマネージド ID を割り当てます。

1. [Azure portal](https://portal.azure.com/) で、**Azure 関数** ウィンドウを開き、関数アプリに移動します。 

1. **[プラットフォーム機能]**  >  **[ID]** タブの順に開きます。 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="関数アプリのプラットフォーム機能と ID オプションを示すスクリーンショット。":::

1. **[ID]** タブで、システム ID の **[状態]** を **[オン]** にし、 **[保存]** を選択します。 **[ID]** ウィンドウは次のようになります。  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="システム ID の [状態] が [オン] に設定されていることを示すスクリーンショット。":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Azure Cosmos アカウントへのアクセスを許可する

このステップでは、関数アプリのシステム割り当てマネージド ID にロールを割り当てます。 Azure Cosmos DB には、マネージド ID に割り当てることが可能な複数の組み込みロールがあります。 このソリューションでは、次の 2 つのロールを使用します。

|組み込みのロール  |説明  |
|---------|---------|
|[DocumentDB Account Contributor](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Azure Cosmos DB アカウントを管理できます。 読み取り/書き込みキーの取得を許可します。 |
|[Cosmos DB アカウントの閲覧者ロール](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Cosmos DB アカウントのデータを読み取ることができます。 読み取りキーの取得を許可します。 |

> [!IMPORTANT]
> Azure Cosmos DB でのロールベースのアクセス制御のサポートは、コントロール プレーン操作にのみ適用されます。 データ プレーン操作は、主キーまたはリソース トークンを使用してセキュリティで保護されています。 詳細については、「[データへのアクセスをセキュリティで保護する](secure-access-to-data.md)」の記事を参照してください。

> [!TIP] 
> ロールを割り当てるときは、必要なアクセス権のみを割り当ててください。 サービスがデータの読み取りのみを必要とする場合は、マネージド ID に **Cosmos DB アカウントの閲覧者** ロールを割り当てます。 最小特権アクセスの重要性についての詳細は、「[特権アカウントの公開時間を短縮する](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)」の記事を参照してください。

このシナリオでは、関数アプリによって水族館の気温が読み取られ、Azure Cosmos DB のコンテナーにそのデータが書き戻されます。 関数アプリによってデータを書き込む必要があるため、**DocumentDB アカウント共同作成者** ロールを割り当てる必要があります。 

### <a name="assign-the-role-using-azure-portal"></a>Azure portal を使用してロールを割り当てる

1. Azure portal にサインインし、Azure Cosmos DB アカウントに移動します。 **[アクセス管理 (IAM)]** ウィンドウ、 **[ロールの割り当て]** タブの順に開きます。

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="[アクセス制御] ウィンドウと [ロールの割り当て] タブを示すスクリーンショット。":::

1. **[+ 追加]**  >  **[ロール割り当ての追加]** の順に選択します。

1. **[ロールの割り当ての追加]** ウィンドウが右側に表示されます。

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="[ロールの割り当ての追加] ウィンドウを示すスクリーンショット。":::

   * **ロール**: **[DocumentDB Account Contributor]** を選択します。
   * **アクセスの割り当て先**: **[システム割り当てマネージド ID]** の選択のサブセクションで、 **[関数アプリ]** を選択します。
   * **Select**:このウィンドウには、サブスクリプション内の、 **[マネージド システム ID]** を持つすべての関数アプリが表示されます。 この場合は、 **[FishTankTemperatureService]** 関数アプリを選択します。 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="例が表示された [ロールの割り当ての追加] ウィンドウを示すスクリーンショット。":::

1. 関数アプリを選択したら、 **[保存]** を選択します。

### <a name="assign-the-role-using-azure-cli"></a>Azure CLI を使用してロールを割り当てる

Azure CLI を使用してロールを割り当てるには、Azure Cloud Shell を開き、次のコマンドを実行します。

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>プログラムを使用して Azure Cosmos DB キーにアクセスする

これで、Azure Cosmos DB のアクセス許可で **DocumentDB アカウント共同作成者** ロールが割り当てられた、システム割り当てマネージド ID を持つ関数アプリが作成されました。 次の関数アプリ コードでは、Azure Cosmos DB キーが取得され、CosmosClient オブジェクトが作成され、水族館の気温が取得されて Azure Cosmos DB に保存されます。

このサンプルでは、[List Keys API](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) を使用して、Azure Cosmos DB アカウント キーにアクセスします。

> [!IMPORTANT] 
> [Cosmos DB アカウントの閲覧者](#grant-access-to-your-azure-cosmos-account)ロールを割り当てるには、[List Read Only Keys API](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys) を使用する必要があります。 これにより、読み取り専用キーのみが設定されます。

List Keys API は `DatabaseAccountListKeysResult` オブジェクトを返します。 この型は C# ライブラリで定義されていません。 次のコードは、このクラスを実装する方法を示しています。  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

また、この例では、次のように定義されている "TemperatureRecord" という単純なドキュメントも使用します。

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

[Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) ライブラリを使用して、システム割り当てマネージド ID トークンを取得します。 トークンを取得する他の方法と、`Microsoft.Azure.Service.AppAuthentication` ライブラリの詳細については、[サービス間認証](/dotnet/api/overview/azure/service-to-service-authentication)に関する記事を参照してください。


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

これで、[関数アプリをデプロイする](../azure-functions/create-first-function-vs-code-csharp.md)準備が整いました。

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB と Azure Active Directory を使用した証明書ベースの認証](certificate-based-authentication.md)
* [Azure Key Vault を使用して Azure Cosmos DB キーをセキュリティで保護する](access-secrets-from-keyvault.md)
* [Azure Cosmos DB のセキュリティ ベースライン](security-baseline.md)