---
title: Azure アクティビティ ログ
description: Azure アクティビティ ログを表示し、それを Azure Monitor ログ、Azure Event Hubs、Azure Storage に送信します。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: e6fb2f09200e42f7ad7781716bb83ab418134509
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86516143"
---
# <a name="azure-activity-log"></a>Azure アクティビティ ログ
アクティビティ ログは、サブスクリプション レベルのイベントの分析情報を提供する Azure の[プラットフォーム ログ](platform-logs-overview.md)です。 これには、リソースが変更されたときや仮想マシンが起動されたときなどの情報が含まれます。 Azure portal でアクティビティ ログを表示したり、PowerShell と CLI を使用してエントリを取得したりできます。 その他の機能を使用するには、診断設定を作成して、[Azure Monitor ログ](data-platform-logs.md)、Azure Event Hubs (Azure の外部に転送するため)、または Azure Storage (アーカイブのため) にアクティビティログを送信する必要があります。 この記事では、アクティビティ ログの表示と、別の宛先への送信について詳しく説明します。

診断設定の作成の詳細については、「[プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](diagnostic-settings.md)」を参照してください。

> [!NOTE]
> アクティビティ ログのエントリはシステムによって生成されるため、変更または削除することはできません。

## <a name="view-the-activity-log"></a>アクティビティ ログを表示する
Azure portal のほとんどのメニューから、アクティビティ ログにアクセスできます。 開くメニューによって、最初のフィルターが決まります。 **[モニター]** メニューから開くと、そのサブスクリプションに対するフィルターだけになります。 リソースのメニューから開くと、フィルターはそのリソースに対して設定されます。 ただし、いつでもフィルターを変更して、他のすべてのエントリを表示できます。 フィルターにプロパティを追加するには、 **[フィルターの追加]** をクリックします。

![アクティビティ ログを表示する](./media/activity-logs-overview/view-activity-log.png)

