---
title: "Azure CDN のログ分析 | Microsoft Docs"
description: "顧客は、Azure CDN のログ分析を有効にすることができます。"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: v-semcev
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 03ff74ae4e40d3f2279caaf4f73e9b4aac6a2ebb
ms.contentlocale: ja-jp
ms.lasthandoff: 08/08/2017

---

# <a name="diagnostics-logs-for-azure-cdn"></a>Azure CDN の診断ログ

アプリケーションの CDN を有効にした後、CDN の使用状況の監視、配信の正常性の確認、潜在的な問題のトラブルシューティングが必要になる場合があるかもしれません。 Azure CDN は、[コア分析](cdn-analyze-usage-patterns.md)と[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)によって、これらの機能を提供しています。

## <a name="cdn-core-analytics"></a>CDN コア分析
現在 Azure CDN ユーザーであり、Verizon の Standard/Premium プロファイルがあれば、Azure ポータルの [管理] オプションからアクセスできる補助ポータルにコア分析を表示することができます。 

## <a name="azure-diagnostic-logs"></a>Azure 診断ログ

この新しい機能を使用して、コア分析をすぐに表示し、1 つまたは複数の宛先に保存できます。

 - Azure ストレージ アカウント
 - Azure Event Hubs
 - [OMS Log Analytics リポジトリ](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
 この機能は、Verizon (Standard/Premium) および Akamai (Standard) の CDN プロファイルに属しているすべての CDN エンドポイントで使用できます。

診断ログにより、基本的な使用メトリックを CDN エンドポイントからさまざまなソースにエクスポートして、それらをカスタマイズした方法で使用できるようになります。 たとえば、次の種類のデータのエクスポートを実行できます。

- BLOB ストレージへのデータのエクスポート、CSV へのエクスポート、Excel でのグラフの生成。
- イベント ハブへのデータのエクスポートと、他の Azure サービスのデータとの関連付け。
- ログ分析へのデータのエクスポートと、自分の OMS ワークスペースでのデータ表示

次の図は、データに対する一般的な CDN コア分析ビューを示しています。

![ポータル - 診断ログ](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*図 1 - CDN コア分析ビュー*

以下のチュートリアルでは、コア分析データのスキーマ、この機能を有効にしてさまざまな宛先に配信するための手順、そしてその保存先からの使用方法について説明します。

## <a name="enable-logging-with-azure-portal"></a>Azure ポータルを使用したログの有効化

> [!NOTE]
> 既定では、診断ログは**オフ**になっています。 

CDN コア分析を使用してログ記録を有効にするには、次の手順に従います。

[Azure ポータル](http://portal.azure.com)にサインインします。 ワークフローで CDN をまだ有効にしていない場合は、続行する前に [Azure CDN を有効にしてください](cdn-create-new-endpoint.md)。

1. ポータルの、**CDN プロファイル**に移動します。
2. CDN プロファイルを選択して、**診断ログ**を有効にする CDN エンドポイントを選択します。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. **[監視]** セクションの下の **[診断ログ]** に移動して、ステータスを**[オン]** に変更します。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Azure Storage でログ記録を有効化する
    
Azure Storage を使用してログを保存するには、**[ストレージ アカウントへのアーカイブ]** を選択し、保持日数を選択し、**[ログ]** の下の **[CoreAnalytics]** をクリックします。

![ポータル - 診断ログ](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png)

*図 2 - Azure Storage のログ記録*

### <a name="logging-with-oms-log-analytics"></a>OMS Log Analytics によるログ記録

OMS Log Analytics を使用してログを保存するには、次の手順に従います。

1. **[診断ログ]** ブレードで、**[監視]** の下の **[Log Analytics への送信]** を選択します。 

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. [構成] をクリックして、Log Analytics のログ記録を構成します。 前のワークスペースを選択するか、新しいワークスペースを作成できるダイアログに移動します。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. **[新しいワークスペースの作成]** をクリックします。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/07_Create-new.png)

4. 次に、新しいワークスペースの名前、既存のサブスクリプション、リソース グループ (新規または既存)、場所、および価格レベルを選択する必要があります。 この構成をダッシュボードにピン留めするオプションがあります。 構成を完了するには、[OK] をクリックします。

    次に、OMS ワークスペースとリソース グループの名前を確認します。 名前は一意である必要があり、文字、数字、およびハイフンのみ使用できます。 スペースとアンダースコアは使用できません。 

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/08_Workspace-resource.png)

5. ワークスペースが作成されたことを通知する短いメッセージが表示された後、ログ記録構成画面に戻ります。 Log Analytics ワークスペースの名前を確認できます。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/09_Return-to-logging.png)

    Log Analytics の構成を設定したら、CDN ログ記録用の [CoreAnalytics] チェックボックスがオンになっていることも確認します。

