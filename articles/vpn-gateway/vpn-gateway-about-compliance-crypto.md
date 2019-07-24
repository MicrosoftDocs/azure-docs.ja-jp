---
title: 暗号化要件と Azure VPN ゲートウェイについて | Microsoft Docs
description: この記事では、暗号化要件と Azure VPN ゲートウェイについて説明します。
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2017
ms.author: yushwang
ms.openlocfilehash: 060e647badcc3bad7b44d7cef3530c36b8ecdf57
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2019
ms.locfileid: "23125180"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>暗号化要件と Azure VPN ゲートウェイについて

この記事では、Azure 内でクロスプレミスの S2S VPN トンネルと VNet 間接続の両方の暗号化要件を満たすように Azure VPN ゲートウェイを構成する方法について説明します。 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Azure VPNゲートウェイの IPsec/IKE ポリシーのパラメーターについて
IPsec/IKE 標準プロトコルは、幅広い暗号アルゴリズムをさまざまな組み合わせでサポートしています。 暗号化アルゴリズムとパラメーターの特定の組み合わせを必要としない場合、Azure VPN ゲートウェイは既定の提案セットを使用します。 既定の構成では、幅広いサード パーティ製 VPN デバイスとの相互運用性を最大化できるように、既定のポリシー セットが選択されています。 そのため、ポリシーと提案の数は、使用可能な暗号化アルゴリズムとキーの強度の可能な組み合わせのすべてには対応できません。

Azure VPN ゲートウェイの既定のポリシー セットは、「[サイト間 VPN ゲートウェイ接続用の VPN デバイスと IPsec/IKE パラメーターについて](vpn-gateway-about-vpn-devices.md)」のドキュメントに示されています。

## <a name="cryptographic-requirements"></a>暗号化要件
コンプライアンスやセキュリティの要件から特定の暗号化アルゴリズムやパラメーターを必要とする通信では、Azure の既定のポリシー セットではなく、特定の暗号化アルゴリズムとキーの強度を備えたカスタム IPsec/IKE ポリシーを使用するように Azure VPN ゲートウェイを構成できます。

たとえば、Azure VPN ゲートウェイの IKEv2 メイン モード ポリシーで使用するのは Diffie-Hellman グループ 2 (1024 ビット) のみですが、グループ 14 (2048 ビット)、グループ 24 (2048 ビット MODP グループ)、または ECP (楕円曲線グループ) 256 または 384 ビット (それぞれグループ 19 およびグループ 20) などの、IKE で使用されるより強力なグループを指定する必要がある場合もあります。 IPsec クイック モード ポリシーにも同じような要件が適用されます。

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Azure VPN ゲートウェイでのカスタム IPSEC/IKE ポリシー
Azure VPN ゲートウェイは、接続ごとのカスタム IPsec/IKE ポリシーをサポートします。 次の例のように、サイト間または VNet 間接続の場合、必要なキーの強度を備えた、IPsec および IKE の暗号化アルゴリズムの特定の組み合わせを選択できます。

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

IPsec/IKE ポリシーを作成し、新規または既存の接続に適用することができます。 

### <a name="workflow"></a>ワークフロー

1. ほかのハウツー ドキュメントの説明に従って、接続トポロジーに適した仮想ネットワーク、VPN ゲートウェイ、またはローカル ネットワーク ゲートウェイを作成します。
2. IPsec/IKE ポリシーの作成
3. S2S または VNet 間接続を作成するときに、ポリシーを適用することができます。
4. 接続が既に作成されている場合、既存の接続に対してポリシーを適用したり更新したりできます。


## <a name="ipsecike-policy-faq"></a>IPsec/IKE ポリシーに関する FAQ

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="next-steps"></a>次の手順
接続にカスタム IPsec/IKE ポリシーを構成する手順については、[IPsec/IKE ポリシーの構成](vpn-gateway-ipsecikepolicy-rm-powershell.md)に関するページをご覧ください。

UsePolicyBasedTrafficSelectors オプションの詳細については、[複数のポリシー ベース VPN デバイスの接続](vpn-gateway-connect-multiple-policybased-rm-ps.md)に関するページもご覧ください。