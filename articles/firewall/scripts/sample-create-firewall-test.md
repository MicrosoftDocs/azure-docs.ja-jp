---
title: Azure Firewall のテスト環境を作成する
description: このサンプル スクリプトでは、ファイアウォールとテスト ネットワーク環境を作成します。 ネットワークには、3 つのサブネットを含む 1 つのVNet があります。
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 4158bc07373a2d0aa6fb6ceaf2dce62b50bb6bd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94658368"
---
# <a name="create-an-azure-firewall-test-environment"></a>Azure Firewall のテスト環境を作成する

このサンプル スクリプトでは、ファイアウォールとテスト ネットワーク環境を作成します。 ネットワークには、*AzureFirewallSubnet*、*ServersSubnet*、および *JumpboxSubnet* の 3 つのサブネットを含む 1 つの VNet があります。 ServersSubnet と JumpboxSubnet には、それぞれ 1 つの 2 コア Windows Server があります。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

ファイアウォールは AzureFirewallSubnet にあり、`www.microsoft.com` へのアクセスを許可する単一のルールを含むアプリケーション ルール コレクションで構成されています。

ユーザー定義のルートが作成されます。このルートは、ファイアウォール規則が適用されるファイアウォールを経由する ServersSubnet からのネットワーク トラフィックを指します。

このスクリプトは、Azure [Cloud Shell](https://shell.azure.com/powershell) から、またはローカルの PowerShell インストールから実行できます。 

PowerShell をローカルで実行する場合、このスクリプトでは Azure PowerShell が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 

アップグレードが必要な場合は、Windows 10 および Windows Server 2016 に組み込まれている `PowerShellGet` を使用できます。

> [!NOTE]
>他の Windows バージョンでは、使用する前に `PowerShellGet` をインストールする必要があります。 `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` を実行すると、このツールがシステムにインストールされているかどうかを確認できます。 出力が空白の場合は、最新の [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616) をインストールする必要があります。

詳細については、[Azure PowerShell のインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。

Web Platform Installer を使用してインストールされた既存の Azure PowerShell は PowerShellGet のインストールと競合するため、削除する必要があります。

PowerShell をローカルで実行している場合は `Connect-AzAccount` を実行して Azure との接続を作成する必要もあることを思い出してください。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、およびすべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは次のコマンドを使用して、リソース グループ、仮想ネットワーク、およびネットワーク セキュリティ グループを作成します。 以下の表の各コマンドは、それぞれのドキュメントにリンクされています。

| command | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | サブネット構成オブジェクトを作成します。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Azure 仮想ネットワークとフロントエンド サブネットを作成します。 |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | ネットワーク セキュリティ グループに割り当てるセキュリティ規則を作成します。 |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |特定のサブネットに対して特定のポートを許可またはブロックする NSG ルールを作成します。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | NSG をサブネットに関連付けます。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | インターネットから VM にアクセスするためのパブリック IP アドレスを作成します。 |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | 仮想ネットワーク インターフェイスを作成し、それらを仮想ネットワークのフロントエンドおよびバックエンド サブネットに接続します。 |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | VM 構成を作成します。 この構成には、VM 名、オペレーティング システム、管理資格情報などの情報が含まれます。 この構成は、VM の作成時に使用されます。 |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 仮想マシンを作成します。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |
|[New-AzFirewall](/powershell/module/az.network/new-azfirewall)| 新しい Azure Firewall データベースを作成します。|
|[Get-AzFirewall](/powershell/module/az.network/get-azfirewall)|Azure Firewall オブジェクトを取得します。|
|[New-AzFirewallApplicationRule](/powershell/module/az.network/new-azfirewallapplicationrule)|新しい Azure Firewall アプリケーション ルールを作成します。|
|[Set-AzFirewall](/powershell/module/az.network/set-azfirewall)|Azure Firewall オブジェクトへの変更をコミットします。|

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。