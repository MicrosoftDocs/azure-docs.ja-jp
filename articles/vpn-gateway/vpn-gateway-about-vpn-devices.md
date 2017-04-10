---
title: "クロスプレミス Azure 接続の VPN デバイスについて | Microsoft Docs"
description: "この記事では、S2S VPN Gateway クロスプレミス接続の VPN デバイスと IPsec パラメーターについて説明します。 構成の手順およびサンプルへのリンクが提供されています。"
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: ba449333-2716-4b7f-9889-ecc521e4d616
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/03/2017
ms.author: yushwang;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: ab0a0ecba9d9e930cbc0ec5d4b83e252d2e50f7b
ms.lasthandoff: 03/16/2017


---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>サイト間 VPN ゲートウェイ接続用の VPN デバイスと IPsec/IKE パラメーターについて

VPN ゲートウェイを使用する Site-to-Site (S2S) クロスプレミス VPN 接続を構成するには、VPN デバイスが必要です。 サイト間接続は、ハイブリッド ソリューションを作成するときに使用できるほか、オンプレミスのネットワークと仮想ネットワークとの間にセキュリティで保護された接続が必要な場合に使用できます。 この記事には、Azure VPN ゲートウェイに使用される IPsec/IKE パラメーターの一覧と、Azure VPN ゲートウェイに接続する検証済みの VPN デバイスの一覧が掲載されています。


