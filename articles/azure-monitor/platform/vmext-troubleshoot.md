---
title: Azure Monitor での Azure Log Analytics VM 拡張機能のトラブルシューティング | Microsoft Docs
description: Windows および Linux Azure VM の Log Analytics VM 拡張機能で最も一般的な問題の現象、原因、解決方法を説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: magoedte
ms.openlocfilehash: 40f0705cfa7f0e9bb45d300a629adebd0cc5be47
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883674"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Azure Monitor での Log Analytics VM 拡張機能のトラブルシューティング
この記事では、Microsoft Azure で実行されている Windows および Linux 仮想マシンの Log Analytics VM 拡張機能で発生する可能性のあるエラーのトラブルシューティングを支援し、それらの問題について考えられる解決策を提案します。

拡張機能の状態を確認するには、Azure Portal から次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. Azure Portal で、**[すべてのサービス]** をクリックします。 リソースの一覧で、「**virtual machines**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Virtual Machines]** を選択します。
3. 仮想マシンの一覧で、検索して選択します。
3. 仮想マシンで、**[拡張機能]** をクリックします。
4. 一覧から、Log Analytics 拡張機能が有効かどうかを確認します。  Linux の場合、エージェントは **OMSAgentforLinux** として表示され、Windows の場合、エージェントは **MicrosoftMonitoringAgent** として表示されます。

   ![VM の拡張機能の表示](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. 拡張機能をクリックして詳細を表示します。 

   ![VM の拡張機能の詳細](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Azure Windows VM 拡張機能のトラブルシューティング

*Microsoft Monitoring Agent* VM 拡張機能のインストールまたはレポートが正しく機能しない場合は、以下の手順で問題のトラブルシューティングを行ってください。

1. Azure VM エージェントがインストールされ、正しく動作しているかどうかを [KB 2965986](https://support.microsoft.com/kb/2965986#mt1) の手順に従って確認します。
   * さらに VM エージェントのログ ファイル (`C:\WindowsAzure\logs\WaAppAgent.log`) を確認します。
   * ログが存在しない場合、VM エージェントがインストールされていません。
   * [Azure VM エージェントのインストール](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. 次の手順に従って、Microsoft Monitoring Agent 拡張機能のハート ビート タスクが実行中であることを確認します。
   * 仮想マシンにログインします。
   * タスク スケジューラを開いて `update_azureoperationalinsight_agent_heartbeat` タスクを探します。
   * タスクが有効になっていて 1 分おきに実行されていることを確認します。
   * `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log` にあるハート ビートのログファイルを確認します。
3. `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent` で Microsoft Monitoring Agent VM 拡張機能のログ ファイルを確認します。
4. 仮想マシンで PowerShell スクリプトを実行できることを確認します。
5. C:\Windows\temp に対するアクセス許可が変更されていないことを確認します。
6. 仮想マシン上の管理者特権の PowerShell ウィンドウで「`(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`」と入力し、Microsoft Monitoring Agent の状態を確認します。
7. `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs` にある Microsoft Monitoring Agent のセットアップ ログ ファイルを確認します。

詳細については、[Windows 拡張機能のトラブルシューティング](../../virtual-machines/extensions/oms-windows.md)に関するページをご覧ください。

## <a name="troubleshooting-linux-vm-extension"></a>Linux VM 拡張機能のトラブルシューティング
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
*Linux 用 Log Analytics エージェント*の VM 拡張機能のインストールまたはレポートが正しく機能しない場合は、以下の手順で問題のトラブルシューティングを行ってください。

1. 拡張機能の状態が "*不明*" になっている場合は、Azure VM エージェントがインストールされて正常に動作しているかどうかを VM エージェントのログ ファイル (`/var/log/waagent.log`) で確認してください。
   * ログが存在しない場合、VM エージェントがインストールされていません。
   * [Linux VM に Azure VM エージェントをインストールします。](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. それ以外の異常な状態については、Linux 用 Log Analytics エージェントの VM 拡張機能のログ ファイル (`/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` および `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`) を確認してください。
3. 拡張機能が正常な状態であるにもかかわらず、データがアップロードされない場合は、Linux 用 Log Analytics エージェントのログ ファイル (`/var/opt/microsoft/omsagent/log/omsagent.log`) を確認してください。

詳細については、[Linux 拡張機能のトラブルシューティング](../../virtual-machines/extensions/oms-linux.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

Azure の外部のコンピューターにホストされている Linux 用 Log Analytics エージェントに関連する追加のトラブルシューティング ガイダンスについては、[Azure Log Analytics Linux エージェントのトラブルシューティング](agent-linux-troubleshoot.md)に関する記事をご覧ください。  
