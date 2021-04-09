---
title: P2S クライアント用のスポーク VNet にあるリソースへのセキュリティで保護されたアクセスを管理する
titleSuffix: Azure Virtual WAN
description: この記事は、Azure Virtual WAN と Azure Firewall の規則を使用して、ユーザー VPN (ポイント対サイト) クライアント用の仮想ネットワークへのセキュリティで保護されたアクセスを管理するのに役立ちます。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cherylmc
ms.openlocfilehash: 751d11fcd4b5d4c33145ee7f2b7b49971b8927ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048262"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>ユーザー VPN クライアント用のスポーク VNet にあるリソースへのセキュリティで保護されたアクセスを管理する

この記事では、Virtual WAN と Azure Firewall の規則およびフィルターを使用して、ポイント対サイト IKEv2 または Open VPN 接続を介して Azure のリソースに接続するためのセキュリティで保護されたアクセスを管理する方法を示します。 この構成は、Azure リソースへのアクセスを制限するリモート ユーザーがいる場合や、Azure のリソースを保護するのに役立ちます。

この記事の手順は、次の図のアーキテクチャを作成するのに役立ちます。これにより、ユーザー VPN クライアントからは、仮想ハブに接続されているスポーク VNet 内の特定のリソース (VM1) にアクセスできるようになりますが、その他のリソース (VM2) にはアクセスできません。 基本的なガイドラインとして、このアーキテクチャの例を使用します。

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="図:セキュリティ保護付き仮想ハブ" :::

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* 使用する認証構成で使用可能な値がある。 たとえば、RADIUS サーバー、Azure Active Directory 認証です。あるいは、[証明書を生成してエクスポート](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)します。

## <a name="create-a-virtual-wan"></a>仮想 WAN を作成する

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>P2S 構成パラメーターを定義する

ポイント対サイト (P2S) 構成では、リモート クライアントを接続するためのパラメーターを定義します。 このセクションは、P2S 構成パラメーターを定義してから、VPN クライアント プロファイルに使用される構成を作成するのに役立ちます。 従う手順は、使用する認証方法によって異なります。

### <a name="authentication-methods"></a>認証方法

認証方法を選択する場合、3 つの選択肢があります。 各方法には特定の要件があります。 次のいずれかの方法を選択し、手順を完了します。

* **Azure Active Directory 認証:** 次のものを取得します。

   * Azure AD テナントに登録されている Azure VPN エンタープライズ アプリケーションの **アプリケーション ID**。
   * **発行者**。 例: `https://sts.windows.net/your-Directory-ID`.
   * **Azure AD テナント**。 例: `https://login.microsoftonline.com/your-Directory-ID`.

* **Radius ベースの認証:** Radius サーバーの IP、Radius サーバーのシークレット、および証明書の情報を取得します。

* **Azure 証明書:** この構成では、証明書が必要です。 証明書を生成または取得する必要があります。 クライアント証明書はクライアントごとに必要です。 また、ルート証明書情報 (公開キー) をアップロードする必要があります。 必要な証明書の詳細については、[証明書の生成とエクスポート](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)に関するページを参照してください。

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>ハブとゲートウェイを作成する

このセクションでは、ポイント対サイト ゲートウェイを持つ仮想ハブを作成します。 構成するときに、次の例の値を使用できます。

* **ハブのプライベート IP アドレス空間:** 10.0.0.0/24
* **クライアント アドレス プール:** 10.5.0.0/16
* **カスタム DNS サーバー:** DNS サーバーは 5 つまで一覧表示できます。

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>VPN クライアント構成ファイルの生成

このセクションでは、構成プロファイル ファイルを生成してダウンロードします。 これらのファイルは、クライアント コンピューター上でネイティブ VPN クライアントを構成するために使用されます。 クライアント プロファイル ファイルの内容の詳細については、[「ポイント対サイト構成」の「証明書」](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md)を参照してください。

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>VPN クライアントの構成

ダウンロードしたプロファイルを使用して、リモート アクセス クライアントを構成します。 オペレーティング システムごとに手順は異なります。お使いのシステムに適用される手順に従ってください。

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>スポーク VNet を接続する

このセクションでは、スポーク仮想ネットワークを仮想 WAN ハブにアタッチします。

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>仮想マシンを作成する

このセクションでは、VNet に 2 つの VM (VM1 と VM2) を作成します。 ネットワーク図では、10.18.0.4 と 10.18.0.5 が使用されています。 VM を構成するときは、必ず自分が作成した仮想ネットワークを選択してください ([ネットワーク] タブにあります)。 VM を作成する手順については、[クイック スタート: VM の作成](../virtual-machines/windows/quick-create-portal.md)に関するページを参照してください。

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>仮想ハブをセキュリティで保護する

