---
title: 診断ログ
titleSuffix: Azure Content Delivery Network
description: Azure 診断ログを使用し、コア分析を保存する方法について説明します。Azure Content Delivery Network エンドポイントから使用状況メトリックをエクスポートできるようになります。
services: cdn
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: allensu
ms.openlocfilehash: 164560fff27adc2d4e63cc8471a26d1d710b89a5
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88191282"
---
# <a name="diagnostic-logs---azure-content-delivery-network"></a>診断ログ - Azure Content Delivery Network

Azure 診断ログでコア分析を確認し、1 つまたは複数の宛先に保存することができます。

* Azure ストレージ アカウント
* Log Analytics ワークスペース
* Azure Event Hubs

この機能は、すべての価格レベルの CDN エンドポイントで使用できます。 

診断ログにより、基本的な使用状況メトリックを CDN エンドポイントからさまざまなソースにエクスポートして、それらをカスタマイズした方法で使用できるようになります。 次の種類のデータのエクスポートを実行できます。

* BLOB ストレージへのデータのエクスポート、CSV へのエクスポート、Excel でのグラフの生成。
* イベント ハブへのデータのエクスポートと、他の Azure サービスのデータとの関連付け。
* ログ分析へのデータのエクスポートと、自分の Azure Monitor ログのワークスペースでのデータ表示

次の手順には、Azure CDN のプロファイルが必要です。 続行する前に、[Azure CDN のプロファイルとエンドポイントの作成](cdn-create-new-endpoint.md)に関するページを参照してください。

## <a name="enable-logging-with-the-azure-portal"></a>Azure Portal を使用したログの有効化

以下の手順に従って、Azure CDN エンドポイントのログ記録を有効にします。

