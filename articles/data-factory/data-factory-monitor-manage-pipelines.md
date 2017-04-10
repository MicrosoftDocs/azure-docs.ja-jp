---
title: "Azure Portal および PowerShell を使用したパイプラインの監視と管理 | Microsoft Docs"
description: "Azure Portal および Azure PowerShell を使用して、作成した Azure Data Factory とパイプラインを監視および管理する方法について説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 2c41c2df135caaead328d8fe05407cfa75cbcb91
ms.openlocfilehash: a486fbe46f9892f6f70dcdcf27edbac63728af6e
ms.lasthandoff: 02/14/2017


---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Azure Portal および PowerShell を使用した Azure Data Factory パイプラインの監視と管理
> [!div class="op_single_selector"]
> * [Azure Portal の使用/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [監視と管理アプリの使用](data-factory-monitor-manage-app.md)


Azure Data Factory では、データの保存、処理、移動の各サービスの全体像について、信頼性の高い情報が得られます。 このサービスでは、次の目的で使用できる監視ダッシュボードが用意されています。

* エンドツーエンドのデータ パイプラインの正常性を素早く評価する。
* 問題を特定し、必要に応じて是正措置を講じる。
* データ系列を追跡する。
* ソース全体のデータ間の関係を追跡する。
* ジョブ実行の全課金履歴、システムの正常性、依存関係を確認する。

この記事では、パイプラインを監視、管理、およびデバッグする方法について説明します。 また、警告を作成して障害について通知を受け取る方法についての情報も提供します。

## <a name="understand-pipelines-and-activity-states"></a>パイプラインとアクティビティの状態の理解
Azure Portal を使用すると、次の操作を行うことができます。

* 図としてデータ ファクトリを表示する。
* パイプライン内のアクティビティを表示する。
* 入力データセットと出力データセットを参照する。

このセクションでは、スライスの状態がどのように移行するかについても説明します。   

### <a name="navigate-to-your-data-factory"></a>Data Factory に移動する
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側のメニューで、**[データ ファクトリ]** をクリックします。 表示されない場合は、**[その他のサービス >]** をクリックし、**[インテリジェンス + 分析]** カテゴリの下にある **[データ ファクトリ]** をクリックします。

   ![[すべて参照] > [データ ファクトリ]](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)

   **[データ ファクトリ]** ブレードにすべてのデータ ファクトリが表示されます。
3. **[データ ファクトリ]** ブレードで、目的のデータ ファクトリを選択します。

    ![データ ファクトリの選択](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   データ ファクトリのホーム ページが表示されます。

   ![Data Factory ブレード](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Data Factory のダイアグラム ビュー
データ ファクトリの**ダイアグラム** ビューでは、データ ファクトリとその資産を監視および管理する&1; つのウィンドウが提供されます。

データ ファクトリの**ダイアグラム** ビューを表示するには、データ ファクトリのホーム ページで **[ダイアグラム]** をクリックします。

![ダイアグラム ビュー](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

ダイアグラムの拡大または縮小、画面に合わせたサイズ変更、100% 表示、レイアウトのロックを行うことができるほか、パイプラインとテーブルを自動で配置することができます。 また、データ系列の情報を確認することもできます (つまり、選択したアイテムのアップストリーム アイテムとダウンストリーム アイテムが表示されます)。

### <a name="activities-inside-a-pipeline"></a>パイプライン内のアクティビティ
1. パイプラインを右クリックして **[パイプラインを開く]** をクリックすると、パイプライン内のすべてのアクティビティとアクティビティの入力データセットおよび出力データセットが表示されます。 この機能は、パイプラインに複数のアクティビティが含まれる場合に、1 つのパイプラインの動作系列を理解するときに便利です。

    ![パイプラインを開くメニュー](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. 次の例では、パイプラインの&2; つのアクティビティとその入力および出力がわかります。 このサンプル パイプラインには、**JoinData** という名前の HDInsight Hive 型アクティビティと、**EgressDataAzure** という名前のコピー型アクティビティがあります。

    ![パイプライン内のアクティビティ](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. 左上隅の階層リンクの **[データ ファクトリ]** リンクをクリックして、データ ファクトリのホーム ページに戻ることができます。

    ![Data Factory に戻る](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>パイプライン内の各アクティビティの状態を表示する
アクティビティによって生成されるデータセットのステータスを表示することにより、アクティビティの現在の状態を確認できます。

次の例では、**BlobPartitionHiveActivity** は正常に実行され、**PartitionedProductsUsageTable** という名前で **Ready** 状態のデータセットを生成しました。

![パイプラインの状態](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

**ダイアグラム**の **PartitionedProductsUsageTable** をダブルクリックすると、パイプライン内のさまざまなアクティビティの実行によって生成されたすべてのスライスが表示されます。 **BlobPartitionHiveActivity** は過去&8; か月間毎月正常に実行され、**Ready** 状態のスライスを生成したことがわかります。

データ ファクトリのデータセット スライスは、次のいずれかの状態になります。

<table>
<tr>
    <th align="left">状態</th><th align="left">下位状態</th><th align="left">Description</th>
</tr>
<tr>
    <td rowspan="8">待機中</td><td>ScheduleTime</td><td>スライスを実行する時刻になっていません。</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>アップストリームの依存関係の準備ができていません。</td>
</tr>
<tr>
<td>ComputeResources</td><td>コンピューティング リソースが使用できません。</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>すべてのアクティビティ インスタンスが、他のスライスを実行しています。</td>
</tr>
<tr>
<td>ActivityResume</td><td>アクティビティは一時停止されており、再開されるまでスライスを実行できません。</td>
</tr>
<tr>
<td>Retry</td><td>アクティビティの実行が再試行されています。</td>
</tr>
<tr>
<td>検証</td><td>検証がまだ開始されていません。</td>
</tr>
<tr>
<td>ValidationRetry</td><td>検証は再試行を待機中です。</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Validating</td><td>検証を実行中です。</td>
</tr>
<td>-</td>
<td>スライスの処理中です。</td>
</tr>
<tr>
<td rowspan="4">Failed</td><td>TimedOut</td><td>アクティビティで許可されている実行時間を超過しました。</td>
</tr>
<tr>
<td>Canceled</td><td>スライスがユーザーの操作によって取り消されました。</td>
</tr>
<tr>
<td>検証</td><td>検証が失敗しました。</td>
</tr>
<tr>
<td>-</td><td>スライスが、生成、検証、またはその両方に失敗しました。</td>
</tr>
<td>Ready</td><td>-</td><td>スライスは使用可能な状態です。</td>
</tr>
<tr>
<td>Skipped</td><td>なし</td><td>スライスは処理中ではありません。</td>
</tr>
<tr>
<td>なし</td><td>-</td><td>スライスは別のステータスで存在していましたが、リセットされました。</td>
</tr>
</table>



**[最近更新されたスライス]** ブレードでスライスのエントリをクリックすると、スライスに関する詳細が表示されます。

![スライスの詳細](./media/data-factory-monitor-manage-pipelines/slice-details.png)

スライスが複数回実行された場合、 **[アクティビティの実行]** ボックスの一覧に複数の行が表示されます。 **[アクティビティの実行]** ボックスの一覧で実行エントリをクリックすると、アクティビティの実行に関する詳細を確認できます。 リストにはすべてのログ ファイルが表示され、存在する場合はエラー メッセージも示されます。 この機能により、Data Factory を離れずにログの確認とデバッグを行うことができます。

![アクティビティ実行の詳細](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

スライスが **Ready** 状態でない場合、現在のスライスの実行をブロックしている準備完了でない上位スライスが、**[準備のできていないアップストリーム スライス]** の一覧に表示されます。 この機能は、スライスが **Waiting** 状態のときに、スライスが待機しているアップストリーム依存関係を理解するのに便利です。

![準備のできていないアップストリーム スライス](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>データセット状態ダイアグラム
データ ファクトリをデプロイした後、パイプラインに有効なアクティブ期間があると、データセット スライスは&1; つの状態から別の状態に遷移します。 現在、スライスのステータスは、次の状態ダイアグラムに従います。

![状態ダイアグラム](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

データ ファクトリのデータセット状態遷移フローは、Waiting -> In-Progress/In-Progress (Validating) -> Ready/Failed となります。

スライスは **Waiting** 状態で始まり、前提条件が満たされるまで待機してから実行されます。 アクティビティの実行が始まると、スライスは **In-Progress** 状態になります。 アクティビティの実行結果は成功か失敗のどちらかになります。 実行結果に応じて、スライスは **Ready** または **Failed** とマークされます。

**Ready** または **Failed** 状態から **Waiting** 状態にスライスをリセットできます。 また、スライスの状態を **Skip** としてマークでき、この状態ではアクティビティは実行されず、スライスは処理されません。

## <a name="manage-pipelines"></a>パイプラインを管理する
Azure PowerShell を使用してパイプラインを管理できます。 たとえば、Azure PowerShell コマンドレットを実行してパイプラインを一時停止および再開できます。

### <a name="pause-and-resume-pipelines"></a>パイプラインを一時停止および再開する
**Suspend-AzureRmDataFactoryPipeline** PowerShell コマンドレットを使用して、パイプラインを一時停止/中断できます。 このコマンドレットは、問題が修正されるまでパイプラインを実行しない場合に便利です。

たとえば、次のスクリーン ショットでは、**productrecgamalbox1dev** データ ファクトリの **PartitionProductsUsagePipeline** に問題が見つかり、パイプラインを中断します。

![パイプラインの中断](./media/data-factory-monitor-manage-pipelines/pipeline-to-be-suspended.png)

パイプラインを中断するには、次の PowerShell コマンドを実行します。

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
For example:

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

**PartitionProductsUsagePipeline** の問題が解決されたら、次の PowerShell コマンドを実行して中断されているパイプラインを再開できます。

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
For example:

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```
## <a name="debug-pipelines"></a>パイプラインをデバッグする
Azure Data Factory では、パイプラインをデバッグおよびトラブルシューティングするための充実した機能が Azure Portal および Azure PowerShell で提供されています。

### <a name="find-errors-in-a-pipeline"></a>パイプラインのエラーを発見する
パイプラインでアクティビティの実行が失敗した場合、パイプラインによって生成されるデータセットは障害のためにエラー状態になります。 次の方法を使用して、Azure Data Factory のエラーをデバッグおよびトラブルシューティングできます。

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Azure Portal を使用してエラーをデバッグする
1. **[テーブル]** ブレードで、**[状態]** が **[Failed]** になっている問題のあるスライスをクリックします。

   ![問題のあるスライスを表示している [テーブル] ブレード](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. **[データ スライス]** ブレードで、失敗したアクティビティの実行をクリックします。

   ![エラーのあるデータ スライス](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. **[アクティビティの実行の詳細]** ブレードでは、HDInsight 処理に関連するファイルをダウンロードできます。 [状態/stderr] の **[ダウンロード]** をクリックし、エラーの詳細を含むエラー ログ ファイルをダウンロードします。

   ![エラーのあるアクティビティ実行詳細ブレード](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>PowerShell を使用してエラーをデバッグする
1. **Azure PowerShell** を開始します。
2. **Get-AzureRmDataFactorySlice** コマンドを実行してスライスとその状態を確認します。 [状態] が **[Failed]**になっているスライスが表示されます。        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   次に例を示します。

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   **StartDateTime** を、Set-AzureRmDataFactoryPipelineActivePeriod に対して指定した StartDateTime の値に置き換えます。
3. **Get-AzureRmDataFactoryRun** コマンドレットを実行して、スライスのアクティビティの実行について詳細を取得します。

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    For example:

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    StartDateTime の値は、前の手順でメモしたエラーまたは問題のあるスライスの開始日時です。 日時は二重引用符で囲む必要があります。
4. エラーの詳細を含む以下のような出力結果が表示されます。

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. 出力結果の ID 値を使用して **Save-AzureRmDataFactoryLog** コマンドレットを実行し、このコマンドレットの **-DownloadLogs** オプションを使用してログ ファイルをダウンロードできます。

    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>パイプラインのエラーを再実行する
### <a name="use-the-azure-portal"></a>Azure ポータルの使用
パイプラインのエラーをトラブルシューティングおよびデバッグした後は、エラー スライスに移動してコマンド バーの **[実行]** ボタンをクリックすることで、エラーを再実行できます。

![障害が発生したスライスの再実行](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

ポリシー エラー (たとえば、データが使用不可能な場合) のためにスライスの検証が失敗した場合は、エラーを修正し、コマンド バーの **[検証]** ボタンをクリックすることによって再度検証できます。
![エラーの修正と検証](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Azure PowerShell の使用
**Set-AzureRmDataFactorySliceStatus** コマンドレットを使用してエラーを再実行できます。 このコマンドレットの構文やその他の詳細については、「[Set-AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx)」のトピックを参照してください。

**例:**

次の例では、Azure Data Factory "WikiADF" のテーブル "DAWikiAggregatedData" のすべてのスライスの状態を 'Waiting' に設定します。

"UpdateType" は "UpstreamInPipeline" に設定されています。これにより、テーブルとすべての依存 (アップストリーム) テーブルの各スライスの状態が "Waiting" に設定されます。 このパラメーターに指定できる他の値は、"Individual" です。

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```

## <a name="create-alerts"></a>アラートを作成する
Azure では、Azure のリソース (データ ファクトリなど) の作成、更新、または削除時に、ユーザー イベントがログに記録されます。 これらのイベントでアラートを作成できます。 Data Factory では、さまざまなメトリックを収集し、メトリックに対してアラートを作成できます。 イベントはリアルタイムの監視に使用し、メトリックは履歴目的に使用することをお勧めします。

### <a name="alerts-on-events"></a>イベントでのアラート
Azure イベントは、Azure のリソースで何が起きているのかを把握するための便利な情報を提供します。 Azure Data Factory の使用時には、次の場合にイベントが生成されます。

* データ ファクトリが作成、更新、または削除された場合。
* データ処理 ("実行") が開始または完了した場合。
* オンデマンド HDInsight クラスターが作成または削除された場合。

これらのユーザー イベントに対してアラートを作成し、サブスクリプションの管理者と共同管理者に電子メール通知を送信するよう構成できます。 さらに、条件が満たされた場合に電子メール通知を受け取る必要があるユーザーの追加の電子メール アドレスを指定できます。 この機能は、Data Factory を常時監視するのではなく、障害が発生したら通知を受け取るようにする場合に便利です。

> [!NOTE]
> 現時点では、ポータルにアラートは表示されません。 すべてのアラートを確認するには、[監視と管理アプリ](data-factory-monitor-manage-app.md)に関するページを参照してください。


#### <a name="specify-an-alert-definition"></a>アラートの定義を指定する
アラートの定義を指定するには、アラートの対象となる操作を記述する JSON ファイルを作成します。 以下の例では、アラートによって RunFinished 操作に関する電子メール通知が送信されます。 具体的には、データ ファクトリで実行が完了し、その実行が失敗していた場合 (Status = FailedExecution) に電子メール通知が送信されます。

```JSON
{
    "contentVersion": "1.0.0.0",
     "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters": {},
    "resources":
    [
        {
            "name": "ADFAlertsSlice",
            "type": "microsoft.insights/alertrules",
            "apiVersion": "2014-04-01",
            "location": "East US",
            "properties":
            {
                "name": "ADFAlertsSlice",
                "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                "isEnabled": true,
                "condition":
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                    "dataSource":
                    {
                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                        "operationName": "RunFinished",
                        "status": "Failed",
                        "subStatus": "FailedExecution"   
                    }
                },
                "action":
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails": [ "<your alias>@contoso.com" ]
                }
            }
        }
    ]
}
```

特定のエラーについてアラートを受信しないようにする場合は、JSON 定義から **subStatus** を削除できます。

この例では、サブスクリプション内のすべてのデータ ファクトリのアラートを設定しています。 特定のデータ ファクトリのアラートを設定する場合は、**dataSource** ブロックの **resourceUri** にデータ ブロックを指定できます。

```JSON
"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"
```

次の表では、使用できる操作と状態 (および副状態) の一覧を示します。

| 操作の名前 | 状態 | 副状態 |
| --- | --- | --- |
| RunStarted |開始済み |Starting |
| RunFinished |Failed / Succeeded |FailedResourceAllocation<br/><br/>Succeeded<br/><br/>FailedExecution<br/><br/>TimedOut<br/><br/><Canceled<br/><br/>FailedValidation<br/><br/>Abandoned |
| OnDemandClusterCreateStarted |開始済み | |
| OnDemandClusterCreateSuccessful |Succeeded | |
| OnDemandClusterDeleted |Succeeded | |

この例で使用する JSON 要素の詳細については、「[Create Alert Rule (アラート ルールの作成)](https://msdn.microsoft.com/library/azure/dn510366.aspx)」を参照してください。

#### <a name="deploy-the-alert"></a>アラートをデプロイする
アラートをデプロイするには、次の例に示すように、Azure PowerShell コマンドレット **New-AzureRmResourceGroupDeployment** を使用します。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  
```

リソース グループのデプロイが正常に終了すると、次のメッセージが表示されます。

```
VERBOSE: 7:00:48 PM - Template is valid.
WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
Please update scripts to remove this parameter.
VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded

DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

> [!NOTE]
> [アラート ルールの作成](https://msdn.microsoft.com/library/azure/dn510366.aspx) REST API を使用してアラート ルールを作成できます。 JSON ペイロードは JSON の例に似ています。  


#### <a name="retrieve-the-list-of-azure-resource-group-deployments"></a>Azure リソース グループのデプロイ一覧を取得する
デプロイした Azure リソース グループの一覧を取得するには、次の例に示すように、**Get-AzureRmResourceGroupDeployment** コマンドレットを使用します。

```powershell
Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
```

```
DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

#### <a name="troubleshoot-user-events"></a>ユーザー イベントをトラブルシューティングする
1. **[メトリックと操作]** タイルをクリックした後に生成されるすべてのイベントを表示できます。

    ![[メトリックと操作] タイル](./media/data-factory-monitor-manage-pipelines/metrics-and-operations-tile.png)
2. イベントを表示するには、**[イベント]** タイルをクリックします。

    ![Events tile](./media/data-factory-monitor-manage-pipelines/events-tile.png)
3. **[イベント]** ブレードでは、イベントの詳細を表示したり、イベントをフィルター処理したりすることができます。

    ![[イベント] ブレード](./media/data-factory-monitor-manage-pipelines/events-blade.png)
4. 操作の一覧で、エラーの原因である**操作**をクリックします。

    ![操作の選択](./media/data-factory-monitor-manage-pipelines/select-operation.png)
5. エラーの詳細を表示するには、**エラー** イベントをクリックします。

    ![イベントのエラー](./media/data-factory-monitor-manage-pipelines/operation-error-event.png)

アラートの追加、取得、削除に使用できる PowerShell コマンドレットについては、「[Azure Insights Cmdlets (Azure Insights コマンドレット)](https://msdn.microsoft.com/library/mt282452.aspx)」を参照してください。 以下に、 **Get-AlertRule** コマンドレットの使用例をいくつか示します。

```powershell
get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
```

```
Properties :
Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
Condition   :
DataSource :
EventName             :
Category              :
Level                 :
OperationName         : RunFinished
ResourceGroupName     :
ResourceProviderName  :
ResourceId            :
Status                : Failed
SubStatus             : FailedExecution
Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
Condition      :
Description : One or more of the data slices for the Azure Data Factory has failed processing.
Status      : Enabled
Name:       : ADFAlertsSlice
Tags       :
$type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
Location   : West US
Name       : ADFAlertsSlice
```

```powershell
Get-AlertRule -res $resourceGroup
```
```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0

Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
Location   : West US
Name       : FailedExecutionRunsWest3
```

```powershell
Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
```

```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0
```

以下の get-help コマンドを実行すると、Get-AlertRule コマンドレットの詳細および例が示されます。

```powershell
get-help Get-AlertRule -detailed
```

```powershell
get-help Get-AlertRule -examples
```


ポータルのブレードにアラート生成イベントが表示されるのに、電子メール通知を受信していない場合は、指定されている電子メール アドレスが外部の送信者からの電子メールを受信するように設定されているかどうかを確認します。 アラートの電子メールが、電子メールの設定によってブロックされている可能性があります。

### <a name="alerts-on-metrics"></a>メトリックでのアラート
Data Factory では、さまざまなメトリックを収集し、メトリックに対してアラートを作成できます。 データ ファクトリのスライスに対して次のメトリックのアラートを監視および作成できます。

* **失敗した実行**
* **成功した実行**

これらのメトリックは便利であり、データ ファクトリ全体での失敗および成功した実行の概要を取得できます。 メトリックは、スライスが実行するたびに生成されます。 毎時&0; 分に、メトリックは集計されて、ストレージ アカウントにプッシュされます。 メトリックを有効にするには、ストレージ アカウントを設定します。

#### <a name="enable-metrics"></a>メトリックの有効化
メトリックを有効にするには、**[データ ファクトリ]** ブレードから次のようにクリックします。

**[監視]** > **[メトリック]** > **[診断設定]** > **[診断]**

![診断リンク](./media/data-factory-monitor-manage-pipelines/diagnostics-link.png)

**[診断]** ブレードで **[オン]** をクリックし、ストレージ アカウントを選択して **[保存]** をクリックします。

![Diagnostics blade](./media/data-factory-monitor-manage-pipelines/diagnostics-blade.png)

メトリックの集計は&1; 時間ごとに行われるので、メトリックが **[監視]** ブレードに表示されるまでに最大で&1; 時間かかることがあります。

### <a name="set-up-an-alert-on-metrics"></a>メトリックに対するアラートを設定する
**[データ ファクトリ メトリックス]** タイルをクリックします。

![[データ ファクトリ メトリックス] タイル](./media/data-factory-monitor-manage-pipelines/data-factory-metrics-tile.png)

**[メトリック]** ブレードのツール バーで、**[+ アラートの追加]** をクリックします。
![[データ ファクトリ メトリックス] ブレード > アラートの追加](./media/data-factory-monitor-manage-pipelines/add-alert.png)

**[アラート ルールの追加]** ページで次の手順を実行し、**[OK]** をクリックします。

* アラートの名前を入力します (例: "failed alert")。
* アラートの説明を入力します (例: "エラーが発生したときに電子メールを送信する")。
* メトリック ("失敗した実行" 対 "成功した実行") を選択します。
* 条件としきい値を指定します。   
* 期間を指定します。
* 電子メールを所有者、共同作成者、および閲覧者に送信するかどうかを指定します。

![[データ ファクトリ メトリックス] ブレード > アラート ルールの追加](./media/data-factory-monitor-manage-pipelines/add-an-alert-rule.png)

アラート ルールが正常に追加されると、ブレードが閉じられ、**[メトリック]** ブレードに新しいアラートが表示されます。

![[データ ファクトリ メトリックス] ブレード > 追加された新しいアラート](./media/data-factory-monitor-manage-pipelines/failed-alert-in-metric-blade.png)

**[アラート ルール]** タイルに、アラートの数も表示されます。 **[アラート ルール]** タイルをクリックします。

![[データ ファクトリ メトリックス] ブレード - アラート ルール](./media/data-factory-monitor-manage-pipelines/alert-rules-tile-rules.png)

**[アラート ルール]** ブレードに、既存のすべてのアラートが表示されます。 アラートを追加するには、ツール バーの **[アラートの追加]** をクリックします。

![[アラート ルール] ブレード](./media/data-factory-monitor-manage-pipelines/alert-rules-blade.png)

### <a name="alert-notifications"></a>アラート通知
アラート ルールが条件に一致すると、アラートがアクティブ化されたことを通知する電子メールを受け取ります。 問題が解決されてアラート条件が一致しなくなると、アラートが解決されたことを通知する電子メールを受け取ります。

この動作は、アラート ルールに適合するすエラーが発生するたびに通知が送信されるイベントとは異なります。

### <a name="deploy-alerts-by-using-powershell"></a>PowerShell を使用したアラートをデプロイする
イベントの場合と同じ方法で、メトリックのアラートをデプロイできます。

**アラートの定義**

```JSON
{
    "contentVersion" : "1.0.0.0",
    "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters" : {},
    "resources" : [
    {
            "name" : "FailedRunsGreaterThan5",
            "type" : "microsoft.insights/alertrules",
            "apiVersion" : "2014-04-01",
            "location" : "East US",
            "properties" : {
                "name" : "FailedRunsGreaterThan5",
                "description" : "Failed Runs greater than 5",
                "isEnabled" : true,
                "condition" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
                    "dataSource" : {
                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
                        "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
                        "metricName" : "FailedRuns"
                    },
                    "threshold" : 5.0,
                    "windowSize" : "PT3H",
                    "timeAggregation" : "Total"
                },
                "action" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails" : ["abhinav.gpt@live.com"]
                }
            }
        }
    ]
}
```

サンプルの *subscriptionId*、*resourceGroupName*、*dataFactoryName* を、適切な値に置き換えます。

*metricName* が現在サポートしている値は次の&2; つです。

* FailedRuns
* SuccessfulRuns

**アラートをデプロイする**

アラートをデプロイするには、次の例に示すように、Azure PowerShell コマンドレット **New-AzureRmResourceGroupDeployment** を使用します。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json
```

デプロイが成功すると、次のメッセージが表示されます。

```
VERBOSE: 12:52:47 PM - Template is valid.
VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded


DeploymentName    : FailedRunsGreaterThan5
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 7/27/2015 7:52:56 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           
```

**Add-AlertRule** コマンドレットを使用して、アラート ルールをデプロイすることもできます。 詳細と例については、「[Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx)」のトピックを参照してください。  

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>データ ファクトリを別のリソース グループまたはサブスクリプションに移動する
データ ファクトリを別のリソース グループまたはサブスクリプションに移動するには、データ ファクトリのホーム ページの **[移動]** コマンド バー ボタンを使用します。

![データ ファクトリの移動](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

また、関連するリソース (データ ファクトリに関連付けられたアラートなど) もデータ ファクトリと併せて移動することができます。

![[リソースの移動] ダイアログ ボックス](./media/data-factory-monitor-manage-pipelines/MoveResources.png)

