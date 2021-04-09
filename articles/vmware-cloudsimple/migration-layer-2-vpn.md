---
title: Azure VMware Solution by CloudSimple - オンプレミスのレイヤー 2 ネットワークをプライベート クラウドに拡張する
description: CloudSimple プライベート クラウドの NSX-T とオンプレミスのスタンドアロン NSX Edge クライアントの間にレイヤー 2 VPN を設定する方法について説明します
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 06446b6c36e36466fe891d7327d8151603cdecd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899373"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>レイヤー 2 拡張済みネットワークを使用したワークロードの移行

このガイドでは、レイヤー 2 VPN (L2VPN) を使用して、レイヤー 2 ネットワークをオンプレミス環境から CloudSimple プライベート クラウドに拡張する方法について説明します。 このソリューションでは、オンプレミスの VMware 環境で実行されているワークロードを、ワークロードの IP を再設定せずに、同じサブネットのアドレス空間内の Azure のプライベート クラウドに移行できます。

レイヤー 2 ネットワークの L2VPN ベースの拡張は、オンプレミスの VMware 環境に NSX ベースのネットワークがあってもなくても機能できます。 オンプレミスのワークロード用の NSX ベースのネットワークがない場合は、スタンドアロンの NSX Edge Services Gateway を使用できます。

> [!NOTE]
> このガイドでは、オンプレミスとプライベート クラウドのデータセンターがサイト間 VPN を介して接続されているシナリオについて説明します。

## <a name="deployment-scenario"></a>デプロイ シナリオ

L2VPN を使用してオンプレミス ネットワークを拡張するには、L2VPN サーバー (宛先の NSX-T Tier0 ルーター) と L2VPN クライアント (ソースのスタンドアロン クライアント) を構成する必要があります。  

このデプロイ シナリオでは、プライベート クラウドはサイト間 VPN トンネルを介してオンプレミス環境に接続されます。これにより、オンプレミスの管理および vMotion のサブネットが、プライベート クラウドの管理および vMotion のサブネットと通信できるようになります。 この準備は、Cross vCenter vMotion (xVC-vMotion) に必要です。 NSX-T Tier0 ルーターは、プライベート クラウドに L2VPN サーバーとしてデプロイされます。

スタンドアロンの NSX Edge は、オンプレミス環境に L2VPN クライアントとしてデプロイされ、その後、L2VPN サーバーとペアになります。 GRE トンネル エンドポイントが各側に作成され、オンプレミスのレイヤー 2 ネットワークをプライベート クラウドに "拡張" するように構成されます。 次の図でこの構成を示します。

![デプロイ シナリオ](media/l2vpn-deployment-scenario.png)

L2 VPN を使用した移行の詳細については、VMware ドキュメントの「[仮想プライベート ネットワーク](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704)」を参照してください。

## <a name="prerequisites-for-deploying-the-solution"></a>ソリューションをデプロイするための前提条件

ソリューションをデプロイおよび構成する前に、次の準備が整っていることを確認します。

* オンプレミスの vSphere のバージョンが 6.7U1 以降または 6.5P03 以降である。
* オンプレミスの vSphere のライセンスは、Enterprise Plus レベル (vSphere Distributed Switch の場合) である。
* プライベート クラウドに拡張されるワークロード レイヤー 2 ネットワークを特定する。
* L2VPN クライアント アプライアンスをデプロイするためにオンプレミス環境でレイヤー 2 ネットワークを特定する。
* [プライベート クラウドが既に作成されている](create-private-cloud.md)。
* スタンドアロンの NSX-T Edge アプライアンスのバージョンが、プライベート クラウド環境で使用されている NSX-T Manager のバージョン (NSX-T 2.3.0) と互換性がある。
* 偽装転送を有効にして、オンプレミスの vCenter にトランク ポート グループが作成されている。
* NSX-T スタンドアロン クライアントのアップリンク IP アドレスに使用するためのパブリック IP アドレスが予約済みであり、2 つのアドレス間の変換のために 1 対 1 の NAT が用意されている。
* az.cloudsimple.io ドメインがプライベート クラウドの DNS サーバーを参照するように、オンプレミスの DNS サーバーで DNS 転送が設定されている。
* VMotion が 2 つのサイト間で機能するために、RTT 待機時間は 150 ミリ秒以下である必要がある。

