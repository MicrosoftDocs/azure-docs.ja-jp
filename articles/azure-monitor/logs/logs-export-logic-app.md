---
title: ロジック アプリを使用して Log Analytics ワークスペースから Azure ストレージにデータをアーカイブする
description: Azure Logic Apps を使用して Log Analytics ワークスペースにあるデータを照会し、Azure Storage に送信する方法について説明します。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: fd66aa1f10a32d94d515a1f0aa25c25331796a8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035689"
---
# <a name="archive-data-from-log-analytics-workspace-to-azure-storage-using-logic-app"></a>ロジック アプリを使用して Log Analytics ワークスペースから Azure ストレージにデータをアーカイブする
この記事では、[Azure Logic Apps](../../logic-apps/index.yml) を使用して Azure Monitor の Log Analytics ワークスペースにあるデータを照会し、Azure Storage に送信する方法について説明します。 このプロセスは、監査とコンプライアンスのシナリオに合わせて Azure Monitor のログ データをエクスポートする必要がある場合や、別のサービスでこのデータを取得できるようにする場合に使用します。  

## <a name="other-export-methods"></a>その他のエクスポート方法
この記事で説明する方法は、ロジック アプリを使用したログ クエリからのスケジュールされたエクスポートについて説明したものです。 特定のシナリオでデータをエクスポートするその他のオプションには、次のようなものがあります。

- Log Analytics ワークスペースのすべてのデータを Azure ストレージ アカウントまたはイベント ハブにエクスポートするには、Azure Monitor ログの Log Analytics ワークスペース データ エクスポート機能を使用します。 「[Azure Monitor の Log Analytics ワークスペース データ エクスポート (プレビュー)](logs-data-export.md)」を参照してください。
- ロジック アプリを使用したワンタイム エクスポート。 「[Logic Apps および Power Automate の Azure Monitor Logs コネクタ](logicapp-flow-connector.md)」を参照してください。
- PowerShell スクリプトを使用したローカル コンピューターへのワンタイム エクスポート。 「[Invoke-AzOperationalInsightsQueryExport]](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport) 」を参照してください。

## <a name="overview"></a>概要
この手順では、[Azure Monitor Logs コネクタ](/connectors/azuremonitorlogs/)を使用します。これにより、ロジック アプリからログ クエリを実行し、その出力をワークフロー内の他のアクションで使用できるようになります。 この手順では、[Azure Blob Storage コネクタ](/connectors/azureblob/)を使用して、クエリ出力を Azure Storage に送信します。 その他のアクションについては以降のセクションで説明します。

![ロジック アプリの概要](media/logs-export-logic-app/logic-app-overview.png)

Log Analytics ワークスペースからデータをエクスポートする場合は、ログ データをフィルター処理して集計し、ロジック アプリ ワークフローによって処理されるデータ量を必要なデータに制限するようにクエリを最適化する必要があります。 たとえば、サインイン イベントをアーカイブする必要がある場合は、次のクエリを使用して必要なイベントをフィルター処理し、必要なフィールドのみを表示することができます。 

```json
SecurityEvent
| where EventID == 4624 or EventID == 4625
| project TimeGenerated , Account , AccountType , Computer
```

