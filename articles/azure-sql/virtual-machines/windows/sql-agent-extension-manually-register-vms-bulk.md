---
title: Azure の複数の SQL VM を SQL IaaS Agent 拡張機能に登録する
description: SQL Server VM を SQL IaaS Agent 拡張機能に一括登録して管理性を向上させます。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 558daede55f6563155d3f54e97d77c0a3ca4de59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97357230"
---
# <a name="register-multiple-sql-vms-in-azure-with-the-sql-iaas-agent-extension"></a>Azure の複数の SQL VM を SQL IaaS Agent 拡張機能に登録する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure 内のご自分の SQL Server 仮想マシン (VM) を、`Register-SqlVMs` Azure PowerShell コマンドレットを使用して、[SQL IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に一括登録する方法について説明します。 


この記事では、SQL Server VM を手動で一括登録する方法について説明します。 また、[すべての SQL Server VM を自動的に](sql-agent-extension-automatic-registration-all-vms.md)登録したり、[個々の SQL Server VM を手動で](sql-agent-extension-manually-register-single-vm.md)登録したりすることができます。 

## <a name="overview"></a>概要

`Register-SqlVMs` コマンドレットを使用すると、指定のサブスクリプションの一覧、リソース グループ、特定の仮想マシンの一覧にあるすべての仮想マシンを登録できます。 このコマンドレットは、仮想マシンを[ライトウェイト管理モード](sql-server-iaas-agent-extension-automate-management.md#management-modes)で登録し、[レポートとログ ファイル](#output-description)の両方を生成します。 

登録プロセスにはリスクがなく、ダウンタイムは発生せず、SQL Server サービスも仮想マシンも再起動されません。 

## <a name="prerequisites"></a>前提条件

SQL Server VM を拡張機能に登録するには、以下のものが必要になります。 

- [**Microsoft.SqlVirtualMachine** プロバイダーに登録されており](sql-agent-extension-manually-register-single-vm.md#register-subscription-with-rp)、未登録の SQL Server 仮想マシンが含まれる [Azure サブスクリプション](https://azure.microsoft.com/free/)。 
- 仮想マシンの登録に使用されるクライアント資格情報は、Azure ロールの **仮想マシンの共同作成者**、**共同作成者**、または **所有者** のいずれかに存在します。 
- 最新バージョンの [Az PowerShell (5.0 以上)](/powershell/azure/new-azureps-module-az)。 


## <a name="get-started"></a>はじめに

先に進む前に、スクリプトのローカル コピーを作成し、それを PowerShell モジュールとしてインポートして、Azure に接続する必要があります。 

### <a name="create-the-script"></a>スクリプトを作成します。

スクリプトを作成するには、この記事の最後にある[完全なスクリプト](#full-script)をコピーし、`RegisterSqlVMs.psm1` としてローカルに保存します。 

### <a name="import-the-script"></a>スクリプトをインポートする

スクリプトを作成したら、PowerShell ターミナルでモジュールとしてインポートできます。 

管理者権限で PowerShell ターミナルを開き、`RegisterSqlVMs.psm1` ファイルを保存した場所に移動します。 その後、次の PowerShell コマンドレットを実行して、スクリプトをモジュールとしてインポートします。 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Azure に接続する

次の PowerShell コマンドレットを使用して Azure に接続します。

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-a-list-of-subscriptions"></a>サブスクリプションの一覧にあるすべての VM 

次のコマンドレットを使用して、サブスクリプションの一覧にあるすべての SQL Server 仮想マシンを登録します。

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

出力例: 

```
Number of subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>単一のサブスクリプションにあるすべての VM

次のコマンドレットを使用して、単一のサブスクリプションにあるすべての SQL Server 仮想マシンを登録します。 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

出力例:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>複数のリソース グループにあるすべての VM

次のコマンドレットを使用して、単一のサブスクリプション内の複数のリソース グループにあるすべての SQL Server 仮想マシンを登録します。

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

出力例:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>リソース グループにあるすべての VM

次のコマンドレットを使用して、すべての SQL Server 仮想マシンを単一のリソース グループに登録します。 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

出力例:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-a-single-resource-group"></a>単一のリソース グループにある特定の VM

次のコマンドレットを使用して、単一のリソース グループ内の特定の SQL Server 仮想マシンを登録します。

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

出力例:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="a-specific-vm"></a>特定の VM

次のコマンドレットを使用して、特定の SQL Server 仮想マシンを登録します。 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

出力例:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>出力の説明

`Register-SqlVMs` コマンドレットが使用されるたびに、レポートとログ ファイルの両方が生成されます。 

### <a name="report"></a>レポート

レポートは `RegisterSqlVMScriptReport<Timestamp>.txt` という名前の `.txt` ファイルとして生成されます。タイムスタンプはコマンドレットが開始された時刻になります。 レポートには次の詳細が一覧表示されます。

| **出力値** | **説明** |
| :--------------  | :-------------- | 
| アクセスできないか、資格情報が正しくないため、登録できなかったサブスクリプションの数 | これには、指定した認証で問題が発生したサブスクリプションの数とその一覧が表示されます。 エラーの詳細は、サブスクリプション ID でログを検索すると見つかります。 | 
| リソース プロバイダーに登録されていないために試行できなかったサブスクリプションの数 | このセクションには、SQL IaaS Agent 拡張機能に登録されていないサブスクリプションの数とその一覧が含まれています。 |
| 見つかった VM の総数 | コマンドレットに渡されたパラメーターのスコープで見つかった仮想マシンの数。 | 
| 既に登録されている VM | 拡張機能に既に登録されているためスキップされた仮想マシンの数。 |
| 正常に登録された VM の数 | コマンドレットの実行後に正常に登録された仮想マシンの数。 登録された仮想マシンを `SubscriptionID, Resource Group, Virtual Machine` の形式で一覧表示します。 | 
| エラーにより登録に失敗した VM の数 | 何らかのエラーが原因で登録に失敗した仮想マシンの数。 エラーの詳細は、ログ ファイルで確認できます。 | 
| VM または VM のゲスト エージェントが実行されていないためスキップされた VM の数 | 仮想マシンまたは仮想マシンのゲスト エージェントが実行されていなかったため登録できなかった仮想マシンの数とその一覧。 これらは、仮想マシンまたはゲスト エージェントが開始された後に再試行できます。 詳細は、ログ ファイルで確認できます。 |
| Windows 上で SQL Server を実行していないためスキップされた VM の数 | SQL Server を実行していないか、Windows 仮想マシンではないためにスキップされた仮想マシンの数。 仮想マシンは `SubscriptionID, Resource Group, Virtual Machine` の形式で一覧表示されます。 | 
| &nbsp; | &nbsp; |

### <a name="log"></a>ログ 

エラーは `VMsNotRegisteredDueToError<Timestamp>.log` という名前のログ ファイルに記録されます。タイムスタンプはスクリプトが開始された時刻になります。 このエラーがサブスクリプション レベルで発生した場合、ログにはコンマで区切られたサブスクリプション ID とエラー メッセージが含まれます。 仮想マシンの登録でエラーが発生した場合、ログにはコンマで区切られたサブスクリプション ID、リソース グループ名、仮想マシン名、エラー コード、メッセージが含まれます。 

## <a name="remarks"></a>解説

指定のスクリプトを使用して SQL Server VM を拡張機能に登録するときは、次の点を考慮してください。

- 拡張機能に登録するには、SQL Server VM でゲスト エージェントが実行されている必要があります。 Windows Server 2008 のイメージにはゲスト エージェントがないため、これらの仮想マシンは失敗します。そのため、[NoAgent 管理モード](sql-server-iaas-agent-extension-automate-management.md#management-modes)を使用して手動で登録する必要があります。
- 透過的なエラーを解決するための再試行ロジックが組み込まれています。 仮想マシンが正常に登録されていれば、短時間の処理になります。 ただし、登録が失敗した場合は、各仮想マシンで再試行されます。  実際の所要時間は、エラーの種類と数によって異なりますが、登録プロセスが完了するまでの時間は十分に確保するようにしてください。 

## <a name="full-script"></a>完全なスクリプト

完全なスクリプトについては、[Az PowerShell を使用した SQL Server VM の一括登録](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1)に関する GitHub のページを参照してください。 

完全なスクリプトをコピーし、`RegisterSqLVMs.psm1` として保存します。

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](frequently-asked-questions-faq.md)
* [Windows VM 上の SQL Server の価格ガイダンス](pricing-guidance.md)
* [Windows VM 上の SQL Server のリリース ノート](../../database/doc-changes-updates-release-notes.md)
