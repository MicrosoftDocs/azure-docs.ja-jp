---
title: Azure Automation への Windows Hybrid Runbook Worker のデプロイ
description: この記事では、お使いのローカル データ センターまたはクラウド環境内の Windows ベースのマシン上で Runbook を実行するために使用できる Hybrid Runbook Worker をデプロイする方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 11/24/2020
ms.topic: conceptual
ms.openlocfilehash: f6858c7350e6c72a096b2f2bd5f4a4ff606bf023
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651359"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker をデプロイする

Azure Automation のユーザー Hybrid Runbook Worker 機能を使用すると、Azure または Azure 以外のマシン ([Azure Arc 対応サーバー](../azure-arc/servers/overview.md)に登録されているサーバーを含む) 上で Runbook を直接実行することができます。 ロールをホストしているマシンまたはサーバーで、環境内のリソースに対して Runbook を直接実行して、それらのローカル リソースを管理できます。

Azure Automation によって Runbook が格納および管理された後、1 つ以上の指定されたマシンに配信されます。 この記事では、Windows マシンにユーザー Hybrid Runbook Worker をデプロイする方法、worker を削除する方法、および Hybrid Runbook Worker グループを削除する方法について説明します。

Runbook Worker が正常にデプロイされたら、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。

## <a name="prerequisites"></a>前提条件

始める前に、以下のものを用意してください。

### <a name="a-log-analytics-workspace"></a>Log Analytics ワークスペース

Hybrid Runbook Worker ロールでは、Azure Monitor Log Analytics ワークスペースに依存してロールがインストールおよび構成されます。 [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace)、[PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)、[Azure portal](../azure-monitor/logs/quick-create-workspace.md) のいずれかを使用して作成できます。

Azure Monitor Log Analytics ワークスペースがない場合は、ワークスペースを作成する前に、[Azure Monitor ログの設計ガイダンス](../azure-monitor/logs/design-logs-deployment.md)を確認してください。

### <a name="log-analytics-agent"></a>Log Analytics エージェント

Hybrid Runbook Worker ロールには、サポートされている Windows オペレーティング システム用の [Log Analytics エージェント](../azure-monitor/agents/log-analytics-agent.md)が必要です。 Azure の外部でホストされているサーバーまたはマシンの場合、[Azure Arc 対応サーバー](../azure-arc/servers/overview.md)を使用すれば Log Analytics エージェントをインストールできます。

### <a name="supported-windows-operating-system"></a>サポートされている Windows オペレーティング システム

Hybrid Runbook Worker 機能では、次のオペレーティング システムがサポートされています。

* Windows Server 2019 (Server Core を含む)
* Windows Server 2016、バージョン 1709 および 1803 (Server Core を除く)
* Windows Server 2012、2012 R2
* Windows Server 2008 SP2 (x64)、2008 R2
* Windows 10 Enterprise (マルチセッションを含む) および Pro
* Windows 8 Enterprise および Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>最小要件

Windows システムおよびユーザー Hybrid Runbook Worker の最小要件は次のようになります。

