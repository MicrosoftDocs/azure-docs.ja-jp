---
title: Azure Automation への Linux Hybrid Runbook Worker のデプロイ
description: この記事では、Azure Automation Hybrid Runbook Worker をインストールして、ローカル データ センターやクラウド環境にある Linux ベースのマシン上で Runbook を実行する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: 7f19aec65ed2616d757718116ac948473dd4b0ed
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448021"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker を展開する

Azure Automation の Hybrid Runbook Worker 機能を使う、ロールをホスティングしているマシン上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 Linux Hybrid Runbook Worker は、昇格が必要なコマンドを実行するために昇格させることができる特殊なユーザーとして Runbook を実行します。 Azure Automation によって Runbook が格納および管理された後、1 つ以上の指定されたマシンに配信されます。 この記事では、Linux マシンに Hybrid Runbook Worker をインストールする方法、worker を削除する方法、および Hybrid Runbook Worker グループを削除する方法について説明します。

Runbook Worker が正常にデプロイされたら、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。

## <a name="prerequisites"></a>前提条件

始める前に、必ず以下のものを用意してください。

### <a name="a-log-analytics-workspace"></a>Log Analytics ワークスペース

Hybrid Runbook Worker ロールでは、Azure Monitor Log Analytics ワークスペースに依存してロールがインストールおよび構成されます。 [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace)、[PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)、[Azure portal](../azure-monitor/learn/quick-create-workspace.md) のいずれかを使用して作成できます。

Azure Monitor Log Analytics ワークスペースがない場合は、ワークスペースを作成する前に、[Azure Monitor ログの設計ガイダンス](../azure-monitor/platform/design-logs-deployment.md)を確認してください。

ワークスペースはあっても、それが Automation アカウントにリンクされていない場合は、Automation の機能を有効にすると、Hybrid Runbook Worker のサポートを含む Azure Automation の機能が追加されます。 Log Analytics ワークスペースで Azure Automation 機能のいずれかを有効にすると (具体的には、[Update Management](update-management/update-mgmt-overview.md) または [Change Tracking とインベントリ](change-tracking.md))、ワーカー コンポーネントがエージェント マシンに自動的にプッシュされます。

ワークスペースに Update Management 機能を追加するには、次の PowerShell コマンドレットを実行します。

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

ワークスペースに Change Tracking とインベントリ機能を追加するには、次の PowerShell コマンドレットを実行します。

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Log Analytics エージェント

Hybrid Runbook Worker ロールには、サポートされている Linux オペレーティング システム用の [Log Analytics エージェント](../azure-monitor/platform/log-analytics-agent.md)が必要です。

### <a name="supported-linux-operating-systems"></a>サポートされている Linux オペレーティング システム

Hybrid Runbook Worker 機能は、次のディストリビューションをサポートします。

* Amazon Linux 2012.09 ～ 2015.09 (x86/x64)
* CentOS Linux 5、6、および 7 (x86/x64)
* Oracle Linux 5、6、および 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6、および 7 (x86/x64)
* Debian GNU/Linux 6、7、および 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS、16.04 LTS、および 18.04 (x86/x64)
* SUSE Linux Enterprise Server 11 および 12 (x86/x64)

### <a name="minimum-requirements"></a>最小要件

Linux Hybrid Runbook Worker の最小要件は次のようになります。

* 2 コア
* 4 GB の RAM
* ポート 443 (送信)

| **必須パッケージ** | **説明** | **最小バージョン**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C ライブラリ| 2.5-12 |
|Openssl| OpenSSL ライブラリ | 1.0 (TLS 1.1 と TLS 1.2 がサポートされます)|
|Curl | cURL Web クライアント | 7.15.5|
|Python-ctypes | Python 2.x が必要 |
|PAM | Pluggable Authentication Module (プラグ可能な認証モジュール)|
| **オプション パッケージ** | **説明** | **最小バージョン**|
| PowerShell Core | PowerShell Runbook を実行するには、PowerShell Core をインストールする必要があります。 インストール方法については、「[Linux への PowerShell Core のインストール](/powershell/scripting/install/installing-powershell-core-on-linux)」をご覧ください。 | 6.0.0 |

## <a name="supported-runbook-types"></a>サポートされている Runbook の種類

Linux Hybrid Runbook Worker でサポートされている Azure Automation の Runbook の種類は制限されており、次の表ではそれについて説明します。

|Runbook の種類 | サポートされています |
|-------------|-----------|
|Python 2 |はい |
|PowerShell |可<sup>1</sup> |
|PowerShell ワークフロー |いいえ |
|グラフィカル |いいえ |
|グラフィカル PowerShell ワークフロー |いいえ |

<sup>1</sup> PowerShell Runbook を使用するには、Linux マシンに PowerShell Core がインストールされている必要があります。 インストール方法については、「[Linux への PowerShell Core のインストール](/powershell/scripting/install/installing-powershell-core-on-linux)」をご覧ください。

## <a name="install-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker をインストールする

Linux Hybrid Runbook Worker をインストールして構成するには、次の手順のようにします。

