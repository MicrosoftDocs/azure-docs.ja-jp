---
title: ログ取り込み用の顧客所有のストレージ アカウント
description: 独自のストレージ アカウントを使用して、Azure Monitor の Log Analytics ワークスペースにログ データを取り込みます。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2020
ms.openlocfilehash: a14f7ca3e5a7b291e430db6ea536edc5396b5448
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318897"
---
# <a name="customer-owned-storage-accounts-for-log-ingestion-in-azure-monitor"></a>Azure Monitor におけるログ取り込み用の顧客所有のストレージ アカウント

Azure Monitor では、[カスタム ログ](data-sources-custom-logs.md)や [Azure ログ](./diagnostics-extension-logs.md)などの一部のデータ型の取り込みプロセスでストレージ アカウントを使用します。 取り込みプロセス中、ログはまずストレージ アカウントに送信され、その後 Log Analytics または Application Insights に取り込まれます。 取り込み中にデータを制御する場合は、サービスで管理されたストレージの代わりに独自のストレージ アカウントを使用します。 独自のストレージ アカウントを使用すると、取り込み中にログのアクセス、コンテンツ、暗号化、リテンション期間を制御できます。 これは、"独自ストレージの持ち込み" または "BYOS" と呼ばれています。 

BYOS を必要とするシナリオの 1 つが、 Private Link によるネットワークの分離です。 VNet を使用する場合、ネットワークの分離は多くの場合要件であり、パブリック インターネットへのアクセスは制限されています。 このような場合、ログ取り込みのために Azure Monitor サービス ストレージにアクセスすることは完全にブロックされるか、または不適切な方法と考えられます。 代わりに、VNet 内の顧客所有のストレージ アカウント経由で取り込むか、または簡単にアクセスできるログを取り込む必要があります。

もう 1 つのシナリオは、カスタマー マネージド キー (CMK) を使用したログの暗号化です。 ユーザーはログを保存するクラスターで CMK を使用して、ログに記録されたデータを暗号化できます。 同じキーを使用して、取り込みプロセス中にログを暗号化することもできます。

## <a name="data-types-supported"></a>サポートされているデータ型

ストレージ アカウントから取り込まれるデータ型には、次のものがあります。 これらの型の取り込みの詳細については、「[Azure Diagnostics 拡張機能から Azure Monitor ログにデータを収集する](./diagnostics-extension-logs.md)」を参照してください。

| Type | テーブル情報 |
|:-----|:------------------|
| IIS ログ | Blob: wad-iis-logfiles|
|Windows イベント ログ | 表: WADWindowsEventLogsTable |
| syslog | 表: LinuxsyslogVer2v0 |
| Windows ETW のログ | 表: WADETWEventTable|
| Service Fabric | 表: WADServiceFabricSystemEventTable <br/> WADServiceFabricReliableActorEventTable<br/> WADServiceFabricReliableServicEventTable |
| カスタム ログ | 該当なし |
| Azure Security Center のワトソン博士のダンプ ファイル | 該当なし|  

## <a name="storage-account-requirements"></a>ストレージ アカウントの要件 
ストレージ アカウントは、次の要件を満たしている必要があります。

- ストレージにログを書き込む VNet 上のリソースにアクセスできること。
- リンク先のワークスペースと同じリージョンにあること。
- Azure Monitor アクセスを許可する - ストレージ アカウントのアクセスを特定のネットワークに制限することを選択した場合は、必ず「*信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します*」の例外を許可してください。

## <a name="process-to-configure-customer-owned-storage"></a>顧客所有のストレージを構成するプロセス
独自のストレージ アカウントを取り込みに使用する基本的なプロセスは次のとおりです。

1. ストレージ アカウントを作成するか、既存のアカウントを選択します。
2. ストレージ アカウントを Log Analytics ワークスペースにリンクします。
3. 負荷とリテンション期間を確認してストレージを管理し、期待どおりに機能していることを確認します。

リンクの作成と削除に使用できる唯一の方法は、REST API を使用することです。 各プロセスに必要な特定の API 要求の詳細については、以下のセクションで説明します。

## <a name="command-line-and-rest-api"></a>コマンド ラインと REST API

### <a name="command-line"></a>コマンド ライン
リンクされたストレージ アカウントを作成および管理するには、[az monitor log-analytics workspace linked-storage](/cli/azure/monitor/log-analytics/workspace/linked-storage) を使用します。 このコマンドでは、ワークスペースからストレージ アカウントをリンクおよびリンク解除したり、リンクされたストレージ アカウントを一覧表示したりできます。

### <a name="request-and-cli-values"></a>要求と CLI の値

#### <a name="datasourcetype"></a>dataSourceType 

- AzureWatson - Azure Security Center のワトソン博士のダンプ ファイルには、この値を使用します。
- CustomLogs – 次のデータ型にはこの値を使用します。
  - カスタム ログ
  - IIS ログ
  - イベント (Windows)
  - Syslog (Linux)
  - ETW ログ
  - Service Fabric イベント
  - 評価データ  