* Windows PowerShell 5.1 ([WMF 5.1 をダウンロードする](https://www.microsoft.com/download/details.aspx?id=54616))。 PowerShell Core はサポートされていません。
* .NET Framework 4.6.2 以降。
* 2 コア
* 4 GB の RAM
* ポート 443 (送信)

### <a name="network-configuration"></a>ネットワーク構成

Hybrid Runbook Worker のネットワーク要件については、[ネットワークの構成](automation-hybrid-runbook-worker.md#network-planning)に関するページを参照してください。

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Hybrid Runbook Worker グループへのマシンの追加

ご利用の Automation アカウントのいずれか 1 つの Hybrid Runbook Worker グループに worker マシンを追加できます。 Update Management によって管理されているシステム Hybrid Runbook Worker をホストしているマシンの場合は、Hybrid Runbook Worker グループに追加できます。 しかし、Update Management と Hybrid Runbook Worker グループ メンバーシップの両方に同じ Automation アカウントを使用する必要があります。

>[!NOTE]
>Azure Automation [Update Management](./update-management/overview.md) を使用すると、Update Management に対して有効になっている Azure または Azure 以外のマシンに、システム Hybrid Runbook Worker が自動的にインストールされます。 ただし、この worker は、Automation アカウント内の Hybrid Runbook Worker グループには登録されません。 これらのマシン上で Runbook を実行するには、それを Hybrid Runbook Worker グループに追加する必要があります。 それをグループに追加するには、「[手動デプロイ](#manual-deployment)」セクションの手順 6 に従ってください。

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Azure Automation State Configuration による管理のための有効化

Azure Automation State Configuration による管理のためのマシンの有効化については、[Azure Automation State Configuration による管理のためのマシンの有効化](automation-dsc-onboarding.md)に関する記事を参照してください。

> [!NOTE]
> Hybrid Runbook Worker ロールをサポートするマシンの構成を Desired State Configuration (DSC) を使用して管理するには、マシンを DSC ノードとして追加する必要があります。

## <a name="installation-options"></a>インストール オプション

Windows ユーザー Hybrid Runbook Worker をインストールして構成するには、次の方法のいずれかを使用します。

* 1 つ以上の Windows マシンの構成プロセスを完全に[自動化](#automated-deployment)するには、提供された PowerShell スクリプトを使用します。 データセンターまたは他のクラウド環境のマシンの場合、この方法をお勧めします。
* Automation ソリューションを手動でインポートし、Windows 用の Log Analytics エージェントをインストールして、マシン上で worker ロールを構成します。

## <a name="automated-deployment"></a>自動化されたデプロイ

自動化されたデプロイ方法では、PowerShell スクリプト **New-OnPremiseHybridWorker.ps1** を使用して、Windows Hybrid Runbook Worker ロールを自動化および構成します。 それによって次が実行されます。

* 必要なモジュールをインストールする
* Azure アカウントを使用してサインインする
* 指定されたリソース グループと Automation アカウントが存在することを確認する
* Automation アカウント属性への参照を作成する
* Azure Monitor Log Analytics ワークスペースを作成する (指定されていない場合)
* ワークスペースで Azure Automation ソリューションを有効にする
* Windows 用 Log Analytics エージェントをダウンロードしてインストールする
* マシンを Hybrid Runbook Worker として登録する

次の手順を実行して、スクリプトを使用して Windows マシンにロールをインストールします。

1. [PowerShell ギャラリー](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)から **New-OnPremiseHybridWorker.ps1** スクリプトをダウンロードします。 スクリプトをダウンロードしたら、ターゲット マシン上でコピーまたは実行します。 **New-OnPremiseHybridWorker.ps1** スクリプトでは、実行中に次のパラメーターが使用されます。

    | パラメーター | Status | 説明 |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | Mandatory | Automation アカウントに関連付けられているリソース グループの名前。 |
    | `AutomationAccountName` | Mandatory | Automation アカウントの名前。
    | `Credential` | 省略可能 | Azure 環境にログインするときに使用する資格情報。 |
    | `HybridGroupName` | Mandatory | このシナリオをサポートしている Runbook のターゲットとして指定する Hybrid Runbook Worker グループの名前。 |
    | `OMSResourceGroupName` | 省略可能 | Log Analytics ワークスペース用のリソース グループの名前。 このリソース グループが指定されていない場合、`AAResourceGroupName` の値が使用されます。 |
    | `SubscriptionID` | Mandatory | お使いの Automation アカウントに関連付けられている Azure サブスクリプションの識別子。 |
    | `TenantID` | 省略可能 | Automation アカウントに関連付けられているテナント組織の識別子。 |
    | `WorkspaceName` | 省略可能 | Log Analytics ワークスペース名。 Log Analytics ワークスペースがない場合は、スクリプトがこれを作成して構成します。 |

2. 管理者特権の 64 ビット PowerShell コマンド プロンプトを開きます。

3. PowerShell コマンド プロンプトから、ダウンロードしたスクリプトがあるフォルダーを参照します。 パラメーター `AutomationAccountName`、`AAResourceGroupName`、`OMSResourceGroupName`、`HybridGroupName`、`SubscriptionID`、および `WorkspaceName`の値を変更します。 その後、スクリプトを実行します。

    スクリプトの実行後、Azure での認証が求められます。 **サブスクリプション管理** ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用してサインインする必要があります。

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

4. NuGet をインストールし、Azure 資格情報で認証するように求められます。 最新の NuGet バージョンをお持ちでない場合は、[使用可能な NuGet ディストリビューション バージョン](https://www.nuget.org/downloads)からダウンロードできます。

5. スクリプトが終了したら、デプロイを確認します。 Automation アカウントの **[Hybrid Runbook Worker のグループ]** ページの **[User hybrid runbook workers group]\(ユーザー Hybrid Runbook Worker グループ\)** タブの下に、新しいグループとメンバーの人数が表示されます。 既存のグループの場合は、メンバーの数が増分されます。 そのページの一覧からグループを選択できます。左側のメニューで **[ハイブリッド worker]** を選択します。 **[ハイブリッド worker]** ページでは、グループの各メンバーの一覧を確認することができます。

## <a name="manual-deployment"></a>手動デプロイ

Windows Hybrid Runbook Worker をインストールして構成するには、次の手順を実行します。

1. 管理者特権での PowerShell コマンドプロンプトで、または [Azure portal](https://portal.azure.com) の Cloud Shell で次のコマンドを実行することにより、Log Analytics ワークスペース内で Azure Automation ソリューションを有効にします。

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. ターゲット マシンに Log Analytics エージェントをデプロイします。

    * Azure VM の場合、[Windows 用仮想マシン拡張機能](../virtual-machines/extensions/oms-windows.md)を使用して、Windows 用 Log Analytics エージェントをインストールします。 この拡張機能では、Azure 仮想マシンに Log Analytics エージェントがインストールされ、仮想マシンが既存の Log Analytics ワークスペースに登録されます。 Azure Resource Manager テンプレート、PowerShell、または Azure Policy を使用すれば、組み込みポリシーである [*Linux* または *Windows* VM 用の Log Analytics エージェントのデプロイ](../governance/policy/samples/built-in-policies.md#monitoring)を割り当てることができます。 エージェントがインストールされたら、Automation アカウントの Hybrid Runbook Worker グループにマシンを追加できます。
    
    * Azure 以外のマシンの場合は、[Azure Arc 対応サーバー](../azure-arc/servers/overview.md)を使用して Log Analytics エージェントをインストールできます。 Arc 対応サーバーでは、以下の方法を使用した Log Analytics エージェントのデプロイがサポートされています。
    
        - VM 拡張機能フレームワークの使用。
        
            Azure Arc 対応サーバーのこの機能を使用すると、Azure 以外の Windows や Linux のサーバーに、Log Analytics エージェントの VM 拡張機能をデプロイできます。 VM 拡張機能は、ハイブリッド コンピューターまたは Arc 対応サーバーで管理されているサーバーで、次の方法を使用して管理できます。
        
            - [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Resource Manager テンプレート](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - Azure Policy の使用。
        
            この方法を利用し、Azure Policy の "[Log Analytics エージェントを Linux または Windows Azure Arc マシンにデプロイする](../governance/policy/samples/built-in-policies.md#monitoring)" という組み込みポリシーを使用して、Arc 対応サーバーに Log Analytics エージェントがインストールされているかどうかを監査します。 エージェントがインストールされていない場合は、修復タスクを使用して自動的にそれがデプロイされます。 または、Azure Monitor for VMs を使用してマシンを監視する予定の場合は、代わりに "[Azure Monitor for VMs を有効にする](../governance/policy/samples/built-in-initiatives.md#monitoring)" というイニシアティブを使用して Log Analytics エージェントのインストールと構成を行います。

    Azure Policy を使用して、Windows または Linux 用の Log Analytics エージェントをインストールすることをお勧めします。

3. エージェントがワークスペースに報告していることを確認します。

    Windows 用 Log Analytics エージェントにより、マシンが Azure Monitor Log Analytics ワークスペースに接続されます。 マシンにエージェントをインストールし、ワークスペースに接続すると、Hybrid Runbook Worker に必要なコンポーネントが自動的にダウンロードされます。

    エージェントが Log Analytics ワークスペースに正常に接続されたら、数分後に次のクエリを実行して、ワークスペースにハートビート データが送信されていることを確認できます。

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    検索結果には、マシンのハートビート レコードが表示されます。これにより、エージェントがサービスに接続され、レポートが送信されていることが示されます。 既定では、各エージェントからその割り当て済みのワークスペースにハートビート レコードが転送されます。 エージェントのインストールとセットアップを完了するには、次の手順を使用します。

4. Log Analytics エージェントをホストしているマシン上で Hybrid Runbook Worker のバージョンを確認し、`C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` を参照して **version** サブフォルダーを確認します。 このフォルダーは、ワークスペースでソリューションが有効にされた後、数分後にマシン上に表示されます。

5. Runbook 環境をインストールして、Azure Automation に接続する Log Analytics ワークスペースにレポートを送信して **Automation** ソリューションをインポートするようにエージェントを構成すると、そのソリューションによって `HybridRegistration` PowerShell モジュールがプッシュダウンされます。 このモジュールには、`Add-HybridRunbookWorker` コマンドレットが含まれています。 マシンに Runbook 環境をインストールして、Azure Automation に登録する場合は、このコマンドレットを使用します。

    管理者モードで PowerShell セッションを開き、次のコマンドを実行してモジュールをインポートします。

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

6. パラメーター `Url`、`Key`、および `GroupName` の値を指定して、`Add-HybridRunbookWorker` コマンドレットを実行します。

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    パラメーター `Url` と `Key` に必要な情報は、Automation アカウントの **[キー]** ページから取得できます。 ページの左側にある **[アカウントの設定]** セクションで **[キー]** を選択します。

    ![キーの管理ページ](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * `Url` パラメーターには、 **[URL]** の値をコピーします。

    * `Key` パラメーターには、 **[プライマリ アクセス キー]** の値をコピーします。

    * `GroupName` パラメーターには、Hybrid Runbook Worker グループの名前を使用します。 Automation アカウントにこのグループが既に存在する場合は、現在のマシンがそれに追加されます。 このグループが存在しない場合は、追加されます。

    * 必要に応じて、`Verbose` パラメーターを設定して、インストールの詳細を取得します。

7. コマンドが完了したら、デプロイを確認します。 Automation アカウントの **[Hybrid Runbook Worker のグループ]** ページの **[User hybrid runbook workers group]\(ユーザー Hybrid Runbook Worker グループ\)** タブの下に、新しいまたは既存のグループとメンバーの人数が表示されます。 既存のグループの場合は、メンバーの数が増分されます。 そのページの一覧からグループを選択できます。左側のメニューで **[ハイブリッド worker]** を選択します。 **[ハイブリッド worker]** ページでは、グループの各メンバーの一覧を確認することができます。

## <a name="install-powershell-modules"></a>PowerShell モジュールをインストールする

Runbook は、Azure Automation 環境にインストールされているモジュールで定義されているアクティビティとコマンドレットをすべて使用できます。 これらのモジュールはオンプレミス マシンに自動的に配置されないため、手動でインストールする必要があります。 Azure モジュールは例外です。 このモジュールは既定でインストールされ、すべての Azure サービスのコマンドレットと Azure Automation のアクティビティにアクセスできます。

Hybrid Runbook Worker の主な目的はローカル リソースを管理することであるため、ほとんどの場合、これらのリソースをサポートするモジュール (特に `PowerShellGet` モジュール) をインストールする必要があります。 Windows PowerShell モジュールのインストールについては、「[Windows PowerShell](/powershell/scripting/developer/windows-powershell)」を参照してください。

インストールされるモジュールは `PSModulePath` 環境変数によって参照されている場所に置き、ハイブリッド worker が自動的にインポートできるようにする必要があります。 詳細については、「[PSModulePath にモジュールをインストールする](/powershell/scripting/developer/module/installing-a-powershell-module)」を参照してください。

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>Hybrid Runbook Worker を削除する

1. Azure Portal で、Automation アカウントに移動します。

2. **[アカウント設定]** で、 **[キー]** を選択し、 **[URL]** と **[プライマリ アクセス キー]** の値をメモします。

3. 管理者モードで PowerShell セッションを開き、その URL とプライマリ アクセス キーの値を使用して、次のコマンドを実行します。 削除処理の詳細なログを取得するには、 `Verbose` パラメーターを使用します。 ハイブリッド Worker グループから古いコンピューターを削除するには、省略可能な `machineName` パラメーターを使用します。

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>ハイブリッド worker グループを削除する

Hybrid Runbook Worker グループを削除するには、まず、グループのメンバーであるすべてのマシンから Hybrid Runbook Worker を削除する必要があります。 その後、次の手順を使用してグループを削除します。

1. Azure ポータルで Automation アカウントを開きます。

2. **[プロセス オートメーション]** で **[ハイブリッド Worker グループ]** を選択します。 削除するグループを選択します。 そのグループの [プロパティ] ページが表示されます。

   ![[プロパティ] ページ](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. 選択したグループの [プロパティ] ページで、 **[削除]** を選択します。 メッセージによって、この操作の確認が求められます。 操作を続行する場合は、 **[はい]** を選択します。

   ![Confirmation message](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   このプロセスが完了するまでに数秒かかる場合があります。 進行状況は、メニューの **[通知]** で追跡できます。

## <a name="next-steps"></a>次のステップ

* オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を学習するには、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」をご覧ください。

* Hybrid Runbook Worker のトラブルシューティングを行う方法については、「[Hybrid Runbook Worker のトラブルシューティング](troubleshoot/hybrid-runbook-worker.md#general)」を参照してください。
