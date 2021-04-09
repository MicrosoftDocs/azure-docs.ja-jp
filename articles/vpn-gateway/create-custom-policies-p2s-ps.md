---
title: 'ポイント対サイトのカスタム IPsec ポリシーの作成と設定: PowerShell'
titleSuffix: Azure VPN Gateway
description: この記事は、VPN Gateway P2S 構成のカスタム IPsec ポリシーの作成および設定に役立ちます。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/09/2020
ms.author: cherylmc
ms.openlocfilehash: a77a870304e20c179e22b4a4ffe404315894d8a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91743694"
---
# <a name="create-and-set-custom-ipsec-policies-for-point-to-site-preview"></a>ポイント対サイトのカスタム IPsec ポリシーの作成と設定 (プレビュー)

ご使用の環境で、暗号化のカスタム IPsec ポリシーが必要な場合は、必要な設定を使用して、ポリシー オブジェクトを簡単に構成することができます。 この記事は、カスタム ポリシー オブジェクトを作成し、PowerShell を使用して設定する場合に役立ちます。

## <a name="before-you-begin"></a>開始する前に

### <a name="prerequisites"></a>前提条件

ご使用の環境が次の前提条件を満たしていることを確認します。

* ポイント対サイト VPN が既に構成されています。 そうでない場合は、[PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md) または [Azure portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md) を使用して、**ポイント対サイト VPN の作成** に関する記事の手順に従って構成します。

### <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="1-set-variables"></a><a name="signin"></a>1.変数の設定

使用する変数を宣言します。 次のサンプルを使用し、必要に応じて独自の値で置き換えます。 演習中の任意の時点で PowerShell セッションまたは Cloud Shell セッションを閉じた場合に変数を再宣言するには、値をもう一度コピーして貼り付けます。

  ```azurepowershell-interactive
  $RG = "TestRG"
  $GWName = "VNet1GW"
  ```

## <a name="2-create-policy-object"></a><a name="create"></a>2.ポリシー オブジェクトの作成

カスタム IPsec ポリシー オブジェクトを作成します。 値は、必要な条件に合わせて調整できます。

```azurepowershell-interactive
$vpnclientipsecpolicy = New-AzVpnClientIpsecPolicy -IpsecEncryption AES256 -IpsecIntegrity SHA256 -SALifeTime 86471 -SADataSize 429496 -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup2 -PfsGroup PFS2
```

## <a name="3-update-gateway-and-set-policy"></a><a name="update"></a>3.ゲートウェイの更新とポリシーの設定

この手順では、既存の P2S VPN ゲートウェイを更新して、IPsec ポリシーを設定します。

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway -VpnClientIpsecPolicy $vpnclientipsecpolicy
```

## <a name="next-steps"></a>次の手順

P2S 構成の詳細については、「[ポイント対サイト VPN について](point-to-site-about.md)」を参照してください。
