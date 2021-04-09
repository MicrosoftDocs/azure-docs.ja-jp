---
title: Azure Automation への Linux Hybrid Runbook Worker のデプロイ
description: この記事では、Azure Automation Hybrid Runbook Worker をインストールして、ローカル データ センターやクラウド環境にある Linux ベースのマシン上で Runbook を実行する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 02/26/2021
ms.topic: conceptual
ms.openlocfilehash: d4d9bcd16e36e76808f19f7fbd43dd0d3e7550c3
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182334"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker を展開する

Azure Automation のユーザー Hybrid Runbook Worker 機能を使用すると、Azure または Azure 以外のマシン ([Azure Arc 対応サーバー](../azure-arc/servers/overview.md)に登録されているサーバーを含む) 上で Runbook を直接実行することができます。 ロールをホストしているマシンまたはサーバーからは、環境内のリソースに対して Runbook を直接実行して、それらのローカル リソースを管理することができます。

Linux Hybrid Runbook Worker は、昇格が必要なコマンドを実行するために昇格させることができる特殊なユーザーとして Runbook を実行します。 Azure Automation によって Runbook が格納および管理された後、1 つ以上の指定されたマシンに配信されます。 この記事では、Linux マシンに Hybrid Runbook Worker をインストールする方法、worker を削除する方法、および Hybrid Runbook Worker グループを削除する方法について説明します。

Runbook Worker が正常にデプロイされたら、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。

## <a name="prerequisites"></a>前提条件

始める前に、以下のものを用意してください。

### <a name="a-log-analytics-workspace"></a>Log Analytics ワークスペース

Hybrid Runbook Worker ロールでは、Azure Monitor Log Analytics ワークスペースに依存してロールがインストールおよび構成されます。 [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace)、[PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)、[Azure portal](../azure-monitor/logs/quick-create-workspace.md) のいずれかを使用して作成できます。

Azure Monitor Log Analytics ワークスペースがない場合は、ワークスペースを作成する前に、[Azure Monitor ログの設計ガイダンス](../azure-monitor/logs/design-logs-deployment.md)を確認してください。

### <a name="log-analytics-agent"></a>Log Analytics エージェント

Hybrid Runbook Worker ロールには、サポートされている Linux オペレーティング システム用の [Log Analytics エージェント](../azure-monitor/agents/log-analytics-agent.md)が必要です。 Azure の外部でホストされているサーバーまたはマシンの場合、[Azure Arc 対応サーバー](../azure-arc/servers/overview.md)を使用すれば Log Analytics エージェントをインストールできます。

>[!NOTE]
>Linux 用 Log Analytics エージェントをインストールした後は、`sudoers.d` フォルダーまたはその所有権のアクセス許可を変更しないでください。 sudo アクセス許可は **nxautomation** アカウントに必要です。これは Hybrid Runbook Worker を実行するユーザー コンテキストです。 アクセス許可は削除しないでください。 これを特定のフォルダーまたはコマンドに制限すると、破壊的変更が発生する可能性があります。
>

### <a name="supported-linux-operating-systems"></a>サポートされている Linux オペレーティング システム

Hybrid Runbook Worker 機能では、次のディストリビューションがサポートされています。 オペレーティング システムはすべて x64 と見なされます。 x86 はどのオペレーティング システムでもサポートされていません。

* Amazon Linux 2012.09 から 2015.09
* CentOS Linux 5、6、7、8
* Oracle Linux 5、6、7
* Red Hat Enterprise Linux Server 5、6、7、8
* Debian GNU/Linux 6、7、8
* Ubuntu 12.04 LTS、14.04 LTS、16.04 LTS、および 18.04 LTS
* SUSE Linux Enterprise Server 12 および 15 (SUSE ではバージョン 13 と 14 はなし)

