---
title: Update Management のエラーをトラブルシューティングする
description: Update Management の問題をトラブルシューティングする方法を説明します。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: a42b05239ae1ddf8909e288486694bf57595b195
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849243"
---
# <a name="troubleshooting-issues-with-update-management"></a>Update Management の問題をトラブルシューティングする

この記事では、Update Management を使用する際に発生する可能性がある問題の解決方法について説明します。

根本的な問題を特定するためのハイブリッド worker エージェント用のエージェント トラブルシューティング ツールがあります。 このトラブルシューティング ツールの詳細については、[更新エージェントの問題のトラブルシューティング](update-agent-issues.md)に関する記事を参照してください。 その他のすべての問題については、次のトラブルシューティング ガイダンスを使用してください。

仮想マシン (VM) にソリューションをオンボードしようとしているときに問題が発生した場合は、ローカル コンピューター上の **[アプリケーションとサービス ログ]** にある **Operations Manager** ログで、イベント ID 4502 のイベントと **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** を含むイベント詳細を確認してください。

次のセクションでは、具体的なエラー メッセージと、考えられる個別の解決策を示します。 他のオンボードの問題については、[ソリューションをオンボードする際のトラブルシューティング](onboarding.md)に関する記事を参照してください。

## <a name="nologs"></a>シナリオ:Update Management のポータルにマシンが表示されない

### <a name="issue"></a>問題

次のような現象が発生します。

* VM の Update Management ビューで自分のマシンが **[構成されていません]** と表示される。

* Azure Automation アカウントの Update Management ビューに自分のマシンがない。

* **[コンプライアンス]** で **[評価が行われていません]** と表示されているマシンがある。 ただし、Hybrid Runbook Worker の Azure Monitor ログにはハートビート データが表示されているが、Update Management には表示されない。

### <a name="cause"></a>原因

この問題は、ローカル構成の問題や正しく設定されなかったスコープ構成が原因で発生する可能性があります。

Hybrid Runbook Worker の再登録と再インストールが必要になる場合があります。

自分のワークスペースで定義したクォータに達していて、それ以上のデータの格納が妨げられている可能性があります。

### <a name="resolution"></a>解決策

