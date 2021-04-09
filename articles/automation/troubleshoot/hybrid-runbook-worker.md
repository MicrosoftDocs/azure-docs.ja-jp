---
title: Azure Automation Hybrid Runbook Worker の問題のトラブルシューティング
description: この記事では、Azure Automation Hybrid Runbook Worker で発生する問題のトラブルシューティングと解決方法について説明します。
services: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.openlocfilehash: 15a18cbfc3a80bbfea0b92e5b616104dc0f593af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100580994"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>Hybrid Runbook Worker の問題のトラブルシューティング

この記事では、Azure Automation Hybrid Runbook Worker に関する問題のトラブルシューティングと解決方法について説明します。 一般情報については、「[Hybrid Runbook Worker overview (Hybrid Runbook Worker の概要)](../automation-hybrid-runbook-worker.md)」をご覧ください。

## <a name="general"></a>全般

Hybrid Runbook Worker はエージェントに依存して Azure Automation アカウントと通信し、ワーカーの登録、Runbook ジョブの受信、および状態の報告を行います。 Windows の場合、このエージェントは Windows 用 Log Analytics エージェントです。 Linux の場合は、Linux 用 Log Analytics エージェントです。

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>シナリオ:Runbook の実行が失敗する

#### <a name="issue"></a>問題

Runbook の実行が失敗し、次のエラー メッセージを受け取ります。

`The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times.`

Runbook は 3 回実行を試みると、短時間中断されます。 Runbook の完了を妨げる可能性がある状況が存在します。 関連するエラー メッセージに追加情報が含まれていない場合があります。

#### <a name="cause"></a>原因

以下の原因が考えられます。

* Runbook がローカル リソースで認証できない。
* Hybrid Worker がプロキシまたはファイアウォールの内側にある。
* Hybrid Runbook Worker を実行するように構成されているコンピューターが、ハードウェアの最小要件を満たしていない。

#### <a name="resolution"></a>解決策

コンピューターがポート 443 で **\*.azure-automation.net** に発信アクセスできることを確認します。

Hybrid Runbook Worker を実行するコンピューターは、この機能をホストするようにワーカーを構成する前に、ハードウェアの最小要件を満たしている必要があります。 Runbook とそれらが使用するバックグラウンド プロセスによってシステムが過剰に使用され、それが原因で Runbook ジョブの遅延やタイムアウトが発生する可能性があります。

Hybrid Runbook Worker の機能を実行するコンピューターがハードウェアの最小要件を満たしていることを確認します。 満たしている場合は、CPU とメモリの使用を監視して、Hybrid Runbook Worker プロセスのパフォーマンスと Windows の間の相関関係を調べます。 メモリまたは CPU の負荷は、リソースのアップグレードが必要であることを示している可能性があります。 最小要件を満たす別のコンピューティング リソースを選択し、ワークロードの需要によって増加が必要であることが示された時点でスケーリングすることもできます。

**Microsoft-SMA** のイベント ログで、`Win32 Process Exited with code [4294967295]` という説明のある、対応するイベントを調べます。 このエラーの原因は、Runbook で認証が構成されていないか、または Hybrid Runbook Worker グループの実行資格情報が指定されていないことです。 「[Hybrid Runbook Worker での Runbook の実行](../automation-hrw-run-runbooks.md)」で Runbook のアクセス許可を調べ直して、Runbook の認証を正しく構成したことを確認します。

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>シナリオ:Hybrid Runbook Worker のイベント 15011

#### <a name="issue"></a>問題

Hybrid Runbook Worker で、クエリ結果が有効でないことを示すイベント 15011 が受信されます。 ワーカーで [SignalR サーバー](/aspnet/core/signalr/introduction)との接続を開こうとするときに、次のエラーが表示されます。

`[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
`

#### <a name="cause"></a>原因

Hybrid Runbook Worker が、Update Management など、機能の自動デプロイ用に正しく構成されていません。 このデプロイには、VM を Log Analytics ワークスペースに接続する部分が含まれています。 PowerShell スクリプトにより、指定された名前のサブスクリプション内でワークスペースが検索されます。 このケースでは、Log Analytics ワークスペースは別のサブスクリプションにあります。 スクリプトでワークスペースが見つからず、作成が試みられますが、その名前は既に使用されています。 結果として、デプロイが失敗します。

#### <a name="resolution"></a>解像度

この問題を解決するには、2 つの方法があります。

* 別のサブスクリプション内で Log Analytics ワークスペースを検索するよう、PowerShell スクリプトを変更します。 今後、多くの Hybrid Runbook Worker マシンをデプロイする予定である場合は、これが適切な解決策です。

