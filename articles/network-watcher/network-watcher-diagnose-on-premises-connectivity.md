---
title: VPN ゲートウェイ経由でオンプレミスの接続を診断する
titleSuffix: Azure Network Watcher
description: この記事では、Azure Network Watcher のリソース トラブルシューティングを使用して、VPN Gateway 経由でオンプレミスの接続を診断する方法を説明します。
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 3b8b53446799eec3473d63c89672393a35f9787a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670957"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>VPN Gateway を使用してオンプレミスの接続を診断する

Azure VPN Gateway を使うと、オンプレミス ネットワークと Azure Virtual Network との間の接続のセキュリティ保護に取り組むハイブリッド ソリューションを作成できます。 要件が一意であるため、オンプレミスの VPN デバイスの選択も一意です。 Azure では現在、デバイス ベンダーと協力して常に検証している、[複数の VPN デバイス](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable)をサポートしています。 オンプレミスの VPN デバイスを構成する前に、デバイス固有の構成設定を見直します。 同様に、Azure VPN Gateway は接続の確立に使用されている、[サポート対象の IPsec パラメーター](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec)のセットで構成されています。 現在、Azure VPN Gateway から特定の IPsec パラメーターの組み合わせを指定または選択する方法はありません。 オンプレミスと Azure との間の接続を正常に確立するには、オンプレミス VPN デバイスの設定が Azure VPN Gateway で規定されている IPsec パラメーターに従っている必要があります。 設定が正しくないと、接続が失われます。また今日まで、これらの問題のトラブルシューティングは簡単ではなく、問題を識別して解決するために通常は何時間もかかりました。

Azure Network Watcher のトラブルシューティング機能により、Gateway と Connections のどんな問題でも診断できるようになり、数分以内に十分な情報に基づいて問題を修正できるようになりました。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>シナリオ

オンプレミスの VPN Gateway として FortiGate を使用して、Azure とオンプレミスとの間のサイト間接続を構成するとします。 このシナリオを成し遂げるには、次のセットアップが必要です。

