---
title: Azure の SAP BusinessObjects BI プラットフォームのデプロイ | Microsoft Docs
description: Azure の SAP BusinessObjects BI プラットフォームの計画、デプロイ、および構成
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 3b3c313df5704e49481c66ad682faccd48d180ea
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505904"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Azure の SAP BusinessObjects BI プラットフォームの計画および実装ガイド

## <a name="overview"></a>概要

このガイドの目的は、SAP BusinessObjects BI プラットフォームを計画、デプロイ、および構成するためのガイドラインを提供することです。これは、Azure の SAP BOBI プラットフォームとも呼ばれます。 このガイドは、SAP BOBI プラットフォームに関連する一般的な Azure サービスと機能について説明することを目的としています。 このガイドでは、可能なすべての構成オプションが網羅されているわけではありません。 一般的なデプロイ シナリオに共通するソリューションが説明されています。

このガイドは、標準的な SAP BOBI プラットフォームのインストールと管理のガイド、オペレーティング システムやデータベースのドキュメントに代わるものではありません。

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>Azure の SAP BusinessObjects BI プラットフォームの計画および実装

Microsoft Azure には、コンピューティング、ストレージ、ネットワークなどのさまざまなサービスが用意されており、時間のかかる調達サイクルを省略してアプリケーションを構築することができます。 Azure 仮想マシン (VM) を使用すると、SAP NetWeaver ベースのアプリケーション、SAP Hybris、SAP BusinessObjects BI プラットフォームなどのさまざまな SAP アプリケーションに対して、ビジネス ニーズに基づいて、オンデマンドでスケーラブルなコンピューティング リソースをデプロイできます。 また、Azure ではクロス プレミス接続もサポートしており、これにより企業は Azure 仮想マシンを自社のオンプレミス ドメイン、プライベート クラウド、および SAP システム ランドス ケープに統合できます。

このドキュメントでは、Azure での SAP BusinessObjects BI プラットフォームの計画と実装に関する考慮事項についてガイダンスを提供します。 SAP BOBI をインストールしてデプロイするときの主要なリソースである SAP インストール ドキュメントと SAP Note を補足する内容となっています。

### <a name="architecture-overview"></a>アーキテクチャの概要

SAP BusinessObjects BI プラットフォームは自己完結型のシステムであり、1 つの Azure 仮想マシンに存在することも、さまざまなコンポーネントを実行する多数の Azure 仮想マシンから成るクラスターにスケーリングすることもできます。 SAP BOBI プラットフォームは、次の 6 つの概念層で構成されています。クライアント層、Web 層、管理層、ストレージ層、処理層、およびデータ層。 (各層の詳細については、[SAP BusinessObjects ビジネス インテリジェンス プラットフォーム](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US)のヘルプ ポータルにある管理者ガイドを参照してください)。 各層の大まかな詳細は次のとおりです。

- **クライアント層:** BI プラットフォームと対話してさまざまな種類のレポート、分析、および管理機能を提供する、すべてのデスクトップ クライアント アプリケーションが含まれます。
- **Web 層:** JAVA Web アプリケーション サーバーにデプロイされた Web アプリケーションが含まれます。 Web アプリケーションでは、Web ブラウザーを通してエンド ユーザーに BI プラットフォーム機能を提供します。
- **管理層:** BI プラットフォームを構成しているすべてのコンポーネントを調整および制御します。 中央管理サーバー (CMS) とイベント サーバー、および関連するサービスが含まれます
- **ストレージ層:** ドキュメントやレポートなどのファイルの処理を担当します。 また、ユーザーがレポートにアクセスしたときにシステム リソースを節約するために、レポート キャッシュも処理します。
- **処理層:** データを分析し、レポートやその他の種類の出力を生成します。 これは、レポート データを含むデータベースにアクセスする唯一の層です。
- **データ層:** CMS システム データベースと監査データ ストアをホストするデータベース サーバーで構成されます。

SAP BI プラットフォームは、1 つまたは複数のホストで実行されているサーバーのコレクションで構成されます。 規模、ビジネス ニーズ、および環境の種類に基づいて適切なデプロイ戦略を選択することが不可欠です。 開発やテストなどの小規模なインストールでは、1 つの Azure 仮想マシンを Web アプリケーション サーバー、データベース サーバー、およびすべての BI プラットフォーム サーバーに使用できます。 Azure から DBaaS (サービスとしてのデータベース) オファリングを使用している場合、データベース サーバーは他のコンポーネントとは別に実行されます。 中規模および大規模インストールでは、複数の Azure 仮想マシン上で実行されているサーバーを使用できます。

