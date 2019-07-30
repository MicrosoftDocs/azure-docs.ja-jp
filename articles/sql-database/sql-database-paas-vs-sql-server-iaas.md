---
title: Azure の SQL 選択肢からの選択 | Microsoft Docs
description: Azure SQL Database 内および Azure SQL Database と Azure Virtual Machines 上の SQL Server 間のデプロイ オプションから選択する方法について説明します
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server クラウド、クラウド内の SQL Server、PaaS データベース、クラウド SQL Server、DBaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/11/2019
ms.openlocfilehash: 9e95569ba3fe65ea5bce7d6a95a24324235e9a7f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447749"
---
# <a name="choose-the-right-sql-server-option-in-azure"></a>Azure で適切な SQL Server オプションを選択する

Azure では、ホストされるインフラストラクチャ (IaaS) 内で、またはホストされるサービス ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) として、SQL Server のワークロードを実行することができます。 PaaS 内には、複数のデプロイ オプションと各デプロイ オプション内にサービス レベルがあります。 PaaS か IaaS かを決定するときに尋ねる必要がある重要な質問は、データベースの管理、修正プログラムの適用、バックアップの実行を自分で行うか、これらの操作を Azure に委任するかということです。
答えに応じて、次のオプションがあります。

- [Azure SQL Database](sql-database-technical-overview.md):最新の安定した SQL Server Enterprise Edition に基づくフルマネージドの SQL データベース エンジンです。 これは Azure クラウドにホストされる、サービスとしてのリレーショナル データベース (DBaaS) で、業界内のカテゴリとしては "*サービスとしてのプラットフォーム (PaaS)* " に分類されます。 SQL Database には複数のデプロイ オプションがあり、各オプションは、Microsoft が所有、ホスト、保守する標準的なハードウェアおよびソフトウェアをベースにして構築されています。 SQL Database では、SQL Server (オンプレミスまたは Azure Virtual Machine 上の) で使用した場合に多くの構成が必要な組み込みの機能を使用できます。 SQL Database は従量課金制で利用でき、中断することなくスケールアップまたはスケールアウトして強化できるオプションもあります。 SQL Database には、組み込みの高可用性、インテリジェンス、管理など、SQL Server では使用できない追加の機能があります。 Azure SQL Database には、次のデプロイの選択肢があります。
  
  - [単一データベース](sql-database-single-database.md)としてデプロイし、それ専用の各種リソースを SQL Database サーバーを介して管理する。 単一データベースは SQL Server の[包含データベース](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)に似ています。 このオプションは、クラウド生まれの新しいアプリケーションの最新のアプリケーション開発用に最適化されています。
  - データベースのコレクションである[エラスティック プール](sql-database-elastic-pool.md)。各種の共有リソースを SQL Database サーバーを介して管理します。 単一データベースはエラスティック プールの内外に移動できます。 このオプションは、マルチテナント SaaS アプリケ―ションを使用して、クラウド生まれの新しいアプリケーションの最新のアプリケーション開発用に最適化されています。
  - 各種の共有リソースを格納するシステムおよびユーザー データベースのコレクションである[マネージド インスタンス](sql-database-managed-instance.md)。 マネージド インスタンスは、[Microsoft SQL Server データベース エンジン] のインスタンスに似ており、データベース用の共有リソースおよびインスタンス スコープの追加機能を提供します。 マネージド インスタンスでは、データベースの最小限の変更または変更なしでのオンプレミスからのデータベースの移行がサポートされます。 このオプションは Azure SQL Database の PaaS のすべての利点を提供しますが、以前は SQL VM でのみ使用できた機能を追加します。 これには、ネイティブ仮想ネットワーク (VNet) およびオンプレミス SQL Server との 100% に近い互換性が含まれます。