> [!IMPORTANT]
> オンプレミスの VPN デバイスと Azure VPN ゲートウェイの間で接続の問題が発生している場合は、「[デバイスの互換性に関する既知の問題](#known)」を参照してください。 


###<a name="items-to-note-when-viewing-the-tables"></a>表を確認するときの注意事項:

* Azure VPN ゲートウェイ関連の用語が変更されています。 おそらく、両方の用語が見つかるでしょう。 機能上の変更はありませんが、名前のみが変更されています。
  * 静的ルーティング = PolicyBased
  * 動的ルーティング = RouteBased
* 高性能 VPN ゲートウェイと RouteBased VPN ゲートウェイの仕様は、特に記載がない限り同じです。 たとえば、RouteBased VPN ゲートウェイと互換性がある検証済みの VPN デバイスは、Azure 高性能 VPN ゲートウェイとも互換性があります。

> [!NOTE]
> サイト間接続を構成するときには、VPN デバイスに公開 IPv4 IP アドレスが必要です。                                                                                                                                                                               


## <a name="devicetable"></a>検証済みの VPN デバイス
Microsoft では、デバイス ベンダーと協力して一連の標準的な VPN デバイスを検証しました。 以下の一覧に含まれているデバイス ファミリ内のすべてのデバイスは、Azure VPN ゲートウェイで動作します。 「 [VPN Gateway について](vpn-gateway-about-vpngateways.md) 」を参照して、構成するソリューションで作成する必要があるゲートウェイの種類を確認してください。

VPN デバイスを構成するには、適切なデバイス ファミリに対応するリンクを参照してください。 VPN デバイスのサポートについては、デバイスの製造元に問い合わせてください。

|**ベンダー名**          |**デバイス ファミリ**     |**OS の最小バージョン** |**PolicyBased** |**RouteBased** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |互換性なし  |[構成ガイド](https://www.a10networks.com/resources/deployment-guides/a10-thunder-cfw-ipsec-vpn-interoperability-azure-vpn-gateways)|
| Allied Telesis     |AR シリーズ VPN ルーター |2.9.2                  |近日対応予定     |互換性なし  |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall F シリーズ |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[構成ガイド](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[構成ガイド](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall X シリーズ |Barracuda Firewall 6.5 |[構成ガイド](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |互換性なし |
| Brocade            |Vyatta 5400 vRouter   |仮想ルーター 6.6R3 GA|[構成ガイド](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |互換性なし |
| Check Point |セキュリティ ゲートウェイ |R77.30 |[構成ガイド](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[構成ガイド](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3 |[構成のサンプル](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) |互換性なし |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |[構成のサンプル](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |[構成のサンプル](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |[構成のサンプル](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |[構成のサンプル*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix |NetScaler MPX、SDX、VPX |10.1 以上 |[構成ガイド](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |互換性なし |
| Dell SonicWALL |TZ シリーズ、NSA シリーズ<br>SuperMassive シリーズ<br>E-class NSA シリーズ |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |[SonicOS 6.2 の構成ガイド](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[SonicOS 5.9 の構成ガイド](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |[SonicOS 6.2 の構成ガイド](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[SonicOS 5.9 の構成ガイド](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 |BIG-IP シリーズ |12.0 |[構成ガイド](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[構成ガイド](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.4.2 |[構成ガイド](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |[構成ガイド](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |
| Internet Initiative Japan (IIJ) |SEIL シリーズ |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[構成ガイド](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |互換性なし |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |[構成のサンプル](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |[構成のサンプル](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper |J シリーズ |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |[構成のサンプル](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |[構成のサンプル](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper |ISG |ScreenOS 6.3 |[構成のサンプル](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |[構成のサンプル](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper |SSG |ScreenOS 6.2 |[構成のサンプル](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |[構成のサンプル](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft |ルーティングとリモート アクセス サービス |Windows Server 2012 |互換性なし |[構成のサンプル](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG |Mission Control Security Gateway |該当なし |[構成ガイド](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |[構成ガイド](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan |Openswan |2.6.32 |(近日対応予定) |互換性なし |
| Palo Alto Networks |PAN-OS を実行しているすべてのデバイス |PAN-OS<br>PolicyBased: 6.1.5 以降<br>RouteBased: 7.1.4 |[構成ガイド](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[構成ガイド](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| WatchGuard |すべて |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[構成ガイド](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[構成ガイド](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

(*) ISR 7200 シリーズのルーターは、PolicyBased の VPN だけをサポートしています。

## <a name="additionaldevices"></a>未検証の VPN デバイス
検証済み VPN デバイスの表にお使いのデバイスが見つからない場合でも、そのデバイスをサイト間接続に利用できる可能性があります。 詳細なサポートと構成手順については、デバイスの製造元にお問い合わせください。

## <a name="editing"></a>デバイス構成のサンプルの編集
提供されている VPN デバイス構成のサンプルをダウンロードしたら、一部の値を使用している環境の設定を反映した値に置換する必要があります。

###<a name="to-edit-a-sample"></a>サンプルを編集するには:

1. メモ帳を使用してサンプルを開きます。
2. お使いの環境に関連する値を含む <*テキスト*> 文字列をすべて検索して置き換えます。 < と > を必ず含めてください。 名前を指定する場合、選択する名前は一意である必要があります。 コマンドが機能しない場合は、デバイスの製造元のドキュメントを参照してください。

| **サンプル テキスト** | **次に変更** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |このオブジェクトに選択した名前。 例: myOnPremisesNetwork |
| &lt;RP_AzureNetwork&gt; |このオブジェクトに選択した名前。 例: myAzureNetwork |
| &lt;RP_AccessList&gt; |このオブジェクトに選択した名前。 例: myAzureAccessList |
| &lt;RP_IPSecTransformSet&gt; |このオブジェクトに選択した名前。 例: myIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |このオブジェクトに選択した名前。 例: myIPSecCryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |範囲を指定します。 例：192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |サブネット マスクを指定します。 例: 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |オンプレミスの範囲を指定します。 例: 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |オンプレミスのサブネット マスクを指定します。 例: 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |この情報は仮想ネットワークに固有であり、 **ゲートウェイの IP アドレス**として管理ポータルに存在しています。 |
| &lt;SP_PresharedKey&gt; |この情報は仮想ネットワークに固有であり、[キーの管理] として管理ポータルに存在しています。 |

## <a name="IPSec"></a>IPsec/IKE パラメーター
> [!NOTE]
> 以下の表に記載した値は Azure VPN Gateway でサポートされていますが、Azure VPN Gateway の特定のアルゴリズムやパラメーターの組み合わせを指定 (または選択) する機構は現在ありません。 すべての制約は、オンプレミスの VPN デバイスから指定する必要があります。
> 
> また、**MSS** は **1350** で固定する必要があります。

以下の表で使われている用語について:

* SA = セキュリティ アソシエーション (Security Association)
* IKE フェーズ 1 は "メイン モード" と呼ばれることもあります。
* IKE フェーズ 2 は "クイック モード" と呼ばれることもあります。

### <a name="ike-phase-1-main-mode-parameters"></a>IKE フェーズ 1 (メイン モード) のパラメーター
| **プロパティ**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| IKE のバージョン           |IKEv1              |IKEv2              |
| Diffie-hellman グループ  |グループ 2 (1024 ビット) |グループ 2 (1024 ビット) |
| 認証方法 |事前共有キー     |事前共有キー     |
| 暗号化とハッシュ アルゴリズム |1.AES256、SHA256<br>2.AES256、SHA1<br>3.AES128、SHA1<br>4. 3DES、SHA1 |1.AES256、SHA1<br>2.AES256、SHA256<br>3.AES128、SHA1<br>4.AES128、SHA256<br>5. 3DES、SHA1<br>6. 3DES、SHA256 |
| SA の有効期間           |28,800 秒     |28,800 秒     |

### <a name="ike-phase-2-quick-mode-parameters"></a>IKE フェーズ 2 (クイック モード) のパラメーター
| **プロパティ**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| IKE のバージョン                   |IKEv1          |IKEv2                                        |
| 暗号化とハッシュ アルゴリズム |1.AES256、SHA256<br>2.AES256、SHA1<br>3.AES128、SHA1<br>4. 3DES、SHA1 |[RouteBased QM SA プラン](#RouteBasedOffers) |
| SA の有効期間 (時間)            |3,600 秒  |27,000 秒                                |
| SA の有効期間 (バイト)           |102,400,000 KB | -                                           |
| Perfect Forward Secrecy (PFS) |いいえ             |[RouteBased QM SA プラン](#RouteBasedOffers) |
| Dead Peer Detection (DPD)     |サポートされていません  |サポートされています                                    |


### <a name ="RouteBasedOffers"></a>RouteBased VPN IPsec セキュリティ アソシエーション (IKE クイック モード SA) プラン
以下の表は、IPsec SA (IKE クイック モード) プランの一覧です。 プランは提示される、または受け入れられる優先度順に表示されています。

#### <a name="azure-gateway-as-initiator"></a>発信側としての Azure ゲートウェイ
|-  |**暗号化**|**認証**|**PFS グループ**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |なし         |
| 2 |AES256        |SHA1              |なし         |
| 3 |3DES          |SHA1              |なし         |
| 4 |AES256        |SHA256            |なし         |
| 5 |AES128        |SHA1              |なし         |
| 6 |3DES          |SHA256            |なし         |

#### <a name="azure-gateway-as-responder"></a>応答側としての Azure ゲートウェイ
|-  |**暗号化**|**認証**|**PFS グループ**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |なし         |
| 2 |AES256        |SHA1              |なし         |
| 3 |3DES          |SHA1              |なし         |
| 4 |AES256        |SHA256            |なし         |
| 5 |AES128        |SHA1              |なし         |
| 6 |3DES          |SHA256            |なし         |
| 7 |DES           |SHA1              |なし         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |なし         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* RouteBased および高性能 VPN ゲートウェイで IPsec ESP NULL 暗号化を指定することができます。 Null ベースの暗号化では、転送中のデータ保護は提供されません。そのため、最大のスループットおよび最小の待機時間が必要な場合にのみ使用する必要があります。  クライアントは、VNet 間の通信シナリオ、または暗号化がソリューションの他の場所に適用されている場合に、この暗号化の使用を選択することができます。
* インターネット経由のクロスプレミス接続では、重要な通信のセキュリティを確保するため、上記の表にある暗号化およびハッシュ アルゴリズムによる既定の Azure VPN Gateway 設定を使用してください。

## <a name="known"></a>デバイスの互換性に関する既知の問題

> [!IMPORTANT]
> この内容は、サード パーティの VPN デバイスと Azure VPN ゲートウェイの互換性に関する既知の問題です。 Azure チームは、ここに記載されている問題に対処するためにベンダーと積極的に連携しています。 問題が解決されると、このページが最新の情報で更新されるため、 定期的に確認してください。

###<a name="feb-16-2017"></a>2017 年 2 月 16 日

Azure ルートベースの VPN 用の **Palo Alto Networks の 7.1.4 より前のバージョンのデバイス**: Palo Alto Networks の PAN-OS のバージョンが 7.1.4 より前の VPN デバイスを使用している場合、Azure ルートベースの VPN ゲートウェイへの接続の問題が発生したときは次の手順を実行してください。

1. Palo Alto Networks デバイスのファームウェアのバージョンを確認します。 PAN-OS バージョンが 7.1.4 よりも前の場合は、7.1.4 にアップグレードしてください。
2. Palo Alto Networks デバイスで、Azure VPN ゲートウェイに接続しているときにフェーズ 2 SA (またはクイック モード SA) の有効期間を 28,800 秒 (8 時間) に変更します。
3. 接続の問題が解消しない場合は、Azure Portal からサポート リクエストを作成してください。

