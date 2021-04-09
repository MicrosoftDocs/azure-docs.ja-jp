---
title: 'トラブルシューティング: サイト間 VPN 接続が断続的に切断される'
description: サイト間 VPN 接続が頻繁に切断される問題をトラブルシューティングする方法について説明します。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/22/2021
ms.author: genli
ms.openlocfilehash: 38846bbe717912092ccfe2b236b717770b79302f
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867255"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>トラブルシューティング: サイト間 VPN 接続が断続的に切断される

新規または既存の Microsoft Azure サイト間 VPN 接続が不安定になったり頻繁に切断されたりする問題に遭遇することがあります。 この記事では、問題の原因の特定と解決に役立つトラブルシューティング手順について説明します。 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順

### <a name="prerequisite-step"></a>事前に必要な手順

Azure 仮想ネットワーク ゲートウェイの種類を確認します。

1. [Azure ポータル](https://portal.azure.com)に移動します。
2. 仮想ネットワーク ゲートウェイの **[概要]** ページで、その種類の情報を確認します。
    
    ![ゲートウェイの概要](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>手順 1 オンプレミス VPN デバイスが検証済みであるかどうかを確認する

1. 使っている [VPN デバイスとオペレーティング システム バージョンが検証済み](vpn-gateway-about-vpn-devices.md#devicetable)であるかどうかを確認します。 検証済みの VPN デバイスではない場合、互換性の問題があるかどうかをデバイスの製造元に問い合わせてください。
2. VPN デバイスが正しく構成されていることを確認します。 詳細については、「[デバイス構成のサンプルの編集](vpn-gateway-about-vpn-devices.md#editing)」を参照してください。

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>手順 2 セキュリティ アソシエーションの設定を確認する (ポリシー ベースの Azure 仮想ネットワーク ゲートウェイの場合)

1. Microsoft Azure の **[ローカル ネットワーク ゲートウェイ]** の定義における仮想ネットワーク、サブネット、範囲が、オンプレミス VPN デバイス上の構成と同じであることを確認します。
2. セキュリティ アソシエーションの設定が一致していることを確認します。

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>手順 3 ユーザー定義ルートまたはネットワーク セキュリティ グループがゲートウェイ サブネットに存在するかどうかを確認する

ゲートウェイ サブネット上のユーザー定義ルートによって、制限されるトラフィックと許可されるトラフィックが生じることがあります。 その場合、一部のトラフィックについてだけ VPN 接続が不安定で、他のトラフィックについては接続状態が良好であるように見えます。 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>手順 4 [one VPN Tunnel per Subnet Pair]\(サブネット ペアごとに 1 つの VPN トンネル\) の設定を確認する (ポリシー ベースの仮想ネットワーク ゲートウェイの場合)

ポリシー ベースの仮想ネットワーク ゲートウェイの場合、オンプレミス VPN デバイスが、**サブネット ペアごとに 1 つの VPN トンネル** を割り当てる設定になっていることを確認します。

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>手順 5 セキュリティ アソシエーションの制限を確認する (ポリシー ベースの仮想ネットワーク ゲートウェイの場合)

ポリシー ベースの仮想ネットワーク ゲートウェイには、サブネットのセキュリティ アソシエーション ペア数に上限 (200 個) があります。 Azure Virtual Network サブネット数にローカル サブネット数を乗じた値が 200 を超えると、散発的にサブネットが切断されることがわかります。

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>手順 6 オンプレミス VPN デバイスの外部インターフェイスのアドレスを確認する

VPN デバイスのインターネット接続 IP アドレスが Azure の **[ローカル ネットワーク ゲートウェイ]** の定義に含まれていると、散発的に接続が途切れることがあります。

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>手順 7 オンプレミス VPN デバイスで Perfect Forward Secrecy が有効になっているかどうかを確認する

接続切断の問題は、**Perfect Forward Secrecy** 機能によって引き起こされる可能性があります。 VPN デバイスで **Perfect Forward Secrecy** が有効になっている場合は、その機能を無効にしてください。 そのうえで、[仮想ネットワーク ゲートウェイの IPsec ポリシーを更新](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy)します。

## <a name="next-steps"></a>次のステップ

- [仮想ネットワークへのサイト間接続を構成する](./tutorial-site-to-site-portal.md)
- [サイト間 VPN 接続の IPsec/IKE ポリシーを構成する](vpn-gateway-ipsecikepolicy-rm-powershell.md)
