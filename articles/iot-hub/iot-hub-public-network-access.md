---
title: Azure IoT Hub のパブリック ネットワーク アクセスの管理
description: IoT ハブのパブリック ネットワーク アクセスを無効および有効にする方法に関するドキュメント
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: c82f98df8fb79fa10f2e30b219c1a02bb646e2de
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85937464"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>IoT ハブのパブリック ネットワーク アクセスの管理

[VNet 内の IoT ハブのプライベート エンドポイント](virtual-network-support.md)のみにアクセスを制限するには、パブリック ネットワーク アクセスを無効にします。 そうするには、Azure portal または `publicNetworkAccess` API を使用します。 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Azure portal を使用してパブリック ネットワーク アクセスを無効にする

1. [Azure portal](https://portal.azure.com) にアクセスします
2. IoT Hub に移動します。
3. 左側のメニューで **[ネットワーキング]** を選択します。
4. [Allow public network access to]\(パブリック ネットワーク アクセスを許可する\) で、 **[無効]** を選択します
5. **[保存]** を選択します。

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="パブリック ネットワーク アクセスを無効にしている Azure portal を示す画像" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

パブリック ネットワーク アクセスを有効にするには、 **[有効]** 、 **[保存]** の順に選択します。

## <a name="ip-filter"></a>IP フィルター 

パブリック ネットワーク アクセスが無効になっている場合、すべての [IP フィルター](iot-hub-ip-filtering.md)規則は無視されます。 これは、パブリック インターネットからのすべての IP がブロックされるためです。 IP フィルターを使用するには、 **[Selected IP ranges]\(選択された IP 範囲\)** オプションを使用します。

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>組み込みのイベント ハブ互換エンドポイントのバグの修正

IoT Hub には、IoT Hub へのパブリック ネットワーク アクセスが無効になっている場合でも、[組み込みのイベント ハブ互換性エンドポイント](iot-hub-devguide-messages-read-builtin.md)がパブリック インターネット経由でアクセス可能であり続けるというバグがあります。 このバグの詳細とお問い合わせについては、[IoT Hub に対するパブリック ネットワーク アクセスの無効化による組み込みのイベント ハブ エンドポイントへのアクセスの無効化](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)に関するページを参照してください。