- [Azure Virtual Machines 上の SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) は、業界内のカテゴリとしては "*サービスとしてのインフラストラクチャ (IaaS)* " に分類され、Azure クラウド上のフルマネージドの仮想マシン内で SQL Server を実行できます。 [SQL Server 仮想マシン](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)は、マイクロソフトが所有、ホスト、保守する標準的なハードウェア上でも実行されます。 VM 上の SQL Server を使用する場合、SQL Server イメージに含まれている SQL Server ライセンスを使用して従量課金で支払いを行うか、単に既存のライセンスを使用することができます。 必要に応じて、VM を停止または再開することもできます。Azure で実行される Windows Server または Linux 仮想マシン (VM) にインストールされてホストされているクラウド上の SQL Server で、サービスとしてのインフラストラクチャ (IaaS) とも呼ばれます。 Azure 仮想マシン上の SQL Server は、データベースの変更を行わずにオンプレミスの SQL Server データベースとアプリケーションを移行する場合に適したオプションです。 SQL Server のすべての最新バージョンとエディションは、IaaS 仮想マシンでのインストールに使用できます。 SQL Database からの最も重要な違いは、SQL Server VM が、データベース エンジンを完全に制御できることです。 メンテナンス/修正プログラムの適用を開始するタイミングを選択する、復旧モデルを単純なログまたは一括ログに変更して高速な負荷のないログを有効にする、必要に応じてエンジンを一時停止または開始する、および SQL Server データベース エンジンを完全にカスタマイズすることができます。 この制御を追加する場合、仮想マシンを管理する責任も追加で伴います。

これらのオプションの主な違いを次の表に示します。

| VM 上の SQL Server | SQL Database のマネージド インスタンス | SQL Database の単一データベース/エラスティック プール |
| --- | --- | --- |
|SQL Server エンジンを完全に制御できます。<br/>最大 99.95% の可用性。<br/>オンプレミスの SQL Server の対応するバージョンと完全に一致。<br/>固定で既知のデータベース エンジン バージョン。<br/>オンプレミスの SQL Server からの移行が簡単。<br/>Azure VNet 内のプライベート IP アドレス。<br/>SQL Server が配置されているホスト上にアプリケーションまたはサービスをデプロイできます。| オンプレミスの SQL Server との高い互換性。<br/>99.99% の可用性を保証。<br/>組み込みのバックアップ、修正プログラムの適用、回復。<br/>最新の安定版データベース エンジン バージョン。<br/>SQL Server からの移行が簡単。<br/>Azure VNet 内のプライベート IP アドレス。<br/>組み込みの高度なインテリジェンスとセキュリティ。<br/>オンラインでのリソース (CPU/ストレージ) の変更。|最もよく使用される SQL Server 機能を利用できます。<br/>99.99% の可用性を保証。<br/>組み込みのバックアップ、修正プログラムの適用、回復。<br/>最新の安定版データベース エンジン バージョン。<br/>個々のデータベースに必要なリソース (CPU/ストレージ) を割り当てる機能。<br/>組み込みの高度なインテリジェンスとセキュリティ。<br/>オンラインでのリソース (CPU/ストレージ) の変更。|
|バックアップと修正プログラムを管理する必要があります。<br>高可用性ソリューションを独自に実装する必要があります。<br/>リソース (CPU/ストレージ) の変更中にダウンタイムが発生|利用できない最小限の数の SQL Server 機能がまだあります。<br/>正確なメンテナンス時間の保証はありません (ただし、ほぼ透過的です)。<br/>データベース互換性レベルをのみを使用して、SQL Server バージョンとの互換性を実現できます。|SQL Server からの移行が難しい可能性があります。<br/>一部の SQL Server 機能は使用できません。<br/>正確なメンテナンス時間の保証はありません (ただし、ほぼ透過的です)。<br/>データベース互換性レベルをのみを使用して、SQL Server バージョンとの互換性を実現できます。<br/>プライベート IP アドレスを割り当てることができません (ファイアウォール規則を使用してアクセスを制限できます)。|

この記事では、展開の各選択肢がマイクロソフトのデータ プラットフォームにどのように組み込まれているかを説明します。お客様のビジネス要件に適したオプションを判断するのにお役立てください。 この記事を参照することで、コスト削減と管理の最小化のいずれを優先するかに関係なく、最大の関心事項であるビジネス要件に適したアプローチを判断できます。