1. [Azure portal](https://portal.azure.com) にサインインします。 

2. Azure portal で **[すべてのリソース]**  -> **自身の CDN のプロファイル**に移動します。

2. 診断ログを有効にする CDN エンドポイントを選択します。

    :::image type="content" source="./media/cdn-diagnostics-log/02_browse-to-diagnostics-logs.png" alt-text="CDN エンドポイントを選択する。" border="true":::

3. **[監視]** セクションで **[診断ログ]** を選択します。

    :::image type="content" source="./media/cdn-diagnostics-log/03_diagnostics-logs-options.png" alt-text="診断ログを選択する。" border="true":::

### <a name="enable-logging-with-azure-storage"></a>Azure Storage でログ記録を有効化する

ストレージ アカウントを使ってログを保存するには、次の手順に従ってください。

 >[!NOTE] 
 >これらの手順を完了するには、ストレージ アカウントが必要です。 以下を参照してください:詳細については、「 **[Azure Storage アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)** 」を参照してください。
    
1. **[Diagnostic setting name]\(診断設定の名前\)** には、診断ログ設定の名前を入力します。
 
2. **ストレージ アカウントへのアーカイブ** を選択し、**CoreAnalytics** を選択します。 

3. **データの保存期間 (日)** には、保存する日数を選択します。 リテンション期間を 0 にすると、ログが無期限に保存されます。 

4. ログのサブスクリプションとストレージ アカウントを選択します。

    :::image type="content" source="./media/cdn-diagnostics-log/04_diagnostics-logs-storage.png" alt-text="診断ログ - ストレージ" border="true":::

3. **[保存]** を選択します。

### <a name="send-to-log-analytics"></a>Log Analytics への送信

ログに Log Analytics を使用するには、次の手順に従います。

>[!NOTE] 
>これらの手順を完了するには、Log Analytics ワークスペースが必要です。 以下を参照してください:詳細については、「 **[Azure ポータルで Log Analytics ワークスペースを作成する](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)** 」を参照してください。
    
1. **[Diagnostic setting name]\(診断設定の名前\)** には、診断ログ設定の名前を入力します。

2. **[Log Analytics への送信]** を選択し、 **[CoreAnalytics]** をクリックします。 

3. ログのサブスクリプションと Log Analytics ワークスペースを選択します。

   :::image type="content" source="./media/cdn-diagnostics-log/05-la-workspace.png" alt-text="診断ログ - Log Analytics" border="true":::

4. **[保存]** を選択します。

### <a name="stream-to-an-event-hub"></a>イベント ハブへのストリーミング

ログにイベント ハブを使用するには、次の手順に従います。

>[!NOTE] 
>これらの手順を完了するには、イベント ハブが必要です。 以下を参照してください: **[クイック スタート:Azure portal を使用したイベント ハブの作成](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)**
    
1. **[Diagnostic setting name]\(診断設定の名前\)** には、診断ログ設定の名前を入力します。

2. **[イベント ハブへのストリーム]** を選択し、 **[CoreAnalytics]** を選択します。 

3. ログのサブスクリプションとイベント ハブの名前空間を選択します。

   :::image type="content" source="./media/cdn-diagnostics-log/06-eventhub-namespace.png" alt-text="診断ログ - イベント ハブ" border="true":::

4. **[保存]** を選択します。


## <a name="enable-logging-with-powershell"></a>PowerShell を使用したログの有効化

以下の例は、Azure PowerShell コマンドレットによる診断ログを有効にする方法を示しています。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>ストレージ アカウントの診断ログを有効にする

1. Azure PowerShell にサインインします。

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. ストレージ アカウントの診断ログを有効にするには、これらのコマンドを入力します。 変数は、実際の値に置き換えてください。

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Categories CoreAnalytics
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Log Analytics ワークスペースの診断ログを有効にする

1. Azure PowerShell にサインインします。

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Log Analytics ワークスペースの診断ログを有効にするには、これらのコマンドを入力します。 変数は、実際の値に置き換えてください。

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Categories CoreAnalytics
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>イベント ハブの名前空間の診断ログを有効にする

1. Azure PowerShell にサインインします。

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Log Analytics ワークスペースの診断ログを有効にするには、これらのコマンドを入力します。 変数は、実際の値に置き換えてください。

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $eventhubname = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhubname -Enabled $true -Categories CoreAnalytics
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Azure Storage からの診断ログの使用
このセクションでは、CDN コア分析のスキーマと、Azure ストレージ アカウントでの編成について説明し、ログを CSV ファイルにダウンロードするためのサンプル コードを提供します。

### <a name="using-microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer の使用
ツールをダウンロードするには、「[Azure Storage Explorer](https://storageexplorer.com/)」を参照してください。 ソフトウェアをダウンロードしてインストールしたら、CDN 診断ログの保存先として構成したのと同じ Microsoft Azure Storage アカウントを使用するように構成します。

1.  **Microsoft Azure Storage Explorer** を開きます
2.  ストレージ アカウントを見つけます
3.  このストレージ アカウントで **Blob Containers** ノードを展開します。
4.  *Insights-logs-coreanalytics* というコンテナーを選択します。
5.  右側のページに結果が表示されます。まず第1レベルが *resourceId=* で表示されます。 *PT1H.json* ファイルにたどり着くまで各レベルを選択していきます。 パスの説明については、「[BLOB パスの形式](cdn-azure-diagnostic-logs.md#blob-path-format)」を参照してください。
6.  各 Blob *PT1H.json* ファイルは、特定の CDN エンドポイントまたはカスタム ドメインについての 1 時間の分析ログを表します。
7.  この JSON ファイルのコンテンツのスキーマは、コア分析ログのセクション スキーマに説明されています。


#### <a name="blob-path-format"></a>BLOB パスの形式

コア分析ログは、1 時間ごとに生成され、そのデータは 1 つの Azure BLOB 内に JSON ペイロードとして収集され、格納されます。 ストレージ エクスプローラー ツールは '/' をディレクトリの区切り記号と見なし、階層を表示します。 Azure BLOB へのパスは、階層構造が存在するかのように表示され、BLOB 名が示されます。 BLOB のこの名前は次の名前付け規則に従っています    

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**フィールドの説明:**

|値|説明|
|-------|---------|
|サブスクリプション ID    |GUID 形式の Azure サブスクリプションの ID。|
|リソース グループ名 |CDN のリソースが属するリソース グループの名前。|
|プロファイル名 |CDN プロファイルの名前|
|エンドポイント名 |CDN エンドポイントの名前|
|年|  年の 4 桁表記 (例: 2017)|
|Month| 月の 2 桁表記。 01 = 1 月 ... 12 = 12 月|
|日|   日付の 2 桁表記|
|PT1H.json| 分析データが格納されている実際の JSON ファイル|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>CSV ファイルへのコア分析データのエクスポート

コア分析にアクセスできるように、ツールのサンプル コードが用意されています。 このツールでは、JSON ファイルをプレーンなカンマ区切りファイル形式にダウンロードして、図や凝集の作成に使用できます。

このツールを使用する方法を次に示します。

1.  次の GitHub リンクにアクセスします: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  コードをダウンロードします。
3.  指示に従ってコンパイルして構成します。
4.  ツールを実行します。
5.  生成される CSV ファイルには単純なフラット階層の分析データが示されています。

## <a name="log-data-delays"></a>ログ データの遅延

次の表は、**Azure CDN Standard from Microsoft**、**Azure CDN Standard from Akamai**、および **Azure CDN Standard/Premium from Verizon** のログ データの遅延を示しています。

Microsoft ログ データの遅延 | Verizon ログ データの遅延 | Akamai ログ データの遅延
--- | --- | ---
1 時間の遅延。 | 1 時間の遅延があり、エンドポイントへの伝達が完了してから表示が開始されるまでに最大で 2 時間かかります。 | 24 時間の遅延があります。24 時間より前に作成されている場合には、表示が開始されるまでに最大で 2 時間かかります。 ログが最近作成された場合は、表示が開始されるまでに最大で 25 時間かかることがあります。

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>CDN コア分析での診断ログの種類

現在のところ、コア分析ログのみが提供されています。これには、CDN POPや CDN エッジから見た HTTP 応答の統計とエグレスの統計を示すメトリックが含まれます。

### <a name="core-analytics-metrics-details"></a>コア分析メトリックの詳細
以下でのコア分析ログに使用可能なメトリックの一覧を次の表に示します。

* **Azure CDN Standard from Microsoft**
* **Azure CDN Standard from Akamai**
* **Azure CDN Premium from Verizon**

プロバイダーによっては使用できないメトリックがありますが、違いはほとんどありません。 この表には、指定したメトリックがプロバイダーで使用できるかどうかも示しています。 メトリックを使用できるのは、トラフィックが存在する CDN エンドポイントに対してのみである。


|メトリック                     | 説明 | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | この期間中にヒットした要求の合計数。 | はい | はい |はい |
| RequestCountHttpStatus2xx | 2xx の HTTP コード (200、202 など) になった要求の合計数。 | はい | はい |はい |
| RequestCountHttpStatus3xx | 3xx の HTTP コード (300、302 など) になった要求の合計数。 | はい | はい |はい |
| RequestCountHttpStatus4xx | 4xx の HTTP コード (400、404 など) になった要求の合計数。 | はい | はい |はい |
| RequestCountHttpStatus5xx | 5xx の HTTP コード (500、504 など) になった要求の合計数。 | はい | はい |はい |
| RequestCountHttpStatusOthers | その他のすべての HTTP コード (2xx-5xx 以外) の数。 | はい | はい |はい |
| RequestCountHttpStatus200 | HTTP コード応答 200 になった要求の合計数。 | はい | いいえ  |はい |
| RequestCountHttpStatus206 | HTTP コード応答 206 になった要求の合計数。 | はい | いいえ  |はい |
| RequestCountHttpStatus302 | HTTP コード応答 302 になった要求の合計数。 | はい | いいえ  |はい |
| RequestCountHttpStatus304 | HTTP コード応答 304 になった要求の合計数。 | はい | いいえ  |はい |
| RequestCountHttpStatus404 | HTTP コード応答 404 になった要求の合計数。 | はい | いいえ  |はい |
| RequestCountCacheHit | キャッシュ ヒットが生じた要求の合計数。 POP からクライアントに対して、アセットが直接処理されました。 | はい | はい | いいえ  |
| RequestCountCacheMiss | キャッシュ ミスが生じた要求の合計数。 キャッシュ ミスとは、クライアントに最も近い POP でアセットが見つからず、元のドメインから取得されたことを意味します。 | はい | はい | いいえ |
| RequestCountCacheNoCache | エッジでのユーザーの構成が原因でキャッシュされなかったアセットに対する要求の合計数。 | はい | はい | いいえ |
| RequestCountCacheUncacheable | アセットの Cache-Control ヘッダーおよび Expires ヘッダーによりキャッシュされなかったアセットに対する要求の合計数。 これは、POP または HTTP クライアントではキャッシュすべきでないことを示します。 | はい | はい | いいえ |
| RequestCountCacheOthers | 上記に含まれないキャッシュの状態の要求の合計数。 | いいえ | はい | いいえ  |
| EgressTotal | 送信データ転送 (GB) | はい |はい |はい |
| EgressHttpStatus2xx | HTTP 状態コードが 2xx の応答の送信データ転送* (GB)。 | はい | はい | いいえ  |
| EgressHttpStatus3xx | HTTP 状態コードが 3xx の応答の送信データ転送 (GB)。 | はい | はい | いいえ  |
| EgressHttpStatus4xx | HTTP 状態コードが 4xx の応答の送信データ転送 (GB)。 | はい | はい | いいえ  |
| EgressHttpStatus5xx | HTTP 状態コードが 5xx の応答の送信データ転送 (GB)。 | はい | はい | いいえ |
| EgressHttpStatusOthers | その他の HTTP 状態コードの応答の送信データ転送 (GB)。 | はい | はい | いいえ  |
| EgressCacheHit | CDN の POP/エッジで CDN キャッシュから直接配信された応答の送信データ転送。 | はい | はい | いいえ |
| EgressCacheMiss. | 最も近い POP サーバーで見つからず、配信元サーバーから取得された応答の送信データ転送。 | はい | はい | いいえ |
| EgressCacheNoCache | エッジでのユーザーの構成が原因でキャッシュされなかったアセットの送信データ転送。 | はい | はい | いいえ |
| EgressCacheUncacheable | アセットの Cache-Control ヘッダーおよび Expires ヘッダー、またはそのいずれかによりキャッシュされなかったアセットの送信データ転送。 POP または HTTP クライアントではキャッシュすべきでないことを示します。 | はい | はい | いいえ |
| EgressCacheOthers | その他のキャッシュ シナリオの送信データ転送。 | いいえ | はい | いいえ |

\* 送信データ転送は、CDN の POP サーバーからクライアントに配信されたトラフィックを指します。


### <a name="schema-of-the-core-analytics-logs"></a>コア分析ログのスキーマ 

すべてのログが JSON 形式で格納され、各エントリには次のスキーマに従う文字列フィールドが含まれます。

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

ここで *time* とは、統計情報を報告する時間の境界の開始時刻を表します。 CDN プロバイダーによってサポートされていないメトリックでは、double または整数の値の代わりに null 値が設定されます。 この null 値は、メトリックがないことを示し、0 の値とは異なります。 エンドポイントでは、ドメインごとにこれらのメトリックのセットが 1 つ構成されます。

以下にプロパティの例を示します。

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>その他のリソース

* [Azure 診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Azure CDN の補助ポータルを使用したコア分析](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Monitor ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics)







