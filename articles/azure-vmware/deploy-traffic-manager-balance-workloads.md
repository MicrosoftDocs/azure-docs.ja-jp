---
title: Azure VMware Solution ワークロードを分散するために Traffic Manager をデプロイする
description: Traffic Manager を Azure VMware Solution と統合して、異なるリージョンにある複数のエンドポイント間でアプリケーションのワークロードを分散する方法について説明します。
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 46570c5a61fc0a641d83126fd0f8ef35b3dc42cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988599"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Azure VMware Solution ワークロードを分散するために Traffic Manager をデプロイする

この記事では、[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) を Azure VMware Solution と統合する方法の手順について説明します。 この統合により、複数のエンドポイント間でアプリケーションのワークロードが分散されます。 また、この記事では、複数の Azure VMware Solution リージョンにまたがる 3 つの [Azure Application Gateway](../application-gateway/overview.md) 間でトラフィックを誘導するように Traffic Manager を構成する手順についても説明します。 

このゲートウェイには、受信した第 7 層の要求の負荷を分散するために、バックエンド プール メンバーとして構成された Azure VMware Solution 仮想マシン (VM) が備わっています。 詳細については、「[Azure Application Gateway を使用して Azure VMware Solution 上の Web アプリを保護する](protect-azure-vmware-solution-with-application-gateway.md)」を参照してください

図は、Azure Traffic Manager で、リージョン エンドポイント間の DNS レベルでアプリケーションの負荷分散を提供する方法を示しています。 ゲートウェイには、バックエンド プール メンバーが IIS サーバーとして構成されており、Azure VMware Solution 外部エンドポイントとして参照されています。 2 つのプライベート クラウド リージョン間の仮想ネットワーク経由の接続では、ExpressRoute ゲートウェイが使用されます。   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Traffic Manager と Azure VMware Solution との統合のアーキテクチャの図" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

