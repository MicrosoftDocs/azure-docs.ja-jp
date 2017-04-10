---
title: "VPN Gateway 接続の確認 | Microsoft Docs"
description: "この記事では、仮想ネットワーク VPN Gateway の接続を確認する方法を説明します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b5bad71095e4b7e3b26df15780467526200ffa10
ms.openlocfilehash: 68d94a6402b1497f65c4d03fb987ba800e86c2a3
ms.lasthandoff: 01/31/2017


---
# <a name="verify-a-vpn-gateway-connection"></a>VPN Gateway 接続の確認
仮想ネットワーク VPN Gateway の接続は、ポータルと PowerShell のどちらを使用しても確認できます。 この記事では、Resouce Manager デプロイメント モデルとクラシック デプロイメント モデルの両方の手順について説明します。

## <a name="verify-using-the-azure-portal"></a>Azure Portal を使用した確認

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="verify-using-powershell"></a>PowerShell を使用した確認

PowerShell を使用して確認を行うには、Azure Resource Manager PowerShell コマンドレットの最新版をインストールします。 PowerShell コマンドレットのインストールについて詳しくは、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。 Resource Manager コマンドレットの使用方法の詳細については、 [Resource Manager での Windows PowerShell の使用](../powershell-azure-resource-manager.md)に関するページをご覧ください。

### <a name="log-in-to-your-azure-account"></a>Azure アカウントにログインする
1. 昇格された特権で PowerShell コンソールを開き、アカウントに接続します。
   
        Login-AzureRmAccount
2. アカウントのサブスクリプションを確認します。
   
        Get-AzureRmSubscription 
3. 使用するサブスクリプションを指定します。
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="verify-your-connection"></a>接続を確認する

[!INCLUDE [Powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal-classic"></a>Azure Portal を使用した確認 (クラシック)
[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


## <a name="verify-using-powershell-classic"></a>PowerShell を使用した確認 (クラシック)
PowerShell を使用して確認を行うには、Azure PowerShell コマンドレットの最新版をインストールします。 Resouce Manager と Service Management (SM) の両方のバージョンをダウンロードしてインストールしてください。 PowerShell コマンドレットのインストールについて詳しくは、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。 

### <a name="log-in-to-your-azure-account"></a>Azure アカウントにログインする
1. 昇格された特権で PowerShell コンソールを開き、アカウントに接続します。
   
        Login-AzureRmAccount
2. アカウントのサブスクリプションを確認します。
   
        Get-AzureRmSubscription 
3. 使用するサブスクリプションを指定します。
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
4. ログインして、クラシック デプロイメント モデル向けの Service Management コマンドレットを使用します。

        Add-AzureAccount

### <a name="verify-your-connection"></a>接続を確認する
[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>次のステップ
* 仮想ネットワークに仮想マシンを追加できます。 手順については、 [仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) に関するページを参照してください。