## <a name="limitations-and-considerations"></a>制限と考慮事項

次の表に、サポートされている vSphere のバージョンとネットワーク アダプターの種類を示します。  

| vSphere のバージョン | ソース vSwitch の種類 | 仮想 NIC ドライバー | ターゲット vSwitch の種類 | サポート対象かどうか |
------------ | ------------- | ------------ | ------------- | ------------- 
| All | DVS | All | DVS | はい |
| vSphere 6.7UI 以降、6.5P03 以降 | DVS | VMXNET3 | N-VDS | はい |
| vSphere 6.7UI 以降、6.5P03 以降 | DVS | E1000 | N-VDS | [VWware ごとにはサポートされない](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7UI または 6.5P03、NSX-V、あるいは NSX-T2.2 より下のバージョン、6.5P03 以降 | All | All | N-VDS | [VWware ごとにはサポートされない](https://kb.vmware.com/s/article/56991) |

VMware NSX-T 2.3 リリース時点:

* L2VPN 経由でオンプレミスに拡張されているプライベート クラウド側の論理スイッチを同時にルーティングすることはできません。 拡張された論理スイッチを論理ルーターに接続することはできません。
* L2VPN とルートベースの IPSEC VPN は、API 呼び出しを使用してのみ構成できます。

詳細については、VMware ドキュメントの「[仮想プライベート ネットワーク](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704)」を参照してください。

### <a name="sample-l2-vpn-deployment-addressing"></a>L2 VPN デプロイのアドレス指定の例

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>スタンドアロンの ESG (L2 VPN クライアント) がデプロイされているオンプレミス ネットワーク

| **Item** | **Value** |
|------------|-----------------|
| ネットワーク名 | MGMT_NET_VLAN469 |
| VLAN | 469 |
| CIDR| 10.250.0.0/24 |
| スタンドアロン Edge アプライアンスの IP アドレス | 10.250.0.111 |
| スタンドアロン Edge アプライアンスの NAT IP アドレス | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>拡張されるオンプレミス ネットワーク

| **Item** | **Value** |
|------------|-----------------|
| VLAN | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>NSX-T Tier0 ルーターのプライベート クラウド IP スキーマ (L2 VPN サーバー)

| **Item** | **Value** |
|------------|-----------------|
| ループバック インターフェイス | 192.168.254.254/32 |
| トンネル インターフェイス | 5.5.5.1/29 |
| 論理スイッチ (拡張済み) | Stretch_LS |
| ループバック インターフェイス (NAT IP アドレス) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>拡張されたネットワークにマップされるプライベート クラウド ネットワーク

| **Item** | **Value** |
|------------|-----------------|
| VLAN | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>L2VPN に必要な論理ルーター ID をフェッチする

次の手順では、IPsec サービスと L2VPN サービスの Tier0 DR 論理ルーター インスタンスの論理ルーター ID をフェッチする方法を示します。 後で L2VPN を実装するときに、論理ルーター ID が必要になります。

1. NSX-T Manager `https://*nsx-t-manager-ip-address*` にサインインし、 **[Networking]\(ネットワーク\)**  >  **[Routers]\(ルーター\)**  >  **[Provider-LR]\(プロバイダー LR\)**  >  **[Overview]\(概要\)** の順に選択します。 **[High Availability Mode]\(高可用性モード\)** については、 **[Active-Standby]\(アクティブ/スタンバイ\)** を選択します。 このアクションにより、Tier0 ルーターが現在アクティブになっている Edge VM を示すポップアップ ウィンドウが開きます。

    ![アクティブ/スタンバイを選択する](media/l2vpn-fetch01.png)

2. **[Fabric]\(ファブリック\)**  >  **[Nodes]\(ノード\)**  >  **[Edges]\(Edge\)** の順に選択します。 前の手順で特定したアクティブな Edge VM (Edge VM1) の管理 IP アドレスをメモしておきます。

    ![管理 IP をメモする](media/l2vpn-fetch02.png)

3. Edge VM の管理 IP アドレスへの SSH セッションを開きます。 ユーザー名 **admin** とパスワード **CloudSimple 123!** を使用して ```get logical-router``` コマンドを実行します。

    ![開いている SSH セッションを示すスクリーンショット。](media/l2vpn-fetch03.png)

4. "DR-Provider-LR" というエントリが表示されない場合は、次の手順を実行します。

5. オーバーレイに対応した 2 つの論理スイッチを作成します。 1 つの論理スイッチは、移行されたワークロードが存在するオンプレミスに拡張されます。 もう 1 つの論理スイッチはダミー スイッチです。 手順については、VMware ドキュメントの「[論理スイッチを作成する](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html)」を参照してください。

    ![論理スイッチを作成する](media/l2vpn-fetch04.png)

6. リンク ローカル IP アドレス、あるいはオンプレミスまたはプライベート クラウドからの重複していないサブネットを使用して、Tier1 ルーターにダミー スイッチを接続します。 VMware ドキュメントの「[Tier-1 論理ルーターにダウンリンク ポートを追加する](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html)」を参照してください。

    ![ダミー スイッチを接続する](media/l2vpn-fetch05.png)

7. Edge VM の SSH セッションで `get logical-router` コマンドを再実行します。 "DR-Provider-LR" 論理ルーターの UUID が表示されます。 UUID をメモします。これは、L2VPN を構成するときに必要になります。

    ![論理ルーターの UUID を示すスクリーンショット。](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>L2VPN に必要な論理スイッチ ID をフェッチする

1. NSX-T Manager (`https://nsx-t-manager-ip-address`) にサインインします。
2. **[Networking]\(ネットワーク\)**  >  **[Switching]\(スイッチ\)**  >  **[Switches]\(スイッチ\)**  >  **[<\Logical switch]\(<\論理スイッチ\)\>**  >  **[Overview]\(概要\)** を選択します。
3. 拡張された論理スイッチの UUID をメモしておきます。これは、L2VPN を構成するときに必要になります。

    ![論理ルーターの出力を取得する](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>L2VPN のルーティングとセキュリティに関する考慮事項

NSX-T Tier0 ルーターとスタンドアロンの NSX Edge クライアントの間に IPsec ルートベースの VPN を確立するには、NSX-T Tier0 ルーターのループバック インターフェイスが、UDP 500/4500 経由で、オンプレミスの NSX スタンドアロン クライアントのパブリック IP アドレスと通信できる必要があります。

### <a name="allow-udp-5004500-for-ipsec"></a>IPsec で UDP 500/4500 を許可する

1. CloudSimple ポータルで、NSX-T Tier0 ループバック インターフェイスの[パブリック IP アドレスを作成](public-ips.md)します。

2. UDP 500/4500 受信トラフィックを許可するステートフル ルールを使用して[ファイアウォール テーブルを作成](firewall.md)し、そのファイアウォール テーブルを NSX-T HostTransport サブネットに接続します。

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>ループバック インターフェイス IP をアンダーレイ ネットワークにアドバタイズする

1. ループバック インターフェイス ネットワークの Null ルートを作成します。 NSX-T Manager にサインインし、 **[Networking]\(ネットワーク\)**  >  **[Routing]\(ルーティング\)**  >  **[Routers]\(ルーター\)**  >  **[Provider-LR]\(プロバイダー LR\)**  >  **[Routing]\(ルーティング\)**  >  **[Static Routes]\(静的ルート\)** の順に選択します。 **[追加]** をクリックします。 **[Network]\(ネットワーク\)** については、ループバック インターフェイスの IP アドレスを入力します。 **[Next Hops]\(次ホップ\)** については、 **[Add]\(追加\)** をクリックし、次ホップに対して "Null" を指定し、[Admin Distance]\(管理者の距離\) については既定の 1 のままにします。

    ![静的ルートを追加する](media/l2vpn-routing-security01.png)

2. IP プレフィックス リストを作成します。 NSX-T Manager にサインインし、 **[Networking]\(ネットワーク\)**  >  **[Routing]\(ルーティング\)**  >  **[Routers]\(ルーター\)**  >  **[Provider-LR]\(プロバイダー LR\)**  >  **[Routing]\(ルーティング\)**  >  **[IP Prefix Lists]\(IP プレフィックス リスト\)** の順に選択します。 **[追加]** をクリックします。 リストを識別する名前を入力します。 **[Prefixes]\(プレフィックス\)** に対して、 **[Add]\(追加\)** を 2 回クリックします。 1 行目では、 **[Network]\(ネットワーク\)** に「0.0.0.0/0」と入力し、 **[Action]\(アクション\)** に「Deny」と入力します。 2 行目では、 **[Network]\(ネットワーク\)** に **[Any]\(任意\)** 、 **[Action]\(アクション\)** に **[Permit]\(許可\)** を選択します。
3. IP プレフィックス リストを両方の BGP 近隣ノード (TOR) にアタッチします。 IP プレフィックス リストを BGP 近隣ノードにアタッチすると、既定のルートが TOR スイッチに対して BGP でアドバタイズされなくなります。 ただし、Null ルートを含むその他のルートは、TOR スイッチに対してループバック インターフェイスの IP アドレスをアドバタイズします。

    ![IP プレフィックス リストを作成する](media/l2vpn-routing-security02.png)

4. NSX-T Manager にサインインし、 **[Networking]\(ネットワーク\)**  >  **[Routing]\(ルーティング\)**  >  **[Routers]\(ルーター\)**  >  **[Provider-LR]\(プロバイダー LR\)**  >  **[Routing]\(ルーティング\)**  >  **[BGP]**  >  **[Neighbors]\(近隣ノード\)** の順に選択します。 最初の近隣ノードを選択します。 **[Edit]\(編集\)**  >  **[Address Families]\(アドレス ファミリ\)** の順にクリックします。 IPv4 ファミリの場合は、 **[Out Filter]\(出力フィルター\)** 列を編集し、作成した IP プレフィックス リストを選択します。 **[保存]** をクリックします。 2 番目の近隣ノードに対してこの手順を繰り返します。

    ![IP プレフィックス リストをアタッチする 1](media/l2vpn-routing-security03.png) ![IP プレフィックス リストをアタッチする 2](media/l2vpn-routing-security04.png)

5. Null 静的ルートを BGP に再配布します。 ループバック インターフェイス ルートをアンダーレイにアドバタイズするには、Null 静的ルートを BGP に再配布する必要があります。 NSX-T Manager にサインインし、 **[Networking]\(ネットワーク\)**  >  **[Routing]\(ルーティング\)**  >  **[Routers]\(ルーター\)**  >  **[Provider-LR]\(プロバイダー LR\)**  >  **[Routing]\(ルーティング\)**  >  **[Route Redistribution]\(ルートの再配布)**  >  **[Neighbors]\(近隣\)** の順に選択します。 **[Provider-LR-Route_Redistribution]** を選択し、 **[Edit]\(編集\)** をクリックします。 **[Static]\(静的\)** チェック ボックスを選択し、 **[Save]\(保存\)** をクリックします。

    ![Null 静的ルートを BGP に再配布する](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>NSX-T Tier0 ルーターでルートベースの VPN を構成する

次のテンプレートを使用して、NSX-T Tier0 ルーターでルートベースの VPN を構成するためのすべての詳細情報を入力します。 後続の POST 呼び出しでは、各 POST 呼び出しの UUID が必要です。 L2VPN のループバック インターフェイスとトンネル インターフェイスの IP アドレスは一意である必要があり、オンプレミスまたはプライベート クラウドのネットワークと重複することはできません。

L2VPN に使用されるループバック インターフェイスとトンネル インターフェイスに対して選択される IP アドレスは一意である必要があり、オンプレミスまたはプライベート クラウドのネットワークと重複することはできません。 ループバック インターフェイス ネットワークは、常に /32 である必要があります。

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section "Steps to fetch Logical-Router ID needed for L2VPN"
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

次のすべての API 呼び出しについては、IP アドレスを NSX-T Manager の IP アドレスに置き換えます。 これらの API 呼び出しはすべて、POSTMAN クライアントから、または `curl` コマンドを使用して実行できます。

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>論理ルーターで IPSec VPN サービスを有効にする

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>プロファイルの作成:IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>プロファイルの作成:DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>プロファイルの作成:Tunnel

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>ローカル エンドポイントを作成する

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>ピア エンドポイントを作成する

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>ルートベースの VPN セッションを作成する

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>NSX-T Tier0 ルーターで L2VPN を構成する

すべての POST 呼び出しの後に、次の情報を入力します。 これらの ID は、後続の POST 呼び出しで必要になります。

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>L2VPN サービスを作成する

構成がまだ完了していないため、次の GET コマンドの出力は空白になります。

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

次の POST コマンドで、論理ルーター ID は、前に取得した Tier0 DR 論理ルーターの UUID です。

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>L2VPN セッションを作成する

次の POST コマンドで、L2VPN サービス ID はたった今取得した ID であり、IPsec VPN セッション ID は前のセクションで取得した ID です。

```    
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

これらの呼び出しは、GRE トンネル エンドポイントを作成します。 状態を確認するには、次のコマンドを実行します。

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>指定されたトンネル ID を使用して論理ポートを作成する

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>NSX-T 側で L2VPN のピア コードを取得する

NSX-T エンドポイントのピア コードを取得します。 ピア コードは、リモート エンドポイントを構成するときに必要です。 L2VPN <session-id> は、前のセクションから取得できます。 詳細については、[NSX-T 2.3 の API ガイド](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html)を参照してください。

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>NSX-T スタンドアロン クライアント (オンプレミス) をデプロイする

デプロイする前に、オンプレミスのファイアウォール規則で、以前に NSX-T T0 ルーターのループバック インターフェイス用に予約されていた CloudSimple のパブリック IP アドレスとの間の UDP 500/4500 の送受信トラフィックが許可されていることを確認します。 

1. [スタンドアロンの NSX Edge クライアント OVF をダウンロード](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230)し、ダウンロードしたバンドルからフォルダーにファイルを抽出します。

    ![スタンドアロンの NSX Edge クライアントをダウンロードする](media/l2vpn-deploy-client01.png)

2. 抽出したすべてのファイルが含まれているフォルダーにアクセスします。 すべての vmdk (大きいアプライアンス サイズ用の NSX-l2t-client-large.mf と NSX-l2t-client-large.ovf、または特大のアプライアンス サイズ用の NSX-l2t-client-Xlarge.mf と NSX-l2t-client-Xlarge.ovf) を選択します。 **[次へ]** をクリックします。

    ![テンプレートの選択](media/l2vpn-deploy-client02.png) ![選択された vmdks ファイルを表示するスクリーンショット。](media/l2vpn-deploy-client03.png)

3. NSX-T スタンドアロン クライアントの名前を入力し、 **[次へ]** をクリックします。

    ![テンプレート名を入力する](media/l2vpn-deploy-client04.png)

4. 必要に応じて **[次へ]** をクリックして、データストアの設定に移動します。 NSX-T スタンドアロン クライアントに適したデータストアを選択し、 **[次へ]** をクリックします。

    ![データストアを選択する](media/l2vpn-deploy-client06.png)

5. NSX-T スタンドアロン クライアントのトランク (トランク PG)、パブリック (アップリンク PG)、HA インターフェイス (アップリンク PG) に対して正しいポート グループを選択します。 **[次へ]** をクリックします。

    ![ポート グループを選択する](media/l2vpn-deploy-client07.png)

6. **[Customize template]\(テンプレートのカスタマイズ\)** 画面で次の詳細を入力して、 **[次へ]** をクリックします。

    L2T を展開します。

    * **[Peer Address]\(ピア アドレス)** 。 NSX-T Tier0 ループバック インターフェイス用に Azure CloudSimple ポータルで予約されている IP アドレスを入力します。
    * **[Peer Code]\(ピア コード\)** 。 L2VPN サーバー デプロイの最後の手順で取得したピア コードを貼り付けます。
    * **[Sub Interfaces VLAN (Tunnel ID)]\(サブインターフェイス VLAN (トンネル ID)\)** 。 拡張する VLAN ID を入力します。 かっこ () に、以前に構成したトンネル ID を入力します。

    アップリンク インターフェイスを展開します。

    * **[DNS IP Address]\(DNS IP アドレス\)** 。 オンプレミスの DNS IP アドレスを入力します。
    * **[Default Gateway]\(既定のゲートウェイ\)** 。  このクライアントの既定のゲートウェイとして機能する VLAN の既定のゲートウェイを入力します。
    * **[IP Address]\(IP アドレス\)** 。 スタンドアロン クライアントのアップリンク IP アドレスを入力します。
    * **[Prefix Length]\(プレフィックス長\)** 。 アップリンク VLAN/サブネットのプレフィックス長を入力します。
    * **[CLI admin/enable/root User Password]\(CLI の admin/enable/root の各ユーザーのパスワード\)** 。 admin/enable/root のアカウントのパスワードを設定します。

      ![テンプレートをカスタマイズする](media/l2vpn-deploy-client08.png)
      ![テンプレートをカスタマイズする - 続き](media/l2vpn-deploy-client09.png)

7. 設定を確認し、 **[Finish]\(完了\)** をクリックします。

    ![構成を完了する](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>オンプレミスのシンク ポートを構成する

いずれかの VPN サイトに NSX がデプロイされていない場合は、そのサイトにスタンドアロンの NSX Edge をデプロイすることで L2 VPN を構成できます。 スタンドアロンの NSX Edge は、NSX で管理されていないホストに OVF ファイルを使用してデプロイされます。 これにより、NSX Edge Services Gateway アプライアンスがデプロイされて、L2 VPN クライアントとして機能します。

スタンドアロンの Edge のトランク vNIC が vSphere Distributed Switch に接続されている場合は、L2 VPN 機能に無作為検出モードまたはシンク ポートが必要です。 無作為検出モードを使用すると、ping の重複や応答の重複が発生する可能性があります。 このため、L2 VPN スタンドアロン NSX Edge の構成では、シンク ポート モードを使用します。 VMware ドキュメントの「[シンク ポートを構成する](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html)」を参照してください。

## <a name="ipsec-vpn-and-l2vpn-verification"></a>IPsec VPN と L2VPN の検証

次のコマンドを使用して、スタンドアロン NSX-T Edge から IPsec と L2VPN のセッションを確認します。

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

次のコマンドを使用して、NSX-T Tier0 ルーターから IPsec と L2VPN のセッションを確認します。

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

次のコマンドを使用して、オンプレミス環境で NSX-T スタンドアロン クライアント VM が存在する ESXi ホスト上のシンク ポートを確認します。

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