6. 準備ができたら、構成ダイアログ ボックスの上部にある **[保存]** ボタンをクリックします。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/10_Save-me.png)

    **[保存]** ボタンがアクティブでなくなり、[オン/オフ] ボタンが [オン] になりますが、色は紫ではなく青になります。

7. 新しい OMS ワークスペースを確認する場合は、Azure ポータルのダッシュボードに移動し、Log Analytics ワークスペースの名前をクリックします。 ワークスペースが表示されます (左の OMS ワークスペースが強調表示されていることを確認してください)。 OMS リポジトリのワークスペースを表示するには、[OMS Portal] タイルをクリックします。 

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    以上で、OMS リポジトリにデータを記録する準備ができました。 そのデータを使用するためには、[OMS ソリューション](#consuming-oms-log-analytics-data)を使用する必要があります。これについては、この記事の中で後で説明します。

ログ データの遅延の詳細については、[こちら](#log-data-delays)を参照してください。

## <a name="enable-logging-with-powershell"></a>PowerShell を使用したログの有効化

次の例は、Azure PowerShell コマンドレットを使用して診断ログを有効にして取得する方法を示しています。

###<a name="enabling-diagnostic-logs-in-a-storage-account"></a>ストレージ アカウントでの診断ログの有効化

まずログインしてサブスクリプションを選択します。

    Login-AzureRmAccount 

    Select-AzureSubscription -SubscriptionId 


ストレージ アカウントで診断ログを有効にするには、次のコマンドを使用します。

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```
OMS ワークスペースで診断ログを有効にするには、次のコマンドを使用します。

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
```



## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Azure Storage からの診断ログの使用
このセクションでは、CDN コア分析のスキーマと、これが Microsoft Azure Storage アカウントの内部でどのように編成されているかについて説明し、ログを CSV ファイルにダウンロードするためのサンプル コードを提供します。

### <a name="using-microsoft-azure-storage-explorer"></a>Microsoft Azure ストレージ エクスプローラーの使用
Azure ストレージ アカウントからコア分析データにアクセスするには、最初にストレージ アカウントの内容にアクセスするためのツールが必要です。 市場にはいくつかのツールがありますが、お勧めするツールは Microsoft Azure ストレージ エクスプローラーです。 このツールは、[こちら](http://storageexplorer.com/)からダウンロードできます。 ソフトウェアをダウンロードしてインストールしたら、CDN 診断ログの保存先として構成したのと同じ Microsoft Azure Storage アカウントを使用するように構成します。

1.  **Microsoft Azure ストレージ エクスプローラー**を開きます
2.  ストレージ アカウントを見つけます
3.  このストレージ アカウントの下にある **“BLOB コンテナー”** ノードに移動して、ノードを展開します
4.  **“insights-logs-coreanalytics”** という名前のコンテナーを選択してダブルクリックします。
5.  最初のレベルが **“resourceId=”** などで始まる結果が右側のウィンドウに表示されます。 **PT1H.json** というファイルが表示されるまでクリックを続けます。 パスの説明については、次のメモを参照してください。
6.  BLOB の **PT1H.json** はそれぞれ特定の CDN エンドポイントまたはそのカスタム ドメインの 1 時間分の分析ログを表します。
7.  この JSON ファイルの内容のスキーマについては、「コア分析ログのスキーマ」のセクションで説明しています


> [!NOTE]
> **BLOB パスの形式**
> 
> コア分析のログは 1 時間ごとに生成されます。 1 時間以内のすべてのデータが収集され、JSON ペイロードとして 1 つの Azure BLOB 内に格納されます。 階層構造がある場合は、この Azure BLOB へのパスが表示されます。 これは、ストレージ エクスプローラー ツールが '/' をディレクトリの区切り記号と見なし、利便性のために階層を表示するためです。 実際には、パス全体が BLOB 名を表します。 BLOB のこの名前は次の名前付け規則に従っています   
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**フィールドの説明:**

|値|説明|
|-------|---------|
|サブスクリプション ID    |Azure サブスクリプションの ID。 GUID 形式が使用されています。|
|リソース |グループ名。CDN のリソースが属するリソース グループの名前|
|プロファイル名 |CDN プロファイルの名前|
|エンドポイント名 |CDN エンドポイントの名前|
|年|  年の 4 桁表記 (例: 2017)|
|月| 月の 2 桁表記。 01 = 1 月 ... 12 = 12 月|
|日|   日付の 2 桁表記|
|PT1H.json| 分析データが格納されている実際の JSON ファイル|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>CSV ファイルへのコア分析データのエクスポート

コア分析にアクセスしやすくするためのツールのサンプル コードが用意されています。このツールによって、JSON ファイルをコンマ区切り形式のフラット ファイルにダウンロードし、これを使用してグラフやその他の集計を簡単に作成できるようになります。

このツールを使用する方法を次に示します。

1.  GitHub のリンクを参照します: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.  コードのダウンロード
3.  指示に従ってコンパイルして構成します
4.  ツールを実行します
5.  生成される CSV ファイルには単純なフラット階層の分析データが示されています。

## <a name="consuming-diagnostics-logs-from-an-oms-log-analytics-repository"></a>OMS Log Analytics リポジトリの診断ログの使用
Log Analytics は、Operations Management Suite (OMS) のサービスであり、クラウド環境とオンプレミス環境を監視して可用性とパフォーマンスを維持します。 Log Analytics を使用すると、クラウドおよびオンプレミスの環境内にあるリソースによって生成されたデータや、他の監視ツールのデータを収集し、複数のソースにわたる分析を行えます。 

Log Analytics を使用するには、Azure OMS Log Analytics リポジトリに対する[ログ記録を有効にする](#enable-logging-with-azure-storage)必要があります。これについては、この記事の前半で説明しています。

### <a name="using-the-oms-repository"></a>OMS リポジトリの使用

 次の図は、リポジトリの入力と出力のアーキテクチャを示しています。

![OMS Log Analytics リポジトリ](./media/cdn-diagnostics-log/12_Repo-overview.png)

*図 3 - Log Analytics リポジトリ*

管理ソリューションを使用して、さまざまな方法でデータを表示できます。 管理ソリューションは、[Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions)から取得できます。

Azure Marketplace から各ソリューションの下にある **[Get it now (今すぐ入手する)]** リンクをクリックして管理ソリューションをインストールすることができます。

### <a name="adding-an-oms-cdn-management-solution"></a>OMS CDN 管理ソリューションの追加

管理ソリューションを追加するには、次の手順に従います。

1.   まだ Azure ポータルにサインインしていない場合は Azure サブスクリプションを使用してサインインし、ダッシュボードに移動します。
    ![Azure ダッシュボード](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. **[新規]** ブレードの **[Marketplace]** で、**[監視 + 管理]** を選択します。

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. **[監視 + 管理]** ブレードで、**[すべて表示]** をクリックします。

    ![すべて表示](./media/cdn-diagnostics-log/15_See-all.png)

4.  検索ボックスで CDN を検索します。

    ![すべて表示](./media/cdn-diagnostics-log/16_Search-for.png)

5.  **[Azure CDN Core Analytics]** を選択します。 

    ![すべて表示](./media/cdn-diagnostics-log/17_Core-analytics.png)

6.  **[作成]** をクリックすると、新しい OMS ワークスペースを作成するか既存のものを使用するかを質問されます。 

    ![すべて表示](./media/cdn-diagnostics-log/18_Adding-solution.png)

7.  前に作成したワークスペースを選択します。 次に、オートメーション アカウントを追加する必要があります。

    ![すべて表示](./media/cdn-diagnostics-log/19_Add-automation.png)

8. 次の画面は、入力する必要があるオートメーション アカウント フォームを示しています。 

    ![すべて表示](./media/cdn-diagnostics-log/20_Automation.png)

9. オートメーション アカウントを作成した後、ソリューションを追加できます。 **[作成]** ボタンをクリックします。

    ![すべて表示](./media/cdn-diagnostics-log/21_Ready.png)

10. これで、ソリューションがワークスペースに追加されます。 Azure ポータルのダッシュボードに戻ります。

    ![すべて表示](./media/cdn-diagnostics-log/22_Dashboard.png)

    ワークスペースに移動するために作成した Log Analytics ワークスペースをクリックします。 

11. OMS ポータルで新しいソリューションを確認するには、**[OMS Portal]** タイルをクリックします。

    ![すべて表示](./media/cdn-diagnostics-log/23_workspace.png)

12. OMS ポータルは次の画面のようになります。

    ![すべて表示](./media/cdn-diagnostics-log/24_OMS-solution.png)

    データのさまざまなビューを表示するには、いずれかのタイルをクリックします。

    ![すべて表示](./media/cdn-diagnostics-log/25_Interior-view.png)

    左右にスクロールすることで、データの個々のビューを表すその他のタイルを確認できます。 

    いずれかのタイルをクリックすると、データの詳細が表示されます。

     ![すべて表示](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>プランと価格レベル

[こちら](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)で OMS 管理ソリューションのプランと価格レベルを確認できます。

### <a name="customizing-views"></a>ビューのカスタマイズ

**ビュー デザイナー**を使用して、データのビューをカスタマイズできます。 OMS ワークスペースに移動し、**[ビュー デザイナー]** タイルをクリックしてデザインを開始します。

![ビュー デザイナー](./media/cdn-diagnostics-log/27_Designer.png)

左からグラフの種類をドラッグ アンド ドロップし、左側で分析するデータの詳細を入力できます。

![ビュー デザイナー](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>ログ データの遅延

Verizon ログ データの遅延 | Akamai ログ データの遅延
--- | ---
Verizon のログ データには 1 時間の遅延があり、エンドポイントへの伝達が完了してから表示が開始されるまでに最大で 2 時間かかります。 | Akamai のログ データには 24 時間の遅延があり、24 時間より前に作成されている場合には、表示が開始されるまでに最大で 2 時間かかります。 ログが最近作成された場合は、表示が開始されるまでに最大で 25 時間かかることがあります。

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>CDN コア分析での診断ログの種類

現在のところ、CDN の POP/エッジから見たHTTP 応答の統計とエグレスの統計を示すメトリックを含むコア分析ログのみ提供されています。

### <a name="core-analytics-metrics-details"></a>コア分析メトリックの詳細
コア分析ログで使用可能なメトリックの一覧を次の表に示します。 プロバイダーによっては使用できないメトリックがありますが、違いはほとんどありません。 以下の表は、そのメトリックがプロバイダーで使用できるかどうかも示しています。 メトリックを使用できるのは、トラフィックが存在する CDN エンドポイントに対してのみであることに注意してください。


|メトリック                     | 説明   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |この期間中にヒットした要求の合計数| あり  |あり   |
| RequestCountHttpStatus2xx |2xx の HTTP コード (200、202 など) になった要求の合計数              | あり  |あり   |
| RequestCountHttpStatus3xx | 3xx の HTTP コード (300、302 など) になった要求の合計数              | あり  |あり   |
| RequestCountHttpStatus4xx |4xx の HTTP コード (400、404 など) になった要求の合計数               | あり   |あり   |
| RequestCountHttpStatus5xx | 5xx の HTTP コード (500、504 など) になった要求の合計数              | あり  |あり   |
| RequestCountHttpStatusOthers |  その他のすべての HTTP コード (2xx-5xx 以外) の数 | あり  |あり   |
| RequestCountHttpStatus200 | HTTP コード応答 200 になった要求の合計数              |いいえ   |あり   |
| RequestCountHttpStatus206 | HTTP コード応答 206 になった要求の合計数              |いいえ   |あり   |
| RequestCountHttpStatus302 | HTTP コード応答 302 になった要求の合計数              |いいえ   |あり   |
| RequestCountHttpStatus304 |  HTTP コード応答 304 になった要求の合計数             |いいえ   |あり   |
| RequestCountHttpStatus404 | HTTP コード応答 404 になった要求の合計数              |いいえ   |あり   |
| RequestCountCacheHit |キャッシュ ヒットが生じた要求の合計数。 これは、POP からクライアントに対して、アセットが直接処理されたことを意味します。               | あり  |いいえ   |
| RequestCountCacheMiss | キャッシュ ミスが生じた要求の合計数。 これは、クライアントに最も近い POP でアセットが見つからなかったため、配信元から取得されたことを意味します。              |あり   | いいえ  |
| RequestCountCacheNoCache | エッジでのユーザーの構成が原因でキャッシュされなかったアセットに対する要求の合計数。              |あり   | いいえ  |
| RequestCountCacheUncacheable | アセットの Cache-Control および Expires ヘッダーによりキャッシュされなかったアセットへの要求の合計数。この場合、POP または HTTP クライアントではキャッシュすべきでないことを示します                |あり   |いいえ   |
| RequestCountCacheOthers | 上記に含まれないキャッシュの状態の要求の合計数。              |あり   | いいえ  |
| EgressTotal | 送信データ転送 (GB)              |あり   |あり   |
| EgressHttpStatus2xx | HTTP 状態コードが 2xx の応答の送信データ転送* (GB)            |あり   |いいえ   |
| EgressHttpStatus3xx | HTTP 状態コードが 3xx の応答の送信データ転送 (GB)              |あり   |いいえ   |
| EgressHttpStatus4xx | HTTP 状態コードが 4xx の応答の送信データ転送 (GB)               |あり   | いいえ  |
| EgressHttpStatus5xx | HTTP 状態コードが 5xx の応答の送信データ転送 (GB)               |あり   |  いいえ |
| EgressHttpStatusOthers | その他の HTTP 状態コードの応答の送信データ転送 (GB)                |あり   |いいえ   |
| EgressCacheHit |  CDN の POP/エッジで CDN キャッシュから直接配信された応答の送信データ転送  |あり   |  いいえ |
| EgressCacheMiss | 最も近い POP サーバーで見つからず、配信元サーバーから取得された応答の送信データ転送              |あり   |  いいえ |
| EgressCacheNoCache | エッジでのユーザーの構成が原因でキャッシュされなかったアセットの送信データ転送。                |あり   |いいえ   |
| EgressCacheUncacheable | アセットの Cache-Control および/または Expires ヘッダーによりキャッシュされなかったアセットの送信データ転送。この場合、POP または HTTP クライアントではキャッシュすべきでないことを示します                    |あり   | いいえ  |
| EgressCacheOthers |  その他のキャッシュ シナリオの送信データ転送。             |あり   | いいえ  |

*送信データ転送は、CDN の POP サーバーからクライアントに配信されたトラフィックを指します。


### <a name="schema-of-the-core-analytics-logs"></a>コア分析ログのスキーマ 

すべてのログが JSON 形式で格納され、各エントリには次のスキーマの文字列フィールドが含まれます。

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

ここで 'time' とは、統計情報を報告する時間の境界の開始時刻を表します。 メトリックが CDN プロバイダーによってサポートされていない場合、double または整数の値の代わりに null 値が設定されます。 この null 値は、メトリックがないことを示し、0 の値とは異なります。 エンドポイントに構成されたドメインごとにこれらのメトリックのセットが 1 つあることにも注意してください。

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
* [Azure OMS Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/en-us/rest/api/loganalytics)








