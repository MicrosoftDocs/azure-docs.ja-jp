---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 923620c1c9719d857cc848507a4c5507f528d34e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95561029"
---
### <a name="to-view-local-network-gateways"></a>ローカル ネットワーク ゲートウェイを表示するには

ローカル ネットワーク ゲートウェイの一覧を表示するには、[az network local-gateway list](/cli/azure/network/local-gateway) コマンドを使用します。

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>共有キーの値を確認するには

共有キーの値が VPN デバイスの構成に使用したものと同じ値であることを確認します。 異なる場合は、デバイスの値を使用して接続をもう一度実行するか、返された値でデバイスを更新します。 値が一致する必要があります。 共有キーを表示するには、[az network vpn-connection-list](/cli/azure/network/vpn-connection) を使用します。

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>VPN ゲートウェイのパブリック IP アドレスを表示するには

仮想ネットワーク ゲートウェイのパブリック IP アドレスを探すときには、[az network public-ip list](/cli/azure/network/public-ip) コマンドを使用します。 読みやすいように、この例の出力には書式を設定し、パブリック IP の一覧が表形式で表示されるようにしてあります。

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```