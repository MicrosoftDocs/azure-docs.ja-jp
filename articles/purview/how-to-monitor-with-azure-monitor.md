---
title: Azure Purview を監視する方法
description: Azure Monitor を使用して Azure Purview のメトリック、アラート、診断設定を構成する方法について説明します。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 4cb3965d359980856c238cd563ed8b761754660b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101667746"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Azure Monitor での Azure Purview メトリック

この記事では、Azure Monitor を使用して Azure Purview のメトリック、アラート、診断設定を構成する方法について説明します。

## <a name="monitor-azure-purview"></a>Azure Purview の監視

Azure Purview の管理者は、Azure Monitor を使用して、Purview アカウントの操作状態を追跡できます。 メトリックは、潜在的な問題の追跡、トラブルシューティング、Purview アカウントの信頼性の向上のためのデータ ポイントを提供するために収集されます。 Azure Purview で発生するイベントについて、メトリックが Azure monitor に送信されます。

## <a name="aggregated-metrics"></a>集計されたメトリック

メトリックには、Purview アカウントの Azure portal からアクセスできます。 メトリックへのアクセスは、Purview アカウントのロールの割り当てによって制御されます。 ユーザーはメトリックを確認するためには、Azure Purview の "監視閲覧者" ロールに属している必要があります。 ロールのアクセス レベルの詳細については、[監視閲覧者ロールのアクセス許可](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles)に関する記事をご覧ください。

Purview アカウントを作成したユーザーは、メトリックを表示するためのアクセス許可を自動的に取得します。 他のユーザーがメトリックを表示できるようにするには、次の手順に従って **監視閲覧者** ロールに追加します。

### <a name="add-a-user-to-the-monitoring-reader-role"></a>ユーザーを監視閲覧者ロールに追加する

**監視閲覧者** ロールにユーザーを追加するには、Purview アカウントの所有者またはサブスクリプション所有者が次の手順を実行します。

1. [Azure portal](https://portal.azure.com) に移動して、Azure Purview アカウント名を検索します。

2. **[アクセス制御 (IAM)]** を選択します。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="IAM へのアクセス方法を示すスクリーンショット。":::

3. **[ロールの割り当てを追加する]** を選択します。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="ロールの割り当ての追加方法を示すスクリーンショット。":::

4. [ロール] で **[監視閲覧者]** を選択し、アクセスの割り当て先を **[Azure AD のユーザー、グループ、サービス プリンシパル]** に設定します。 そして、AAD アカウントをメトリックにアクセスするように割り当てます。  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="監視閲覧者ロールの追加方法を示すスクリーンショット。":::

## <a name="metrics-visualization"></a>メトリックの視覚化

**監視閲覧者** ロールのユーザーは、Azure Monitor に送信された集計メトリックと診断ログを表示できます。 メトリックは、対応する Purview アカウントの Azure portal に一覧表示されます。 Azure portal で [メトリック] セクションを選択して、使用可能なすべてのメトリックの一覧を表示します。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="使用可能な Purview メトリックのセクションを示すスクリーンショット。" lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Azure Purview ユーザーは、Azure Purview アカウントの管理センターからメトリック ページに直接アクセスすることもできます。 Purview の管理センターのメイン ページで [Azure Monitor] を選択して Azure portal を起動します。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="管理センターから Purview メトリックを起動するためのスクリーンショット。" lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>使用可能なメトリック

Azure portal のメトリック セクションの使用方法を理解するには、次の 2 つのドキュメントを事前に確認してください。 [メトリックス エクスプローラーの概要](../azure-monitor/essentials/metrics-getting-started.md)に関する記事と、[メトリックス エクスプローラーの高度な機能](../azure-monitor/essentials/metrics-charts.md)に関する記事です。

次の表に、Azure portal で確認できるメトリックの一覧を示します。

| メトリックの名前 | メトリック名前空間 | 集計の種類 | 説明 |
| ------------------- | ------------------- | ------------------- | ----------------- |
| Scan Cancelled (キャンセルされたスキャン) | Automated scan (自動スキャン) | SUM <br> Count | 一定期間におけるキャンセルされたデータ ソースのスキャンを集計します |
| Scan Completed (完了したスキャン) | Automated scan (自動スキャン) | SUM <br> Count | 一定期間に完了したデータ ソースのスキャンを集計します |
| Scan Failed (失敗したスキャン) | Automated scan (自動スキャン) | SUM <br> Count | 一定期間における失敗したデータ ソースのスキャンを集計します |
| Scan time taken (スキャンの所要時間) | Automated scan (自動スキャン) | Min <br> Max <br> SUM <br> Avg | 一定期間におけるスキャンにかかった合計時間を集計します |

## <a name="diagnostic-logs-to-azure-storage-account"></a>Azure Storage アカウントへの診断ログ

未加工のテレメトリ イベントは Azure Monitor に出力されます。 イベントは、詳細な分析のために、選択した顧客のストレージ アカウントにログ記録できます。 ログのエクスポートは、Azure portal の Purview アカウントの診断設定を使用して行います。

手順に従って、Azure Purview アカウントの診断設定を作成します。

1. 次の記事に従って、プラットフォーム ログとメトリックを収集するための新しい診断設定を作成します。[プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md) 宛先は Azure ストレージ アカウントとしてのみ選択します。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="診断ログの作成を示すスクリーンショット。" lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. ストレージ アカウントにイベントをログします。 診断ログのアーカイブには、専用のストレージ アカウントを使用することをお勧めします。 この記事に従って[ストレージ アカウントを作成します](../storage/common/storage-account-create.md?tabs=azure-portal)。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="診断ログ用のストレージ アカウントの割り当てを示すスクリーンショット。" lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

新しく作成されたストレージ アカウントでログの受信が開始されるまでに最大 15 分かかります。 [Azure Storage アカウントでのリソース ログのデータ保持とスキーマを確認します](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)。 診断ログが構成されると、イベントがストレージ アカウントに送られるようになります。

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

イベントによりスキャンのライフ サイクルが追跡されます。 スキャン操作によって、進行状況が [処理待ち] から [実行中]、そして最終的な状態である [成功] | [失敗] | [キャンセル済み] まで、状態のシーケンスを通じて追跡されます イベントは状態が遷移するたびにログされます。イベントのスキーマには次のプロパティが設定されます。

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

イベント インスタンスのサンプル ログを以下のセクションに示します。

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>次のステップ

[アセットの分析情報を表示する](asset-insights.md)