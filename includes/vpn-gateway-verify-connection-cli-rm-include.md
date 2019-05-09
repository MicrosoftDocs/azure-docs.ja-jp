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
ms.openlocfilehash: 0e009354e66ab13cdb9fbc3cf9e4b37e904bdfd1
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246194"
---
接続に成功したことを確認するには、[az network vpn-connection show](/cli/azure/network/vpn-connection) コマンドを使用します。 この例では、テストする接続の名前が "--name" で示されています。 接続が確立中の場合は、接続状態は "Connecting" と表示されます。 接続が確立されると、状態は "Connected" に変更されます。

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
