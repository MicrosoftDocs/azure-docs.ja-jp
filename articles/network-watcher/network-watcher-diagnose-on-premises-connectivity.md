---
title: "Azure Network Watcher を使用して VPN Gateway 経由でオンプレミスの接続を診断する | Microsoft Docs"
description: "この記事では、Azure Network Watcher のリソース トラブルシューティングを使用して、VPN Gateway 経由でオンプレミスの接続を診断する方法を説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 8b832916f5b6fe413f9fc7b3fcefcea40d3ce7ef
ms.lasthandoff: 03/29/2017

---

# <a name="diagnose-on-premise-connectivity-via-vpn-gateways"></a>VPN Gateway 経由でオンプレミスの接続を診断する

Azure VPN Gateway を使うと、オンプレミス ネットワークと Azure Virtual Network との間の接続のセキュリティ保護に取り組むハイブリッド ソリューションを作成できます。 要件が一意であるため、オンプレミスの VPN デバイスの選択も一意です。 Azure では現在、デバイス ベンダーと協力して常に検証している、[複数の VPN デバイス](../vpn-gateway/vpn-gateway-about-vpn-devices.md#a-namedevicetableavalidated-vpn-devices)をサポートしています。 オンプレミスの VPN デバイスを構成する前に、デバイス固有の構成設定を見直します。 同様に、Azure VPN Gateway は接続の確立に使用されている、[サポート対象の IPsec パラメーター](../vpn-gateway/vpn-gateway-about-vpn-devices.md#IPSec)のセットで構成されています。 現在、Azure VPN Gateway から特定の IPsec パラメーターの組み合わせを指定または選択する方法はありません。 オンプレミスと Azure との間の接続を正常に確立するには、オンプレミス VPN デバイスの設定が Azure VPN Gateway で規定されている IPsec パラメーターに従っている必要があります。 この規定に従っていない場合は、接続が失われます。これまではこれらの問題をトラブルシューティングするのは簡単ではなく、たいてい何時間もかけて問題を特定して、修正していました。

Azure Network Watcher のトラブルシューティング機能により、Gateway と Connections のどんな問題でも診断できるようになり、数分以内に十分な情報に基づいて問題を修正できるようになりました。

## <a name="scenario"></a>シナリオ

オンプレミスの VPN Gateway として Cisco ASA を使用して、Azure とオンプレミスとの間のサイト間接続を構成するとします。 このシナリオを成し遂げるには、次のセットアップが必要です。

1. Virtual Network Gateway - Azure 上の VPN Gateway
1. Local Network Gateway - Azure クラウドで表示されている、[オンプレミスの (CISCO ASA) VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)
1. サイト間接続 (ポリシー ベース) - [VPN Gateway とオンプレミスの CISCO ASA との間の接続](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#a-namecreateconnectiona8-create-a-site-to-site-vpn-connection)
1. [CISCO ASA の構成](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA)

サイト間構成を構成するための詳細なステップ バイ ステップ ガイダンスについては、「[Azure Portal を使用したサイト間接続を持つ VNet の作成](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)」をご覧ください。 

最も重要な構成の手順の 1 つは、IPsec コミュニケーション パラメーターの構成です。いかなる構成の誤りでも、オンプレミス ネットワークと Azure との間の接続は失われます。 Azure VPN Gateway は現在、以下のフェーズ 1 の IPsec パラメーターをサポートするように構成されています。 既に説明したように、これらの設定は修正できないことに注意してください。  次の表に示すように、Azure VPN Gateway でサポートされている暗号化アルゴリズムは、AES256、AES128、3DES です。

### <a name="ike-phase-1-setup"></a>IKE フェーズ 1 セットアップ

| **プロパティ** | **PolicyBased** | **RouteBased と Standard、または高性能 VPN ゲートウェイ** |
| --- | --- | --- |
| IKE のバージョン |IKEv1 |IKEv2 |
| Diffie-hellman グループ |グループ 2 (1024 ビット) |グループ 2 (1024 ビット) |
| 認証方法 |事前共有キー |事前共有キー |
| 暗号化アルゴリズム |AES256 AES128 3DES |AES256 3DES |
| ハッシュ アルゴリズム |SHA1(SHA128) |SHA1(SHA128)、SHA2(SHA256) |
| フェーズ 1 のセキュリティ アソシエーション (SA) の有効期間 (時間) |28,800 秒 |10,800 秒 |
 
ユーザーとして、CISCO ASA を構成する必要があります。構成のサンプルは [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Cisco/Current/ASA/ASA_9.1_and_above_Show_running-config.txt) で参照できます。 他の構成の中でもとりわけ、ハッシュ アルゴリズムを指定する必要があります。 CISCO ASA では、Azure VPN Gateway よりも多く[暗号化とハッシュ アルゴリズム](http://www.cisco.com/c/en/us/about/security-center/next-generation-cryptography.html)をサポートしています。 知らないうちに CISCO ASA に対して SHA-512 をハッシュ アルゴリズムとして使用するように構成したとします。 このアルゴリズムはポリシー ベースの接続でサポートされていないため、VPN 接続が機能しません。

これら問題をトラブルシューティングするのは難しく、根本的な原因は直感とは異なる場合が多いです。 この場合はサポート チケットを開いて、問題解決のためにサポートを受けることができます。 一方で、Azure Network Watcher のトラブルシューティング API を使用すると、これらの問題を自分で特定できます。 

## <a name="troubleshooting-using-azure-network-watcher"></a>Azure Network Watcher を使用したトラブルシューティング

接続を診断するには、Azure PowerShell に接続して `Start-AzureRmNetworkWatcherResourceTroubleshooting` コマンドレットを開始します。 このコマンドレットの使用に関する詳細については、[PowerShell を使用した仮想ネットワーク ゲートウェイと接続のトラブルシューティング](network-watcher-troubleshoot-manage-powershell.md)に関する記事をご覧ください。 このコマンドレットの完了には最大で数分かかる場合があります。 

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

### <a name="gateway"></a>ゲートウェイ

| エラーの種類 | 理由 | ログ|
|---|---|---|
| NoFault | エラーが検出されなかった場合。 |はい|
| GatewayNotFound | ゲートウェイが見つからなかったか、プロビジョニングされていません。 |いいえ|
| PlannedMaintenance |  ゲートウェイ インスタンスはメンテナンス中です。  |いいえ|
| UserDrivenUpdate | ユーザーの更新が進行中である場合。 サイズ変更操作が行われていると考えられます。 | いいえ |
| VipUnResponsive | ゲートウェイのプライマリ インスタンスに到達できません。 これは、正常性プローブでエラーが発生した場合に起こります。 | いいえ |
| PlatformInActive | プラットフォームに問題があります。 | なし|
| ServiceNotRunning | 基になるサービスが実行されていません。 | いいえ|
| NoConnectionsFoundForGateway | ゲートウェイ上に接続が存在しません。 これはただの警告です。| なし|
| ConnectionsNotConnected | どの接続も接続されていません。 これはただの警告です。| はい|
| GatewayCPUUsageExceeded | 現在のゲートウェイの CPU 使用率が 95% を超えています。 | はい |

### <a name="connection"></a>接続

| エラーの種類 | 理由 | ログ|
|---|---|---|
| NoFault | エラーが検出されなかった場合。 |はい|
| GatewayNotFound | ゲートウェイが見つからなかったか、プロビジョニングされていません。 |いいえ|
| PlannedMaintenance | ゲートウェイ インスタンスはメンテナンス中です。  |いいえ|
| UserDrivenUpdate | ユーザーの更新が進行中である場合。 サイズ変更操作が行われていると考えられます。  | いいえ |
| VipUnResponsive | ゲートウェイのプライマリ インスタンスに到達できません。 これは、正常性プローブでエラーが発生した場合に起こります。 | いいえ |
| ConnectionEntityNotFound | 接続の構成がありません。 | なし |
| ConnectionIsMarkedDisconnected | 接続が "切断" とマークされています。 |なし|
| ConnectionNotConfiguredOnGateway | 基になるサービスで接続が構成されていません。 | はい |
| ConnectionMarkedStandy | 基になるサービスがスタンバイとマークされています。| はい|
| 認証 | 事前共有キーが一致しません。 | はい|
| PeerReachability | ピア ゲートウェイに到達できません。 | はい|
| IkePolicyMismatch | ピア ゲートウェイに、Azure のサポート対象外の IKE ポリシーが設定されています。 | はい|
| WfpParse Error | WFP ログの解析中にエラーが発生しました。 |はい|

## <a name="next-steps"></a>次のステップ

PowerShell と Azure Automation を使用した VPN Gateway の接続の確認については、[Azure Network Watcher のトラブルシューティングを使用した VPN ゲートウェイの監視](network-watcher-monitor-with-azure-automation.md)に関する記事をご覧ください。

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png

