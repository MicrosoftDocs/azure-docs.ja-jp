---
title: Azure Automation への Windows Hybrid Runbook Worker のデプロイ
description: この記事では、お使いのローカル データ センターまたはクラウド環境内の Windows ベースのマシン上で Runbook を実行するために使用できる Hybrid Runbook Worker をデプロイする方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 08/20/2020
ms.topic: conceptual
ms.openlocfilehash: 74657743d14b9365f66ed3373592b708a07e11dc
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660514"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker をデプロイする

Azure Automation の Hybrid Runbook Worker 機能を使うと、ロールをホスティングしているマシン上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 Azure Automation によって Runbook が格納および管理された後、1 つ以上の指定されたマシンに配信されます。 この記事では、Windows マシンに Hybrid Runbook Worker をデプロイする方法、worker を削除する方法、および Hybrid Runbook Worker グループを削除する方法について説明します。

Runbook Worker が正常にデプロイされたら、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。

## <a name="prerequisites"></a>前提条件

始める前に、以下のものを用意してください。

### <a name="a-log-analytics-workspace"></a>Log Analytics ワークスペース

Hybrid Runbook Worker ロールでは、Azure Monitor Log Analytics ワークスペースに依存してロールがインストールおよび構成されます。 [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace)、[PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)、[Azure portal](../azure-monitor/learn/quick-create-workspace.md) のいずれかを使用して作成できます。

Azure Monitor Log Analytics ワークスペースがない場合は、ワークスペースを作成する前に、[Azure Monitor ログの設計ガイダンス](../azure-monitor/platform/design-logs-deployment.md)を確認してください。

ワークスペースはあっても、それが Automation アカウントにリンクされていない場合は、Automation の機能を有効にすると、Hybrid Runbook Worker のサポートを含む Azure Automation の機能が追加されます。 Log Analytics ワークスペースで Azure Automation 機能のいずれかを有効にすると (具体的には、[Update Management](update-management/update-mgmt-overview.md) または[変更履歴とインベントリ](change-tracking.md))、worker コンポーネントがエージェント マシンに自動的にプッシュされます。

