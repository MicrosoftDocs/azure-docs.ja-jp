---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4e958026b1167d65f47bddbe5e89ec4d6fed7ee3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92217966"
---
接続に成功したことを確認するには、[az network vpn-connection show](/cli/azure/network/vpn-connection) コマンドを使用します。 この例では、テストする接続の名前が "--name" で示されています。 接続が確立中の場合は、接続状態は "Connecting" と表示されます。 接続が確立されると、状態は "Connected" に変更されます。

```azurecli-interactive
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
