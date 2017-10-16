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
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: b2d702ecdd5e1fca342e7c84c6e75339097f0bcd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="verify-a-vpn-gateway-connection"></a>VPN Gateway 接続の確認

この記事では、クラシックと Resource Manager 両方のデプロイメント モデルでの VPN ゲートウェイ接続を確認する方法について説明します。

## <a name="azure-portal"></a>Azure ポータル

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

PowerShell を使用して Resource Manager デプロイメント モデルでの VPN ゲートウェイ接続を確認するには、[Azure Resource Manager PowerShell コマンドレット](/powershell/azure/overview)の最新バージョンをインストールします。

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用して Resource Manager デプロイメント モデルでの VPN ゲートウェイ接続を確認するには、[CLI コマンドレット](https://docs.microsoft.com/cli/azure/install-azure-cli)の最新バージョン (2.0 以降) をインストールします。

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Azure Portal (クラシック)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (クラシック)

PowerShell を使用してクラシック デプロイメント モデルでの VPN ゲートウェイ接続を確認するには、Azure PowerShell コマンドレットの最新バージョンをインストールします。 [Service Management](https://docs.microsoft.com/powershell/azure/install-azure-ps?view=azuresmps-3.7.0) モジュールをダウンロードし、インストールしてください。 'Add-AzureAccount' を使用して、クラシック デプロイメント モデルにログインします。

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>次のステップ

* 仮想ネットワークに仮想マシンを追加できます。 手順については、 [仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) に関するページを参照してください。