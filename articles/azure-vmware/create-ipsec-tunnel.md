---
title: Azure VMware Solution への IPSec トンネルを作成する
description: Virtual WAN ハブを作成して、Azure VMware Solution への IPSec トンネルを確立する方法について説明します。
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 21df674862b65ef6573a8a3fcfd7538b1053f04e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491848"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Azure VMware Solution への IPSec トンネルを作成する

この記事では、Microsoft Azure Virtual WAN ハブで終端する VPN (IPsec IKE v1 および IKE v2) サイト間トンネルを確立する手順について説明します。 パブリック IP アドレスがアタッチされた Azure Virtual WAN ハブと VPN ゲートウェイを作成します。 次に、Azure ExpressRoute ゲートウェイを作成し、Azure VMware Solution エンドポイントを確立します。 また、ポリシーベースの VPN オンプレミス設定を有効にする方法についても説明します。 

## <a name="topology"></a>トポロジ

![VPN サイト間トンネル アーキテクチャを示す図。](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

Azure Virtual ハブには、Azure VMware Solution ExpressRoute ゲートウェイとサイト間 VPN ゲートウェイが含まれています。 これにより、オンプレミスの VPN デバイスは Azure VMware Solution エンドポイントに接続されます。

## <a name="before-you-begin"></a>開始する前に

サイト間 VPN トンネルを作成するには、オンプレミスの VPN デバイスで終端する公開 IP アドレスを作成する必要があります。

## <a name="create-a-virtual-wan-hub"></a>Virtual WAN のハブを作成する

1. Azure portal で、**仮想 WAN** を探します。 **[+追加]** を選択します。 [WAN の作成] ページが開きます。  

2. **[WAN の作成]** ページで必要なフィールドを入力し、 **[確認と作成]** を選択します。
   
   | フィールド | 値 |
   | --- | --- |
   | **サブスクリプション** | 値には、リソース グループが属しているサブスクリプションが事前に設定されています。 |
   | **リソース グループ** | 仮想 WAN はグローバル リソースであり、特定のリージョンに限定されません。  |
   | **リソース グループの場所** | Virtual WAN ハブを作成するには、リソース グループの場所を設定する必要があります。  |
   | **名前** |   |
   | **Type** | **[Standard]** を選択します。これにより、VPN ゲートウェイのトラフィック以外も許可されます。  |

   :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="Azure portal の [WAN の作成] ページを示すスクリーンショット。":::

3. Azure portal で、前の手順で作成した仮想 WAN を選択し、 **[仮想ハブの作成]** を選択し、必要なフィールドを入力して、 **[次へ: サイト間]** を選択します。 

   | フィールド | 値 |
   | --- | --- |
   | **リージョン** | 管理の観点から、リージョンの選択は必須です。  |
   | **名前** |    |
   | **ハブのプライベート アドレス空間** | `/24` (最小) を使用してサブネットを入力します。  |

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="[仮想ハブを作成する] ページを示すスクリーンショット。":::

4. **[サイト対サイト]** タブで、 **[ゲートウェイ スケール ユニット]** ドロップダウンから合計スループットを設定して、サイト間ゲートウェイを定義します。 

   >[!TIP]
   >1 つのスケール ユニット = 500 Mbps。 スケール ユニットは冗長性のためにペアになっており、それぞれが 500 Mbps をサポートしています。
  
5. **[ExpressRoute]** タブで ExpressRoute ゲートウェイを作成します。 

   >[!TIP]
   >スケール ユニットの値は 2 Gbps です。 

    各ハブの作成には約 30 分かかります。 

## <a name="create-a-vpn-site"></a>VPN サイトを作成する 

1. Azure portal の **[最近のリソース]** で、前のセクションで作成した仮想 WAN を選択します。

2. 仮想ハブの **[概要]** で、 **[接続]**  >  **[VPN (Site-to-site)]\(VPN (サイト間)\)** を選択し、 **[VPN サイトの新規作成]** を選択します。

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="[VPN (Site-to-site)]\(VPN (サイト間)\) と [VPN サイトの新規作成] が選択されている仮想ハブの [概要] ページのスクリーンショット。":::  
 
3. **[基本]** タブで、必要なフィールドを入力し、 **[次へ: リンク]** を選択します。 

   | フィールド | 値 |
   | --- | --- |
   | **リージョン** | 前のセクションで指定したものと同じリージョン。  |
   | **名前** |  |
   | **デバイス ベンダー** |  |
   | **Border Gateway Protocol** | **[有効]** に設定して、Azure VMware Solution とオンプレミス サーバーの両方がトンネルを介してルートのアドバタイズを実行するようにします。 無効にした場合、アドバタイズする必要のあるサブネットは手動で保守する必要があります。 サブネットが欠落している場合、HCX はサービス メッシュの形成に失敗します。 詳細については、「[BGP と Azure VPN Gateway について](../vpn-gateway/vpn-gateway-bgp-overview.md)」を参照してください。 |
   | **プライベート アドレス空間**  | オンプレミスの CIDR ブロックを入力します。  これは、トンネルを介してオンプレミスにバインドされているすべてのトラフィックをルーティングするために使用されます。  CIDR ブロックは、BGP を有効にしない場合にのみ必要です。 |
   | **接続先** |   |

4. **[リンク]** タブで、必須フィールドに入力し、 **[確認と作成]** を選択します。 リンクとプロバイダーの名前を指定すると、ハブの一部として最終的に作成される可能性のあるゲートウェイの数がいくつになっても区別できます。 BGP と自律システム番号 (ASN) は、組織内で一意である必要があります。
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a>(省略可能) ポリシーベースの VPN サイト間トンネル用の VPN サイトの定義

このセクションは、ポリシーベースの VPN にのみ適用されます。 ポリシーベース (または静的なルートベース) の VPN 設定は、ほとんどの場合、オンプレミスの VPN デバイスの機能によって決まります。 オンプレミスと Azure VMware Solution のネットワークを指定する必要があります。 Azure Virtual WAN ハブを使用する Azure VMware Solution の場合、"*任意*" のネットワークを選択することはできません。 そうではなく、関連するすべてのオンプレミスおよび Azure VMware Solution Virtual WAN ハブの範囲を指定する必要があります。 このようなハブの範囲は、ポリシーベースの VPN トンネル オンプレミス エンドポイントの暗号化ドメインを指定するために使用されます。 Azure VMware Solution 側では、ポリシーベースのトラフィック セレクター インジケーターを有効にすることのみが必要です。 

1. Azure portal で、お使いの仮想 WAN ハブ サイトに移動します。 **[接続]** の **[VPN (Site to site)]\(VPN (サイト間)\)** を選択します。

2. VPN サイト名、右端の省略記号 (...)、 **[このハブへの VPN 接続の編集]** の順に選択します。
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="[このハブへの VPN 接続の編集] にアクセスするために選択された省略記号を示している Azure の仮想 WAN ハブ サイト用のページのスクリーンショット。" lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. VPN サイトとハブ間の接続を編集し、 **[保存]** を選択します。
   - インターネット プロトコル セキュリティ (IPSec) で **[カスタム]** を選択します。
   - ポリシーベースのトラフィック セレクターを使用して **[有効にする]** を選択します
   - **[IKE フェーズ 1]** と **[IKE フェーズ 2 (ipsec)]** の詳細を指定します。 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="[VPN 接続を編集する] ページのスクリーンショット。"::: 
 
   ポリシーベースの暗号化ドメインの一部であるトラフィック セレクターまたはサブネットは、次のようになります。
    
   - 仮想 WAN ハブ /24
   - Azure VMware Solution のプライベート クラウド /22
   - 接続されている Azure 仮想ネットワーク (存在する場合)

## <a name="connect-your-vpn-site-to-the-hub"></a>VPN サイトをハブに接続する

1. VPN サイト名を選択して、 **[VPN サイトの接続]** を選択します。 
1. **[事前共有キー]** フィールドに、オンプレミス エンドポイントに対して以前に定義したキーを入力します。 

   >[!TIP]
   >以前に定義したキーがない場合は、このフィールドは空白のままでかまいません。 キーが自動的に生成されます。 
 
   >[!IMPORTANT]
   >ハブにファイアウォールを展開していて、それがそのトンネルを介した接続の次ホップである場合にのみ、 **[既定のルートを伝達する]** を有効にします。

1. **[接続]** を選択します。 接続状態画面にトンネル作成の状態が表示されます。

2. Virtual WAN 概要に移動し、VPN サイト ページを開いて、オンプレミス エンドポイントの VPN 構成ファイルをダウンロードします。  

3. Virtual WAN ハブで Azure VMware Solution ExpressRoute に修正プログラムを適用します。 この手順を実行するには、最初にプライベート クラウドを作成する必要があります。

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Virtual WAN ハブで Azure VMware Solution と VPN ゲートウェイをリンクします。 
   1. Azure portal で、以前に作成した仮想 WAN を開きます。 
   1. 作成した Virtual WAN ハブを選択し、左側のペインで **[ExpressRoute]** を選択します。 
   1. **[+ 認可キーを利用する]** を選択します。

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="プライベート クラウドの [ExpressRoute] ページで [認可キーを利用する] が選択されているスクリーンショット。":::

   1. 承認キーを [承認キー] フィールドに貼り付けます。
   1. ExpressRoute ID を **[ピア回線の URI]** フィールドに貼り付けます。 
   1. **[この ExpressRoute 回線をハブに自動的に関連付けます]** を選択します。 
   1. **[追加]** を選択してリンクを確立します。 

5. 接続をテストするために、[NSX-T セグメントを作成](./tutorial-nsx-t-network-segment.md)してネットワーク上に VM をプロビジョニングします。 オンプレミスと Azure VMware Solution の両方のエンドポイントに ping を実行します。
