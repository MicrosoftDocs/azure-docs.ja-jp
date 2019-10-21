---
title: トラブルシューティング - Azure Automation Hybrid Runbook Worker
description: この記事では、Azure Automation Hybrid Runbook Worker のトラブルシューティングについて説明します。
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 02/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 39cf6126f6212b6e83f1974dae7aaab0038e69c6
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240980"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Hybrid Runbook Worker のトラブルシューティング

この記事では、Hybrid Runbook Worker での問題のトラブルシューティングについて説明します。

## <a name="general"></a>全般

Hybrid Runbook Worker は、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、および状態の報告を行うためにエージェントに依存しています。 Windows では、このエージェントは Microsoft Monitoring Agent です。 Linux では OMS エージェント for Linux です。

### <a name="runbook-execution-fails"></a>シナリオ:Runbook の実行が失敗する

#### <a name="issue"></a>問題

Runbook の実行が失敗し、次のエラーを受け取ります。

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Runbook は、3 回実行を試みると、短時間中断されます。 Runbook の完了を妨げる可能性がある状況があります。 これが発生すると、関連するエラー メッセージに、理由を示す追加情報が含まれないことがあります。

#### <a name="cause"></a>原因

考えられる原因は次のとおりです。

* Runbook がローカル リソースで認証できない

* Hybrid Worker がプロキシまたはファイアウォールの内側にある

* Runbook がローカル リソースで認証できない

* Hybrid Runbook Worker の機能を実行するように構成されているコンピューターが、ハードウェアの最小要件を満たしていない。

#### <a name="resolution"></a>解決策

コンピューターがポート 443 で *.azure-automation.net に発信アクセスできることを確認します。

Hybrid Runbook Worker を実行するコンピューターは、この機能をホストするように構成する前に、ハードウェアの最小要件を満たしている必要があります。 Runbook とそれらが使用するバック グラウンド プロセスによってシステムが過剰に使用される場合があり、それが原因で Runbook ジョブの遅延またはタイムアウトが発生します。

Hybrid Runbook Worker の機能を実行するコンピューターがハードウェアの最小要件を満たしていることを確認します。 満たしている場合は、CPU とメモリの使用を監視して、Hybrid Runbook Worker プロセスのパフォーマンスと Windows の間の相関関係を調べます。 メモリまたは CPU に負荷がかかっている場合は、リソースのアップグレードが必要である可能性があります。 または、最小要件を満たす別のコンピューティング リソースを選択し、ワークロードがさらに多くのリソースを必要とすることを示したときに拡張できます。

