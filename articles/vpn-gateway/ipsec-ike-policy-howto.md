---
title: サイト間 VPN および VNet 間接続の IPsec/IKE ポリシー:Azure portal
titleSuffix: Azure VPN Gateway
description: Azure Resource Manager と Azure portal を使用して、Azure VPN Gateway でサイト間または VNet 間接続の IPsec/IKE ポリシーを構成します。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: 2b298185866d16da02fe8d3b3fdb41f0b0b1f726
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878546"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections-azure-portal"></a>S2S VPN または VNet 対 VNet 接続用の IPsec/IKE ポリシーを構成する:Azure portal

この記事では、Azure portal を使用して、VPN Gateway のサイト間 VPN または VNet 間接続の IPsec/IKE ポリシーを構成する手順について説明します。 以降のセクションで、IPsec/IKE ポリシーを作成して構成し、ポリシーを新規または既存の接続に適用する方法について説明します。

## <a name="about-ipsec-and-ike-policy-parameters"></a><a name="about"></a>IPsec および IKE ポリシーのパラメーターについて

IPsec/IKE 標準プロトコルは、幅広い暗号アルゴリズムをさまざまな組み合わせでサポートしています。 これが、クロスプレミスと VNet 間の接続を確保して、コンプライアンスまたはセキュリティの要件を満たすためにどのように役立つかについては、「[暗号化要件と Azure VPN Gateway について](vpn-gateway-about-compliance-crypto.md)」を参照してください。

この記事では、IPsec/IKE ポリシーを作成して構成し、新規または既存の VPN Gateway 接続に適用する手順について説明します。

### <a name="considerations"></a>考慮事項

* IPsec/IKE ポリシーは、次のゲートウェイ SKU でのみ機能します。
  * "***VpnGw1 から 5 および VpnGw1AZ から 5AZ***"
  * ***Standard** _ および _ *_HighPerformance_**
* ある特定の接続に対して指定できるポリシーの組み合わせは ***1 つ*** だけです。
* IKE (メイン モード) と IPsec (クイック モード) の両方について、すべてのアルゴリズムとパラメーターを指定する必要があります。 ポリシーを部分的に指定することはできません。
* オンプレミスの VPN デバイスでポリシーがサポートされることを、VPN デバイス ベンダーの仕様で確認してください。 ポリシーに対応していない場合、S2S または VNet-to-VNet 接続を確立することはできません。

## <a name="workflow"></a><a name ="workflow"></a>ワークフロー

このセクションでは、S2S VPN または VNet-to-VNet 接続の IPsec/IKE ポリシーの作成と更新を行うためのワークローについて説明します。

1. 仮想ネットワークと VPN ゲートウェイを作成します。
2. クロスプレミス接続用のローカル ネットワーク ゲートウェイ、または VNet 間接続用の別の仮想ネットワークとゲートウェイを作成します。
3. 接続 (IPsec または VNet2VNet) を作成します。
4. 接続リソースの IPsec/IKE ポリシーを構成、更新、または削除します。

この記事の手順に従うと、次の図に示すように、IPsec/IKE ポリシーを設定して構成できます。

:::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE ポリシーの図" border="false":::

## <a name="supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>サポートされている暗号アルゴリズムとキーの強度

### <a name="algorithms-and-keys"></a><a name ="table1"></a>アルゴリズムとキー

以下の表は、サポートされている暗号アルゴリズムと、ユーザーが構成できるキーの強度を一覧にしたものです。

| **IPsec/IKE**    | **[オプション]**    |
| ---              | ---            |
| IKE 暗号化   | AES256、AES192、AES128、DES3、DES                  |
| IKE 整合性    | SHA384、SHA256、SHA1、MD5                          |
| DH グループ         | DHGroup24、ECP384、ECP256、DHGroup14、DHGroup2048、DHGroup2、DHGroup1、なし |
| IPsec 暗号化 | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128、DES3、DES、なし    |
| IPsec 整合性  | GCMASE256、GCMAES192、GCMAES128、SHA256、SHA1、MD5 |
| PFS グループ        | PFS24、ECP384、ECP256、PFS2048、PFS2、PFS1、なし   |
| QM SA の有効期間   | (**オプション**: 指定されていない場合、既定値が使用されます)<br>秒 (整数: **最小 300**/既定値 27,000 秒)<br>キロバイト数 (整数: **最小 1024**/既定値 102,400,000 キロバイト)    |
| トラフィック セレクター | UsePolicyBasedTrafficSelectors** ($True/$False； **Optional**、指定されていない場合、既定値は $False)    |
| DPD タイムアウト      | 秒 (整数: 最小 9、最大 3600、既定値 45 秒) |
|  |  |

