---
title: Azure VMware Solution by CloudSimple - オンプレミス ワークロード用のディザスター サイトとしてプライベート クラウドを使用する
description: CloudSimple プライベート クラウドをオンプレミスの VMware ワークロード用のディザスター リカバリー サイトとして設定する方法について説明します
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5dd1f157984cdb300cc3b375d71654e5da54d1ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898132"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>CloudSimple プライベート クラウドをオンプレミスの VMware ワークロード用のディザスター リカバリー サイトとして設定する

CloudSimple プライベート クラウドは、災害時にもビジネス継続性を提供するための、オンプレミス アプリケーションの復旧サイトとして設定できます。 この復旧ソリューションは、レプリケーションおよびオーケストレーションのプラットフォームとしての Zerto Virtual Replication に基づいています。 重要なインフラストラクチャとアプリケーション仮想マシンを、オンプレミスの vCenter からプライベート クラウドに継続的にレプリケートできます。 プライベート クラウドは、フェールオーバー テストの実施と、災害時のアプリケーションの可用性の確保のために使用することができます。 同様のアプローチに従って、プライベート クラウドを別の場所にある復旧サイトによって保護されるプライマリ サイトとして設定することができます。

> [!NOTE]
> お使いのディザスター リカバリー環境のサイズ設定に関するガイドラインについては、Zerto のドキュメント「[Zerto Virtual Replication のサイズ設定に関する考慮事項](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf)」を参照してください。

CloudSimple ソリューション:

* ディザスター リカバリー (DR) 専用のデータセンターを設定する必要がなくなります。
* CloudSimple がデプロイされている Azure の場所を活用して、世界規模での地理的回復性を実現できます。
* DR のためのデプロイ コストと総保有コストを削減できるようになります。

このソリューションでは、次のことを行う必要があります。

* プライベート クラウド内で Zerto をインストール、構成、管理します。
* プライベート クラウドが保護対象サイトの場合は、手持ちの Zerto ライセンスを提供します。 CloudSimple サイトで実行されている Zerto とお使いのオンプレミス サイトをペアリングすることによって、ライセンスを付与できます。

次の図は、Zerto ソリューション用のアーキテクチャを示しています。

![Architecture](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>ソリューションをデプロイする方法

以下のセクションでは、プライベート クラウド内で Zerto Virtual Replication を使用して DR ソリューションをデプロイする方法について説明します。

1. [前提条件](#prerequisites)
2. [CloudSimple プライベート クラウド上でのオプションの構成](#optional-configuration-on-your-private-cloud)
3. [CloudSimple プライベート クラウド上での ZVM と VRA の設定](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Zerto Virtual Protection Group の設定](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>前提条件

オンプレミス環境からプライベート クラウドへの Zerto Virtual Replication を有効にするには、次の前提条件を満たす必要があります。

1. [お使いのオンプレミス ネットワークと CloudSimple プライベート クラウドとの間にサイト間 VPN 接続を設定します](set-up-vpn.md)。
2. [お使いのプライベート クラウドの管理コンポーネントがプライベート クラウドの DNS サーバーに転送されるように DNS 参照を設定します](on-premises-dns-setup.md)。  DNS 参照の転送を有効にするには、`*.cloudsimple.io` 用のオンプレミス DNS サーバーに CloudSimple DNS サーバーへの転送ゾーン エントリを作成します。
3. オンプレミスの vCenter コンポーネントがオンプレミス DNS サーバーに転送されるように、DNS 参照を設定します。  DNS サーバーは、CloudSimple プライベート クラウドからサイト間 VPN を経由して到達可能になっている必要があります。 サポートが必要な場合は、次の情報を記載した[サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を送信してください。  

    * オンプレミスの DNS ドメイン名
    * オンプレミス DNS サーバーの IP アドレス

4. プライベート クラウドに Windows サーバーをインストールします。 このサーバーは Zerto Virtual Manager のインストール先として使用します。
5. [CloudSimple 権限をエスカレートします](escalate-private-cloud-privileges.md)。
6. Zerto Virtual Manager のサービス アカウントとして使用するために、管理者ロールを付与した新しいユーザーをプライベート クラウドの vCenter に作成します。

### <a name="optional-configuration-on-your-private-cloud"></a>お使いのプライベート クラウド上でのオプションの構成

1. プライベート クラウドの vCenter に 1 つ以上のリソース プールを作成します。これは、VM 用のターゲット リソース プールとしてオンプレミス環境から使用するためのものです。
2. プライベート クラウドの vCenter に 1 つ以上のフォルダーを作成します。これは、VM 用のターゲット フォルダーとしてオンプレミス環境から使用するためのものです。
3. フェールオーバー ネットワーク用の VLAN を作成し、ファイアウォール規則を設定します。 サポートが必要な場合は、[サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開いてください。
4. フェールオーバー ネットワーク用の分散ポート グループを作成し、VM のフェールオーバーをテストするためのネットワークをテストします。
5. [DHCP サーバーと DNS サーバー](dns-dhcp-setup.md)をインストールするか、お使いのプライベート クラウド環境で Active Directory ドメイン コントローラーを使用します。

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>プライベート クラウド上での ZVM と VRA の設定

1. プライベート クラウド内の Windows サーバーに Zerto Virtual Manager (ZVM) をインストールします。
2. 前の手順で作成したサービス アカウントを使用して ZVM にサインインします。
3. Zerto Virtual Manager のライセンスを設定します。
4. Zerto Virtual Replication Appliance (VRA) を、お使いのプライベート クラウドの ESXi ホスト上にインストールします。
5. プライベート クラウドの ZVM をお使いのオンプレミスの ZVM とペアリングします。

### <a name="set-up-zerto-virtual-protection-group"></a>Zerto Virtual Protection Group の設定

1. 新しい Virtual Protection Group (VPG) を作成し、その VPG の優先順位を指定します。
2. ビジネス継続性のための保護を必要とする仮想マシンを選択し、必要に応じて起動順序をカスタマイズします。
3. お使いのプライベート クラウドを復旧サイトとして選択し、プライベート クラウド クラスターまたは作成したリソース グループを既定の復旧サーバーとして選択します。 お使いのプライベート クラウド上の復旧データストアとして **vsanDatastore** を選択します。

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > [VM Settings]\(VM 設定\) オプションでは、個々の VM のホスト オプションをカスタマイズできます。

4. 必要に応じてストレージ オプションをカスタマイズします。
5. 前の手順で作成した分散ポート グループを、フェールオーバー ネットワークとフェールオーバー テスト ネットワーク用に使用する復旧ネットワークとして指定します。また、必要に応じて復旧スクリプトをカスタマイズします。
6. 必要に応じて個々の VM のネットワーク設定をカスタマイズし、VPG を作成します。
7. レプリケーションが完了したら、フェールオーバーをテストします。

## <a name="reference"></a>リファレンス

[Zerto のドキュメント](https://www.zerto.com/myzerto/technical-documentation/)
