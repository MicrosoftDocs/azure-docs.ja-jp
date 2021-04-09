---
title: Azure Automation での Windows Update エージェントに関する問題のトラブルシューティング
description: この記事では、Update Management の使用時に Windows Update エージェントに関する問題をトラブルシューティングして解決する方法について説明します。
services: automation
ms.date: 01/25/2020
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: 9516210021ce48f069ae3b3b4e02503527e0db24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580895"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Windows Update エージェントの問題をトラブルシューティングする

Update Management のデプロイ時にマシンが準備完了 (正常) と表示されない理由は多数存在する可能性があります。 Windows Hybrid Runbook Worker エージェントの正常性を検査して、背後にある問題を特定できます。 マシンの 3 つの準備状態を次に示します。

* 準備完了: Hybrid Runbook Worker がデプロイされ、最後に表示されてからの経過時間が 1 時間未満である。
* 切断状態の場合: Hybrid Runbook Worker がデプロイされ、最後に表示されてからの経過時間が 1 時間以上である。
* 未構成:Hybrid Runbook Worker が見つからないか、デプロイが終了していない。

> [!NOTE]
> Azure portal に表示される内容とマシンの現在の状態の間で、わずかに遅延が発生する可能性があります。

この記事では、Azure portal から Azure マシンを対象として、また、[オフラインのシナリオ](#troubleshoot-offline)で Azure 以外のマシンを対象としてトラブルシューティング ツールを実行する方法について説明します。

> [!NOTE]
> トラブルシューティング スクリプトには、Windows Server Update Services (WSUS) のチェック、およびキーの自動ダウンロードとインストールのキーのチェックが新しく組み込まれました。

## <a name="start-the-troubleshooter"></a>トラブルシューティングの開始

Azure マシンの場合は、ポータルの **[Update Agent Readiness]\(Update エージェントの準備\)** 列にある **[トラブルシューティング]** リンクを選択することで、[Troubleshoot Update Agent]\(Update エージェントのトラブルシューティング\) ページを起動できます。 Azure 以外のマシンの場合は、リンクをクリックすると、この記事が表示されます。 Azure 以外のマシンをトラブルシューティングするには、「[オフライン トラブルシューティング](#troubleshoot-offline)」を参照してください。

![Update Management の仮想マシンの一覧のスクリーンショット](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Hybrid Runbook Worker の正常性を確認するには、VM が実行されている必要があります。 VM が実行されていない場合は、 **[Start the VM]\(VM を起動\)** ボタンが表示されます。

[Troubleshoot Update Agent]\(Update エージェントのトラブルシューティング\) ページで **[チェックを実行]** を選択すると、トラブルシューティング ツールが開始します。 トラブルシューティング ツールでは [[実行コマンド]](../../virtual-machines/windows/run-command.md) を使用してマシンに対してスクリプトを実行し、依存関係を検証します。 トラブルシューティング ツールが終了すると、チェック結果が返されます。

![[Troubleshoot Update Agent]\(Update エージェントのトラブルシューティング\) ページのスクリーンショット](../media/update-agent-issues/troubleshoot-page.png)

準備ができると、結果がページに表示されます。 チェックのセクションには、各チェックに含まれる内容が表示されます。

![[Troubleshoot Update Agent]\(Update エージェントのトラブルシューティング\) のチェックのスクリーンショット](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>前提条件のチェック

### <a name="operating-system"></a>オペレーティング システム

オペレーティング システム チェックでは、Hybrid Runbook Worker が次の表のいずれかのオペレーティング システムを実行しているかどうかが検証されます。

|オペレーティング システム  |Notes  |
|---------|---------|
|Windows Server 2012 以降 |.NET Framework 4.6 以降が必要です。 ([.NET Framework のダウンロード](/dotnet/framework/install/guide-for-developers)。)<br/> Windows PowerShell 5.1 が必要です。  ([Windows Management Framework 5.1 のダウンロード](https://www.microsoft.com/download/details.aspx?id=54616)。)        |

### <a name="net-462"></a>.NET 4.6.2

.NET Framework のチェックでは、システムに [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) 以降がインストールされているかどうかが検証されます。

### <a name="wmf-51"></a>WMF 5.1

WMF のチェックでは、必要なバージョンの Windows Management Framework (WMF)、つまり [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) がシステムに存在するかどうかが検証されます。

### <a name="tls-12"></a>TLS 1.2

このチェックでは、TLS 1.2 を使って通信を暗号化しているかどうかを判別します。 TLS 1.0 は、プラットフォームでサポートされなくなりました。 Update Management と通信するために、TLS 1.2 を使用してください。

## <a name="connectivity-checks"></a>接続チェック

### <a name="registration-endpoint"></a>登録エンドポイント

このチェックでは、エージェントがエージェント サービスと正しく通信できるかどうかを判別します。

Hybrid Runbook Worker エージェントが登録エンドポイントと通信できるように、プロキシとファイアウォールが構成されている必要があります。 アドレスと開くポートの一覧については、[ネットワーク計画](../automation-hybrid-runbook-worker.md#network-planning)に関する記事を参照してください。

### <a name="operations-endpoint"></a>操作エンドポイント

このチェックでは、エージェントが Job Runtime Data Service と正しく通信できるかどうかを判別します。

Hybrid Runbook Worker エージェントが Job Runtime Data Service と通信できるように、プロキシとファイアウォールが構成されている必要があります。 アドレスと開くポートの一覧については、「[ネットワークの計画](../automation-hybrid-runbook-worker.md#network-planning)」を参照してください。

## <a name="vm-service-health-checks"></a>VM サービス正常性チェック

### <a name="monitoring-agent-service-status"></a>エージェント サービスの状態の監視

このチェックでは、Windows 用 Log Analytics エージェント (`healthservice`) がマシン上で実行されているかどうかを確認します。 このサービスのトラブルシューティングの詳細については、「[Windows 用 Log Analytics エージェントが実行されていない](hybrid-runbook-worker.md#mma-not-running)」を参照してください。

Windows 用の Log Analytics エージェントを再インストールする場合、「[Windows 用エージェントをインストールする](../../azure-monitor/vm/quick-collect-windows-computer.md#install-the-agent-for-windows)」を参照してください。

### <a name="monitoring-agent-service-events"></a>エージェント サービス イベントの監視

このチェックでは、過去 24 時間にマシンの Azure Operations Manager ログに 4502 イベントが記録されているかどうかを判別します。

このイベントの詳細については、「[Operations Manager ログのイベント 4502](hybrid-runbook-worker.md#event-4502)」を参照してください。

## <a name="access-permissions-checks"></a>アクセス許可のチェック

> [!NOTE]
> このトラブルシューティング ツールでは、現在、プロキシ サーバーが構成されている場合、プロキシ サーバー経由でトラフィックがルーティングされません。

### <a name="crypto-folder-access"></a>暗号化フォルダー アクセス

暗号化フォルダー アクセスのチェックでは、ローカル システム アカウントが C:\ProgramData\Microsoft\Crypto\RSA にアクセスできるかどうかが判別されます。

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>オフライン トラブルシューティング

スクリプトをローカルに実行することで、Hybrid Runbook Worker のトラブルシューティング ツールをオフラインで使用できます。 GitHub から次のスクリプトを入手します。[UM_Windows_Troubleshooter_Offline.ps1](https://github.com/Azure/updatemanagement/blob/main/UM_Windows_Troubleshooter_Offline.ps1)。 このスクリプトを実行するには、WMF 4.0 以降をインストールしておく必要があります。 最新バージョンの PowerShell をダウンロードするには、「[PowerShell のさまざまなバージョンのインストール](/powershell/scripting/install/installing-powershell)」を参照してください。

このスクリプトの出力は次の例のようになります。

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>次のステップ

[Hybrid Runbook Worker の問題のトラブルシューティング](hybrid-runbook-worker.md)。