* Orchestrator サンドボックスで実行するようにワーカー マシンを手動で構成します。 次に、Azure Automation アカウントで作成された Runbook をワーカーで実行して、機能をテストします。

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>シナリオ:ハイブリッド worker グループから Windows Azure VM が自動的に削除される

#### <a name="issue"></a>問題

ワーカー マシンが長時間オフになっている場合、Hybrid Runbook Worker または VM が表示されません。

#### <a name="cause"></a>原因

Hybrid Runbook Worker マシンで、Azure Automation に対する ping が 30 日以上行われていません。 その結果、Automation によって Hybrid Runbook Worker グループまたはシステム ワーカー グループが削除されました。 

#### <a name="resolution"></a>解像度

ワーカー マシンを起動し、Azure Automation に登録します。 Runbook 環境をインストールして Azure Automation に接続する方法の手順については、「[Windows Hybrid Runbook Worker をデプロイする](../automation-windows-hrw-install.md)」を参照してください。

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>シナリオ:Hybrid Runbook Worker の証明書ストアで証明書が見つからない

#### <a name="issue"></a>問題

Hybrid Runbook Worker で実行される Runbook が次のエラー メッセージで失敗します。

`Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000`  
`At line:3 char:1`  
`+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...`  
`+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~`  
`    + CategoryInfo          : CloseError: (:) [Connect-AzAccount],ArgumentException`  
`    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand`

#### <a name="cause"></a>原因

