---
title: Azure VMware Solution by CloudSimple - プライベート クラウド サイトを使用し、VMware Horizon を使用して仮想デスクトップ インフラストラクチャをホストする
description: CloudSimple プライベート クラウド サイトを使用し、VMware Horizon を使用して仮想デスクトップ インフラストラクチャをホストする方法について説明します
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 513af1ba2e354412278fb88af4ee6527c236fae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895599"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>CloudSimple プライベート クラウド サイトを使用し、VMware Horizon を使用して仮想デスクトップ インフラストラクチャをホストする

CloudSimple プライベート クラウド サイトを使用し、VMware Horizon 7.x を使用して仮想デスクトップ インフラストラクチャ (VDI) をホストできます。 次の図は、VDI 用の論理ソリューション アーキテクチャを示しています。

![Horizon のデプロイ](media/horizon-deployment.png)

このソリューションでは、Horizon View Manager と App Volume を完全に制御できます。 使い慣れた UI、API、CLI のインターフェイスにより、既存のスクリプトとツールを使用できるようになります。

CloudSimple ソリューションでは、次の操作を行う必要があります。

* プライベート クラウド内で VMware Horizon 7.x をインストール、構成、管理します。
* 独自の Horizon ライセンスを提供します。

## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

以下のセクションでは、プライベート クラウド内で Horizon を使用して VDI ソリューションをデプロイする方法について説明します。

