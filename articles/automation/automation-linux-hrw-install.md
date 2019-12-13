---
title: Azure Automation の Linux Hybrid Runbook Worker
description: この記事では、Azure Automation Hybrid Runbook Worker をインストールして、ローカル データ センターやクラウド環境にある Linux ベースのコンピューターで Runbook を実行できるようにする方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 01dcb30fca2819bc4cf85ea624de5735cc84801b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850824"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker を展開する

Azure Automation の Hybrid Runbook Worker 機能を使う、ロールをホスティングしているコンピューター上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 Linux Hybrid Runbook Worker は、昇格が必要なコマンドを実行するために昇格させることができる特殊なユーザーとして Runbook を実行します。 Runbook は Azure Automation で格納および管理された後、1 つ以上の指定されたコンピューターに配信されます。

この記事では、Linux コンピューターに Hybrid Runbook Worker をインストールする方法について説明します。

## <a name="supported-linux-operating-systems"></a>サポートされている Linux オペレーティング システム

Hybrid Runbook Worker 機能は、次のディストリビューションをサポートします。

* Amazon Linux 2012.09 ～ 2015.09 (x86/x64)
* CentOS Linux 5、6、および 7 (x86/x64)
* Oracle Linux 5、6、および 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6、および 7 (x86/x64)
* Debian GNU/Linux 6、7、および 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS、および 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 および 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker のインストール

Linux コンピューターに Hybrid Runbook Worker をインストールして構成するには、ロールを手動でインストールおよび構成するための非常に簡単なプロセスに従います。 **Automation Hybrid Worker** ソリューションを Azure Log Analytics ワークスペースで有効にする必要があります。その後、一連のコマンドを実行してコンピューターを worker として登録し、グループに追加します。

Linux Hybrid Runbook Worker の最小要件は次のようになります。

* 2 コア
* 4 GB の RAM
* ポート 443 (送信)

### <a name="package-requirements"></a>パッケージの要件

| **必須パッケージ** | **説明** | **最小バージョン**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C ライブラリ| 2.5-12 |
|Openssl| OpenSSL ライブラリ | 1.0 (TLS 1.1 と TLS 1.2 がサポートされます)|
|Curl | cURL Web クライアント | 7.15.5|
|Python-ctypes | Python 2.x が必要 |
|PAM | Pluggable Authentication Module (プラグ可能な認証モジュール)|
| **オプション パッケージ** | **説明** | **最小バージョン**|
| PowerShell Core | PowerShell の Runbook を実行するには、PowerShell をインストールする必要があります。インストール方法については、「[Linux への PowerShell Core のインストール](/powershell/scripting/install/installing-powershell-core-on-linux)」をご覧ください。  | 6.0.0 |

### <a name="installation"></a>インストール

先に進む前に、Automation アカウントがリンクされている Log Analytics ワークスペースを書き留めておきます。 また、Automation アカウントのプライマリ キーも書き留めます。 Azure portal から、Automation アカウントを選び、ワークスペース ID の**ワークスペース**、およびプライマリ キーの**キー**を選ぶことで、両方を検索できます。 Hybrid Runbook Worker に必要なポートとアドレスについては、「[ネットワークを構成する](automation-hybrid-runbook-worker.md#network-planning)」をご覧ください。

1. 次のいずれかの方法を使って、Azure において **Automation Hybrid Worker** ソリューションを有効にします。

   * [Azure Monitor ログ ソリューションのワークスペースへの追加](../log-analytics/log-analytics-add-solutions.md)に関するページの手順を使用して、**Automation Hybrid Worker** ソリューションをサブスクリプションに追加します。
   * 次のコマンドレットを実行します。

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. 次のコマンドを実行して、Log Analytics エージェント for Linux をインストールします。 \<WorkspaceID\> と \<WorkspaceKey\> は、お使いのワークスペースの適切な値に置き換えてください。

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. 次のコマンドを、 *-w*、 *-k*、 *-g*、および *-e* パラメーターの値を変更して実行します。 *-g* パラメーターを、新しい Linux Hybrid Runbook Worker を参加させる Hybrid Runbook Worker グループの名前の値に置き換えます。 この名前が Automation アカウントに存在しない場合は、その名前を持つ新しい Hybrid Runbook Worker グループが作成されます。

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. コマンドが完了すると、Azure portal の **[ハイブリッド Worker グループ]** ページに、新しいグループとメンバーの数が表示されます。 既存のグループの場合は、メンバーの数がインクリメントされます。 **[ハイブリッド worker グループ]** ページ上にあるリストからグループを選択し、 **[ハイブリッド worker]** タイルを選択できます。 **[ハイブリッド worker]** ページで、グループの各メンバーが一覧表示されます。

> [!NOTE]
> Azure VM 用の Linux で Azure Monitor 仮想マシン拡張機能を使用している場合は、`autoUpgradeMinorVersion` を false に設定することをお勧めします。これは、バージョンを自動的にアップグレードすると、Hybrid Runbook Worker で問題が発生する場合があるからです。 拡張機能を手動でアップグレードする方法については、「[Azure CLI deployment (Azure CLI のデプロイ)](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)」を参照してください。

## <a name="turning-off-signature-validation"></a>署名の検証をオフにする

既定では、Linux Hybrid Runbook Worker は、署名の検証を必要とします。 ワーカーに対して未署名の Runbook を実行した場合は、"署名の検証が失敗しました" というエラーが表示されます。 署名の検証をオフにするには、次のコマンドを実行します。 2 番目のパラメーターは Log Analytics ワークスペース ID に置き換えます。

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>サポートされている Runbook の種類

Linux Hybrid Runbook Worker は、Azure Automation での一連の完全な Runbook の種類をサポートしていません。

Linux Hybrid Worker では、次の Runbook の種類が機能します。

* Python 2
* PowerShell

  > [!NOTE]
  > PowerShell Runbook を使うには、Linux マシンに PowerShell Core がインストールされている必要があります。 インストール方法については、「[Linux への PowerShell Core のインストール](/powershell/scripting/install/installing-powershell-core-on-linux)」をご覧ください。

Linux Hybrid Worker では、次の Runbook の種類は機能しません。

* PowerShell ワークフロー
* グラフィカル
* グラフィカル PowerShell ワークフロー

## <a name="next-steps"></a>次の手順

* オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を学習するには、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」をご覧ください。
* Hybrid Runbook Worker を削除する方法については、「[Hybrid Runbook Worker の削除](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)」をご覧ください。
* Linux Hybrid Runbook Worker をトラブルシューティングする方法については、[Linux Hybrid Runbook Worker のトラブルシューティング](troubleshoot/hybrid-runbook-worker.md#linux)に関する記事を参照してください。
