---
title: Azure VMware Solution (AVS) ワークロードを分散するために Traffic Manager をデプロイする
description: Traffic Manager を Azure VMware Solution (AVS) と統合して、異なるリージョンにある複数のエンドポイント間でアプリケーションのワークロードを分散する方法について説明します。
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: dc3107c6a237273b103fe52a91b3569d9e694e1a
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642530"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-avs-workloads"></a>Azure VMware Solution (AVS) ワークロードを分散するために Traffic Manager をデプロイする

この記事では、Traffic Manager を Azure VMware Solution (AVS) と統合して、複数のエンドポイント間でアプリケーションのワークロードを分散する方法について説明します。 ここでは、複数の AVS リージョン (米国西部、西ヨーロッパ、オンプレミス (米国東部)) にまたがる 3 つのアプリケーション ゲートウェイ間で、Traffic Manager によってトラフィックが転送されるシナリオについて見ていきます。 

Azure Traffic Manager は、世界中の Azure リージョン間でサービスへのトラフィックを最適に配分する DNS ベースのトラフィック ロード バランサーです。 Azure で実行されているワークロードと外部パブリック エンドポイントの両方の間でアプリケーション トラフィックを負荷分散します。 Traffic Manager の詳細については、[「Traffic Manager について」](../traffic-manager/traffic-manager-overview.md)を参照してください。

最初に[前提条件](#prerequisites)をご確認ください。その後、以下を行うための手順について説明します。

> [!div class="checklist"]
> * アプリケーション ゲートウェイの構成を確認する
> * NSX-T セグメントの構成を確認する
> * Traffic Manager プロファイルを作成する
> * Traffic Manager プロファイルに外部エンドポイントを追加する

## <a name="topology"></a>トポロジ

次の図に示すように、Azure Traffic Manager は、リージョン エンドポイント間の DNS レベルでアプリケーションの負荷分散を提供します。 アプリケーション ゲートウェイには、バックエンド プール メンバーが IIS サーバーとして構成されており、AVS 外部エンドポイントとして参照されています。

2 つの AVS プライベート クラウド リージョン、米国西部と西ヨーロッパ、およびオンプレミス サーバー (米国東部) 間の仮想ネットワーク経由の接続では、ExpressRoute ゲートウェイが使用されます。   

![AVS を使用した Traffic Manager 統合](media/traffic-manager/traffic-manager-topology.png)
 
## <a name="prerequisites"></a>前提条件

- 異なる AVS リージョン (米国西部、西ヨーロッパ、およびオンプレミス) で実行されている Microsoft IIS サーバーとして構成された 3 つの仮想マシン。 

- 外部エンドポイントが米国西部、西ヨーロッパ、およびオンプレミスにあるアプリケーション ゲートウェイ。

- 検証のためインターネットに接続されているホスト。 

## <a name="verify-configuration-of-your-application-gateways"></a>アプリケーション ゲートウェイの構成を確認する

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) は、Web アプリケーションに対するトラフィックを管理できるレイヤー 7 の Web トラフィック ロード バランサーです。 Application Gateway の詳細については、「[Azure Application Gateway とは](../application-gateway/overview.md)」を参照してください。 

このシナリオでは、3 つのアプリケーション ゲートウェイ インスタンスが外部の AVS エンドポイントとして構成されています。 このアプリケーション ゲートウェイには、受信した第 7 層の要求の負荷を分散するために、バックエンド プール メンバーとして構成された AVS 仮想マシンが備わっています。 (バックエンド プールとして AVS の仮想マシンを使用して、Application Gateway を構成する方法については、「[Azure Application Gateway を使用して Azure VMware Solution 上の Web アプリを保護する](protect-avs-web-apps-with-app-gateway.md)」を参照してください。)  

次の手順では、アプリケーション ゲートウェイの正しい構成を確認します。

1. Azure portal を開き、 **[アプリケーション ゲートウェイ]** を選択して、現在のアプリケーション ゲートウェイの一覧を表示します。 

    このシナリオでは、次の 3 つのアプリケーション ゲートウェイを構成しました。
    - AVS-GW-WUS
    - AVS-GW-EUS (オンプレミス)
    - AVS-GW-WEU

    :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="アプリケーション ゲートウェイの一覧。" lightbox="media/traffic-manager/app-gateways-list-1.png":::

2. 以前にデプロイしたアプリケーション ゲートウェイのいずれかを選択します。 アプリケーション ゲートウェイのさまざまな情報を示すウィンドウが開きます。 いずれかのバックエンド プールの構成を確認するには、 **[バックエンド プール]** を選択します。

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="アプリケーション ゲートウェイの詳細。" lightbox="media/traffic-manager/backend-pool-config.png":::
 
3. この例では、1 つの仮想マシン バックエンド プール メンバーが、IP アドレスが 172.29.1.10 の Web サーバーとして構成されています。
 
     :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="バックエンド プールの編集。":::

    同様に、他のアプリケーション ゲートウェイとバックエンド プール メンバーの構成を確認できます。 

