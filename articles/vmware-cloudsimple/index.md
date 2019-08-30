---
title: Azure VMware Solution by CloudSimple
description: Azure VMware Solution by CloudSimple のドキュメント ポータル。
author: sharaths-cs
ms.author: b-mashar
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 936a9eb4d9b33b45800fab213fc72a306a04146e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972733"
---
# <a name="azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple

Azure VMware Solution by CloudSimple を利用する際に役立つワンストップ ポータルへようこそ。
このドキュメント サイトでは、次のトピックについて学習できます。

## <a name="overview"></a>概要

Azure VMware Solution by CloudSimple の詳細について学習します

* [Azure VMware Solution by CloudSimple の概要](cloudsimple-vmware-solutions-overview.md)に関するページで、機能、利点、および利用シナリオについて学習する
* [管理の主要な概念](key-concepts.md)を確認する

## <a name="quickstart"></a>クイック スタート

このソリューションの使用を開始する方法を学習します

* [サービスを初期化し、容量を購入する](quickstart-create-cloudsimple-service.md)方法を理解する
* 「[プライベート クラウドの環境を構成する](quickstart-create-private-cloud.md)」で、新しい VMware 環境を作成する方法を学習する
* 記事「[Azure での VMware VM の使用](quickstart-create-vmware-virtual-machine.md)」を確認して、VMware と Azure の管理を統合する方法を学習する

## <a name="concepts"></a>概念

次の概念について学習します

* [CloudSimple サービス](cloudsimple-service.md) ("Azure VMware Solution by CloudSimple - サービス" とも呼ばれます)。 このリソースはリージョンごとに作成する必要があります。
* 1 つ以上の [CloudSimple ノード](cloudsimple-node.md) リソースを作成して、お使いの環境用の容量を購入する。 これらのリソースは、"Azure VMware Solution by CloudSimple - ノード" とも呼ばれます。
* [プライベート クラウド](cloudsimple-private-cloud.md)を使用して、実際の VMware 環境を初期化し、構成する。
* [CloudSimple 仮想マシン](cloudsimple-virtual-machines.md) ("Azure VMware Solution by CloudSimple - 仮想マシン" とも呼ばれます) を使用して、管理を統合する。
* [VLAN やサブネット](cloudsimple-vlans-subnets.md)を使用して、アンダーレイ ネットワークを設計する。
* [ファイアウォール テーブル](cloudsimple-firewall-tables.md) リソースを使用して、実際のアンダーレイ ネットワークをセグメント化し、セキュリティで保護する。
* [VPN ゲートウェイ](cloudsimple-vpn-gateways.md)を使用して、実際の VMware 環境への WAN 経由による安全なアクセスを実現する。
* [パブリック IP](cloudsimple-public-ip-address.md) を使用して、ワークロードのパブリック アクセスを有効にする。
* [Azure ネットワーク接続](cloudsimple-azure-network-connection.md)を使用して、Azure 仮想ネットワークとオンプレミス ネットワークへの接続を確立する。
* [アカウント管理](cloudsimple-account.md)を使用して、アラート メールのターゲットを構成する。
* [アクティビティ管理](cloudsimple-activity.md)画面を使用して、ユーザーとシステムのアクティビティ ログを表示する。
* さまざまな [VMware コンポーネント](vmware-components.md)について理解する。

## <a name="tutorials"></a>チュートリアル

次のような一般的なタスクを実行する方法について学習します

* VMware 環境をデプロイするリージョンごとに 1 回 [CloudSimple サービスを作成](create-cloudsimple-service.md)する。
* [CloudSimple ポータル](access-cloudsimple-portal.md)内でコア サービス機能を管理する。
* [CloudSimple ノードの購入](create-nodes.md)によって、容量を有効にし、お使いのインフラストラクチャに対する請求を最適化する。
* プライベート クラウドを使用して、VMware 環境の構成を管理する。 プライベート クラウドの[作成](create-private-cloud.md)、[管理](manage-private-cloud.md)、[拡張](expand-private-cloud.md)、または[縮小](shrink-private-cloud.md)を行うことができます。
* [Azure サブスクリプションをマッピング](azure-subscription-mapping.md)して、統合管理を有効にする。
* [[アクティビティ] ページ](monitor-activity.md)を使用して、ユーザーとシステムのアクティビティを監視する。
* [サブネットを作成して管理](create-vlan-subnet.md)することで、実際の環境のネットワークを構成する。
* [ファイアウォール テーブルとルール](firewall.md)を使用して、実際の環境をセグメント化し、セキュリティで保護する。
* [パブリック IP を割り当て](public-ips.md)て、ワークロードのインバウンド インターネット アクセスを有効にする。
* [VPN を設定](vpn-gateway.md)して、実際の内部ネットワークまたはクライアント ワークステーションからの接続を有効にする。
* 実際の[オンプレミス環境](on-premises-connection.md)から、および [Azure 仮想ネットワーク](virtual-network-connection.md)への通信を有効にする。
* アラートのターゲットを構成し、[アカウントの概要](account.md)で購入済み容量の合計を表示する。
* CloudSimple ポータルにアクセスした[ユーザー](users.md)を表示する。
* Azure portal から VMware 仮想マシンを管理する。
    * Azure portal 内での[仮想マシンの作成](azure-create-vm.md)。
    * 作成した[仮想マシンの管理](azure-manage-vm.md)。

## <a name="how-to-guides"></a>ハウツー ガイド

これらのガイドでは、次のような目標を達成する方法について説明しています

* [実際の環境をセキュリティで保護](private-cloud-secure.md)する
* サードパーティのツールをインストールし、[特権エスカレーション](escalate-privileges.md)を使用して vSphere 内で追加のユーザーと外部認証ソースを有効にする。
* [オンプレミスの DNS を構成](on-premises-dns-setup.md)して、さまざまな VMware サービスへのアクセスを構成する。
* [ワークロードの DNS と DHCP を構成](dns-dhcp-setup.md)して、実際のワークロードに対する名前とアドレスの割り当てを有効にする。
* このサービスではサービスの[更新とアップグレード](vmware-components.md#updates-and-upgrades)を通じてお使いのプラットフォームのセキュリティと機能がどのように確保されるかを理解する。
* [Veeam などのサードパーティのバックアップ ソフトウェア](backup-workloads-veeam.md)を使用して、サンプルのバックアップ アーキテクチャを作成することにより、バックアップの TCO を節約する。
* [サードパーティの KMS 暗号化ソフトウェア](vsan-encryption.md)を使用して保存時の暗号化を有効にすることで、セキュリティで保護された環境を作成する。
* [Azure Active Directory (Azure AD) ID ソース](azure-ad.md)を構成して、Azure AD の管理を VMware に拡張する。