アクティビティ ログのカテゴリについては、「[Azure アクティビティ ログのイベント スキーマ](activity-log-schema.md#categories)」を参照してください。

### <a name="view-change-history"></a>変更履歴を表示する

イベントによっては、変更履歴を表示することができます。そこには、そのイベントの間に発生した変更が表示されます。 アクティビティ ログから、詳細を確認したいイベントを選択します。 **[変更履歴 (プレビュー)]** タブを選択し、そのイベントに関連する変更を表示します。

![イベントの変更履歴の一覧](media/activity-logs-overview/change-history-event.png)

イベントに関連する変更がある場合は、変更の一覧が表示され、選択できます。 これにより、 **[変更履歴 (プレビュー)]** ページが開きます。 このページには、リソースに対する変更が表示されます。 次の例では、VM のサイズが変更されたことだけでなく、変更前と変更後の VM のサイズを確認できます。 変更履歴の詳細については、「[リソースの変更の取得](../../governance/resource-graph/how-to/get-resource-changes.md)」を参照してください。

![相違を示す変更履歴ページ](media/activity-logs-overview/change-history-event-details.png)


### <a name="other-methods-to-retrieve-activity-log-events"></a>アクティビティ ログ イベントを取得する他の方法
次の方法を使用して、アクティビティ ログ イベントにアクセスすることもできます。

- PowerShell からアクティビティ ログを取得するには、[Get-AzLog](/powershell/module/az.monitor/get-azlog) コマンドレットを使用します。 「[Azure Monitor PowerShell のサンプル](../samples/powershell-samples.md#retrieve-activity-log)」を参照してください。
- CLI からアクティビティ ログ エントリを取得するには、[az monitor activity-log](/cli/azure/monitor/activity-log) を使用します。  [Azure Monitor CLI のサンプル](../samples/cli-samples.md#view-activity-log)をご覧ください。
- REST クライアントからアクティビティ ログを取得するには、[Azure Monitor REST API](/rest/api/monitor/) を使用します。 


## <a name="send-to-log-analytics-workspace"></a>Log Analytics ワークスペースに送信する
 次のような [Azure Monitor ログ](data-platform-logs.md)の機能を有効にするには、アクティビティ ログを Log Analytics ワークスペースに送信します。

- アクティビティ ログ データを、Azure Monitor によって収集されたその他の監視データと関連付けます。
- 複数の Azure サブスクリプションおよびテナントのログ エントリを 1 つの場所に統合して、まとめて分析できるようにします。
- ログ クエリを使用して複雑な分析を実行し、アクティビティ ログのエントリから詳細な分析情報を得ます。
- アクティビティ エントリでログ アラートを使用すると、より複雑なアラート ロジックを使用できます。
- アクティビティ ログのエントリを 90 日を超えて保存します。
- Log Analytics ワークスペースに格納されているアクティビティ ログ データのデータ インジェストまたはデータ保持の料金は発生しません。

アクティビティ ログを Log Analytics ワークスペースに送信するには、[診断設定を作成します](diagnostic-settings.md)。 任意の 1 つのサブスクリプションから最大 5 つのワークスペースに、アクティビティ ログを送信できます。 テナント間でログを収集するには [Azure Lighthouse](../../lighthouse/index.yml) が必要です。

Log Analytics ワークスペースでは、アクティビティ ログのデータは *AzureActivity* という名前のテーブルに格納されます。このテーブルは、[Log Analytics](../log-query/get-started-portal.md) の[ログ クエリ](../log-query/log-query-overview.md)で取得できます。 このテーブルの構造は[ログ エントリのカテゴリ](activity-log-schema.md)によって異なります。 テーブルのプロパティの説明については、[Azure Monitor データ リファレンス](/azure/azure-monitor/reference/tables/azureactivity)のページを参照してください。

たとえば、各カテゴリのアクティビティ ログ レコードの数を表示するには、次のクエリを使用します。

```kusto
AzureActivity
| summarize count() by Category
```

管理カテゴリのすべてのレコードを取得するには、次のクエリを使用します。

```kusto
AzureActivity
| where Category == "Administrative"
```


## <a name="send-to-azure-event-hubs"></a>Azure Event Hubs に送信する
たとえば、サードパーティの SIEM や他のログ分析ソリューションなど、Azure の外部にエントリを送信するには、アクティビティ ログを Azure Event Hubs に送信します。 Event Hubs からのアクティビティ ログ イベントは、レコードが含まれる `records` 要素が各ペイロードにある JSON 形式で使用されます。 スキーマはカテゴリによって異なります。「[ストレージ アカウントとイベント ハブからのスキーマ](activity-log-schema.md)」を参照してください。

次に示すのは、アクティビティ ログに対する Event Hubs からの出力データの例です。

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
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
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
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


## <a name="send-to--azure-storage"></a>Azure Storage に送信する
監査、静的分析、またはバックアップのためにログ データを 90 日より長く保持する場合は、アクティビティ ログを Azure ストレージ アカウントに送信します。 90 日以内でイベントを保持する必要があるだけの場合は、ストレージ アカウントにアーカイブを設定する必要はありません。アクティビティ ログのイベントは Azure プラットフォームに 90 日間保持されるためです。

アクティビティ ログを Azure に送信すると、イベントが発生するとすぐにストレージ コンテナーがストレージ アカウントに作成されます。 コンテナー内の BLOB には、次の名前付け規則が使用されます。

```
insights-activity-logs/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

たとえば、特定の BLOB には次のような名前が付けられることがあります。

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/y=2020/m=06/d=08/h=18/m=00/PT1H.json
```

各 PT1H.json BLOB には、BLOB の URL で指定された時間内に発生したイベントの JSON BLOB が含まれます (例: h = 12)。 現在の時間内にイベントが発生すると、PT1H.json ファイルにイベントが追加されます。 リソース ログ イベントは 1 時間ごとに個々の BLOB に分類されるため、分の値 (m = 00) は常に 00 です。

各イベントは、次の形式の PT1H.json ファイルに格納されます。一般的な最上位スキーマが使用されますが、それ以外については「[アクティビティ ログのスキーマ](activity-log-schema.md)」で説明されているようにカテゴリごとに固有です。

``` JSON
{ "time": "2020-06-12T13:07:46.766Z", "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MV-VM-01", "correlationId": "0f0cb6b4-804b-4129-b893-70aeeb63997e", "operationName": "Microsoft.Resourcehealth/healthevent/Updated/action", "level": "Information", "resultType": "Updated", "category": "ResourceHealth", "properties": {"eventCategory":"ResourceHealth","eventProperties":{"title":"This virtual machine is starting as requested by an authorized user or process. It will be online shortly.","details":"VirtualMachineStartInitiatedByControlPlane","currentHealthStatus":"Unknown","previousHealthStatus":"Unknown","type":"Downtime","cause":"UserInitiated"}}}
```


## <a name="legacy-collection-methods"></a>従来の収集方法
このセクションでは、診断設定の前に使用されていた、アクティビティ ログを収集するための従来の方法について説明します。 これらの方法を使用している場合は、機能およびリソース ログとの整合性がより優れている、診断設定への移行を検討する必要があります。

### <a name="log-profiles"></a>ログ プロファイル
ログ プロファイルは、Azure Storage またはイベント ハブにアクティビティ ログを送信するためのレガシの方法です。 ログ プロファイルを引き続き使用する場合、または診断設定への移行の準備としてそれを無効にする場合は、次の手順に従います。

1. Azure portal の **[Azure Monitor]** メニューで、 **[アクティビティ ログ]** を選択します。
3. **[診断設定]** をクリックします。

   ![診断設定](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 従来のエクスペリエンスについては、紫色のバナーをクリックします。

    ![従来のエクスペリエンス](media/diagnostic-settings-subscription/legacy-experience.png)



### <a name="configure-log-profile-using-powershell"></a>PowerShell を使用してログ プロファイルを構成する

ログ プロファイルが既に存在する場合は、最初に既存のログ プロファイルを削除してから、新しいものを作成する必要があります。

1. `Get-AzLogProfile` を使用して、ログ プロファイルが存在するかどうかを確認します。  ログ プロファイルが存在する場合は、*name* プロパティを探します。

1. `Remove-AzLogProfile` を使用し、*name* プロパティの値を使用してログ プロファイルを削除します。

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. `Add-AzLogProfile` を使用して、新しいログ プロファイルを作成します。

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | プロパティ | 必須 | Description |
    | --- | --- | --- |
    | 名前 |はい |ログ プロファイルの名前。 |
    | StorageAccountId |いいえ |アクティビティ ログの保存先となるストレージ アカウントのリソース ID。 |
    | serviceBusRuleId |いいえ |Event Hubs を作成する Service Bus 名前空間の Service Bus 規則 ID。 文字列の形式は `{service bus resource ID}/authorizationrules/{key name}` になります。 |
    | 場所 |はい |アクティビティ ログ イベントを収集するリージョンのコンマ区切りリスト。 |
    | RetentionInDays |はい |ストレージ アカウントにイベントを保持する日数 (1 から 365 の範囲)。 値が 0 の場合、ログは無期限に保存されます。 |
    | カテゴリ |いいえ |収集するイベント カテゴリのコンマ区切りリスト。 指定できる値は、_Write_、_Delete_、_Action_ です。 |

### <a name="example-script"></a>サンプル スクリプト
ストレージ アカウントとイベント ハブの両方にアクティビティ ログを書き込むログ プロファイルを作成する PowerShell スクリプトのサンプルを次に示します。

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Azure CLI を使用してログ プロファイルを構成する

ログ プロファイルが既に存在する場合は、最初に既存のログ プロファイルを削除してから、新しいログ プロファイルを作成する必要があります。

1. `az monitor log-profiles list` を使用して、ログ プロファイルが存在するかどうかを確認します。
2. `az monitor log-profiles delete --name "<log profile name>` を使用し、*name* プロパティの値を使用してログ プロファイルを削除します。
3. `az monitor log-profiles create` を使用して、新しいログ プロファイルを作成します。

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | プロパティ | 必須 | 説明 |
    | --- | --- | --- |
    | name |はい |ログ プロファイルの名前。 |
    | storage-account-id |はい |アクティビティ ログの保存先となるストレージ アカウントのリソース ID。 |
    | locations |はい |アクティビティ ログ イベントを収集するリージョンのスペース区切りリスト。 `az account list-locations --query [].name` を使って、サブスクリプションのすべてのリージョンの一覧を見ることができます。 |
    | days |はい |イベントを保持する日数。1 -365 の範囲。 値が 0 の場合、ログは無期限に (いつまでも) 保存されます。  0 の場合は、enabled パラメーターを false に設定する必要があります。 |
    |enabled | はい |True または False。  アイテム保持ポリシーを有効または無効にするために使います。  True の場合は、days パラメーターを 0 より大きい値にする必要があります。
    | categories |はい |収集するイベント カテゴリのスペース区切りリスト。 指定できる値は、Write、Delete、Action です。 |


### <a name="log-analytics-workspace"></a>Log Analytics ワークスペース
アクティビティ ログを Log Analytics ワークスペースに送信する従来の方法は、ワークスペースの構成でログを接続することです。 

1. Azure portal の **[Log Analytics ワークスペース]** メニューから、アクティビティ ログを収集するためのワークスペースを選択します。
1. ワークスペースのメニューの **[ワークスペースのデータ ソース]** セクションで、 **[Azure アクティビティ ログ]** を選択します。
1. 接続するサブスクリプションをクリックします。

    ![Workspaces](media/activity-log-collect/workspaces.png)

1. **[接続]** をクリックして、選択したワークスペースにサブスクリプションのアクティビティ ログを接続します。 サブスクリプションが既に別のワークスペースに接続されている場合、最初に **[切断]** をクリックして切断します。

    ![ワークスペースを接続する](media/activity-log-collect/connect-workspace.png)


この設定を無効にするには、同じ手順を実行し、 **[切断]** をクリックして、ワークスペースからサブスクリプションを削除します。

### <a name="data-structure-changes"></a>データ構造の変更
診断設定では、従来のアクティビティ ログ送信方法と同じデータが送信されますが、*AzureActivity* テーブルの構造が若干変更されています。

次の表の列は、更新されたスキーマで非推奨とされています。 これらは、*AzureActivity* にまだ存在しますが、データを持ちません。 これらの列の代わりとなるものは新しいものではありませんが、非推奨の列と同じデータが格納されています。 それらは形式が異なるため、それらを使用するログ クエリの変更が必要になる場合があります。 

| 非推奨の列 | 置換列 |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> 場合によっては、これらの列の値がすべて大文字になることがあります。 これらの列を含むクエリがある場合は、[=~ 演算子](/azure/kusto/query/datatypes-string-operators)を使用して、大文字と小文字を区別しない比較を実行する必要があります。

更新されたスキーマの *AzureActivity* には、次の列が追加されています。

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-analytics-monitoring-solution"></a>Activity Log Analytics 監視ソリューション
Azure Log Analytics 監視ソリューションは、間もなく非推奨となり、Log Analytics ワークスペースで更新されたスキーマを使用するブックに置き換えられます。 このソリューションは、既に有効にしている場合に引き続き使用できますが、レガシの設定を使用してアクティビティ ログを収集している場合にのみ使用できます。 



### <a name="use-the-solution"></a>ソリューションの使用
監視ソリューションには Azure portal の **[Monitor]\(監視\)** メニューからアクセスします。 **[Insights]\(インサイト\)** セクションで **[More]\(詳細\)** を選択して、ソリューション タイルのある **[概要]** ページを開きます。 **[Azure アクティビティ ログ]** タイルには、ワークスペース内の **AzureActivity** レコードの数が表示されます。

![Azure アクティビティ ログのタイル](media/collect-activity-logs/azure-activity-logs-tile.png)


**[Azure アクティビティ ログ]** タイルをクリックして、 **[Azure アクティビティ ログ]** ビューを開きます。 ビューには、次の表の視覚化パーツが含まれています。 それぞれのパーツには、指定された時間範囲について、そのパーツの基準に該当する項目が 10 個まで表示されます。 パーツの下部にある **[すべて表示]** をクリックすると、すべての一致するレコードを返すログ クエリを実行できます。

![Azure のアクティビティ ログのダッシュボード](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>新しいサブスクリプションでソリューションを有効にする
間もなく、Azure portal を使用して、アクティビティ ログ分析ソリューションをサブスクリプションに追加できなくなります。 Resource Manager テンプレートを使用して、次の手順で追加できます。 

1. 次の json を *ActivityLogTemplate*.json というファイルにコピーします。

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. 次の PowerShell コマンドを使用して、テンプレートをデプロイします。

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```



## <a name="next-steps"></a>次のステップ

* [プラットフォーム ログの概要を確認する](platform-logs-overview.md)
* [他の送信先にアクティビティ ログを送信するための診断設定を作成する](diagnostic-settings.md)
