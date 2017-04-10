---
title: "SQL Database (PaaS) とクラウドの VM 上の SQL Server (IaaS) | Microsoft Docs"
description: "Azure SQL Database (PaaS) とクラウドの Azure Virtual Machines 上の SQL Server のどちらのクラウド SQL Server オプションがお客様のアプリケーションに適合するかを説明します。"
services: sql-database, virtual-machines
keywords: "SQL Server クラウド、クラウド内の SQL Server、PaaS データベース、クラウド SQL Server、DBaaS"
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: cjgronlund
ms.assetid: 7467f422-b77d-4b60-9cb5-0f1ec17ec565
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 28edde3e70bca833d888cdf9831c1544d8cdd4bb
ms.lasthandoff: 03/31/2017


---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>クラウド SQL Server オプションの選択: Azure SQL (PaaS) Database または Azure VM (IaaS) の SQL Server
Azure には、Microsoft Azure で SQL Server ワークロードをホストするためのオプションが 2 つあります。

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): クラウドにネイティブに対応した SQL データベースで、サービスとしてのプラットフォーム (PaaS) データベースまたはサービスとしてのデータベース (DBaaS) とも呼ばれます。サービスとしてのソフトウェア (SaaS) アプリの開発用に最適化されています。 SQL Server の機能の大部分と互換性があります。 PaaS の詳細については、「[PaaS とは](https://azure.microsoft.com/overview/what-is-paas/)」を参照してください。
* [Azure Virtual Machines 上の SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/): Azure で実行される Windows Server 仮想マシン (VM) にインストールされてホストされているクラウド上の SQL Server で、サービスとしてのインフラストラクチャ (IaaS) とも呼ばれます。
  Azure 仮想マシン上の SQL Server は、既存の SQL Server アプリケーションを移行するために最適化されています。 SQL Server のすべてのバージョンとエディションが利用できます。 SQL Server と 100% の互換性があるため、必要な数のデータベースをホストし、データベース間のトランザクションを実行できます。 また、SQL Server と Windows を完全に制御できます。

この記事では、各オプションがマイクロソフトのデータ プラットフォームにどのように組み込まれているかを説明します。お客様のビジネス要件に適したオプションを判断するのにお役立てください。 この記事を参照することで、コスト削減と管理の最小化のいずれを優先するかに関係なく、最大の関心事項であるビジネス要件に適したアプローチを判断できます。

## <a name="microsofts-data-platform"></a>マイクロソフトのデータ プラットフォーム
Azure とオンプレミス SQL Server データベースの話を始める前にまず、それらがすべてが利用可能であるということを理解しておいてください。 Microsoft のデータ プラットフォームは、SQL Server テクノロジを活用し、物理的なオンプレミスマシン、プライベート クラウド環境、サード パーティにホストされているプライベート クラウド環境、パブリック クラウドのすべてでそのテクノロジを使用できるようにします。 Azure 仮想マシン上の SQL Server によって、環境全体でサーバー製品、開発ツール、専門知識を同じ組み合わせで使用しながら、オンプレミスとクラウド ホスト型のデプロイを組み合わせることで、多様な独自のビジネス ニーズを満たすことができます。

   ![クラウド SQL Server オプション: IaaS 上の SQL Server またはクラウド内の SaaS SQL Database](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

図に示すように、各製品は、インフラストラクチャに及ぼす管理のレベル (X 軸) と、データベース レベルの統合と自動化で実現するコスト効率の度合い (Y 軸) によって特徴付けることができます。

アプリケーションの設計時には、アプリケーションの SQL Server の部分をホストするために 4 つの基本的なオプションを使用できます。

* 仮想化されていない物理マシン上の SQL Server
* オンプレミス仮想マシン内の SQL Server (プライベート クラウド)
* Azure 仮想マシン内の SQL Server (Microsoft パブリック クラウド)
* Azure SQL Database (Microsoft パブリック クラウド)

次のセクションでは、Microsoft パブリック クラウドの SQL Server (Azure SQL Database と Azure VM 上の SQL Server) について説明します。 さらに、アプリケーションにとって最適なオプションを決定する一般的なビジネスの要因について確認します。

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Azure SQL Database と Azure VM 上の SQL Server の詳細
**Azure SQL Database** は、Azure クラウドでホストされる、サービスとしてのリレーショナル データベース (DBaaS) で、業界内のカテゴリとしては "*サービスとしてのソフトウェア (SaaS)*" または "*サービスとしてのプラットフォーム (PaaS)*" に分類されます。 [SQL Database](sql-database-technical-overview.md) は、Microsoft が所有、ホスト、保守する標準的なハードウェアおよびソフトウェアをベースにして構築されています。 SQL Database では、組み込みの機能を使用して、サービス上で直接開発を行えます。 SQL Database は従量課金制で利用でき、中断することなくスケールアップまたはスケールアウトして強化できるオプションもあります。

**Azure Virtual Machines (VM) 上の SQL Server** は、業界内のカテゴリとしては *サービスとしてのインフラストラクチャ (IaaS)* に分類され、クラウド上の仮想マシン内で SQL Server を実行できます。 SQL Database と同様に、マイクロソフトが所有、ホスト、保守する標準的なハードウェア上に構築されています。 VM 上の SQL Server を使用する場合、SQL Server イメージに含まれている SQL Server ライセンスを使用して従量課金で支払いを行うか、単に既存のライセンスを使用することができます。 また、VM のスケールアップ、スケールダウン、一時停止、再開を必要に応じて簡単に行うことができます。

概して、目的別に最適化された次の 2 つの SQL オプションがあります。

* **Azure SQL Database** は、多数のデータベースをプロビジョニングして管理するためのコスト全体を最小限に抑えるように最適化されています。 仮想マシン、オペレーティング システム、データベース ソフトウェアを管理する必要がないため、継続的な管理コストが軽減されます。 アップグレード、高可用性、 [バックアップ](sql-database-automated-backups.md)を管理する必要はありません。 一般的には、Azure SQL Database を使用すると、単一の IT または開発リソースが管理するデータベースの数を大幅に増やすことができます。
* **Azure VM 上で実行されている SQL Server** は、既存のアプリケーションを Azure に移行したり、ハイブリッド デプロイで既存のオンプレミス アプリケーションをクラウドに拡張したりできるように最適化されています。 さらに、仮想マシン上の SQL Server は従来の SQL Server アプリケーションを開発、テストするためにも使用できます。 Azure VM 上の SQL Server では、専用の SQL Server インスタンスとクラウド ベースの VM に対して完全な管理者権限があります。 仮想マシンの維持に使用できる IT リソースが組織に既にある場合は、Azure VM 内の SQL Server を選択することをお勧めします。 これらの機能を使用すると、高度にカスタマイズされたシステムを構築して、アプリケーション固有のパフォーマンスや可用性の要件に対処できます。

次の表に SQL Database と Azure VM 上の SQL Server の主な特徴をまとめます。

| **最適な用途:** | **Azure SQL Database** | **Azure の仮想マシン内の SQL Server** |
| --- | --- | --- |
|  |開発とマーケティングに時間的制約があるクラウド設計の新しいアプリケーション。 |変更を最小限に抑えてクラウドに迅速に移行する必要がある既存のアプリケーション。 運用環境以外のオンプレミス SQL Server のハードウェアを購入しない場合の迅速な開発およびテスト シナリオ。 |
|  | 高可用性、障害復旧、アップグレードが組み込まれたデータベースを必要とするチーム。 |SQL Server の高可用性、障害復旧、修正プログラムの適用を構成して管理できるチーム。 自動化された機能がいくつか用意されているため、構成と管理が劇的に簡素化されます。 | |
|  | 基盤のオペレーティング システムと構成設定の管理を回避したいチーム。 |完全な管理者アクセス権がある、カスタマイズされた環境を必要とする場合。 | |
|  | 1 TB までのデータベース、またはスケールアウト パターンを使用して [水平または垂直方向にパーティション分割](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) できる、より大きなデータベース。 |記憶域が 64 TB までの SQL Server インスタンス。 インスタンスでは必要な数のデータベースがサポートされます。 | |
|  | [サービスとしてのソフトウェア (SaaS) アプリケーションの構築](sql-database-design-patterns-multi-tenancy-saas-applications.md)。 |エンタープライズ アプリケーションとハイブリッド アプリケーションの移行と構築。 | |
|  | | |
| **リソース:** |基盤となるインフラストラクチャの構成と管理のために IT リソースを使用するつもりがなく、アプリケーション層に的を絞りたい。 |構成と管理のための IT リソースが存在する。 自動化された機能がいくつか用意されているため、構成と管理が劇的に簡素化されます。 |
| **総保有コスト:** |ハードウェアのコストを削減し、管理コストを削減します。 |ハードウェアのコストを削減します。 |
| **ビジネス継続性:** |Azure SQL Database は、組み込みのフォールト トレランスのインフラストラクチャ機能以外にも、ビジネス継続性を向上させるために、[自動バックアップ](sql-database-automated-backups.md)、[ポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)、[geo リストア](sql-database-recovery-using-backups.md#geo-restore)、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)などの機能を提供します。 詳細については、 [SQL Database のビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。 |Azure VM 上の SQL Server では、データベース固有のニーズに合わせて、高可用性と障害復旧のソリューションを設定できます。 そのため、アプリケーション向けにシステムを大幅に最適化できます。 必要なときに自分でフェールオーバーをテストして実行できます。 詳細については、「 [Azure Virtual Machines における SQL Server の高可用性と障害復旧](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md)」を参照してください。 |
| **ハイブリッド クラウド:** |オンプレミス アプリケーションから Azure SQL Database 内のデータにアクセスできます。 |Azure VM 上の SQL Server を使用して、アプリケーションの一部をクラウド上で実行し、一部をオンプレミスで実行することができます。 たとえば、 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)を介してオンプレミス ネットワークと Active Directory ドメインをクラウドに拡張できます。 さらに、 [Azure の SQL Server データ ファイル](http://msdn.microsoft.com/library/dn385720.aspx)を使用して、Azure Storage にオンプレミスのデータ ファイルを格納することができます。 詳細については、「 [SQL Server 2014 ハイブリッド クラウドの概要](http://msdn.microsoft.com/library/dn606154.aspx)」を参照してください。 |
|  | サブスクライバーとして、データをレプリケートするための [SQL Server のトランザクション レプリケーション](https://msdn.microsoft.com/library/mt589530.aspx) がサポートされます。 |データをレプリケートするための [SQL Server のトランザクション レプリケーション](https://msdn.microsoft.com/library/mt589530.aspx)、[AlwaysOn 可用性グループ](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md)、Integration Services、ログ配布が完全にサポートされます。 また、従来の SQL Server バックアップも完全にサポートされます。 | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Azure SQL Database と Azure VM 上の SQL Server のいずれかを選択するときのビジネスの要因
### <a name="cost"></a>コスト
現金が潤沢にないスタートアップ企業や厳しい予算で経営している老舗企業では、資金調達での限界がデータベースをホストする方法を決定する主な要因になることがあります。 このセクションではまず、SQL Database と Azure VM 上の SQL Server の 2 つのリレーショナル データベース オプションに関する Azure の課金とライセンスの基礎について説明します。 また、アプリケーションの総コストの計算についても説明します。

#### <a name="billing-and-licensing-basics"></a>課金とライセンスの基礎
**SQL Database** は、ライセンスではなくサービスとしてお客様に販売されます。  [Azure VM 上の SQL Server](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) は、分単位で支払いを行う付属のライセンスと共に販売されます。 既存のライセンスをお持ちの場合、それを使用することもできます。  

現在、 **SQL Database** は複数のサービス階層で利用でき、選択したサービス階層とパフォーマンス レベルに基づく固定率で時間単位で課金されます。 さらに、インターネット トラフィックの送信も通常の [データ転送料金](https://azure.microsoft.com/pricing/details/data-transfers/)で課金されます。 Basic、Standard、および Premium のサービス階層は、アプリケーションのピーク時の要件を満たすために、複数のパフォーマンス レベルで、予測できるパフォーマンスを実現するように設計されています。 サービス階層とパフォーマンス レベルを変更して、アプリケーションのさまざまなスループット ニーズを満たすことができます。 データベースのトランザクション量が膨大で、多数の同時ユーザーをサポートする必要がある場合は、Premium サービス階層をお勧めします。 現在サポートされているサービス階層の最新情報については、 [Azure SQL Database のサービス階層](sql-database-service-tiers.md)に関するページを参照してください。 [エラスティック プール](sql-database-elastic-pool.md)を作成して、データベース インスタンス間でパフォーマンス リソースを共有することもできます。

**SQL Database**では、データベース ソフトウェアの自動的な構成、修正プログラムの適用、およびアップグレードがマイクロソフトによって行われるため、管理コストが軽減されます。 また、 [組み込みのバックアップ](sql-database-automated-backups.md) 機能は、特に、多数のデータベースがある場合の大幅なコスト削減に役立ちます。

**Azure VM 上の SQL Server**では、プラットフォームによって提供される、いずれかの SQL Server イメージ (ライセンスが付属) を使用するか、所有している SQL Server ライセンスを移行することができます。 サポートされている SQL Server のバージョン (2008R2、2012、2014、2016) とエディション (Developer、Express、Web、Standard、Enterprise) はすべて利用できます。 さらに、ライセンス持ち込み (BYOL) バージョンのイメージも利用できます。 Azure によって提供されるイメージを使用する場合、運用コストは、VM のサイズと選択する SQL Server のエディションによって決まります。 VM のサイズや SQL Server のエディションにかかわらず、ユーザーは、SQL Server および Windows Server の 1 分あたりのライセンス費用と VM ディスクの Azure Storage コストを支払います。 1 分あたりの課金のオプションでは、SQL Server の追加ライセンスを購入することなく、必要なだけ SQL Server を使用することができます。 Azure に SQL Server のライセンスを移行する場合は、Windows Server コストとストレージ コストのみに対して課金されます。 現在所有しているライセンスの移行の詳細については、「 [Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)」を参照してください。

#### <a name="calculating-the-total-application-cost"></a>アプリケーションの総コストの計算
クラウド プラットフォームの使用を開始する際のアプリケーションのランニング コストには、開発コストと管理コストのほか、パブリック クラウド プラットフォームのサービス コストが含まれます。

SQL Database と Azure VM 上の SQL Server で実行されるアプリケーションの詳細なコストの計算方法を次に示します。

**Azure SQL Database を使用する場合:**

*アプリケーションの総コスト = 大幅に最小化される管理コスト + ソフトウェア開発コスト + SQL Database サービス コスト*

**Azure VM 上の SQL Server を使用する場合:**

*アプリケーションの総コスト = 大幅に削減されるソフトウェア開発コスト + 管理コスト + SQL Server と Windows Server のライセンス コスト + Azure Storage のコスト*

価格の詳細については、次のリソースを参照してください。

* [SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)
* [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) および [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows) 向けの [Virtual Machines の価格](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

> [!NOTE]
> SQL Server の機能には、SQL Database に適用できないものや SQL Database で利用できないものがいくつかあります。 詳細については、[SQL Database の機能](sql-database-features.md)に関する記事と [SQL Database Transact-SQL 情報](sql-database-transact-sql-information.md)に関する記事を参照してください。 既存の SQL Server ソリューションをクラウドに移行する場合は、「 [SQL Server データベースの Azure SQL Database への移行](sql-database-cloud-migrate.md)」を参照してください。 既存のオンプレミス SQL Server アプリケーションを SQL Database に移行する場合は、クラウド サービスで提供されている機能を活用できるようにアプリケーションを更新することを検討してください。 たとえば、[Azure Web Apps サービス](https://azure.microsoft.com/services/app-service/web/)または [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) を使用してアプリケーション層をホストすると、コスト面での利点が大きくなります。
> 
> 

### <a name="administration"></a>管理
多くの企業にとって、クラウド サービスに切り替えるかどうかの決定には、コストだけでなく、管理の複雑さの軽減も重要な要素です。 **SQL Database**では、基盤となるハードウェアは Microsoft によって管理されます。 高可用性を実現するための全データのレプリケート、データベース ソフトウェアの構成とアップグレード、負荷分散の管理、サーバーに障害が発生した場合の透過的なフェールオーバーは、Microsoft によって自動的に実行されます。 ユーザーは引き続きデータベースを管理できますが、データベース エンジン、サーバー オペレーティング システム、ハードウェアを管理する必要はありません。  引き続き管理できるアイテムの例として、データベースとログイン、インデックスとクエリのチューニング、監査とセキュリティなどがあります。

**Azure VM 上の SQL Server**では、オペレーティング システムと SQL Server インスタンスの構成を全面的に制御できます。 VM を使用する場合、オペレーティング システムとデータベース ソフトウェアの更新やアップグレードのタイミングに加えて、ウイルス対策などの追加ソフトウェアのインストールのタイミングは、ユーザーが決定します。 修正プログラムの適用、バックアップ、高可用性の実現を大幅に簡素化するために、自動化された機能がいくつか用意されています。 また、VM のサイズ、ディスクの数、ストレージの構成を制御できます。 Azure では、必要に応じて VM のサイズを変更できます。 詳細については、「 [Azure の仮想マシンおよびクラウド サービスのサイズ](../virtual-machines/windows/sizes.md)」を参照してください。 

### <a name="service-level-agreement-sla"></a>サービス レベル アグリーメント (SLA)
多くの IT 部門では、サービス レベル アグリーメント (SLA) の稼働時間に関する義務を遂行することは、最優先事項です。 このセクションでは、各データベースのホスト オプションに適用される SLA について説明します。

**SQL Database** では、Basic、Standard、および Premium サービス階層で、可用性 99.99% の SLA が提供されます。 最新情報については、 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/sql-database/)に関するページを参照してください。 SQL Database サービス階層と、サポートされているビジネス継続性計画の最新情報については、 [サービス階層](sql-database-service-tiers.md)に関するページを参照してください。

**Azure VM 上で実行される SQL Server**では、仮想マシンのみを対象に、可用性 99.95% の SLA が提供されます。 この SLA は VM 上で実行される (SQL Server などの) プロセスは対象としておらず、可用性セットに少なくとも 2 つの VM インスタンスをホストしている必要があります。 最新情報については、 [VM の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)に関するページを参照してください。 VM 内のデータベース高可用性 (HA) を実現するには、 [AlwaysOn 可用性グループ](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)など、SQL Server でサポートされているいずれかの高可用性オプションを構成する必要があります。 サポートされている高可用性オプションを使用した場合、SLA を追加することはできないものの、ほぼ 99.99% のデータベース可用性が実現されます。

### <a name="market"></a>製品化に要する時間
**SQL Database** は、開発者の生産性と製品化に要する時間の短縮が重要な、クラウド用に設計されたアプリケーションに最適なソリューションです。 プログラムによる DBA のような機能を備えることで、基になるオペレーティング システムとデータベースを管理する必要性が減少するため、クラウドの設計者と開発者に最適です。 たとえば、[REST API](http://msdn.microsoft.com/library/azure/dn505719.aspx) と [PowerShell コマンドレット](http://msdn.microsoft.com/library/mt740629.aspx)を使用して、数千のデータベースの管理の操作を自動化して管理できます。 [エラスティック プール](sql-database-elastic-pool.md)などの機能を使用すると、アプリケーション層に注目し、ソリューションをより早く市場に提供することができます。

**Azure VM 上で実行される SQL Server** は、既存または新規のアプリケーションに、大規模なデータベース、相互に関連するデータベース、SQL Server または Windows の全機能へのアクセスのいずれかが必要な場合に最適です。 また、既存のオンプレミス アプリケーションとデータベースを Azure にそのまま移行する場合にも適しています。 プレゼンテーション層、アプリケーション層、およびデータ層を変更する必要がないため、既存のソリューションを再設計する時間と予算が節約されます。 その一方で、すべてのソリューションを Azure に移行し、Azure プラットフォームで必要な一部のパフォーマンスの最適化に集中できます。 詳細については、 [Azure Virtual Machines 上の SQL Server のパフォーマンスに関するベスト プラクティス](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)に関するページを参照してください。

## <a name="summary"></a>概要
この記事では、SQL Database と Azure Virtual Machines (VM) 上の SQL Server について詳しく紹介し、意思決定に影響する可能性のある一般的なビジネス要因について説明しました。 以下は、検討に役立つ提案をまとめたものです。

次の状況に該当する場合は、 **Azure SQL Database** を選択します。

* クラウド サービスによって実現されるコスト削減やパフォーマンス最適化を活用するために、クラウドベースの新しいアプリケーションを構築しようとしている。 このアプローチでは、完全に管理されたクラウド サービスの利点を活かして、最初の市場投入までの時間を短縮し、長期的なコスト最適化を実現できます。
* マイクロソフトのサービスを利用して、データベースで一般的な管理操作を実行し、データベースの可用性の SLA を強化する場合。

次の状況に該当する場合は、 **Azure VM 上の SQL Server** を選択します。

* クラウドに移行または拡張したい既存のオンプレミス アプリケーションがある場合、または 1 TB を超えるエンタープライズ アプリケーションを構築したい場合。 このアプローチでは、100% の SQL 互換性、大規模なデータベース容量、SQL Server と Windows に対する完全な制御、オンプレミスへの安全なトンネリングの利点を活かすことができます。 このアプローチを使用すると、既存のアプリケーションの開発と修正にかかるコストを最小限に抑えられます。
* 既存の IT リソースがあり、最終的には修正プログラムの適用、バックアップ、データベースの高可用性の実現を自身で行える場合。 いくつかの自動化された機能によって、これらの操作を大幅に簡素化できることにご注目ください。 

## <a name="next-steps"></a>次のステップ
* SQL Database の使用を開始するには、「[初めての Azure SQL Database](sql-database-get-started.md)」を参照してください。
* 「 [SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」を参照してください。
* Azure VM での SQL Server の基本的な使い方については、 [Azure での SQL Server 仮想マシンのプロビジョニング](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) に関する記事を参照してください。


