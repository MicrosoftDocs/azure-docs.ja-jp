---
title: Azure Monitor 診断ログの形式変更のための準備
description: Azure 診断ログでは、2018 年 11 月 1 日より追加 BLOB を使用するように移行されます。
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: ab5fba6bbbf6ade83c7699edec937ba02b222939
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370059"
---
# <a name="prepare-for-format-change-to-azure-monitor-diagnostic-logs-archived-to-a-storage-account"></a>ストレージ アカウントにアーカイブされている Azure Monitor 診断ログの形式変更のための準備

> [!WARNING]
> [Azure リソース診断ログまたはメトリックをリソース診断設定を使用してストレージ アカウントに](./../../azure-monitor/platform/archive-diagnostic-logs.md)、または[アクティビティ ログをログ プロファイルを使用してストレージ アカウントに](./../../azure-monitor/platform/archive-activity-log.md)送信する場合、ストレージ アカウントのデータの形式は、2018 年 11 月 1 日付より JSON Lines に変更されます。 以下の手順では、この変更による影響と、新しい形式に対応するためのツールの更新方法について説明します。 
>
> 

## <a name="what-is-changing"></a>変更点

Azure Monitor で、Azure ストレージ アカウント、Event Hubs 名前空間、または Azure Monitor の Log Analytics ワークスペースへのリソース診断データやアクティビティ ログ データの送信を可能にする機能が提供されます。 システム パフォーマンスの問題に対処するために、**2018 年 11 月 1 日の深夜 12 時 00 分 (UTC)** より、BLOB ストレージに送信されるログ データの形式が変更されます。 BLOB ストレージからデータを読み取っているツールがある場合は、新しいデータ形式を認識するようにツールを更新する必要があります。

* 2018 年 11 月 1 日木曜日の深夜 12 時 00 分 (UTC) より、BLOB 形式は [JSON Lines](http://jsonlines.org/) に変更されます。 つまり、各レコードは改行で区切られるようになり、外部のレコード配列や JSON レコード間のコンマはなくなります。
* すべてのサブスクリプションのすべての診断設定の BLOB 形式が一度に変更されます。 11 月 1 日に出力される最初の PT1H.json ファイルでは、この新しい形式が使用されます。 BLOB およびコンテナーの名前は変わりません。
* 今から 11 月 1 日までの間に診断設定が行われた場合、11 月 1 日までは現在の形式で引き続きデータが出力されます。
* この変更はすべてのパブリック クラウド リージョンで一度に行われます。 Azure 中国、Azure ドイツ、Azure Government クラウドではまだこの変更は行われません。
* この変更は次のデータの種類に影響します。
  * [Azure リソース診断ログ](./../../azure-monitor/platform/archive-diagnostic-logs.md) ([このリソースの一覧を参照してください](./../../azure-monitor/platform/diagnostic-logs-schema.md))
  * [診断設定でエクスポートされる Azure リソース メトリック](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)
  * [ログ プロファイルでエクスポートされる Azure アクティビティ ログ データ](./../../azure-monitor/platform/archive-activity-log.md)
* この変更は以下のものには影響しません。
  * ネットワーク フロー ログ
  * Azure Monitor を通じてまだ使用可能になっていない Azure サービス ログ (Azure App Service の診断ログ、ストレージ分析ログなど)
  * Azure 診断ログとアクティビティ ログの他の送信先 (Event Hubs、Log Analytics) へのルーティング

### <a name="how-to-see-if-you-are-impacted"></a>影響があるかどうかを確認する方法

この変更が影響するのは、以下の場合のみです。
1. リソース診断設定を使用して、Azure ストレージ アカウントにログ データを送信する。
2. ストレージ内のこれらのログの JSON 構造に依存しているツールがある。
 
Azure ストレージ アカウントにデータを送信しているリソース診断設定があるかどうかを確認するには、ポータルの **[監視]** セクションに移動し、**[診断設定]** をクリックして、**診断状態**が**有効**に設定されているリソースを確認します。

![Azure Monitor の [診断設定] ブレード](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

診断状態が有効に設定されている場合、そのリソースにはアクティブな診断設定があります。 リソースをクリックして、診断設定でデータがストレージ アカウントに送信されるかどうかを確認します。

![有効なストレージ アカウント](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

これらのリソース診断設定を使用して、ストレージ アカウントにデータを送信しているリソースがある場合、そのストレージ アカウント内のデータの形式はこの変更による影響を受けます。 これらのストレージ アカウントから動作するカスタム ツールがない限り、形式変更の影響はありません。

### <a name="details-of-the-format-change"></a>形式変更の詳細

Azure BLOB ストレージ内の PT1H.json ファイルの現在の形式では、JSON のレコード配列が使用されます。 ここでは、KeyVault ログ ファイルの例を示します。

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

新しい形式では [JSON Lines](http://jsonlines.org/) が使用され、各イベントは 1 行で示され、改行文字は新しいイベントがあることを示します。 変更後の PT1H.json ファイルでは、上記のサンプルは次のようになります。

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

この新しい形式では、Azure Monitor で[追加 BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) を使用してログ ファイルをプッシュすることができ、継続的に新しいイベント データを追加する場合により効率的です。

## <a name="how-to-update"></a>更新方法

さらに処理するためにこれらのログ ファイルを取り込むカスタム ツールがある場合にのみ、更新する必要があります。 外部のログ分析や SIEM ツールを利用する場合は、[代わりにイベント ハブを使用してこのデータを取り込む](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)ことをお勧めします。 多くのサービスからのログの処理と特定のログでの場所のブックマークという観点から、イベント ハブの統合は簡単です。

現在の形式と、上述の JSON Lines 形式の両方に対応するには、カスタム ツールを更新する必要があります。 これにより、新しい形式でデータの表示が開始されるときに、ご利用のツールで作業が中断されることはなくなります。

## <a name="next-steps"></a>次の手順

* [ストレージ アカウントへのリソース診断ログのアーカイブ](./../../azure-monitor/platform/archive-diagnostic-logs.md)について確認します。
* [ストレージ アカウントへのアクティビティ ログ データのアーカイブ](./../../azure-monitor/platform/archive-activity-log.md)について確認します。

