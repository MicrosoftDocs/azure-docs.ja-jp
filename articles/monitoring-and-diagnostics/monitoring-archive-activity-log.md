---
title: "Azure アクティビティ ログのアーカイブ | Microsoft Docs"
description: "ストレージ アカウントの長期保持に関する Azure アクティビティ ログをアーカイブする方法を説明します。"
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: aaa162df8a6cd60cb174242e6a353439f2da58b4
ms.openlocfilehash: eb3a0ad811a4286df1bac963904bd9154c0ccfa3


---
# <a name="archive-the-azure-activity-log"></a>Azure アクティビティ ログのアーカイブ
この記事では、Azure Portal、PowerShell コマンドレット、またはクロス プラットフォーム CLI を使用して、ストレージ アカウントで [**Azure アクティビティ ログ**](monitoring-overview-activity-logs.md)をアーカイブする方法について説明します。 このオプションは、監査、静的分析、またはバックアップに対して (保持ポリシーを完全に制御して) 90 日よりも長いアクティビティ ログを保持する場合に便利です。 90 日以下でイベントを保持する必要があるだけの場合は、ストレージ アカウントにアーカイブを設定する必要はありません。アーカイブを有効にしなければ、アクティビティ ログのイベントは Azure プラットフォームに90 日間保持されるためです。

