---
title: Azure リソース ログをストレージ アカウントにアーカイブする | Microsoft Docs
description: Azure リソース ログを長期保有するためにストレージ アカウントにアーカイブする方法を説明します。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 8a1802f0f24ba5ccad3ec1c45f3baa29dfe6909f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71263038"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Azure リソース ログをストレージ アカウントにアーカイブする
Azure の[リソース ログ](resource-logs-overview.md)からは、Azure リソースの内部操作で頻繁に見られるデータが豊富に提供されます。 この記事では、アーカイブ対象のデータを保持するための、Azure ストレージ アカウントへのリソース ログの収集について説明します。

## <a name="prerequisites"></a>前提条件
[Azure ストレージ アカウント](../../storage/common/storage-quickstart-create-account.md)をまだお持ちでない場合は、作成する必要があります。 設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセス権を持っている限り、ストレージ アカウントはログを送信するリソースと同じサブスクリプションに属している必要はありません。

監視データへのアクセスをさらに制御するために他の非監視データが格納されている、既存のストレージ アカウントは使用しないでください。 [アクティビティ ログ](activity-logs-overview.md)もストレージ アカウントにアーカイブする場合は、中央の場所にすべての監視データを保持するために、同じアカウントを使用することができます。

## <a name="create-a-diagnostic-setting"></a>診断設定の作成
既定では、リソース ログは収集されません。 Azure リソース用の診断設定を作成して、Azure ストレージ アカウントやその他の送信先でリソース ログを収集します。 詳細については、「[Azure でログとメトリックを収集するための診断設定を作成する](diagnostic-settings.md)」を参照してください。


## <a name="data-retention"></a>データの保持
ストレージ アカウントに格納される各ログ カテゴリのデータおよびメトリック データの保持日数をリテンション ポリシーで定義します。 リテンション ポリシーは、0 から 365 の範囲の任意の日数にすることができます。 リテンション ポリシーを 0 にした場合、そのログ カテゴリのイベントを無期限に格納することが指定されます。

保持ポリシーは日単位で適用されるため、その日の終わり (UTC) に、保持ポリシーの期間を超えることになるログは削除されます。 たとえば、保持ポリシーが 1 日の場合、その日が始まった時点で、一昨日のログは削除されます。 削除プロセスは午前 0 時 (UTC) に開始されますが、ストレージ アカウントからのログの削除には最大で 24 時間かかる可能性があるので注意してください。 


## <a name="schema-of-resource-logs-in-storage-account"></a>ストレージ アカウントのリソース ログのスキーマ

診断設定の作成後、有効にしたいずれかのログ カテゴリのイベントが発生するとすぐ、ストレージ アカウントにストレージ コンテナーが作成されます。 コンテナー内の BLOB には、次の名前付け規則が使用されます。

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

たとえば、ネットワーク セキュリティ グループの BLOB には、次のような名前を付けることができます。

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

各 PT1H.json BLOB には、BLOB の URL で指定された時間内に発生したイベントの JSON BLOB が含まれます (例: h = 12)。 現在の時間内にイベントが発生すると、PT1H.json ファイルにイベントが追加されます。 リソース ログ イベントは 1 時間ごとに個々の BLOB に分類されるため、分の値 (m = 00) は常に 00 です。

PT1H.json ファイル内では、各イベントは、次の形式で保存されます。 これには一般的な最上位スキーマを使用しますが、「[リソース ログのスキーマ](resource-logs-overview.md#resource-logs-schema)」で説明されているように、各 Azure サービスに固有です。

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> プラットフォーム ログは、[JSON Lines](http://jsonlines.org/) を使用して BLOB ストレージに書き込まれ、各イベントは 1 行で示され、改行文字は新しいイベントがあることを示します。 この形式は、2018 年 11 月に実装されました。 この日付より前は、「[ストレージ アカウントにアーカイブされている Azure Monitor プラットフォーム ログの形式変更のための準備](resource-logs-blob-format.md)」で説明されているように、ログはレコードの JSON 配列として BLOB ストレージに書き込まれていました。

## <a name="next-steps"></a>次の手順

* [分析のための BLOB のダウンロード](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Azure Monitor による Azure Active Directory ログのアーカイブ](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
* [リソース ログの詳細について読む](../../azure-monitor/platform/resource-logs-overview.md)

