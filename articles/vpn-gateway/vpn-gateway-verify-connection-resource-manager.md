---
title: 'Azure VPN Gateway: ゲートウェイ接続を確認する'
description: この記事では、仮想ネットワーク VPN Gateway の接続を確認する方法を説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: b59294d07ef64875cb6fbd3e3a49dec61d8b8135
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659405"
---
# <a name="verify-a-vpn-gateway-connection"></a>VPN Gateway 接続の確認

この記事では、クラシック デプロイ モデルと Resource Manager デプロイ モデルの両方での VPN ゲートウェイ接続を確認する方法について説明します。

## <a name="azure-portal"></a>Azure portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

PowerShell を使用して Resource Manager デプロイ モデルでの VPN ゲートウェイ接続を確認するには、[Azure Resource Manager PowerShell コマンドレット](/powershell/azure/)の最新バージョンをインストールします。

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用して Resource Manager デプロイ モデルでの VPN ゲートウェイ接続を確認するには、[CLI コマンドレット](/cli/azure/install-azure-cli)の最新バージョン (2.0 以降) をインストールします。

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="azure-portal-classic"></a>Azure Portal (クラシック)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (クラシック)

PowerShell を使用してクラシック デプロイ モデルでの VPN ゲートウェイ接続を確認するには、Azure PowerShell コマンドレットの最新バージョンをインストールします。 [Service Management](/powershell/azure/servicemanagement/install-azure-ps?#azure-service-management-cmdlets) モジュールをダウンロードし、インストールしてください。 'Add-AzureAccount' を使用して、クラシック デプロイ モデルにログインします。

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>次のステップ

* 仮想ネットワークに仮想マシンを追加できます。 手順については、 [仮想マシンの作成](../virtual-machines/windows/quick-create-portal.md) に関するページを参照してください。