1. [VMware 製品のバージョンに互換性があることを確認する](#verify-that-vmware-product-versions-are-compatible)
2. [デスクトップ環境のサイズを見積もる](#estimate-the-size-of-your-desktop-environment)
3. [自分の環境用のプライベート クラウドを作成する](#create-a-private-cloud-for-your-environment)
4. [プライベート クラウドに VMware Horizon をインストールする](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>VMware 製品のバージョンに互換性があることを確認する

* Horizon、App Volumes、Unified Access Gateway、User Environment Manager の現在のバージョンと予定されているバージョンが相互に互換で、プライベート クラウド内の vCenter および PSC とも互換性があることを確認します。 互換性情報については、[Horizon 7.5 に関する VMware の互換性マトリックス](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=)のページをご覧ください。
* プライベート クラウド内の vCenter および PSC の現在のバージョンを確認するには、[CloudSimple ポータル](access-cloudsimple-portal.md)内で **[リソース]** に移動し、ご自身のプライベート クラウドを選択して、**[vSphere 管理ネットワーク]** タブをクリックします。

![vCenter と PSC のバージョン](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>デスクトップ環境のサイズを見積もる

* 識別された構成が VMware の運用上の制限内にあることを確認します。
* お使いのすべてのデスクトップおよびお使いの Horizon 管理コンポーネントに必要なすべてのリソースを見積もります。

### <a name="create-a-private-cloud-for-your-environment"></a>自分の環境用のプライベート クラウドを作成する

1. 「[プライベート クラウドの環境を構成する](quickstart-create-private-cloud.md)」の手順に従って、CloudSimple ポータルからプライベート クラウドを作成します。  CloudSimple では、新しく作成されたすべてのプライベート クラウド内に "cloudowner" という名前の既定の vCenter ユーザーが作成されます。 既定のプライベート クラウドのユーザーとアクセス許可モデルについて詳しくは、[プライベート クラウドのアクセス許可モデル](learn-private-cloud-permissions.md)に関する記事をご覧ください。
2. Horizon 管理プレーン用のプライベート クラウド内に VLAN を作成し、サブネット CIDR をそれに割り当てます。 手順については、[VLAN/サブネットの作成と管理](create-vlan-subnet.md)に関する記事をご覧ください。 これは、すべてのソリューション コンポーネント (Unified Access Gateway、Connection Server、App Volume Server、User Environment Manager サーバー) がインストールされるネットワークです。
3. プライベート クラウド vCenter で外部 ID プロバイダーを使用するかどうかを決定します。 はいの場合、次のいずれかのオプションを選択します。
    * オンプレミスの Active Directory を外部 ID プロバイダーとして使用します。 手順については、[vCenter ID ソース](set-vcenter-identity.md)に関する記事をご覧ください。
    * Horizon 管理プレーン VLAN 内のプライベート クラウドに、外部 ID プロバイダーとして使用する Active Directory サーバーを設定します。 手順については、[vCenter ID ソース](set-vcenter-identity.md)に関する記事をご覧ください。
    * プライベート クラウド内の Horizon 管理プレーン VLAN に DHCP と DNS サーバーを設定します。 手順については、「[CloudSimple プライベート クラウドでの DNS および DHCP アプリケーションとワークロードの設定](dns-dhcp-setup.md)」をご覧ください。
4. プライベート クラウドにインストールされている DNS サーバー上で DNS 転送を構成します。 手順については、「[条件付きフォワーダーの作成](on-premises-dns-setup.md#create-a-conditional-forwarder)」をご覧ください。

### <a name="install-vmware-horizon-in-your-private-cloud"></a>プライベート クラウドに VMware Horizon をインストールする

次のデプロイ図は、プライベート クラウドにデプロイされた Horizon ソリューションを示しています。 Unified Access Gateway、AD/DC、View、App Volume Server は、ユーザーが作成した VLAN 234 にインストールされています。 Unified Access Gateway には、インターネットから到達可能なパブリック IP アドレスが割り当てられています。 Horizon デスクトップ プール VM は、分離とセキュリティを強化するために VLAN 235 にデプロイされています。

![プライベート クラウド内での Horizon のデプロイ](media/horizon-private-cloud.png)

以下のセクションでは、図に示されているものと同様のデプロイを設定する手順について説明します。 開始する前に、以下があることを確認してください。

* デスクトップ プールを実行するのに十分な容量を備えた、CloudSimple ポータルを使用して作成されたプライベート クラウド。
* デスクトップのネットワーク トラフィックをサポートするための、オンプレミス環境とプライベート クラウド環境の間の十分な帯域幅。
* オンプレミスのデータセンターとプライベート クラウドの間に設定されたサイト間 VPN トンネル。
* オンプレミス環境内のエンドユーザー サブネットから CloudSimple プライベート クラウド サブネットへの IP の到達可能性。
* プライベート クラウド用にインストールされた AD/DHCP/DNS。

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>CloudSimple ポータル: デスクトップ プール用の専用 VLAN/サブネットを作成する

Horizon デスクトップ プール用の VLAN を作成し、それをサブネット CIDR に割り当てます。 手順については、[VLAN/サブネットの作成と管理](create-vlan-subnet.md)に関する記事をご覧ください。 これは、すべてのデスクトップ仮想マシンが実行されるネットワークです。

標準的なセキュリティのベスト プラクティスに従って、Horizon のデプロイをセキュリティで保護します。

* デスクトップ VM に対してデスクトップ RDP トラフィック/SSH トラフィックのみを許可します。
* Horizon 管理プレーン VLAN とデスクトップ プール VLAN 間では管理トラフィックのみを許可します。
* オンプレミス ネットワークからの管理トラフィックのみを許可します。

CloudSimple ポータルから[ファイアウォール規則](firewall.md)を構成することにより、これらのベスト プラクティスを適用できます。

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>CloudSimple ポータル: セキュリティで保護された Horizon 管理プレーンに対するファイアウォール規則を構成する

CloudSimple ポータル内で、次の規則を設定します。 手順については、[ファイアウォールのテーブルと規則の設定](firewall.md)に関する記事をご覧ください。

1. CloudSimple N-S ファイアウォール内でファイアウォール規則を構成して、[Horizon ポートの一覧](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html)に関する VMware ドキュメントに記載されているネットワーク ポートのみが許可されるように、オンプレミスのサブネットと Horizon 管理 VLAN 間の通信を許可します。

2. プライベート クラウド内で、Horizon 管理 VLAN とデスクトップ プール VLAN の間に E-W ファイアウォール規則を作成します。

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>CloudSimple ポータル: Unified Access Gateway 用のパブリック IP アドレスを作成する

Unified Access Gateway アプライアンス用のパブリック IP アドレスを作成して、インターネットからのデスクトップ クライアント接続を有効にします。 手順については、[パブリック IP アドレスの割り当て](public-ips.md)に関する記事をご覧ください。

セットアップが完了すると、パブリック IP アドレスが割り当てられ、[パブリック IP] ページに一覧表示されます。

#### <a name="cloudsimple-portal-escalate-privileges"></a>CloudSimple ポータル: 特権をエスカレートする

既定の "cloudowner" ユーザーには、Horizon をインストールするための十分な特権がプライベート クラウド vCenter にないため、ユーザーの vCenter 特権をエスカレートする必要があります。 詳しくは、[特権のエスカレート](escalate-private-cloud-privileges.md)に関する記事をご覧ください。

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter UI: Horizon のインストール用のユーザーをプライベート クラウドに作成する

1. "cloudowner" ユーザー資格情報を使用して vCenter にサインインします。
2. VCenter 内で新しいユーザー "horizon-soln-admin" を作成し、そのユーザーを vCenter 内の Administrators グループに追加します。
3. "cloudowner" ユーザーとして vCenter からサインアウトし、"horizon-soln-admin" ユーザーとしてサインインします。

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter UI: VMware Horizon をインストールする

前の論理アーキテクチャのセクションで説明したように、Horizon ソリューションには以下のコンポーネントがあります。

* VMware Horizon View
* VMware Unified Access Gateway
* VMware App Volume Manager
* VMware User Environment Manager

次のようにコンポーネントをインストールします。

1. VMware ドキュメント「[VMware Unified Access Gateway のデプロイと構成](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html)」に記載されている手順に従って、Unified Access Gateway をインストールし、構成します。

2. 「[View インストール](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)」ガイドの手順に従って、プライベート クラウドに Horizon View をインストールします。

3. 「[VMware App Volumes のインストールと構成](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)」の手順に従って、App Volume Manager をインストールします。

4. 「[VMware User Environment Manager のインストールと構成について](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)」の手順に従って、User Environment Manager をインストールし、構成します。

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>VMware Horizon のあらかじめパッケージ化されたアプリ ボリュームをアップロードするためのサポート リクエストを提出する

インストール プロセスの一環として、App Volume Manager では事前にパッケージ化されたボリュームを使用して、アプリ スタックと書き込み可能ボリュームがプロビジョニングされます。 これらのボリュームは、アプリ スタックと書き込み可能ボリューム用のテンプレートとして機能します。

ボリュームをプライベート クラウドのデータストアにアップロードするには、ESXi ルート パスワードが必要です。 サポートが必要な場合は、[サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を送信してください。 CloudSimple サポート担当者がお客様のプライベート クラウド環境にテンプレートをアップロードできるように、AppVolumes インストーラー バンドルをアタッチしてください。

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple ポータル:特権のエスカレートを解除する

ここで、"cloudowner" ユーザーの[特権のエスカレートを解除](escalate-private-cloud-privileges.md#de-escalate-privileges)できます。

## <a name="ongoing-management-of-your-horizon-solution"></a>Horizon ソリューションの継続的な管理

プライベート クラウド環境では、Horizon および App Volume Manager ソフトウェアを完全に制御でき、必要なソフトウェア ライフサイクル管理を実行することが期待されています。 Horizon または App Volume を更新またはアップグレードする前に、新しいバージョンのソフトウェアがプライベート クラウドの vCenter および PSC と互換であることを確認します。
