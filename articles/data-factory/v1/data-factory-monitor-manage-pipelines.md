---
title: Azure portal および PowerShell を使用したパイプラインの監視と管理
description: Azure Portal および Azure PowerShell を使用して、作成した Azure Data Factory とパイプラインを監視および管理する方法について説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 0744a7d915d6bee868b160abc29964d58947dd28
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779632"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Azure Portal および PowerShell を使用した Azure Data Factory パイプラインの監視と管理
> [!div class="op_single_selector"]
> * [Azure Portal の使用/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [監視と管理アプリの使用](data-factory-monitor-manage-app.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[Data Factory パイプラインの監視と管理](../monitor-visually.md)に関するページを参照してください。

この記事では、Azure Portal と PowerShell を使用してパイプラインを監視、管理、デバッグする方法について説明します。

> [!IMPORTANT]
> 監視と管理アプリケーションによって、ご使用のデータ パイプラインの管理および管理や、問題のトラブルシューティングがより適切にサポートされます。 このアプリケーションの使用法の詳細については、[新しい監視と管理アプリを使用した Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md)に関する記事を参照してください。 

> [!IMPORTANT]
> Azure Data Factory バージョン 1 は、新しい [Azure Monitor アラート インフラストラクチャ](../../azure-monitor/alerts/alerts-metric.md) を使用するようになりました。 以前のアラート インフラストラクチャは非推奨です。 その結果、バージョン 1 データ ファクトリ用に構成された既存のアラートは機能しなくなります。 v1 データ ファクトリの既存のアラートは自動的に移行されません。 これらのアラートは、新しいアラート インフラストラクチャ上に再作成する必要があります。 Azure Portal にログインして **[モニター]** を選択し、バージョン 1 のデータ ファクトリ用にメトリックに対する新しいアラートを作成します (失敗した実行、成功した実行など)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>パイプラインとアクティビティの状態の理解
Azure Portal を使用すると、次の操作を行うことができます。

* 図としてデータ ファクトリを表示する。
* パイプライン内のアクティビティを表示する。
* 入力データセットと出力データセットを参照する。

このセクションでは、データセット スライスの状態がどのように移行するかについても説明します。   

### <a name="navigate-to-your-data-factory"></a>Data Factory に移動する
1. [Azure portal](https://portal.azure.com) にサインインします。
2. 左側のメニューで、**[データ ファクトリ]** をクリックします。 表示されない場合は、**[その他のサービス >]** をクリックし、**[インテリジェンス + 分析]** カテゴリの下にある **[データ ファクトリ]** をクリックします。

   ![[すべて参照] > [データ ファクトリ]](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. **[データ ファクトリ]** ブレードで、目的のデータ ファクトリを選択します。

    ![データ ファクトリの選択](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   データ ファクトリのホーム ページが表示されます。

   ![Data Factory ブレード](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Data Factory のダイアグラム ビュー
データ ファクトリの **ダイアグラム** ビューでは、データ ファクトリとその資産を監視および管理する 1 つのウィンドウが提供されます。 データ ファクトリの **ダイアグラム** ビューを表示するには、データ ファクトリのホーム ページで **[ダイアグラム]** をクリックします。

![[ダイアグラム] ビュー](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

ダイアグラムの拡大または縮小、画面に合わせたサイズ変更、100% 表示、レイアウトのロックを行うことができるほか、パイプラインとデータセットを自動で配置することができます。 また、データ系列の情報を確認することもできます (つまり、選択したアイテムのアップストリーム アイテムとダウンストリーム アイテムが表示されます)。

### <a name="activities-inside-a-pipeline"></a>パイプライン内のアクティビティ
1. パイプラインを右クリックして **[パイプラインを開く]** をクリックすると、パイプライン内のすべてのアクティビティとアクティビティの入力データセットおよび出力データセットが表示されます。 この機能は、パイプラインに複数のアクティビティが含まれる場合に、1 つのパイプラインの動作系列を理解するときに便利です。

    ![パイプラインを開くメニュー](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. 次の例では、パイプラインのコピー アクティビティ、入力、出力を確認できます。 

    ![パイプライン内のアクティビティ](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. 左上隅の階層リンクの **[データ ファクトリ]** リンクをクリックして、データ ファクトリのホーム ページに戻ることができます。

    ![Data Factory に戻る](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>パイプライン内の各アクティビティの状態を表示する
アクティビティによって生成されるデータセットのステータスを表示することにより、アクティビティの現在の状態を確認できます。

**ダイアグラム** の **OutputBlobTable** をダブルクリックすると、パイプライン内のさまざまなアクティビティの実行によって生成されたすべてのスライスが表示されます。 コピー アクティビティは過去 8 時間正常に実行され、**Ready** 状態のスライスを生成したことがわかります。  

![パイプラインの状態](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

データ ファクトリのデータセット スライスは、次のいずれかの状態になります。

<table>
<tr>
    <th align="left">State</th><th align="left">下位状態</th><th align="left">説明</th>
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
<td>[再試行]</td><td>アクティビティの実行が再試行されています。</td>
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
<td rowspan="4">失敗</td><td>TimedOut</td><td>アクティビティで許可されている実行時間を超過しました。</td>
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
<td>スキップ</td><td>なし</td><td>スライスは処理中ではありません。</td>
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
データ ファクトリをデプロイした後、パイプラインに有効なアクティブ期間があると、データセット スライスは 1 つの状態から別の状態に遷移します。 現在、スライスのステータスは、次の状態ダイアグラムに従います。

![状態ダイアグラム](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

データ ファクトリのデータセット状態遷移フローは、Waiting -> In-Progress/In-Progress (Validating) -> Ready/Failed となります。

スライスは **Waiting** 状態で始まり、前提条件が満たされるまで待機してから実行されます。 アクティビティの実行が始まると、スライスは **In-Progress** 状態になります。 アクティビティの実行結果は成功か失敗のどちらかになります。 実行結果に応じて、スライスは **Ready** または **Failed** とマークされます。

**Ready** または **Failed** 状態から **Waiting** 状態にスライスをリセットできます。 また、スライスの状態を **Skip** としてマークでき、この状態ではアクティビティは実行されず、スライスは処理されません。

## <a name="pause-and-resume-pipelines"></a>パイプラインを一時停止および再開する
Azure PowerShell を使用してパイプラインを管理できます。 たとえば、Azure PowerShell コマンドレットを実行してパイプラインを一時停止および再開できます。 

> [!NOTE] 
> ダイアグラム ビューは、パイプラインの一時停止と再開をサポートしていません。 ユーザー インターフェイスを使用する場合は、監視と管理アプリケーションを使用してください。 このアプリケーションの使用法の詳細については、[新しい監視と管理アプリを使用した Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md)に関する記事を参照してください。 

**Suspend-AzDataFactoryPipeline** PowerShell コマンドレットを使用して、パイプラインを一時停止/中断できます。 このコマンドレットは、問題が修正されるまでパイプラインを実行しない場合に便利です。 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
次に例を示します。

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

パイプラインの問題が解決されたら、次の PowerShell コマンドを実行して、一時停止されているパイプラインを再開できます。

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
次に例を示します。

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>パイプラインをデバッグする
Azure Data Factory では、パイプラインをデバッグおよびトラブルシューティングするための充実した機能が Azure Portal および Azure PowerShell で提供されています。

> [!NOTE] 
> 監視と管理アプリを使用してエラーをトラブルシューティングする方がはるかに簡単です。 このアプリケーションの使用法の詳細については、[新しい監視と管理アプリを使用した Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md)に関する記事を参照してください。 

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
1. **PowerShell** を起動します。
2. **Get-AzDataFactorySlice** コマンドを実行してスライスとその状態を確認します。 [状態] が **[Failed]** になっているスライスが表示されます。        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   次に例を示します。

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   **StartDateTime** を、ご使用のパイプラインの開始時刻で置き換えます。 
3. **Get-AzDataFactoryRun** コマンドレットを実行して、スライスのアクティビティの実行について詳細を取得します。

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    次に例を示します。

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
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
5. 出力結果の ID 値を使用して **Save-AzDataFactoryLog** コマンドレットを実行し、このコマンドレットの **-DownloadLogs** オプションを使用してログ ファイルをダウンロードできます。

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>パイプラインのエラーを再実行する

> [!IMPORTANT]
> 監視と管理アプリを使用してエラーのトラブルシューティングを行い、失敗したスライスを返す方が簡単です。 このアプリケーションの使用法の詳細については、[新しい監視と管理アプリを使用した Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md)に関する記事を参照してください。 

### <a name="use-the-azure-portal"></a>Azure ポータルの使用
パイプラインのエラーをトラブルシューティングおよびデバッグした後は、エラー スライスに移動してコマンド バーの **[実行]** ボタンをクリックすることで、エラーを再実行できます。

![障害が発生したスライスの再実行](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

ポリシー エラー (たとえば、データが使用不可能な場合) のためにスライスの検証が失敗した場合は、エラーを修正し、コマンド バーの **[検証]** ボタンをクリックすることによって再度検証できます。

![エラーの修正と検証](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Azure PowerShell の使用
**Set-AzDataFactorySliceStatus** コマンドレットを使用してエラーを再実行できます。 このコマンドレットの構文やその他の詳細については、「[Set-AzDataFactorySliceStatus](/powershell/module/az.datafactory/set-azdatafactoryslicestatus)」のトピックをご覧ください。

**例:**

次の例では、Azure Data Factory "WikiADF" のテーブル "DAWikiAggregatedData" のすべてのスライスの状態を 'Waiting' に設定します。

"UpdateType" は "UpstreamInPipeline" に設定されています。これにより、テーブルとすべての依存 (アップストリーム) テーブルの各スライスの状態が "Waiting" に設定されます。 このパラメーターに指定できる他の値は、"Individual" です。

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Azure Portal でアラートを作成する

1.  Azure Portal にログインし、**[モニター]、[アラート]** の順に選択して [アラート] ページを開きます。

    ![[アラート] ページを開きます。](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  **[+ 新しいアラート ルール]** を選択して新しいアラートを作成します。

    ![新しいアラートを作成する](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  **[Alert condition]\(アラートの条件)** を定義します。 ( **[リソースの種類でフィルター]** では **[データ ファクトリ]** を選択するようにしてください)。 **[ディメンション]** の値を指定することもできます。

    ![アラートの条件を定義する - ターゲットを選択する](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![アラートの条件を定義する - アラート条件を追加する](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![アラートの条件を定義する - アラート ロジックを追加する](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  **[アラートの詳細]** を定義します。

    ![アラートの詳細を定義する](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  **[Action group] (アクション グループ)** を定義します。

    ![アクション グループを定義する - 新しいアクション グループを作成する](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![アクション グループを定義する - プロパティを設定する](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![アクション グループを定義する - 作成された新しいアクション グループ](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>データ ファクトリを別のリソース グループまたはサブスクリプションに移動する
データ ファクトリを別のリソース グループまたはサブスクリプションに移動するには、データ ファクトリのホーム ページの **[移動]** コマンド バー ボタンを使用します。

![データ ファクトリの移動](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

また、関連するリソース (データ ファクトリに関連付けられたアラートなど) もデータ ファクトリと併せて移動することができます。

![[リソースの移動] ダイアログ ボックス](./media/data-factory-monitor-manage-pipelines/MoveResources.png)