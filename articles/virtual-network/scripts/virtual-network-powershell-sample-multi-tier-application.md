---
title: 多層アプリケーション用の仮想ネットワークの作成 - Azure PowerShell スクリプト サンプル
description: 多層アプリケーション用の仮想ネットワークの作成 - Azure PowerShell スクリプト サンプル。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51cafb0a344c5b6226b3b033d8c60327eaf2c05f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91267943"
---
# <a name="create-a-network-for-multi-tier-applications-script-sample"></a>多層アプリケーション用のネットワークの作成のスクリプト サンプル

このスクリプト サンプルは、フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークを作成します。 フロントエンド サブネットへのトラフィックは HTTP と SSH に制限され、バックエンド サブネットへのトラフィックは MySQL、ポート 3306 に制限されます。 このスクリプトを実行すると、2 つの仮想マシンが、Web サーバーと MySQL ソフトウェアをデプロイできる各サブネットに 1 つずつ作成されます。

Azure [Cloud Shell](https://shell.azure.com/powershell) から、またはローカルの PowerShell インストールからスクリプトを実行することができます。 PowerShell をローカルで使用する場合、このスクリプトでは Azure PowerShell モジュール バージョン 1.0.0 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!-- gitHub issue https://github.com/MicrosoftDocs/azure-docs/issues/17748 -->
仮想ネットワークを作成すると (具体的には、-Subnet オプションを指定して New-AzVirtualNetwork コマンドレットを使用した場合)、サブネット ID が割り当てられます。 New-AzVirtualNetwork を呼び出す前に New-AzVirtualNetworkSubnetConfig コマンドレットを使用してサブネットを構成した場合は、New-AzVirtualNetwork を呼び出すまでサブネット ID は表示されません。

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.ps1  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループ、VM、およびすべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想ネットワーク、およびネットワーク セキュリティ グループを作成します。 以下の表の各コマンドは、それぞれのドキュメントにリンクされています。

| command | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Azure 仮想ネットワークとフロントエンド サブネットを作成します。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | バックエンド サブネットを作成します。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | インターネットから VM にアクセスするためのパブリック IP アドレスを作成します。 |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | 仮想ネットワーク インターフェイスを作成し、それらを仮想ネットワークのフロントエンドおよびバックエンド サブネットに接続します。 |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | フロントエンドおよびバックエンド サブネットに関連付けられているネットワーク セキュリティ グループ (NSG) を作成します。 |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |特定のサブネットに対して特定のポートを許可またはブロックする NSG ルールを作成します。 |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 仮想マシンを作成し、NIC を各 VM にアタッチします。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループとそれに含まれるすべてのリソースを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

他の仮想ネットワークの PowerShell スクリプト サンプルについては、[仮想ネットワークの PowerShell サンプル](../powershell-samples.md)に関するページを参照してください。