> [!NOTE]
> Update Management または変更履歴とインベントリ機能を有効にすると、Azure Automation では、Log Analytics ワークスペースと Automation アカウントのリンクに特定のリージョンのみがサポートされます。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](how-to/region-mappings.md)に関する記事をご覧ください。 いずれの機能でも有効にする前に、Azure Automation の [Azure の価格](https://azure.microsoft.com/pricing/details/automation/)情報を確認します。

   ワークスペースに Update Management 機能を追加するには、次の PowerShell コマンドレットを実行します。

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

   ワークスペースに変更履歴とインベントリ機能を追加するには、次の PowerShell コマンドレットを実行します。

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Log Analytics エージェント

Hybrid Runbook Worker ロールには、サポートされている Windows オペレーティング システム用の [Log Analytics エージェント](../azure-monitor/platform/log-analytics-agent.md)が必要です。

### <a name="supported-windows-operating-system"></a>サポートされている Windows オペレーティング システム

Windows Hybrid Runbook Worker では、次のバージョンの Windows オペレーティング システムが正式にサポートされています。

* Windows Server 2019
* Windows Server 2016、バージョン 1709 および 1803
* Windows Server 2012、2012 R2
* Windows Server 2008 SP2 (x64)、2008 R2
* Windows 10 Enterprise (マルチセッションを含む) および Pro
* Windows 8 Enterprise および Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>最小要件

Windows Hybrid Runbook Worker の最小要件は次のようになります。

* Windows PowerShell 5.1 以降 ([WMF 5.1 をダウンロード](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 以降。
* 2 コア
* 4 GB の RAM
* ポート 443 (送信)

### <a name="network-configuration"></a>ネットワーク構成

Hybrid Runbook Worker のその他のネットワーク要件については、[ネットワークの構成](automation-hybrid-runbook-worker.md#network-planning)に関するページを参照してください。

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Hybrid Runbook Worker グループへのマシンの追加

Automation アカウントの Hybrid Runbook Worker グループに worker マシンを追加できます。 Azure Automation 機能と Hybrid Runbook Worker グループ メンバーシップの両方に同じアカウントを使用している場合、Automation Runbook をサポートする必要があることに注意してください。 この機能は、Hybrid Runbook Worker のバージョン 7.2.12024.0 に追加されました。

>[!NOTE]
>Azure Automation [Update Management](update-management/update-mgmt-overview.md) を有効にすると、オペレーティング システムの更新の管理をサポートするために、Log Analytics ワークスペースに接続された Windows マシンはすべて Hybrid Runbook Worker として自動的に構成されます。 ただし、このワーカーは、Automation アカウントで既に定義されている Hybrid Runbook Worker グループには登録されません。

## <a name="enabling-machines-for-management-with-azure-automation-state-configuration"></a>Azure Automation State Configuration による管理のためのマシンの有効化

Azure Automation State Configuration による管理のためのマシンの有効化については、[Azure Automation State Configuration による管理のためのマシンの有効化](automation-dsc-onboarding.md)に関する記事を参照してください。

> [!NOTE]
> Hybrid Runbook Worker ロールをサポートするマシンの構成を Desired State Configuration (DSC) を使用して管理するには、マシンを DSC ノードとして追加する必要があります。

## <a name="windows-hybrid-runbook-worker-installation-options"></a>Windows Hybrid Runbook Worker のインストール オプション

Windows Hybrid Runbook Worker をインストールして構成するには、次の方法のいずれかを使用します。

* Azure VM の場合、[Windows 用仮想マシン拡張機能](../virtual-machines/extensions/oms-windows.md)を使用して、Windows 用 Log Analytics エージェントをインストールします。 この拡張機能では、Azure 仮想マシンに Log Analytics エージェントがインストールされ、Azure Resource Manager テンプレートまたは PowerShell を使用して仮想マシンが既存の Log Analytics ワークスペースに登録されます。 エージェントがインストールされたら、Automation アカウントの Hybrid Runbook Worker グループに VM を追加できます。

* Azure 以外の VM の場合は、「[Windows コンピューターを Azure Monitor に接続する](../azure-monitor/platform/agent-windows.md)」の記事で説明されているデプロイ オプションを使用して、Windows 用の Log Analytics エージェントをインストールします。 複数のマシンにこのプロセスを繰り返して、複数の worker を環境に追加できます。 エージェントがインストールされたら、Automation アカウントの Hybrid Runbook Worker グループに VM を追加できます。

* 1 つ以上の Windows マシンの構成プロセスを完全に[自動化](#automated-deployment)するには、提供された PowerShell スクリプトを使用します。 データセンターまたは他のクラウド環境のマシンの場合、この方法をお勧めします。

## <a name="automated-deployment"></a>自動化されたデプロイ

ターゲット マシン上で、PowerShell スクリプト **New-OnPremiseHybridWorker.ps1** を使用して Windows Hybrid Worker ロールのインストールと構成を自動化するために、次の手順を実行します。 スクリプトでは、次の手順が実行されます。

* 必要なモジュールをインストールする
* Azure アカウントを使用してサインインする
* 指定されたリソース グループと Automation アカウントが存在することを確認する
* Automation アカウント属性への参照を作成する
* Azure Monitor Log Analytics ワークスペースを作成する (指定されていない場合)
* ワークスペースで Azure Automation ソリューションを有効にする
* Windows 用 Log Analytics エージェントをダウンロードしてインストールする
* マシンを Hybrid Runbook Worker として登録する

### <a name="step-1---download-the-powershell-script"></a>手順 1 - PowerShell スクリプトをダウンロードする

[PowerShell ギャラリー](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)から **New-OnPremiseHybridWorker.ps1** スクリプトをダウンロードします。 スクリプトをダウンロードしたら、ターゲット マシン上でコピーまたは実行します。 **New-OnPremiseHybridWorker.ps1** スクリプトでは、実行中に以下で説明するパラメーターが使用されます。

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

### <a name="step-2---open-windows-powershell-command-line-shell"></a>手順 2 - Windows PowerShell コマンド ウィンドウを開く

**[スタート]** メニューから、 **[スタート]** をクリックして「**PowerShell**」と入力し、 **[Windows PowerShell]** を右クリックして、 **[管理者として実行]** をクリックします。

### <a name="step-3---run-the-powershell-script"></a>手順 3 - PowerShell スクリプトを実行する

PowerShell コマンドライン シェルで、ダウンロードしたスクリプトがあるフォルダーを参照します。 パラメーター `AutomationAccountName`、`AAResourceGroupName`、`OMSResourceGroupName`、`HybridGroupName`、`SubscriptionID`、および `WorkspaceName`の値を変更します。 その後、スクリプトを実行します。

スクリプトの実行後、Azure での認証が求められます。 サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用してサインインする必要があります。

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

### <a name="step-4---install-nuget"></a>手順 4 - NuGet をインストールする

NuGet をインストールし、Azure 資格情報で認証するように求められます。 最新の NuGet バージョンをお持ちでない場合は、[使用可能な NuGet ディストリビューション バージョン](https://www.nuget.org/downloads)からダウンロードできます。

### <a name="step-5---verify-the-deployment"></a>手順 5 - デプロイを検証する

スクリプトが終了すると、Automation アカウントの [ハイブリッド Worker グループ] ページに、新しいグループとメンバーの数が表示されます。 既存のグループの場合は、メンバーの数が増分されます。 [ハイブリッド worker グループ] ページ上にあるリストからグループを選択し、 **[ハイブリッド Worker]** タイルを選択できます。 [ハイブリッド Worker] ページでは、グループの各メンバーの一覧を見ることができます。

## <a name="manual-deployment"></a>手動デプロイ

Windows Hybrid Runbook Worker をインストールして構成するには、次の手順を実行します。

### <a name="step-1---verify-agent-is-reporting-to-workspace"></a>手順 1 - エージェントがワークスペースにレポートを送信していることを確認する

Windows 用 Log Analytics エージェントにより、マシンが Azure Monitor Log Analytics ワークスペースに接続されます。 マシンにエージェントをインストールし、ワークスペースに接続すると、Hybrid Runbook Worker に必要なコンポーネントが自動的にダウンロードされます。

エージェントが Log Analytics ワークスペースに正常に接続されたら、数分後に次のクエリを実行して、ワークスペースにハートビート データが送信されていることを確認できます。

```kusto
Heartbeat 
| where Category == "Direct Agent"
| where TimeGenerated > ago(30m)
```

検索結果には、マシンのハートビート レコードが表示されます。これにより、エージェントがサービスに接続され、レポートが送信されていることが示されます。 既定では、各エージェントからその割り当て済みのワークスペースにハートビート レコードが転送されます。 エージェントのインストールとセットアップを完了するには、次の手順を使用します。

1. エージェント マシンを追加するには、この機能を有効にします。 Update Management と Azure VM については、「[Automation アカウントから Update Management を有効にする](update-management/update-mgmt-enable-automation-account.md)」、[Azure portal を参照して Update Management を有効にする方法](update-management/update-mgmt-enable-portal.md)に関するページ、「[Runbook から Update Management を有効にする](update-management/update-mgmt-enable-runbook.md)」、または「[Azure VM から Update Management を有効にする](update-management/update-mgmt-enable-vm.md)」を参照してください。 変更履歴と VM については「[Azure VM の有効化](automation-enable-changes-from-auto-acct.md#enable-azure-vms)」を、Azure 以外の VMについては「[ワークスペースでのマシンの有効化](automation-enable-changes-from-auto-acct.md#enable-machines-in-the-workspace)」を参照してください。

2. Hybrid Runbook Worker のバージョンを確認するには、`C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` を参照し、**version** サブフォルダーを確認します。

### <a name="step-2---install-the-runbook-environment-and-connect-to-azure-automation"></a>手順 2 - Runbook 環境をインストールして、Azure Automation に接続する

Log Analytics ワークスペースにレポートを送信するようにエージェントを構成すると、Azure Automation 機能により、`Add-HybridRunbookWorker` コマンドレットを含む `HybridRegistration` PowerShell モジュールがプッシュダウンされます。 マシンに Runbook 環境をインストールして、Azure Automation に登録する場合は、このコマンドレットを使用します。

管理者モードで PowerShell セッションを開き、次のコマンドを実行してモジュールをインポートします。

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

次に、次の構文を使用して `Add-HybridRunbookWorker` コマンドレットを実行します。

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
```

パラメーター `Url` と `Key` に必要な情報は、Automation アカウントの **[キー]** ページから取得できます。 ページの左側にある **[アカウントの設定]** セクションで **[キー]** を選択します。

![キーの管理ページ](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* `Url` パラメーターには、 **[URL]** の値をコピーします。

* `Key` パラメーターには、 **[プライマリ アクセス キー]** の値をコピーします。

* `GroupName` パラメーターには、Hybrid Runbook Worker グループの名前を使用します。 Automation アカウントにこのグループが既に存在する場合は、現在のマシンがそれに追加されます。 このグループが存在しない場合は、追加されます。

* 必要に応じて、`Verbose` パラメーターを設定して、インストールの詳細を取得します。

### <a name="step-3----install-powershell-modules"></a>手順 3 - PowerShell モジュールをインストールする

Runbook は、Azure Automation 環境にインストールされているモジュールで定義されているアクティビティとコマンドレットをすべて使用できます。 これらのモジュールはオンプレミス マシンに自動的に配置されないため、手動でインストールする必要があります。 Azure モジュールは例外です。 このモジュールは既定でインストールされ、すべての Azure サービスのコマンドレットと Azure Automation のアクティビティにアクセスできます。

Hybrid Runbook Worker の主な目的はローカル リソースを管理することであるため、ほとんどの場合、これらのリソースをサポートするモジュール (特に `PowerShellGet` モジュール) をインストールする必要があります。 Windows PowerShell モジュールのインストールについては、「[Windows PowerShell](/powershell/scripting/developer/windows-powershell)」を参照してください。

インストールされるモジュールは `PSModulePath` 環境変数によって参照されている場所に置き、ハイブリッド worker が自動的にインポートできるようにする必要があります。 詳細については、「[PSModulePath にモジュールをインストールする](/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7)」を参照してください。

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-machine"></a><a name="remove-windows-hybrid-runbook-worker"></a>オンプレミスの Windows マシンから Hybrid Runbook Worker を削除する

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
