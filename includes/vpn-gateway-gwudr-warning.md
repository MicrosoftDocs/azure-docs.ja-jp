---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a852807ab685e85b76d26e5b39c99a32f645bbd7
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838158"
---
0\.0.0.0/0 が宛先のユーザー定義のルートと GatewaySubnet の NSG は**サポートされていません**。 この構成で作成されたゲートウェイの作成はブロックされます。 ゲートウェイが正常に機能するためには、管理コントローラーへのアクセスが必要です。
