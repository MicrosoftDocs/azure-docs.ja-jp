---
title: "SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ | Microsoft Docs"
description: "SAP HANA on Azure (L インスタンス) をデプロイする方法のアーキテクチャの概要。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/02/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 63e1820033e051b72601291c5206772192e68769
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ

## <a name="what-is-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス) とは

SAP HANA on Azure (L インスタンス) は、Azure ならではのソリューションです。 Azure Virtual Machines 上で SAP HANA をデプロイおよび実行するだけでなく、お客様専用のベア メタル サーバー上でも SAP HANA を実行およびデプロイすることができます。 SAP HANA on Azure (L インスタンス) ソリューションは、お客様に割り当てられた非共有のホスト/サーバーのベア メタル ハードウェア上に構築されます。 サーバー ハードウェアは、コンピューティング/サーバー、ネットワーク、ストレージのインフラストラクチャを含むより大きなスタンプに埋め込まれます。 この全体が HANA TDI 認定となります。 SAP HANA on Azure (L インスタンス) のサービス プランでは、72 CPU、768 GB メモリのユニットから、960 CPU、20 TB メモリのユニットまで、さまざまな種類のサーバー SKU やサイズが提供されます。

インフラストラクチャ スタンプ内のお客様の分離はテナントで行われ、具体的には以下のようになります。

- ネットワーク: お客様に割り当てられているテナントごとに、仮想ネットワークを通じてインフラストラクチャ スタック内のお客様を分離します。 テナントは 1 件のお客様に割り当てられます。 お客様は複数のテナントを持つことができます。 テナントのネットワークの分離では、インフラストラクチャ スタンプ レベルでのテナント間のネットワーク通信が禁止されます。 テナント同士が同じお客様に属している場合も同様です。
- ストレージ コンポーネント: ストレージ ボリュームが割り当てられているストレージ仮想マシンを通じて分離します。 ストレージ ボリュームは 1 つのストレージ仮想マシンにのみ割り当てることができます。 ストレージ仮想マシンは、SAP HANA TDI 認定のインフラストラクチャ スタック内の 1 つのテナントに排他的に割り当てられます。 そのため、ストレージ仮想マシンに割り当てられているストレージ ボリュームは、関連付けられている特定のテナントのみがアクセスでき、 別の場所にデプロイされているテナントには表示されません。
- サーバーまたはホスト: サーバー ユニットやホスト ユニットがお客様間やテナント間で共有されることはありません。 お客様にデプロイされたサーバーまたはホストは、1 つのテナントに割り当てられたアトミック ベア メタル コンピューティング ユニットです。 別のお客様とホストやサーバーを共有することになるハードウェア パーティション分割やソフト パーティション分割が使用されることは**ありません**。 特定のテナントのストレージ仮想マシンに割り当てられたストレージ ボリュームは、このようなサーバーにマウントされます。 テナントは、排他的に割り当てられている別の SKU のサーバー ユニットを 1 つから複数持つことができます。
- SAP HANA on Azure (L インスタンス) インフラストラクチャ スタンプ内では多数の異なるテナントがデプロイされており、テナントのコンセプトを通じて、ネットワーク、ストレージ、およびコンピューティング レベルでそれぞれ分離されています。 


これらのベア メタル サーバー ユニットでは、SAP HANA の実行のみがサポートされます。 SAP アプリケーション レイヤーまたはワークロードのミドルウェア レイヤーは、Microsoft Azure Virtual Machines で実行されます。 SAP HANA on Azure (L インスタンス) のユニットを実行しているインフラストラクチャ スタンプは Azure Network のバックボーンに接続しているので、SAP HANA on Azure (L インスタンス) のユニットと Azure Virtual Machines の間には低レイテンシの接続が提供されます。

このドキュメントは、SAP HANA on Azure (L インスタンス) のトピックについて説明している 5 つのドキュメントのうちの 1 つです。 このドキュメントでは、基本的なアーキテクチャ、責任、提供されるサービス、およびソリューションの機能についての概要を説明します。 ネットワークや接続をはじめとする大部分の領域については、他の 4 つのドキュメントで詳しく説明しています。 SAP NetWeaver インストールや Azure VM 内の SAP NetWeaver のデプロイについては、SAP HANA on Azure (L インスタンス) のドキュメントではなく、 同じドキュメント コンテナーにある別のドキュメントで取り上げています。 


このガイドの 5 つのパートで取り上げるトピックは次のとおりです。

