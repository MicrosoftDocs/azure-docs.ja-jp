---
title: クイック スタート:C# を使用して Azure Data Explorer クラスターとデータベースを作成する
description: C# を使用して Azure Data Explorer クラスターとデータベースを作成する方法を学習します
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/25/2019
ms.openlocfilehash: 1489048619fa8d650b30659a00ae7679c8d5109d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050698"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>C# を使用して Azure Data Explorer クラスターとデータベースを作成する

> [!div class="op_single_selector"]
> * [ポータル](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure Data Explorer は、アプリケーション、Web サイト、IoT デバイスなどからの大量のデータ ストリーミングをリアルタイムに分析するためのフル マネージドのデータ分析サービスです。 Azure Data Explorer を使用するには、最初にクラスターを作成し、そのクラスター内に 1 つまたは複数のデータベースを作成します。 その後、クエリを実行できるように、データをデータベースに取り込み (読み込み) ます。 このクイック スタートでは、C# を使用して、クラスターとデータベースを 1 つずつ作成します。

## <a name="prerequisites"></a>前提条件

* Visual Studio 2017 をインストールしていない場合は、**無料**の [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="install-c-nuget"></a>C# nuget をインストールする

1. [Azure Data Explorer (Kusto) NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)をインストールします。

1. 認証用に [Microsoft.IdentityModel.Clients.ActiveDirectory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)をインストールします。

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターを作成する

1. 次のコードを使用してクラスターを作成します。

    ```C#-interactive
    string resourceGroupName = "testrg";    
    string clusterName = "mykustocluster";
    string location = "Central US";
    AzureSku sku = new AzureSku("D13_v2", 5);
    Cluster cluster = new Cluster(location, sku);
    
    ar authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantName}");
    var credential = new ClientCredential(clientId: "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx", clientSecret: "xxxxxxxxxxxxxx");
    var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential).Result;
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
     
    KustoManagementClient KustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    };

    KustoManagementClient.Clusters.CreateOrUpdate(resourceGroupName, clusterName, cluster);
    ```

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | clusterName | *mykustocluster* | クラスターの任意の名前。|
   | sku | *D13_v2* | クラスターに使用される SKU。 |
   | resourceGroupName | *testrg* | クラスターが作成されるリソース グループの名前。 |

    使用できる省略可能なパラメーターが他にも存在します (クラスターの容量など)。

1. [資格情報](https://docs.microsoft.com/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)を設定します。

1. クラスターが正常に作成されたかどうかを確認するには、次のコマンドを実行します。

    ```C#-interactive
    KustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

結果に値が `Succeeded` の `ProvisioningState` が含まれている場合、クラスターは正常に作成されています。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターでデータベースを作成する

1. 次のコードを使用してデータベースを作成します。

    ```c#-interactive
    TimeSpan hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    TimeSpan softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    string databaseName = "mykustodatabase";
    Database database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);
    
    KustoManagementClient.Databases.CreateOrUpdate(resourceGroupName, clusterName, databaseName, database);
    ```

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | clusterName | *mykustocluster* | データベースの作成先となるクラスターの名前。|
   | databaseName | *mykustodatabase* | データベースの名前。|
   | resourceGroupName | *testrg* | クラスターが作成されるリソース グループの名前。 |
   | softDeletePeriod | *3650:00:00:00* | データをクエリに使用できるようにしておく時間。 |
   | hotCachePeriod | *3650:00:00:00* | データをキャッシュに保持する時間。 |

2. 次のコマンドを実行して、作成したデータベースを確認します。

    ```c#-interactive
    KustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

クラスターとデータベースが作成されました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

* 他のクイック スタートやチュートリアルを行う場合は、作成したリソースをそのままにします。
* リソースをクリーンアップするには、クラスターを削除します。 クラスターを削除するときに、その中に含まれるデータベースもすべて削除されます。 クラスターを削除するには次のコマンドを使います。

    ```C#-interactive
    KustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [クイック スタート:Azure Data Explorer .NET Standard SDK (プレビュー) を使用してデータを取り込む](net-standard-ingest-data.md)