スケジュールに基づいてデータをエクスポートする場合は、クエリで ingestion_time() 関数を使用して、遅れて到着するデータを見逃さないようにします。 インジェスト時間を使用すると、ネットワークまたはプラットフォームの問題が原因で遅延したデータが、次回のロジック アプリ実行に確実に含まれるようになります。 「[Azure Monitor Logs アクションの追加](#add-azure-monitor-logs-action)」を参照してください。

## <a name="prerequisites"></a>前提条件
この手順を完了するには、次の前提条件が揃っている必要があります。

- Log Analytics ワークスペース。 ロジック アプリを作成するユーザーには、少なくともワークスペースに対する読み取りアクセス許可が必要です。 
- Azure ストレージ アカウント。 ストレージ アカウントは、Log Analytics ワークスペースと同じサブスクリプションに含まれている必要はありません。 ロジック アプリを作成するユーザーには、ストレージ アカウントに対する書き込みアクセス許可が必要です。 


## <a name="connector-limits"></a>コネクタの制限
Azure Monitor の Log Analytics ワークスペースとログ クエリは、顧客を保護および分離し、サービスの品質を維持する制限を含むマルチテナント機能サービスです。 大量のデータを照会する場合は、次の制限事項を考慮する必要があります。これは、ロジック アプリの繰り返しとログ クエリの構成方法に影響する可能性があります。

- ログ クエリで 500,000 より多い行を返すことはできません。
- ログ クエリで 64,000,000 より多いバイト数を返すことはできません。
- 既定では、ログ クエリの実行時間は 10 分を超えることはできません。 
- Log Analytics コネクタは、1 分あたり呼び出し 100 回に制限されます。


## <a name="create-container-in-the-storage-account"></a>ストレージ アカウントにコンテナーを作成する
「[コンテナーを作成する](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)」の手順に従って、エクスポートされたデータを格納するためのコンテナーをストレージ アカウントに追加します。 この記事で使用するコンテナーの名前は **loganalytics-data** ですが、任意の名前を使用できます。


## <a name="create-logic-app"></a>ロジック アプリの作成

Azure portal で **[Logic Apps]** に移動し、 **[追加]** をクリックします。 新しいロジック アプリを格納する **サブスクリプション**、**リソース グループ**、**リージョン** を選択し、一意の名前を付けます。 **[Log Analytics]** 設定を有効にすると、「[Azure Monitor ログを設定し、Azure Logic Apps の診断データを収集する](../../logic-apps/monitor-logic-apps-log-analytics.md)」で説明されているように、ランタイム データおよびイベントに関する情報を収集できます。 この設定は、Azure Monitor Logs コネクタを使用する場合には不要です。

![ロジック アプリを作成する](media/logs-export-logic-app/create-logic-app.png)


**[確認および作成]** 、 **[作成]** の順にクリックします。 デプロイが完了したら、 **[リソースに移動]** をクリックし、 **[Logic Apps デザイナー]** を開きます。

## <a name="create-a-trigger-for-the-logic-app"></a>ロジック アプリのトリガーを作成する
**[一般的なトリガーで開始する]** の下で、 **[繰り返し]** を選択します。 これにより、一定の間隔で自動的に実行されるロジック アプリが作成されます。 ワークフローが 1 日 1 回実行されるように、アクションの **[頻度]** ボックスで、 **[日]** を選択し、 **[間隔]** ボックスに「**1**」と入力します。

![繰り返しアクション](media/logs-export-logic-app/recurrence-action.png)


### <a name="add-azure-monitor-logs-action"></a>Azure Monitor Logs アクションの追加
**[+ 新しいステップ]** をクリックして、繰り返しアクションの後に実行するアクションを追加します。 **[アクションを選択してください]** の下で「**azure monitor**」と入力して、 **[Azure Monitor Logs]** を選択します。

![Azure Monitor Logs のアクション](media/logs-export-logic-app/select-azure-monitor-connector.png)

**[Azure Log Analytics – クエリの実行と結果の一覧表示]** をクリックします。

![ロジック アプリ デザイナーのステップに追加される新しいアクションのスクリーンショット。 [アクションを選択してください] の下に Azure Monitor Logs が強調表示されています。](media/logs-export-logic-app/select-query-action-list.png)

テナントを選択し、ワークフローでのクエリの実行に使用するアカウントを使用して、Log Analytics ワークスペースへのアクセス権を付与するように求められます。


## <a name="add-azure-monitor-logs-action"></a>Azure Monitor Logs アクションの追加
Azure Monitor Logs アクションを使用すると、実行するクエリを指定できます。 この例で使用されているログ クエリの場合、1 時間ごとの繰り返しに合わせて最適化されており、特定の実行時間のために取り込まれたデータを収集します。 たとえば、ワークフローが 4:35 に実行される場合、時間の範囲は 4:00 から 5:00 までになります。 ロジック アプリを別の頻度で実行するように変更する場合は、クエリも変更する必要があります。 たとえば、繰り返しを毎日実行するように設定した場合は、クエリで startTime を startofday(make_datetime(year,month,day,0,0)) に設定します。 

Log Analytics ワークスペースの **サブスクリプション** と **リソース グループ** を選択します。 **[リソースの種類]** で *[Log Analytics ワークスペース]* を選択し、 **[リソース名]** でワークスペースの名前を選択します。

**[クエリ]** ウィンドウに次のログ クエリを追加します。  

```Kusto
let dt = now();
let year = datetime_part('year', dt);
let month = datetime_part('month', dt);
let day = datetime_part('day', dt);
let hour = datetime_part('hour', dt);
let startTime = make_datetime(year,month,day,hour,0)-1h;
let endTime = startTime + 1h - 1tick;
AzureActivity
| where ingestion_time() between(startTime .. endTime)
| project 
    TimeGenerated,
    BlobTime = startTime, 
    OperationName ,
    OperationNameValue ,
    Level ,
    ActivityStatus ,
    ResourceGroup ,
    SubscriptionId ,
    Category ,
    EventSubmissionTimestamp ,
    ClientIpAddress = parse_json(HTTPRequest).clientIpAddress ,
    ResourceId = _ResourceId 
```

**[時間の範囲]** には、**TimeGenerated** 列に基づいてクエリに含まれるレコードを指定します。 これは、クエリで選択した時間の範囲と同じかそれよりも大きい値に設定する必要があります。 このクエリには **TimeGenerated** 列が使用されていないため、 **[クエリに設定します]** オプションは使用できません。 時間の範囲の詳細については、「[クエリ スコープ](./scope.md)」を参照してください。 

**[時間の範囲]** で **[過去 4 時間]** を選択します。 これにより、インジェスト時間が **TimeGenerated** よりも大きいレコードが結果に含まれるようになります。
   
![[クエリの実行と結果の視覚化] という名前の新しい Azure Monitor Logs アクションの設定のスクリーンショット。](media/logs-export-logic-app/run-query-list-action.png)


### <a name="add-parse-json-activity-optional"></a>Parse JSON アクティビティの追加 (省略可能)
**[クエリの実行と結果の一覧表示]** アクションの出力は JSON 形式です。 このデータを解析し、 **[作成]** アクションの準備の一環として操作できます。 

受け取りたいペイロードを表す JSON スキーマを指定することができます。 デザイナーは、このスキーマを使って JSON コンテンツを解析することにより、JSON コンテンツ内の各種プロパティを表すユーザー フレンドリなトークンを生成します。 その後、ロジック アプリのワークフローの至る所で、これらのプロパティを簡単に参照して使用することができます。 


**[+ 新しいステップ]** をクリックし、 **[+ アクションの追加]** をクリックします。 **[アクションを選択してください]** の下に「**json**」と入力し、 **[JSON の解析]** を選択します。

![[JSON の解析] アクションを選択する](media/logs-export-logic-app/select-parse-json.png)

**[コンテンツ]** ボックス内をクリックして、前のアクティビティの値の一覧を表示します。 **[クエリの実行と結果の一覧表示]** アクションの **[本文]** を選択します。 これはログ クエリからの出力です。

[![[本文] を選択する](media/logs-export-logic-app/select-body.png)](media/logs-export-logic-app/select-body.png#lightbox)

5.  **[サンプルのペイロードを使用してスキーマを生成する]** をクリックします。 ログ クエリを実行し、サンプル ペイロードに使用する出力をコピーします。  このサンプル クエリには次の出力を使用できます。


```json
{
    "TimeGenerated": "2020-09-29T23:11:02.578Z",
    "BlobTime": "2020-09-29T23:00:00Z",
    "OperationName": "Returns Storage Account SAS Token",
    "OperationNameValue": "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE",
    "Level": "Informational",
    "ActivityStatus": "Started",
    "ResourceGroup": "monitoring",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000",
    "Category": "Administrative",
    "EventSubmissionTimestamp": "2020-09-29T23:11:02Z",
    "ClientIpAddress": "192.168.1.100",
    "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/monitoring/providers/microsoft.storage/storageaccounts/my-storage-account"
}
```



![JSON ペイロードの解析](media/logs-export-logic-app/parse-json-payload.png)

## <a name="add-the-compose-action"></a>作成アクションの追加
**[作成]** アクションは、解析された JSON 出力を受け取り、BLOB に格納する必要があるオブジェクトを作成します。

**[+ 新しいステップ]** をクリックし、 **[+ アクションの追加]** をクリックします。 **[アクションを選択してください]** の下に「**compose**」と入力して、 **[作成]** アクションを選択します。

![作成アクションの選択](media/logs-export-logic-app/select-compose.png)


**[入力]** ボックスをクリックして、前のアクティビティの値の一覧を表示します。 **[JSON の解析]** アクションの **[本文]** を選択します。 これは、ログ クエリからの解析された出力です。

[![[作成] アクションの本文を選択する](media/logs-export-logic-app/select-body-compose.png)](media/logs-export-logic-app/select-body-compose.png#lightbox)


## <a name="add-the-create-blob-action"></a>BLOB の作成アクションの追加
[BLOB の作成] は、構成された JSON をストレージに書き込むアクションです。

**[+ 新しいステップ]** をクリックし、 **[+ アクションの追加]** をクリックします。 **[アクションを選択してください]** の下に「**blob**」と入力して、 **[BLOB の作成]** アクションを選択します。

![[BLOB の作成] を選択する](media/logs-export-logic-app/select-create-blob.png)

**[接続名]** でストレージ アカウントへの接続の名前を入力し、 **[フォルダーのパス]** ボックスのフォルダー アイコンをクリックして、ストレージ アカウント内のコンテナーを選択します。 **[BLOB 名]** をクリックして、前のアクティビティの値の一覧を表示します。 **[式]** をクリックし、使用する時間間隔に一致する式を入力します。 1 時間ごとに実行されるこのクエリの場合、次の式によって、直前の 1 時間あたりの BLOB 名を設定します。 

```json
subtractFromTime(formatDateTime(utcNow(),'yyyy-MM-ddTHH:00:00'), 1,'Hour')
```

[![BLOB 式](media/logs-export-logic-app/blob-expression.png)](media/logs-export-logic-app/blob-expression.png#lightbox)

**[BLOB コンテンツ]** ボックスをクリックして、前のアクティビティの値の一覧を表示し、 **[作成]** セクションで **[出力]** を選択します。


![BLOB 式の作成](media/logs-export-logic-app/create-blob.png)


## <a name="test-the-logic-app"></a>ロジック アプリをテストする
**[実行]** をクリックして、ワークフローをテストします。 ワークフローにエラーがある場合は、問題が発生したステップに示されます。 実行内容を表示し、エラーを調査するために各ステップにドリルインして入力と出力を表示することができます。 「[Azure Logic Apps でのワークフローの問題のトラブルシューティングと診断](../../logic-apps/logic-apps-diagnosing-failures.md)」を参照してください。

[![実行履歴](media/logs-export-logic-app/runs-history.png)](media/logs-export-logic-app/runs-history.png#lightbox)


## <a name="view-logs-in-storage"></a>ストレージ内のログの表示
Azure portal の **[ストレージ アカウント]** メニューに移動し、ストレージ アカウントを選択します。 **[BLOB]** タイルをクリックし、[BLOB の作成] アクションで指定したコンテナーを選択します。 BLOB の 1 つを選択し、次に **[BLOB の編集]** を選択します。

[![BLOB データ](media/logs-export-logic-app/blob-data.png)](media/logs-export-logic-app/blob-data.png#lightbox)

## <a name="next-steps"></a>次のステップ

- [Azure Monitor のログ クエリ](./log-query-overview.md)についての詳細を見る。
- [Logic Apps](../../logic-apps/index.yml) についての詳細を見る
- [Power Automate](https://flow.microsoft.com) についての詳細を見る。