* OS に応じて、[Windows](update-agent-issues.md#troubleshoot-offline) 用または [Linux](update-agent-issues-linux.md#troubleshoot-offline) 用のトラブルシューティング ツールを実行します。

* マシンが適切なワークスペースに対してレポートしていることを確認します。 この側面を確認する方法については、「[Log Analytics へのエージェント接続を確認する](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)」を参照してください。 このワークスペースが自分の Azure Automation アカウントにリンクされていることも確認します。 確認するには、自分の Automation アカウントに移動して、 **[関連リソース]** の **[リンクされたワークスペース]** を選択します。

* Log Analytics ワークスペースにマシンが表示されていることを確認します。 自分の Automation アカウントにリンクされた Log Analytics ワークスペースで、次のクエリを実行します。

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  クエリの結果に自分のマシンが表示されない場合は、最近チェックインされていません。つまり、ローカル構成に問題がある可能性があり、[エージェントを再インストール](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)する必要があります。 クエリの結果に自分のマシンが表示される場合は、この一覧の次の箇条書きで指定されているスコープ構成を確認する必要があります。

* スコープ構成に問題がないかどうかを確認します。 [スコープ構成](../automation-onboard-solutions-from-automation-account.md#scope-configuration)では、どのマシンがソリューション用に構成されるかを決定します。 自分のワークスペースで表示されているマシンが **Update Management** ポータルに表示されていない場合、スコープ構成を設定してそのマシンをターゲットにする必要があります。 これを行う方法については、「[ワークスペースでのマシンの配布準備](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)」を参照してください。

* 自分のワークスペースで、次のクエリを実行します。

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  結果に `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` と表示される場合、自分のワークスペースで定義したクォータに達していて、データの保存が停止されています。 自分のワークスペースで **[使用量と推定コスト]**  >  **[データ ボリュームの管理]** の順に移動して、クォータを確認するか、削除します。

* これらの手順で問題が解決しない場合は、「[Windows Hybrid Runbook Worker をデプロイする](../automation-windows-hrw-install.md)」の手順に従って、Windows にハイブリッド worker を再インストールしてください。 または、Linux の場合は、[Linux Hybrid Runbook Worker をデプロイします](../automation-linux-hrw-install.md)。

## <a name="rp-register"></a>シナリオ:サブスクリプションで Automation リソース プロバイダーを登録できない

### <a name="issue"></a>問題

Automation アカウントでソリューションを使用するときに、次のエラーが発生します。

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>原因

Automation リソース プロバイダーがサブスクリプションに登録されていません。

### <a name="resolution"></a>解決策

Automation リソース プロバイダーを登録するには、Azure portal で次の手順に従います。

1. ポータルの下部にある Azure サービスの一覧で **[すべてのサービス]** を選択し、[全般] サービス グループの **[サブスクリプション]** を選択します。
2. サブスクリプションを選択します。
3. **[設定]** で、 **[リソース プロバイダー]** を選択します。
4. リソース プロバイダーの一覧で、**Microsoft.Automation** リソース プロバイダーが登録されていることを確認します。
5. 一覧に表示されていない場合は、「[リソース プロバイダーの登録エラーの解決](/azure/azure-resource-manager/resource-manager-register-provider-errors)」の手順に従って、**Microsoft. Automation** プロバイダーを登録します。

## <a name="components-enabled-not-working"></a>シナリオ:Update Management ソリューションのコンポーネントは有効であり、現在この仮想マシンを構成中である

### <a name="issue"></a>問題

オンボードから 15 分経過しても、仮想マシンに関する次のメッセージが引き続き表示される。

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>原因

このエラーは、次の理由で発生することがあります。

- Automation アカウントとの通信がブロックされています。
- オンボードしている VM の複製元が、Microsoft Monitoring Agent (MMA) がインストールされた状態で sysprep されなかった複製マシンであった可能性があります。

### <a name="resolution"></a>解決策

1. [ネットワークの計画](../automation-hybrid-runbook-worker.md#network-planning)に関する記事にアクセスし、Update Management を動作させるために許可する必要があるアドレスとポートを確認してください。
2. 複製されたイメージを使用する場合:
   1. Log Analytics ワークスペースで、`MicrosoftDefaultScopeConfig-Updates` スコープ構成の保存された検索条件から VM を削除します (表示された場合)。 保存された検索条件は、ワークスペース内の **[全般]** にあります。
   2. `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force` を実行します。
   3. `Restart-Service HealthService` を実行して `HealthService` を再起動します。 これにより、キーが再作成され、新しい UUID が生成されます。
   4. この方法がうまくいかない場合は、まずイメージで sysprep を実行してから、MMA をインストールします。

## <a name="multi-tenant"></a>シナリオ:別の Azure テナントのマシンを対象とした更新プログラムの展開を作成するときに、リンクされているサブスクリプションのエラーが発生する

### <a name="issue"></a>問題

別の Azure テナントのマシンを対象とした更新プログラムの展開を作成しようとすると、次のエラーが発生します。

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>原因

このエラーは、更新プログラムの展開に含まれる別のテナントの Azure VM を持つ更新プログラムの展開を作成するときに発生します。

### <a name="resolution"></a>解決策

次の回避策を使用して、これらの項目をスケジュールします。 スケジュールを作成するには、`-ForUpdate` スイッチを指定して [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) コマンドレットを使用します。 次に、[AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) コマンドレットを使用して、他のテナントのマシンを `-NonAzureComputer` パラメーターに渡します。 以下の例は、その方法を示しています。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>シナリオ:原因不明の再起動

### <a name="issue"></a>問題

**[Reboot control]\(再起動制御\)** オプションを **[再起動しない]** に設定しても、更新プログラムのインストール後にマシンが再起動します。

### <a name="cause"></a>原因

Windows Update はいくつかのレジストリ キーによって変更でき、そのいずれかによって再起動の動作が変更されることがあります。

### <a name="resolution"></a>解決策

「[レジストリを編集して自動更新を構成する](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)」と、[「再起動の管理に使われるレジストリ キー](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)」に記載されているレジストリ キーを確認して、マシンが正しく構成されていることを確認します。

## <a name="failed-to-start"></a>シナリオ:更新プログラムの展開で "起動できませんでした" とマシンに表示される

### <a name="issue"></a>問題

マシンに **[起動できませんでした]** 状態が表示されます。 マシンの特定の詳細情報を表示すると、次のエラーが表示されます。

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>原因

このエラーは、次のいずれかの理由で発生することがあります。

* そのマシンはもう存在しません。
* マシンの電源が切れていて到達できません。
* マシンにネットワーク接続の問題があるため、マシン上のハイブリッド worker にアクセスできません。
* MMA に対する更新があり、SourceComputerId が変更されました。
* Automation アカウントで 2,000 個の同時ジョブの制限に達した場合は、更新の実行が制限されました。 各展開は 1 つのジョブと見なされ、更新プログラムの展開内の各マシンは 1 つのジョブとカウントされます。 Automation アカウントで現在実行されている他のオートメーション ジョブや更新プログラムの展開は、すべて同時ジョブ制限の対象になります。

### <a name="resolution"></a>解決策

該当する場合は、更新プログラムの展開に[動的グループ](../automation-update-management-groups.md)を使用します。 追加として:

* マシンがまだ存在し、到達可能であることを確認します。 存在しない場合は、展開を編集してそのマシンを削除します。
* Update Management に必要なポートとアドレスの一覧については、「[ネットワークの計画](../automation-update-management.md#ports)」セクションを参照し、ご使用のマシンがこれらの要件を満たしていることを確認します。
* Log Analytics で次のクエリを実行して、環境内で `SourceComputerId` が変更されているマシンを見つけます。 `Computer` 値は同じで `SourceComputerId` 値が異なるコンピューターを探します。 

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```
   影響を受けるマシンを見つけたら、それらのマシンをターゲットとする更新プログラムの展開を編集し、その後、`SourceComputerId` が正しい値を反映するようにそれらのマシンの削除と再追加を行います。

## <a name="updates-nodeployment"></a>シナリオ:展開なしで更新プログラムがインストールされる

### <a name="issue"></a>問題

Update Management に Windows マシンを登録すると、展開なしで更新プログラムがインストールされます。

### <a name="cause"></a>原因

Windows では、更新プログラムは、使用可能になるとすぐに自動的にインストールされます。 この動作が原因で、更新プログラムをマシンに展開するスケジュールを設定しなかった場合、混乱が生じる可能性があります。

### <a name="resolution"></a>解決策

レジストリ キー `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` の既定値は、4: **自動でダウンロードしてインストールする**に設定されています。

Update Management クライアントの場合は、このキーを 3: **自動でダウンロードするが、自動でインストールはしない**に設定することをお勧めします。

詳細については、[自動更新の構成](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates)に関する記事を参照してください。

## <a name="machine-already-registered"></a>シナリオ:マシンが違うアカウントに既に登録されている

### <a name="issue"></a>問題

次のエラー メッセージが表示されます。

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>原因

マシンが既に Update Management 用の別のワークスペースにオンボードされています。

### <a name="resolution"></a>解決策

1. 「[Update Management のポータルにマシンが表示されない](#nologs)」の手順に従って、マシンのレポート先が正しいワークスペースであることを確認します。
2. [Hybrid Runbook グループを削除する](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)ことにより、マシン上の古いアーティファクトをクリーンアップしてから、再試行します。

## <a name="machine-unable-to-communicate"></a>シナリオ:マシンがサービスと通信できない

### <a name="issue"></a>問題

次のいずれかのエラー メッセージが表示されます。

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

### <a name="cause"></a>原因

プロキシ、ゲートウェイ、またはファイアウォールがネットワーク通信をブロックしている可能性があります。

### <a name="resolution"></a>解決策

ネットワークを見直し、適切なポートとアドレスが許可されていることを確認します。 Update Management および Hybrid Runbook Worker で必要なポートとアドレスの一覧については、[ネットワーク要件](../automation-hybrid-runbook-worker.md#network-planning)を参照してください。

## <a name="unable-to-create-selfsigned-cert"></a>シナリオ:自己署名証明書を作成できない

### <a name="issue"></a>問題

次のいずれかのエラー メッセージが表示されます。

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>原因

Hybrid Runbook Worker が自己署名証明書を生成できませんでした。

### <a name="resolution"></a>解決策

**C:\ProgramData\Microsoft\Crypto\RSA** フォルダーへの読み取りアクセスがシステム アカウントにあることを確認してから、再試行します。

## <a name="mw-exceeded"></a>シナリオ:スケジュールされた更新が、MaintenanceWindowExceeded エラーで失敗しました

### <a name="issue"></a>問題

更新の既定のメンテナンス時間は 120 分です。 メンテナンス期間は、最大 6 時間つまり 360 分まで増やすことができます。

### <a name="resolution"></a>解決策

スケジュール済みの更新プログラムの展開で失敗したものがあれば編集し、メンテナンス期間を延長します。

メンテナンス期間の詳細については、[更新プログラムのインストール](../automation-tutorial-update-management.md#schedule-an-update-deployment)に関する記事を参照してください。

## <a name="hresult"></a>シナリオ:マシンに "評価が行われていません" と表示され、HResult 例外が表示される

### <a name="issue"></a>問題

* **[コンプライアンス]** に **[評価が行われていません]** と表示されているマシンがあり、その下に例外メッセージが表示されます。
* 評価が行われていないとして表示されているマシンがあります。
* ポータルに HRESULT エラー コードが表示されます。

### <a name="cause"></a>原因

更新エージェント (Windows 上の Windows Update エージェント、Linux ディストリビューション用のパッケージ マネージャー) が正しく構成されていません。 Update Management は、必要な更新プログラム、パッチの状態、展開されたパッチの結果を提供するために、マシンの更新エージェントを利用しています。 この情報がないと、Update Management は必要なパッチやインストール済みのパッチを適切にレポートすることができません。

### <a name="resolution"></a>解決策

マシンで更新プログラムをローカルで実行してみてください。 これが失敗した場合は、通常、更新エージェントに構成エラーがあることを意味します。

この問題は、ネットワーク構成とファイアウォールの問題が原因で発生することがよくあります。 次の操作を試してみてください。

* Linux の場合は、適切なドキュメントを確認して、パッケージ リポジトリのネットワーク エンドポイントにアクセスできることを確認します。
* Windows の場合は、「[イントラネット エンドポイントから更新プログラムがダウンロードされない (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)」の一覧にあるエージェントの構成を確認してください。
  * マシンが Windows Update 用に構成されている場合は、「[HTTP またはプロキシに関連する問題](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)」で説明されているエンドポイントにアクセスできることを確認します。
  * マシンが Windows Server Update Services (WSUS) 用に構成されている場合は、[WUServer レジストリ キー](/windows/deployment/update/waas-wu-settings)によって構成された WSUS サーバーにアクセスできることを確認します。

HRESULT が表示される場合は、赤で表示された例外をダブルクリックして、例外メッセージ全体を確認します。 考えられる解決策または推奨される対策については、次の表を参照してください。

|例外  |解決策または対策  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | [Windows Update エラー コード一覧](https://support.microsoft.com/help/938205/windows-update-error-code-list)で該当するエラー コードを検索して、例外の原因に関する詳細を確認します。        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | これらはネットワーク接続の問題を示しています。 マシンが Update Management にネットワーク接続されていることを確認します。 必要なポートとアドレスの一覧については、「[ネットワークの計画](../automation-update-management.md#ports)」セクションを参照してください。        |
|`0x8024001E`| サービスまたはシステムがシャットダウン中のため、更新操作が完了しませんでした。|
|`0x8024002E`| Windows Update サービスが無効です。|
|`0x8024402C`     | WSUS サーバーを使用している場合は、レジストリ キー `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` の下の `WUServer` と `WUStatusServer` のレジストリ値で正しい WSUS サーバーが指定されていることを確認します。        |
|`0x80072EE2`|ネットワーク接続の問題、または構成された WSUS サーバーとの通信に問題があります。 WSUS 設定を確認し、サービスがクライアントからアクセス可能であることを確認します。|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Windows Update サービス (wuauserv) が実行されており、無効になっていないことを確認します。        |
|その他の一般的な例外     | 可能な解決策についてインターネットで検索を行い、最寄りの IT サポートと連携します。         |

また、Windowsupdate.log ファイルを確認すると、考えられる原因の特定に役立ちます。 ログの読み取り方法の詳細については、「[Windowsupdate.log ファイルの解釈](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file)」を参照してください。

[Windows Update トラブルシューティング ツール](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)をダウンロードして実行し、マシン上の Windows Update に問題がないか確認することもできます。

> [!NOTE]
> [Windows Update トラブルシューティングツール](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)のドキュメントに、Windows クライアントでの使用向けであると記載されていますが、Windows Server でも動作します。

## <a name="scenario-update-run-returns-failed-status-linux"></a>シナリオ: 更新プログラムの実行で "失敗" 状態が返される (Linux)

### <a name="issue"></a>問題

更新プログラムの実行が開始されますが、実行中にエラーが発生します。

### <a name="cause"></a>原因

考えられる原因:

* パッケージ マネージャーが異常です。
* 更新エージェント (Windows の場合は WUA、Linux の場合はディストリビューション固有のパッケージ マネージャー) が正しく構成されていません。
* 特定のパッケージが、クラウド ベースの修正プログラムの適用を妨げています。
* マシンにアクセスできません。
* 更新プログラムに、解決されていない依存関係がありました。

### <a name="resolution"></a>解決策

正常に開始した後に更新プログラムの実行中にエラーが発生した場合は、実行で影響を受けたマシンからの[ジョブ出力を確認](../manage-update-multi.md#view-results-of-an-update-deployment)します。 マシンからの特定のエラー メッセージが見つかれば、調査して対処することができます。 Update Management で更新プログラムをデプロイするには、パッケージ マネージャーが正常である必要があります。

ジョブが失敗する直前に特定の修正プログラム、パッケージ、または更新プログラムが表示される場合は、次の更新プログラムの展開からそれらを[除外](../automation-tutorial-update-management.md#schedule-an-update-deployment)してみることができます。 Windows Update からログ情報を収集するには、「[Windows Update のログ ファイル](/windows/deployment/update/windows-update-logs)」を参照してください。

修正プログラムの問題を解決できない場合は、次の更新プログラムの展開が開始される "*前に*"、トラブルシューティングのために次のログ ファイルをコピーして、保存してください。

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>修正プログラムがインストールされない

### <a name="machines-dont-install-updates"></a>マシンで更新プログラムがインストールされない

* マシン上で直接更新プログラムを実行してみてください。 マシンで更新プログラムを適用できない場合は、[トラブルシューティング ガイドで、発生する可能性のあるエラーの一覧](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult)をご覧ください。
* 更新プログラムがローカルで実行される場合は、[Update Management からの VM の削除](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources)に関する記事のガイダンスに従って、マシン上でエージェントを削除し、再インストールしてみてください。

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>更新プログラムを利用できることはわかっているが、自分のマシンに利用可能として表示されない

* これは、多くの場合、WSUS または System Center Configuration Manager (SCCM) から更新プログラムを取得するようにマシンが構成されているのに、WSUS および SCCM で更新プログラムが承認されなかった場合に発生します。
* WSUS と SCCM のためにマシンが構成されているかどうかは、[こちらの記事の「レジストリを編集して自動更新を構成する」セクション内のレジストリ キーに対して UseWUServer レジストリ キーを相互参照する](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)ことでチェックできます。
* 更新プログラムは、WSUS 内で承認されていない場合、インストールされません。 次のクエリを実行して、Log Analytics 内で未承認の更新プログラムを確認できます。

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>インストールされると更新プログラムが表示されますが、コンピューター上で検索できない

* 更新プログラムは多くの場合、他の更新プログラムによって置き換えられます。 詳細については、Windows Update のトラブルシューティング ガイドの「[更新プログラムは置き換えられました](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)」を参照してください。

### <a name="installing-updates-by-classification-on-linux"></a>Linux での更新プログラムの分類別インストール

* 分類 ([緊急更新プログラムとセキュリティ更新プログラム]) 別に Linux に更新プログラムを展開する場合、特に CentOS に関する重要な注意事項があります。 これらの制限事項は、[Update Management の概要に関するページ](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)に記載されています。

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 が常に欠落している

* KB2267602 は [Windows Defender の定義更新](https://www.microsoft.com/wdsi/definitions)です。 これは毎日更新されます。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルで追加のサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。
* Azure サポート インシデントを送信する。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