#### <a name="storage_account_resource_id"></a>storage_account_resource_id
この値は、次の構造を使用します。

```
subscriptions/{subscriptionId}/resourcesGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName1}
```


### <a name="get-linked-storage-accounts-for-all-data-source-types"></a>すべてのデータソースの型のリンクされたストレージ アカウントを取得する

#### <a name="api-request"></a>API 要求

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Response

```json
{
    [
        {
            "properties":
            {
                "dataSourceType": "CustomLogs",
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_1>",
                    "<storage_account_resource_id_2>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
            "name": "CustomLogs",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        },
        {
            "properties":
            {
                "dataSourceType": " AzureWatson "
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_3>",
                    "<storage_account_resource_id_4>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/AzureWatson",
            "name": "AzureWatson",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        }
    ]
}
```


### <a name="get-linked-storage-accounts-for-a-specific-data-source-type"></a>特定のデータソースの型のリンクされたストレージ アカウントを取得する

#### <a name="api-request"></a>API 要求

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Response 

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs",
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
    "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
    "name": "CustomLogs",
    "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```

## <a name="create-or-modify-a-link"></a>リンクを作成または変更する

ストレージ アカウントをワークスペースにリンクすると、Log Analytics ではサービスによって所有されているストレージ アカウントの代わりにそのアカウントが使用されるようになります。 ストレージ アカウントの一覧を同時に登録したり、同じストレージ アカウントを複数のワークスペースに対して使用したりすることができます。

ワークスペースで VNet リソースと VNet 外のリソースの両方を処理する場合は、インターネットからのトラフィックを拒否していないことを確認する必要があります。 お使いのストレージには、ワークスペースと同じ設定が適用され、VNet 外のリソースで使用できるようになっている必要があります。 

### <a name="api-request"></a>API 要求

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

### <a name="payload"></a>ペイロード

```json
{
    "properties":
    {
        "storageAccountIds  " : 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    }
}
```

### <a name="response"></a>Response

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs"
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
"id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
"name": "CustomLogs",
"type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```


## <a name="unlink-a-storage-account"></a>ストレージ アカウントのリンクを解除する
取り込みにストレージ アカウントを使用することを停止する場合、または使用するワークスペースを置換する場合は、ワークスペースからストレージのリンクを解除する必要があります。

ワークスペースからすべてのストレージ アカウントのリンクを解除すると、取り込みはサービスで管理されたストレージ アカウントに依存することになります。 インターネットへのアクセスが制限されている VNet でエージェントが実行されている場合は、取り込みに失敗することが予想されます。 ワークスペースには、監視対象リソースからアクセスできるリンクされたストレージ アカウントが必要です。

ストレージ アカウントを削除する前に、格納されているすべてのデータがワークスペースに取り込まれていることを確認します。 予防策として、別のストレージをリンクした後も、ストレージ アカウントを使用可能な状態にしておきます。 すべてのコンテンツが取り込まれ、新しいデータが新しく接続したストレージ アカウントに書き込まれていることを確認してから削除します。


### <a name="api-request"></a>API 要求
```
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

## <a name="replace-a-storage-account"></a>ストレージ アカウントを置換する

取り込みに使用したストレージ アカウントを置き換えるには、最初に新しいストレージ アカウントのリンクを作成します。 ログ エージェントによって更新された構成が取得され、さらに、新しいストレージへのデータの送信を開始します。

次に、古いストレージ アカウントのリンクを解除して、エージェントから削除されたアカウントへの書き込みを停止します。 データがこのアカウントからすべて取り込まれるまで、取り込みプロセスでデータを読み取り続けます。 すべてのログが取り込まれるまでは、ストレージ アカウントを削除しないでください。

エージェントの構成は数分後に更新され、新しいストレージに切り替えられます。

## <a name="manage-storage-account"></a>ストレージ アカウントを管理する

### <a name="load"></a>[読み込み]

ストレージ アカウントでは、要求の調整が開始されるまでに、特定の量の読み取り要求と書き込み要求の負荷を処理できます。 調整はログの取り込みにかかる時間に影響するため、データが失われる可能性があります。 ストレージが過負荷になっている場合は、追加のストレージ アカウントを登録し、負荷をアカウント間で分散します。 

### <a name="related-charges"></a>関連料金

ストレージ アカウントは、格納されているデータの量、ストレージの種類、および冗長性の種類に応じて課金されます。 詳細については、「[ブロック BLOB の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)」と「[Table Storage の料金](https://azure.microsoft.com/pricing/details/storage/tables/)」に関するページを参照してください。

ワークスペースの登録済みストレージ アカウントが別のリージョンにある場合は、これらの「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」に従って、送信の料金が発生します。



## <a name="next-steps"></a>次のステップ

- プライベート リンクの設定に関する詳細については、「[Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する](private-link-security.md)」を参照してください。