- [SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA on Azure (L インスタンス) のインストールと構成の方法](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA on Azure (L インスタンス) のトラブルシューティングと監視の方法](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="definitions"></a>定義

このアーキテクチャ/テクニカル デプロイ ガイドでは、いくつかの一般的な定義が広く使用されています。 次の用語とその意味に注意してください。

- **IaaS:** サービスとしてのインフラストラクチャ
- **PaaS:** サービスとしてのプラットフォーム
- **SaaS:** サービスとしてのソフトウェア
- **SAP コンポーネント:** 個々の SAP アプリケーション (ECC、BW、Solution Manager、EP など)。 SAP コンポーネントは、従来の ABAP または Java テクノロジか、ビジネス オブジェクトなどの非 NetWeaver ベース アプリケーションに基づいて作成できます。
- **SAP 環境:** 開発、QAS、トレーニング、DR、実稼働などのビジネス機能を実行するために論理的にグループ化された、1 つまたは複数の SAP コンポーネント。
- **SAP ランドスケープ:** IT 環境内にある SAP 資産全体を指します。 SAP ランドス ケープには、運用環境と非運用環境のすべてが含まれます。
- **SAP システム:** DBMS 層とアプリケーション層を組み合わせたシステム (SAP ERP 開発システム、SAP BW テスト システム、SAP CRM 運用システムなど)。Azure デプロイメントでは、オンプレミスと Azure 間でこれら 2 つの層を分割することはできません。 つまり、SAP システムはオンプレミスか Azure のいずれかにデプロイされます。 ただし、SAP ランドス ケープの異なるシステムを Azure かオンプレミスのいずれかにデプロイすることはできます。 たとえば、SAP CRM の開発システムとテスト システムを Azure にデプロイし、SAP CRM 運用システムをオンプレミスにデプロイすることは可能です。 SAP HANA on Azure (L インスタンス) の場合、Azure VM で SAP システムの SAP アプリケーション層をホストし、HANA L インスタンス スタンプのユニットで関連する SAP HANA インスタンスをホストすることを目的としています。
- **L インスタンス スタンプ:** SAP HANA TDI 認定のハードウェア インフラストラクチャ スタック。Azure 内で SAP HANA インスタンスを実行するための専用のスタックです。
- **SAP HANA on Azure (L インスタンス):** 異なる Azure リージョンの L インスタンス スタンプにデプロイされる SAP HANA TDI 認定ハードウェアで HANA インスタンスを実行する、Azure におけるサービスの正式名称。 関連用語の **HANA L インスタンス**は、SAP HANA on Azure (L インスタンス) の省略形であり、このテクニカル デプロイ ガイドで広く使用されています。
- **クロスプレミス:** オンプレミスのデータ センターと Azure の間で、サイト間接続、マルチサイト接続、または ExpressRoute 接続を使用する Azure サブスクリプションに VM をデプロイするシナリオを指します。 この種のデプロイメントは、共通の Azure ドキュメントでもクロスプレミス シナリオとして説明されています。 この接続の目的は、オンプレミスのドメイン、オンプレミスの Active Directory/OpenLDAP、およびオンプレミスの DNS を、Azure 内に拡張することです。 オンプレミスのランドスケープが、Azure サブスクリプションの Azure 資産に拡張されます。 この拡張により。VM はオンプレミス ドメインの一部になることができます。 オンプレミス ドメインのドメイン ユーザーは、サーバーにアクセスし、それらの VM (DBMS サービスなど) 上でサービスを実行できます。 オンプレミスにデプロイした VM と Azure にデプロイした VM の間では、通信と名前解決が可能です。 これが、ほとんどの SAP 資産をデプロイする場合の一般的なシナリオです。 詳細については、「[VPN ゲートウェイの計画と設計](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」および「[Azure Portal を使用してサイト間接続を持つ VNet を作成する](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。
- **テナント**: HANA L インスタンス スタンプにデプロイされたお客様は、"テナント" に分離されます。 テナントは、ネットワーク、ストレージ、およびコンピューティング レイヤーで他のテナントから分離されます。 そのため、HANA L インスタンス スタンプ レベルでは、別のテナントに割り当てられているストレージやコンピューティング ユニットが互いを参照して通信することはできません。 同じお客様が別々のテナントにデプロイを置くこともありますが、 その場合でも HANA L インスタンス スタンプ レベルでテナント間が通信することはありません。

Microsoft Azure パブリック クラウドでの SAP ワークロードのデプロイに関するトピックでは、その他のさまざまなリソースが紹介されています。 Azure での SAP HANA のデプロイを計画して実行する場合は、Azure IaaS のプリンシパルと Azure IaaS での SAP ワークロードのデプロイに詳しい担当者を起用することを強くお勧めします。 次のリソースで詳細を示します。これらのリソースは作業を続行する前に参照してください。


- [Microsoft Azure 仮想マシンにおける SAP ソリューションの使用](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>認定

SAP では、Azure IaaS などの特定のインフラストラクチャ上の SAP HANA をサポートするための要件として、NetWeaver 認定資格のほか、SAP HANA の特別な認定資格を要求しています。

NetWeaver (および一部の SAP HANA 認定資格) に関する主要な SAP ノートは、「[SAP Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533)」(SAP ノート #1928533 – Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類) です。

この「[SAP Note #2316233 - SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233/E)」(SAP ノート #2316233 - SAP HANA on Microsoft Azure (L インスタンス)) も重要です。 この SAP ノートでは、このガイドで説明するソリューションについて取り上げています。 また、GS5 VM の種類の Azure での SAP HANA の実行がサポートされています。 [この場合の情報は、SAP の Web サイトで公開されています](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)。

SAP ノート #2316233 で取り上げられている SAP HANA on Azure (L インスタンス) ソリューションを使用すると、Microsoft と SAP のお客様は大規模な SAP Business Suite、SAP Business Warehouse (BW)、S/4 HANA、BW/4HANA、またはその他の SAP HANA ワークロードを Azure にデプロイできます。 このソリューションは、SAP-HANA 認定の専用のハードウェア スタンプ ([SAP HANA Tailored Datacenter Integration – TDI](https://scn.sap.com/docs/DOC-63140)) に基づいています。 SAP HANA TDI で構成されたソリューションとして実行すると、SAP HANA ベースのすべてのアプリケーション (SAP Business Suite on SAP HANA、SAP Business Warehouse (BW) on SAP HANA、S4/HANA、BW4/HANA など) をハードウェア インフラストラクチャで確実に機能させることができます。

Azure Virtual Machines で SAP HANA を実行する場合と比較すると、このソリューションにはメモリ ボリュームが大幅に増加するという利点があります。 このソリューションを有効にする場合は、いくつかの重要な点を理解しておく必要があります。

- SAP アプリケーション層および SAP 以外のアプリケーションは、通常の Azure ハードウェア スタンプでホストされる Azure Virtual Machines (VM) で実行されます。
- お客様のオンプレミスのインフラストラクチャ、データ センター、アプリケーションのデプロイは、Azure ExpressRoute (推奨) または仮想プライベート ネットワーク (VPN) 経由で Microsoft Azure クラウド プラットフォームに接続されます。 Active Directory (AD) と DNS も Azure に拡張されます。
- HANA ワークロード用の SAP HANA データベース インスタンスは SAP HANA on Azure (L インスタンス) で実行されます。 L インスタンス スタンプは Azure ネットワークに接続されるため、Azure VM で実行されるソフトウェアは HANA L インスタンスで実行される HANA インスタンスとやり取りできます。
- SAP HANA on Azure (L インスタンス) のハードウェアは、SUSE Linux Enterprise Server または Red Hat Enterprise Linux がプレインストールされたサービスとしてのインフラストラクチャ (IaaS) で提供される専用のハードウェアです。 Azure Virtual Machines と同様に、オペレーティング システムに対する更新とメンテナンスはユーザーが実行します。
- HANA のインストールまたは HANA L インスタンスのユニットでの SAP HANA の実行に必要な追加コンポーネントのインストールはユーザーが実行し、SAP HANA on Azure の実行中のすべての運用および管理は現状のままです。
- ここで説明するソリューション以外にも、SAP HANA on Azure (L インスタンス) に接続する Azure サブスクリプションに他のコンポーネントをインストールできます。  たとえば、SAP HANA データベースとの (直接の) 通信を有効にするコンポーネント (ジャンプ サーバー、RDP サーバー、SAP HANA Studio、SAP BI シナリオ向けの SAP Data Services、ネットワーク監視ソリューション) などです。
- Azure と同様に、HANA L インスタンスは高可用性とディザスター リカバリー機能をサポートします。

## <a name="architecture"></a>アーキテクチャ

SAP HANA on Azure (L インスタンス) ソリューションでは、大まかには、Azure VM に SAP アプリケーション層が配置され、Azure IaaS に接続される同じ Azure リージョン内の L インスタンス スタンプにある SAP TDI で構成されたハードウェアにデータベース層が配置されます。

> [!NOTE]
> SAP アプリケーション層は、SAP DBMS 層と同じ Azure リージョンにデプロイする必要があります。 この規則は、Azure 上の SAP ワークロードに関して公開されている情報で明確に文書化されています。 

SAP HANA on Azure (L インスタンス) の全体的なアーキテクチャでは、SAP TDI 認定のハードウェア構成 (SAP HANA データベース用の仮想化されていない、ベア メタルの高パフォーマンス サーバー)、およびニーズに合わせて SAP アプリケーション層用のリソースをスケールするための Azure の機能と柔軟性が提供されます。

![SAP HANA on Azure (L インスタンス) のアーキテクチャの概要](./media/hana-overview-architecture/image1-architecture.png)

上記のアーキテクチャは、次の 3 つのセクションに分かれています。

- **右:** エンド ユーザーがアクセスする LOB アプリケーション (SAP など) を使用してデータ センター内のさまざまなアプリケーションを実行するオンプレミスのインフラストラクチャ。 可能であれば、Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) を使用してこのオンプレミスのインフラストラクチャを Azure に接続するのが理想的です。

- **中央:** Azure IaaS を示します。この例では、Azure VM を使用して、SAP アプリケーションや、SAP HANA を DBMS システムとして使用する他のアプリケーションをホストします。 Azure VM が提供するメモリを使用して機能する小規模な HANA インスタンスは、アプリケーション層と共に Azure VM にデプロイされます。 Virtual Machines の詳細については、[こちら](https://azure.microsoft.com/services/virtual-machines/)を参照してください。
<br />SAP システムとその他のアプリケーションをグループ化して Azure Virtual Network (VNet) を作成するために、Azure ネットワークが使用されます。 これらの VNet は、オンプレミスのシステムおよび SAP HANA on Azure (L インスタンス) に接続します。
<br />Microsoft Azure での実行用にサポートされている SAP NetWeaver アプリケーションおよびデータベースについては、「[SAP Support Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533)」(SAP サポート ノート #1928533 – Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類) を参照してください。 Azure での SAP ソリューションのデプロイについては、次のドキュメントをご覧ください。

  -  [Windows Virtual Machines (VM) における SAP の使用](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Microsoft Azure 仮想マシンにおける SAP ソリューションの使用](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **左:** Azure L インスタンス スタンプの SAP HANA TDI 認定ハードウェアを示します。 HANA L インスタンス ユニットは、オンプレミスから Azure への接続と同じテクノロジを使用してサブスクリプションの Azure VNet に接続されます。

Azure L インスタンス スタンプ自体は次のコンポーネントを結合します。

- **コンピューティング:** 必要なコンピューティング能力を提供し、SAP HANA 認定を受けている Intel Xeon E7-8890v3 プロセッサまたは Intel Xeon E7-8890v4 プロセッサを搭載したサーバー。
- **ネットワーク:** コンピューティング、ストレージ、および LAN コンポーネントを相互接続する統合された高速ネットワーク ファブリック。
- **ストレージ:** 統合されたネットワーク ファブリック経由でアクセスするストレージ インフラストラクチャ。 デプロイする SAP HANA on Azure (L インスタンス) 構成に応じて、特定のストレージ容量が提供されます (追加の月額をお支払いいただくと、ストレージ容量を増やすことができます)。

L インスタンス スタンプのマルチテナント インフラストラクチャ内では、分離されたテナントとしてお客様の環境がデプロイされます。 テナントのデプロイ時に、Azure 加入契約内で Azure サブスクリプションに名前を付ける必要があります。 これが HANA L インスタンスの課金対象となる Azure サブスクリプションになります。 これらのテナントには Azure サブスクリプションとの 1 対 1 の関係があります。 ネットワーク的には、異なる Azure サブスクリプションに属する異なる Azure VNet から、1 つの Azure リージョン内の 1 つのテナントにデプロイされた HANA L インスタンス ユニットにアクセスできます。 ただし、それらの Azure サブスクリプションが同じ Azure 加入契約に属している必要があります。 

Azure VM と同様に、SAP HANA on Azure (L インスタンス) は複数の Azure リージョンで提供されます。 障害復旧機能を提供するためにオプトインを選択できます。 1 つの地政学的リージョン内の異なる L インスタンス スタンプが相互に接続されます。 たとえば、米国西部と米国東部の HANA L インスタンス スタンプは、DR レプリケーションのために、専用のネットワーク リンクを介して接続されます。 

Azure Virtual Machines で異なる VM の種類から選択できるのと同様に、ワークロードの種類が異なる SAP HANA に対応した HANA L インスタンスのさまざまな SKU から選択できます。 SAP では、プロセッサのソケットの比率に対してメモリを適用し、Intel プロセッサの世代に基づいてワークロードを変更します。4 種類の SKU が提供されています。

2017 年 7 月時点で、SAP HANA on Azure (L インスタンス) は、米国西部、米国東部、オーストラリア東部、オーストラリア南東部、西ヨーロッパ、北ヨーロッパの各 Azure リージョンで、次のような複数の構成で提供されています。

| SAP ソリューション | CPU | メモリ | Storage | 可用性 |
| --- | --- | --- | --- | --- |
| OLAP 用に最適化: SAP BW、BW/4HANA、<br /> または SAP HANA (一般的な OLAP ワークロード向け) | SAP HANA on Azure S72<br /> – 2 x Intel® Xeon® プロセッサ E7-8890 v3<br /> 36 CPU コアと 72 CPU スレッド |  768 GB |  3 TB | 使用可能 |
| --- | SAP HANA on Azure S144<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v3<br /> 72 CPU コアと 144 CPU スレッド |  1.5 TB |  6 TB | 提供終了 |
| --- | SAP HANA on Azure S192<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 96 CPU コアと 192 CPU スレッド |  2.0 TB |  8 TB | 使用可能 |
| --- | SAP HANA on Azure S384<br /> – 8 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 192 CPU コアと 384 CPU スレッド |  4.0 TB |  16 TB | 注文可能 |
| OLTP 用に最適化: SAP Business Suite<br /> on SAP HANA または S/4HANA (OLTP)、<br /> 一般的な OLTP | SAP HANA on Azure S72m<br /> – 2 x Intel® Xeon® プロセッサ E7-8890 v3<br /> 36 CPU コアと 72 CPU スレッド |  1.5 TB |  6 TB | 使用可能 |
|---| SAP HANA on Azure S144m<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v3<br /> 72 CPU コアと 144 CPU スレッド |  3.0 TB |  12 TB | 提供終了 |
|---| SAP HANA on Azure S192m<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 96 CPU コアと 192 CPU スレッド  |  4.0 TB |  16 TB | 使用可能 |
|---| SAP HANA on Azure S384m<br /> – 8 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 192 CPU コアと 384 CPU スレッド |  6.0 TB |  18 TB | 注文可能 |
|---| SAP HANA on Azure S384xm<br /> – 8 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 192 CPU コアと 384 CPU スレッド |  8.0 TB |  22 TB |  注文可能 |
|---| SAP HANA on Azure S576<br /> – 12 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 288 CPU コアと 576 CPU スレッド |  12.0 TB |  28 TB | 注文可能 |
|---| SAP HANA on Azure S768<br /> – 16 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 384 CPU コアと 768 CPU スレッド |  16.0 TB |  36 TB | 注文可能 |
|---| SAP HANA on Azure S960<br /> – 20 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 480 CPU コアと 960 CPU スレッド |  20.0 TB |  46 TB | 注文可能 |

- CPU コア = サーバー ユニットのプロセッサの合計の、非ハイパースレッド CPU コアの合計。
- CPU スレッド = サーバー ユニットのプロセッサの合計の、ハイパースレッド CPU コアによって提供されるコンピューティング スレッドの合計。 すべてのユニットは、既定で、ハイパースレッディングを使用するように構成されています。


"使用可能" または "提供終了" と示されている上記の各種構成については、「[SAP Support Note #2316233 – SAP HANA on Microsoft Azure (Large Instances) (SAP サポート ノート #2316233 – SAP HANA on Microsoft Azure (L インスタンス))](https://launchpad.support.sap.com/#/notes/2316233/E)」をご覧ください。 "注文可能" と示されている構成については、間もなく SAP ノートで確認できるようになります。 ただし、それらのインスタンス SKU は、HANA L インスタンス サービスを利用できる 6 か所の Azure リージョン用に既に注文できます。

選択する構成は、ワークロード、CPU リソース、および必要なメモリによって異なります。 OLTP ワークロードでは、OLAP ワークロード用に最適化された SKU を使用できます。 

すべてのプランのハードウェア ベースは、SAP HANA TDI 認定を受けています。 ただし、ハードウェアの 2 種類のクラスを区別し、SKU を次のように分けています。

- S72、S72m、S144、S144m、S192、S192m - "Type I クラス" の SKU と呼びます。
- S384、S384m、S384xm、S576、S768、S960 - "Type II クラス" の SKU と呼びます。

完全な HANA L インスタンス スタンプは 1 件のお客様が使用するためだけに割り当てられるものではないことに注意してください。 これは、Azure にデプロイされたネットワーク ファブリック経由で接続されるコンピューティング リソースとストレージ リソースのラックにも当てはまります。 Azure などの HANA L インスタンスのインフラストラクチャは、次の 3 つのレベルで別のテナントから分離されたお客様の "&quot;テナント&quot;" をデプロイします。

- ネットワーク: HANA L インスタンス スタンプ内の仮想ネットワークを通じて分離します。
- ストレージ: ストレージ ボリュームが割り当てられており、テナント間でストレージ ボリュームを分離するストレージ仮想マシンを通じて分離します。
- コンピューティング: サーバー ユニットを 1 つのテナントに独占的に割り当てます。 サーバー ユニットがハード パーティション分割やソフト パーティション分割されることはなく、 1 つのサーバー ユニットやホスト ユニットがテナント間で共有されることもありません。 

このため、異なるテナント間で HANA L インスタンス ユニットのデプロイが互いに表示されることはありません。 また、異なるテナントにデプロイされた HANA L インスタンス ユニットが、HANA L インスタンス スタンプ レベルで相互に直接通信することもできません。 HANA L インスタンス スタンプ レベルでは、同じテナント内の HANA L インスタンス ユニットだけが相互に通信することができます。
L インスタンス スタンプにデプロイされたテナントは、課金の点で 1 つの Azure サブスクリプションに割り当てられます。 ただし、ネットワーク的には、同じ Azure 加入契約内の他の Azure サブスクリプションの Azure VNet からアクセスできます。 同じ Azure リージョンの別の Azure サブスクリプションを使用してデプロイする場合は、分離された HANA L インスタンス テナントを要求することもできます。

HANA L インスタンスで実行する SAP HANA と Azure にデプロイされた Azure VM で実行する SAP HANA には大きな違いがあります。

- SAP HANA on Azure (L インスタンス) の仮想化層はありません。 基になるベア メタル ハードウェアのパフォーマンスを確保します。
- Azure とは異なり、SAP HANA on Azure (L インスタンス) サーバーは特定のお客様に専用のサーバーです。 サーバー ユニットやホスト ユニットがハード パーティション分割やソフト パーティション分割されることはありません。 そのため、HANA L インスタンス ユニットは、その全体がテナントおよびお客様に割り当てられて使用されます。 サーバーを再起動またはシャットダウンしても、自動的にオペレーティング システムと SAP HANA が別のサーバーにデプロイされることはありません。 (Type I クラスの SKU の場合、唯一の例外は、サーバーで問題が発生している可能性があり、別のサーバーで再デプロイを実行する必要がある場合です)。
- Azure とは異なり、価格/パフォーマンス比が最も高くなるようにホスト プロセッサの種類を選択する場合は、Intel E7v3 および E7v4 プロセッサ ラインの最もパフォーマンスが高いプロセッサが SAP HANA on Azure (L インスタンス) 用に選択されます。


### <a name="running-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>1 つの HANA L インスタンス ユニットでの複数の SAP HANA インスタンスの実行
HANA L インスタンス ユニットで複数のアクティブな SAP HANA インスタンスをホストできます。 ストレージ スナップショットとディザスター リカバリーの機能を引き続き提供するために、このような構成ではインスタンスごとにボリューム セットが必要になります。 現時点では、HANA L インスタンス ユニットを次のように分けることができます。

- S72、S72m、S144、S192: 256 GB の最小ユニットから 256 GB ずつ増加。 256 GB、512 GB などの異なる増分値をユニットの最大メモリまで組み合わせることができます。
- S144m、S192m: 512 GB の最小ユニットから 256 GB ずつ増加。 512 GB、768 GB などの異なる増分値をユニットの最大メモリまで組み合わせることができます。
- Type II クラス: 2 TB の最小ユニットから 512 GB ずつ増加。 512 GB、1 TB、1.5 TB などの異なる増分値をユニットの最大メモリまで組み合わせることができます。

複数の SAP HANA インスタンスの実行例を次に示します。

| SKU | メモリ サイズ | ストレージ サイズ | 複数のデータベースとサイズ |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 768 GB HANA インスタンス x 1<br /> または 512 GB インスタンス x 1 + 256 GB インスタンス x 1<br /> または 256 GB インスタンス x 3 | 
| S72m | 1.5 TB | 6 TB | 512 GB HANA インスタンス x 3<br />または 512 GB インスタンス x 1 + 1 TB インスタンス x 1<br />または 256 GB インスタンス x 6<br />または 1.5 TB インスタンス x 1 | 
| S192m | 4 TB | 16 TB | 512 GB インスタンス x 8<br />または 1 TB インスタンス x 4<br />または 512 GB インスタンス x 4 + 1 TB インスタンス x 2<br />または 768 GB インスタンス x 4 + 512 GB インスタンス x 2<br />または 4 TB インスタンス x 1 |
| S384xm | 8 TB | 22 TB | 2 TB インスタンス x 4<br />または 4 TB インスタンス x 2<br />または 3 TB インスタンス x 2 + 2 TB インスタンス x 1<br />または 2.5 TB インスタンス x 2 + 3 TB インスタンス x 1<br />または 8 TB インスタンス x 1 |


大体おわかりでしょうか。 他のバリエーションもあることは確かです。 

### <a name="using-sap-hana-data-tiering-and-extension-nodes"></a>SAP HANA のデータ階層化および拡張ノードの使用
SAP では、さまざまな SAP NetWeaver リリースの SAP BW および SAP BW/4HANA のデータ階層化モデルをサポートしています。 データ階層化モデルに関する詳細については、このドキュメントおよびこのドキュメントで参照している SAP のブログ (「[SAP BW/4HANA AND SAP BW ON HANA WITH SAP HANA EXTENSION NODES (SAP HANA 拡張ノードを持つ SAP BW/4HANA および SAP BW-on-HANA)](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#)」) を参照してください。
HANA L インスタンスを使用すると、この FAQ および SAP のブログ ドキュメントで述べられているように、SAP HANA 拡張ノードのオプション 1 の構成を使用できます。 オプション 2 の構成は次の HANA L インスタンス SKU を使用して設定できます: S72m、S192、S192m、S384、S384m。  
ドキュメントを参照してもすぐには利点が見つからないかもしれません。 しかし、SAP のサイズ変更ガイドラインを見ると、オプション 1 およびオプション 2 の SAP HANA 拡張ノードの使用による利点が分かります。 次に例を示します。

- SAP HANA のサイズ変更ガイドラインでは通常、メモリの 2 倍のデータ ボリュームが必要です。 従って、ホット データで SAP HANA インスタンスを実行している場合、メモリの 50% 以下しかデータが入っていません。 残りのメモリは、SAP HANA が作業を実行するために残しておくのが理想です。
- つまり、2 TB のメモリを持つ HANA L インスタンス S192 ユニットで SAP BW データベースを実行すると、データ ボリュームとしては 1 TB しかありません。
- オプション 1 の追加の SAP HANA 拡張ノード、また S192 HANA L インスタンス SKU を使用する場合は、データ ボリュームに追加で 2 TB の容量が得られます。 オプション 2 の構成では、ウォーム データ ボリューム用に 4 TB が追加されます。 ホット ノードと比較した場合、オプション 1 ではデータ格納に "ウォーム" 拡張ノードの全メモリ容量を使用することができ、オプション 2 の SAP HANA 拡張ノード構成ではデータ ボリュームに 2 倍のメモリを使用することができます。
- その結果、オプション 1 ではデータ容量が 3 TB、ホット データとウォーム データの割合が 1:2 になり、オプション 2 の拡張ノード構成ではデータ容量が 5 TB、ホット/ウォームの割合が1:4 になります。

ただし、メモリに対してデータ ボリュームが大きいほど、求めるウォーム データがディスク ストレージに保存されている可能性が高くなります。


## <a name="operations-model-and-responsibilities"></a>運用モデルと責任

SAP HANA on Azure (L インスタンス) が提供するサービスは Azure IaaS サービスと連携しています。 HANA L インスタンスには、SAP HANA 用に最適化されたオペレーティング システムがインストールされています。 Azure IaaS VM と同様に、OS の強化、必要な追加ソフトウェアのインストール、HANA のインストール、OS と HANA の運用、OS と HANA の更新などの大部分のタスクはユーザーが担当します。 Microsoft が OS の更新や HANA の更新を強制することはありません。

![SAP HANA on Azure (L インスタンス) の責任](./media/hana-overview-architecture/image2-responsibilities.png)

上の図に示すように、SAP HANA on Azure (L インスタンス) はマルチ テナントのサービスとしてのインフラストラクチャです。 そのため、ほとんどの部分で OS とインフラストラクチャの境界に基づいて責任が分担されています。 Microsoft はオペレーティング システムのラインの下側のサービスのすべての面を担当し、お客様はラインの上側 (オペレーティング システムを含む) を担当します。 そのため、コンプライアンス、セキュリティ、アプリケーション管理、基盤と OS の管理に現在使用しているほとんどのオンプレミスの手法を引き続き使用できます。 システムはネットワーク内に存在するかのように見えます。

ただし、このサービスは SAP HANA 用に最適化されているため、最良の結果を得るためにお客様と Microsoft が協力して、基になるインフラストラクチャの機能を使用しなければならない領域が存在します。

それぞれの層とお客様の責任の詳細を次に示します。

**ネットワーク:** SAP HANA を実行する L インスタンス スタンプ、ストレージへのアクセス、インスタンス間の接続 (スケールアウトとその他の機能用)、ランドスケープへの接続、SAP アプリケーション層が Azure Virtual Machines でホストされる Azure への接続のためのすべての内部ネットワーク。 また、障害復旧時のレプリケーション用の Azure データ センター間の WAN 接続も含まれます。 すべてのネットワークはテナントによってパーティション分割され、QoS が適用されます。

**ストレージ:** SAP HANA サーバーで必要なすべてのボリュームおよびスナップショットに対応した、仮想化およびパーティション分割されたストレージ。 

**サーバー:** テナントに割り当てられている SAP HANA DB を実行する専用の物理サーバー。 Type I クラスの SKU のサーバーは、抽象化されたハードウェアです。 この種のサーバーでは、サーバー構成が収集され、物理ハードウェア間で移動できるプロファイルに保持されます。 操作によるプロファイルのこのような (手動の) 移動は、Azure のサービス復旧に少し似ています。 Type II クラスの SKU のサーバーは、このような機能を提供していません。

**SDDC:** データ センターをソフトウェアによるエンティティとして管理するために使用する管理ソフトウェア。 SDDC により、Microsoft はスケール、可用性、パフォーマンスのためのリソースをプールできます。

**O/S:** サーバーで実行するために選択する OS (SUSE Linux または Red Hat Linux)。 お客様に提供される OS イメージは、SAP HANA を実行するために個々の Linux ベンダーが Microsoft に提供するイメージになります。 特定の SAP HANA 最適化イメージでは、Linux ベンダーのサブスクリプションが必要です。 お客様の責任には、OS ベンダーへのイメージの登録が含まれます。 さらに、Microsoft による引き渡しの時点から、Linux オペレーティング システムの修正プログラムも、お客様の責任になります。 この修正プログラムには、SAP HANA を正常にインストールするために必要だが、特定の Linux ベンダーが SAP HANA に最適化された OS イメージに含めていない追加パッケージも含まれます (HANA のインストールに関する SAP のドキュメントと SAP ノートをご覧ください)。 お客様の責任には、OS の動作不良/最適化に関連する OS の修正プログラムと特定のサーバー ハードウェアに関連するドライバー、 または OS のセキュリティや機能の修正プログラムも含まれます。 以下の項目の監視とキャパシティ プランニングも、お客様の責任になります。

- CPU リソース使用量
- メモリ使用量
- 空き領域、IOPS、待機時間に関連するディスク ボリューム
- HANA L インスタンスと SAP アプリケーション層の間のネットワーク ボリュームのトラフィック

HANA L インスタンスの基になるインフラストラクチャは、OS ボリュームのバックアップと復元の機能を提供します。 この機能もお客様の責任で使用します。

**ミドルウェア:** 主に SAP HANA インスタンスです。 管理、運用、監視はお客様が担当します。 ストレージのスナップショットを使用してバックアップ/復元および障害復旧を実行するための機能が提供されます。 これらの機能はインフラストラクチャから提供されます。 ただし、これらの機能を使用した高可用性とディザスター リカバリーの設計、高可用性とディザスター リカバリーの活用、ストレージ スナップショットが正常に実行されたかどうかの監視もお客様の責任に含まれます。

**データ:** SAP HANA で管理されるデータおよびその他のデータ (ボリュームやファイル共有上のバックアップ ファイルなど)。 お客様の責任には、ディスクの空き領域の監視、ボリューム上のコンテンツの管理、およびディスク ボリュームとストレージのスナップショットのバックアップが正常に実行されたかどうかの監視が含まれます。 ただし、データを DR サイトに正常にレプリケーションする処理は Microsoft が担当します。

**アプリケーション:** SAP アプリケーション インスタンス。または SAP 以外のアプリケーションの場合は、それらのアプリケーションのアプリケーション層。 お客様の責任には、Azure VNet 内および Azure VNet から SAP HANA on Azure (L インスタンス) への CPU リソース使用量、メモリ使用量、Azure Storage 使用量、ネットワーク帯域幅の使用量のキャパシティ プランニングに関連するアプリケーションのデプロイ、管理、運用、監視が含まれます。

**WAN:** ワークロード用に確立するオンプレミスから Azure デプロイメントへの接続。 HANA L インスタンスを使用するすべてのお客様が、接続に Azure ExpressRoute を使用します。 この接続は SAP HANA on Azure (L インスタンス) ソリューションの一部ではないため、この接続のセットアップを担当するのはユーザーです。

**アーカイブ:** ストレージ アカウントで独自の手法を使用してデータのアーカイブ コピーを作成できます。 アーカイブには、管理、コンプライアンス、コスト、運用が必要です。 ユーザーが担当するのは、Azure でアーカイブ コピーとバックアップを生成し、対応する方法でそれらを保存する処理です。

「[Azure 上の SAP HANA の L インスタンスの SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/)」を参照してください。

## <a name="sizing"></a>サイズ変更

通常、HANA L インスタンスのサイズ変更は HANA のサイズ変更とまったく同じです。 デプロイ済みの既存のシステムでは、他の RDBMS から HANA への移動が必要な場合があり、既存の SAP システムで実行する複数のレポートが SAP から提供されています。 データベースを HANA に移動する場合、これらのレポートによってデータが確認され、HANA インスタンスのメモリ要件が計算されます。 これらのレポートを実行する方法、およびレポートの最新の修正プログラム/バージョンを入手する方法の詳細については、次の SAP ノートを参照してください。

- [SAP Note #1793345 - Sizing for SAP Suite on HANA (SAP ノート #1793345 - HANA での SAP Suite のサイズ変更)](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Suite on HANA and S/4 HANA sizing report (SAP ノート #1872170 - Suite on HANA および S/4 HANA のサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330 - FAQ: SAP BW on HANA Sizing Report (SAP ノート #2121330 - FAQ: SAP BW on HANA のサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - Sizing Report for BW on HANA (SAP ノート #1736976 - BW on HANA のサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - New Sizing Report for BW on HANA (SAP ノート #2296290 - BW on HANA の新しいサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/2296290)

緑色のフィールドの実装では、SAP Quick Sizer を使用して、HANA 上への SAP ソフトウェアの実装に必要なメモリを計算できます。

HANA に必要なメモリはデータ ボリュームの拡張に伴って増加するため、現時点でのメモリ使用量を把握しておくと、将来的に必要になるメモリを予測できます。 必要なメモリに基づいて、いずれかの HANA L インスタンス SKU に要求をマップできます。

## <a name="requirements"></a>必要条件

SAP HANA on Azure (L インスタンス) を実行するための要件を以下に示します。

**Microsoft Azure:**

- SAP HANA on Azure (L インスタンス) にリンク可能な Azure サブスクリプション。
- Microsoft プレミア サポートの連絡先。 Azure での SAP の実行に関連する情報については、「[SAP Support Note #2015553 – SAP on Microsoft Azure: Support Prerequisites](https://launchpad.support.sap.com/#/notes/2015553)」(SAP サポート ノート #2015553 – Microsoft Azure 上のSAP: サポートの前提条件) を参照してください。 384 個以上の CPU で HANA L インスタンス ユニットを使用する場合は、Premier サポート契約に拡張して Azure Rapid Response (ARR) を含める必要があります。
- SAP を使用したサイズ変更のエクササイズの実行後に必要な HANA L インスタンス SKU の認識。

**ネットワーク接続:**

- オンプレミスと Azure 間の Azure ExpressRoute: オンプレミスのデータ センターを Azure に接続するには、1 Gbps 以上の接続を ISP と契約してください。 

**オペレーティング システム:**

- SAP アプリケーション用の SUSE Linux Enterprise Server 12 のライセンス。

> [!NOTE] 
> Microsoft が提供するオペレーティング システムは SUSE に登録されず、SMT インスタンスにも接続されません。

- Azure VM 上の Azure にデプロイされる SUSE Linux Subscription Management Tool (SMT)。 これにより、SAP HANA on Azure (L インスタンス) を SUSE で登録し、個々に更新できるようになります (HANA L インスタンス データ センター内ではインターネットにアクセスできないため)。 
- SAP HANA 用の Red Hat Enterprise Linux 6.7 または 7.2 のライセンス。

> [!NOTE]
> Microsoft が提供するオペレーティング システムは Red Hat に登録されません。また、Red Hat Subscription Manager インスタンスにも接続されません。

- Azure VM 上の Azure にデプロイされる Red Hat Subscription Manager。 Red Hat Subscription Manager により、SAP HANA on Azure (L インスタンス) を Red Hat で登録し、個々に更新できるようになります (Azure L インスタンス スタンプにデプロイされたテナント内から直接インターネットにアクセスできないため)。
- SAP が定める要件上、Linux プロバイダーとのサポート契約も必要です。 HANA L インスタンスのソリューションであるということ、または Linux を Azure 内で実行するという事実によって、この要件がなくなるわけではありません。 いくつかの Linux Azure ギャラリー イメージとは異なり、HANA L インスタンスのソリューション プランにサービス使用料は含まれません。 Linux ディストリビューターとのサポート契約に関する SAP の要件は、お客様ご自身で満たす必要があります。   
   - SUSE Linux については、「[SAP Note #1984787 - SUSE LINUX Enterprise Server 12: Installation notes (SAP ノート #1984787 - SUSE LINUX Enterprise Server 12: インストールに関する注意事項)](https://launchpad.support.sap.com/#/notes/1984787)」および「[SAP Note #1056161 - SUSE Priority Support for SAP applications (SAP ノート #1056161 - SAP アプリケーションの SUSE 優先サポート)](https://launchpad.support.sap.com/#/notes/1056161)」でサポート契約の要件を確認してください。
   - Red Hat Linux の場合、サポートとサービス (HANA L インスタンスのオペレーティング システムに対する更新) を含む適切なサブスクリプション レベルを満たしている必要があります。 Red Hat では、"RHEL for SAP Business Applications" サブスクリプションの取得を推奨しています。 サポートとサービスの詳細については、「[SAP Note #2002167 - Red Hat Enterprise Linux 7.x: Installation and Upgrade (SAP ノート #2002167 - Red Hat Enterprise Linux 7.x: インストールとアップグレード)](https://launchpad.support.sap.com/#/notes/2002167)」および「[SAP Note #1496410 - Red Hat Enterprise Linux 6.x: Installation and Upgrade (SAP ノート #1496410 - Red Hat Enterprise Linux 6.x: インストールとアップグレード)](https://launchpad.support.sap.com/#/notes/1496410)」をご覧ください。

**データベース:**

- SAP HANA (Platform Edition または Enterprise Edition) 用のライセンスとソフトウェア インストール コンポーネント。

**アプリケーション:**

- SAP HANA に接続する SAP アプリケーション用のライセンスとソフトウェア インストール コンポーネント、および関連する SAP サポート契約。
- SAP HANA on Azure (L インスタンス) 環境に関連して使用される SAP 以外のアプリケーション用のライセンスとソフトウェア インストール コンポーネント、および関連する SAP サポート契約。

**スキル:**

- Azure IaaS とそのコンポーネントの使用経験と知識。
- Azure への SAP ワークロードのデプロイの経験と知識。
- "SAP HANA Installation" の資格を持つ担当者。
- SAP HANA に関連する高可用性と障害復旧を設計するための SAP アーキテクトのスキル。

**SAP:**

- SAP の顧客であり、SAP とサポート契約を結んでいることが望まれます。
- 特に、Type II クラスの HANA L インスタンス SKU での実装の場合、サイズの大きいスケールアップ ハードウェア上の SAP HANA のバージョンと最終的な構成について、SAP に相談することを強くお勧めします。


## <a name="storage"></a>Storage

SAP HANA on Azure (L インスタンス) の記憶域のレイアウトは、ホワイト ペーパー『[SAP HANA Storage Requirements (SAP HANA ストレージ要件)](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)』に記載されている SAP が推奨するガイドラインを使用して、SAP HANA on Azure サービス管理によって構成されています。

Type I クラスの HANA L インスタンスには、ストレージ ボリュームの 4 倍のメモリ ボリュームが搭載されます。 Type II クラスの HANA L インスタンス ユニットの場合、ストレージが 4 倍以上になることはありません。 このユニットは、HANA トランザクション ログ バックアップを保存するためのボリュームを備えています。 詳しくは、「[SAP HANA on Azure (L インスタンス) のインストールと構成の方法](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

記憶域割り当てに関しては、次の表を参照してください。 この表は、各種 HANA L インスタンス ユニットで提供されるさまざまなボリュームのおおよその容量を示しています。

| HANA L インスタンス SKU | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S192 | 4,608 GB | 1,024 GB | 1,536 GB | 1,024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12,000 GB | 2,050 GB | 2,050 GB | 2040 GB |
| S384xm | 16,000 GB | 2,050 GB | 2,050 GB | 2040 GB |
| S576 | 20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S768 | 28,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S960 | 36,000 GB | 4,100 GB | 2,050 GB | 4,100 GB |


実際にデプロイされるボリュームは、デプロイと、ボリューム サイズの表示に使用するツールによって少し異なる場合があります。

HANA L インスタンス SKU を分割する場合、考えられる各分割部分の例は次のようになります。

| メモリ パーティション (GB) | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1024 | 1,792 GB | 640 GB | 1,024 GB | 640 GB |
| 1536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


これらのサイズはおおよそのボリューム数であり、デプロイと、ボリュームの確認に使用するツールによって若干異なる場合があります。 他のパーティション サイズ (2.5 TB など) も考えられます。 これらのストレージ サイズは、上記のパーティションに使用したものと同様の数式で計算されます。 「パーティション」という用語は、オペレーティング システム、メモリ、または CPU リソースがパーティション分割されていることを示しているわけではありません。 1 つの HANA L インスタンス ユニットにデプロイできるさまざまな HANA インスタンスのストレージ パーティションを示しているにすぎません。 

ストレージを増やす必要がある場合は、ストレージを追加して 1 TB 単位で追加のストレージを購入できます。 この追加のストレージは、ボリュームとして追加することも、1 つ以上の既存のボリュームを拡張するために使用することもできます。 もともとデプロイされているボリュームのサイズ (上記の表に記載) を減らすことはできません。 また、ボリュームの名前やマウント名を変更することもできません。 上記のストレージ ボリュームは、NFS4 ボリュームとして HANA L インスタンス ユニットに接続されます。

バックアップ/復元やディザスター リカバリーのためにストレージ スナップショットを使用するかどうかは、お客様が選択できます。 この点について詳しくは、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

### <a name="encryption-of-data-at-rest"></a>保存データの暗号化
HANA L インスタンス用のストレージでは、ディスクに保存されるデータを透過的に暗号化することができます。 HANA L インスタンス ユニットのデプロイ時に、この種の暗号化を有効にするかどうかを選択できます。 また、暗号化の対象ボリュームをデプロイ後に変更することもできます。 非暗号化ボリュームから暗号化ボリュームへの移行は透過的に行われ、ダウンタイムは発生しません。 

Type I クラスの SKU では、ブート LUN が格納されているボリュームが暗号化されます。 HANA L インスタンスの Type II クラスの SKU の場合、OS 方式でブート LUN を暗号化する必要があります。 


## <a name="networking"></a>ネットワーク

Azure ネットワークのアーキテクチャは、HANA L インスタンスへの SAP アプリケーションのデプロイを成功させるための重要な要素です。 通常、SAP HANA on Azure (L インスタンス) デプロイメントには、サイズとデータベース、CPU リソース使用量、メモリ使用量が異なる複数の SAP ソリューションを含む大規模な SAP ランドスケープが存在します。 このような SAP システムのすべてが SAP HANA に基づくわけではないと考えられるので、SAP ランドスケープは次のコンポーネントを使用するハイブリッド構成になることがほとんどです。

- デプロイされた SAP システム (オンプレミス)。 サイズが原因で、これらのシステムは現在 Azure でホストできません。典型的な例として、Azure VM が提供するより多くの CPU またはメモリ リソースを必要とする、Microsoft SQL Server (データベースとして使用) で実行される SAP ERP 実稼働システムが挙げられます。
- デプロイされたSAP HANA ベースの SAP システム (オンプレミス)。
- デプロイされた SAP システム (Azure VM 内)。 Azure (VM) に正常にデプロイ可能な SAP NetWeaver ベースのアプリケーション用の (リソース使用量とメモリの需要に基づく) 開発、テスト、サンドボックス、または実稼働インスタンスです。 また、これらのシステムは、SQL Server (「[SAP Support Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types (SAP サポート ノート #1928533 – Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類)](https://launchpad.support.sap.com/#/notes/1928533/E)」を参照) や SAP HANA (「[SAP HANA Certified IaaS Platforms (SAP HANA 認定 IaaS プラットフォーム)](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)」を参照) などのデータベースに基づく場合もあります。
- Azure L インスタンス スタンプの SAP HANA on Azure (L インスタンス) を活用する、デプロイされた SAP アプリケーション サーバー (VM 上の Azure 内)。

(4 種類以上のデプロイ シナリオがある) ハイブリッド SAP ランドスケープが一般的ですが、Azure で実行されている完全な SAP ランドスケープの顧客事例が多数あります。 Microsoft Azure VM の強化に伴って、すべての SAP ソリューションを Azure に移行するお客様のの数が増えています。

Azure にデプロイされた SAP システムにおける Azure ネットワークは複雑なものではありません。 このネットワークは次の原則に基づいています。

- Azure Virtual Network (VNet) は、オンプレミスのネットワークに接続する Azure ExpressRoute 回線に接続する必要があります。
- 通常、オンプレミスを Azure に接続する ExpressRoute 回線には 1 Gbps 以上の帯域幅が必要です。 この最小帯域幅により、オンプレミスのシステムと Azure VM で実行されているシステム間のデータ転送 (およびオンプレミスのエンド ユーザーから Azure システムへの接続) に十分な帯域幅を確保できます。
- Azure 内のすべての SAP システムを Azure VNet で設定して、相互通信を可能にする必要があります。
- オンプレミスでホストされる Active Directory と DNS は、ExpressRoute 経由でオンプレミスから Azure に拡張されます。


> [!NOTE] 
> 課金の観点から、1 つの Azure サブスクリプションは、特定の Azure リージョン内の L インスタンス スタンプの 1 つのテナントにのみリンクできます。逆に言うと、1 つの L インスタンス スタンプのテナントは 1 つの Azure サブスクリプションにのみリンクできます。 この点は Azure のその他の課金対象オブジェクトと変わりません。

SAP HANA on Azure (L インスタンス) を複数の異なる Azure リージョンにデプロイすると、別のテナントが L インスタンス スタンプにデプロイされます。 ただし、2 つのインスタンスが同じ SAP ランドスケープに含まれている限り、それらは同じ Azure サブスクリプションで実行することができます。 

> [!IMPORTANT] 
> Azure Resource Management デプロイメントは SAP HANA on Azure (L インスタンス) でサポートされます。

 

### <a name="additional-azure-vnet-information"></a>Azure VNet の追加情報

Azure VNet を ExpressRoute に接続するには、Azure ゲートウェイを作成する必要があります (「[ExpressRoute 用の仮想ネットワーク ゲートウェイについて](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照)。 Azure ゲートウェイを使用すると、Azure の外部にあるインフラストラクチャ (または Azure L インスタンス スタンプ) への ExpressRoute 接続を確立したり、Azure VNet 間を接続したりできます (「[PowerShell を使用した Resource Manager の VNet 間接続の構成](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照)。 ExpressRoute の接続元が、異なる MS Enterprise Edges (MSEE) ルーターであれば、Azure ゲートウェイを最大 4 つの異なる ExpressRoute に接続できます。  詳細については、「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 

> [!NOTE] 
> Azure ゲートウェイが提供するスループットは 2 つのユース ケースで異なります (「[VPN Gateway について](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照)。 ExpressRoute 接続を使用して VNet ゲートウェイで実現する最大スループットは 10 Gbps です。 Azure VNet 内の Azure VM とオンプレミスのシステム間で (単一のコピー ストリームとして) ファイルをコピーすると、さまざまなゲートウェイ SKU の最大限のスループットを実現できないことに注意してください。 VNet ゲートウェイのすべての帯域幅を活用するには、複数のストリームを使用するか、1 つのファイルのパラレル ストリームで複数のファイルをコピーする必要があります。


### <a name="networking-architecture-for-hana-large-instances"></a>HANA L インスタンスのネットワーク アーキテクチャ
次に示す HANA L インスタンスのネットワーク アーキテクチャは、4 つの部分に分けることができます。

- オンプレミス ネットワークと Azure への ExpressRoute 接続。 この部分はお客様の領域であり、ExpressRoute 経由で Azure に接続されます。 これは次の図の右下の部分です。
- Azure VNet を使用する前述の Azure ネットワーク (ここでもゲートウェイを使用)。 これは、アプリケーションの要件、セキュリティ、コンプライアンス要件に適した設計を見つける必要がある領域です。 VNet と Azure ゲートウェイ SKU の数の選択の観点で、HANA L インスタンスの使用がもう 1 つの検討事項となります。 これは図の右上の部分です。
- ExpressRoute テクノロジを使用した Azure への HANA L インスタンスの接続。 この部分は Microsoft によってデプロイされ、処理されます。 お客様が行う必要があるのは、IP アドレス範囲をいくつか指定し、その後で ExpressRoute 回線を Azure VNet に接続して HANA L インスタンスに資産をデプロイすることだけです (「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照)。 
- HANA L インスタンスでのネットワーキング。ほとんどの場合、これはお客様に対して透過的です。

![SAP HANA on Azure (L インスタンス) およびオンプレミスに接続された Azure VNet](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

HANA L インスタンスを使用しても、オンプレミスの資産を ExpressRoute 経由で Azure に接続するための要件が変わることはありません。 また、HANA L インスタンス ユニットでホストされている HANA インスタンスに接続するアプリケーション層をホストする Azure VM を、1 つまたは複数の VNet で実行するための要件も変わりません。 

純粋な Azure での SAP デプロイとは次の点で異なります。

- 顧客テナントの HANA L インスタンス ユニットは、別の ExpressRoute 回線を介して Azure VNet に接続されます。 負荷条件を分離するために、オンプレミスと Azure VNet 間の ExpressRoute リンクと、Azure VNet と HANA L インスタンス間のリンクは、同じルーターを共有しません。
- SAP アプリケーション層と HANA インスタンス間のワークロード プロファイルは、SAP HANA からアプリケーション層への多数の小さな要求とデータ転送 (結果セット) などのバーストという異なる特性を示します。
- SAP アプリケーション アーキテクチャは、オンプレミスと Azure 間でデータが交換される標準的なシナリオよりも、ネットワーク待機時間の影響を受けやすくなります。
- VNet ゲートウェイには少なくとも 2 つの ExpressRoute 接続があり、これらの接続が VNet ゲートウェイの受信データの最大帯域幅を共有します。

Azure VM と HANA L インスタンス ユニット間で発生するネットワーク待機時間は、VM 間のネットワーク ラウンド トリップの標準的な待機時間よりも長くなる可能性があります。 Azure リージョンによっては、測定値が、「[SAP Note #1100926 - FAQ: Network performance (SAP ノート #1100926 - FAQ: ネットワーク パフォーマンス)](https://launchpad.support.sap.com/#/notes/1100926/E)」で平均以下に分類されているラウンド トリップの待機時間 (0.7 ms) を超える可能性があります。 しかし、お客様は SAP HANA ベースの実稼働 SAP アプリケーションを SAP HANA L インスタンスにまったく問題なくデプロイしています。 デプロイを実行したお客様から、HANA L インスタンス ユニットを使用して SAP HANA で SAP アプリケーションを実行することによる大幅な改善が報告されています。 ただし、Azure HANA L インスタンスでビジネス プロセスを十分にテストする必要があります。
 
Azure VM と HANA L インスタンス間の決定論的なネットワーク待機時間を実現するには、Azure VNet ゲートウェイ SKU の選択が不可欠となります。 オンプレミスと Azure VM 間のトラフィック パターンとは異なり、Azure VM と HANA L インスタンス間のトラフィック パターンでは、送信される要求やデータ量は小さくても、これらの大きなバーストが発生する可能性があります。 このようなバーストを適切に処理するために、UltraPerformance ゲートウェイ SKU を使用することを強くお勧めします。 Type II クラスの HANA L インスタンス SKU の場合、Azure VNet ゲートウェイとして UltraPerformance ゲートウェイ SKU を使用することが必須となります。  

> [!IMPORTANT] 
> SAP アプリケーションとデータベース層の間のすべてのネットワーク トラフィックについて考えた場合、SAP HANA on Azure (L インスタンス) への接続の際には、VNet 用の HighPerformance または UltraPerformance ゲートウェイ SKU のみサポートされます。 HANA L インスタンスの Type II SKU の場合、Azure VNet ゲートウェイとしてサポートされるのは UltraPerformance ゲートウェイ SKU だけです。



### <a name="single-sap-system"></a>単一の SAP システム

上に示したオンプレミスのインフラストラクチャは ExpressRoute 経由で Azure に接続され、ExpressRoute 回線は Microsoft Enterprise Edge Router (MSEE) に接続されます (「[ExpressRoute の技術概要](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照)。 接続が確立されると、そのルートは Microsoft Azure バックボーンに接続され、すべての Azure リージョンにアクセスできるようになります。

> [!NOTE] 
> Azure で SAP ランドスケープを実行する場合は、SAP ランドスケープの Azure リージョンに最も近い MSEE に接続してください。 Azure IaaS 内の Azure VM と L インスタンス スタンプ間のネットワーク待ち時間を最小限に抑えるため、Azure L インスタンス スタンプは専用の MSEE デバイスを経由して接続されます。

SAP アプリケーション インスタンスをホストする Azure VM 用の VNet ゲートウェイはその ExpressRoute 回線に接続され、L インスタンス スタンプに接続するための専用の別の MSEE ルーターに同じ VNet が接続されます。

これは、SAP アプリケーション層が Azure でホストされ、SAP HANA データベースが SAP HANA on Azure (L インスタンス) で実行される 1 つの SAP システムのわかりやすい例です。 VNet ゲートウェイの帯域幅 (2 Gbps または 10 Gbps のスループット) がボトルネックにならないことが前提です。

### <a name="multiple-sap-systems-or-large-sap-systems"></a>複数の SAP システムまたは大規模な SAP システム

SAP HANA on Azure (L インスタンス) に接続する複数の SAP システムまたは大規模な SAP システムをデプロイする場合は、VNet ゲートウェイのスループットがボトルネックになる可能性があることを想定するのが妥当です。 このような場合は、アプリケーション層を複数の Azure VNet に分割する必要があります。 また次のようなケースでは、HANA L インスタンスに接続する特別な VNet を作成することをお勧めします。

- NFS 共有のホストとなる Azure 内の VM に対して HANA L インスタンス内の HANA インスタンスから直接バックアップを実行する。
- HANA L インスタンス ユニットから、バックアップ ファイルなど大きなファイルを Azure 内の管理されたディスク領域にコピーする。

ストレージを管理する VM を個別の VNet でホストすれば、HANA L インスタンスから Azure に大きなファイルやデータを転送することによる影響が、SAP アプリケーション層を実行する VM にサービスを提供する VNet ゲートウェイに及ぶことはありません。 

スケーラブルなネットワーク アーキテクチャでは次のように対応します。

- 1 つの大規模な SAP アプリケーション層に対して複数の Azure VNet を活用します。
- 同じ VNet に属する個々のサブネット内の SAP システムの組み合わせに対して、デプロイされた SAP システムごとに個別の Azure VNet をデプロイします。

 SAP HANA on Azure (L インスタンス) 用のスケーラブルなネットワーク アーキテクチャを次に示します。

![複数の Azure VNet にわたる SAP アプリケーション層のデプロイ](./media/hana-overview-architecture/image4-networking-architecture.png)

上の図に示すように、SAP アプリケーション層 (コンポーネント) を複数の Azure VNet にわたってデプロイした場合、Azure VNet でホストされるアプリケーション間の通信時に発生する待機時間のオーバーヘッドを回避できませんでした。 既定では、異なる VNet にある Azure VM 間のネットワーク トラフィックは、この構成では MSEE ルーター経由でルーティングされます。 ただし、2016 年 9 月以降は、このルーティングを最適化できます。 2 つの VNet 間の通信を最適化し、遅延時間を削減する方法は、同じリージョン内の Azure VNet をピアリングすることです。 それらの VNet が異なるサブスクリプションに存在する場合も同様です。 Azure VNet のピアリングを使用することで、2 つの異なる Azure VNet 内の VM 間の通信は、Azure ネットワークのバックボーンを使用して直接的に相互に通信できます。 その結果、VM が同じ VNet 内に存在する場合と同様の遅延時間が示されます。 これに対して、Azure VNet ゲートウェイ経由で接続される IP アドレス範囲のトラフィックは、VNet の個々の VNet ゲートウェイ経由でルーティングされます。 Azure VNet のピアリングの詳細については、「[VNet ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)」を参照してください。


### <a name="routing-in-azure"></a>Azure でのルーティング

SAP HANA on Azure (L インスタンス) のネットワーク ルーティングに関する重要な考慮事項が 2 つあります。

1. SAP HANA on Azure (L インスタンス) には、専用の ExpressRoute 接続の Azure VM からのみアクセスできます。オンプレミスから直接アクセスすることはできません。 そのため、直接アクセスを必要とする管理クライアントやアプリケーション (オンプレミスで実行する SAP Solution Manager など) は SAP HANA データベースに接続できません。

2. SAP HANA on Azure (L インスタンス) ユニットには、お客様が提出したサーバー IP プールのアドレス範囲から IP アドレスが割り当てられます (詳しくは「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください)。  この IP アドレスには、Azure VNet を HANA on Azure (L インスタンス) に接続する ExpressRoute と Azure サブスクリプションを使用してアクセスできます。 サーバー IP プールのアドレス範囲から割り当てられる IP アドレスは、このソリューションの最初のデプロイにおけるケースであるため、直接ハードウェア ユニットに割り当てられ、NAT 変換されません。 

> [!NOTE] 
> "_データ ウェアハウス_" のシナリオ (直接実行されている SAP HANA データベースにアプリケーションまたはエンド ユーザーが接続する必要がある) で SAP HANA on Azure (L インスタンス) に接続する必要がある場合は、別のネットワーク コンポーネントを使用してください (データを相互にルーティングするためのリバース プロキシ)。 たとえば、仮想ファイアウォール/トラフィック ルーティング ソリューションとして Azure にデプロイする F5 BIG-IP や NGINX (および Traffic Manager) などです。

### <a name="internet-connectivity-of-hana-large-instances"></a>HANA L インスタンスのインターネット接続
HANA L インスタンスには、インターネットとの間に直接的な接続がありません。 そのため、機能上の制約が生じます (OS イメージを直接 OS ベンダーに登録できないなど)。 ローカル SLES SMT サーバーや RHEL サブスクリプション マネージャーに対する作業が必要となる場合があります。

### <a name="data-encryption-between-azure-vms-and-hana-large-instances"></a>Azure VM と HANA L インスタンス間のデータの暗号化
HANA L インスタンスと Azure VM 間で転送されるデータは暗号化されません。 ただし HANA DBMS 側と JDBC/ODBC ベースのアプリケーションとの間で純粋にデータを交換する目的であれば、トラフィックを暗号化することが可能です。 [SAP から提供されているこちらのドキュメント](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)をご覧ください。

### <a name="using-hana-large-instance-units-in-multiple-regions"></a>複数のリージョンでの HANA L インスタンス ユニットの使用

障害復旧を除くその他の理由で SAP HANA on Azure (L インスタンス) を複数の Azure リージョンにデプロイしなければならない場合があります。 このような場合はおそらく、リージョン内の異なる VNet にデプロイされた各 VM から HANA L インスタンスにアクセスする必要があります。 複数の HANA L インスタンス ユニットに割り当てられた IP アドレスは (ゲートウェイ経由でインスタンスに直接接続されている) Azure VNet を超えて反映されないため、前述の VNet の設計はわずかに変更されます。Azure VNet ゲートウェイは、複数の MSEE の 4 つの異なる ExpressRoute 回線を処理できます。いずれかの L インスタンス スタンプに接続される各 VNet は別の Azure リージョン内の L インスタンス スタンプに接続できます。

![複数の Azure リージョン内の Azure L インスタンス スタンプに接続される Azure VNet](./media/hana-overview-architecture/image8-multiple-regions.png)

上の図は、2 つのリージョン内の異なる Azure VNet が 2 つの Azure リージョン内の SAP HANA on Azure (L インスタンス) への接続に使用する 2 つの異なる ExpressRoute 回線にどのように接続されるかを示しています。 新しく導入される接続を直交する赤い線で示しています。 Azure VNet からのこれらの接続により、いずれかの VNet で実行される VM は、2 つのリージョンにデプロイされた各 HANA L インスタンス ユニットにアクセスできます。 上の図に示すように、オンプレミスから 2 つの Azure リージョンへの 2 つの ExpressRoute 接続があることが前提となります。この設計は、障害復旧の用途に推奨されます。

> [!IMPORTANT] 
> 複数の ExpressRoute 回線を使用する場合は、トラフィックを適切にルーティングするために、AS パス プリペンドとローカル プリファレンス BGP の設定を使用してください。


