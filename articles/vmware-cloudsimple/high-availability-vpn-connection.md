---
title: CloudSimple による Azure VMware ソリューション - オンプレミスから CloudSimple VPN ゲートウェイへの高可用性の構成
description: 高可用性のために、オンプレミス環境から CloudSimple VPN ゲートウェイへの高可用接続を構成する方法について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e3118814eacc6cc63b5db59bd7f1877c1d347dc
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927305"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>オンプレミスから CloudSimple VPN ゲートウェイへの高可用接続を構成する

ネットワーク管理者は、オンプレミス環境から CloudSimple VPN ゲートウェイへの高可用 IPsec サイト間 VPN 接続を構成できます。

このガイドでは、IPsec サイト間 VPN 高可用接続のためにオンプレミスのファイアウォールを構成する手順について説明します。 詳細な手順は、オンプレミスのファイアウォールの種類によって異なります。 このガイドでは、例として、2 種類のファイアウォールについて手順を示します。Cisco ASA と Palo Alto Networks です。

## <a name="before-you-begin"></a>開始する前に

オンプレミスのファイアウォールを構成する前に、次のタスクを実行します。

1. お客様の組織が必要なノードを[プロビジョニング](create-nodes.md)し、少なくとも 1 つの CloudSimple Private Cloud を作成していることを確認します。
2. オンプレミス ネットワークと CloudSimple Private Cloud の間に[サイト間 VPN ゲートウェイを構成](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)します。

サポートされているフェーズ 1 とフェーズ 2 の提案については、「[VPN ゲートウェイの概要](cloudsimple-vpn-gateways.md)」を参照してください。

## <a name="configure-on-premises-cisco-asa-firewall"></a>オンプレミスの Cisco ASA ファイアウォールを構成する

このセクションの手順は、Cisco ASA バージョン 8.4 以降に適用されます。 この構成の例では、Cisco Adaptive Security Appliance Software バージョン 9.10 がデプロイされ、IKEv1 モードで構成されています。

サイト間 VPN を機能させるには、オンプレミスの Cisco ASA VPN ゲートウェイの外部インターフェイス上で、CloudSimple のプライマリおよびセカンダリ パブリック IP (ピア IP) から UDP 500/4500 と ESP (IP プロトコル 50) を許可する必要があります。

### <a name="1-configure-phase-1-ikev1"></a>1.フェーズ 1 (IKEv1) を構成する

外部インターフェイス上でフェーズ 1 (IKEv1) を有効にするには、Cisco ASA ファイアウォールで次の CLI コマンドを入力します。

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2.IKEv1 ポリシーを作成する

ハッシュ、認証、Diffie-Hellman グループ、有効期間、および暗号化に使用するアルゴリズムとメソッドを定義する IKEv1 ポリシーを作成します。

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3.トンネル グループを作成する

IPsec 属性以下にトンネル グループを作成します。 ピア IP アドレスとトンネル事前共有キーを構成します。これは、[サイト間 VPN ゲートウェイを構成する](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)ときに設定します。

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4.フェーズ 2 (IPsec) を構成する

フェーズ 2 (IPsec) を構成するには、暗号化およびトンネリングするトラフィックを定義するアクセス制御リスト (ACL) を作成します。 次の例では、オンプレミスのローカル サブネット (10.16.1.0/24) から Private Cloud のリモート サブネット (192.168.0.0/24) までがソースのトラフィックが対象です。 サイト間に複数のサブネットがある場合は、ACL に複数のエントリを含めることができます。

Cisco ASA バージョン8.4 以降では、ネットワーク、サブネット、ホスト IP アドレス、または複数のオブジェクトのコンテナーとして機能するオブジェクトまたはオブジェクト グループを作成できます。 ローカルのオブジェクトとリモート サブネットのオブジェクトを作成し、それらを暗号化 ACL および NAT ステートメントに使用します。

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>オンプレミスのローカル サブネットをオブジェクトとして定義する

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>CloudSimple リモート サブネットをオブジェクトとして定義する

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>対象のトラフィックのアクセス リストを構成する

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5.変換セットを構成する

