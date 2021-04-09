---
title: ExpressRoute と Azure VPN に対する Azure Route Server (プレビュー) のサポートについて
description: Azure Route Server が ExpressRoute ゲートウェイと Azure VPN ゲートウェイとどのように連携しているかについて説明します。
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 6e588c7c0381c6825bcf75cbbe28a1dd6b865940
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101678812"
---
# <a name="about-azure-route-server-preview-support-for-expressroute-and-azure-vpn"></a>ExpressRoute と Azure VPN に対する Azure Route Server (プレビュー) のサポートについて

Azure Route Server は、Azure で実行されているサードパーティ製のネットワーク仮想アプライアンス (NVA) をサポートするのみならず、ExpressRoute および Azure VPN ゲートウェイとのシームレスな統合も行います。 ゲートウェイと Azure Route Server の間で、BGP ピアリングを構成したり、管理したりする必要はありません。 ゲートウェイと Azure Route Server の間でのルート交換は、簡単な[構成変更](quickstart-configure-route-server-powershell.md#route-exchange)によって有効にすることができます。

> [!IMPORTANT]
> Azure Route Server (プレビュー) は現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

仮想ネットワーク内で ExpressRoute ゲートウェイと NVA と共に Azure Route Server をデプロイする場合、Azure Route Server では、既定で、NVA と ExpressRoute ゲートウェイから受信する各ルートが相互に伝達されません。 ルート交換を有効にすると、ExpressRoute と NVA は、互いのルートを学習します。

次の図に例を示します。

* SDWAN アプライアンスは、ExpressRoute に接続されている “On-prem 2” からのルートを、仮想ネットワーク ルートと併せて Azure Route Server から受信します。

* ExpressRoute ゲートウェイは、SDWAN アプライアンスに接続されている “On-prem 1” からのルートを、仮想ネットワーク ルートと併せて Azure Route Server から受信します。

    ![ルート サーバーで構成された ExpressRoute を示す図。](./media/expressroute-vpn-support/expressroute-with-route-server.png)

SDWAN アプライアンスを Azure VPN ゲートウェイに置き換えることもできます。 Azure VPN ゲートウェイと ExpressRoute はフル マネージドされているため、ルート交換を有効にするだけで、2 つのオンプレミス ネットワークが相互に通信できるようになります。

> [!IMPORTANT] 
> Azure VPN ゲートウェイは、[**アクティブ/アクティブ**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) モードで構成する必要があります。
>

![ルート サーバーで構成された ExpressRoute と VPN ゲートウェイを示す図。](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>次のステップ

- [Azure Route Server](route-server-faq.md) に関する詳細情報を確認します。
- [Azure Route Server を構成する](quickstart-configure-route-server-powershell.md)方法を確認します。
- [Azure ExpressRoute と Azure VPN の共存](../expressroute/expressroute-howto-coexist-resource-manager.md)に関する詳細情報を確認します。
