---
title: "PowerShell を使用してゾーン パブリック IP アドレスを作成する |Microsoft Docs"
description: "PowerShell を使用して可用性ゾーン内にパブリック IP を作成します。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: bfeba57036338b4e325d2f122443f2cde0373eed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-powershell"></a>PowerShell を使用して可用性ゾーン内にパブリック IP アドレスを作成する

Azure 可用性ゾーン (プレビュー) 内にパブリック IP アドレスをデプロイできます。 可用性ゾーンとは、Azure リージョン内の物理的に分離されたゾーンのことです。 以下の項目について説明します。

> * 可用性ゾーン内にパブリック IP アドレスを作成する
> * 可用性ゾーン内に作成された関連リソースを識別する
  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

この記事では、バージョン 4.4.0 以上の AzureRM モジュールがインストールされている必要があります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureRM)から最新バージョンの AzureRM モジュールをインストールします。

> [!NOTE]
> 可用性ゾーンはプレビュー段階であり、開発とテストのシナリオで使用できます。 選択された Azure リソース、リージョン、および VM サイズ ファミリに対するサポートが使用できます。 使用を開始する方法、および可用性ゾーンを試行する場合にどの Azure リソース、リージョン、および VM サイズ ファミリを使用できるかの詳細については、「[Overview of Availability Zones (可用性ゾーンの概要)](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)」を参照してください。 サポートについては、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) でアクセスするか、または [Azure サポート チケットを開く](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ことができます。

## <a name="log-in-to-azure"></a>Azure へのログイン

`Login-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Login-AzureRmAccount
```
## <a name="create-resource-group"></a>Create resource group

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 この例では、*myResourceGroup* という名前のリソース グループが *westeurope* リージョンに作成されます。 *westeurope* は、可用性グループ (プレビュー段階) をサポートする Azure リージョンの 1 つです。

```powershell
New-AzureRmResourceGroup -Name AzTest -Location westeurope
```

## <a name="create-a-zonal-public-ip-address"></a>ゾーンのパブリック IP アドレスの作成

次のコマンドを使用して、可用性ゾーン内にパブリック IP アドレスを作成します。

```powershell
    New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name myPublicIp `
        -Location westeurope `
        -AllocationMethod Static `
        -Zone 2
```

> [!NOTE]
> Standard SKU のパブリック IP アドレスを仮想マシンのネットワーク インターフェイスに割り当てる場合は、[ネットワーク セキュリティ グループ](security-overview.md#network-security-groups)で目的のトラフィックを明示的に許可する必要があります。 ネットワーク セキュリティ グループを作成して関連付け、目的のトラフィックを明示的に許可するまで、そのリソースとの通信は失敗します。

## <a name="get-zone-information-about-a-public-ip-address"></a>パブリック IP アドレスに関するゾーン情報を取得する

次のコマンドを使用して、パブリック IP アドレスのゾーン情報を取得します。

```powershell
Get-AzureRmPublicIpAddress ` 
    -ResourceGroup myResourceGroup `
    -Name myPublicIp
```

## <a name="next-steps"></a>次のステップ

- [可用性ゾーン](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)の詳細を学習する
- [パブリック IP アドレス](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json)の詳細を学習する 