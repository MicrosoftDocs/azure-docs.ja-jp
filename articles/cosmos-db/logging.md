---
title: "Azure Cosmos DB 診断ログ | Microsoft Docs"
description: "このチュートリアルを使用すると、Azure Cosmos DB のログ記録を簡単に始めることができます。"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mimig
ms.openlocfilehash: 34c46fb282ad154225f5ee8ef544bc8da1c50016
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Azure Cosmos DB 診断ログ

1 つまたは複数の Azure Cosmos DB データベースを使用し始めた場合、データベースのアクセス方法と時間を監視したいと考えるのではないでしょうか。 Azure Cosmos DB の診断ログなら、この監視を実行できます。 診断ログを有効にすると、ログを [Azure Storage](https://azure.microsoft.com/services/storage/) に送信する、[Azure Event Hubs](https://azure.microsoft.com/en-us/services/event-hubs/) にストリーミングする、[Log Analytics](https://azure.microsoft.com/services/log-analytics/) を介して [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) ワークスペースにエクスポートすることができます。

![Log Analytics を介した Storage、Event Hubs、Operations Management Suite への診断ログ記録](./media/logging/azure-cosmos-db-logging-overview.png)

このチュートリアルを読むと、Azure Portal、CLI、または PowerShell を使用して Azure Cosmos DB のログ記録を開始できるようになります。

## <a name="what-is-logged"></a>ログに記録される内容

* 認証されたすべての REST DocumentDB (SQL) API 要求がログ記録されます。これには、アクセス許可がないため、システム エラーのため、または不正な要求の結果として、失敗した要求が含まれます。 MongoDB、Graph、および Table API は現在サポートされていません。
* データベース自体に対する操作。すべてのドキュメント、コンテナー、およびデータベースに対する CRUD 操作が含まれます。
* アカウント キーに対する操作。これらのキーの作成、変更、または削除が含まれます。
* 結果として 401 応答が発生する、認証されていない要求。 たとえば、ベアラー トークンを持たない要求、形式が正しくない要求、有効期限切れの要求、または無効なトークンを持つ要求です。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下のリソースが必要です。

* 既存の Azure Cosmos DB アカウント、データベース、およびコンテナー。 これらのリソースの作成手順については、[Azure Portal を使用したデータベース アカウントの作成](create-documentdb-dotnet.md#create-a-database-account)、[CLI サンプル](cli-samples.md)、または [PowerShell サンプル](powershell-samples.md)に関するページを参照してください。


## <a name="turn-on-logging-in-the-azure-portal"></a>Azure Portal でログを有効にする

1. [Azure Portal](https://portal.azure.com) で、Azure Cosmos DB アカウントの左のナビゲーションから、**[診断ログ]** をクリックし、**[診断をオンにする]** をクリックします。

    ![Azure Portal で Azure Cosmos DB の診断ログを有効にする](./media/logging/turn-on-portal-logging.png)

2. **[診断設定]** ページで次の操作を行います。 

    * **名前**。 作成するログの名前を入力します。

    * **[ストレージ アカウントへのアーカイブ]**。 このオプションを使用するには、接続先として既存のストレージ アカウントが必要です。 Portal で新しいストレージ アカウントを作成するには、[ストレージ アカウントの作成に関するページ](../storage/common/storage-create-storage-account.md)を参照し、Resource Manager の汎用アカウントの作成手順を実行します。 Portal でこのページに戻り、ストレージ アカウントを選択します。 新しく作成されたストレージ アカウントがドロップダウン メニューに表示されるまでには、数分かかる場合があります。
    * **イベント ハブにストリーミングします**。 このオプションを使用するには、既存の Event Hubs 名前空間と接続先のイベント ハブが必要です。 Event Hubs 名前空間を作成するには、「[Azure Portal を使用して Event Hubs 名前空間とイベント ハブを作成する](../event-hubs/event-hubs-create.md)」を参照してください。 Portal でこのページに戻り、Event Hubs 名前空間とポリシー名を選択します。
    * **[Log Analytics への送信]**。 このオプションを使用するには、既存のワークスペースのいずれかを使用するか、Portal のプロンプトに従って新しい Operations Management Suite ワークスペースを作成します。
    * **DataPlaneRequests をログに記録します**。 ストレージ アカウントにアーカイブしている場合、**[DataPlaneRequests]** を選択し、ログの保持日数を選択して、診断ログのリテンション期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。 

3. [ **Save**] をクリックします。

    いつでもこのページに戻って、アカウントの診断ログ設定を変更することができます。

## <a name="turn-on-logging-using-cli"></a>CLI を使用してログ記録を有効にする

Azure CLI を使用してメトリックと診断のロギングを有効にするには、次のコマンドを使用します。

- ストレージ アカウントへの診断ログの保存を有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   `resourceId` は、Azure Cosmos DB アカウントの名前です。 `storageId` は、ログの送信先のストレージ アカウント名です。

- Event Hubs への診断ログのストリーミングを有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   `resourceId` は、Azure Cosmos DB アカウントの名前です。 `serviceBusRuleId` は、次の形式の文字列です。

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Log Analytics ワークスペースへの診断ログの送信を有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

## <a name="turn-on-logging-using-powershell"></a>PowerShell を使用してログ記録を有効にする

PowerShell を使用してログ記録を有効にするには、Azure PowerShell バージョン 1.0.1 以降が必要です。

Azure PowerShell をインストールして、Azure サブスクリプションに関連付けるには、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」を参照してください。

Azure PowerShell をインストール済みで、バージョンがわからない場合は、PowerShell コンソールで「`(Get-Module azure -ListAvailable).Version`」と入力します。  

### <a id="connect"></a>サブスクリプションへの接続
Azure PowerShell セッションを開始し、次のコマンドで Azure アカウントにサインインします。  

```powershell
Login-AzureRmAccount
```

ポップアップ ブラウザー ウィンドウで、Azure アカウントのユーザー名とパスワードを入力します。 Azure PowerShell は、このアカウントに関連付けられているすべてのサブスクリプションを取得し、既定で最初のサブスクリプションを使用します。

複数のサブスクリプションをお持ちの場合は、Azure Key Vault を作成するときに使用した特定の 1 つを指定することが必要なことがあります。 アカウントのサブスクリプションを確認するには、次を入力します。

```powershell
Get-AzureRmSubscription
```

ログを記録する Azure Cosmos DB アカウントに関連付けられているサブスクリプションを指定するには、次を入力します。

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> 複数のサブスクリプションがアカウントに関連付けられている場合は、サブスクリプションを指定することが重要です。
>
>

Azure PowerShell の詳細については、「 [How to install and configure Azure PowerShell (Azure PowerShell のインストールと構成の方法)](/powershell/azure/overview)」をご覧ください。

### <a id="storage"></a>ログ用に新しいストレージ アカウントを作成する
ログに既存のストレージ アカウントを使用することもできますが、このチュートリアルでは、Azure Cosmos DB ログ専用の新しいストレージ アカウントを作成します。 便宜上、ストレージ アカウントの詳細を **sa** という変数に格納します。

さらに管理を簡単にするために、このチュートリアルでは、Azure Cosmos DB データベースと同じリソース グループを使用します。 ContosoResourceGroup、contosocosmosdblogs、"米国中北部" の値は、適宜、実際の値で置き換えてください。

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> 既存のストレージ アカウントを使用する場合、そのストレージ アカウントでは、目的の Azure Cosmos DB と同じサブスクリプションを使用する必要があります。また、クラシック デプロイメント モデルではなく、リソース マネージャー デプロイメント モデルを使用する必要があります。
>
>

### <a id="identify"></a>ログ用の Azure Cosmos DB アカウントを指定する
Azure Cosmos DB アカウント名を **account** という変数に設定します。この ResourceName は、Azure Cosmos DB アカウントの名前です。

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>ログの有効化
Azure Cosmos DB のログ記録を有効にするには、新しいストレージ アカウント、Azure Cosmos DB アカウント、およびログを有効にするカテゴリの変数を指定して、Set-AzureRmDiagnosticSetting コマンドレットを使用します。 次のコマンドを実行し、**-Enabled** フラグを **$true** に設定します。

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

コマンドの出力は次のようになります。

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

これを見れば、ログ記録がデータベースに対して有効になっていること、ストレージ アカウントに情報が保存されることを確認できます。

必要に応じて、ログのリテンション期間ポリシーを使用して、古いログが自動的に削除されるように設定することもできます。 たとえば、**-RetentionEnabled** フラグを **$true** に設定し、**-RetentionInDays** パラメーターを **90** に設定したリテンション期間ポリシーを設定すると、90 日より前のログが自動的に削除されます。

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>ログへのアクセス
**DataPlaneRequests** カテゴリの Azure Cosmos DB ログは、指定したストレージ アカウントの **insights-logs-data-plane-requests** コンテナーに格納されます。 

最初に、コンテナー名の変数を作成します。 この変数は、チュートリアルの残りの部分で使用します。

```powershell
    $container = 'insights-logs-dataplanerequests'
```

このコンテナー内のすべての BLOB を一覧表示するには、次のように入力します。

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

出力は次のようになります。

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

この出力からわかるように、BLOB は次の命名規則に従います。`resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

日付と時刻の値には UTC が使用されます。

同じストレージ アカウントを使用して複数のリソースのログを収集することができるので、必要な BLOB のみにアクセスしたり、ダウンロードしたりする場合には、BLOB 名の完全修飾リソース ID を使用すると便利です。 その前に、すべての BLOB をダウンロードする方法を説明します。

まず、フォルダーを作成して BLOB をダウンロードします。 For example:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

すべての BLOB の一覧を取得します。  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

'Get-AzureStorageBlobContent' を介してこの一覧をパイプして、BLOB を宛先フォルダーにダウンロードします。

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

この 2 番目のコマンドを実行すると、BLOB 名に含まれる **/** 区切り記号によって、宛先フォルダーの下にフォルダー構造全体が作成されます。 このフォルダー構造は、BLOB をファイルとしてダウンロードし、保存するために使用されます。

BLOB を選択的にダウンロードするには、ワイルドカードを使用します。 For example:

* 複数のデータベースを持っている場合に、CONTOSOCOSMOSDB3 という名前のデータベースのみについてログをダウンロードするには、次のようにします。

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* 複数のリソース グループを持っている場合、1 つのリソース グループのみについてログをダウンロードするには、次のように `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`を使用します。

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* 2017 年 7 月のすべてのログをダウンロードする場合は、次のように `-Blob '*/year=2017/m=07/*'`を使用します。

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

これで、ログの内容を検討する準備が整いました。 ただし、ログの内容の検討に入る前に、`Get-AzureRmDiagnosticSetting` 用のさらに 2 つのパラメーターを把握しておく必要があります。

* データベース リソースの診断設定の状態のクエリを実行する場合: `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
* データベース アカウント リソースの **DataPlaneRequests** カテゴリのログ記録を無効にする場合: `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`

## <a id="interpret"></a> Azure Cosmos DB ログを解釈する
個々の BLOB はテキストとして格納されます (JSON BLOB 形式)。 次の JSON はログ エントリの例です。

    {
        "records":
        [
            {
               "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
               "resourceId": "contosocosmosdb",
               "category": "DataPlaneRequests",
               "operationName": "Query",
               "resourceType": "Database",
               "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
               "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
               "resourceType": "Database","statusCode": "200","documentResourceId": "",`
               "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
               "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
            }
        ]
    }


次の表にフィールド名と説明を示します。

| フィールド名 | Description |
| --- | --- |
| time |操作が発生した日時 (UTC)。 |
| resourceId |ログが有効になっている Azure Cosmos DB アカウント。|
| カテゴリ |Azure Cosmos DB ログの場合、使用できる値は DataPlaneRequests のみです。 |
| operationName |操作の名前。 この値は、Create、Update、Read、ReadFeed、Delete、Replace、Execute、SqlQuery、Query、JSQuery、Head、HeadFeed、または Upsert 操作のいずれかです。   |
| properties |このフィールドの内容については、次の表を参照してください。 |

次の表は、プロパティ フィールド内でログに記録されるフィールドの一覧です。

| プロパティ フィールド名 | Description |
| --- | --- |
| activityId | ログに記録された操作の一意の GUID。 |
| userAgent |要求を実行するクライアント ユーザー エージェントを示す文字列。 {ユーザー エージェント名}/{バージョン} という形式です。|
| resourceType | アクセスされたリソースの種類。 この値は、リソースの種類 Database、Collection、Document、Attachment、User、Permission、StoredProcedure、Trigger、UserDefinedFunction、または Offer のいずれかです。 |
| StatusCode |操作の応答状態。 |
| requestResourceId | 要求に関する resourceId は、実行された操作によって databaseRid、collectionRid、または documentRid の可能性があります。|
| clientIpAddress |クライアントの IP アドレス。 |
| requestCharge | 操作に使用された RU 数 |
| collectionRid | コレクションの一意の ID。|
| duration | 操作の期間 (ティック単位)。 |
| requestLength |要求の長さ (バイト単位)。 |
| responseLength | 応答の長さ (バイト単位)。|
| resourceTokenUserRid | 認証に[リソース トークン](https://docs.microsoft.com/en-us/azure/cosmos-db/secure-access-to-data#resource-tokens)が使用され、ユーザーのリソース ID を示す場合、この値は空ではありません。 |

## <a name="managing-your-logs"></a>ログの管理

ログは、アカウントで Azure Cosmos DB 操作が実行されてから 2 時間使用できます。 ストレージ アカウントでのログの管理はお客様に委ねられます。

* ログにアクセスできるユーザーを制限することでログのセキュリティを保護するには、標準的な Azure アクセス制御方法を使用します。
* ストレージ アカウントに保持する必要がなくなったログは削除します。
* ストレージ アカウントにアーカイブされるデータ プレーン要求のリテンション期間は、**[Log DataPlaneRequests]\(DataPlaneRequests のログを記録する\)** をオンにしたときに構成されます。 この設定を変更する方法については、「[Azure Portal でログを有効にする](#turn-on-logging-in-the-azure-portal)」を参照してください。

## <a name="next-steps"></a>次のステップ

- ロギングを有効にする方法だけでなく、各種 Azure サービスでサポートされるメトリックとログのカテゴリーについて理解を深めるには、「[Microsoft Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)」と「[Azure リソースからのログ データの収集と使用](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)」の両方を参照してください。
- Event Hubs については次の記事をご覧ください。
   - [Azure Event Hubs とは](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- [Azure Storage からメトリックとログをダウンロードする方法](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)に関する記事をご覧ください。
