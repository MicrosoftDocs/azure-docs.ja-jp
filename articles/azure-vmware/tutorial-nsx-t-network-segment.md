---
title: チュートリアル:Azure VMware Solution で NSX-T ネットワーク セグメントを作成する
description: このチュートリアルでは、vCenter 内の VM に使用される NSX-T ネットワーク セグメントを作成しました
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: cee65211cbef25ec029c68888bc8e6059f7c7896
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750466"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>チュートリアル:Azure VMware Solution で NSX-T ネットワーク セグメントを作成する

NSX-T Manager で作成されたネットワーク セグメントは、vCenter 内の仮想マシン (VM) 用のネットワークとして使用されます。 vCenter に作成された VM は、NSX-T に作成されたネットワーク セグメントに配置されて、vCenter に表示されるようになります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * NSX-T Manager を操作してネットワーク セグメントを追加する
> * 新しいネットワーク セグメントを追加する
> * vCenter で新しいネットワーク セグメントを監視する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、vCenter と NSX-T Manager の管理インターフェイスへのアクセスが可能な Azure VMware Solution プライベート クラウドが必要です。 「[チュートリアル: Azure で VMware プライベート クラウド用のネットワークを構成する](tutorial-configure-networking.md)」を参照してください。

## <a name="provision-a-network-segment-in-nsx-t"></a>NSX-T でネットワーク セグメントをプロビジョニングする

1. プライベート クラウドの vCenter で、 **[SDDC-Datacenter]\(SDDC データセンター\) > [Networks]\(ネットワーク\)** の順に選択します。まだネットワークがないことに注目してください。

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="プライベート クラウドの vCenter で、[SDDC-Datacenter]\(SDDC データセンター\) > [Networks]\(ネットワーク\) の順に選択します。まだネットワークがないことに注目してください。":::

1. プライベート クラウドの NSX-T Manager で、 **[Networking]\(ネットワーク\)** を選択します。

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="プライベート クラウドの NSX-T Manager で、[Networking]\(ネットワーク\) を選択します。":::

1. **[Segments]\(セグメント\)** を選択します。

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="[Network Overview]\(ネットワークの概要\) ページで [Segments]\(セグメント\) を選択する。":::

1. NSX-T のセグメントの概要ページで、 **[ADD SEGMENT]\(セグメントの追加\)** を選択します。 プライベート クラウドのプロビジョニングの一環として 3 つのセグメントが作成されますが、VM には使用できません。  そのために新しいネットワーク セグメントを追加する必要があります。

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="NSX-T のセグメントの概要ページで、[ADD SEGMENT]\(セグメントの追加\) を選択します。":::

1. セグメントに名前を付け、 **[Connected Gateway]\(接続されているゲートウェイ\)** に、事前構成済みの Tier1 ゲートウェイ (TNTxx-T1) を選択します。 **[Type]\(種類\)** は [Flexible]\(柔軟\) のままにし、事前構成済みのオーバーレイ **トランスポート ゾーン** (TNTxx-OVERLAY-TZ) を選択して、[Set Subnets]\(サブネットの設定\) を選択します。 このセクションのその他すべての設定および **[PORTS]\(ポート\)** と **[SEGMENT PROFILES]\(セグメント プロファイル\)** は既定の設定のままでかまいません。

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="セグメント名、接続されているゲートウェイ、種類、トランスポート ゾーンを設定し、[Set Subnets]\(サブネットの設定\) を選択します。":::

1. 新しいセグメントのゲートウェイの IP アドレスを設定し、 **[ADD]\(追加\)** を選択します。 使用する IP アドレスは、重複しない RFC1918 アドレス ブロック上に存在する必要があります。これで新しいセグメント上の VM に確実に接続することができます。

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="新しいセグメントのゲートウェイの IP アドレスを設定し、[ADD]\(追加\) を選択します。":::

1. **[APPLY]\(適用\)** を選択して新しいネットワーク セグメントを適用し、 **[SAVE]\(保存\)** を使用して構成を保存します。

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="[APPLY]\(適用\) を使用して、新しいネットワーク セグメントを NSX-T 構成に適用する。":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="[SAVE]\(保存\) を使用して、新しいネットワーク セグメントを NSX-T 構成に保存する。":::

1. これで新しいネットワーク セグメントが作成されました。ここで、 **[NO]\(いいえ\)** を選択して、セグメントの構成を続行するのを辞退します。

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="[NO]\(いいえ\) を選択して、新しく作成されたネットワーク セグメントのさらなる構成を辞退する。":::

1. NSX-T に新しいネットワーク セグメントが存在することを確認します。これには、 **[Networking]\(ネットワーク\) > [Segments]\(セグメント\)** を選択し、新しいセグメント (この場合は "ls01") が一覧に表示されていることを確認してください。

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="新しいネットワーク セグメントが NSX-T に存在することを確認する。":::

1. 新しいネットワーク セグメントが vCenter に存在することを確認します。これには、 **[Networking]\(ネットワーク\) > [SDDC-Datacenter]\(SDDC データセンター\)** を選択し、新しいセグメント (この場合は "ls01") が一覧に表示されていることを確認してください。

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="新しいネットワーク セグメントが vCenter に存在することを確認する。":::

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、vCenter 内の VM に使用される NSX-T ネットワーク セグメントを作成しました。 これで、「[チュートリアル: Azure VMware Solution で VM をデプロイするためのコンテンツ ライブラリを作成する](tutorial-deploy-vm-content-library.md)」を使用して、vCenter にコンテンツ ライブラリを作成し、このチュートリアルで作成したネットワークに VM をプロビジョニングすることができます。

<!-- LINKS - external-->

<!-- LINKS - internal -->
