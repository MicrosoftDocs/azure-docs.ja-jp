---
title: Azure Firewall を仮想 WAN ハブにインストールする
titleSuffix: Azure Virtual WAN
description: 仮想 WAN ハブ内で Azure Firewall を構成する手順
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 577340e485550e84941a33d82b58aa6ff1c933d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983660"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>仮想 WAN ハブ内で Azure Firewall を構成する

**セキュリティ保護付きハブ** は、Azure Firewall を備えた Azure Virtual WAN ハブです。 この記事では、Azure Virtual WAN ポータル ページから直接 Azure Firewall をインストールして、仮想 WAN ハブをセキュリティ保護付きハブに変換する手順について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事の手順では、1 つ以上のハブがある仮想 WAN が既にデプロイされていること想定します。

新しい仮想 WAN と新しいハブを作成するには、次の記事の手順に従います。

* [仮想 WAN を作成する](virtual-wan-site-to-site-portal.md#openvwan)
* [ハブを作成する](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>仮想ハブの表示

仮想 WAN の **[概要]** ページには、仮想ハブとセキュリティ保護付きハブの一覧が表示されます。 次の図は、セキュリティ保護付きハブがない仮想 WAN を示しています。

[ ![スクリーンショットには、仮想ハブの一覧を含む仮想 WAN の [概要] ページが示されています。](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>セキュリティ保護付きハブへの変換

1. 仮想 WAN の **[概要]** ページで、セキュリティ保護付きハブに変換するハブを選択します。 仮想ハブ ページに、Azure Firewall をこのハブにデプロイするための 2 つのオプションが表示されます。 どちらかのオプションを選択します。

   [ ![スクリーンショットには、仮想 WAN の [概要] ページが示されています。ここで、[Convert to secure hub]\(セキュリティ保護付きハブに変換\) または [Azure Firewall] を選択できます。](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. いずれかのオプションを選択すると、 **[Convert to secure hub]\(セキュリティ保護付きハブに変換\)** ページが表示されます。 変換するハブを選択し、ページの下部にある **[次へ:Azure Firewall]** を選択します。

   [ ![ハブを選択](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. ワークフローが完了したら、 **[確認]** を選択します。

   [ ![スクリーンショットには、[確認] が選択された [Convert to secure hub]\(セキュリティ保護付きハブに変換\) ペインが示されています。](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. ハブがセキュリティ保護付きハブに変換されたら、仮想 WAN の **[概要]** ページで確認できます。

   [ ![セキュリティ保護付きハブの表示](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>ハブ リソースの表示

仮想 WAN の **[概要]** ページで、セキュリティ保護付きハブを選択します。 ハブ ページには、Azure Firewall を含むすべての仮想ハブ リソースが表示されます。

[ ![ハブ リソースの表示](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

セキュリティ保護付きハブから Azure Firewall 設定を表示するには、 **[セキュリティ]** で、 **[Secured virtual hub settings]\(セキュリティ保護付き仮想ハブの設定\)** を選択します。
[ ![ハブの設定の表示](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>追加の設定の構成

仮想ハブ用の追加の Azure Firewall 設定を構成するには、**Azure Firewall Manager** へのリンクを選択します。 ファイアウォール ポリシーの詳細については、[Azure Firewall Manager](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub) に関するページを参照してください。

[ ![追加設定](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

ハブの **[概要]** ページに戻るには、次の図の矢印で示されているようなパスをクリックします。

[ ![概要に戻る](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
