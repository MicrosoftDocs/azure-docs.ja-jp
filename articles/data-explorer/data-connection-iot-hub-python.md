---
title: Python を使用して Azure Data Explorer 用に IoT Hub データ接続を作成する
description: この記事では、Python を使用して Azure Data Explorer 用に IoT Hub データ接続を作成する方法について学習します。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1e6cd94e18382b3e66fc6a9c284539bc178e199c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031044"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>Python を使用して Azure Data Explorer 用に IoT Hub データ接続を作成する (プレビュー)

> [!div class="op_single_selector"]
> * [ポータル](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 Azure Data Explorer では、BLOB コンテナーに書き込まれた Event Hubs、IoT Hub、BLOB からのインジェスト (データの読み込み) を提供します。 この記事では、Python を使用して Azure Data Explorer 用に IoT Hub データ接続を作成します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

* [クラスターとデータベース](create-cluster-database-csharp.md)を作成します

* [テーブルと列のマッピング](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)を作成します

* [データベースとテーブルのポリシー](database-table-policies-csharp.md) (オプション) を設定します

* [共有アクセス ポリシーを構成して IoT ハブ](ingest-data-iot-hub.md#create-an-iot-hub)を作成します

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>IoT Hub データ接続を追加する 

次の例は、IoT Hub データ接続をプログラムを使用して追加する方法を示しています。 Azure portal を使用して Iot Hub データ接続を追加する方法については、「[Azure Data Explorer テーブルを IoT ハブに接続する](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub)」を参照してください。

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import IotHubDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";
data_connection_name = "myeventhubconnect";
#The IoT hub that is created as part of the Prerequisites
iot_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
shared_access_policy_name = "iothubforread";
consumer_group = "$Default";
location = "Central US";
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents";
mapping_rule_name = "StormEvents_CSV_Mapping";
data_format = "csv";

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=IotHubDataConnection(iot_hub_resource_id=iot_hub_resource_id, shared_access_policy_name=shared_access_policy_name, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**設定** | **推奨値** | **フィールドの説明**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | テナント ID。 ディレクトリ ID とも呼ばれます。|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | リソースの作成に使用するサブスクリプション ID。|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント ID。|
| client_secret | *xxxxxxxxxxxxxx* | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント シークレット。 |
| resource_group_name | *testrg* | ご利用のクラスターを含むリソース グループの名前。|
| cluster_name | *mykustocluster* | ご利用のクラスターの名前。|
| database_name | *mykustodatabase* | ご利用のクラスター内のターゲット データベースの名前。|
| data_connection_name | *myeventhubconnect* | データ接続の任意の名前。|
| table_name | *StormEvents* | ターゲット データベース内のターゲット テーブルの名前。|
| mapping_rule_name | *StormEvents_CSV_Mapping* | ターゲット テーブルに関連付けられている列マッピングの名前。|
| data_format | *csv* | メッセージのデータ形式。|
| iot_hub_resource_id | *リソース ID* | インジェスト用のデータを保持している IoT ハブのリソース ID。|
| shared_access_policy_name | *iothubforread* | IoT Hub に接続するデバイスとサービスのアクセス許可を定義する共有アクセス ポリシーの名前。 |
| consumer_group | *$Default* | ご利用のイベント ハブのコンシューマー グループ。|
| location | *米国中部* | データ接続リソースの場所。|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]