---
title: Azure VPN Gateway:暗号化要件
description: クロスプレミス S2S VPN トンネルと Azure VNet 間接続の両方の暗号化要件を満たすように Azure VPN ゲートウェイを構成する方法について説明します。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 12/02/2020
ms.author: yushwang
ms.openlocfilehash: 47d14c5ee7f6c4816bf15351e9cb28a2aaa72b4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546847"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>暗号化要件と Azure VPN ゲートウェイについて

この記事では、Azure 内でクロスプレミスの S2S VPN トンネルと VNet 間接続の両方の暗号化要件を満たすように Azure VPN ゲートウェイを構成する方法について説明します。

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Azure VPN 接続のための IKEv1 と IKEv2 について

従来から、IKEv1 接続は Basic SKU に対してのみ許可し、IKEv2 接続は Basic SKU 以外のすべての VPN ゲートウェイ SKU に対して許可してきました。 Basic SKU では 1 つの接続のみが許可され、パフォーマンスなどの他の制限と共に、IKEv1 プロトコルのみをサポートするレガシ デバイスを使用している顧客はエクスペリエンスが制限されました。 IKEv1 プロトコルを使用している顧客のエクスペリエンスを拡張するために、Basic SKU を除くすべての VPN ゲートウェイ SKU に対して IKEv1 接続が許可されるようになりました。 詳細については、[VPN ゲートウェイの SKU](./vpn-gateway-about-vpn-gateway-settings.md#gwsku) に関するページを参照してください。

![Azure VPN Gateway の IKEv1 および IKEv2 接続](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

IKEv1 および IKEv2 接続が同じ VPN ゲートウェイに適用されている場合、これらの 2 つの接続間の転送は自動的に有効になります。

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Azure VPNゲートウェイの IPsec/IKE ポリシーのパラメーターについて

IPsec/IKE 標準プロトコルは、幅広い暗号アルゴリズムをさまざまな組み合わせでサポートしています。 暗号化アルゴリズムとパラメーターの特定の組み合わせを要求しない場合、Azure VPN ゲートウェイは一連の既定の提案を使用します。 既定の構成では、幅広いサード パーティ製 VPN デバイスとの相互運用性を最大化できるように、既定のポリシー セットが選択されています。 そのため、ポリシーと提案の数は、使用可能な暗号化アルゴリズムとキーの強度の可能な組み合わせのすべてには対応できません。

### <a name="default-policy"></a>既定のポリシー

Azure VPN ゲートウェイの既定のポリシー セットは、「[サイト間 VPN ゲートウェイ接続用の VPN デバイスと IPsec/IKE パラメーターについて](vpn-gateway-about-vpn-devices.md)」のドキュメントに示されています。

## <a name="cryptographic-requirements"></a>暗号化要件

特定の暗号化アルゴリズムまたはパラメーターを必要とする通信 (通常はコンプライアンスまたはセキュリティの要件のために) では、Azure の既定のポリシー セットではなく、特定の暗号化アルゴリズムとキーの強度を備えたカスタム IPsec/IKE ポリシーを使用するように Azure VPN ゲートウェイを構成できるようになりました。

たとえば、Azure VPN ゲートウェイの IKEv2 メイン モード ポリシーでは Diffie-Hellman グループ 2 (1024 ビット) のみを利用するのに対して、IKE でグループ 14 (2048 ビット)、グループ 24 (2048 ビット MODP グループ)、ECP (楕円曲線グループ) 256 または 384 ビット (それぞれグループ 19 およびグループ 20) などのより強力なグループが使用されるように指定することが必要になる場合があります。 IPsec クイック モード ポリシーにも同じような要件が適用されます。

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Azure VPN ゲートウェイでのカスタム IPSEC/IKE ポリシー

Azure VPN ゲートウェイは、接続ごとのカスタム IPsec/IKE ポリシーをサポートします。 次の例のように、サイト間または VNet 間接続の場合、必要なキーの強度を備えた、IPsec および IKE の暗号化アルゴリズムの特定の組み合わせを選択できます。

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

IPsec/IKE ポリシーを作成し、新規または既存の接続に適用することができます。

### <a name="workflow"></a>ワークフロー

1. ほかのハウツー ドキュメントの説明に従って、接続トポロジーに適した仮想ネットワーク、VPN ゲートウェイ、またはローカル ネットワーク ゲートウェイを作成します。
2. IPsec/IKE ポリシーの作成
3. S2S または VNet 間接続を作成するときに、ポリシーを適用することができます。
4. 接続が既に作成されている場合、ポリシーを既存の接続に適用するか、更新する

## <a name="ipsecike-policy-faq"></a>IPsec/IKE ポリシーに関する FAQ

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>次のステップ

接続にカスタム IPsec/IKE ポリシーを構成する手順については、[IPsec/IKE ポリシーの構成](vpn-gateway-ipsecikepolicy-rm-powershell.md)に関するページをご覧ください。

UsePolicyBasedTrafficSelectors オプションの詳細については、[複数のポリシー ベース VPN デバイスの接続](vpn-gateway-connect-multiple-policybased-rm-ps.md)に関するページもご覧ください。
