---
title: "Azure 診断ログのアーカイブ | Microsoft Docs"
description: "ストレージ アカウントの長期保持に関する Azure 診断ログをアーカイブする方法を説明します。"
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 3a55c73f-2ef3-45f3-8956-bcf9c0cb7e05
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 3e82377c36da1563931bc0301daa4534e6f82377
ms.openlocfilehash: e3413236445f3f6034a228dc93624d3fdc9b87ad


---
# <a name="archive-azure-diagnostic-logs"></a>Azure 診断ログのアーカイブ
この記事では、Azure Portal や PowerShell コマンドレット、CLI、REST API を使用し、ストレージ アカウントで [Azure 診断ログ](monitoring-overview-of-diagnostic-logs.md) をアーカイブする方法について説明します。 この方法は、監査や静的分析、バックアップなどを目的に任意のリテンション期間ポリシーで診断ログを保存したい場合に活用できます。 設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセスを持っている限り、ストレージ アカウントはログを出力するリソースと同じサブスクリプションに属している必要はありません。

## <a name="prerequisites"></a>前提条件
開始する前に、診断ログのアーカイブ先となる[ストレージ アカウントを作成する](../storage/storage-create-storage-account.md#create-a-storage-account)必要があります。 既存のストレージ アカウントを使用しないことを強くお勧めします。既存のストレージ アカウントには、監視データへのアクセスをさらに制御するために保存されている他の非監視データがあります。 ただし、アクティビティ ログと診断メトリックもストレージ アカウントにアーカイブする場合は、中央の場所にすべての監視データを保持するために、診断ログのそのストレージ アカウントも使用するのが適切であることがあります。 使用するストレージ アカウントは、BLOB ストレージ アカウントではなく、一般的な目的のストレージ アカウントである必要があります。

## <a name="diagnostic-settings"></a>診断設定
以下のいずれかの方法で診断ログをアーカイブするには、特定のリソースの **[診断設定]** を定義する必要があります。 リソースの診断設定では、保存またはストリーミングの対象となるログのカテゴリと出力 (ストレージ アカウントまたはイベント ハブ) を定義します。 また、ストレージ アカウントに格納される各ログ カテゴリのイベントに関して、リテンション期間ポリシー (保持する日数) を定義します。 リテンション期間ポリシーが&0; に設定されている場合は、各ログ カテゴリのイベントが無制限に (つまり、いつまでも) 保存されます。 そうでない場合は、リテンション期間ポリシーを 1 ～ 2,147, 483,647 までの範囲の任意の日数にすることができます。 [診断設定の詳細については、こちらを参照してください](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)。 保持ポリシーは日単位で適用されるため、その日の終わり (UTC) に、保持ポリシーの期間を超えることになるログは削除されます。 たとえば、保持ポリシーが&1; 日の場合、その日が始まった時点で、一昨日のログは削除されます。

## <a name="archive-diagnostic-logs-using-the-portal"></a>ポータルを使用して診断ログをアーカイブする
1. ポータルから、診断ログのアーカイブを有効にするリソースのブレードをクリックします。
2. リソース設定メニューの **[監視]** セクションで **[診断]** を選択します。
   
    ![Monitoring section of resource menu](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-sec.png)
3. **[ストレージ アカウントにエクスポート]**のチェック ボックスをオンにし、ストレージ アカウントを選択します。 必要に応じて **[リテンション期間 (日数)]** スライダーを使用し、ログを保持する日数を設定します。 リテンション期間を&0; にすると、ログが無期限に保存されます。
   
    ![Diagnostic Logs blade](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-blade.png)
4. [ **Save**] をクリックします。

新しいイベント データが生成されるとすぐに、診断ログがストレージ アカウントにアーカイブされます。

## <a name="archive-diagnostic-logs-via-the-powershell-cmdlets"></a>PowerShell コマンドレットを使用して診断ログをアーカイブする
```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| プロパティ | 必須 | Description |
| --- | --- | --- |
| ResourceId |はい |診断設定の対象となるリソースの ID。 |
| StorageAccountId |いいえ |診断ログの保存先となるストレージ アカウントのリソース ID。 |
| カテゴリ |いいえ |有効にするログ カテゴリのコンマ区切りのリスト。 |
| 有効 |はい |このリソースに対する診断が有効であるか無効であるかを示すブール値。 |
| RetentionEnabled |いいえ |このリソースに対するリテンション期間ポリシーが有効であるか無効であるかを示すブール値。 |
| RetentionInDays |なし |イベントを保持する日数。1 ～2,147,483,647 の範囲。 値が&0; の場合、ログは無期限に保存されます。 |

## <a name="archive-diagnostic-logs-via-the-cross-platform-cli"></a>クロスプラットフォーム CLI を使用して診断ログをアーカイブする
```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| プロパティ | 必須 | Description |
| --- | --- | --- |
| ResourceId |はい |診断設定の対象となるリソースの ID。 |
| storageId |いいえ |診断ログの保存先となるストレージ アカウントのリソース ID。 |
| カテゴリ |いいえ |有効にするログ カテゴリのコンマ区切りのリスト。 |
| 有効 |はい |このリソースに対する診断が有効であるか無効であるかを示すブール値。 |

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>REST API を使用して診断ログをアーカイブする
Azure Monitor REST API を使用した診断設定のセットアップ方法については、[こちらのドキュメント](https://msdn.microsoft.com/library/azure/dn931931.aspx)を参照してください。

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>ストレージ アカウントにおける診断ログのスキーマ
アーカイブの設定後、有効にしたいずれかのログ カテゴリのイベントが発生するとすぐ、ストレージ アカウントにストレージ コンテナーが作成されます。 コンテナー内の BLOB は、診断ログおよびアクティビティ ログ全体で同じ形式に従います。 これらのBLOB の構造は次のとおりです。

> insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
> 
> 

または、もっと単純に次のような形式が使用されます。

> insights-logs-{log category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
> 
> 

たとえば、BLOB の名前は次のようになります。

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
> 
> 

各 PT1H.json BLOB には、BLOB の URL で指定された時間内に発生したイベントの JSON BLOB が含まれます (例: h =&12;)。 現在の時間内にイベントが発生すると、PT1H.json ファイルにイベントが追加されます。 分の値 (m = 00) は常に 00 です。診断ログ イベントが個々の BLOB に 1 時間ごとに分類されるためです。

PT1H.json ファイル内では、各イベントは、この形式に従って “レコード” 配列で保存されます。

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| 要素名 | Description |
| --- | --- |
| time |イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| ResourceId |影響を受けるリソースのリソース ID。 |
| operationName |操作の名前。 |
| カテゴリ |イベントのログ カテゴリ。 |
| プロパティ |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。 |

> [!NOTE]
> プロパティとそのプロパティの使用方法については、リソースによって異なることがあります。
> 
> 

## <a name="next-steps"></a>次のステップ
* [分析のための BLOB のダウンロード](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
* [診断ログを Event Hubs にストリーミングする](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [診断ログの詳細についてはこちら](monitoring-overview-of-diagnostic-logs.md)




<!--HONumber=Dec16_HO2-->