1. Virtual Network Gateway - Azure 上の VPN Gateway
1. Local Network Gateway - Azure クラウドで表示されている、[オンプレミスの (FortiGate) VPN Gateway](../vpn-gateway/tutorial-site-to-site-portal.md#LocalNetworkGateway)
1. サイト間接続 (ルート ベース) - [VPN Gateway とオンプレミス ルーター間の接続](../vpn-gateway/tutorial-site-to-site-portal.md#CreateConnection)
1. [FortiGate の構成](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

サイト間構成を構成するための詳細なステップ バイ ステップ ガイダンスについては、「[Azure Portal を使用したサイト間接続を持つ VNet の作成](../vpn-gateway/tutorial-site-to-site-portal.md)」をご覧ください。

最も重要な構成の手順の 1 つは、IPsec コミュニケーション パラメーターの構成です。いかなる構成の誤りでも、オンプレミス ネットワークと Azure との間の接続は失われます。 Azure VPN Gateway は現在、以下のフェーズ 1 の IPsec パラメーターをサポートするように構成されています。 次の表に示すように、Azure VPN Gateway でサポートされている暗号化アルゴリズムは、AES256、AES128、3DES です。

### <a name="ike-phase-1-setup"></a>IKE フェーズ 1 セットアップ

| **プロパティ** | **PolicyBased** | **RouteBased と Standard、または高性能 VPN ゲートウェイ** |
| --- | --- | --- |
| IKE のバージョン |IKEv1 |IKEv2 |
| Diffie-hellman グループ |グループ 2 (1024 ビット) |グループ 2 (1024 ビット) |
| 認証方法 |事前共有キー |事前共有キー |
| 暗号化アルゴリズム |AES256 AES128 3DES |AES256 3DES |
| ハッシュ アルゴリズム |SHA1(SHA128) |SHA1(SHA128)、SHA2(SHA256) |
| フェーズ 1 のセキュリティ アソシエーション (SA) の有効期間 (時間) |28,800 秒 |28,800 秒 |

ユーザーとして、FortiGate を構成する必要があります。構成のサンプルは [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt) で参照できます。 知らないうちに FortiGate に対して SHA-512 をハッシュ アルゴリズムとして使用するように構成したとします。 このアルゴリズムはポリシー ベースの接続でサポートされていないため、VPN 接続が機能しません。

これら問題をトラブルシューティングするのは難しく、根本的な原因は直感とは異なる場合が多いです。 この場合はサポート チケットを開いて、問題解決のためにサポートを受けることができます。 一方で、Azure Network Watcher のトラブルシューティング API を使用すると、これらの問題を自分で特定できます。

## <a name="troubleshooting-using-azure-network-watcher"></a>Azure Network Watcher を使用したトラブルシューティング

接続を診断するには、Azure PowerShell に接続して `Start-AzNetworkWatcherResourceTroubleshooting` コマンドレットを開始します。 このコマンドレットの使用に関する詳細については、[PowerShell を使用した仮想ネットワーク ゲートウェイと接続のトラブルシューティング](network-watcher-troubleshoot-manage-powershell.md)に関する記事をご覧ください。 このコマンドレットの完了には最大で数分かかる場合があります。

コマンドレットが完了したら、コマンドレットの指定されたストレージの場所に移動して、この問題の詳細な情報とログを取得できます。 Azure Network Watcher により、次のログ ファイルを含む zip フォルダーが作成されます。

![1][1]

IKEErrors.txt というファイルを開くと、オンプレミスの IKE 設定に構成ミスの問題があることを示す、次のエラーが表示されます。

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

エラーについては Scrubbed-wfpdiag.txt から詳細な情報を取得できます。この場合、接続の不具合につながった `ERROR_IPSEC_IKE_POLICY_MATCH` があったことが言及されています。

もう 1 つの一般的な構成の誤りとしては、不適切な共有キーが指定されることです。 前述の例で別の共有キーを指定していた場合、IKEErrors.txt には次のエラーが表示されます: `Error: Authentication failed. Check shared key`。

Azure Network Watcher のトラブルシューティング機能を使用すると、VPN Gateway と Connection を単純な PowerShell コマンドレットのように簡単に診断してトラブルシューティングできるようになります。 私たちは現在、次の症状の診断をサポートしており、より多くの症状について診断できるように取り組んでおります。

### <a name="gateway"></a>Gateway

| エラーの種類 | 理由 | ログ|
|---|---|---|
| NoFault | エラーが検出されなかった場合。 |はい|
| GatewayNotFound | ゲートウェイが見つからなかったか、プロビジョニングされていません。 |いいえ|
| PlannedMaintenance |  ゲートウェイ インスタンスはメンテナンス中です。  |いいえ|
| UserDrivenUpdate | ユーザーの更新が進行中である場合。 サイズ変更操作が行われていると考えられます。 | いいえ |
| VipUnResponsive | ゲートウェイのプライマリ インスタンスに到達できません。 これは、正常性プローブでエラーが発生した場合に起こります。 | いいえ |
| PlatformInActive | プラットフォームに問題があります。 | いいえ|
| ServiceNotRunning | 基になるサービスが実行されていません。 | いいえ|
| NoConnectionsFoundForGateway | ゲートウェイ上に接続が存在しません。 これはただの警告です。| いいえ|
| ConnectionsNotConnected | どの接続も接続されていません。 これはただの警告です。| はい|
| GatewayCPUUsageExceeded | 現在のゲートウェイの CPU 使用率が 95% を超えています。 | はい |

### <a name="connection"></a>Connection

| エラーの種類 | 理由 | ログ|
|---|---|---|
| NoFault | エラーが検出されなかった場合。 |はい|
| GatewayNotFound | ゲートウェイが見つからなかったか、プロビジョニングされていません。 |いいえ|
| PlannedMaintenance | ゲートウェイ インスタンスはメンテナンス中です。  |いいえ|
| UserDrivenUpdate | ユーザーの更新が進行中である場合。 サイズ変更操作が行われていると考えられます。  | いいえ |
| VipUnResponsive | ゲートウェイのプライマリ インスタンスに到達できません。 これは、正常性プローブでエラーが発生した場合に起こります。 | いいえ |
| ConnectionEntityNotFound | 接続の構成がありません。 | いいえ |
| ConnectionIsMarkedDisconnected | 接続が "切断" とマークされています。 |いいえ|
| ConnectionNotConfiguredOnGateway | 基になるサービスで接続が構成されていません。 | はい |
| ConnectionMarkedStandby | 基になるサービスがスタンバイとマークされています。| はい|
| 認証 | 事前共有キーが一致しません。 | はい|
| PeerReachability | ピア ゲートウェイに到達できません。 | はい|
| IkePolicyMismatch | ピア ゲートウェイに、Azure のサポート対象外の IKE ポリシーが設定されています。 | はい|
| WfpParse Error | WFP ログの解析中にエラーが発生しました。 |はい|

## <a name="next-steps"></a>次のステップ

PowerShell と Azure Automation を使用した VPN Gateway の接続の確認については、[Azure Network Watcher のトラブルシューティングを使用した VPN ゲートウェイの監視](network-watcher-monitor-with-azure-automation.md)に関する記事をご覧ください。

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png