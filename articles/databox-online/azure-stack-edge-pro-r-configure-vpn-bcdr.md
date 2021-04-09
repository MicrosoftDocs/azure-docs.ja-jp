---
title: Azure Stack Edge 仮想プライベート ネットワーク (VPN) に対して事業継続とディザスター リカバリー (BCDR) を構成する
description: Azure Stack Edge VPN に対して BCDR を構成する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: a35a7e5e5c7eccf006f18badad88656e8bc73453
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367692"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>Azure Stack Edge VPN に対して事業継続とディザスター リカバリーを構成する

[!INCLUDE [applies-to-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge デバイス上に構成された仮想プライベート ネットワーク (VPN) に対して事業継続とディザスター リカバリー (BCDR) を構成する方法について説明します。

この記事は、Azure Stack Edge Pro R と Azure Stack Edge Mini R デバイスの両方に適用されます。

## <a name="configure-failover-to-a-paired-region"></a>ペアのリージョンへのフェールオーバーを構成する

ご利用の Azure Stack Edge デバイスでは、Azure Storage などの他の Azure サービスも使用されます。 Azure Stack Edge デバイスで使用される特定の Azure サービスに対して BCDR を構成することができます。 Azure Stack Edge で使用されている Azure サービスを、そのペアのリージョンにフェールオーバーする場合、Azure Stack Edge デバイスは新しい IP アドレスに接続され、通信が二重に暗号化されなくなります。 

Azure Stack Edge デバイスでは分割トンネリングが使用され、ホーム リージョン (ご利用の Azure Stack Edge デバイスに関連付けられているリージョン) 内で構成されているすべてのデータとサービスが VPN トンネルを経由します。 Azure サービスをホーム リージョンの外部にあるペアのリージョンにフェールオーバーする場合、データは VPN 経由ではなくなり、二重に暗号化されなくなります。 

このシナリオでは、影響を受けるのは、通常、ほんの一握りの Azure サービスのみです。 この問題に対処するには、Azure Stack Edge VPN 構成内で次の変更を行う必要があります。

1. Azure Stack Edge 用 VPN の包括ルートにフェールオーバー Azure サービス IP 範囲を追加します。 これで、該当するサービスが VPN 経由でルーティングされるようになります。

    包括ルートを追加するには、サービス固有のルートを含む json ファイルをダウンロードする必要があります。 このファイルは、必ず新しいルートで更新してください。
2. 対応する Azure サービス IP 範囲を Azure ルート テーブルに追加します。
3. ファイアウォールにルートを追加します。

> [!NOTE]
>
> 1. Azure VPN ゲートウェイと Azure Virtual Network (VNET) のフェールオーバーについては、[災害によって障害が発生した Azure リージョンからの回復](#recover-from-a-failed-azure-region)に関するセクションをご覧ください。
> 2. Azure ルート テーブルに IP 範囲が追加されると、上限の 400 を超える可能性があります。 これが発生した場合は、[Azure リージョン間の移動](#move-from-an-azure-region-to-another)に関するセクションに記載のガイダンスに従う必要があります。

## <a name="recover-from-a-failed-azure-region"></a>障害が発生した Azure リージョンからの回復

地震などの大惨事によって Azure リージョン全体がフェールオーバーされる場合、そのリージョン内のすべての Azure サービス (Azure Stack Edge サービスを含む) がフェールオーバーされます。 複数のサービスがあることから、包括ルートは優に数百に及ぶ可能性があります。 Azure では、ルートの数が 400 に制限されています。 

リージョンのフェールオーバーが発生すると、仮想ネットワーク (Vnet) も新しいリージョンにフェールオーバーされます。仮想ネットワーク ゲートウェイ (VPN ゲートウェイ) も同様です。 この変更に対処するには、ご利用の Azure Stack Edge VPN 構成内で次の変更を行います。

1. ご利用の Vnet をターゲット リージョンに移動します。 詳細については、次を参照してください。[Azure portal を介して Azure 仮想ネットワークを別のリージョンに移動する](../virtual-network/move-across-regions-vnet-portal.md)
2. Vnet を移動したターゲット リージョンに新しい Azure VPN ゲートウェイをデプロイします。 詳細については、「[仮想ネットワーク ゲートウェイの作成](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)」をご覧ください。
3. VPN 接続で上記の VPN ゲートウェイを使用するように Azure Stack Edge VPN 構成を更新してから、ターゲット リージョンを選択して、VPN ゲートウェイを使用するルートを追加します。
4. クライアント アドレス プールも変更される場合は、受信 Azure ルート テーブルを更新します。 

## <a name="move-from-an-azure-region-to-another"></a>Azure リージョン間を移動する

ご利用の Azure Stack Edge を、ある場所から別の場所に移動することができます。 ご利用のデバイスがデプロイされている場所に最も近いリージョンを使用するには、そのデバイスを新しいホーム リージョンに合わせて構成する必要があります。 次の変更を行います。

1. 新しいリージョンの VPN ゲートウェイを使用するように Azure Stack Edge VPN 構成を更新してから、新しいリージョンを選択して VPN ゲートウェイを使用するルートを追加することができます。

## <a name="next-steps"></a>次の手順

[Azure Stack Edge デバイスをバックアップする](azure-stack-edge-gpu-prepare-device-failure.md)