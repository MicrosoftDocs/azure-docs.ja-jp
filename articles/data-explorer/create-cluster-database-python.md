---
title: クイック スタート:Python を使用して Azure Data Explorer クラスターとデータベースを作成する
description: Python を使用して Azure Data Explorer クラスターとデータベースを作成する方法を学習します。
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/25/2019
ms.openlocfilehash: 408b34db16f0d6d22340f0483b90ce5d72ffa613
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045203"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Python を使用して Azure Data Explorer クラスターとデータベースを作成する

> [!div class="op_single_selector"]
> * [ポータル](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure Data Explorer は、アプリケーション、Web サイト、IoT デバイスなどからの大量のデータ ストリーミングをリアルタイムに分析するためのフル マネージドのデータ分析サービスです。 Azure Data Explorer を使用するには、最初にクラスターを作成し、そのクラスター内に 1 つまたは複数のデータベースを作成します。 その後、クエリを実行できるように、データをデータベースに取り込み (読み込み) ます。 このクイック スタートでは、Python を使用して、クラスターとデータベースを 1 つずつ作成します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="install-python-package"></a>Python パッケージのインストール

Azure Data Explorer (Kusto) 向けの Python パッケージをインストールするには、Python をパスに設定した状態でコマンド プロンプトを開きます。 次のコマンドを実行します。

```
pip install azure-mgmt-kusto
```

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターを作成する

1. 次のコマンドを使用して、クラスターを作成します。

    ```Python
    from azure.mgmt.kusto.kusto_management_client import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku

    credentials = xxxxxxxxxxxxxxx
    
    subscription_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx'
    location = 'Central US'
    sku = 'D13_v2'
    capacity = 5
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku, capacity=capacity))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | cluster_name | *mykustocluster* | クラスターの任意の名前。|
   | sku | *D13_v2* | クラスターに使用される SKU。 |
   | resource_group_name | *testrg* | クラスターが作成されるリソース グループの名前。 |

    使用できる省略可能なパラメーターが他にも存在します (クラスターの容量など)。
    
1. [*資格情報*](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python)を設定します。

1. クラスターが正常に作成されたかどうかを確認するには、次のコマンドを実行します。

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

結果に値が `Succeeded` の `provisioningState` が含まれている場合、クラスターは正常に作成されています。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターでデータベースを作成する

1. 次のコマンドを使用して、データベースを作成します。

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = Database(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | cluster_name | *mykustocluster* | データベースの作成先となるクラスターの名前。|
   | database_name | *mykustodatabase* | データベースの名前。|
   | resource_group_name | *testrg* | クラスターが作成されるリソース グループの名前。 |
   | soft_delete_period | *3650 days, 0:00:00* | データをクエリに使用できるようにしておく時間。 |
   | hot_cache_period | *3650 days, 0:00:00* | データをキャッシュに保持する時間。 |

1. 次のコマンドを実行して、作成したデータベースを確認します。

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

クラスターとデータベースが作成されました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

* 他のクイック スタートやチュートリアルを行う場合は、作成したリソースをそのままにします。
* リソースをクリーンアップするには、クラスターを削除します。 クラスターを削除するときに、その中に含まれるデータベースもすべて削除されます。 クラスターを削除するには次のコマンドを使います。

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [クイック スタート:Azure Data Explorer の Python ライブラリを使用してデータを取り込む](python-ingest-data.md)