## <a name="prerequisites"></a>前提条件
開始する前に、アクティビティ ログのアーカイブ先の[ストレージ アカウントを作成](../storage/storage-create-storage-account.md#create-a-storage-account)する必要があります。 既存のストレージ アカウントを使用しないことを強くお勧めします。既存のストレージ アカウントには、監視データへのアクセスをさらに制御するために保存されている他の非監視データがあります。 ただし、診断ログとメトリックもストレージ アカウントにアーカイブする場合は、中央の場所にすべての監視データを保持するために、アクティビティ ログのそのストレージ アカウントも使用するのが適切であることがあります。 使用するストレージ アカウントは、BLOB ストレージ アカウントではなく、一般的な目的のストレージ アカウントである必要があります。 設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセスを持っている限り、ストレージ アカウントは、ログを出力するのと同じサブスクリプションに属している必要はありません。

## <a name="log-profile"></a>ログ プロファイル
以下の方法のいずれかを使用して、アクティビティ ログをアーカイブするには、サブスクリプションに **ログ プロファイル** を設定します。 ログ プロファイルは、保存またはストリーミングされたイベントの種類と、ストレージ アカウントまたはイベント ハブの出力の種類を定義します。 また、ストレージ アカウントに格納されたイベントの保持ポリシー (保持する日数) も定義します。 保持ポリシーが&0; に設定されている場合は、イベントが無制限に保存されます。 それ以外の場合は、1 ～ 2,147, 483,647 の範囲の任意の値に設定できます。 保持ポリシーは日単位で適用されるため、その日の終わり (UTC) に、保持ポリシーの期間を超えることになるログは削除されます。 たとえば、保持ポリシーが&1; 日の場合、その日が始まった時点で、一昨日のログは削除されます。 [ログ プロファイルの詳細については、こちらを参照してください](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles)。 

## <a name="archive-the-activity-log-using-the-portal"></a>ポータルを使用したアクティビティ ログのアーカイブ
1. ポータルで、左側のナビゲーションの **[アクティビティ ログ]** リンクをクリックします。 アクティビティ ログのリンクが表示されない場合は、最初に **[More Services (詳細なサービス)]** リンクをクリックします。
   
    ![[アクティビティ ログ] ブレードに移動します。](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. ブレードの上部にある、 **[エクスポート]**をクリックします。
   
    ![[エクスポート] ボタンをクリックします。](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. 表示されるブレードで、 **[Export to a storage account (ストレージ アカウントへのエクスポート)]** チェック ボックスをオンにし、ストレージ アカウントを選択します。
   
    ![ストレージ アカウントを設定します。](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. スライダーまたはテキスト ボックスを使用して、アクティビティ ログのイベントをストレージ アカウント内で保持する必要がある日数を定義します。 データがストレージ アカウントに無期限に保持されるようにする場合は、この数を&0; に設定します。
5. [ **Save**] をクリックします。

## <a name="archive-the-activity-log-via-powershell"></a>PowerShell を使用したアクティビティ ログのアーカイブ
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| プロパティ | 必須 | Description |
| --- | --- | --- |
| StorageAccountId |なし |アクティビティ ログの保存先となるストレージ アカウントのリソース ID。 |
| 場所 |はい |アクティビティ ログ イベントを収集するリージョンのコンマ区切りリスト。 [このページにアクセス](https://azure.microsoft.com/en-us/regions)して、または [Azure Management REST API](https://msdn.microsoft.com/library/azure/gg441293.aspx) を使用して、すべてのリージョンの一覧を表示できます。 |
| RetentionInDays |はい |イベントを保持する日数。1 ～2,147,483,647 の範囲。 値が&0; の場合、ログは無期限に (いつまでも) 保存されます。 |
| カテゴリ |はい |収集するイベント カテゴリのコンマ区切りリスト。 指定できる値は、Write、Delete、Action です。 |

## <a name="archive-the-activity-log-via-cli"></a>CLI を使用したアクティビティ ログのアーカイブ
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| プロパティ | 必須 | Description |
| --- | --- | --- |
| name |はい |ログ プロファイルの名前。 |
| storageId |なし |アクティビティ ログの保存先となるストレージ アカウントのリソース ID。 |
| 場所 |はい |アクティビティ ログ イベントを収集するリージョンのコンマ区切りリスト。 [このページにアクセス](https://azure.microsoft.com/en-us/regions)して、または [Azure Management REST API](https://msdn.microsoft.com/library/azure/gg441293.aspx) を使用して、すべてのリージョンの一覧を表示できます。 |
| RetentionInDays |はい |イベントを保持する日数。1 ～2,147,483,647 の範囲。 値が&0; の場合、ログは無期限に (いつまでも) 保存されます。 |
| categories |はい |収集するイベント カテゴリのコンマ区切りリスト。 指定できる値は、Write、Delete、Action です。 |

## <a name="storage-schema-of-the-activity-log"></a>アクティビティ ログのストレージ スキーマ
アーカイブの設定後、アクティビティ ログ イベントが発生するとすぐに、ストレージ コンテナーは、ストレージ アカウントに作成されます。 コンテナー内の BLOB は、アクティビティ ログおよび診断ログ全体で同じ形式に従います。 これらのBLOB の構造は次のとおりです。

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{サブスクリプション ID}/y={4 桁の年数値}/m={2 桁の月数値}/d={2 桁の日数値}/h={2 桁の 24時制の時間数値}/m=00/PT1H.json
> 
> 

たとえば、BLOB の名前は次のようになります。

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
> 
> 

各 PT1H.json BLOB には、BLOB の URL で指定された時間内に発生したイベントの JSON BLOB が含まれます (例: h =&12;)。 現在の時間内にイベントが発生すると、PT1H.json ファイルにイベントが追加されます。 分の値 (m = 00) は常に 00 です。アクティビティ ログ イベントが個々の BLOB に 1 時間ごとに分類されるためです。

PT1H.json ファイル内では、各イベントは、この形式に従って “レコード” 配列で保存されます。

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
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
| カテゴリ |アクションのカテゴリ。例:  Write、Read、Action |
| resultType |結果の種類。例:  Success、Failure、Start |
| resultSignature |リソースの種類によって異なります。 |
| durationMs |操作時間 (ミリ秒) |
| callerIpAddress |操作、UPN 要求、または可用性に基づく SPN 要求を実行したユーザーの IP アドレス。 |
| correlationId |通常は文字列形式の GUID。 correlationId を共有するイベントは、同じ uber アクションに属します。 |
| ID |承認および要求を記述する JSON BLOB。 |
| authorization |イベントの RBAC プロパティの BLOB。 通常は、"action"、"role"、"scope" の各プロパティが含まれます。 |
| level |イベントのレベル。 次の値のいずれか: “Critical”、“Error”、“Warning”、“Informational” and “Verbose” |
| location |発生した場所のリージョン (またはグローバル)。 |
| プロパティ |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。 |

> [!NOTE]
> プロパティとそのプロパティの使用方法については、リソースによって異なることがあります。
> 
> 

## <a name="next-steps"></a>次のステップ
* [分析のための BLOB のダウンロード](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
* [アクティビティ ログの Event Hubs へのストリーム](monitoring-stream-activity-logs-event-hubs.md)
* [詳細については、アクティビティ ログに関するセクションをご覧ください](monitoring-overview-activity-logs.md)




<!--HONumber=Dec16_HO2-->


