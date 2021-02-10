---
title: マネージド ソリューションを使用して Azure と AWS の間に VPN を作成する
description: VM またはアプライアンスの代わりに、マネージド ソリューションを使用して Azure と AWS の間に VPN 接続を作成する方法について説明します。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: ricmmartins
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/22/2021
ms.author: ricmart
ms.openlocfilehash: a0655ce1d2e9939981bb4fd3280af80e359ea1e1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737746"
---
# <a name="create-a-vpn-connection-between-azure-and-aws-using-managed-solutions"></a>マネージド ソリューションを使用して Azure と AWS の間に VPN 接続を作成する

マネージド ソリューションを使用して Azure と AWS の間で接続を確立できます。 以前は、レスポンダーとして機能するアプライアンスまたは VM を使用する必要がありました。 現在は、仮想マシンなどの IaaS リソースの管理について心配することなく、AWS 仮想プライベート ゲートウェイを Azure VPN Gateway に直接接続できます。 この記事は、マネージド ソリューションのみを使用して、Azure と AWS の間に VPN 接続を作成するのに役立ちます。

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/diagram.png" alt-text="アーキテクチャの図":::

## <a name="configure-azure"></a>Azure を構成する

### <a name="configure-a-virtual-network"></a>仮想ネットワークを構成する

仮想ネットワークを構成する。 手順については、[仮想ネットワークのクイック スタート](../virtual-network/quick-create-portal.md)に関する記事を参照してください。

この記事では、例として次の値が使用されています。

* **リソース グループ:** rg-azure-aws
* **[リージョン]:** 米国東部
* **仮想ネットワーク名:** vnet-azure
* **IPv4 アドレス空間:** 172.10.0.0/16
* **サブネット名:** subnet-01
* **サブネットのアドレス範囲:** 172.10.1.0/24

### <a name="create-a-vpn-gateway"></a>VPN ゲートウェイの作成

仮想ネットワークの VPN ゲートウェイを作成します。 手順については、「[チュートリアル:VPN ゲートウェイを作成して管理する](tutorial-create-gateway-portal.md)」を参照してください。

この記事では、例として次の値と設定が使用されています。

* **ゲートウェイ名:** vpn-azure-aws
* **[リージョン]:** 米国東部
* **ゲートウェイの種類:** VPN
* **VPN の種類:** ルート ベース
* **SKU:** VpnGw1
* **世代:** 第 1 世代
* **仮想ネットワーク:** ゲートウェイを作成する VNet を指定する必要があります。
* **ゲートウェイ サブネットのアドレス範囲:** 172.10.0.0/27
* **[パブリック IP アドレス]** : 新規作成
* **パブリック IP アドレス名:** pip-vpn-azure-aws
* **アクティブ/アクティブ モードの有効化:** Disable
* **BGP の構成:** Disable

例:

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/summary.png" alt-text="仮想ネットワーク ゲートウェイの概要":::

## <a name="configure-aws"></a>AWS の構成

1. Virtual Private Cloud (VPC) を作成します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpc.png" alt-text="VPC 情報を作成する":::

1. VPC (仮想ネットワーク) 内にサブネットを作成します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-subnet-vpc.png" alt-text="サブネットを作成する":::

1. Azure VPN Gateway のパブリック IP アドレスを指すカスタマー ゲートウェイを作成します。 **カスタマー ゲートウェイ** は、カスタマー ゲートウェイ デバイス (この場合は Azure VPN Gateway) に関する AWS の情報を含む AWS リソースです。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-customer-gw.png" alt-text="カスタマー ゲートウェイを作成する":::

1. 仮想プライベート ゲートウェイを作成します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpg.png" alt-text="仮想プライベート ゲートウェイを作成する":::

1. 仮想プライベート ゲートウェイを VPC にアタッチします。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-vpg.png" alt-text="VPG を VPC にアタッチする":::

1. VPC を選択します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attaching-vpg.png" alt-text="[アタッチ]":::

1. サイト間 VPN 接続を作成します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpn-connection.png" alt-text="VPN 接続を作成する":::

1. [ルーティング オプション] を **[静的]** に設定し、Azure subnet-01 プレフィックス **(172.10.1.0/24)** をポイントします。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/set-static-route.png" alt-text="静的ルートを設定する":::

1. オプションを入力した後、接続を **作成** します。

1. 構成ファイルをダウンロードします。 Azure は有効なオプションではないため、正しい構成をダウンロードするために、[ベンダー]、[プラットフォーム]、[ソフトウェア] を **[Generic]\(汎用\)** に変更します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/download-config.png" alt-text="構成をダウンロードする":::

1. 構成ファイルには、AWS によって作成された 2 つの IPsec トンネルそれぞれの事前共有キーとパブリック IP アドレスが含まれます。

   **トンネル 1**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1.png" alt-text="トンネル 1":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1-config.png" alt-text="トンネル 1 の構成":::

   **トンネル 2**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2.png" alt-text="トンネル 2":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2-config.png" alt-text="トンネル 2 の構成":::

1. トンネルが作成されると、この例のような内容が表示されます。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-details.png" alt-text="AWS VPN 接続の詳細":::

## <a name="create-local-network-gateway"></a>ローカル ネットワーク ゲートウェイの作成