標準的な仮想ハブには、スポーク仮想ネットワーク内のリソースを保護するためのセキュリティ ポリシーが組み込まれていません。 セキュリティ保護付き仮想ハブでは、Azure Firewall またはサードパーティのプロバイダーを使用して、Azure 内のリソースを保護するための受信および送信トラフィックを管理します。

次の記事を使用して、ハブをセキュリティ保護付きハブに変換します: [仮想 WAN ハブ内で Azure Firewall を構成する](howto-firewall.md)。

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a>トラフィックを管理およびフィルター処理する規則を作成する

Azure Firewall の動作を規定する規則を作成します。 ハブをセキュリティで保護することにより、仮想ハブに入るすべてのパケットが確実に、Azure リソースにアクセスする前にファイアウォール処理の対象となります。

これらの手順を完了すると、VPN ユーザーがプライベート IP アドレス 10.18.0.4 を持つ VM にアクセスできるものの、プライベート IP アドレス 10.18.0.5 を持つ VM にはアクセス **できない** アーキテクチャが作成されます

1. Azure portal で、**Firewall Manager** に移動します。
1. [セキュリティ] で、 **[Azure Firewall ポリシー]** を選択します。
1. **[Azure ファイアウォール ポリシーの作成]** を選択します。
1. **[ポリシーの詳細]** で、名前を入力し、仮想ハブがデプロイされているリージョンを選択します。
1. **[Next:DNS Settings (preview)]\(次へ: DNS 設定 (プレビュー)\)** を選択します。
1. **[Next:Rules]\(次へ: 規則\)** を選択します。
1. **[規則]** タブで、 **[規則コレクションの追加]** を選択します。
1. コレクションの名前を指定します。 種類を **ネットワーク** として設定します。 **100** の優先順位値を追加します。
1. 以下の例に示すように、規則の名前、ソースの種類、ソース、プロトコル、宛先ポート、および宛先の種類を入力します。 その後、 **[追加]** を選択します。 この規則により、VPN クライアント プールの任意の IP アドレスで、プライベート IP アドレス 10.18.04 を持つ VM にアクセスできるようになりますが、仮想ハブに接続されているその他のリソースにはアクセスできません。 必要なアーキテクチャに適したすべての規則とアクセス許可の規則を作成します。

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="ファイアウォール規則" :::

1. **[Next:脅威インテリジェンス**] を選択します。
1. **次へ:[次へ: ハブ]** を選択します。
1. **[ハブ]** タブで、 **[Associate virtual hubs]\(仮想ハブの関連付け\)** を選択します。
1. 先ほど作成した仮想ハブを選んでから、 **[追加]** を選択します。
1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **［作成］** を選択します

このプロセスが完了するまで 5 分以上かかることがあります。

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>トラフィックを Azure Firewall 経由でルーティングする

このセクションでは、確実にトラフィックが Azure Firewall 経由でルーティングされるようにする必要があります。

1. ポータルで、**Firewall Manager** から **[セキュリティで保護された仮想ハブ]** を選択します。
1. 作成した仮想ハブを選択します。
1. **[設定]** の **[セキュリティの構成]** を選択します。
1. **[Private traffic]\(プライベート トラフィック\)** の **[Send via Azure Firewall]\(Azure Firewall 経由で送信\)** を選択します。
1. VNet 接続とブランチ接続のプライベート トラフィックが Azure Firewall によって保護されていることを確認します。
1. **[保存]** を選択します。

## <a name="validate"></a><a name="validate"></a>検証

セキュリティ保護付きハブの設定を確認します。

1. クライアント デバイスから VPN 経由で **セキュリティ保護付き仮想ハブ** に接続します。
1. クライアントから IP アドレス **10.18.0.4** に Ping を実行します。 応答が表示されるはずです。
1. クライアントから IP アドレス **10.18.0.5** に Ping を実行します。 応答は表示できないはずです。

### <a name="considerations"></a>考慮事項

* セキュリティ保護付き仮想ハブ上の **有効なルート テーブル** に、ファイアウォール経由のプライベート トラフィックの次のホップがあることを確認します。 有効なルート テーブルにアクセスするには、**仮想ハブ** リソースに移動します。 **[接続]** で、 **[ルーティング]** を選んでから、 **[有効なルート]** を選択します。 そこから、**既定の** ルート テーブルを選択します。
* [規則の作成](#create-rules)に関するセクションで規則を作成したことを確認します。 これらの手順を行わないと、作成した規則が実際にはハブに関連付けられず、ルート テーブルとパケット フローで Azure Firewall は使用されません。

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[Virtual WAN FAQ](virtual-wan-faq.md) を参照してください。
* Azure Firewall の詳細については、「[Azure Firewall に関する FAQ](../firewall/firewall-faq.yml)」を参照してください。
