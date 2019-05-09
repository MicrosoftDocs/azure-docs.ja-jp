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
ms.openlocfilehash: e368b1590f263618969423d57cdf0531fc2bb54d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246179"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>ローカル ネットワーク ゲートウェイの "gatewayIpAddress" を変更するには

接続先の VPN デバイスのパブリック IP アドレスが変更された場合には、ローカル ネットワーク ゲートウェイを変更してアドレスの変更を反映する必要があります。 ゲートウェイの IP アドレスは、既存の VPN ゲートウェイ接続 (がある場合には、その接続) を削除することなく変更できます。 ゲートウェイの IP アドレスを変更するには、[az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway) コマンドを使用します。値 "Site2" と "TestRG1" は独自の値に置き換えてください。

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

出力に表示されている IP アドレスが正しいことを確認します。

```
"gatewayIpAddress": "23.99.222.170",
```