1. ターゲット マシンに Log Analytics エージェントをデプロイします。

    * Azure VM の場合、[Linux 用仮想マシン拡張機能](../virtual-machines/extensions/oms-linux.md)を使用して、Linux 用 Log Analytics エージェントをインストールします。 この拡張機能では、Azure 仮想マシンに Log Analytics エージェントがインストールされ、Azure Resource Manager テンプレートまたは Azure CLI を使用して仮想マシンが既存の Log Analytics ワークスペースに登録されます。 エージェントがインストールされたら、Automation アカウントの Hybrid Runbook Worker グループに VM を追加できます。

    * Azure 以外の VM の場合は、「[Linux コンピューターを Azure Monitor に接続する](../azure-monitor/platform/agent-linux.md)」の記事で説明されているデプロイ オプションを使用して、Linux 用の Log Analytics エージェントをインストールします。 マシンごとにこのプロセスを繰り返して、複数の worker を環境に追加できます。 エージェントがインストールされたら、Automation アカウントの Hybrid Runbook Worker グループに VM を追加できます。

    > [!NOTE]
    > Hybrid Runbook Worker ロールをサポートするマシンの構成を Desired State Configuration (DSC) を使用して管理するには、マシンを DSC ノードとして追加する必要があります。

    > [!NOTE]
    > Linux Hybrid Worker のインストール中は、対応する sudo アクセス許可を持った [nxautomation アカウント](automation-runbook-execution.md#log-analytics-agent-for-linux)が存在していなければなりません。 そのアカウントが存在しない、または適切なアクセス許可がアカウントにない状態でワーカーをインストールしようとすると、インストールは失敗します。

2. エージェントがワークスペースに報告していることを確認します。

    Linux 用 Log Analytics エージェントにより、マシンが Azure Monitor Log Analytics ワークスペースに接続されます。 マシンにエージェントをインストールし、ワークスペースに接続すると、Hybrid Runbook Worker に必要なコンポーネントが自動的にダウンロードされます。

    エージェントが Log Analytics ワークスペースに正常に接続されたら、数分後に次のクエリを実行して、ワークスペースにハートビート データが送信されていることを確認できます。

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    検索結果には、マシンのハートビート レコードが表示されます。これにより、エージェントがサービスに接続され、レポートが送信されていることが示されます。 既定では、各エージェントからその割り当て済みのワークスペースにハートビート レコードが転送されます。

3. 次のコマンドを実行して、マシンを Hybrid Runbook Worker グループに追加します。このとき、`-w`、`-k`、`-g`、`-e` のパラメーターに値を指定します。

    パラメーター `-k` と `-e` に必要な情報は、Automation アカウントの **[キー]** ページから取得できます。 ページの左側にある **[アカウントの設定]** セクションで **[キー]** を選択します。

    ![キーの管理ページ](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * `-e` パラメーターには、 **[URL]** の値をコピーします。

    * `-k` パラメーターには、 **[プライマリ アクセス キー]** の値をコピーします。

    * `-g` パラメーターでは、新しい Linux Hybrid Runbook Worker を参加させる Hybrid Runbook Worker グループの名前を指定します。 Automation アカウントにこのグループが既に存在する場合は、現在のマシンがそれに追加されます。 このグループが存在しない場合は、その名前で作成されます。

    * `-w` パラメーターでは、Log Analytics ワークスペースの ID を指定します。

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

4. コマンドが完了すると、Automation アカウントの [ハイブリッド Worker グループ] ページに、新しいグループとメンバーの数が表示されます。 既存のグループの場合は、メンバーの数がインクリメントされます。 [ハイブリッド worker グループ] ページ上にあるリストからグループを選択し、 **[ハイブリッド worker]** タイルを選択できます。 [ハイブリッド worker] ページで、グループの各メンバーが一覧表示されます。

    > [!NOTE]
    > Azure VM 用の Linux で Log Analytics 仮想マシン拡張機能を使用している場合は、`autoUpgradeMinorVersion` を `false` に設定することをお勧めします。これは、バージョンを自動的にアップグレードすると、Hybrid Runbook Worker で問題が発生する場合があるからです。 拡張機能を手動でアップグレードする方法については、「[Azure CLI でのデプロイ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)」を参照してください。

## <a name="turn-off-signature-validation"></a>署名の検証をオフにする

既定では、Linux Hybrid Runbook Worker は、署名の検証を必要とします。 worker に対して署名されていない Runbook を実行すると、`Signature validation failed` エラーが表示されます。 署名の検証をオフにするには、次のコマンドを実行します。 2 番目のパラメーターは Log Analytics のワークスペース ID に置き換えます。

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-machine"></a><a name="remove-linux-hybrid-runbook-worker"></a>オンプレミスの Linux マシンから Hybrid Runbook Worker を削除する

Hybrid Runbook Worker で `ls /var/opt/microsoft/omsagent` コマンドを使用して、ワークスペース ID を取得できます。 ワークスペース ID を使用して名前が付けられたフォルダーが作成されます。

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> このスクリプトでは、マシンから Linux 用 Log Analytics エージェントは削除されません。 Hybrid Runbook Worker ロールの機能と構成のみが削除されます。

## <a name="remove-a-hybrid-worker-group"></a>ハイブリッド worker グループを削除する

Linux マシンの Hybrid Runbook Worker グループを削除するには、Windows ハイブリッド Worker グループの場合と同じ手順を使用します。 詳細については、「[ハイブリッド worker グループを削除する](automation-windows-hrw-install.md#remove-a-hybrid-worker-group)」を参照してください。

## <a name="next-steps"></a>次のステップ

* オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を学習するには、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」をご覧ください。

* Hybrid Runbook Worker のトラブルシューティングを行う方法については、「[Hybrid Runbook Worker のトラブルシューティング - Linux](troubleshoot/hybrid-runbook-worker.md#linux)」を参照してください。