このエラーは、Hybrid Runbook Worker 上で実行される Runbook 内で[実行アカウント](../automation-security-overview.md#run-as-accounts)を使用しようとしたが、その実行アカウントの証明書が存在しない場合に発生します。 Hybrid Runbook Workers には、既定では証明書資産はローカルにありません。 実行アカウントでは、この資産が適切に動作していることが要求されます。

#### <a name="resolution"></a>解像度

お使いの Hybrid Runbook Worker が Azure VM である場合は、代わりに、[マネージド ID による Runbook 認証](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities)を使用できます。 このシナリオでは、実行アカウントの代わりに Azure VM のマネージド ID を使用して Azure リソースへの認証を可能にすることで、認証を簡素化します。 Hybrid Runbook Worker がオンプレミスのコンピューターである場合は、実行アカウント証明書をコンピューターにインストールする必要があります。 証明書をインストールする方法については、「[Hybrid Runbook Worker での Runbook の実行](../automation-hrw-run-runbooks.md)」で、PowerShell Runbook の **Export-RunAsCertificateToHybridWorker** を実行するための手順を参照してください。

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>シナリオ:Hybrid Runbook Worker の登録中にエラー 403 が発生した

#### <a name="issue"></a>問題

ワーカーの初期登録フェーズが失敗し、次のエラー (403) が表示されます。

`Forbidden: You don't have permission to access / on this server.`

#### <a name="cause"></a>原因

以下の問題が原因となっている可能性があります。

* エージェントの設定で、ワークスペース ID かワークスペース キー (プライマリ) に入力ミスがあります。 
* Hybrid Runbook Worker で構成をダウンロードできず、それがアカウント リンク エラーの原因となっています。 Azure では、マシンで機能を有効にしたときに、Log Analytics ワークスペースと Automation アカウントをリンクするために特定のリージョンのみがサポートされます。 コンピューターで、誤った日付または時刻が設定されている可能性もあります。 時刻が現在の時刻の 15 分後または前である場合、機能のデプロイは失敗します。

#### <a name="resolution"></a>解像度

##### <a name="mistyped-workspace-id-or-key"></a>ワークスペース ID またはキーの入力ミス
エージェントのワークスペース ID またはワークスペース キーに入力ミスがあったかどうかを確認するには、Windows エージェントの場合は [Windows エージェントでのワークスペースの追加または削除](../../azure-monitor/agents/agent-manage.md#windows-agent)に関する記事を、Linux エージェントの場合は [Linux エージェントでのワークスペースの追加または削除](../../azure-monitor/agents/agent-manage.md#linux-agent)に関する記事を参照してください。 Azure portal から完全な文字列を選択し、注意深くコピーと貼り付けを行うようにします。

##### <a name="configuration-not-downloaded"></a>構成がダウンロードされていない

Log Analytics ワークスペースと Automation アカウントは、リンクされたリージョン内にある必要があります。 サポートされているリージョンの一覧については、[Azure Automation と Log Analytics ワークスペースのマッピング](../how-to/region-mappings.md)に関する記事を参照してください。

コンピューターの日付やタイム ゾーンの更新が必要な場合もあります。 カスタムの時間範囲を選択する場合は、その範囲が UTC であることを確認してください。これは、ローカル タイムゾーンとは異なる場合があります。

### <a name="scenario-set-azstorageblobcontent-fails-on-a-hybrid-runbook-worker"></a><a name="set-azstorageblobcontent-execution-fails"></a>シナリオ:Hybrid Runbook Worker で Set-AzStorageBlobContent が失敗する 

#### <a name="issue"></a>問題

Runbook が `Set-AzStorageBlobContent` の実行を試みたときに失敗し、次のエラーメッセージが表示されます。

`Set-AzStorageBlobContent : Failed to open file xxxxxxxxxxxxxxxx: Illegal characters in path`

#### <a name="cause"></a>原因

 このエラーは、UNC パスを追加する `[System.IO.Path]::GetFullPath()` の呼び出しの長いファイル名の動作が原因で発生します。

#### <a name="resolution"></a>解決方法

回避策として、次の内容を含む `OrchestratorSandbox.exe.config` という名前の構成ファイルを作成できます。

```azurecli
<configuration>
  <runtime>
    <AppContextSwitchOverrides value="Switch.System.IO.UseLegacyPathHandling=false" />
  </runtime>
</configuration>
```

このファイルを実行可能ファイル `OrchestratorSandbox.exe` と同じフォルダーに配置します。 たとえば、次のように入力します。

`%ProgramFiles%\Microsoft Monitoring Agent\Agent\AzureAutomation\7.3.702.0\HybridAgent`

>[!Note]
> エージェントをアップグレードすると、この構成ファイルは削除されるため、再作成する必要があります。

## <a name="linux"></a>Linux

Linux Hybrid Runbook Worker は、[Linux 用 Log Analytics エージェント](../../azure-monitor/agents/log-analytics-agent.md) を使用することにより、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、状態の報告を行います。 ワーカーの登録に失敗した場合に考えられるエラーの原因を次に示します。

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>シナリオ:Linux Hybrid Runbook Worker で、Runbook に署名するときにパスワードの入力が求められる

#### <a name="issue"></a>問題

Linux Hybrid Runbook Worker で `sudo` コマンドを実行すると、パスワードの入力を求める想定外のプロンプトが表示されます。

#### <a name="cause"></a>原因

Linux 用 Log Analytics エージェントの **nxautomationuser** アカウントが、**sudoers** ファイルで正しく構成されていません。 Hybrid Runbook Worker では、Linux Runbook Worker で Runbook に署名できるように、アカウントのアクセス許可やその他のデータが適切に構成されている必要があります。

#### <a name="resolution"></a>解像度

* マシン上で、Hybrid Runbook Worker に GnuPG (GPG) の実行可能ファイルがあることを確認します。

* **sudoers** ファイルで **nxautomationuser** アカウントの構成を確認します。 「[Hybrid Runbook Worker での Runbook の実行](../automation-hrw-run-runbooks.md)」を参照してください

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>シナリオ:Linux 用 Log Analytics エージェントが実行されていない

#### <a name="issue"></a>問題

Linux 用 Log Analytics エージェントが実行されていません。

#### <a name="cause"></a>原因

エージェントが実行されていない場合、Linux Hybrid Runbook Worker は Azure Automation と通信できません。 さまざまな理由で、エージェントが実行されていない可能性があります。

#### <a name="resolution"></a>解像度

 `ps -ef | grep python` コマンドを入力して、このエージェントが実行されていることを確認します。 次のような出力が表示されます。 Python では、**nxautomation** ユーザー アカウントを使用して処理が行われます。 Azure Automation 機能が有効になっていない場合、次のどのプロセスも実行されていません。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

次の一覧は、Linux Hybrid Runbook Worker のために開始されるプロセスを示します。 これらはすべて /var/opt/microsoft/omsagent/state/automationworker/ ディレクトリに配置されています。

* **oms.conf**:worker マネージャー プロセスです。 これは、DSC から直接開始されます。
* **worker.conf**:自動登録ハイブリッド worker プロセスです。 worker マネージャーによって開始されます。 このプロセスは Update Management によって使用され、ユーザーに透過的です。 マシンで Update Management が有効になっていない場合、このプロセスは存在しません。
* **diy/worker.conf**:DIY ハイブリッド worker プロセスです。 DIY ハイブリッド ワーカー プロセスは、Hybrid Runbook Worker でユーザーの Runbook を実行するために使用されます。 キーの詳細での自動登録ハイブリッド ワーカー プロセスとの唯一の違いは、異なる構成を使用していることです。 Azure Automation が無効であり、DIY Linux ハイブリッド worker が登録されていない場合、このプロセスは存在しません。

エージェントが実行されていない場合、次のコマンドを実行してサービスを開始します。`sudo /opt/microsoft/omsagent/bin/service_control restart`

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>シナリオ:指定したクラスが存在しない

**/var/opt/microsoft/omsconfig/omsconfig.log** 内エラー メッセージ `The specified class does not exist..` がある場合は、Linux 用の Log Analytics エージェントを更新する必要があります。 次のコマンドを実行してエージェントを再インストールしてください。

```Bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows Hybrid Runbook Worker は、[Windows 用 Log Analytics エージェント](../../azure-monitor/agents/log-analytics-agent.md) を使用することにより、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、状態の報告を行います。 このセクションには、ワーカーの登録に失敗した場合の考えられる理由をいくつか示しています。

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>シナリオ:Windows 用 Log Analytics エージェントが実行されていない

#### <a name="issue"></a>問題

`healthservice` が Hybrid Runbook Worker マシンで実行されていません。

#### <a name="cause"></a>原因

Windows サービス用の Log Analytics が実行されていない場合、Hybrid Runbook Worker は Azure Automation と通信できません。

#### <a name="resolution"></a>解像度

PowerShell で次のコマンドを入力して、このエージェントが実行されていることを確認します: `Get-Service healthservice`。 サービスが停止している場合は、PowerShell で次のコマンドを入力してサービスを開始します: `Start-Service healthservice`。

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>シナリオ:Operations Manager ログのイベント 4502

#### <a name="issue"></a>問題

**[アプリケーションとサービス ログ] の [Operations Manager]** イベント ログに、イベント 4502 と、`Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` を含むイベント メッセージがあります。また、次の説明も含まれています。<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>原因

この問題は、プロキシまたはネットワークのファイアウォールが Microsoft Azure への通信をブロックしていることが原因で発生する可能性があります。 コンピューターがポート 443 で **\*.azure-automation.net** に発信アクセスできることを確認します。

#### <a name="resolution"></a>解決策

ログは、各ハイブリッド worker の C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes にローカルに格納されます。 警告やエラー イベントが、**アプリケーションとサービス ログ\Microsoft-SMA\Operations** と **アプリケーションとサービス ログ\Operations Manager** イベント ログにあるかどうかを確認できます。 これらのログは、Azure Automation に対するロールの有効化に影響する接続や他の種類の問題、または通常の操作中に発生した問題を示しています。 Log Analytics エージェントに関する問題のトラブルシューティングの詳細については、[Log Analytics Windows エージェントの問題のトラブルシューティング](../../azure-monitor/agents/agent-windows-troubleshoot.md)に関するページを参照してください。

ハイブリッド worker により、クラウドで実行されている Runbook ジョブが出力とメッセージを送信するのと同じ方法で、[Runbook の出力とメッセージ](../automation-runbook-output-and-messages.md)が Azure Automation に送信されます。 詳細ストリームと進行状況ストリームは、Runbook の場合と同じように有効にすることができます。

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-microsoft-365-through-proxy"></a>シナリオ:Orchestrator.Sandbox.exe をプロキシ経由で Microsoft 365 に接続できない

#### <a name="issue"></a>問題

Windows Hybrid Runbook Worker で実行されるスクリプトを、想定どおりに Orchestrator サンドボックスの Microsoft 365 に接続できません。 スクリプトでは、接続に [Connect-MsolService](/powershell/module/msonline/connect-msolservice) を使用しています。 

**Orchestrator.Sandbox.exe.config** を調整してプロキシとバイパス リストを設定しても、サンドボックスに正常に接続されません。 プロキシとバイパス リストの設定が同じ **Powershell_ise.exe.config** ファイルは、想定したとおり機能しているようです。 Service Management Automation (SMA) ログと PowerShell ログでは、プロキシに関する情報が提供されません。

#### <a name="cause"></a>原因

サーバー上の Active Directory フェデレーション サービス (AD FS) への接続でプロキシをバイパスすることはできません。 PowerShell サンドボックスはログインしたユーザーとして実行されることに注意してください。 ただし、Orchestrator サンドボックスは大幅にカスタマイズされており、**Orchestrator.Sandbox.exe.config** ファイルの設定は無視されることがあります。 それにはマシンまたは Log Analytics エージェントのプロキシ設定を処理するための特殊なコードが含まれていますが、それら以外のカスタム プロキシ設定は処理されません。 

#### <a name="resolution"></a>解像度

PowerShell コマンドレットのスクリプトを、MSOnline モジュールではなく Azure Active Directory モジュールを使用するように移行することで、Orchestrator サンドボックスの問題を解決できます。 詳細については、「[Orchestrator から Azure Automation (ベータ版) へ移行する](../automation-orchestrator-migration.md)」を参照してください。

引き続き MSOnline モジュールのコマンドレットを使用する場合は、[Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command) を使用するようにスクリプトを変更します。 `ComputerName` パラメーターと `Credential` パラメーターの値を指定します。 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

このコード変更によって、指定された資格情報のコンテキストで、まったく新しい PowerShell セッションが開始されます。 これで、アクティブ ユーザーを認証しているプロキシ サーバー経由でトラフィックを送信できるはずです。

>[!NOTE]
>この解決策を使用すると、サンドボックス構成ファイルを操作する必要がなくなります。 スクリプトを使用して構成ファイルを正常に動作させることができた場合でも、Hybrid Runbook Worker エージェントが更新されるたびにそのファイルは消去されます。

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>シナリオ:Hybrid Runbook Worker の報告がない

#### <a name="issue"></a>問題

Hybrid Runbook Worker マシンは実行されていますが、ワークスペース内のマシンのハートビート データが表示されません。

次のクエリの例は、ワークスペース内のマシンとそれらの最後のハートビートを示しています:

```kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>原因

この問題は、Hybrid Runbook Worker 上のキャッシュの破損が原因である可能性があります。

#### <a name="resolution"></a>解像度

この問題を解決するには、Hybrid Runbook Worker にサインインし、次のスクリプトを実行します。 このスクリプトは、Windows 用 Log Analytics エージェントを停止し、そのキャッシュを削除して、サービスを再起動します。 この操作により、Hybrid Runbook Worker の構成が Azure Automation から強制的に再ダウンロードされます。

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-windows-hybrid-runbook-worker"></a><a name="already-registered"></a>シナリオ:Windows Hybrid Runbook Worker を追加できない

#### <a name="issue"></a>問題

`Add-HybridRunbookWorker` コマンドレットを使用して Hybrid Runbook Worker の追加を試みると、次のメッセージが表示されます。

`Machine is already registered`

#### <a name="cause"></a>原因

この問題は、マシンが既に別の Automation アカウントに登録されている場合や、マシンから Hybrid Runbook Worker を削除した後にそれを再度追加しようとした場合に発生する可能性があります。

#### <a name="resolution"></a>解像度

この問題を解決するには、次のレジストリ キーを削除し、`HealthService` を再起動して、もう一度 `Add-HybridRunbookWorker` コマンドレットを試します。

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

### <a name="scenario-you-cant-add-a-linux-hybrid-runbook-worker"></a><a name="already-registered"></a>シナリオ:Linux Hybrid Runbook Worker を追加できない

#### <a name="issue"></a>問題

`sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` Python スクリプトを使用して Hybrid Runbook Worker の追加を試みると、次のメッセージが表示されます。

`Unable to register, an existing worker was found. Please deregister any existing worker and try again.`

さらに、`sudo python /opt/microsoft/omsconfig/.../onboarding.py --deregister` Python スクリプトを使用して Hybrid Runbook Worker の登録を解除しようとした場合:

`Failed to deregister worker. [response_status=404]`

#### <a name="cause"></a>原因

この問題は、マシンが既に別の Automation アカウントに登録されている場合、Azure ハイブリッド Worker グループが削除されている場合、またはマシンから Hybrid Runbook Worker を削除した後で再度追加しようとした場合に発生する可能性があります。

#### <a name="resolution"></a>解像度

この問題を解決するには、次の手順を実行します。

1. `sudo sh onboard_agent.sh --purge` エージェントを削除します。

1. これらのコマンドを実行します。

   ```
   sudo mv -f /home/nxautomation/state/worker.conf /home/nxautomation/state/worker.conf_old
   sudo mv -f /home/nxautomation/state/worker_diy.crt /home/nxautomation/state/worker_diy.crt_old
   sudo mv -f /home/nxautomation/state/worker_diy.key /home/nxautomation/state/worker_diy.key_old
   ```

1. `sudo sh onboard_agent.sh -w <workspace id> -s <workspace key> -d opinsights.azure.com` エージェントを再びオンボードします。

1. `/opt/microsoft/omsconfig/modules/nxOMSAutomationWorker` フォルダーが設定されるのを待ちます。

1. `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` Python スクリプトをもう一度試してください。

## <a name="next-steps"></a>次のステップ

該当する問題がここにない場合、または問題を解決できない場合は、追加のサポートを受けるために、次のいずれかのチャネルをお試しください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure サポートにより、Azure コミュニティの回答、サポート、エキスパートと結び付けられます。
* Azure サポート インシデントを送信する。 [Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートを受ける]** を選択します。
