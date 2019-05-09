---
title: Update Management のエラーをトラブルシューティングする
description: Update Management の問題をトラブルシューティングする方法を説明します。
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 22e3ea1c90946902fc2a16d947ff2884e5e0a44b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274588"
---
# <a name="troubleshooting-issues-with-update-management"></a>Update Management の問題をトラブルシューティングする

この記事では、Update Management の使用時に発生する可能性がある問題の解決策について説明します。

根本的な問題を特定するための Hybrid Worker エージェント用のエージェント トラブルシューティング ツールがあります。 このトラブルシューティング ツールの詳細については、[更新エージェントの問題のトラブルシューティング](update-agent-issues.md)に関する記事を参照してください。 その他のすべての問題については、考えられる問題に関する以下の詳細情報をご覧ください。

## <a name="general"></a>全般

### <a name="components-enabled-not-working"></a>シナリオ: "Update Management" ソリューションのコンポーネントは有効であり、この仮想マシンの構成を行っている

#### <a name="issue"></a>問題

オンボードから 15 分経過しても、仮想マシンに関する次のメッセージが引き続き表示される。

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>原因

このエラーは次の理由で発生する可能性があります。

1. Automation アカウントに戻る通信がブロックされています。
2. オンボードしている VM の複製元が、Microsoft Monitoring Agent がインストールされた状態で sysprep されなかった複製マシンであった可能性があります。

#### <a name="resolution"></a>解決策