開始する前に、最初に[前提条件](#prerequisites)を確認してください。その後、以下を行うための手順について説明します。

> [!div class="checklist"]
> * アプリケーション ゲートウェイと NSX-T セグメントの構成を確認する
> * Traffic Manager プロファイルを作成する
> * Traffic Manager プロファイルに外部エンドポイントを追加する

## <a name="prerequisites"></a>[前提条件]

- 異なる Azure VMware Solution リージョンで実行されている Microsoft IIS サーバーとして構成された 3 つの VM: 
   - 米国西部
   - 西ヨーロッパ
   - 米国東部 (オンプレミス) 

- 前述の Azure VMware Solution リージョンに外部エンドポイントがあるアプリケーション ゲートウェイ。

- 検証のためインターネットに接続されているホスト。 

- [Azure VMware Solution で作成された NSX-T ネットワーク セグメント](tutorial-nsx-t-network-segment.md)。

## <a name="verify-your-application-gateways-configuration"></a>アプリケーション ゲートウェイの構成を確認する

次の手順では、アプリケーション ゲートウェイの構成を確認します。

1. Azure portal で、 **[アプリケーション ゲートウェイ]** を選択して、現在のアプリケーション ゲートウェイの一覧を表示します。

   - AVS-GW-WUS
   - AVS-GW-EUS (オンプレミス)
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="構成済みのアプリケーション ゲートウェイの一覧を示す [アプリケーション ゲートウェイ] ページのスクリーンショット。" lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. 以前にデプロイしたアプリケーション ゲートウェイのいずれかを選択します。 

   アプリケーション ゲートウェイのさまざまな情報を示すウィンドウが開きます。 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="選択されたアプリケーション ゲートウェイの詳細を示す [アプリケーション ゲートウェイ] ページのスクリーンショット。" lightbox="media/traffic-manager/backend-pool-config.png":::

1. いずれかのバックエンド プールの構成を確認するには、 **[バックエンド プール]** を選択します。 IP アドレスが 172.29.1.10 の Web サーバーとして構成されている 1 つの VM バックエンド プール メンバーが表示されます。
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="ターゲット IP アドレスが強調表示されている [バックエンド プールの編集] ページのスクリーンショット。":::

1. 他のアプリケーション ゲートウェイとバックエンド プール メンバーの構成を確認します。 

## <a name="verify-the-nsx-t-segment-configuration"></a>NSX-T セグメントの構成を確認する

次の手順では、Azure VMware Solution 環境での NSX-T セグメントの構成を確認します。

1. 構成されているセグメントを表示するには、 **[セグメント]** を選択します。  Contoso-T01 ゲートウェイ (第 1 層のフレキシブル ルーター) に接続されている Contoso-segment1 が表示されます。

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="NSX-T Manager のセグメント プロファイルを示すスクリーンショット。" lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. **[第 1 層ゲートウェイ]** を選択して、リンクされたセグメントの数を含む第 1 層ゲートウェイの一覧を表示します。 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="選択されたセグメントのゲートウェイ アドレスを示すスクリーンショット。":::    

1. Contoso-T01 にリンクされているセグメントを選択します。 第 01 層ルーターで構成されている論理インターフェイスを示すウィンドウが開きます。 これは、セグメントに接続されているバックエンド プール メンバー VM へのゲートウェイとして機能します。

1. vSphere クライアントで、VM を選択して詳細を表示します。 

   >[!NOTE]
   >この IP アドレスは、前のセクションで Web サーバーとして構成されている VM バックエンド プール メンバーと一致します (172.29.1.10)。

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="vSphere クライアントの VM の詳細を示すスクリーンショット。" lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. VM を選択し、 **[アクション] > [設定の編集]** の順に選択して、NSX-T セグメントへの接続を確認します。

## <a name="create-your-traffic-manager-profile"></a>Traffic Manager プロファイルを作成する

1. [Azure portal](https://rc.portal.azure.com/#home) にサインインします。 **[Azure サービス] > [ネットワーク]** にある **[Traffic Manager プロファイル]** を選択します。

2. 新しい Traffic Manager プロファイルを作成するには、 **[+ 追加]** を選択します。
 
3. 以下の情報を入力した後、 **[作成]** を選びます。

   - [プロファイル名]
   - ルーティング方法 ([重み付け](../traffic-manager/traffic-manager-routing-methods.md)を使用
   - サブスクリプション
   - Resource group

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Traffic Manager プロファイルに外部エンドポイントを追加する

1. 検索結果のウィンドウから Traffic Manager プロファイルを選択し、 **[エンドポイント]** を選択してから、 **[+ 追加]** をクリックします。

1. さまざまなリージョンの外部エンドポイントごとに、必要な詳細を入力し、 **[追加]** を選択します。 
   - Type
   - 名前
   - 完全修飾ドメイン名 (FQDN) または IP
   - 重み (各エンドポイントに重み 1 を割り当てます)。 

   作成されると、3 つすべてが Traffic Manager プロファイルに表示されます。 3 つのすべてのモニターの状態が **[オンライン]** である必要があります。

3. **[概要]** を選択し、 **[DNS 名]** の下にある URL をコピーします。

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="DNS 名が強調表示されている Traffic Manager エンドポイントの概要を示すスクリーンショット。" lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. DNS 名の URL をブラウザーに貼り付けます。 スクリーンショットは、西ヨーロッパ リージョンへのトラフィックを示しています。

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="西ヨーロッパへルーティングされたトラフィックを示すブラウザー ウィンドウのスクリーンショット。"::: 

5. ブラウザーを更新します。 スクリーンショットは、米国西部リージョンにあるバックエンド プール メンバーの別のセットを指しているトラフィックを示しています。

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="米国西部へルーティングされたトラフィックを示すブラウザー ウィンドウのスクリーンショット。"::: 

6. ブラウザーを、最新の情報に再度更新します。 スクリーンショットは、オンプレミスのバックエンド プール メンバーの最後のセットを指しているトラフィックを示しています。

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="オンプレミスへルーティングされたトラフィックを示すブラウザー ウィンドウのスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

Azure Traffic Manager と Azure VMware ソリューションの統合について理解したので、次のことについて確認する必要がある可能性があります。

- [Azure VMware Solution で Azure Application Gateway を使用する](protect-azure-vmware-solution-with-application-gateway.md)。
- [Traffic Manager のルーティング方法](../traffic-manager/traffic-manager-routing-methods.md)。
- [Azure の負荷分散サービスを組み合わせる](../traffic-manager/traffic-manager-load-balancing-azure.md)。
- [Traffic Manager のパフォーマンスを測定する](../traffic-manager/traffic-manager-performance-considerations.md)。