#### <a name="important-requirements"></a>重要な要件

* ご使用のオンプレミス VPN デバイスの構成は、Azure IPsec/IKE ポリシーで指定した次のアルゴリズムおよびパラメーターと一致している (または含んでいる) 必要があります。
  * IKE 暗号化アルゴリズム (メイン モード/フェーズ 1)
  * IKE 整合性アルゴリズム (メイン モード/フェーズ 1)
  * DH グループ (メイン モード/フェーズ 1)
  * IPsec 暗号化アルゴリズム (クイック モード/フェーズ 2)
  * IPsec 整合性アルゴリズム (クイック モード/フェーズ 2)
  * PFS グループ (クイック モード/フェーズ 2)>    * トラフィック セレクター (UsePolicyBasedTrafficSelectors が使用される場合)
  * SA の有効期間は、ローカルの指定のみとなります。一致している必要はありません。

* IPsec 暗号化アルゴリズム用として GCMAES を使用する場合は、IPsec 整合性のため、同じ GCMAES アルゴリズムとキーの長さを選択する必要があります。たとえば、両方に GCMAES128 を使用します。

* 上記の[アルゴリズムとキー テーブル](#table1)で次のようにします。
  * IKE はメイン モードまたはフェーズ 1 に対応する
  * IPsec はクイック モードまたはフェーズ 2 に対応しています
  * DH グループは、メイン モードまたはフェーズ 1 で使用される Diffie-Hellman グループを指定します
  * PFS グループは、クイック モードまたはフェーズ 2 で使用される Diffie-Hellman グループを指定します

* IKE メイン モード SA の有効期間は、Azure VPN ゲートウェイで 28,800 秒に固定します。

* 接続の **UsePolicyBasedTrafficSelectors** を $True に設定すると、Azure VPN ゲートウェイは、オンプレミスのポリシーベースの VPN ファイアウォールに接続するように構成されます。 PolicyBasedTrafficSelectors を有効にする場合は、オンプレミス ネットワーク (ローカル ネットワーク ゲートウェイ) プレフィックスと Azure Virtual Network プレフィックスのすべての組み合わせに対応するトラフィック セレクターが VPN デバイスに定義されている必要があります (any-to-any は不可)。 たとえば、オンプレミス ネットワークのプレフィックスが 10.1.0.0/16 と 10.2.0.0/16 で、仮想ネットワークのプレフィックスが 192.168.0.0/16 と 172.16.0.0/16 である場合、次のトラフィック セレクターを指定する必要があります。
  * 10.1.0.0/16 <====> 192.168.0.0/16
  * 10.1.0.0/16 <====> 172.16.0.0/16
  * 10.2.0.0/16 <====> 192.168.0.0/16
  * 10.2.0.0/16 <====> 172.16.0.0/16

   ポリシー ベースのトラフィック セレクターの詳細については、[複数のオンプレミスのポリシー ベース VPN デバイスへの接続](vpn-gateway-connect-multiple-policybased-rm-ps.md)に関する記事を参照してください。

* DPD タイムアウト - Azure VPN ゲートウェイでは、既定値は 45 秒です。 タイムアウトを短い期間に設定すると、IKE によるキー更新がより積極的になり、場合によっては接続が切断されているように見えます。 これは、オンプレミスの場所が VPN ゲートウェイが存在する Azure リージョンから離れている場合や、物理的なリンク条件によってパケット損失が発生する可能性がある場合には望ましくないことがあります。 一般的な推奨事項は、**30 から 45** 秒間のタイムアウトを設定することです。

### <a name="diffie-hellman-groups"></a>Diffie-hellman グループ

以下の表は、カスタム ポリシーでサポートされている、対応する Diffie-Hellman グループを示したものです。

| **Diffie-Hellman グループ**  | **DHGroup**              | **PFSGroup** | **キーの長さ** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768 ビット MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 ビット MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 ビット MODP  |
| 19                        | ECP256                   | ECP256       | 256 ビット ECP    |
| 20                        | ECP384                   | ECP384       | 384 ビット ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 ビット MODP  |

詳細については、[RFC3526](https://tools.ietf.org/html/rfc3526) と [RFC5114](https://tools.ietf.org/html/rfc5114) を参照してください。

## <a name="s2s-vpn-with-ipsecike-policy"></a><a name ="S2S"></a>IPsec/IKE ポリシーを使用したサイト間 VPN

このセクションでは、IPsec/IKE ポリシーを使用してサイト間 VPN 接続を作成する手順について説明します。 以下の手順で、次の図に示す接続を作成します。

:::image type="content" source="./media/ipsec-ike-policy-howto/site-to-site-diagram.png" alt-text="サイト間ポリシー" border="false":::

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>手順1 - 仮想ネットワーク、VPN ゲートウェイ、およびローカル ネットワーク ゲートウェイを作成する

以下のリソースを次のスクリーンショットに示すように作成します。 手順については、[サイト間 VPN 接続の作成](./tutorial-site-to-site-portal.md)に関するページを参照してください。

* **仮想ネットワーク:** TestVNet1

   :::image type="content" source="./media/ipsec-ike-policy-howto/testvnet-1.png" alt-text="VNet":::

* **VPN ゲートウェイ:** VNet1GW

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-1-gateway.png" alt-text="ゲートウェイ":::

* **[ローカル ネットワーク ゲートウェイ]:** Site6

   :::image type="content" source="./media/ipsec-ike-policy-howto/lng-site-6.png" alt-text="サイト":::

* **Connection:** VNet1 から Site6

    :::image type="content" source="./media/ipsec-ike-policy-howto/connection-site-6.png" alt-text="接続":::

### <a name="step-2---configure-ipsecike-policy-on-the-s2s-vpn-connection"></a><a name="s2sconnection"></a>手順 2 - サイト間 VPN 接続で IPsec/IKE ポリシーを構成する

このセクションでは、次のアルゴリズムとパラメーターを使用して IPsec/IKE ポリシーを構成します。

* IKE: AES256、SHA384、DHGroup24、DPD タイムアウト 45 秒
* IPsec:AES256、SHA256、PFS なし、SA の有効期間 30000 秒、102400000 KB

1. Azure portal で、接続リソース **VNet1toSite6** に移動します。 **[構成]** ページを選択し、 **[カスタム]** の IPsec/IKE ポリシーを選択してすべての構成オプションを表示します。 次のスクリーンショットは、一覧に基づいた構成を示しています。

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-site-6.png" alt-text="Site 6":::

1. IPsec に GCMAES を使用する場合、IPsec 暗号化と整合性の両方で同じ GCMAES アルゴリズムとキーの長さを使用する必要があります。 たとえば、次のスクリーンショットでは、IPsec 暗号化と IPsec 整合性の両方に GCMAES128 を指定しています。

   :::image type="content" source="./media/ipsec-ike-policy-howto/gcmaes.png" alt-text="IPsec に GCMAES を指定":::

1. 必要に応じて **[Use policy based traffic selectors]\(ポリシー ベースのトラフィック セレクターを使用する\)** オプションに **[有効化]** を選択して、Azure VPN ゲートウェイがオンプレミスのポリシーベースの VPN デバイスに接続できるようにすることができます (前述の説明を参照してください)。

   :::image type="content" source="./media/ipsec-ike-policy-howto/policy-based-selector.png" alt-text="ポリシー ベースのトラフィック セレクター":::

1. すべてのオプションを選択したら、 **[保存]** を選択して、接続リソースの変更をコミットします。 ポリシーは約 1 分で適用されます。

> [!IMPORTANT]
>
> * 接続に IPsec/IKE ポリシーを指定すると、Azure VPN ゲートウェイは、その特定の接続に対して指定された暗号アルゴリズムとキーの強度を使用する IPsec/IKE 提案のみを送受信します。 接続するオンプレミスの VPN デバイスが、ポリシーの完全な組み合わせを使用するか受け入れることを確認する必要があります。それ以外の場合、S2S VPN トンネルは確立されません。
>
> * **ポリシーベースのトラフィック セレクター** と **DPD タイムアウト** のオプションは、カスタム IPsec/IKE ポリシーを使用せず、上記のスクリーンショットに示しているように、**既定** のポリシーを使用して指定できます。
>

## <a name="vnet-to-vnet-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>IPsec/IKE ポリシーを使用した VNet 間

IPsec/IKE ポリシーを使用した VNet 間接続の作成手順は、サイト間 VPN 接続の作成手順に似ています。

:::image type="content" source="./media/ipsec-ike-policy-howto/vnet-policy.png" alt-text="VNet 間ポリシーの図" border="false":::

1. [VNet 間接続の作成](vpn-gateway-vnet-vnet-rm-ps.md)に関する記事の手順を使用して、VNet 間接続を作成します。

2. 手順を完了すると、次のスクリーンショットに示すように、VNet2GW リソースの 2 つの VNet 間接続が表示されます。

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-connections.png" alt-text="VNet 間接続":::

3. 接続リソースにアクセスし、ポータルの **[構成]** ページに移動します。 **[IPsec/IKE policy]\(IPsec/IKE ポリシー\)** で **[カスタム]** を選択すると、カスタム ポリシーオプションが表示されます。 キーの長さが一致する暗号アルゴリズムを選択します。

   スクリーンショットには、次のアルゴリズムとパラメーターを使用した別の IPsec/IKE ポリシーが示されています。
   * IKE:AES128、SHA1、DHGroup14、DPD タイムアウト 45 秒
   * IPsec: GCMAES128、GCMAES128、PFS14、SA の有効期間 14,400 秒および 102,400,000 KB

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-policy.png" alt-text="接続ポリシー":::

4. 接続リソースにポリシーの変更を適用するには、 **[保存]** を選択します。

5. 同じポリシーをもう一方の接続リソース VNet2toVNet1 に適用します。 そうしないと、ポリシーの不一致により、IPsec/IKE VPN トンネルが接続されません。

   > [!IMPORTANT]
   > 接続に IPsec/IKE ポリシーを指定すると、Azure VPN ゲートウェイは、その特定の接続に対して指定された暗号アルゴリズムとキーの強度を使用する IPsec/IKE 提案のみを送受信します。 両方の接続の IPsec ポリシーが同じであることを確認してください。それ以外の場合、VNet 間の接続は確立されません。

6. これらの手順を完了すると、数分後に接続が確立され、次のネットワーク トポロジが設定されます。

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE ポリシーの図" border="false":::

## <a name="to-remove-custom-ipsecike-policy-from-a-connection"></a><a name ="deletepolicy"></a>接続からカスタム IPsec/IKE ポリシーを削除する

1. 接続からカスタム ポリシーを削除するには、接続リソースにアクセスし、 **[構成]** ページに移動して、現在のポリシーを表示します。

2. **[IPsec/IKE policy]\(IPsec/IKE ポリシー\)** オプションで **[既定]** を選択します。 これにより、この接続で以前に指定したすべてのカスタム ポリシーが削除され、接続に既定の IPsec/IKE 設定が復元されます。

   :::image type="content" source="./media/ipsec-ike-policy-howto/delete-policy.png" alt-text="ポリシーの削除":::

3. **[保存]** を選択すると、カスタム ポリシーが削除され、接続の既定の IPsec/IKE 設定が復元されます。

## <a name="next-steps"></a>次のステップ

ポリシー ベースのトラフィック セレクターの詳細については、[複数のオンプレミスのポリシー ベースの VPN デバイスへの接続](vpn-gateway-connect-multiple-policybased-rm-ps.md)に関する記事を参照してください。