## <a name="microsofts-sql-data-platform"></a>マイクロソフトの SQL データ プラットフォーム

Azure とオンプレミス SQL Server データベースの話を始める前にまず、それらがすべてが利用可能であるということを理解しておいてください。 Microsoft のデータ プラットフォームは、SQL Server テクノロジを活用し、物理的なオンプレミスマシン、プライベート クラウド環境、サード パーティにホストされているプライベート クラウド環境、パブリック クラウドのすべてでそのテクノロジを使用できるようにします。 Azure 仮想マシン上の SQL Server によって、環境全体でサーバー製品、開発ツール、専門知識を同じ組み合わせで使用しながら、オンプレミスとクラウド ホスト型のデプロイを組み合わせることで、多様な独自のビジネス ニーズを満たすことができます。

   ![クラウド SQL Server オプション:IaaS 上の SQL Server またはクラウド内の SaaS SQL Database](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

図に示すように、各製品は、インフラストラクチャに及ぼす管理のレベル (X 軸) と、データベース レベルの統合と自動化で実現するコスト効率の度合い (Y 軸) によって特徴付けることができます。

アプリケーションの設計時には、アプリケーションの SQL Server の部分をホストするために 4 つの基本的なオプションを使用できます。

- 仮想化されていない物理マシン上の SQL Server
- オンプレミス仮想マシン内の SQL Server (プライベート クラウド)
- Azure 仮想マシン内の SQL Server (Microsoft パブリック クラウド)
- Azure SQL Database (Microsoft パブリック クラウド)

次のセクションでは、Microsoft パブリック クラウドの SQL Server Azure SQL Database と Azure VM の SQL Server さらに、アプリケーションにとって最適なオプションを決定する一般的なビジネスの要因について確認します。

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Azure SQL Database と Azure VM 上の SQL Server の詳細

概して、目的別に最適化された次の 2 つの SQL オプションがあります。

- **Azure SQL Database**

多数のデータベースをプロビジョニングおよび管理するための管理コスト全体を最小限に抑えるように最適化されています。 仮想マシン、オペレーティング システム、データベース ソフトウェアを管理する必要がないため、継続的な管理コストが軽減されます。 アップグレード、高可用性、 [バックアップ](sql-database-automated-backups.md)を管理する必要はありません。 一般的には、Azure SQL Database を使用すると、単一の IT または開発リソースが管理するデータベースの数を大幅に増やすことができます。 [エラスティック プール](sql-database-elastic-pool.md)は、テナントの分離、データベース間でリソースを共有することでコストを削減するように拡大縮小する機能などの機能を備えた SaaS マルチ テナント アプリケーション アーキテクチャもサポートします。 [マネージド インスタンス](sql-database-managed-instance.md)は、既存のアプリケーションの簡単な移行およびデータベース間でのリソースの共有を可能にするインスタンス スコープの機能のサポートを提供します。

- **Azure VM 上で実行されている SQL Server**

既存のアプリケーションを Azure に移行したり、ハイブリッド デプロイで既存のオンプレミス アプリケーションをクラウドに拡張したりできるように最適化されています。 さらに、仮想マシン上の SQL Server は従来の SQL Server アプリケーションを開発、テストするためにも使用できます。 Azure VM 上の SQL Server では、専用の SQL Server インスタンスとクラウド ベースの VM に対して完全な管理者権限があります。 仮想マシンの維持に使用できる IT リソースが組織に既にある場合は、Azure VM 内の SQL Server を選択することをお勧めします。 これらの機能を使用すると、高度にカスタマイズされたシステムを構築して、アプリケーション固有のパフォーマンスや可用性の要件に対処できます。

次の表に SQL Database と Azure VM 上の SQL Server の主な特徴をまとめます。

| | SQL Database の単一データベースとエラスティック プール | SQL Database Managed Instance |Azure Virtual Machines と SQL Server |
| --- | --- | --- |---|
| **最適な用途:** |最新の安定した SQL Server 機能を使用する必要があり、開発とマーケティングに時間の制約がある新しいクラウド デザイン アプリケーション。 | 最新の新しい SQL Server 機能を使用する必要があり、最小限の変更でクラウドに移行される新しいアプリケーションまたは既存のオンプレミス アプリケーション。  | 変更を最小限に抑えるか、変更なしでクラウドに迅速に移行する必要がある既存のアプリケーション。 運用環境以外のオンプレミス SQL Server のハードウェアを購入しない場合の迅速な開発およびテスト シナリオ。 |
|  | 高可用性、障害復旧、アップグレードが組み込まれたデータベースを必要とするチーム。 | SQL Database の単一およびプールされたデータベースと同じです。 | SQL Server の高可用性、ディザスター リカバリー、修正プログラムの適用を構成、調整、カスタマイズして管理できるチーム。 自動化された機能がいくつか用意されているため、構成と管理が劇的に簡素化されます。 |
|  | 基盤のオペレーティング システムと構成設定の管理を回避したいチーム。 | SQL Database の単一およびプールされたデータベースと同じです。 | 完全な管理者アクセス権がある、カスタマイズされた環境を必要としている。 |
|  | 最大 100 TB のデータベース。 | 最大 8 TB。 | 記憶域が 64 TB までの SQL Server インスタンス。 インスタンスでは必要な数のデータベースがサポートされます。 |
| **互換性** | ほとんどのオンプレミス データベース レベルの機能をサポートしています。 | ほとんどすべてのオンプレミス インスタンス レベルおよびデータベース レベルの機能をサポートしています。 | すべてのオンプレミス機能をサポートしています。 |
| **リソース:** | 基盤となるインフラストラクチャの構成と管理のために IT リソースを使用するつもりがなく、アプリケーション層に的を絞りたい。 | SQL Database の単一およびプールされたデータベースと同じです。 | 構成と管理のための IT リソースが存在する。 自動化された機能がいくつか用意されているため、構成と管理が劇的に簡素化されます。 |
| **総保有コスト:** | ハードウェアのコストを削減し、管理コストを削減します。 | SQL Database の単一およびプールされたデータベースと同じです。 | ハードウェアのコストを削減します。 |
| **ビジネス継続性:** |Azure SQL Database では、[組み込みのフォールト トレランスのインフラストラクチャ機能](sql-database-high-availability.md)以外にも、ビジネス継続性を向上させるために、[自動バックアップ](sql-database-automated-backups.md)、[ポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)、[geo リストア](sql-database-recovery-using-backups.md#geo-restore)、[アクティブ geo レプリケーション](sql-database-active-geo-replication.md)、[自動フェールオーバー グループ](sql-database-auto-failover-group.md)などの機能が提供されます。 詳細については、 [SQL Database のビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。 | SQL Database の単一およびプールされたデータベースと同じですが、ユーザーが開始したコピーのみのバックアップも可能です。 | Azure VM 上の SQL Server では、データベース固有のニーズに合わせて、高可用性とディザスター リカバリーのソリューションを設定できます。 そのため、アプリケーション向けにシステムを大幅に最適化できます。 必要なときに自分でフェールオーバーをテストして実行できます。 詳細については、「 [Azure Virtual Machines における SQL Server の高可用性と障害復旧](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md)」を参照してください。 |
| **ハイブリッド クラウド:** |オンプレミス アプリケーションから Azure SQL Database 内のデータにアクセスできます。 | Azure Express Route または VPN Gateway を使った、オンプレミス環境への[ネイティブ仮想ネットワークの実装](sql-database-managed-instance-vnet-configuration.md)と接続。 | Azure VM 上の SQL Server を使用して、アプリケーションの一部をクラウド上で実行し、一部をオンプレミスで実行することができます。 たとえば、 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)を介してオンプレミス ネットワークと Active Directory ドメインをクラウドに拡張できます。 ハイブリッド クラウド ソリューションについて詳しくは、「[オンプレミス データ ソリューションのクラウドへの拡張](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud)」をご覧ください。 |
|  | サブスクライバーとして、データをレプリケートするための [SQL Server のトランザクション レプリケーション](https://msdn.microsoft.com/library/mt589530.aspx) がサポートされます。 | レプリケーションは、プレビュー機能としてマネージド インスタンスでサポートされています。 | データをレプリケートするための [SQL Server のトランザクション レプリケーション](https://msdn.microsoft.com/library/mt589530.aspx)、[AlwaysOn 可用性グループ](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md)、Integration Services、ログ配布が完全にサポートされます。 また、従来の SQL Server バックアップも完全にサポートされます。 |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Azure SQL Database と Azure VM 上の SQL Server のいずれかを選択するときのビジネスの要因

SQL データベースをホストするために PaaS と IaaS のどちらを選択するかの決定に影響する可能性のある要素がいくつかあります。

- [コスト](#cost) - PaaS と IaaS の両方のオプションに、基になるインフラストラクチャとライセンスの基本価格が含まれます。 ただし、IaaS オプションではデータベースを管理するために追加の時間とリソースを投資する必要がありますが、PaaS ではこれらの管理機能が価格に含まれています。 IaaS オプションでは、使用していないときにリソースをシャットダウンしてコストを削減できますが、PaaS バージョンでは、リソースを削除して必要なときに再作成しない限り常に実行されます。
- [管理](#administration) - PaaS オプションでは、データベースの管理に投入する必要がある時間量が減少します。 ただし、実行できるカスタムの管理タスクおよびスクリプトの範囲も制限されます。 たとえば、CLR は単一またはプールされたデータベースではサポートされませんが、マネージド インスタンスではサポートされています。 また、PaaS のデプロイ オプションでは、トレース フラグの使用がサポートされていません。
- [サービス レベル アグリーメント](#service-level-agreement-sla) - IaaS と PaaS の両方で、高度な業界標準の SLA が提供されます。 PaaS オプションでは 99.99% の SLA が保証されますが、IaaS ではインフラストラクチャについて 99.95% の SLA が保証されます。これは、データベースの可用性を確保するために追加のメカニズムを実装する必要があることを意味します。 極端なケースでは、一致する PaaS である高可用性ソリューションを実装する場合、VM に追加の SQL サーバーを作成し、AlwaysOn Availability グループを構成する必要があり、それによってデータベースのコストが倍増する可能性があります。
- [Azure への移行にかかる時間](#market) - Azure VM 内の SQL Server はお客様の環境と完全に一致するので、オンプレミスから Azure SQL VM への移行は、オンプレミス サーバー間でのデータベースの移行と同じです。 マネージド インスタンスではとても簡単に移行できますが、マネージド インスタンスに移行する前に適用する必要がある変更がいくつかあります。

これらの要因については、次のセクションで詳しく説明します。

### <a name="cost"></a>コスト

現金が潤沢にないスタートアップ企業や厳しい予算で経営している老舗企業では、資金調達での限界がデータベースをホストする方法を決定する主な要因になることがあります。 このセクションではまず、SQL Database と Azure VM 上の SQL Server の 2 つのリレーショナル データベース オプションに関する Azure の課金とライセンスの基礎について説明します。 また、アプリケーションの総コストの計算についても説明します。

#### <a name="billing-and-licensing-basics"></a>課金とライセンスの基礎

現在、**SQL Database** は、サービスとして販売され、リソースの料金が異なる複数のデプロイ オプションと複数のサービス レベルで利用できます。いずれの場合も、選択したサービス レベルとコンピューティング サイズに基づく固定レートで時間単位で課金されます。 現在のサポートされているサービス レベル、コンピューティング サイズ、ストレージ容量の最新情報については、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)と[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)に関する記事を参照してください。

- SQL Database の単一データベースでは、5 ドル/月の Basic レベルから開始するさまざまな価格範囲からニーズに合ったサービス レベルを選択できます。
- [エラスティック プール](sql-database-elastic-pool.md)を作成して、データベース インスタンス間でパフォーマンス リソースを共有し、コストを削減して、ピーク時の使用に対応できます。
- SQL Database Managed Instance では、ライセンスの持ち込みも可能です。 現在所有しているライセンスの移行の詳細について「[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)」をご覧になるか、[Azure ハイブリッド特典計算ツール](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database)を使用して**最大 40% 節約する**方法をご確認ください。

さらに、インターネット トラフィックの送信も通常の [データ転送料金](https://azure.microsoft.com/pricing/details/data-transfers/)で課金されます。 サービス レベルとコンピューティング サイズを動的に調整して、アプリケーションのさまざまなスループット ニーズを満たすことができます。

**SQL Database**では、データベース ソフトウェアの自動的な構成、修正プログラムの適用、およびアップグレードがマイクロソフトによって行われるため、管理コストが軽減されます。 また、 [組み込みのバックアップ](sql-database-automated-backups.md) 機能は、特に、多数のデータベースがある場合の大幅なコスト削減に役立ちます。

**Azure VM 上の SQL Server**では、プラットフォームによって提供される、いずれかの SQL Server イメージ (ライセンスが付属) を使用するか、所有している SQL Server ライセンスを移行することができます。 サポートされている SQL Server のバージョン (2008R2、2012、2014、2016) とエディション (Developer、Express、Web、Standard、Enterprise) はすべて利用できます。 さらに、ライセンス持ち込み (BYOL) バージョンのイメージも利用できます。 Azure によって提供されるイメージを使用する場合、運用コストは、VM のサイズと選択する SQL Server のエディションによって決まります。 VM のサイズや SQL Server のエディションにかかわらず、SQL Server および Windows Server または Linux Server の  1 分あたりのライセンス費用と VM ディスクの Azure Storage コストをお支払いいただきます。 1 分あたりの課金のオプションでは、SQL Server の追加ライセンスを購入することなく、必要なだけ SQL Server を使用することができます。 Azure に SQL Server のライセンスを持ち込む場合は、サーバー コストとストレージ コストのみに対して課金されます。 現在所有しているライセンスの移行の詳細については、「 [Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)」を参照してください。 さらに、インターネット トラフィックの送信も通常の [データ転送料金](https://azure.microsoft.com/pricing/details/data-transfers/)で課金されます。

#### <a name="calculating-the-total-application-cost"></a>アプリケーションの総コストの計算

クラウド プラットフォームの使用を開始する際のアプリケーションのランニング コストには、新規開発コストと継続的管理コストのほか、パブリック クラウド プラットフォームのサービス コストが含まれます。

**Azure SQL Database を使用する場合:**

- 高度に最小化された管理コスト
- 移行されるアプリケーション (マネージド インスタンス) の制限された開発コスト
- SQL Database サービス コスト
- ハードウェアの購入コストは含まれない

**Azure VM 上の SQL Server を使用する場合:**

- より高い管理コスト
- 移行されるアプリケーションの開発コストなしに制限される
- 仮想マシン サービス コスト
- ハードウェアの購入コストは含まれない

価格の詳細については、次のリソースを参照してください。

- [SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)
- [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) および [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows) 向けの[仮想マシンの価格](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>[管理]

多くの企業にとって、クラウド サービスに切り替えるかどうかの決定には、コストだけでなく、管理の複雑さの軽減も重要な要素です。 IaaS および PaaS では、マイクロソフトが、基盤となるインフラストラクチャを管理して、すべてのデータを自動的にレプリケートし、ディザスター リカバリーを提供し、さらにデータベース ソフトウェアの構成とアップグレード、負荷分散の管理、データ センター内のサーバーに障害が発生した場合の透過的なフェールオーバーを実行します。

- **Azure SQL Database** を使用すると、ユーザーは引き続きデータベースを管理できますが、データベース エンジン、オペレーティング システム、ハードウェアを管理する必要はありません。 引き続き管理できるアイテムの例として、データベースとログイン、インデックスとクエリのチューニング、監査とセキュリティなどがあります。 さらに、別のデータ センターへの高可用性を構成するには、最小限の構成と管理が必要です。
- **Azure VM 上の SQL Server**では、オペレーティング システムと SQL Server インスタンスの構成を全面的に制御できます。 VM を使用する場合、オペレーティング システムとデータベース ソフトウェアの更新やアップグレードのタイミングに加えて、ウイルス対策などの追加ソフトウェアのインストールのタイミングは、ユーザーが決定します。 修正プログラムの適用、バックアップ、高可用性の実現を大幅に簡素化するために、自動化された機能がいくつか用意されています。 また、VM のサイズ、ディスクの数、ストレージの構成を制御できます。 Azure では、必要に応じて VM のサイズを変更できます。 詳細については、「 [Azure の仮想マシンおよびクラウド サービスのサイズ](../virtual-machines/windows/sizes.md)」を参照してください。

### <a name="service-level-agreement-sla"></a>サービス レベル アグリーメント (SLA)

多くの IT 部門では、サービス レベル アグリーメント (SLA) の稼働時間に関する義務を遂行することは、最優先事項です。 このセクションでは、各データベースのホスト オプションに適用される SLA について説明します。

**SQL Database** の場合、マイクロソフトは、99.99% の可用性の SLA を提供しています。 最新情報については、 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/sql-database/)に関するページを参照してください。

**Azure VM 上で実行される SQL Server**では、仮想マシンのみを対象に、可用性 99.95% の SLA が提供されます。 この SLA は VM 上で実行される (SQL Server などの) プロセスは対象としておらず、可用性セットに少なくとも 2 つの VM インスタンスをホストしている必要があります。 最新情報については、 [VM の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)に関するページを参照してください。 VM 内のデータベース高可用性 (HA) を実現するには、[Always On 可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)など、SQL Server でサポートされているいずれかの高可用性オプションを構成する必要があります。 サポートされている高可用性オプションを使用した場合、SLA を追加することはできないものの、ほぼ 99.99% のデータベース可用性が実現されます。

### <a name="market"></a>Azure へ移行するタイミング

**SQL Database の単一データベースまたはエラスティック プール**は、開発者の生産性と新しいソリューションの製品化に要する時間の短縮が重要な、クラウド用に設計されたアプリケーションに最適なソリューションです。 プログラムによる DBA のような機能を備えることで、基になるオペレーティング システムとデータベースを管理する必要性が減少するため、クラウドの設計者と開発者に最適です。

**SQL Database マネージド インスタンス**は、Azure SQL Database への既存のアプリケーションの移行を大幅に簡素化し、移行されたデータベース アプリケーションを Azure ですばやく市場に投入できるようにします。

**Azure VM 上で実行される SQL Server** は、既存または新規のアプリケーションに、大規模なデータベース、SQL Server または Windows/Linux の全機能へのアクセスのいずれかが必要で、新しいオンプレミス ハードウェアの取得にかかる時間とコストを回避したい場合に最適です。 また、既存のオンプレミス アプリケーションとデータベースを Azure にそのまま移行する場合で、Azure SQL Database Managed Instance が適合しない場合にも適しています。 プレゼンテーション層、アプリケーション層、およびデータ層を変更する必要がないため、既存のソリューションを再設計する時間と予算が節約されます。 その一方で、すべてのソリューションを Azure に移行し、Azure プラットフォームで必要な一部のパフォーマンスの最適化に集中できます。 詳細については、 [Azure 仮想マシン上の SQL Server のパフォーマンスに関するベスト プラクティス](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- SQL Database の使用を開始するには、「[初めての Azure SQL Database](sql-database-single-database-get-started.md)」を参照してください。
- 「 [SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」を参照してください。
- Azure VM での SQL Server の基本的な使い方については、 [Azure での SQL Server 仮想マシンのプロビジョニング](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) に関する記事を参照してください。
- [オンプレミス データベースに適した Azure SQL Database/Managed Instance SKU を特定します](/sql/dma/dma-sku-recommend-sql-db/)。