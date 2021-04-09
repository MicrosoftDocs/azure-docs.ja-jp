---
title: Azure Virtual Machines 上の SAP Business One | Microsoft Docs
description: Azure 上の SAP Business One。
author: msjuergent
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 07/15/2018
ms.author: juergent
ms.openlocfilehash: e17739c65c0b80beb1f6fdd09f31897b317d7858
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506890"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>Azure Virtual Machines 上の SAP Business One
このドキュメントでは、Azure Virtual Machines に SAP Business One をデプロイするためのガイダンスを提供します。 このドキュメントは、SAP の Business One のインストール ドキュメントに代わるものではありません。 このドキュメントでは、Business One アプリケーションを実行する Azure インフラストラクチャの計画とデプロイに関する基本的なガイドラインについて説明します。

Business One では、次の 2 つの異なるデータベースをサポートしています。
- SQL Server: [SAP Note #928839 - Release Planning for Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839) (Microsoft SQL Server のリリース計画) を参照
- SAP HANA: SAP HANA の正確な SAP Business One サポート マトリックスについては、[SAP の製品出荷マトリックス (PAM)](https://support.sap.com/pam) を確認

SQL Server については、「[SAP NetWeaver のための Azure Virtual Machines DBMS のデプロイ](./dbms_guide_general.md)」に記載されている、デプロイに関する基本的な考慮事項が適用されます。 SAP HANA の考慮事項は、このドキュメントで説明します。

## <a name="prerequisites"></a>前提条件
このガイドを使用するには、次の Azure コンポーネントに関する基本的な知識が必要です。

- [Windows 上の Azure 仮想マシン](../../windows/tutorial-manage-vm.md)
- [Linux 上の Azure 仮想マシン](../../linux/tutorial-manage-vm.md)
- [PowerShell を使用した Azure ネットワークと仮想ネットワークの管理](../../windows/tutorial-virtual-network.md)
- [CLI を使用した Azure ネットワークと仮想ネットワーク](../../linux/tutorial-virtual-network.md)
- [Azure CLI を使用した Azure ディスクの管理](../../linux/tutorial-manage-disks.md)

関心があるのが Business One だけであっても、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装](./planning-guide.md)」は有益な情報源となります。

SAP Business One のデプロイの例として、以下のことに精通していることを前提としています。

- VM のような特定のインフラストラクチャでの SAP HANA のインストール
- Azure VM のようなインフラストラクチャでの SAP Business One アプリケーションのインストール
- SAP Business One と選択した DBMS システムの操作
- Azure でのインフラストラクチャのデプロイ

これらはいずれもこのドキュメントでは説明しません。

Azure ドキュメントの他に、Business One に言及している、または SAP の Business One からのセントラル ノートであるメインの SAP ノートにも注目してください。

- [528296 - General Overview Note for SAP Business One Releases and Related Products](https://launchpad.support.sap.com/#/notes/528296) (SAP Business One リリースと関連製品に関する全般的な概要ノート)
- [2216195 - Release Updates Note for SAP Business One 9.2, version for SAP HANA](https://launchpad.support.sap.com/#/notes/2216195) (SAP Business One 9.2、SAP HANA のバージョンのリリース更新ノート)
- [2483583 - Central Note for SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483583) (SAP Business One 9.3 のセントラル ノート)
- [2483615 - Release Updates Note for SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483615) (SAP Business One 9.3 のリリース更新ノート)
- [2483595 - Collective Note for SAP Business One 9.3 General Issues](https://launchpad.support.sap.com/#/notes/2483595) (SAP Business One 9.3 の一般的な問題の集合的なノート)
- [2027458 - Collective Consulting Note for SAP HANA-Related Topics of SAP Business One, version for SAP HANA](https://launchpad.support.sap.com/#/notes/2027458) (SAP Business One、SAP HANA のバージョンの SAP HANA 関連トピックの集合的なコンサルティング ノート)


## <a name="business-one-architecture"></a>Business One アーキテクチャ
Business One は、次の 2 つの階層を持つアプリケーションです。

- 'ファット' クライアントによるクライアント層
- テナントのデータベース スキーマを含むデータベース層

どのコンポーネントがクライアント側で実行され、どの部分がサーバー側で実行されているかについて、わかりやすい概要が「[SAP Business One Administrator's Guide](https://help.sap.com/doc/601fbd9113be4240b81d74626439cfa9/10.0/en-US/AdministratorGuide_SQL.pdf)」 (SAP ビジネス 1 つ管理者のガイド) に記載されています。 

クライアント層と DBMS 層との重要なやりとりには、長い待機時間があるため、Azure でデプロイする際には、両方の層を Azure に配置する必要があります。 その後、通常はユーザーが Business One クライアント コンポーネント用の RDS を実行している 1 つまたは複数の VM に RDS を配置します。

### <a name="sizing-vms-for-sap-business-one"></a>SAP Business One の VM のサイズ設定

クライアント VM のサイズ設定については、SAP のドキュメント「[SAP Business One Hardware Requirements Guide](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf)」 (SAP Business One のハードウェア要件ガイド) にリソース要件が記載されています。 Azure については、このドキュメントのチャプター 2.4 に記載されている要件を使用して、計算する必要があります。

Business One クライアント コンポーネントと DBMS ホストをホストするための Azure 仮想マシンとしては、SAP NetWeaver がサポートされている VM のみが許可されます。 SAP NetWeaver がサポートされている Azure VM の一覧を検索するには、[SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533) をお読みください。

Business One の DBMS バックエンドとして SAP HANA を実行すると、[HANA で認定された IaaS プラットフォーム一覧](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One)内の Business on HANA にリストされる VM のみが HANA でサポートされます。 Business One のクライアント コンポーネントは、DBMS システムとしての SAP HANA に対する強力なこの制限の影響を受けません。

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>SAP Business One に使用するオペレーティング システム リリース

原則として、常に最新のオペレーティング システム リリースを使用してください。 特に Linux 空間では、SUSE および Red Hat のより新しいマイナー リリースで Azure の新機能が導入されています。 Windows 側では、Windows Server 2016 を使用することを強くお勧めします。


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Azure for SAP Business One にインフラストラクチャをデプロイする
次の数章で、SAP をデプロイするために重要なインフラストラクチャ要素について説明します。

### <a name="azure-network-infrastructure"></a>Azure ネットワーク インフラストラクチャ
Azure にデプロイする必要があるネットワーク インフラストラクチャは、1 つの Business One system システムを自分のためにデプロイするか、 または顧客のために多数の Business One システムをホストしているホスト側かによって決まります。 Azure に接続する方法によっても、デザインがわずかに変わる場合もあります。 さまざまな可能性を見てみると、あるデザインには、Azure への VPN 接続があったり、Active Directory を [VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) または [ExpressRoute](../../../expressroute/expressroute-introduction.md) を介して Azure に拡張するものもあります。

![Business One を使用した単純なネットワーク構成](./media/business-one-azure/simple-network-with-VPN.PNG)

示されている簡略化された構成では、ルーティングの制御および制限を可能にするいくつかのセキュリティの例が紹介されています。 以下のものがあります。 

- 顧客のオンプレミス側のルーター/ファイアウォール。
- 次の例は、お使いの SAP Business One 構成を実行している Azure VNet にルーティングとセキュリティ規則を導入するために使用できる [Azure Network Security Group](../../../virtual-network/network-security-groups-overview.md) です。
- Business One クライアントのユーザーに Business One サーバーを実行する (データベースを実行している) サーバーを見せないようにするには、Business One クライアントをホストしている VM と、Business One サーバーを、VNet 内の 2 つの異なるサブネットに分離する必要があります。
- Business One サーバーへのアクセスを制限するには、2 つの異なるサブネットに割り当てられている Azure NSG をもう一度使用します。

より高度な Azure のネットワーク構成は、Azure ドキュメントに記載されている[ハブとスポークのアーキテクチャのベスト プラクティス](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)に基づいています。 ハブとスポークのアーキテクチャ パターンを使用すると、最初の簡略化された構成はこのように変わります。


![Business One を使用したハブとスポークの構成](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

ユーザーがプライベート接続なしでインターネットを介して Azure に接続している場合、Azure のネットワークのデザインは、「[Azure とインターネットの間の DMZ](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)」の Azure 参照アーキテクチャに記載されている原則に合わせる必要があります。

### <a name="business-one-database-server"></a>Business One データベース サーバー
データベースの種類については、SQL Server と SAP HANA が使用できます。 DBMS とは関係なく、「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](./dbms_guide_general.md)」のドキュメントを読んで、Azure VM での DBMS デプロイと、関連するネットワークとストレージのトピックについて理解する必要があります。

特定のおよび全般的なデータベースのドキュメントで既に強調されていますが、以下に精通している必要があります。

- 「[Azure での Windows 仮想マシンの可用性の管理](../../availability.md)」と「[Linux 仮想マシンの可用性管理](../../availability.md)」
- 「[Virtual Machines の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)」

これらのドキュメントは、ストレージの種類と高可用性構成の選択範囲を決定する際に役立ちます。

原則として、次を行う必要があります。

- Standard HDD よりも Premium SSD を使用する。 使用できるディスクの種類の詳細については、[ディスクの種類の選択](../../disks-types.md)に関する記事を参照してください。
- アンマネージド ディスクよりも Azure Managed Disks を使用する
- ディスク構成に十分な IOPS と I/O スループットが構成されていることを確認する
- コスト効率に優れたストレージ構成を確保するため /hana/data と /hana/log volume を組み合わせる


#### <a name="sql-server-as-dbms"></a>DBMS としての SQL Server
Business One 用の DBMS として SQL Server をデプロイするには、「[SQL Server Azure Virtual Machines DBMS deployment for SAP NetWeaver](./dbms_guide_sqlserver.md)」 (SAP NetWeaver のための SQL Server Azure Virtual Machines DBMS のデプロイ) のドキュメントに従います。 

SQL Server の DBMS 側の大まかなサイズ見積もりは次のとおりです。

| ユーザーの数 | vCPU 数 | メモリ | VM の種類の例 |
| --- | --- | --- | --- |
| 最大 20 | 4 | 16 GB | D4s_v3、E4s_v3 |
| 最大 40 | 8 | 32 GB | D8s_v3、E8s_v3 |
| 最大 80 | 16 | 64 GB | D16s_v3、E16s_v3 |
| 最大 150 | 32 | 128 GB | D32s_v3、E32s_v3 |

上記のサイズは、手始めとして使えるアイデアを提供するためのものです。 リソースを増減する必要がある場合に、Azure での適応が容易になります。 VM の種類の変更は、その VM を再起動するだけで行えます。

#### <a name="sap-hana-as-dbms"></a>DBMS としての SAP HANA
DBMS として SAP HANA を使用する場合は、「[SAP HANA on Azure 運用ガイド](./hana-vm-operations.md)」ドキュメントの次のセクションの考慮事項に従う必要があります。

Azure で Business One のデータベースとして SAP HANA を中心にした高可用性とディザスター リカバリー構成については、「[Azure 仮想マシンの SAP HANA の高可用性](./sap-hana-availability-overview.md)」のドキュメントと、このドキュメントで指摘されているドキュメントをお読みください。

SAP HANA のバックアップと復元の戦略については、「[Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](./sap-hana-backup-guide.md)」のドキュメントと、このドキュメントで指摘されているドキュメントをお読みください。

 
### <a name="business-one-client-server"></a>Business One クライアント サーバー
これらのコンポーネントについては、ストレージに関する考慮事項は大きな問題ではありません。 しかし、信頼性の高いプラットフォームは必要です。 したがって、ベース VHD の場合でも、この VM に Azure Premium Storage を使用する必要があります。 「[SAP Business One Hardware Requirements Guide](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf)」 (SAP Business One のハードウェア要件ガイド) で指定されているデータを使用して、VM をサイズ設定します。 Azure については、このドキュメントのチャプター 2.4 に記載されている要件を使用して、計算する必要があります。 最適な VM を見つけるため、要件を計算するときに、それらを次のドキュメントと比較する必要があります。

- [Azure の Windows 仮想マシンのサイズ](../../sizes.md)
- [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533)

必要な CPU とメモリの数を Microsoft のドキュメントに記載されている数と比較します。 VM を選択する際には、ネットワーク スループットにも注意してください。
