---
title: ネットワーク アクセス制御を構成する
titleSuffix: Azure SignalR Service
description: Azure SignalR Service のネットワーク アクセス制御を構成します。
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 24e56736e0d033420f9aaf976b0fb7d9727c1a5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92143227"
---
# <a name="configure-network-access-control"></a>ネットワーク アクセス制御を構成する

Azure SignalR Service では、要求の種類と使用されるネットワークのサブセットに基づいて、サービス エンドポイントへのアクセス レベルをセキュリティで保護し、制御することができます。 ネットワーク ルールが構成されている場合、指定された一連のネットワークを介してデータを要求するアプリケーションのみが、Azure SignalR サービスにアクセスできます。

Azure SignalR サービスには、インターネット経由でアクセスできるパブリック エンドポイントがあります。 [Azure SignalR Service のプライベート エンドポイント](howto-private-endpoints.md)を作成することもできます。 プライベート エンドポイントは、VNet のプライベート IP アドレスを Azure SignalR Service に割り当て、プライベート リンクを介して VNet と Azure SignalR Service 間のすべてのトラフィックを保護します。 Azure SignalR Service ネットワーク アクセス制御は、パブリック エンドポイントとプライベート エンドポイントの両方に対するアクセス制御を提供します。

必要に応じて、パブリック エンドポイントと各プライベート エンドポイントに対する特定の種類の要求を許可するか拒否するかを選択できます。 たとえば、パブリック エンドポイントからのすべての[サーバー接続](signalr-concept-internals.md#server-connections)をブロックし、特定の VNet から送信されたものだけにすることができます。

ネットワーク アクセス制御ルールが有効なときに Azure SignalR Service にアクセスするアプリケーションでも、要求に対する適切な承認が必要です。

## <a name="scenario-a---no-public-traffic"></a>シナリオ A - パブリック トラフィックなし

すべてのパブリック トラフィックを完全に拒否するには、まず要求の種類を許可しないようにパブリック ネットワーク ルールを構成する必要があります。 次に、特定の VNet からのトラフィックにアクセスを許可するルールを構成する必要があります。 この構成では、アプリケーションに対してセキュリティで保護されたネットワーク境界を構築することができます。

## <a name="scenario-b---only-client-connections-from-public-network"></a>シナリオ B - パブリック ネットワークからのクライアント接続のみ

このシナリオでは、パブリック ネットワークからの[クライアント接続](signalr-concept-internals.md#client-connections)のみを許可するようにパブリック ネットワーク ルールを構成できます。 その後、特定の VNet から送信された他の種類の要求を許可するように、プライベート ネットワーク ルールを構成することができます。 この構成では、パブリック ネットワークからアプリ サーバーが非表示になり、アプリ サーバーと Azure SignalR Service 間にセキュリティで保護された接続が確立されます。

## <a name="managing-network-access-control"></a>ネットワーク アクセス制御の管理

Azure portal を使用して、Azure SignalR Service のネットワーク アクセス制御を管理できます。

### <a name="azure-portal"></a>Azure portal

1. セキュリティで保護する Azure SignalR Service に移動します。

1. **ネットワーク アクセス制御** という名前の設定メニューをクリックします。

    ![ポータルでのネットワーク ACL](media/howto-network-access-control/portal.png)

1. 既定のアクションを編集するには、 **[許可/拒否]** ボタンを切り替えます。

    > [!TIP]
    > 既定のアクションは、一致する ACL ルールがない場合に実行するアクションです。 たとえば、既定のアクションが **[拒否]** の場合、下で明示的に承認されていない種類の要求は拒否されます。

1. パブリック ネットワーク ルールを編集するには、 **[パブリック ネットワーク]** で許可されている要求の種類を選択します。

    ![ポータルでパブリック ネットワーク ACL を編集する ](media/howto-network-access-control/portal-public-network.png)

1. プライベート エンドポイント ネットワーク ルールを編集するには、 **[プライベート エンドポイント 接続]** の下の各行で許可された種類の要求を選択します。

    ![ポータルでプライベート エンドポイント ACL を編集する ](media/howto-network-access-control/portal-private-endpoint.png)

1. **[保存]** をクリックして変更を保存します。

## <a name="next-steps"></a>次のステップ

[Azure Private Link](../private-link/private-link-overview.md) について学習します。