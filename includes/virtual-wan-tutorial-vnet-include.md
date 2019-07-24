---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 660bbf50e1a8ae73bd7bbe1f7c42691ed62d276a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552995"
---
VNet がまだない場合は、PowerShell を使用して簡単に作成できます。 仮想ネットワークは、Azure portal を使用して作成することもできます。

* 作成する VNet のアドレス空間が接続する他の VNet のアドレス範囲やオンプレミス ネットワークのアドレス空間と重複しないことを確認してください。 
* VNet が既にある場合は、VNet が必要な条件を満たしていて、仮想ネットワーク ゲートウェイがないことを確認します。

この記事の [使ってみる] をクリックして PowerShell コンソールを開くと、VNet を簡単に作成することができます。 値を調整し、コマンドをコピーしてコンソール ウィンドウに貼り付けます。

### <a name="create-a-resource-group"></a>リソース グループの作成

PowerShell コマンドを調整した後、リソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>VNet を作成する

PowerShell コマンドを調整して、ご使用の環境に適した VNet を作成します。

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```