**Microsoft-SMA** のイベント ログで " *Win32 Process Exited with code [4294967295]* " という説明の対応するイベントを確認します。 このエラーの原因は、Runbook で認証が構成されていないか、または Hybrid Worker グループの Run As 資格情報が指定されていません。 「[Runbook のアクセス許可](../automation-hrw-run-runbooks.md#runbook-permissions) 」を参照して、Runbook の認証を正しく構成してあることを確認します。

### <a name="no-cert-found"></a>シナリオ:Hybrid Runbook Worker の証明書ストア内に証明書が見つからない

#### <a name="issue"></a>問題

Hybrid Runbook Worker で実行される Runbook が次のエラー メッセージで失敗します。

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>原因

このエラーは、Hybrid Runbook Worker 上で実行される Runbook 内で[実行アカウント](../manage-runas-account.md)を使用しようとしたが、その実行アカウントの証明書が存在しない場合に発生します。 既定では、Hybrid Runbook Worker には、実行アカウントを正常に機能させるために必要なローカルの証明書資産はありません。

#### <a name="resolution"></a>解決策

Hybrid Runbook Worker が Azure VM の場合は、[Azure リソース用のマネージド ID](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) を代わりに使用できます。 このシナリオでは、実行アカウントの代わりに Azure VM のマネージド ID を使用して Azure リソースへの認証を行うことで、認証を簡素化できます。 Hybrid Runbook Worker がオンプレミスのコンピューターである場合は、実行アカウント証明書をコンピューターにインストールする必要があります。 証明書をインストールする方法については、[Export-RunAsCertificateToHybridWorker](../automation-hrw-run-runbooks.md#runas-script) Runbook を実行するための手順を参照してください。

## <a name="linux"></a>Linux

Linux Hybrid Runbook Worker は、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、および状態の報告を行うために OMS エージェント for Linux に依存しています。 ワーカーの登録に失敗した場合に考えられるエラーの原因を次に示します。

### <a name="oms-agent-not-running"></a>シナリオ:OMS エージェント for Linux が実行されていない

#### <a name="issue"></a>問題

OMS エージェント for Linux が実行されていない

#### <a name="cause"></a>原因

OMS エージェント for Linux が実行されていない場合、Linux Hybrid Runbook Worker は Azure Automation と通信できません。 さまざまな理由で、エージェントが実行されていない可能性があります。

#### <a name="resolution"></a>解決策

 次のコマンドを入力して、このエージェントが実行されていることを確認します: `ps -ef | grep python`。 次のように、**nxautomation** ユーザー アカウントの python プロセスが出力されます。 Update Management または Azure Automation ソリューションが有効でない場合、次のどのプロセスも実行されません。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

次の一覧は、Linux Hybrid Runbook Worker のために開始されるプロセスを示します。 これらはすべて `/var/opt/microsoft/omsagent/state/automationworker/` ディレクトリにあります。


* **oms.conf** - この値は worker マネージャー プロセスです。 これは、DSC から直接開始されます。

* **worker.conf** - このプロセスは Auto Registered Hybrid (自動登録ハイブリッド) ワーカー プロセスで、ワーカー マネージャーによって開始されます。 このプロセスは Update Management によって使用され、ユーザーに透過的です。 Update Management ソリューションがコンピューター上で有効でない場合、このプロセスは存在しません。

* **diy/worker.conf** - このプロセスは DIY ハイブリッド ワーカー プロセスです。 DIY ハイブリッド ワーカー プロセスは、Hybrid Runbook Worker でユーザーの Runbook を実行するために使用されます。 自動登録ハイブリッド ワーカー プロセスとの違いは、異なる構成を使用するキーの詳細だけです。 Azure Automation ソリューションが無効な場合、このプロセスは存在せず、DIY Linux ハイブリッド worker は登録されません。

OMS エージェント for Linux が実行されていない場合、次のコマンドを実行してサービスを開始します。`sudo /opt/microsoft/omsagent/bin/service_control restart`

### <a name="class-does-not-exist"></a>シナリオ:指定されたクラスが存在しない

次のようなエラーが表示された場合:**指定されたクラスが存在しません..** `/var/opt/microsoft/omsconfig/omsconfig.log` で表示された場合、OMS エージェント for Linux を更新する必要があります。 OMS Agent を再インストールするには、次のコマンドを実行します。

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows Hybrid Runbook Worker は、Automation アカウントと通信して worker の登録、Runbook ジョブの受信、および状態の報告を行うために Microsoft Monitoring Agent に依存しています。 ワーカーの登録に失敗した場合に考えられるエラーの原因を次に示します。

### <a name="mma-not-running"></a>シナリオ:Microsoft Monitoring Agent が実行されていません。

#### <a name="issue"></a>問題

`healthservice` サービスが Hybrid Runbook Worker マシンで実行されていません。

#### <a name="cause"></a>原因

Microsoft Monitoring Agent の Windows サービスが実行されていない場合、この状態により Hybrid Runbook Worker は Azure Automation と通信できません。

#### <a name="resolution"></a>解決策

PowerShell で次のコマンドを入力して、このエージェントが実行されていることを確認します: `Get-Service healthservice`。 サービスが停止している場合は、PowerShell で次のコマンドを入力してサービスを開始します: `Start-Service healthservice`。

### <a name="event-4502"></a>Operations Manager ログのイベント 4502

#### <a name="issue"></a>問題

**アプリケーションとサービス ログ\Operations Manager** のイベント ログで、イベント 4502 と、**Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** および次の説明を含む EventMessage が表示されます:*サービス \<wsid\>.oms.opinsights.azure.com によって提示された証明書は、Microsoft サービスで使用する証明機関によって発行されたものではありません。TLS/SSL 通信を遮断するプロキシが実行されているかどうかをネットワーク管理者に問い合わせてください。記事 KB3126513 に、接続の問題に関するトラブルシューティング情報が記載されています。* "

#### <a name="cause"></a>原因

この問題は、プロキシまたはネットワークのファイアウォールが Microsoft Azure への通信をブロックしていることが原因で発生する可能性があります。 コンピューターがポート 443 で *.azure-automation.net に発信アクセスできることを確認します。

#### <a name="resolution"></a>解決策

ログは、各ハイブリッド worker の C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes にローカルに格納されます。 Azure Automation へのロールのオンボードに影響する接続などの問題や、通常の動作中に発生した問題を示唆する警告やエラー イベントが、**アプリケーションとサービス ログ\Microsoft-SMA\Operations** と**アプリケーションとサービス ログ\Operations Manager** イベント ログにあるかどうかをチェックできます。

[Runbook の出力とメッセージ](../automation-runbook-output-and-messages.md) は、クラウドで実行される Runbook ジョブと同様に、Hybrid Worker から Azure Automation に送られます。 他の Runbook と同じ方法で、Verbose および Progress ストリームも有効にできます。

### <a name="corrupt-cache"></a> Hybrid Runbook Worker の報告がない

#### <a name="issue"></a>問題

Hybrid Runbook Worker マシンは動作していますが、ワークスペース内のマシンのハートビート データが表示されません。

次のクエリの例は、ワークスペース内のマシンとそれらの最後のハートビートを示しています:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>原因

この問題は、Hybrid Runbook Worker 上のキャッシュの破損が原因である可能性があります。

#### <a name="resolution"></a>解決策

この問題を解決するには、Hybrid Runbook Worker にサインインし、次のスクリプトを実行します。 このスクリプトは Microsoft Monitoring Agent を停止し、そのキャッシュを削除し、サービスを再起動します。 この操作により、Hybrid Runbook Worker の構成が Azure Automation から強制的に再ダウンロードされます。

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>シナリオ:Hybrid Runbook Worker を追加できない

#### <a name="issue"></a>問題

`Add-HybridRunbookWorker` コマンドレットを使用して Hybrid Runbook Worker を追加しようとすると、次のメッセージが表示されます。

```error
Machine is already registered
```

#### <a name="cause"></a>原因

これは、マシンが既に別の Automation アカウントに登録されている場合や、マシンから Hybrid Runbook Worker を削除した後にそれを再度追加しようとした場合に発生する可能性があります。

#### <a name="resolution"></a>解決策

この問題を解決するには、次のレジストリ キーを削除し、`HealthService` を再起動した後、もう一度 `Add-HybridRunbookWorker` コマンドレットを試してください。

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。

