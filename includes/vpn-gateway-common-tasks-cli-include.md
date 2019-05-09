---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 61082333afc88abef0a8d8a57d1f1b1d893b6148
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246197"
---
### <a name="to-view-local-network-gateways"></a>ローカル ネットワーク ゲートウェイを表示するには

ローカル ネットワーク ゲートウェイの一覧を表示するには、[az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway) コマンドを使用します。

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>共有キーの値を確認するには

共有キーの値が VPN デバイスの構成に使用したものと同じ値であることを確認します。 異なる場合は、デバイスの値を使用して接続をもう一度実行するか、返された値でデバイスを更新します。 値は一致する必要があります。 共有キーを表示するには、[az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection) を使用します。

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>VPN ゲートウェイのパブリック IP アドレスを表示するには

仮想ネットワーク ゲートウェイのパブリック IP アドレスを探すときには、[az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip) コマンドを使用します。 読みやすいように、この例の出力には書式を設定し、パブリック IP の一覧が表形式で表示されるようにしてあります。

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
