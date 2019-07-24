---
title: Azure 診断ログ | Microsoft Docs
description: 顧客は、Azure CDN のログ分析を有効にすることができます。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: magattus
ms.openlocfilehash: a5fab3e2bf9908fa35cf5f5485df3116b7718d8c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57881131"
---
# <a name="azure-diagnostic-logs"></a>Azure 診断ログ

Azure 診断ログでコア分析を確認し、1 つまたは複数の宛先に保存することができます。

 - Azure ストレージ アカウント
 - Azure Event Hubs
 - [Log Analytics ワークスペース](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
この機能は、すべての価格レベルの CDN エンドポイントで使用できます。 

Azure Diagnostics ログにより、基本的な使用メトリックを CDN エンドポイントからさまざまなソースにエクスポートして、それらをカスタマイズした方法で使用できるようになります。 たとえば、次の種類のデータのエクスポートを実行できます。

- BLOB ストレージへのデータのエクスポート、CSV へのエクスポート、Excel でのグラフの生成。
- イベント ハブへのデータのエクスポートと、他の Azure サービスのデータとの関連付け。
- ログ分析へのデータのエクスポートと、自分の Azure Monitor ログのワークスペースでのデータ表示

以下の図は、データの通常の CDN コア分析ビューを示します。

![ポータル - 診断ログ](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*図 1 - CDN コア分析ビュー*

診断ログの詳細については、[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)に関するページを参照してください。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Azure Portal を使用したログの有効化

CDN コア分析を使用してログ記録を有効にするには、次の手順に従います。

[Azure Portal](https://portal.azure.com) にサインインします。 ワークフローの CDN をまだ有効にしていない場合は、[Azure CDN のプロファイルとエンドポイントを作成](cdn-create-new-endpoint.md)してから続行してください。

1. Azure Portal で、**CDN プロファイル** に移動してください。

2. Azure Portal で、CDN プロファイルを探すかダッシュボードから選択します。 次に、診断ログを有効にする CDN エンドポイントを選択します。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. [監視] セクションで **[診断ログ]** を選択します。

   **診断ログ** ページが表示されます。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Azure Storage でログ記録を有効化する

ストレージ アカウントを使ってログを保存するには、次の手順に従ってください。
    
1. **Name** には、診断ログ設定の名前を入力します。
 
2. **ストレージ アカウントへのアーカイブ** を選択し、**CoreAnalytics** を選択します。 

2. **データの保存期間 (日)** には、保存する日数を選択します。 リテンション期間を 0 にすると、ログが無期限に保存されます。 

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. **ストレージ アカウント**を選択します。

    **ストレージ アカウントの選択**ページが表示されます。

4. ドロップダウン リストから任意のストレージ アカウントを選択し、**OK**を選択します。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. 診断ログの設定が終わったら、**保存**を選択します。

### <a name="logging-with-azure-monitor"></a>Azure Monitor でのログ記録

Azure Monitor を使用してログを保存するには、次の手順に従います。

1. **診断ログ**ページで、**Log Analytics** への送信を選択します。 

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. **構成** を選択して Azure Monitor ログ記録を構成します。 

   **[Log Analytics ワークスペース]** ページが表示されます。

    >[!NOTE] 
    >OMS ワークスペースは、Log Analytics ワークスペースと呼ばれるようになりました。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. **新しいワークスペースの作成**を選択します。

    **[Log Analytics ワークスペース]** ページが表示されます。

    >[!NOTE] 
    >OMS ワークスペースは、Log Analytics ワークスペースと呼ばれるようになりました。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/07_Create-new.png)

4. **[Log Analytics ワークスペース]** に、Log Analytics ワークスペース名を入力します。 Log Analytics ワークスペースには一意の名前を付ける必要があります。名前には、英字、数字、およびハイフンのみを使用できます。スペースとアンダースコアは使用できません。 

5. **サブスクリプション**には、ドロップダウン リストから既存のサブスクリプション名を選択します。 

6. **リソース グループ**には、新しいリソース グループ名を作成するか既存の名前を選択します。

7. **場所**には、リストから任意の場所を選択します。

8. ログ構成をダッシュボードに保存する場合は、**ダッシュボードにピン留め**を選択します。 

9. 構成を終了するには、**OK** をクリックします。

10. ワークスペースを作成したら、**診断ログ**ページに戻ります。 新しい Log Analytics ワークスペースの名前を確認します。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. **CoreAnalytics** を選択し**保存**をクリックします。

12. 新しい Log Analytics ワークスペースを表示するには、CDN エンドポイント ページから**コア分析**を選択します。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    これで、Log Analytics ワークスペースがデータをログ記録する準備ができました。 そのデータを使用するためには、[Azure Monitor ログのソリューション](#consuming-diagnostics-logs-from-a-log-analytics-workspace) を使用する必要があります。これについては、この記事の中で後で説明します。

ログ データの遅延の詳細については、「[Log data delays](#log-data-delays)」(ログ データの遅延) を参照してください。

## <a name="enable-logging-with-powershell"></a>PowerShell を使用したログの有効化

以下の例は、Azure PowerShell コマンドレットによる診断ログを有効にする方法を示しています。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>ストレージ アカウントの診断ログを有効にする

1. ログインしてサブスクリプションを選択します。

    Connect-AzAccount 

    Select-AzureSubscription -SubscriptionId 

2. ストレージ アカウントの診断ログを有効にするには、次のコマンドを入力します。

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Log Analytics ワークスペースの診断ログを有効にするには、次のコマンドを入力します。

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Azure Storage からの診断ログの使用
このセクションでは、CDN コア分析のスキーマと、これが Microsoft Azure Storage アカウントの内部でどのように編成されているかについて説明し、ログを CSV ファイルにダウンロードするためのサンプル コードを提供します。

### <a name="using-microsoft-azure-storage-explorer"></a>Microsoft Azure ストレージ エクスプローラーの使用
Azure ストレージ アカウントからコア分析データにアクセスするには、ストレージ アカウント内のコンテンツにアクセスするためのツールが必要です。 市場にはいくつかのツールがありますが、お勧めするツールは Microsoft Azure ストレージ エクスプローラーです。 ツールをダウンロードするには、「[Azure Storage Explorer](https://storageexplorer.com/)」を参照してください。 ソフトウェアをダウンロードしてインストールしたら、CDN 診断ログの保存先として構成したのと同じ Microsoft Azure Storage アカウントを使用するように構成します。

1.  **Microsoft Azure ストレージ エクスプローラー**を開きます
2.  ストレージ アカウントを見つけます
3.  このストレージ アカウントで **Blob Containers** ノードを展開します。
4.  *Insights-logs-coreanalytics* というコンテナーを選択します。
5.  右側のページに結果が表示されます。まず第1レベルが *resourceId=* で表示されます。 *PT1H.json* ファイルにたどり着くまで各レベルを選択していきます。 パスの説明については、「[BLOB パスの形式](cdn-azure-diagnostic-logs.md#blob-path-format)」を参照してください。
6.  各 Blob *PT1H.json* ファイルは、特定の CDN エンドポイントまたはカスタム ドメインについての 1 時間の分析ログを表します。
7.  この JSON ファイルのコンテンツのスキーマは、コア分析ログのセクション スキーマに説明されています。


#### <a name="blob-path-format"></a>BLOB パスの形式

コア分析ログは、1 時間ごとに生成され、そのデータは 1 つの Azure BLOB 内に JSON ペイロードとして収集され、格納されます。 Storage Explorer ツールでは '/' がディレクトリの区切り記号と解釈され、階層表示されるため、Azure BLOB のパスは、階層構造が存在するように表示され、BLOB 名が表示されます。 BLOB のこの名前は次の名前付け規則に従っています   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**フィールドの説明:**

|値|説明|
|-------|---------|
|サブスクリプション ID    |GUID 形式の Azure サブスクリプションの ID。|
|リソース グループ名 |CDN のリソースが属するリソース グループの名前。|
|プロファイル名 |CDN プロファイルの名前|
|エンドポイント名 |CDN エンドポイントの名前|
|年|  年の 4 桁表記 (例: 2017)|
|月| 月の 2 桁表記。 01 = 1 月 ... 12 = 12 月|
|日|   日付の 2 桁表記|
|PT1H.json| 分析データが格納されている実際の JSON ファイル|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>CSV ファイルへのコア分析データのエクスポート

コア分析に簡単にアクセスできるように、ツールのサンプル コードが用意されています。 このツールでは、JSON ファイルをプレーンなカンマ区切りファイル形式にダウンロードして、図や凝集の作成に使用できます。

このツールを使用する方法を次に示します。

1.  次の GitHub リンクにアクセスします: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  コードをダウンロードします。
3.  指示に従ってコンパイルして構成します。
4.  ツールを実行します。
5.  生成される CSV ファイルには単純なフラット階層の分析データが示されています。

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Log Analytics ワークスペースからの診断ログの使用
Azure Monitor は、クラウド環境とオンプレミス環境を監視して可用性とパフォーマンスを維持する Azure サービスです。 Log Analytics を使用すると、クラウドおよびオンプレミスの環境内にあるリソースによって生成されたデータや、他の監視ツールのデータを収集し、複数のソースにわたる分析を行えます。 

Azure Monitor を使用するには、Azure Log Analytics ワークスペースに対する[ ログ記録を有効にする](#enable-logging-with-azure-storage) 必要があります。これについては、この記事の前半で説明しています。

### <a name="using-the-log-analytics-workspace"></a>Log Analytics ワークスペースの使用

 次の図は、リポジトリの入力と出力のアーキテクチャを示しています。

![Log Analytics ワークスペース](./media/cdn-diagnostics-log/12_Repo-overview.png)

*図 3 - Log Analytics リポジトリ*

管理ソリューションを使用して、さまざまな方法でデータを表示できます。 管理ソリューションは、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions)から取得できます。

各ソリューション下部の**今すぐ入手する** リンクを選択し、Azure マーケットプレースから管理ソリューションをインストールできます。

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Azure Monitor CDN 監視ソリューションを追加する

Azure Monitor 監視ソリューションを追加するには、次の手順に従います。

1.   Azure サブスクリプションで Azure Portal にサインインし、ダッシュボードに移動します。
    ![Azure ダッシュボード](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. **マーケットプレース** の**新規作成**ページで、**監視 + 管理**を選択します。

    ![マーケットプレース](./media/cdn-diagnostics-log/14_Marketplace.png)

3. **監視 + 管理**ページで、**すべて表示**を選択します。

    ![すべて表示](./media/cdn-diagnostics-log/15_See-all.png)

4. 検索ボックスで CDN を検索します。

    ![すべて表示](./media/cdn-diagnostics-log/16_Search-for.png)

5. **[Azure CDN Core Analytics]** を選択します。 

    ![すべて表示](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. **作成**を選択すると、Log Analytics ワークスペースを新規作成するか、既存のものを使用するかを質問されます。 

    ![すべて表示](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. 前に作成したワークスペースを選択します。 次に、オートメーション アカウントを追加する必要があります。

    ![すべて表示](./media/cdn-diagnostics-log/19_Add-automation.png)

8. 次の画面は、入力する必要があるオートメーション アカウント フォームを示しています。 

    ![すべて表示](./media/cdn-diagnostics-log/20_Automation.png)

9. オートメーション アカウントを作成した後、ソリューションを追加できます。 **[作成]** ボタンを選択します。

    ![すべて表示](./media/cdn-diagnostics-log/21_Ready.png)

10. これで、ソリューションがワークスペースに追加されます。 Azure Portal のダッシュボードに戻ります。

    ![すべて表示](./media/cdn-diagnostics-log/22_Dashboard.png)

    ワークスペースに移動するために作成した Log Analytics ワークスペースを選択します。 

11. **OMS ポータル**タイルを選択して、新しいソリューションを表示します。

    ![すべて表示](./media/cdn-diagnostics-log/23_workspace.png)

12. ポータルは次の画面のようになります。

    ![すべて表示](./media/cdn-diagnostics-log/24_OMS-solution.png)

    データに複数のビューを表示するにはタイルを 1 つ選択します。

    ![すべて表示](./media/cdn-diagnostics-log/25_Interior-view.png)

    左右にスクロールすることで、データの個々のビューを表すその他のタイルを確認できます。 

    データに関する詳細を表示するにはタイルを 1 つ選択します。

     ![すべて表示](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>プランと価格レベル

[こちら](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)で管理ソリューションのプランと価格レベルを確認できます。

### <a name="customizing-views"></a>ビューのカスタマイズ

**ビュー デザイナー**を使用して、データのビューをカスタマイズできます。 デザインを開始するには、 Log Analytics　ワークスペースに移動し、**ビュー デザイナー**タイルを選択します。

![ビュー デザイナー](./media/cdn-diagnostics-log/27_Designer.png)

グラフの種類をドラッグ アンド ドロップし、分析するデータの詳細を入力します。

![ビュー デザイナー](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>ログ データの遅延

次の表は、**Azure CDN Standard from Microsoft**、**Azure CDN Standard from Akamai**、および **Azure CDN Standard/Premium from Verizon** のログ データの遅延を示しています。

Microsoft ログ データの遅延 | Verizon ログ データの遅延 | Akamai ログ データの遅延
--- | --- | ---
1 時間の遅延。 | 1 時間の遅延があり、エンドポイントへの伝達が完了してから表示が開始されるまでに最大で 2 時間かかります。 | 24 時間の遅延があります。24 時間より前に作成されている場合には、表示が開始されるまでに最大で 2 時間かかります。 ログが最近作成された場合は、表示が開始されるまでに最大で 25 時間かかることがあります。

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>CDN コア分析での診断ログの種類

現在のところ、コア分析ログのみが提供されています。これには、CDN POPや CDN エッジから見た HTTP 応答の統計とエグレスの統計を示すメトリックが含まれます。

### <a name="core-analytics-metrics-details"></a>コア分析メトリックの詳細
次の表は、**Azure CDN Standard from Microsoft**、**Azure CDN Standard from Akamai**、および **Azure CDN Standard/Premium from Verizon** のコア分析のログで使用できるメトリックの一覧を示しています。 プロバイダーによっては使用できないメトリックがありますが、違いはほとんどありません。 この表には、指定したメトリックがプロバイダーで使用できるかどうかも示しています。 メトリックを使用できるのは、トラフィックが存在する CDN エンドポイントに対してのみである。


|メトリック                     | 説明 | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | この期間中にヒットした要求の合計数。 | はい | はい |はい |
| RequestCountHttpStatus2xx | 2xx の HTTP コード (200、202 など) になった要求の合計数。 | はい | はい |はい |
| RequestCountHttpStatus3xx | 3xx の HTTP コード (300、302 など) になった要求の合計数。 | はい | はい |はい |
| RequestCountHttpStatus4xx | 4xx の HTTP コード (400、404 など) になった要求の合計数。 | はい | はい |はい |
| RequestCountHttpStatus5xx | 5xx の HTTP コード (500、504 など) になった要求の合計数。 | はい | はい |はい |
| RequestCountHttpStatusOthers | その他のすべての HTTP コード (2xx-5xx 以外) の数。 | はい | はい |はい |
| RequestCountHttpStatus200 | HTTP コード応答 200 になった要求の合計数。 | はい | いいえ   |はい |
| RequestCountHttpStatus206 | HTTP コード応答 206 になった要求の合計数。 | はい | いいえ   |はい |
| RequestCountHttpStatus302 | HTTP コード応答 302 になった要求の合計数。 | はい | いいえ   |はい |
| RequestCountHttpStatus304 | HTTP コード応答 304 になった要求の合計数。 | はい | いいえ   |はい |
| RequestCountHttpStatus404 | HTTP コード応答 404 になった要求の合計数。 | はい | いいえ   |はい |
| RequestCountCacheHit | キャッシュ ヒットが生じた要求の合計数。 POP からクライアントに対して、アセットが直接処理されました。 | はい | はい | いいえ   |
| RequestCountCacheMiss | キャッシュ ミスが生じた要求の合計数。 キャッシュ ミスとは、クライアントに最も近い POP でアセットが見つからず、元のドメインから取得されたことを意味します。 | はい | はい | いいえ  |
| RequestCountCacheNoCache | エッジでのユーザーの構成が原因でキャッシュされなかったアセットに対する要求の合計数。 | はい | はい | いいえ  |
| RequestCountCacheUncacheable | アセットの Cache-Control および Expires ヘッダーによりキャッシュされなかったアセットへの要求の合計数。この場合、POP または HTTP クライアントではキャッシュすべきでないことを示します。 | はい | はい | いいえ  |
| RequestCountCacheOthers | 上記に含まれないキャッシュの状態の要求の合計数。 | いいえ  | はい | いいえ   |
| EgressTotal | 送信データ転送 (GB) | はい |はい |はい |
| EgressHttpStatus2xx | HTTP 状態コードが 2xx の応答の送信データ転送* (GB)。 | はい | はい | いいえ   |
| EgressHttpStatus3xx | HTTP 状態コードが 3xx の応答の送信データ転送 (GB)。 | はい | はい | いいえ   |
| EgressHttpStatus4xx | HTTP 状態コードが 4xx の応答の送信データ転送 (GB)。 | はい | はい | いいえ   |
| EgressHttpStatus5xx | HTTP 状態コードが 5xx の応答の送信データ転送 (GB)。 | はい | はい | いいえ  |
| EgressHttpStatusOthers | その他の HTTP 状態コードの応答の送信データ転送 (GB)。 | はい | はい | いいえ   |
| EgressCacheHit | CDN の POP/エッジで CDN キャッシュから直接配信された応答の送信データ転送。 | はい | はい | いいえ  |
| EgressCacheMiss. | 最も近い POP サーバーで見つからず、配信元サーバーから取得された応答の送信データ転送。 | はい | はい | いいえ  |
| EgressCacheNoCache | エッジでのユーザーの構成が原因でキャッシュされなかったアセットの送信データ転送。 | はい | はい | いいえ  |
| EgressCacheUncacheable | アセットの Cache-Control および/または Expires ヘッダーによりキャッシュされなかったアセットの送信データ転送。 POP または HTTP クライアントではキャッシュすべきでないことを示します。 | はい | はい | いいえ  |
| EgressCacheOthers | その他のキャッシュ シナリオの送信データ転送。 | いいえ  | はい | いいえ  |

* 送信データ転送は、CDN の POP サーバーからクライアントに配信されたトラフィックを指します。


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

ここで *time* とは、統計情報を報告する時間の境界の開始時刻を表します。 メトリックが CDN プロバイダーによってサポートされていない場合、double または整数の値の代わりに null 値が設定されます。 この null 値は、メトリックがないことを示し、0 の値とは異なります。 エンドポイントに構成されたドメインごとにこれらのメトリックのセットが 1 つあります。

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







