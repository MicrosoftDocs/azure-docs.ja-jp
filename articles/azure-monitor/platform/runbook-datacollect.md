---
title: Azure Automation で Runbook を使用して Log Analytics データを収集する | Microsoft Docs
description: Log Analytics による分析用のデータをリポジトリに収集するための Runbook を Azure Automation で作成する詳細な手順について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 67378a5911e5bd83888342aa3773f7f5ed4ccf29
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58102586"
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Azure Automation の Runbook を使用して Log Analytics でデータを収集する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

エージェントの[データ ソース](../../azure-monitor/platform/agent-data-sources.md)や [Azure から収集されたデータ](../../azure-monitor/platform/collect-azure-metrics-logs.md)など、Log Analytics ではさまざまなソースから大量のデータを収集できます。 しかし、これらの標準的なソースではアクセスできないデータの収集が必要なシナリオがあります。 このような場合は、[HTTP データ コレクター API](../../azure-monitor/platform/data-collector-api.md) を使って、REST API クライアントから Log Analytics にデータを書き込むことができます。 このデータ収集を実行する一般的な方法は、Azure Automation で Runbook を使うものです。

この記事では、Log Analytics にデータを書き込むために Azure Automation で Runbook を作成してスケジュールを設定する手順について説明します。

## <a name="prerequisites"></a>前提条件
このシナリオでは、Azure サブスクリプションに次のリソースが構成されている必要があります。 どちらも無料アカウントでかまいません。

