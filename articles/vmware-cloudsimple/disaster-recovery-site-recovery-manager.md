---
title: Azure VMware Solution by CloudSimple - VMware Site Recovery Manager を使用して、プライベート クラウドをディザスター リカバリー サイトとして設定する
description: VMware Site Recovery Manager を使用して、CloudSimple プライベート クラウドをディザスター リカバリー サイトとして設定する方法について説明します。
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d71f8aec1f35514ac6c10b17b6f7b69b79b05bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97897911"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>VMware Site Recovery Manager を使用して、プライベート クラウドをディザスター リカバリー ターゲットとして設定する

CloudSimple プライベート クラウドをオンプレミスの VMware ワークロード用のディザスター リカバリー (DR) サイトとして使用できます。

DR ソリューションは、vSphere Replication と VMware Site Recovery Manager (SRM) をベースにしています。 同様のアプローチに従って、プライベート クラウドをオンプレミスの復旧サイトによって保護されているプライマリ サイトとして有効にすることができます。

CloudSimple ソリューション:

* DR 専用のデータセンターを設定する必要がなくなります。
* CloudSimple がデプロイされている Azure の場所を活用して、世界規模での地理的回復性を実現できます。
* DR を確立するためのデプロイ コストと総保有コストを削減するオプションが提供されます。

CloudSimple ソリューションでは、次の操作を行う必要があります。

* プライベート クラウド内で vSphere Replication と SRM をインストール、構成、管理します。
* プライベート クラウドが保護されたサイトの場合は、独自のライセンスを SRM に提供します。 CloudSimple サイトが復旧サイトとして使用されている場合、追加の SRM ライセンスは必要ありません。

このソリューションでは、vSphere Replication と SRM を完全に制御できます。 使い慣れた UI、API、CLI のインターフェイスにより、既存のスクリプトとツールを使用できるようになります。

![Site Recovery Manager のデプロイ](media/srm-deployment.png)

プライベート クラウドおよびオンプレミス環境と互換性のある任意のバージョンの vRA と SRM を使用できます。 このガイドの例では、vRA 6.5 と SRM 6.5 を使用します。 これらのバージョンは、CloudSimple でサポートされている vSphere 6.5 と互換性があります。

## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

以下のセクションでは、プライベート クラウド内で SRM を使用して DR ソリューションをデプロイする方法について説明します。