Azure では、ローカル ネットワーク ゲートウェイは、通常、オンプレミスの場所を表す Azure リソースです。 これには、オンプレミスの VPN デバイスへの接続に使用される情報が設定されます。 ただし、この構成では、ローカル ネットワーク ゲートウェイが作成され、AWS 仮想プライベート ゲートウェイの接続情報が設定されます。 Azure ローカル ネットワーク ゲートウェイの詳細については、[Azure VPN Gateway の設定](vpn-gateway-about-vpn-gateway-settings.md#lng)に関する記事を参照してください。

Azure でローカル ネットワーク ゲートウェイを作成します。 手順については、「[ローカル ネットワーク ゲートウェイの作成](tutorial-site-to-site-portal.md#LocalNetworkGateway)」を参照してください。

次の値を指定します。

* **名前:** この例では、lng-azure-aws を使用します。
* **エンドポイント:** IP アドレス
* **[IP アドレス]:** AWS 仮想プライベート ゲートウェイのパブリック IP アドレスと VPC CIDR プレフィックス。 パブリック IP アドレスは、前にダウンロードした構成ファイルで見つけることができます。

AWS により、高可用性の目的で 2 つの IPsec トンネルが作成されます。 次の例は、IPsec トンネル #1 からのパブリック IP アドレスを示しています。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/local-network-gateway.png" alt-text="ローカル ネットワーク ゲートウェイ":::

## <a name="create-vpn-connection"></a>VPN 接続を作成する

このセクションでは、Azure 仮想ネットワーク ゲートウェイと AWS ゲートウェイの間に VPN 接続を作成します。

1. Azure 接続を作成します。 接続を作成する手順については、「[VPN 接続を作成する](tutorial-site-to-site-portal.md#CreateConnection)」を参照してください。

   次の例では、共有キーは、前にダウンロードした構成ファイルから取得されています。 この例では、AWS によって作成され、構成ファイルで記述されている IPsec トンネル #1 の値を使用します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection.png" alt-text="Azure 接続オブジェクト":::

1. 接続を確認します。 数分後に接続が確立されます。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/connection-established.png" alt-text="動作中の接続":::

1. AWS IPsec トンネル #1 が **[UP]\(稼働中\)** であることを確認します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-established.png" alt-text="AWS トンネルが [UP]\(稼働中\) であることを確認する":::

1. VPC に関連付けられているルート テーブルを編集します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/edit-aws-route.png" alt-text="ルートを編集する":::

1. Azure サブネットにルートを追加します。 このルートは、VPC ゲートウェイを経由します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/save-aws-route.png" alt-text="ルート構成を保存する":::

## <a name="configure-second-connection"></a>2 番目の接続を構成する

このセクションでは、高可用性を確保するために 2 番目の接続を作成します。

1. AWS の IPsec トンネル #2 のパブリック IP アドレスを指す別のローカル ネットワーク ゲートウェイを作成します。 これはスタンバイ ゲートウェイです。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-lng-standby.png" alt-text="ローカル ネットワーク ゲートウェイを作成する":::

1. Azure から AWS への 2 番目の VPN 接続を作成します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection-standby.png" alt-text="スタンバイ ローカル ネットワーク ゲートウェイ接続を作成する":::

1. Azure VPN ゲートウェイ接続を確認します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-tunnels.png" alt-text="Azure 接続の状態":::

1. AWS 接続を確認します。 この例では、接続が現在確立されていることを確認できます。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-tunnels.png" alt-text="AWS 接続の状態":::

## <a name="to-test-connections"></a>接続をテストするには

1. AWS 上の VPC に **インターネット ゲートウェイ** を追加します。 インターネット ゲートウェイは、Amazon VPN とインターネットの間の論理接続です。 このリソースを使用すると、インターネット経由で AWS パブリック IP からテスト VM に接続できます。 このリソースは、VPN 接続には必要ありません。 テストのためにのみ使用します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-igw.png" alt-text="インターネット ゲートウェイを作成する":::

1. **[Attach to VPC]\(VPC にアタッチする\)** を選択します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw.png" alt-text="インターネット ゲートウェイを VPC にアタッチする":::

1. VPC を選択し、 **[インターネット ゲートウェイをアタッチする]** を選択します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw-2.png" alt-text="ゲートウェイをアタッチする":::

1. インターネット ゲートウェイ経由で **0.0.0.0/0** (インターネット) への接続を許可するためのルートを作成します。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/allow-internet-igw.png" alt-text="ゲートウェイ経由のルートを構成する":::

1. Azure では、ルートは自動的に作成されます。 **[VM] > [ネットワーク] > [ネットワーク インターフェイス] > [有効なルート]** を選択することで、Azure VM からのルートを確認できます。 2 つのルート (接続ごとに 1 つのルート) が表示されています。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-effective-routes.png" alt-text="有効なルートを確認する":::

1. これは、Azure 上の Linux VM からテストできます。 結果は、次の例のように表示されます。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-overview.png" alt-text="Linux VM からの Azure の概要":::

1. これも、AWS 上の Linux VM からテストできます。 結果は、次の例のように表示されます。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-overview.png" alt-text="Linux VM からの AWS の概要":::

1. Azure VM から接続をテストします。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-ping.png" alt-text="Azure からの ping テスト":::

1. AWS VM から接続をテストします。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-ping.png" alt-text="AWS からの ping テスト":::

## <a name="next-steps"></a>次の手順

IKEv2 の AWS サポートの詳細については、[AWS の記事](https://aws.amazon.com/about-aws/whats-new/2019/02/aws-site-to-site-vpn-now-supports-ikev2/)を参照してください。
