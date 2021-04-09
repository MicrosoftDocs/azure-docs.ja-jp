---
title: Azure IoT Hub のパブリック ネットワーク アクセスの管理
description: IoT ハブのパブリック ネットワーク アクセスを無効および有効にする方法に関するドキュメント
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: b225ad7e12a95b4dcbb8656f54f8a9e9562b2ee8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604697"
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

パブリック ネットワーク アクセスを有効にするには、 **[すべてのネットワーク]** 、 **[保存]** の順に選択します。

## <a name="accessing-the-iot-hub-after-disabling-public-network-access"></a>パブリック ネットワーク アクセスを無効にした後の IoT Hub へのアクセス

パブリック ネットワーク アクセスを無効にした後、IoT Hub には、[その VNet プライベート エンドポイントから Azure プライベート リンクを使用して](virtual-network-support.md)アクセスする必要があります。

## <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>パブリック ネットワーク アクセスを無効にした後の IoT Hub エンドポイント、IP アドレス、およびポート

IoT Hub はマルチテナントのサービスとしてのプラットフォーム (PaaS) であるため、異なる顧客がコンピューティング、ネットワーク、およびストレージのハードウェア リソースの同じプールを共有します。 IoT Hub のホスト名は、インターネット経由でパブリックにルーティング可能な IP アドレスを持つパブリック エンドポイントにマップされます。 この IoT Hub パブリック エンドポイントはさまざまな顧客によって共有され、IoT デバイスはワイドエリア ネットワークやオンプレミス ネットワーク経由ですべてアクセスできます。 

特定の IoT Hub リソースでは、パブリック ネットワーク アクセスの無効化が強制され、分離が保証されます。 パブリック パスを使用して他のカスタマー リソースに対してサービスをアクティブな状態に保つために、パブリック エンドポイントは解決可能な状態のままで、IP アドレスは検出可能で、ポートは開いたままになります。 テナント間の完全な分離を実現するために、Microsoft では複数のセキュリティ層が統合されるため、これは問題ではありません。 詳細については、「[Azure Public Cloud での分離](../security/fundamentals/isolation-choices.md#tenant-level-isolation)」を参照してください。

## <a name="ip-filter"></a>IP フィルター 

パブリック ネットワーク アクセスが無効になっている場合、すべての [IP フィルター](iot-hub-ip-filtering.md)規則は無視されます。 これは、パブリック インターネットからのすべての IP がブロックされるためです。 IP フィルターを使用するには、 **[Selected IP ranges]\(選択された IP 範囲\)** オプションを使用します。

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>組み込みのイベント ハブ互換エンドポイントのバグの修正

IoT Hub には、IoT Hub へのパブリック ネットワーク アクセスが無効になっている場合でも、[組み込みのイベント ハブ互換性エンドポイント](iot-hub-devguide-messages-read-builtin.md)がパブリック インターネット経由でアクセス可能であり続けるというバグがあります。 このバグの詳細とお問い合わせについては、[IoT Hub に対するパブリック ネットワーク アクセスの無効化による組み込みのイベント ハブ エンドポイントへのアクセスの無効化](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)に関するページを参照してください。