> [!IMPORTANT]
> システムの Hybrid Runbook Worker ロールに依存する Update Management 機能を有効にする前に、[ここ](update-management/overview.md#supported-operating-systems)でサポート対象の配布を確認してください。

### <a name="minimum-requirements"></a>最小要件

Linux システムおよびユーザー Hybrid Runbook Worker の最小要件は次のようになります。

* 2 コア
* 4 GB の RAM
* ポート 443 (送信)

| **必須パッケージ** | **説明** | **最小バージョン**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C ライブラリ| 2.5-12 |
|Openssl| OpenSSL ライブラリ | 1.0 (TLS 1.1 と TLS 1.2 がサポートされます)|
|Curl | cURL Web クライアント | 7.15.5|
|Python-ctypes | Python 2.x または Python 3.x が必要 |
|PAM | Pluggable Authentication Module (プラグ可能な認証モジュール)|
| **オプション パッケージ** | **説明** | **最小バージョン**|
| PowerShell Core | PowerShell Runbook を実行するには、PowerShell Core をインストールする必要があります。 インストール方法については、「[Linux への PowerShell Core のインストール](/powershell/scripting/install/installing-powershell-core-on-linux)」をご覧ください。 | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Hybrid Runbook Worker グループへのマシンの追加

ご利用の Automation アカウントのいずれか 1 つの Hybrid Runbook Worker グループに worker マシンを追加できます。 Update Management によって管理されているシステム Hybrid Runbook Worker をホストしているマシンの場合は、Hybrid Runbook Worker グループに追加できます。 しかし、Update Management と Hybrid Runbook Worker グループ メンバーシップの両方に同じ Automation アカウントを使用する必要があります。

>[!NOTE]
>Azure Automation [Update Management](./update-management/overview.md) を使用すると、Update Management に対して有効になっている Azure または Azure 以外のマシンに、システム Hybrid Runbook Worker が自動的にインストールされます。 ただし、この worker は、Automation アカウント内の Hybrid Runbook Worker グループには登録されません。 これらのマシン上で Runbook を実行するには、それを Hybrid Runbook Worker グループに追加する必要があります。 それをグループに追加するには、「[Linux Hybrid Runbook Worker をインストールする](#install-a-linux-hybrid-runbook-worker)」セクションの手順 4 に従ってください。

## <a name="supported-linux-hardening"></a>Linux のセキュリティ強化のサポート

次はまだサポートされていません。

* CIS

## <a name="supported-runbook-types"></a>サポートされている Runbook の種類

Linux Hybrid Runbook Worker でサポートされている Azure Automation の Runbook の種類は制限されており、次の表ではそれについて説明します。

|Runbook の種類 | サポートされています |
|-------------|-----------|
|Python 3 (プレビュー)|はい (SUSE LES 15、RHEL 8、CentOS 8 の配布でのみ必要)|
|Python 2 |はい (Python 3<sup>1</sup> を必要としない配布用) |
|PowerShell |はい<sup>2</sup> |
|PowerShell ワークフロー |いいえ |
|グラフィカル |いいえ |
|グラフィカル PowerShell ワークフロー |いいえ |

<sup>1</sup> [サポートされている Linux オペレーティング システム](#supported-linux-operating-systems)を参照してください。

<sup>2</sup> PowerShell Runbook を使うには、Linux マシンに PowerShell Core がインストールされている必要があります。 インストール方法については、「[Linux への PowerShell Core のインストール](/powershell/scripting/install/installing-powershell-core-on-linux)」をご覧ください。

### <a name="network-configuration"></a>ネットワークの構成

Hybrid Runbook Worker のネットワーク要件については、[ネットワークの構成](automation-hybrid-runbook-worker.md#network-planning)に関するページを参照してください。

## <a name="install-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker をインストールする

Linux Hybrid Runbook Worker をインストールして構成するには、次の手順のようにします。

1. 管理者特権での PowerShell コマンドプロンプトで、または [Azure portal](https://portal.azure.com) の Cloud Shell で次のコマンドを実行することにより、Log Analytics ワークスペース内で Azure Automation ソリューションを有効にします。

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. ターゲット マシンに Log Analytics エージェントをデプロイします。

    * Azure VM の場合、[Linux 用仮想マシン拡張機能](../virtual-machines/extensions/oms-linux.md)を使用して、Linux 用 Log Analytics エージェントをインストールします。 この拡張機能では、Azure 仮想マシンに Log Analytics エージェントがインストールされ、仮想マシンが既存の Log Analytics ワークスペースに登録されます。 Azure Resource Manager テンプレート、Azure CLI、または Azure Policy を使用すれば、組み込みポリシーである [*Linux* または *Windows* VM 用の Log Analytics エージェントのデプロイ](../governance/policy/samples/built-in-policies.md#monitoring)を割り当てることができます。 エージェントがインストールされたら、Automation アカウントの Hybrid Runbook Worker グループにマシンを追加できます。

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

    > [!NOTE]
    > Hybrid Runbook Worker ロールをサポートするマシンの構成を Desired State Configuration (DSC) を使用して管理するには、マシンを DSC ノードとして追加する必要があります。

    > [!NOTE]
    > Linux Hybrid Worker のインストール中は、対応する sudo アクセス許可を持った [nxautomation アカウント](automation-runbook-execution.md#log-analytics-agent-for-linux)が存在していなければなりません。 そのアカウントが存在しない、または適切なアクセス許可がアカウントにない状態でワーカーをインストールしようとすると、インストールは失敗します。

3. エージェントがワークスペースに報告していることを確認します。

    Linux 用 Log Analytics エージェントにより、マシンが Azure Monitor Log Analytics ワークスペースに接続されます。 マシンにエージェントをインストールし、ワークスペースに接続すると、Hybrid Runbook Worker に必要なコンポーネントが自動的にダウンロードされます。

    エージェントが Log Analytics ワークスペースに正常に接続されたら、数分後に次のクエリを実行して、ワークスペースにハートビート データが送信されていることを確認できます。

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    検索結果には、マシンのハートビート レコードが表示されます。これにより、エージェントがサービスに接続され、レポートが送信されていることが示されます。 既定では、各エージェントからその割り当て済みのワークスペースにハートビート レコードが転送されます。

4. 次のコマンドを実行して、マシンを Hybrid Runbook Worker グループに追加します。このとき、`-w`、`-k`、`-g`、`-e` のパラメーターに値を指定します。

    パラメーター `-k` と `-e` に必要な情報は、Automation アカウントの **[キー]** ページから取得できます。 ページの左側にある **[アカウントの設定]** セクションで **[キー]** を選択します。

    ![キーの管理ページ](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * `-e` パラメーターには、 **[URL]** の値をコピーします。

    * `-k` パラメーターには、 **[プライマリ アクセス キー]** の値をコピーします。

    * `-g` パラメーターでは、新しい Linux Hybrid Runbook Worker を参加させる Hybrid Runbook Worker グループの名前を指定します。 Automation アカウントにこのグループが既に存在する場合は、現在のマシンがそれに追加されます。 このグループが存在しない場合は、その名前で作成されます。

    * `-w` パラメーターでは、Log Analytics ワークスペースの ID を指定します。

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. スクリプトが完了したら、デプロイを確認します。 Automation アカウントの **[Hybrid Runbook Worker のグループ]** ページの **[User hybrid runbook workers group]\(ユーザー Hybrid Runbook Worker グループ\)** タブの下に、新しいまたは既存のグループとメンバーの人数が表示されます。 既存のグループの場合は、メンバーの数が増分されます。 そのページの一覧からグループを選択できます。左側のメニューで **[ハイブリッド worker]** を選択します。 **[ハイブリッド worker]** ページでは、グループの各メンバーの一覧を確認することができます。

    > [!NOTE]
    > Azure VM 用の Linux で Log Analytics 仮想マシン拡張機能を使用している場合は、`autoUpgradeMinorVersion` を `false` に設定することをお勧めします。これは、バージョンを自動的にアップグレードすると、Hybrid Runbook Worker で問題が発生する場合があるからです。 拡張機能を手動でアップグレードする方法については、「[Azure CLI でのデプロイ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)」を参照してください。

## <a name="turn-off-signature-validation"></a>署名の検証をオフにする

既定では、Linux Hybrid Runbook Worker は、署名の検証を必要とします。 worker に対して署名されていない Runbook を実行すると、`Signature validation failed` エラーが表示されます。 署名の検証をオフにするには、次のコマンドを実行します。 2 番目のパラメーターは Log Analytics のワークスペース ID に置き換えます。

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>Hybrid Runbook Worker を削除します。

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