1. [VMware 製品のバージョンに互換性があることを確認する](#verify-that-vmware-product-versions-are-compatible)
2. [DR 環境のサイズを見積もる](#estimate-the-size-of-your-dr-environment)
3. [自分の環境用のプライベート クラウドを作成する](#create-a-private-cloud-for-your-environment)
4. [SRM ソリューション用のプライベート クラウド ネットワークを設定する](#set-up-private-cloud-networking-for-the-srm-solution)
5. [オンプレミス ネットワークとプライベート クラウドの間にサイト間 VPN 接続を設定し、必要なポートを開く](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [プライベート クラウド内にインフラストラクチャ サービスを設定する](#set-up-infrastructure-services-in-your-private-cloud)
7. [オンプレミス環境に vSphere Replication アプライアンスをインストールする](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [プライベート クラウド環境に vSphere Replication アプライアンスをインストールする](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [オンプレミス環境に SRM サーバーをインストールする](#install-srm-server-in-your-on-premises-environment)
10. [プライベート クラウドに SRM サーバーをインストールする](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>VMware 製品のバージョンに互換性があることを確認する

このガイド内の構成には、次の互換性要件が適用されます。

* 同じバージョンの SRM がプライベート クラウドとオンプレミス環境にデプロイされている必要があります。
* 同じバージョンの vSphere Replication がプライベート クラウドとオンプレミス環境にデプロイされている必要があります。
* プライベート クラウドとオンプレミス環境内の Platform Services Controller (PSC) のバージョンに互換性が必要です。
* プライベート クラウドとオンプレミス環境内の vCenter のバージョンに互換性が必要です。
* SRM と vSphere Replication のバージョンが相互に互換で、PSC および vCenter とも互換性がある必要があります。

関連する VMware ドキュメントと互換性情報へのリンクについては、「[VMware Site Recovery Manager のドキュメント](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)」をご覧ください。

プライベート クラウド内の vCenter と PSC のバージョンを確認するには、CloudSimple ポータルを開きます。 **[リソース]** に移動し、プライベート クラウドを選択し、 **[vSphere 管理ネットワーク]** タブをクリックします。

![プライベート クラウド内の vCenter と PSC のバージョン](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>DR 環境のサイズを見積もる

1. 特定されたオンプレミス構成がサポートされている制限内であることを確認します。 SRM 6.5 の場合、制限は [Site Recovery Manager 6.5 の運用上の制限](https://kb.vmware.com/s/article/2147110)に関する VMware サポート技術情報の記事に記載されています。
2. ワークロード サイズと RPO の要件を満たすのに十分なネットワーク帯域幅があることを確認します。 [vSphere Replication の帯域幅要件の計算](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)に関する VMware サポート技術情報の記事には、帯域幅の制限に関するガイダンスが記載されています。
3. CloudSimple サイズ設定ツールを使用して、オンプレミス環境を保護するために DR サイト内で必要なリソースを見積もります。

### <a name="create-a-private-cloud-for-your-environment"></a>自分の環境用のプライベート クラウドを作成する

[プライベート クラウドの作成](create-private-cloud.md)に関する記事の手順とサイズ設定に関する推奨事項に従って、CloudSimple ポータルからプライベート クラウドを作成します。

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>SRM ソリューション用のプライベート クラウド ネットワークを設定する

CloudSimple ポータルにアクセスして、SRM ソリューション用のプライベート クラウド ネットワークを設定します。

SRM ソリューション ネットワーク用の VLAN を作成し、それをサブネット CIDR に割り当てます。 手順については、[VLAN/サブネットの作成と管理](create-vlan-subnet.md)に関する記事をご覧ください。

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>オンプレミス ネットワークとプライベート クラウドの間にサイト間 VPN 接続を設定し、必要なポートを開く

オンプレミスのネットワークとプライベート クラウドの間にサイト間接続を設定します。 手順については、「[CloudSimple プライベート クラウドへの VPN 接続の構成](set-up-vpn.md)」をご覧ください。

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>プライベート クラウド内にインフラストラクチャ サービスを設定する

ワークロードとツールを簡単に管理できるように、プライベート クラウド内でインフラストラクチャ サービスを構成します。

次のいずれかを実行する場合は、「[CloudSimple プライベート クラウド上の vCenter の ID プロバイダーとして Azure AD を使用する](azure-ad.md)」の説明に従って、外部 ID プロバイダーを追加できます。

* プライベート クラウド内のオンプレミスの Active Directory (AD) からユーザーを識別します。
* すべてのユーザーに対してプライベート クラウドに AD を設定します。
* Azure AD を使用します。

プライベート クラウド内のワークロードに対して IP アドレス参照、IP アドレス管理、名前解決サービスを提供するには、「[CloudSimple プライベート クラウドでの DNS および DHCP アプリケーションとワークロードの設定](dns-dhcp-setup.md)」の説明に従って DHCP および DNS サーバーを設定します。

プライベート クラウド内の管理 VM では *.cloudsimple.io ドメインが使用されます。 このドメインに対する要求を解決するには、「[条件付きフォワーダーの作成](on-premises-dns-setup.md#create-a-conditional-forwarder)」の説明に従って、DNS サーバー上で DNS 転送を構成します。

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>オンプレミス環境に vSphere Replication アプライアンスをインストールする

VMware のドキュメントに従って、オンプレミス環境に vSphere Replication アプライアンス (vRA) をインストールします。 インストールは、次の大まかな手順で構成されています。

1. vRA インストール用のオンプレミス環境を準備します。

2. vmware.com の VR ISO の OVF を使用して、オンプレミス環境に vRA をデプロイします。 vRA 6.5 では、[この VMware ブログ](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices)に関連情報が記載されています。

3. オンプレミスの vRA を、オンプレミス サイトの vCenter シングル サインオンに登録します。 vSphere Replication 6.5 での詳細な手順については、[VMware vSphere Replication 6.5 のインストールと構成](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)に関する VMware のドキュメントをご覧ください。

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>プライベート クラウド環境に vSphere Replication アプライアンスをインストールする

開始する前に、以下があることを確認してください。

* オンプレミス環境内のサブネットからプライベート クラウドの管理サブネットへの IP の到達可能性
* オンプレミスの vSphere 環境内のレプリケーション サブネットからプライベート クラウドの SRM ソリューション サブネットへの IP の到達可能性

手順については、「[CloudSimple プライベート クラウドへの VPN 接続の構成](set-up-vpn.md)」をご覧ください。 手順は、オンプレミス インストールの手順と似ています。

CloudSimple では、vRA および SRM のインストール中に IP アドレスの代わりに FQDN を使用することをお勧めします。 プライベート クラウド内の vCenter と PSC の FQDN を確認するには、CloudSimple ポータルを開きます。 **[リソース]** に移動し、プライベート クラウドを選択し、 **[vSphere 管理ネットワーク]** タブをクリックします。

![プライベート クラウド内での vCenter/PSC の FQDN の検索](media/srm-resources.png)

CloudSimple では、既定の "cloudowner" ユーザーを使用して vRA と SRM をインストールせずに、新しいユーザーを作成する必要があります。 これは、プライベート クラウド vCenter 環境に高いアップタイムと可用性を確保するために行われます。 しかし、プライベート クラウド vCenter 内の既定の cloudowner ユーザーには、管理者特権を持つ新しいユーザーを作成するための十分な特権がありません。

vRA と SRM をインストールする前に、cloudowner ユーザーの vCenter 特権をエスカレートしてから、vCenter SSO ドメイン内に管理者特権を持つユーザーを作成する必要があります。 既定のプライベート クラウドのユーザーとアクセス許可モデルについて詳しくは、[プライベート クラウドのアクセス許可モデル](learn-private-cloud-permissions.md)に関する記事をご覧ください。

インストールは、次の大まかな手順で構成されています。

1. [権限をエスカレートします](escalate-private-cloud-privileges.md)。
2. vSphere Replication と SRM のインストール用のユーザーをプライベート クラウドに作成します。 下の「[vCenter UI:vRA と SRM のインストール用のユーザーをプライベート クラウドに作成する](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation)」で説明されています。
3. vRA インストール用のプライベート クラウド環境を準備します。
4. vmware.com の VR ISO の OVF を使用して、プライベート クラウドに vRA をデプロイします。 vRA 6.5 では、[この VMware ブログ](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices)に関連情報が記載されています。
5. vRA 用のファイアウォール規則を構成します。 下の「[CloudSimple ポータル:vRA 用のファイアウォール規則を構成する](#cloudsimple-portal-configure-firewall-rules-for-vra)」で説明されています。
6. プライベート クラウド サイトで、vCenter シングル サインオンを使用してプライベート クラウド vRA を登録します。
7. 2 つのアプライアンス間の vSphere Replication 接続を構成します。 必要なポートがファイアウォール経由で開かれていることを確認します。 vSphere Replication 6.5 に対して開く必要があるポート番号の一覧については、[この VMware サポート技術情報の記事](https://kb.vmware.com/s/article/2087769)をご覧ください。

vSphere Replication 6.5 の詳細なインストール手順については、[VMware vSphere Replication 6.5 のインストールと構成](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)に関する VMware のドキュメントをご覧ください。

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter UI:vRA と SRM のインストール用のユーザーをプライベート クラウドに作成する

CloudSimple ポータルから特権をエスカレートした後、cloudowner ユーザーの資格情報を使用して vCenter にサインインします。

VCenter 内で新しいユーザー `srm-soln-admin` を作成し、そのユーザーを vCenter 内の Administrators グループに追加します。
cloudowner ユーザーとして vCenter からサインアウトし、*srm-soln-admin* ユーザーとしてサインインします。

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple ポータル:vRA 用のファイアウォール規則を構成する

[ファイアウォールのテーブルと規則の設定](firewall.md)に関する記事の説明に従ってファイアウォール規則を構成してポートを開き、以下の間の通信を有効にします。

* SRM ソリューション ネットワーク内の vRA と管理ネットワーク内の vCenter および ESXi ホスト。
* 2 つのサイトにある vRA アプライアンス。

vSphere Replication 6.5 に対して開く必要があるポート番号の一覧については、[この VMware サポート技術情報の記事](https://kb.vmware.com/s/article/2087769)をご覧ください。

### <a name="install-srm-server-in-your-on-premises-environment"></a>オンプレミス環境に SRM サーバーをインストールする

開始する前に、以下を確認します。

* vSphere Replication アプライアンスが、オンプレミス環境とプライベート クラウド環境にインストールされている。
* 両方のサイトの vSphere Replication アプライアンスが相互に接続されている。
* 前提条件とベスト プラクティスについて、VMware の情報を確認した。 SRM 6.5 については、[SRM 6.5 の前提条件とベスト プラクティス](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)に関する VMware ドキュメントをご覧ください。

この [VMware ドキュメント](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)で説明されているように、VMware のドキュメントに従って、デプロイ モデル "プラットフォーム サービス コントローラーごとに 1 つの vCenter インスタンスをがある 2 サイト トポロジ" で SRM サーバーのインストールを実行します。 SRM 6.5 のインストール手順については、VMware ドキュメント「[Site Recovery Manager のインストール](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)」をご覧ください。

### <a name="install-srm-server-in-your-private-cloud"></a>プライベート クラウドに SRM サーバーをインストールする

開始する前に、以下を確認します。

* vSphere Replication アプライアンスが、オンプレミス環境とプライベート クラウド環境にインストールされている。
* 両方のサイトの vSphere Replication アプライアンスが相互に接続されている。
* 前提条件とベスト プラクティスについて、VMware の情報を確認した。 SRM 6.5 については、[Site Recovery Manager 6.5 サーバーのインストールに関する前提条件とベスト プラクティス](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)に関するページをご覧ください。

次の手順では、プライベート クラウドの SRM のインストールについて説明します。

1. [vCenter UI:SRM をインストールする](#vcenter-ui-install-srm)
2. [CloudSimple ポータル:SRM 用のファイアウォール規則を構成する](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter UI:SCP を構成する](#vcenter-ui-configure-srm)
4. [CloudSimple ポータル: 特権のエスカレートを解除する](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter UI:SRM をインストールする

srm-soln-admin 資格情報を使用して vCenter にログインした後、この [VMware ドキュメント](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)で説明されているように、VMware のドキュメントに従って、デプロイ モデル "プラットフォーム サービス コントローラーごとに 1 つの vCenter インスタンスをがある 2 サイト トポロジ" で SRM サーバーのインストールを実行します。 SRM 6.5 のインストール手順については、VMware ドキュメント「[Site Recovery Manager のインストール](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)」をご覧ください。

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple ポータル:SRM 用のファイアウォール規則を構成する

[ファイアウォールのテーブルと規則の設定](firewall.md)に関する記事の説明に従ってファイアウォール規則を構成して、以下の間の通信を可能にします。

プライベート クラウド内の SRM サーバーと vCenter/PSC。
両方のサイトの SRM サーバー

vSphere Replication 6.5 に対して開く必要があるポート番号の一覧については、[この VMware サポート技術情報の記事](https://kb.vmware.com/s/article/2087769)をご覧ください。

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI:SCP を構成する

プライベート クラウドに SRM をインストールした後、VMware Site Recovery Manager のインストールおよび構成ガイドのセクションの説明に従って、以下のタスクを実行します。 SRM 6.5 での手順については、VMware ドキュメント「[Site Recovery Manager のインストール](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)」をご覧ください。

1. 保護されたサイトと復旧サイト上で Site Recovery Manager Server インスタンスを接続します。
2. リモートの Site Recovery Manager Server インスタンス へのクライアント接続を確立します。
3. Site Recovery Manager ライセンス キーをインストールします。

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple ポータル:特権のエスカレートを解除する

特権のエスカレートを解除するには、「[特権のエスカレートを解除する](escalate-private-cloud-privileges.md#de-escalate-privileges)」をご覧ください。

## <a name="ongoing-management-of-your-srm-solution"></a>SRM ソリューションの継続的な管理

プライベート クラウド環境では、vSphere Replication および SRM ソフトウェアを完全に制御でき、必要なソフトウェア ライフサイクル管理を実行することが期待されています。 vSphere Replication または SRM を更新またはアップグレードする前に、新しいバージョンのソフトウェアがプライベート クラウドの vCenter および PSC と互換であることを確認します。

> [!NOTE]
> CloudSimple では、現在、マネージド DR サービスを提供するためのオプションを調査しています。 

## <a name="multiple-replication-configuration"></a>複数レプリケーションの構成

 [配列ベースのレプリケーションと vSphere レプリケーション テクノロジの両方を同時に SRM と共に使用](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)できます。 ただし、これらは個別の VM セットに適用する必要があります (特定の VM を配列ベースのレプリケーションまたは vSphere レプリケーションによって保護できますが、両方で保護することはできません)。 さらに、CloudSimple サイトは、複数の保護されたサイトの復旧サイトとして構成できます。 マルチサイト構成については、「[SRM マルチサイト オプション](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/)」をご覧ください。

## <a name="references"></a>References

* [VMware Site Recovery Manager のドキュメント](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Site Recovery Manager 6.5 の運用上の制限](https://kb.vmware.com/s/article/2147110)
* [vSphere Replication の帯域幅要件の計算](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [vSphere Replication 6.5 をデプロイする場合の OVF の選択](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere Replication 6.5 のインストールと構成](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [SRM 6.5 の前提条件とベスト プラクティス](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [プラットフォーム サービス コントローラーごとに 1 つの vCenter Server インスタンスがある 2 サイト トポロジでの Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware Site Recovery Manager 6.5 インストールおよび構成ガイド](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [配列ベースのレプリケーションと vSphere レプリケーションでの SRM に関する VMware ブログ](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [SRM マルチサイト オプションに関する VMware ブログ](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [vSphere Replication 5.8.x、6.x、8 用に開く必要があるポート番号](https://kb.vmware.com/s/article/2147112)