1. 「[ネットワークの計画](../automation-hybrid-runbook-worker.md#network-planning)」で、Update Management を動作させるために許可する必要があるアドレスとポートを確認してください。
2. 複製されたイメージを使用する場合:
   1. Log Analytics ワークスペースで、スコープ構成 `MicrosoftDefaultScopeConfig-Updates` の保存された検索条件から VM を削除します (表示された場合)。 保存された検索条件は、ワークスペース内の **[全般]** にあります。
   2. ラン `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. `Restart-Service HealthService` を実行して `HealthService` を再起動します。 これにより、キーが再作成され、新しい UUID が生成されます。
   4. これが機能しない場合は、そのイメージを先に sysprep した後、MMA エージェントをインストールします。

### <a name="multi-tenant"></a>シナリオ:別の Azure テナントのマシンを対象とした更新プログラムのデプロイを作成しているときに、リンクされているサブスクリプションのエラーが発生する。

#### <a name="issue"></a>問題

別の Azure テナントのマシンを対象とした更新プログラムのデプロイを作成しようとすると次のエラーが発生します。

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>原因

このエラーは、別のテナントの Azure 仮想マシンを含んだ更新プログラムのデプロイを作成するときに発生します。

#### <a name="resolution"></a>解決策

次の回避策を使用してそれらをスケジュールする必要があります。 スケジュールは、[New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) コマンドレットと `-ForUpdate` スイッチを使用して作成できます。[New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) コマンドレットを使用する際、`-NonAzureComputer` パラメーターに他のテナントのマシンを渡すことができます。 以下の例は、その方法を示しています。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>シナリオ:マシンの Azure Monitor ログに表示されない Update Management データ

#### <a name="issue"></a>問題

**[コンプライアンス]** の下に **[評価が行われていません]** と表示されるマシンがありますが、Hybrid Runbook Worker に対する Azure Monitor ログにハートビート データが表示され、Update Management には表示されません。

#### <a name="cause"></a>原因

Hybrid Runbook Worker の再登録と再インストールが必要な可能性があります。

#### <a name="resolution"></a>解決策

「[Windows Hybrid Runbook Worker をデプロイする](../automation-windows-hrw-install.md)」(Windows の場合) または「[Linux Hybrid Runbook Worker を展開する](../automation-linux-hrw-install.md)」(Linux の場合) の手順に従ってハイブリッド worker を再インストールしてください。

## <a name="windows"></a>Windows

ソリューションまたは仮想マシンをオンボードしようとして問題が発生した場合は、ローカル コンピューター上の **[アプリケーションとサービス ログ]** にある **Operations Manager** イベント ログで、イベント ID **4502** のイベントと **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** を含むイベント メッセージを確認してください。

次のセクションでは、具体的なエラー メッセージと、考えられる個別の解決策を示します。 他のオンボードの問題については、[ソリューションをオンボードする際のトラブルシューティング](onboarding.md)に関する記事を参照してください。

### <a name="machine-already-registered"></a>シナリオ: マシンが違うアカウントに既に登録されている

#### <a name="issue"></a>問題

次のエラー メッセージが表示されます。

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>原因

マシンが既に Update Management 用の別のワークスペースにオンボードされています。

#### <a name="resolution"></a>解決策

[Hybrid Runbook グループを削除する](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)ことにより、マシン上の古いアーティファクトのクリーンアップを実行してから、再試行します。

### <a name="machine-unable-to-communicate"></a>シナリオ: マシンがサービスと通信できない

#### <a name="issue"></a>問題

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

#### <a name="cause"></a>原因

ネットワーク通信をブロックしているプロキシ、ゲートウェイ、またはファイアウォールが存在する可能性があります。

#### <a name="resolution"></a>解決策

ネットワークを見直し、適切なポートとアドレスが許可されていることを確認します。 Update Management および Hybrid Runbook Worker で必要なポートとアドレスの一覧については、[ネットワーク要件](../automation-hybrid-runbook-worker.md#network-planning)を参照してください。

### <a name="unable-to-create-selfsigned-cert"></a>シナリオ: 自己署名証明書を作成できない

#### <a name="issue"></a>問題

次のいずれかのエラー メッセージが表示されます。

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>原因

Hybrid Runbook Worker が自己署名証明書を生成できませんでした。

#### <a name="resolution"></a>解決策

フォルダー **C:\ProgramData\Microsoft\Crypto\RSA** への読み取りアクセスがシステム アカウントにあることを確認してから、再試行します。

### <a name="hresult"></a>シナリオ: マシンが未評価として表示され、HResult 例外が表示される

#### <a name="issue"></a>問題

**[コンプライアンス]** に **[評価が行われていません]** と表示されているマシンがあり、その下に例外メッセージが表示されます。

#### <a name="cause"></a>原因

マシンで Windows Update または WSUS が正しく構成されていません。 Update Management は、必要な更新プログラム、パッチの状態、デプロイされたパッチの結果を提供するために、Windows Update または WSUS を利用しています。 この情報がないと、必要なパッチやインストール済みのパッチを適切にレポートすることができません。

#### <a name="resolution"></a>解決策

赤で表示された例外をダブルクリックして、例外メッセージ全体を確認します。 考えられる解決策または取るべき対策については、次の表を参照してください。

|例外  |解決策または対策  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | [Windows Update エラー コード一覧](https://support.microsoft.com/help/938205/windows-update-error-code-list)で該当するエラー コードを検索して、例外の原因の詳細を確認します。        |
|`0x8024402C` or `0x8024401C`     | これらのエラーはネットワーク接続の問題です。 お使いのマシンが Update Management に適切にネットワーク接続されていることを確認します。 必要なポートとアドレスの一覧については、[ネットワーク計画](../automation-update-management.md#ports)に関するセクションをご覧ください。        |
|`0x8024402C`     | WSUS サーバーを使用している場合は、レジストリ キー `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` の下の `WUServer` と `WUStatusServer` のレジストリ値に正しい WSUS サーバーが含まれていることを確認します。        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Windows Update サービス (wuauserv) が実行されており、無効になっていないことを確認します。        |
|その他の一般的な例外     | 考えられる解決策をインターネットで検索し、最寄りの IT サポートと連携してください。         |

`windowsupdate.log` を確認すると、可能性がある原因を特定するのにも役立ちます。 ログを読み取る方法については、「[Windowsupdate.log ファイルの解釈](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file)」を参照してください。

さらに、[Windows Update トラブルシューティング ツール](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)をダウンロードして実行し、コンピューター上の Windows Update に問題がないか確認できます。

> [!NOTE]
> [Windows Update トラブルシューティング ツール](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)により、それは Windows クライアント向けであるが、Windows Server でも機能することが示されます。

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>シナリオ: 更新プログラムの実行を開始できない

#### <a name="issue"></a>問題

Linux マシンで更新プログラムの実行を開始できません。

#### <a name="cause"></a>原因

Linux ハイブリッド worker が異常です。

#### <a name="resolution"></a>解決策

次のログ ファイルのコピーを作成し、トラブルシューティングのために保存します。

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>シナリオ: 更新プログラムの実行が開始されるがエラーが発生する

#### <a name="issue"></a>問題

更新プログラムの実行が開始されますが、実行中にエラーが発生します。

#### <a name="cause"></a>原因

次の原因が考えられます。

* パッケージ マネージャーが異常です。
* 特定のパッケージがクラウドに基づく修正プログラムと干渉しています。
* その他の理由。

#### <a name="resolution"></a>解決策

Linux 上で正常に開始した後に更新プログラムの実行中にエラーが発生した場合は、実行で影響を受けるマシンからの出力を確認します。 コンピューターのパッケージ マネージャーからの特定のエラー メッセージが見つかれば、調査して対処することができます。 Update Management で更新プログラムをデプロイするには、パッケージ マネージャーが正常である必要があります。

場合によっては、Update Management とパッケージの更新プログラムが干渉して、更新プログラムのデプロイが完了されていないことがあります。 この場合、これらのパッケージを今後の更新プログラムの実行から除外するか、それらを手動でインストールする必要があります。

修正プログラムの問題を解決できない場合は、次のログ ファイルをコピーし、トラブルシューティングのために、次の更新プログラムの展開が開始される**前に**保存します。

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