変換セット (TS) を構成します。これにはキーワード ```ikev1``` を使用する必要があります。 TS に指定された暗号化属性とハッシュ属性は、「[CloudSimple VPN ゲートウェイの既定の構成](cloudsimple-vpn-gateways.md)」に記載されているパラメーターと一致する必要があります。

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6.暗号化マップを構成する

次のコンポーネントを含む暗号化マップを構成します。

* ピア IP アドレス
* 対象のトラフィックを含む定義済み ACL
* 変換セット

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7.暗号化マップを適用する

外部インターフェイスに暗号化マップを適用します。

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8.適用できる NAT 規則を確認する

使用される NAT 規則を次に示します。 VPN トラフィックが他の NAT 規則の対象になっていないことを確認します。

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Cisco ASA からの IPsec サイト間 VPN の確立された出力の例

フェーズ 1 の出力:

![Cisco ASA ファイアウォールのフェーズ 1 の出力](media/ha-vpn-connection-cisco-phase1.png)

フェーズ 2 の出力:

![Cisco ASA ファイアウォールのフェーズ 2 の出力](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>オンプレミスの Palo Alto Networks ファイアウォールを構成する

このセクションの手順は、Palo Alto Networks バージョン 7.1 以降に適用されます。 この構成例では、Palo Alto Networks VM シリーズ ソフトウェア バージョン 8.1.0 が IKEv1 モードでデプロイおよび構成されます。

サイト間 VPN を機能させるには、オンプレミスの Palo Alto Networks ゲートウェイの外部インターフェイス上で、CloudSimple のプライマリおよびセカンダリ パブリック IP (ピア IP) から UDP 500/4500 と ESP (IP プロトコル 50) を許可する必要があります。

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1.プライマリおよびセカンダリ トンネル インターフェイスを作成する

Palo Alto ファイアウォールにサインインし、 **[Network]\(ネットワーク\)**  >  **[Interfaces]\(インターフェイス\)**  >  **[Tunnel]\(トンネル\)**  >  **[Add]\(追加\)** を選択し、次のフィールドを構成し、 **[OK]** をクリックします。

* [Interface Name]\(インターフェイス名\)。 最初のフィールドには、キーワード "tunnel" が自動入力されます。 隣接するフィールドに、1 から 9999 の任意の数を入力します。 このインターフェイスは、オンプレミスのデータセンターと Private Cloud の間でサイト間トラフィックを伝送するプライマリ トンネル インターフェイスとして使用されます。
* [Comment]\(コメント\)。 トンネルの目的を簡単に識別できるように、コメントを入力します
* [Netflow Profile]\(Netflow プロファイル\)。 既定値のままにします。
* [Config]\(構成\)。[Assign Interface To]\(インターフェイスの割り当て先\):[Virtual Router]\(仮想ルーター\): **[default]\(既定値\)** を選択します。 
        [Security Zone]\(セキュリティ ゾーン\):信頼されている LAN トラフィックのゾーンを選択します。 この例では、LAN トラフィックのゾーン名は "Trust" です。
* [IPv4]。 **[Add]\(追加\)** をクリックし、お客様の環境内で重複せず、使用されていない /32 IP アドレスを追加します。これは、プライマリ トンネル インターフェイスに割り当てられ、トンネルの監視に使用されます (後述します)。

この構成は高可用性 VPN 用なので、2 つのトンネル インターフェイスが必要です。1 つのプライマリと 1 つのセカンダリです。 前の手順を繰り返して、セカンダリ トンネル インターフェイスを作成します。 別のトンネル ID と、別の使用されていない /32 IP アドレスを選択します。

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2.サイト間 VPN 経由で Private Cloud のサブネットに到達するように静的ルートを設定する

オンプレミスのサブネットが CloudSimple プライベート クラウドのサブネットに接続するためのルートが必要です。

**[Network]\(ネットワーク\)**  >  **[Virtual Routers]\(仮想ルート\)**  >  *[default]\(既定値\)*  >  **[Static Routes]\(静的ルート\)**  >  **[Add]\(追加\)** を選択し、次のフィールドを構成して、 **[OK]** をクリックします。

* [Name]\(名前\)。 ルートの目的を簡単に識別できるように、任意の名前を入力します。
* 宛先。 オンプレミスから S2S トンネル インターフェイス経由で到達できる CloudSimple プライベート クラウドのサブネットを指定します
* [Interface]\(インターフェイス\)。 ドロップダウンから、手順 1 (セクション 2) で作成したプライマリ トンネル インターフェイスを選択します。 この例では、tunnel.20 です。
* [Next Hop]\(次ホップ\)。 **[なし]** を選択します。
* [Admin Distance]\(アドミニストレーティブ ディスタンス\)。 既定値のままにします。
* [Metric]\(メトリック\)。 1 から 65535 の任意の値を入力します。 重要なのは、セカンダリ トンネル インターフェイスに対応するルートと比較して、プライマリ トンネル インターフェイスに対応するルートに低いメトリックを入力して、前のルートを優先させることです。 tunnel.30 のメトリック値が 30 に対して tunnel.20 のメトリック値が 20 の場合は、tunnel.20 が優先されます。
* [Route Table]\(ルート テーブル\)。 既定値のままにします。
* [BFD Profile]\(BFD プロファイル\)。 既定値のままにします。
* [Path monitoring]\(パスの監視\)。 オフのままにします。

前の手順を繰り返して、セカンダリ トンネル インターフェイス経由でセカンダリ/バックアップ ルートとして使用する Private Cloud のサブネットに別のルートを作成します。 今回は、プライマリ ルートとは異なるトンネル ID とより高いメトリックを選択します。

### <a name="3-define-the-cryptographic-profile"></a>3.暗号化プロファイルを定義する

IKEv1 フェーズ 1 で VPN トンネルを設定するために使用する ID、認証、および暗号化のプロトコルとアルゴリズムを指定する暗号化プロファイルを定義します。

**[Network]\(ネットワーク\)**  >  **[Expand Network Profiles]\(ネットワーク プロファイルの展開\)**  >  **[IKE Crypto]\(IKE 暗号化\)**  >  **[Add]\(追加\)** を選択し、次のフィールドを構成し、 **[OK]** をクリックします。

* [Name]\(名前\)。 IKE 暗号化プロファイルの名前を入力します。
* [DH Group]\(DH グループ\)。 **[Add]\(追加\)** をクリックし、適切な DH グループを選択します。
* 暗号化。 **[Add]\(追加\)** をクリックし、適切な暗号化方法を選択します。
* 認証 **[Add]\(追加\)** をクリックし、適切な認証方法を選択します。
* [Key lifetime]\(キーの有効期間\)。 既定値のままにします。
* [IKEv2 Authentication Multiple]\(複数の IKEv2 認証\)。 既定値のままにします。

### <a name="4-define-ike-gateways"></a>4.IKE ゲートウェイを定義する

VPN トンネルの両端にわたるピア間の通信を確立するには、IKE ゲートウェイを定義します。

**[Network]\(ネットワーク\)**  >  **[Expand Network Profiles]\(ネットワーク プロファイルの展開\)**  >  **[IKE Gateways]\(IKE ゲートウェイ\)**  >  **[Add]\(追加\)** を選択し、次のフィールドを構成し、 **[OK]** をクリックします。

[General]\(全般\) タブ:

* [Name]\(名前\)。 プライマリ CloudSimple VPN ピアとピアリングされる IKE ゲートウェイの名前を入力します。
* バージョン。 **[IKEv1 only mode]\(IKEv1 のみモード\)** を選択します。
* [Address Type]\(アドレスの種類\)。 **[IPv4]** を選択します。
* [Interface]\(インターフェイス\)。 パブリック向けまたは外部インターフェイスを選択します。
* [Local IP Address]\(ローカル IP アドレス\)。 既定値のままにします。
* [Peer IP Address Type]\(ピア IP アドレスの種類\)。 **[IP]** を選択します。
* [Peer Address]\(ピア アドレス\)。 プライマリ CloudSimple VPN ピアの IP アドレスを入力します。
* 認証 **[Pre-Shared Key]\(事前共有キー\)** を選択します。
* [Pre-shared Key]\(事前共有キー\)、[Confirm Pre-shared Key]\(事前共有キーの確認入力\)。 CloudSimple VPN ゲートウェイ キーと一致する事前共有キーを入力します。
* [Local Identification]\(ローカル ID\)。 オンプレミスの Palo Alto ファイアウォールのパブリック IP アドレスを入力します。
* [Peer Identification]\(ピア ID\)。 プライマリ CloudSimple VPN ピアの IP アドレスを入力します。

[Advanced Options]\(詳細オプション\) タブ:

* [Enable Passive Mode]\(パッシブ モードを有効にする\)。 オフのままにします。
* [Enable NAT Traversal]\(NAT トラバーサルを有効にする\)。 オンプレミスの Palo Alto ファイアウォールが NAT デバイスの背後にない場合は、オフのままにします。 それ以外の場合は、チェックボックスをオンにします。

[IKEv1]:

* [Exchange Mode]\(Exchange モード\)。 **[main]\(メイン\)** を選択します。
* [IKE Crypto Profile]\(IKE 暗号化プロファイル\)。 前の手順で作成した IKE 暗号化プロファイルを選択します。 [Enable Fragmentation]\(断片化を有効にする\) チェックボックスはオフのままにします。
* [Dead Peer Detection]\(停止しているピアの検出\)。 チェックボックスをオフのままにします。

前の手順を繰り返して、セカンダリ IKE ゲートウェイを作成します。

### <a name="5-define-ipsec-crypto-profiles"></a>5.IPSEC 暗号化プロファイルを定義する

**[Network]\(ネットワーク\)**  >  **[Expand Network Profiles]\(ネットワーク プロファイルの展開\)**  >  **[IPSEC Crypto]\(IPSEC 暗号化\)**  >  **[Add]\(追加\)** を選択し、次のフィールドを構成し、 **[OK]** をクリックします。

* [Name]\(名前\)。 IPsec 暗号化プロファイルの名前を入力します。
* [IPsec Protocol]\(IPsec プロトコル\)。 **[ESP]** を選択します。
* 暗号化。 **[Add]\(追加\)** をクリックし、適切な暗号化方法を選択します。
* 認証 **[Add]\(追加\)** をクリックし、適切な認証方法を選択します。
* [DH Group]\(DH グループ\)。 **[no-pfs]** を選択します。
* [Lifetime]\(有効期間\)。 30 分に設定します。
* [Enable]\(有効にする\)。 チェックボックスをオフのままにします。

前の手順を繰り返して、別の IPsec 暗号化プロファイルを作成します。これは、セカンダリ CloudSimple VPN ピアとして使用されます。 同じ IPSEC 暗号化プロファイルをプライマリとセカンダリの両方の IPsec トンネルに使用することもできます (次の手順を参照してください)。

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6.トンネル監視の監視プロファイルを定義する

**[Network]\(ネットワーク\)**  >  **[Expand Network Profiles]\(ネットワーク プロファイルの展開\)**  >  **[Monitor]\(監視\)**  >  **[Add]\(追加\)** を選択し、次のフィールドを構成し、 **[OK]** をクリックします。

* [Name]\(名前\)。 障害に対して事前対応のためのトンネル監視に使用するモニター プロファイルの名前を入力します。
* [Action]\(アクション\)。 **[Fail Over]\(フェール オーバー\)** を選択します。
* [Interval]\(間隔\)。 値「**3**」を入力します。
* [Threshold]\(しきい値\)。 値「**7**」を入力します。

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7.プライマリとセカンダリの IPsec トンネルを設定します。

**[Network]\(ネットワーク\)**  >  **[IPsec Tunnels]\(IPsec トンネル\)**  >  **[Add]\(追加\)** を選択し、次のフィールドを構成し、 **[OK]** をクリックします。

[General]\(全般\) タブ:

* [Name]\(名前\)。 プライマリ CloudSimple VPN ピアとピアリングされるプライマリ IPSEC トンネルの名前を入力します。
* [Tunnel Interface]\(トンネル インターフェイス\)。 プライマリ トンネル インターフェイスを選択します。
* [Type]\(種類\)。 既定値のままにします。
* [Address Type]\(アドレスの種類\)。 **[IPv4]** を選択します。
* [IKE Gateway]\(IKE ゲートウェイ\)。 プライマリ IKE ゲートウェイを選択します。
* [IPsec Crypto Profile]\(IPsec Crypto プロファイル\)。 プライマリ IPsec プロファイルを選択します。 **[Show Advanced options]\(詳細オプションの表示\)** を選択します。
* [Enable Replay Protection]\(再生保護を有効にする\)。 既定値のままにします。
* [Copy TOS Header]\(TOS ヘッダーをコピーする\)。 チェックボックスをオフのままにします。
* [Tunnel Monitor]\(トンネル モニター\)。 チェックボックスをオンにします。
* [Destination IP]\(宛先 IP\)。 サイト間接続で許可されている CloudSimple Private Cloud サブネットに属する任意の IP アドレスを入力します。 Palo Alto 上のトンネル インターフェイス (tunnel.20 - 10.64.5.2/32 や tunnel.30 - 10.64.6.2/32 など) が、サイト間 VPN を介して CloudSimple Private Cloud の IP アドレスに到達できることを確認しておきます。 プロキシ ID については、次の構成を参照してください。
* [プロファイル]。 モニター プロファイルを選択します。

[Proxy IDs]\(プロキシ ID\) タブ: **[IPv4]**  >  **[Add]\(追加\)** の順にクリックし、次のように構成します。

* [Proxy ID]\(プロキシ ID\)。 対象のトラフィックの名前を入力します。 1 つの IPsec トンネル内に複数のプロキシ ID が存在する可能性があります。
* Local。 サイト間 VPN 経由で Private Cloud のサブネットと通信することが許可されているオンプレミスのローカル サブネットを指定します。
* [Remote]\(リモート\)。 ローカル サブネットとの通信が許可されている Private Cloud のリモート サブネットを指定します。
* [Protocol]\(プロトコル\)。 **[any]\(すべて\)** を選択します。

前の手順を繰り返して、セカンダリ CloudSimple VPN ピアに使用する別の IPsec トンネルを作成します。

## <a name="references"></a>参照

Cisco ASA 上の NAT の構成:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Cisco ASA 5500 シリーズ構成ガイド</a>

Cisco ASA でサポートされている IKEv1 および IKEv2 属性:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Cisco ASA シリーズ CLI 構成ガイド</a>

バージョン 8.4 以降の Cisco ASA で IPsec サイト間 VPN の構成:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">ASA 上で ASDM または CLI を使用して IKEv1 IPsec サイト間トンネルを構成する</a>

Azure 上の Cisco Adaptive Security Appliance virtual (ASAv) の構成:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Cisco Adaptive Security Virtual Appliance (ASAv) クイックスタート ガイド</a>

Palo Alto でのプロキシ ID を使用したサイト間 VPN の構成:

[サイト間 VPN を設定する](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

トンネル モニターの設定:

[トンネル監視を設定する](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

IKE ゲートウェイまたは IPsec トンネルの操作:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">IKE ゲートウェイまたは IPsec トンネルの有効化/無効化、更新、または再起動</a>