- [Log Analytics ワークスペース](../../azure-monitor/learn/quick-create-workspace.md)。
- [Azure Automation アカウント](../..//automation/automation-quickstart-create-account.md)。

## <a name="overview-of-scenario"></a>シナリオの概要
このチュートリアルでは、Automation のジョブに関する情報を収集する Runbook を作成します。 Azure Automation の Runbook は PowerShell を使って実装されるので、最初に Azure Automation エディターでスクリプトを作成してテストします。 必要な情報が収集されることを確認した後、Log Analytics にそのデータを書き込み、カスタム データ型を検証します。 最後に、定期的に Runbook を起動するスケジュールを作成します。

> [!NOTE]
> この Runbook を使わずに Log Analytics にジョブ情報を送信するように Azure Automation を構成することができます。 このシナリオは主としてチュートリアルをサポートするために使われ、テスト ワークスペースにデータを送信することをお勧めします。

## <a name="1-install-data-collector-api-module"></a>1.データ コレクター API モジュールをインストールする
[HTTP データ コレクター API からのすべての要求](../../azure-monitor/platform/data-collector-api.md#create-a-request)を適切な形式に設定し、Authorization ヘッダーを含める必要があります。 Runbook でこれを行うこともできますが、このプロセスを簡略化するモジュールを使うことで必要なコードの量を減らすことができます。 使うことができるモジュールの 1 つは、PowerShell ギャラリーの [OMSIngestionAPI モジュール](https://www.powershellgallery.com/packages/OMSIngestionAPI)です。

[モジュール](../../automation/automation-integration-modules.md) を Runbook で使うには、Automation アカウントにモジュールをインストールする必要があります。  同じアカウント内のすべての Runbook で、モジュールの関数を使うことができます。 Automation アカウントで **[資産]** > **[モジュール]** > **[モジュールの追加]** の順に選んで、新しいモジュールをインストールできます。

ただし、PowerShell ギャラリーには Automation アカウントに直接モジュールをデプロイできる簡単なオプションがあるので、このチュートリアルではそのオプションを使ってもかまいません。

![OMSIngestionAPI モジュール](media/runbook-datacollect/OMSIngestionAPI.png)

1. [PowerShell ギャラリー](https://www.powershellgallery.com/)に移動します。
2. **OMSIngestionAPI** を探します。
3. **[Deploy to Azure Automation]\(Azure Automation にデプロイする\)** ボタンをクリックします。
4. Automation アカウントを選び、**[OK]** をクリックしてモジュールをインストールします。

## <a name="2-create-automation-variables"></a>2.Automation 変数を作成する
[Automation 変数](../../automation/automation-variables.md)は、Automation アカウントのすべての Runbook で使うことができる値を保持します。 実際の Runbook を編集することなくこれらの値を変更できるので、Runbook の柔軟性が高くなります。 HTTP データ コレクター API からのすべての要求では、Log Analytics ワークスペースの ID とキーが必要であり、この情報を格納するには変数資産が最適です。

![variables](media/runbook-datacollect/variables.png)

1. Azure Portal で、Automation アカウントに移動します。
2. **[共有リソース]** の **[変数]** を選びます。
2. **[変数の追加]** をクリックして、次の表の 2 つの変数を作成します。

| プロパティ | ワークスペース ID の値 | ワークスペース キーの値 |
|:--|:--|:--|
| Name | WorkspaceId | WorkspaceKey |
| type | String | String |
| 値 | Log Analytics ワークスペースのワークスペース ID を貼り付けます。 | Log Analytics ワークスペースのプライマリ キーまたはセカンダリ キーを貼り付けます。 |
| 暗号化 | いいえ  | はい |

## <a name="3-create-runbook"></a>手順 3.Runbook を作成する

ポータルにある Azure Automation のエディターを使って、Runbook を編集およびテストできます。 スクリプト エディターを使って [PowerShell を直接](../../automation/automation-edit-textual-runbook.md)操作したり、[グラフィカルな Runbook を作成](../../automation/automation-graphical-authoring-intro.md)したりすることもできます。 このチュートリアルでは、PowerShell スクリプトを使います。

![Runbook を編集する](media/runbook-datacollect/edit-runbook.png)

1. Automation アカウントに移動します。
2. **[Runbook]** > **[Runbook の追加]** > **[新しい Runbook の作成]** の順にクリックします。
3. Runbook 名に「**Collect-Automation-jobs**」と入力します。 Runbook の種類として、**[PowerShell]** を選びます。
4. **[作成]** をクリックして Runbook を作成し、エディターを開始します。
5. 以下のコードをコピーして、Runbook に貼り付けます。 コードの説明については、スクリプト内のコメントをご覧ください。
    ```
    # Get information required for the automation account from parameter values when the runbook is started.
    Param
    (
        [Parameter(Mandatory = $True)]
        [string]$resourceGroupName,
        [Parameter(Mandatory = $True)]
        [string]$automationAccountName
    )
    
    # Authenticate to the Automation account using the Azure connection created when the Automation account was created.
    # Code copied from the runbook AzureAutomationTutorial.
    $connectionName = "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName
    Connect-AzAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    
    # Set the $VerbosePreference variable so that we get verbose output in test environment.
    $VerbosePreference = "Continue"
    
    # Get information required for Log Analytics workspace from Automation variables.
    $customerId = Get-AutomationVariable -Name 'WorkspaceID'
    $sharedKey = Get-AutomationVariable -Name 'WorkspaceKey'
    
    # Set the name of the record type.
    $logType = "AutomationJob"
    
    # Get the jobs from the past hour.
    $jobs = Get-AzAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
    
    if ($jobs -ne $null) {
        # Convert the job data to json
        $body = $jobs | ConvertTo-Json
    
        # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
        Write-Verbose $body
    
        # Send the data to Log Analytics.
        Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
    }
    ```

## <a name="4-test-runbook"></a>4.Runbook をテストする
Azure Automation には、発行する前に [Runbook をテストする](../../automation/automation-testing-runbook.md)ための環境が含まれています。 運用環境に発行する前に、Runbook によって収集されたデータを検査し、意図したとおりに Log Analytics に書き込まれることを確認できます。

![Runbook をテストする](media/runbook-datacollect/test-runbook.png)

1. **[保存]** をクリックして Runbook を保存します。
1. **[テスト ウィンドウ]** をクリックして、テスト環境で Runbook を開きます。
1. Runbook にはパラメーターがあるので、それらの値を入力するように求められます。 ジョブ情報を収集するリソース グループの名前と Automation アカウントを入力します。
1. **[開始]** をクリックして Runbook を開始します。
1. Runbook は **[キューに登録済み]** の状態で開始した後、**[実行中]** に移行します。
1. Runbook は、Json 形式で収集されたジョブの詳細出力を表示します。 ジョブが表示されない場合は、過去 1 時間の間に Automation アカウントでジョブが作成されていない可能性があります。 Automation アカウントで Runbook を開始してみた後、テストをもう一度実行します。
1. Log Analytics への post コマンドでエラーが何も表示されないことを出力で確認します。 次のようなメッセージが表示されます。

    ![Post の出力](media/runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5.Log Analytics でレコードを確認する
Runbook のテストが完了し、出力を正常に受信したことを確認した後、[Log Analytics でのログ検索](../../azure-monitor/log-query/log-query-overview.md)を使って、レコードが作成されたことを確認できます。

![ログの出力](media/runbook-datacollect/log-output.png)

1. Azure Portal で、Log Analytics ワークスペースを選びます。
2. **[ログ検索]** をクリックします。
3. 「`Type=AutomationJob_CL`」というコマンドを入力し、検索ボタンをクリックします。 レコードの種類に、スクリプトでは指定されていない _CL が含まれることに注意してください。 このサフィックスは、カスタム ログの種類であることを示すためにログの種類に自動的に追加されます。
4. Runbook が正しく動作していることを示す 1 つまたは複数のレコードが返されます。

## <a name="6-publish-the-runbook"></a>6.Runbook を発行する
Runbook が正しく動作していることを確認した後、運用環境で実行できるように発行する必要があります。 発行されたバージョンを変更することなく、Runbook の編集とテストを続けることができます。

![Runbook の発行](media/runbook-datacollect/publish-runbook.png)

1. Automation アカウントに戻ります。
2. **[Runbook]** をクリックし、**Collect-Automation-jobs** を選びます。
3. **[編集]**、**[発行]** の順にクリックします。
4. 以前に発行したバージョンの上書きの確認を求められたら、**はい** をクリックします。

## <a name="7-set-logging-options"></a>7.ログ オプションを設定する
テストでは、スクリプトで $VerbosePreference 変数を設定してあったため、[詳細な出力](../../automation/automation-runbook-output-and-messages.md#message-streams)を見ることができました。 運用環境で詳細な出力を表示する場合は、Runbook のログ プロパティを設定する必要があります。 このチュートリアルで使われている Runbook では、Log Analytics に送信されている Json データが表示されます。

![ログとトレース](media/runbook-datacollect/logging.png)

1. Runbook のプロパティで、**[Runbook の設定]** の **[ログとトレース]** を選びます。
2. **[詳細レコードの記録]** の設定を **[オン]** に変更します。
3. **[Save]** をクリックします。

## <a name="8-schedule-runbook"></a>8.Runbook のスケジュールを設定する
監視データを収集する Runbook を開始する最も一般的な方法は、自動的に実行するようにスケジュールを設定することです。 そのためには、[Azure Automation でスケジュール](../../automation/automation-schedules.md)を作成し、Runbook に関連付けます。

![Runbook のスケジュールを設定する](media/runbook-datacollect/schedule-runbook.png)

1. Runbook のプロパティで、**[リソース]** の **[スケジュール]** を選びます。
2. **[スケジュールの追加]** > **[スケジュールを Runbook にリンクします]** > **[新しいスケジュールを作成します]** の順にクリックします。
5. スケジュールに次の値を入力し、**[作成]** をクリックします。

| プロパティ | 値 |
|:--|:--|
| Name | AutomationJobs-Hourly |
| 開始 | 現在時刻より 5 分以上後の任意の時刻を選びます。 |
| 繰り返し | 繰り返し |
| 繰り返しの間隔 | 1 時間 |
| 有効期限の設定 | いいえ  |

スケジュールを作成した後、このスケジュールが Runbook を開始するたびに使われるパラメーター値を設定する必要があります。

1. **[パラメータと実行設定を構成する]** をクリックします。
1. **[ResourceGroupName]** と **[AutomationAccountName]** に値を入力します。
1. Click **OK**.

## <a name="9-verify-runbook-starts-on-schedule"></a>9.スケジュールに従って Runbook が開始することを確認する
Runbook が開始されるたびに、[ジョブが作成](../../automation/automation-runbook-execution.md)されて、出力がログに記録されます。 実際、これらは Runbook が収集している同じジョブです。 スケジュールの開始時刻が経過した後、Runbook のジョブをチェックして、Runbook が意図したように開始していることを確認できます。

![[ジョブ]](media/runbook-datacollect/jobs.png)

1. Runbook のプロパティで、**[リソース]** の **[ジョブ]** を選びます。
2. Runbook が開始されるたびに作成されたジョブの一覧が表示されます。
3. ジョブの 1 つをクリックすると詳細が表示されます。
4. Runbook からのログと出力を表示するには、**[すべてのログ]** をクリックします。
5. 一番下までスクロールし、次の図のようなエントリを探します。<br>![詳細](media/runbook-datacollect/verbose.png)
6. このエントリをクリックすると、Log Analytics に送信された詳細な Json データが表示されます。

## <a name="next-steps"></a>次の手順
- [ビュー デザイナー](../../azure-monitor/platform/view-designer.md)を使って、Log Analytics リポジトリに収集したデータを表示するビューを作成します。
- Runbook を[管理ソリューション](../../azure-monitor/insights/solutions-creating.md)にパッケージ化し、ユーザーに配布します。
- [Log Analytics](https://docs.microsoft.com/azure/log-analytics/) についてさらに学習します。
- [Azure Automation](https://docs.microsoft.com/azure/automation/) についてさらに学習します。
- [HTTP データ コレクター API](../../azure-monitor/platform/data-collector-api.md) についてさらに学習します。