## <a name="verify-configuration-of-the-nsx-t-segment"></a>NSX-T セグメントの構成を確認する

NSX-T Manager で作成されたネットワーク セグメントは、vCenter 内の仮想マシン用のネットワークとして使用されます。 詳細については、チュートリアルの「[Azure VMware Solution (AVS) で NSX-T ネットワーク セグメントを作成する](tutorial-nsx-t-network-segment.md)」を参照してください。

このシナリオでは、バックエンド プール メンバーの仮想マシンが接続されている AVS 環境で、NSX-T セグメントを構成します。

1. 構成されているセグメントを表示するには、 **[セグメント]** を選択します。 この例では、Contoso-segment1 が Contoso-T01 ゲートウェイ (第 1 層のフレキシブル ルーター) に接続されていることがわかります。

    :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="NSX-T Manager のセグメント プロファイル。":::    

2. **[Tier-1 Gateways]\(第 1 層ゲートウェイ\)** を選択して、リンクされたセグメントの数を含む第 1 層ゲートウェイの一覧を表示します。 Contoso-T01 にリンクされているセグメントを選択します。 第 01 層ルーターで構成されている論理インターフェイスを示すウィンドウが開きます。 これは、セグメントに接続されているバックエンド プール メンバー仮想マシンへのゲートウェイとして機能します。

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="リンクされたセグメント。":::    

3. VM vSphere クライアントで、仮想マシンを選択して詳細を表示します。 その IP アドレスは、前のセクションの手順 3 で確認したもの (172.29.1.10) と一致します。

    :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="仮想マシンの詳細。":::    

4. 仮想マシンを選択し、 **[アクション] > [設定の編集]** の順に選択して、NSX-T セグメントへの接続を確認します。

## <a name="create-your-traffic-manager-profile"></a>Traffic Manager プロファイルを作成する

1. [Azure Portal](https://rc.portal.azure.com/#home) にログインします。 **[Azure サービス] > [ネットワーク]** にある **[Traffic Manager プロファイル]** を選択します。

2. 新しい Traffic Manager プロファイルを作成するには、 **[+ 追加]** を選択します。
 
3. プロファイル名、ルーティング方法 (このシナリオでは重みを使用します。詳細については、「[Traffic Manager のルーティング方法](../traffic-manager/traffic-manager-routing-methods.md)」を参照してください)、サブスクリプション、およびリソース グループを指定し、 **[作成]** を選択します。

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Traffic Manager プロファイルに外部エンドポイントを追加する

1. 検索結果のウィンドウから Traffic Manager プロファイルを選択し、 **[エンドポイント]** を選択してから、 **[+ 追加]** をクリックします。

2. 次の必要な詳細を入力します。種類、名前、完全修飾ドメイン名 (FQDN) または IP、および重み (このシナリオでは、各エンドポイントに 1 の重みを割り当てます)。 **[追加]** を選択します。

    :::image type="content" source="media/traffic-manager/traffic-manager-profile.png" alt-text="Traffic Manager プロファイル - エンドポイントの追加。":::  
 
    これにより、外部エンドポイントが作成されます。 モニターの状態は、 **[オンライン]** である必要があります。 

    同じ手順を繰り返して、外部エンドポイントをもう 2 つ (別のリージョンに 1 つとオンプレミスに 1 つ) を作成します。 作成すると、3 つすべてが Traffic Manager プロファイルに表示され、3 つすべての状態が **[オンライン]** と表示されるはずです。

3. **[概要]** を選択します。 **[DNS 名]** の下にある URL をコピーします。

    :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="DNS 名がある Traffic Manager の概要。"::: 

4. DNS 名の URL をブラウザーに貼り付けます。 次のスクリーンショットは、西ヨーロッパ リージョンへのトラフィックを示しています。

    :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="西ヨーロッパにルーティングされているトラフィック。"::: 

5. ブラウザーを更新します。 次のスクリーンショットは、米国西部リージョンにあるバックエンド プール メンバーの別のセットを指しているトラフィックを示しています。

    :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="米国西部にルーティングされているトラフィック。"::: 

6. ブラウザーを、最新の情報に再度更新します。 次のスクリーンショットは、オンプレミスのバックエンド プール メンバーの最後のセットを指しているトラフィックを示しています。

    :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="オンプレミスにルーティングされているトラフィック。":::

## <a name="next-steps"></a>次のステップ

各項目の詳細情報

- [Azure VMware Solution (AVS) で Azure Application Gateway を使用する](protect-avs-web-apps-with-app-gateway.md)
- [Traffic Manager のルーティング方法](../traffic-manager/traffic-manager-routing-methods.md)
- [Azure の負荷分散サービスを組み合わせる](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Traffic Manager のパフォーマンスを測定する](../traffic-manager/traffic-manager-performance-considerations.md)
