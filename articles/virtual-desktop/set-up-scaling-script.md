---
title: Azure Automation によるセッション ホストのスケーリング - Azure
description: Azure Automation を使用して Windows Virtual Desktop のセッション ホストを自動的にスケーリングする方法。
author: Heidilohr
ms.topic: how-to
ms.date: 03/09/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: f60341ea51f1cf4e856b1b4598887da3dc37ebb2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102613121"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Azure Automation を使用してセッション ホストをスケーリングする

仮想マシン (VM) をスケーリングすると、Windows Virtual Desktop の総デプロイ コストを削減できます。 これは、ピーク時以外の使用時間帯にセッション ホスト VM をシャットダウンして割り当て解除し、ピーク時間帯に再びオンにして再割り当てすることを意味します。

この記事では、Azure Automation アカウントで構築されたスケーリング ツールと、Windows Virtual Desktop 環境でセッション ホスト VM を自動的にスケーリングする Azure Logic Appsについて説明します。 スケーリング ツールの使用方法を確認するには、「[前提条件](#prerequisites)」に進んでください。

## <a name="how-the-scaling-tool-works"></a>スケーリング ツールのしくみ

スケーリング ツールでは、セッション ホスト VM のコストを最適化したいお客様のために低コストの自動化オプションを提供しています。

スケーリング ツールを使用すると、次の操作を行えます。

- ピーク時とピーク時以外の営業時間に基づいて、VM の起動と停止をスケジュールします。
- CPU コアあたりのセッション数に基づいて VM をスケールアウトします。
- ピーク時以外の時間帯に VM をスケールインして、最低限の数のセッション ホスト VM だけを実行状態のままにします。

スケーリング ツールは、Azure Automation アカウント、PowerShell Runbook、Webhook、Azure Logic Appsを組み合わせて使用することで機能します。 ツールが実行されると、Azure Logic Appsによって Webhook が呼び出されて Azure Automation Runbook が起動します。 その後、Runbook によってジョブが作成されます。

ピーク時の使用時間帯は、このジョブによって現在のセッション数と現在実行中のセッション ホストの VM 容量が、ホスト プールごとにチェックされます。 この情報を使用して、実行中のセッション ホスト VM が既存のセッションをサポートできるかどうかが、**CreateOrUpdateAzLogicApp.ps1** ファイルに定義された *SessionThresholdPerCPU* パラメーターに基づいて計算されます。 セッション ホスト VM が既存のセッションをサポートできない場合は、このジョブによってホスト プール内の追加のセッション ホスト VM が起動されます。

>[!NOTE]
>*SessionThresholdPerCPU* では、VM 上のセッション数は制限されません。 このパラメーターは、接続を負荷分散するために、どのタイミングで新しい VM を起動する必要があるかを決めるだけのものです。 セッションの数を制限するには、「[Update-AzWvdHostPool](configure-host-pool-load-balancing.md#configure-breadth-first-load-balancing)」の手順に従って、*MaxSessionLimit* パラメーターを適切に構成する必要があります。

ピーク時以外の使用時間中に、*MinimumNumberOfRDSH* パラメーターに基づいて、シャットダウンすべきセッション ホスト VM の数がジョブによって決定されます。 *LimitSecondsToForceLogOffUser* パラメーターを 0 以外の正の値に設定した場合、ジョブによってセッション ホスト VM はドレイン モードに設定され、新しいセッションがホストに接続できないようになります。 その後、ジョブによって、現在サインインしているすべてのユーザーは作業内容を保存するよう通知され、構成された時間待機した後、ユーザーは強制的にサインアウトされます。セッション ホスト VM 上のすべてのユーザー セッションがサインアウトされると、ジョブによって VM がシャットダウンされます。 VM がシャットダウンされると、ジョブによってセッション ホストのドレイン モードがリセットされます。

>[!NOTE]
>セッション ホスト VM を手動でドレイン モードに設定した場合、セッション ホスト VM はジョブでは管理されません。 セッション ホスト VM が実行中で、ドレイン モードに設定されている場合は、使用不可として扱われます。これにより、ジョブで負荷を処理するために追加の VM が起動されます。 Azure VM を手動でドレイン モードに設定する場合は、事前にタグを付けるようお勧めします。 後で Azure Logic Apps スケジューラを作成するときに、*MaintenanceTagName* パラメーターを使用してタグに名前を付けることができます。 タグを使用すると、こうした VM をスケーリング ツールが管理する VM と区別することができます。 メンテナンス タグを設定すると、タグを削除するまで、スケーリング ツールでその VM に変更を加えることもできなくなります。

*LimitSecondsToForceLogOffUser* パラメーターを 0 に設定した場合は、指定されたグループ ポリシー内のセッション構成設定で、ユーザー セッションのサインオフを処理することがジョブによって許可されます。 これらのグループ ポリシーを確認するには、 **[コンピューターの構成]**  >  **[ポリシー]**  >  **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[リモート デスクトップ サービス]**  >  **[リモート デスクトップ セッション ホスト]**  >  **[セッションの時間制限]** にアクセスしてください。 セッション ホスト VM にアクティブなセッションが存在する場合は、ジョブによってセッション ホスト VM は実行状態のままとなります。 アクティブなセッションが存在しない場合は、ジョブによってセッション ホスト VM はシャットダウンされます。

常に、ジョブではホスト プールの *MaxSessionLimit* を考慮に入れて、現在のセッション数が最大容量の 90% を超えているかの判断も行われます。 超えている場合、ジョブによって追加のセッション ホスト VM が起動されます。

ジョブは、設定された繰り返し間隔に基づいて定期的に実行されます。 Windows Virtual Desktop 環境のサイズに基づいてこの間隔を変更できますが、VM の起動とシャットダウンには時間がかかることがあるため、延期期間を考慮してください。 繰り返し間隔を 15 分に設定することをお勧めします。

ただし、このツールには次の制限事項もあります。

- このソリューションは、プールされたマルチセッションのセッション ホスト VM にのみ適用されます。
- このソリューションでは任意のリージョンの VM が管理されますが、Azure Automation アカウントおよび Azure Logic Appsと同じサブスクリプションでのみ使用できます。
- Runbook 内のジョブの最長実行時間は、3 時間です。 ホスト プール内の VM の起動または停止に時間がかかる場合、ジョブは失敗します。 詳細については、「[共有リソース](../automation/automation-runbook-execution.md#fair-share)」を参照してください。
- スケーリング アルゴリズムが適切に機能するためには、少なくとも 1 つの VM またはセッション ホストをオンにする必要があります。
- スケーリング ツールでは、CPU またはメモリに基づくスケーリングはサポートされていません。
- スケーリングは、ホスト プール内の既存のホストでのみ機能します。 スケーリング ツールでは、新しいセッション ホストのスケーリングはサポートされていません。

>[!NOTE]
>スケーリング ツールにより、現在スケーリングされているホスト プールの負荷分散モードが制御されます。 このツールでは、ピーク時とピーク時以外の両方に対して、幅優先の負荷分散モードが使用されます。

## <a name="prerequisites"></a>前提条件

スケーリング ツールの設定を開始する前に、次の準備ができていることを確認してください。

- [Windows Virtual Desktop のホスト プール](create-host-pools-azure-marketplace.md)
- 構成されて Windows Virtual Desktop サービスに登録されたセッション ホスト プール VM
- Azure サブスクリプション対する[共同作成者のアクセス権](../role-based-access-control/role-assignments-portal.md)を持っているユーザー

ツールのデプロイに使用するマシンには、次のものが必要です。

- Windows PowerShell 5.1 以降
- Microsoft Az PowerShell モジュール

すべての準備が整ったら、始めましょう。

## <a name="create-or-update-an-azure-automation-account"></a>Azure Automation アカウントを作成または更新する

>[!NOTE]
>以前のバージョンのスケーリング スクリプトを実行している Runbook を持つ Azure Automation アカウントが既にある場合は、以下の手順に従って更新されていることを確認するだけで済みます。

まず、PowerShell Runbook を実行するために、Azure Automation アカウントが必要です。 このセクションで説明するプロセスは、PowerShell Runbook の設定に使用するつもりの既存の Azure Automation アカウントがある場合でも有効です。 以下に、設定の方法を説明します。

1. Windows PowerShell を開きます。

2. 次のコマンドレットを実行して、Azure アカウントにサインインします。

    ```powershell
    Login-AzAccount
    ```

    >[!NOTE]
    >アカウントには、スケーリング ツールをデプロイする Azure サブスクリプションに対する共同作成者の権限が必要です。

3. 次のコマンドレットを実行して、Azure Automation アカウントを作成するためのスクリプトをダウンロードします。

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzAutoAccount.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzAutoAccount.ps1"
    ```

4. 次のコマンドレットを実行してスクリプトを実行し、Azure Automation アカウントを作成します。 パラメーターの値を入力するか、既定値を使用するようにコメントを付けることができます。

    ```powershell
    $Params = @{
         "AADTenantId"           = "<Azure_Active_Directory_tenant_ID>"   # Optional. If not specified, it will use the current Azure context
         "SubscriptionId"        = "<Azure_subscription_ID>"              # Optional. If not specified, it will use the current Azure context
         "UseARMAPI"             = $true
         "ResourceGroupName"     = "<Resource_group_name>"                # Optional. Default: "WVDAutoScaleResourceGroup"
         "AutomationAccountName" = "<Automation_account_name>"            # Optional. Default: "WVDAutoScaleAutomationAccount"
         "Location"              = "<Azure_region_for_deployment>"
         "WorkspaceName"         = "<Log_analytics_workspace_name>"       # Optional. If specified, Log Analytics will be used to configure the custom log table that the runbook PowerShell script can send logs to
    }

    .\CreateOrUpdateAzAutoAccount.ps1 @Params
    ```

5. コマンドレットの出力には、Webhook の URI が含まれています。 Azure Logic Appsの実行スケジュールを設定するときにパラメーターとして使用するため、必ずこの URI を記録しておいてください。

6. Log Analytics に **WorkspaceName** パラメーターを指定した場合、コマンドレットの出力には、Log Analytics ワークスペース ID とその主キーも含まれます。 後で Azure Logic Appsの実行スケジュールを設定するときにパラメーターとしてまた使用する必要があるため、必ずこの URI を覚えておいてください。

7. Azure Automation アカウントの設定が完了したら、Azure サブスクリプションにサインインし、次の図に示すように、指定のリソースグループに Azure Automation アカウントと関連する Runbook が表示されていることを確認します。

    >[!div class="mx-imgBorder"]
    >![新しく作成した Azure Automation アカウントと Runbook を示す Azure の概要ページの画像。](media/automation-account.png)

    Webhook が必要な場所にあるかどうかを確認するには、Runbook の名前を選択します。 次に、Runbook の Resources セクションに移動して、 **[Webhooks]** を選択します。

## <a name="create-an-azure-automation-run-as-account"></a>Azure Automation の実行アカウントを作成する

これで Azure Automation アカウントの用意ができたので、Azure Automation の実行アカウントがまだない場合はこれも作成する必要があります。 このアカウントを使用すると、ツールから Azure リソースにアクセスできるようになります。

[Azure Automation の実行アカウント](../automation/manage-runas-account.md)により、Azure コマンドレットを使用して Azure のリソースを管理するための認証が提供されます。 実行アカウントを作成すると、新しいサービス プリンシパル ユーザーが Azure Active Directory に作成され、サブスクリプション レベルでこのサービス プリンシパル ユーザーに共同作成者ロールが割り当てられます。 Azure 実行アカウントは、資格情報オブジェクトにユーザー名とパスワードを保存しなくても、証明書とサービス プリンシパル名を使用して安全に認証するための優れた方法です。 実行アカウントの認証の詳細については、「[実行アカウントのアクセス許可の制限](../automation/manage-runas-account.md#limit-run-as-account-permissions)」を参照してください。

サブスクリプション管理者ロールのメンバーであり、サブスクリプションの共同管理者であるユーザーは、誰でも実行アカウントを作成できます。

Azure Automation アカウントで実行アカウントを作成するには、次の操作を行います。

1. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、 **[Automation アカウント]** を入力して選択します。

2. **[Automation アカウント]** ページで、自分の Azure Automation アカウントの名前を選択します。

3. ウィンドウの左側のペインで、 **[アカウント設定]** セクションの **[実行アカウント]** を選択します。

4. **[Azure 実行アカウント]** を選択します。 **[Azure 実行アカウントを追加する]** ペインが表示されたら、概要情報を確認した後で **[作成]** を選択して、アカウントの作成プロセスを開始します。

5. Azure で実行アカウントが作成されるまで数分待ちます。 メニューの [通知] で作成の進行状況を追跡できます。

6. 作成プロセスが完了すると、指定の Azure Automation アカウントに **AzureRunAsConnection** という名前の資産が作成されます。 **[Azure 実行アカウント]** を選択します。 この接続資産には、アプリケーション ID、テナント ID、サブスクリプション ID、証明書の拇印が格納されます。 **[接続]** ページでも同じ情報を確認できます。 このページに移動するには、ウィンドウの左側のペインで、 **[共有リソース]** セクションの **[接続]** を選択し、**AzureRunAsConnection** という接続資産をクリックします。

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Azure Logic Apps と実行スケジュールを作成する

最後に、Azure Logic Apps を作成し、新しいスケーリング ツールの実行スケジュールを設定する必要があります。 まず、PowerShell セッション内で使用する [Desktop Virtualization PowerShell モジュール](powershell-module.md)をダウンロードしてインポートします (まだ行っていない場合)。

1. Windows PowerShell を開きます。

2. 次のコマンドレットを実行して、Azure アカウントにサインインします。

    ```powershell
    Login-AzAccount
    ```

3. 次のコマンドレットを実行して、Azure Logic Apps を作成するためのスクリプトをダウンロードします。

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzLogicApp.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzLogicApp.ps1"
    ```

4. 次の PowerShell スクリプトを実行して、ホスト プール用の Azure Logic Apps と実行スケジュールを作成します。

    >[!NOTE]
    >このスクリプトは、自動スケーリングするホスト プールごとに実行する必要がありますが、必要な Azure Automation アカウントは 1 つだけです。

    ```powershell
    $AADTenantId = (Get-AzContext).Tenant.Id

    $AzSubscription = Get-AzSubscription | Out-GridView -OutputMode:Single -Title "Select your Azure Subscription"
    Select-AzSubscription -Subscription $AzSubscription.Id

    $ResourceGroup = Get-AzResourceGroup | Out-GridView -OutputMode:Single -Title "Select the resource group for the new Azure Logic App"

    $WVDHostPool = Get-AzResource -ResourceType "Microsoft.DesktopVirtualization/hostpools" | Out-GridView -OutputMode:Single -Title "Select the host pool you'd like to scale"

    $LogAnalyticsWorkspaceId = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Workspace ID returned by when you created the Azure Automation account, otherwise leave it blank"
    $LogAnalyticsPrimaryKey = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Primary Key returned by when you created the Azure Automation account, otherwise leave it blank"
    $RecurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
    $BeginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
    $EndPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
    $TimeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
    $SessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
    $MinimumNumberOfRDSH = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
    $MaintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"
    $LimitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, any session host VM that has user sessions, will be left untouched"
    $LogOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
    $LogOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

    $AutoAccount = Get-AzAutomationAccount | Out-GridView -OutputMode:Single -Title "Select the Azure Automation account"
    $AutoAccountConnection = Get-AzAutomationConnection -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName | Out-GridView -OutputMode:Single -Title "Select the Azure RunAs connection asset"

    $WebhookURIAutoVar = Get-AzAutomationVariable -Name 'WebhookURIARMBased' -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName

    $Params = @{
         "AADTenantId"                   = $AADTenantId                             # Optional. If not specified, it will use the current Azure context
         "SubscriptionID"                = $AzSubscription.Id                       # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"             = $ResourceGroup.ResourceGroupName         # Optional. Default: "WVDAutoScaleResourceGroup"
         "Location"                      = $ResourceGroup.Location                  # Optional. Default: "West US2"
         "UseARMAPI"                     = $true
         "HostPoolName"                  = $WVDHostPool.Name
         "HostPoolResourceGroupName"     = $WVDHostPool.ResourceGroupName           # Optional. Default: same as ResourceGroupName param value
         "LogAnalyticsWorkspaceId"       = $LogAnalyticsWorkspaceId                 # Optional. If not specified, script will not log to the Log Analytics
         "LogAnalyticsPrimaryKey"        = $LogAnalyticsPrimaryKey                  # Optional. If not specified, script will not log to the Log Analytics
         "ConnectionAssetName"           = $AutoAccountConnection.Name              # Optional. Default: "AzureRunAsConnection"
         "RecurrenceInterval"            = $RecurrenceInterval                      # Optional. Default: 15
         "BeginPeakTime"                 = $BeginPeakTime                           # Optional. Default: "09:00"
         "EndPeakTime"                   = $EndPeakTime                             # Optional. Default: "17:00"
         "TimeDifference"                = $TimeDifference                          # Optional. Default: "-7:00"
         "SessionThresholdPerCPU"        = $SessionThresholdPerCPU                  # Optional. Default: 1
         "MinimumNumberOfRDSH"           = $MinimumNumberOfRDSH                     # Optional. Default: 1
         "MaintenanceTagName"            = $MaintenanceTagName                      # Optional.
         "LimitSecondsToForceLogOffUser" = $LimitSecondsToForceLogOffUser           # Optional. Default: 1
         "LogOffMessageTitle"            = $LogOffMessageTitle                      # Optional. Default: "Machine is about to shutdown."
         "LogOffMessageBody"             = $LogOffMessageBody                       # Optional. Default: "Your session will be logged off. Please save and close everything."
         "WebhookURI"                    = $WebhookURIAutoVar.Value
    }

    .\CreateOrUpdateAzLogicApp.ps1 @Params
    ```

    スクリプトを実行すると、次の図に示すように、Azure Logic Appsがリソース グループに表示されます。

    >[!div class="mx-imgBorder"]
    >![Azure Logic Appsの例を表す概要ページの画像。](media/logic-app.png)

    繰り返し間隔やタイム ゾーンを変更するなど、実行スケジュールに変更を加えるには、Azure Logic Appsの自動スケーリングのスケジューラにアクセスし、 **[編集]** を選択して Azure Logic Apps デザイナーに移動します。

    >[!div class="mx-imgBorder"]
    >![Azure Logic Apps デザイナーの画像。 ユーザーが繰り返し時間と Webhook ファイルを編集するための [繰り返し] および [Webhook] メニューが開いています。](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>スケーリング ツールを管理する

スケーリング ツールの作成が完了したので、その出力にアクセスできます。 ここでは、役に立つと思われるいくつかの機能について説明します。

### <a name="view-job-status"></a>ジョブの状態を見る

すべての Runbook ジョブの状態の概要を表示したり、Azure portal で特定の Runbook ジョブの詳細な状態を表示したりできます。

選択した Azure Automation アカウントの右側にある [ジョブの統計情報] で、すべての Runbook ジョブの概要の一覧を表示できます。 ウィンドウの左側にある **[ジョブ]** ページを開くと、現在のジョブの状態、開始時刻、完了時刻が表示されます。

>[!div class="mx-imgBorder"]
>![ジョブの状態ページのスクリーンショット。](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>ログとスケーリング ツールの出力を見る

スケールアウト操作やスケールイン操作のログを表示するには、Runbook を開いて、ジョブを選択します。

Azure Automation アカウントをホストしているリソース グループの Runbook に移動して、 **[概要]** を選択します。 [概要] ページで、 **[最近のジョブ]** の下にあるジョブを選択すると、次の図に示すようなスケーリング ツールの出力が表示されます。

>[!div class="mx-imgBorder"]
>![スケーリング ツールの出力ウィンドウの画像。](media/tool-output.png)

### <a name="check-the-runbook-script-version-number"></a>Runbook スクリプトのバージョン番号を確認する

使用している Runbook スクリプトのバージョンを確認するには、Azure Automation アカウントで Runbook ファイルを開き、 **[表示]** を選択します。 Runbook のスクリプトが画面の右側に表示されます。 このスクリプトでは、`SYNOPSIS` セクションにバージョン番号が `v#.#.#` 形式で表示されます。 最新のバージョン番号は[こちら](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/wvd-scaling-script/ARM_based/basicScale.ps1#L1)で入手できます。 Runbook スクリプトにバージョン番号が表示されない場合は、以前のバージョンのスクリプトが実行されているということなので、すぐに更新する必要があります。 Runbook スクリプトを更新する必要がある場合は、「[Azure Automation アカウントを作成または更新する](#create-or-update-an-azure-automation-account)」の手順に従ってください。

### <a name="reporting-issues"></a>問題の報告

問題を報告する際には、トラブルシューティングに役立つ次の情報を提供する必要があります。

- 問題の原因となったジョブの **[すべてのログ]** タブのすべてのログ。 ログを取得する方法については、「[ログとスケーリング ツールの出力を見る](#view-logs-and-scaling-tool-output)」の手順に従ってください。 機密または非公開の情報がログに含まれている場合は、問題を Microsoft にお送りいただく前に削除することができます。

- 使用している Runbook スクリプトのバージョン。 バージョン番号を取得する方法については、「[Runbook スクリプトのバージョン番号を確認する](#check-the-runbook-script-version-number)」を参照してください。

- Azure Automation アカウントにインストールされている次の PowerShell モジュールのそれぞれのバージョン番号。 これらのモジュールを確認するには、Azure Automation アカウントを開き、ウィンドウの左側のペインの **[共有リソース]** セクションで **[モジュール]** を選択し、モジュールの名前を検索します。
    - Az.Accounts
    - Az.Compute
    - Az.Resources
    - Az.Automation
    - OMSIngestionAPI
    - Az.DesktopVirtualization

- [実行アカウント](#create-an-azure-automation-run-as-account)の有効期限。 これを確認するには、Azure Automation アカウントを開き、ウィンドウの左側のペインの **[アカウント設定]** の下にある **[実行アカウント]** を選択します。 有効期限は **[Azure 実行アカウント]** の下で確認できます。

### <a name="log-analytics"></a>Log Analytics

Log Analytics を使用することにした場合、Log Analytics ワークスペースの **[ログ]** ビューの **[カスタム ログ]** の下にある **WVDTenantScale_CL** という名前のカスタム ログですべてのログ データを表示できます。 役に立ちそうなサンプル クエリをいくつか示しました。

- ホスト プールのすべてのログを表示するには、次のクエリを入力してください

    ```Kusto
    WVDTenantScale_CL
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- ホスト プールで現在実行中のセッション ホスト VM とアクティブ ユーザー セッションの合計数を表示するには、次のクエリを入力してください

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Number of running session hosts:"
         or logmessage_s contains "Number of user sessions:"
         or logmessage_s contains "Number of user sessions per Core:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- ホスト プール内のすべてのセッション ホスト VM の状態を表示するには、次のクエリを入力してください

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Session host:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- エラーと警告を表示するには、次のクエリを入力してください

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "ERROR:" or logmessage_s contains "WARN:"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

## <a name="report-issues"></a>レポートに関する問題

スケーリング ツールの問題に関する報告は、現在、Microsoft サポートで処理されています。 問題の報告を作成する場合は、「[問題の報告](#reporting-issues)」に記載されている手順に従ってください。 ツールに関するフィードバックがある場合、または新機能をリクエストする場合は、[RDS GitHub ページ](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4-WVD-scaling-tool)で、"4-WVD-scaling-tool" という名前の GitHub 問題を開きます。