次の図は、Azure 仮想マシンでの SAP BOBI プラットフォームの大規模デプロイのアーキテクチャを示しています。各コンポーネントは可用性セットに分散されて配置されており、サービスが中断された場合にフェールオーバーを維持できます。

![Azure の SAP BusinessObjects BI プラットフォームのアーキテクチャ](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>アーキテクチャの詳細

- Load Balancer

  SAP BOBI のマルチインスタンス デプロイでは、Web アプリケーション サーバー (または Web 層) が 2 つ以上のホストで実行されています。 Web サーバー間でユーザー ロードを均等に分散するために、エンド ユーザーと Web サーバーの間にロード バランサーを使用できます。 Azure では、[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) または [Azure Application Gateway](../../../application-gateway/overview.md) を使用して、Web サーバーへのトラフィックを管理できます。

- Web アプリケーション サーバー

  Web サーバーでは、CMC や BI 起動パッドなどの SAP BOBI プラットフォームの Web アプリケーションをホストします。 Web サーバーの高可用性を実現するには、冗長性と負荷分散を管理するために、少なくとも 2 つの Web アプリケーション サーバーをデプロイする必要があります。 Azure では、これらの Web アプリケーション サーバーを可用性セットまたは可用性ゾーンのどちらかに配置して、可用性を向上させることができます。

  Tomcat は、SAP BI プラットフォームの既定の Web アプリケーションです。 Tomcat の高可用性を実現するには、Azure で[静的メンバーシップ インターセプター](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes)を使用してセッション レプリケーションを有効にします。 これにより、Tomcat サービスが中断された場合でも、ユーザーは SAP BI Web アプリケーションにアクセスできるようになります。

  > [!Important]
  > 既定では、Tomcat ではクラスタリングにマルチキャスト IP とポートが使用されますが、Azure ではサポートされていません (SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907))。

- BI プラットフォーム サーバー

  BI プラットフォーム サーバーには、SAP BOBI アプリケーション (管理層、処理層、およびストレージ層) の一部であるすべてのサービスが含まれています。 Web サーバーは、要求を受信すると、各 BI プラットフォーム サーバー (具体的には、クラスター内のすべての CMS サーバー) を検出し、要求の負荷を自動的に分散します。 BI プラットフォームのホストの 1 つで障害が発生した場合、Web サーバーは自動的に他のホストに要求を送信します。

  BI プラットフォームの高可用性または冗長性を実現するには、少なくとも 2 つの Azure 仮想マシンにアプリケーションをデプロイする必要があります。 規模に応じて、より多くの Azure 仮想マシンで実行されるように BI プラットフォームをスケーリングできます。

- ファイル リポジトリ サーバー (FRS)

  ファイル リポジトリ サーバーには、作成されたすべてのレポートとその他の BI ドキュメントが含まれています。 マルチインスタンス デプロイでは、BI プラットフォーム サーバーは複数の仮想マシン上で実行されており、各 VM がこれらのレポートやその他の BI ドキュメントにアクセスできる必要があります。 そのため、ファイル システムは、すべての BI プラットフォームサーバーで共有される必要があります。

  Azure では、ファイル リポジトリ サーバーに [Azure Premium Files](../../../storage/files/storage-files-introduction.md) または [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) を使用できます。 どちらの Azure サービスにも、冗長性が組み込まれています。

  > [!Important]
  > Azure Files の SMB プロトコルは一般公開されていますが、Azure Files の NFS プロトコル サポートは現在プレビューの段階です。 詳細については、「[Azure Files での NFS 4.1 サポートのプレビューが開始されました](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)」を参照してください

- CMS および監査データベース
  
  SAP BOBI プラットフォームには、システムデータを格納するデータベースが必要です。これは、CMS データベースと呼ばれます。 これは、ユーザー、サーバー、フォルダー、ドキュメント、構成、認証の詳細などの BI プラットフォーム情報を格納するために使用されます。

  Azure では、CMS データベースと監査データベースに使用できる DBaaS (サービスとしてのデータベース) オファリングとして、[MySQL Database](https://azure.microsoft.com/en-us/services/mysql/) と [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database/) を提供しています。 これは PaaS オファリングなので、お客様はデータベースの操作、可用性、およびメンテナンスについて心配する必要はありません。 また、お客様のビジネス ニーズに基づいて、CMS および監査リポジトリ用に独自のデータベースを選択することもできます。

## <a name="support-matrix"></a>サポート マトリックス

このセクションでは、SAP BusinessObjects BI プラットフォームのバージョン、オペレーティング システム、データベースなど、さまざまな SAP BOBI コンポーネントの Azure でのサポートについて説明します。

### <a name="sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI プラットフォーム

Azure IaaS (サービスとしてのインフラストラクチャ) を使用すると、Azure Compute で SAP BusinessObjects BI プラットフォームをデプロイして構成することができます。 次のバージョンの SAP BOBI プラットフォームがサポートされています。

- SAP BusinessObjects BI プラットフォーム 4.3
- SAP BusinessObjects BI プラットフォーム 4.2 SP04+
- SAP BusinessObjects BI プラットフォーム 4.1 SP05+

SAP BI プラットフォームは、さまざまなオペレーティング システムとデータベースで動作します。 オペレーティング システムとデータベースのバージョン間の SAP BOBI プラットフォームのサポートについては、SAP BOBI の[製品の可用性マトリックス](https://apps.support.sap.com/sap/support/pam)を参照してください。

### <a name="operating-system"></a>オペレーティング システム

Azure では、SAP BusinessObjects BI プラットフォームのデプロイに次のオペレーティング システムがサポートされています。

- Microsoft Windows サーバー
- SUSE Linux Enterprise Server (SLES)
- Red Hat Enterprise Linux (RHEL)
- Oracle Linux (OL)

[SAP BusinessObjects BI プラットフォームの製品の可用性マトリックス (PAM)](https://support.sap.com/pam) に記載されているオペレーティング システムのバージョンは、Azure インフラストラクチャで実行するための互換性がある限りにおいてサポートされます。

### <a name="databases"></a>データベース

BI プラットフォームには、[SAP 製品の可用性マトリックス](https://support.sap.com/pam)に記載されているサポートされるデータベースにインストールできる、次のような CMS および監査データ ストア用のデータベースが必要です。

- Microsoft SQL Server

- [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database/) (Windows 上の SAP BOBI プラットフォームに対してのみサポートされるデータベース)

  最新の安定した SQL Server Enterprise Edition に基づいた、フル マネージドの SQL Server データベース エンジンです。 Azure SQL Database では、アップグレード、修正プログラムの適用、監視などのほとんどのデータベース管理機能がユーザーの介入なしで処理されます。 Azure SQL Database を使用すると、Azure のアプリケーションやソリューションのための高可用性かつ高パフォーマンスのデータ ストレージ層を作成できます。 詳細については、[Azure SQL Database](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md) のドキュメントを確認してください。

- [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql/) (SAP PAM の MySQL AB に対して指定されているのと同じ互換性ガイドラインに従います)

  MySQL Community Edition を基盤としたリレーショナル データベース サービスです。 フルフル マネージドの DBaaS (サービスとしてのデータベース) オファリングであり、予測可能なパフォーマンスと動的なスケーラビリティを実現しながら、ミッション クリティカルなワークロードを処理できます。 高可用性、自動バックアップ、ソフトウェア修正プログラムの適用、自動障害検出、および最大 35 日間のポイントインタイム リストアが組み込まれており、操作タスクが大幅に削減されます。 詳細については、[Azure Database for MySQL](../../../mysql/overview.md) のドキュメントを確認してください。

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle (バージョンと制限については、SAP Note [2039619](https://launchpad.support.sap.com/#/notes/2039619) を確認してください)

- MaxDB

このドキュメントでは、**Windows で Azure SQL Database と共に SAP BOBI プラットフォームを**、および **Linux で Azure Database for MySQL と共に SAP BOBI プラットフォームを** デプロイするためのガイドラインを示します。 これは、Azure で SAP BusinessObjects BI プラットフォームを実行するための推奨される方法でもあります。

## <a name="sizing"></a>サイズ変更

サイズ設定とは、アプリケーションを効率的に実行するためのハードウェア要件を決定するプロセスです。 SAP BOBI プラットフォームの場合、サイズ設定は [Quick Sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer) という SAP サイジング ツールを使用して行う必要があります。 このツールでは、入力に基づいて SAPS が提供されます。それをその後、SAP について認定されている Azure 仮想マシンの種類にマップする必要あります。 SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) では、サポートされる SAP 製品と Azure VM の種類が SAPS と共に一覧で示されています。 サイズ設定の詳細については、[SAP BI のサイズ設定に関するガイド](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons)を確認してください。

SAP BOBI プラットフォームのストレージ ニーズに応じて、Azure ではさまざまな種類の[マネージド ディスク](../../managed-disks-overview.md)が提供されています。 SAP BOBI インストール ディレクトリには、Premium マネージド ディスクを使用することをお勧めします。また、仮想マシン上で実行されるデータベースについては、[SAP ワークロードのための DBMS デプロイ](dbms_guide_general.md)に関するページに記載されているガイダンスに従ってください。

Azure では、SAP BOBI プラットフォームのデータ層向けの DBaaS オファリングを 2 つサポートしています。[Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database) (Windows で実行される BI アプリケーション) と、[Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql) (Linux および Windows で実行される BI アプリケーション) です。 そのため、サイズ設定の結果に基づいて、ニーズに最も適した購入モデルを選択できます。

> [!Tip]
> サイズ設定の簡単な参考として、SAP BOBI プラットフォームのデータベース層の SAPS 結果を Azure のサービスとしてのデータベース (Azure SQL Database または Azure Database for MySQL) にマップするときには、800 SAPS = 1 vCPU と見なしてください。

### <a name="sizing-models-for-azure-sql-database"></a>Azure SQL Database のサイズ設定モデル

Azure SQL Database には、次の 3 つの購入モデルが用意されています。

- 仮想コアベース

  仮想コアの数、メモリの量、およびストレージの容量と速度を選択できます。 また、仮想コアベースの購入モデルでは、[SQL Server 向けの Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を使用して、コストを削減することもできます。 このモデルは、柔軟性、制御、および透明性を重視するお客様に適しています。

  仮想コア モデルで提供されている[サービス レベルのオプション](../../../azure-sql/database/service-tiers-vcore.md#service-tiers)には、General Purpose、Business Critical、および Hyperscale の 3 つがあります。 サービス レベルでは、ストレージ アーキテクチャ、容量、I/O の制限、および可用性とディザスター リカバリーに関連するビジネス継続性のオプションが定義されています。 サービス レベルの各オプションの大まかな詳細は次のとおりです。

  1. **General Purpose** サービス レベルは、ビジネス ワークロードに最適です。 予算重視で、バランスの取れた、スケーラブルなコンピューティングおよびストレージ オプションを提供します。 詳細については、[リソースのオプションと制限](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5)に関するページを参照してください。
  2. **Business Critical** サービス レベルでは、複数の分離されたレプリカを使用して、障害に対する最大の回復性をビジネス アプリケーションに提供し、データベース レプリカあたりの最大の I/O パフォーマンスを実現します。 詳細については、[リソースのオプションと制限](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5)に関するページを参照してください。
  3. **Hyperscale** サービス レベルは、高度にスケーラブルなストレージと読み取りスケールの要件を持つビジネス ワークロードに最適です。 複数の分離されたデータベース レプリカを構成できるようにして、障害に対するより高い回復性を提供します。 詳細については、[リソースのオプションと制限](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)に関するページを参照してください。

- DTU ベース

  DTU ベースの購入モデルでは、データベースの軽量ワークロードと重量ワークロードをサポートするため、コンピューティング リソース、メモリ リソース、I/O リソースの組み合わせが、3 つのサービス レベルで提供されています。 各レベルにおけるコンピューティング サイズでは、これらのリソースのさまざまな組み合わせが提供され、ストレージ リソースを追加することができます。 シンプルな構成済みリソースのオプションを希望するお客様に最適です。

  DTU ベースの購入モデルでの[サービス レベル](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers)は、固定された容量の付属ストレージ、固定されたバックアップ保有期間、および固定された価格を持つさまざまなコンピューティング サイズによって区別されます。

- サーバーレス

  サーバーレス モデルでは、ワークロードの需要に基づいてコンピューティングを自動的にスケーリングし、1 秒あたりのコンピューティング使用量に対して請求を行います。 サーバーレス コンピューティング レベルでは、アイドル期間にデータベースを自動的に一時停止します。このときはストレージのみに課金され、再びアクティブになると自動的にデータベースが再開されます。 詳細については、[リソースのオプションと制限](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)に関するページを参照してください。
  
  長期にわたって平均コンピューティング使用率が低い、間欠的で予測できない使用状況に適しています。 したがって、運用環境以外の SAP BOBI デプロイにはこのモデルを使用できます。

> [!Note]
> SAP BOBI の場合は、仮想コア ベースのモデルを使用し、ビジネス ニーズに応じて General Purpose または Business Critical のどちらかのサービス レベルを使用すると便利です。

### <a name="sizing-models-for-azure-database-for-mysql"></a>Azure Database for MySQL のサイズ設定モデル

Azure Database for MySQL には、3 つの異なる価格レベルが用意されています。 仮想コアでのコンピューティング量、仮想コアあたりのメモリ、およびデータの格納に使用されるストレージ テクノロジによって区別されています。 オプションの大まかな詳細は次のとおりです。さまざまな属性の詳細については、Azure Database for MySQL の[価格レベル](../../../mysql/concepts-pricing-tiers.md)に関するページを参照してください。

- Basic

  低負荷なコンピューティングと I/O パフォーマンスを必要とするターゲット ワークロードに使用されます。

- General Purpose

  バランスの取れたコンピューティングとメモリ、およびスケーラブルな I/O スループットを必要とするほとんどのビジネス ワークロードに適しています。

- メモリ最適化

  高速トランザクション処理と高いコンカレンシーを実現するためのインメモリ パフォーマンスを必要とする、高パフォーマンス データベース ワークロードに適しています。

> [!Note]
> SAP BOBI の場合は、ビジネスワークロードに応じて General Purpose またはメモリ最適化の価格レベルを使用すると便利です。

## <a name="azure-resources"></a>Azure リソース

### <a name="choosing-regions"></a>リージョンの選択

Azure リージョンとは、さまざまな Azure サービスを実行してホストするインフラストラクチャを含めた、1 か所または複数のデータセンターの集合です。 このインフラストラクチャには多数のノードが含まれており、それぞれのノードは、コンピューティング ノードやストレージ ノードとして機能したり、ネットワーク機能を実行したりします。 すべてのリージョンで同じサービスが提供されるわけではありません。

SAP BI プラットフォームにはさまざまなコンポーネントが含まれており、それらに必要な特定の VM の種類、ストレージ (Azure Files、Azure NetApp Files など)、またはそのデータ層用の DBaaS (サービスとしてのデータベース) が、特定のリージョンでは利用できない場合もあります。 VM の種類、Azure Storage の種類、またはその他の Azure サービスについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/en-us/global-infrastructure/services/)」のサイトで正確な情報を確認できます。 Azure で SAP システムを既に実行している場合、リージョンはおそらく既に特定されています。 その場合は、SAP BI プラットフォームのアーキテクチャを決定するために、必要なサービスがこれらのリージョンで利用可能であることを最初に調査する必要があります。

### <a name="availability-zones"></a>可用性ゾーン

可用性ゾーンは、Azure リージョン内の物理的に分離された場所です。 それぞれの可用性ゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。

SAP BI プラットフォームの各層で高可用性を実現するには、高可用性フレームワークを実装することによって可用性ゾーン全体に VM を分散させることができます。これにより、Azure で最高の SLA を実現できます。 Azure の仮想マシンの SLA については、[仮想マシンの SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)の最新バージョンを参照してください。

データ層に対しては、Azure DBaaS (サービスとしてのデータベース) サービスによって既定で高可用性フレームワークが提供されます。 ユーザーは、計画的な停止や計画外の停止によるデータベースのダウンタイムを軽減するための高可用性、冗長性、回復性の各機能が最初から備わっているリージョンとサービスを選択するだけで済み、追加のコンポーネントを構成する必要がありません。 Azure でサポートされている DBaaS オファリングの SLA の詳細については、[Azure Database for MySQL の高可用性](../../../mysql/concepts-high-availability.md)および [Azure SQL Database の高可用性](../../../azure-sql/database/high-availability-sla.md)に関するページを参照してください。

### <a name="availability-sets"></a>可用性セット

可用性セットは、デプロイされる仮想マシン (VM) リソースを相互に分離するための論理的なグループ化機能です。 Azure では、可用性セット内に配置された VM は、複数の物理サーバー、コンピューティング ラック、ストレージ ユニット、およびネットワーク スイッチ間で実行されます。 ハードウェアまたはソフトウェアの障害が発生した場合、影響を受けるのは VM のサブセットだけであり、ソリューション全体は動作状態を維持します。 したがって、仮想マシンが可用性セットに配置されている場合、VM は Azure ファブリック コントローラーによってさまざまな[障害](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains)ドメインと[アップグレード](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains) ドメインに分散されるため、1 つの障害ドメイン内で発生した障害やインフラストラクチャのメンテナンスが原因ですべての VM がアクセス不可になることが防止されます。

SAP BI プラットフォームにはさまざまなコンポーネントが含まれており、アーキテクチャを設計する際には、このコンポーネントのそれぞれが中断から確実に回復できるようにする必要があります。 これは、各コンポーネントの Azure 仮想マシンを可用性セット内に配置することによって実現できます。 異なる VM ファミリの VM を 1 つの可用性セット内で混在させる場合、特定の種類の VM をその可用性セットに含めることができないという問題が発生する可能性がある点に留意してください。 そのため、「アーキテクチャの概要」で説明されているように、Web アプリケーション用、SAP BI プラットフォームでは BI アプリケーション用に、独立した可用性セットを作成してください。

また、Azure スケール ユニット内の Azure 可用性セットで使用できる更新ドメインと障害ドメインの数に制限はありません。 そのため、1 つの可用性セットに VM を追加し続けると、2 つ以上の VM が最終的に同じ障害ドメインまたは更新ドメインに含まれることになります。 詳しくは、SAP のための Azure 仮想マシンの計画と実装に関するドキュメントの「[Azure の可用性セット](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets)」のセクションを参照してください。

Azure 可用性セットの概念と、可用性セットが障害ドメインやアップグレード ドメインとどう関連しているかを理解するには、[可用性の管理](../../availability.md)に関する記事を参照してください。

> [!Important]
> Azure Availability Zones と Azure 可用性セットの概念は、相互に排他的です。 つまり、2つまたはそれ以上の VM を、特定の可用性ゾーンまたは Azure 可用性セットのどちらか一方にデプロイできますが、 両方にすることはできません。

### <a name="virtual-machines"></a>仮想マシン

Azure 仮想マシンは、カスタムイメージを IaaS (サービスとしてのインフラストラクチャ) インスタンスとして Azure にデプロイできるサービス オファリングです。 Azure プラットフォームでは、Web アプリケーションと接続型アプリケーションをホスト、拡張、および管理するためのコンピューティング リソースとストレージ をオンデマンドで利用できるので、アプリケーションの保守と運用を簡素化できます。

Azure では、アプリケーションのニーズに合わせてさまざまな仮想マシンを提供しています。 ただし、SAP ワークロードに関しては、Azure での選択肢は、SAP ワークロード、より具体的には SAP HANA ワークロードに適したいくつかの VM ファミリに限定されています。 詳細については、「[Azure デプロイでサポートされている SAP ソフトウェア](sap-supported-product-on-azure.md)」を参照してください。

SAP BI プラットフォームのサイズ設定に基づいて、Azure for SAP 製品でサポートされている Azure 仮想マシンに要件をマップする必要があります。 最初に SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) を参照することをお勧めします。ここには、Windows および Linux で SAP 製品に対してサポートされている Azure VM の種類が一覧で示されています。 また、サポートされている VM の種類の選択肢だけでなく、それらの VM の種類が特定のリージョンで使用できるかどうかを確認する必要がある点にも留意してください。 使用できる VM の種類は、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」のページで確認できます。 価格モデルの選択については、「[SAP ワークロード用の Azure 仮想マシン](planning-guide.md#azure-virtual-machines-for-sap-workload)」を参照してください

### <a name="storage"></a>ストレージ

Azure Storage は、高い可用性とセキュリティ、耐久性、スケーラビリティ、冗長性を備えたストレージを提供する、Azure が管理するクラウド サービスです。 ストレージの種類の中には、SAP シナリオでの使用が制限されているものがあります。 しかし、いくつかの Azure Storage の種類が、特定の SAP ワークロードのシナリオ用として適切であるか、または最適化されています。 詳細については、[SAP ワークロードの Azure Storage の種類](planning-guide-storage.md)に関するガイドを参照してください。SAP に適したさまざまなストレージ オプションの概要が説明されています。

Azure Storage には、お客様が使用できるさまざまなストレージの種類があります。その詳細については、[Azure で利用できるディスクの種類](../../disks-types.md)に関する記事を参照してください。 SAP BOBI プラットフォームでは、次の Azure Storage を使用してアプリケーションをビルドします。

- Azure マネージド ディスク

  Azure によって管理されるブロック レベルのストレージ ボリュームです。 このディスクは、Azure 仮想マシンにインストールされる場合、SAP BOBI プラットフォームのアプリケーション サーバーおよびデータベース用に使用できます。 さまざまな種類の [Azure マネージド ディスク](../../managed-disks-overview.md)が利用可能ですが、SAP BOBI プラットフォームのアプリケーションとデータベースには [Premium SSD](../../disks-types.md#premium-ssd) を使用することをお勧めします。

  次の例では、BOBI プラットフォームのインストール ディレクトリに Premium SSD が使用されています。 仮想マシンにインストールされているデータベースの場合、ガイドラインに従って、データとログのボリュームにマネージド ディスクを使用できます。 通常、CMS および監査データベースは小さく、そのストレージ パフォーマンス要件は他の SAP OLTP/OLAP データベースのものとは異なります。

- Azure Premium Files または Azure NetApp Files

  SAP BOBI プラットフォームでは、ファイル リポジトリ サーバー (FRS) とは、レポート、ユニバース、接続などのコンテンツが格納されている、そのシステムのすべてのアプリケーション サーバーによって使用されるディスク ディレクトリを指します。 [Azure Premium Files](../../../storage/files/storage-files-introduction.md) または [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) ストレージは、SAP BOBI アプリケーションの FRS 用共有ファイル システムとして使用できます。 このストレージ オファリングはすべてのリージョンで使用できるわけではないため、最新情報については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/en-us/global-infrastructure/services/)」のサイトを参照してください。

  お客様のリージョンでサービスを利用できない場合は、NFS サーバーを作成し、そのファイル システムを SAP BOBI アプリケーションと共有できます。 ただし、高可用性を考慮する必要もあります。

![Azure の SAP BusinessObjects BI プラットフォームのストレージ レイアウト](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>ネットワーク

SAP BOBI は、ビジネス データを保持しない、レポートと分析の BI プラットフォームです。 そのため、システムは他のデータベース サーバーに接続し、そこからすべてのデータを取り込んで、ユーザーに分析情報を提供します。 Azure では、オンプレミス システムへの接続や、別の仮想ネットワーク内のシステムへの接続など、SAP BI プラットフォームで実現できるすべてのシナリオのマッピングを可能にするネットワーク インフラストラクチャが提供されます。 詳細については、[SAP ワークロード用の Microsoft Azure ネットワーク](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking)に関するページを参照してください。

DBaaS (サービスとしてのデータベース) オファリングの場合、新しく作成されたデータベース (Azure SQL Database または Azure Database for MySQL) には、すべての外部接続をブロックするファイアウォールがあります。 BI プラットフォームの仮想マシンから DBaaS サービスへのアクセスを許可するには、1 つまたは複数のサーバー レベルのファイアウォール規則を指定して、DBaaS サーバーへのアクセスを有効にする必要があります。 詳細については、Azure Database for MySQL の[ファイアウォール規則](../../../mysql/concepts-firewall-rules.md)に関するセクション、および Azure SQL Database の[ネットワーク アクセス制御](../../../azure-sql/database/network-access-controls-overview.md)に関するセクションを参照してください。

## <a name="next-steps"></a>次の手順

- [Linux での SAP BusinessObjects BI プラットフォームのデプロイ](businessobjects-deployment-guide-linux.md)
- [SAP のための Azure Virtual Machines の計画と実装](planning-guide.md)
- [SAP のための Azure Virtual Machines のデプロイ](deployment-guide.md)
- [SAP のための Azure Virtual Machines DBMS のデプロイ](./dbms_guide_general.md)