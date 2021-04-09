---
title: Azure SQL とは
description: 次の Azure SQL ファミリのサービス内のさまざまなオプションについて説明します:Azure SQL Database、Azure SQL Managed Instance、および Azure VM 上の SQL Server。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: SQL Server クラウド、クラウド内の SQL Server、PaaS データベース、クラウド SQL Server、DBaaS、IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: ea097119111d5dbd5eba3c11aba549d201186e3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592185"
---
# <a name="what-is-azure-sql"></a>Azure SQL とは 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL は、管理され、セキュリティで保護されたインテリジェントな製品のファミリであり、Azure クラウドで SQL Server データベース エンジンを使用します。

- **Azure SQL Database**:サーバーレス コンピューティングを含む、インテリジェントな管理されたデータベース サービスで最新のクラウド アプリケーションをサポートします。 
- **Azure SQL Managed Instance**:インテリジェントなサービスとしてのフル マネージド インスタンスを使用して、既存の SQL Server アプリケーションを大規模に最新化し、SQL Server データベース エンジンとほぼ 100% の機能パリティを実現します。 クラウドへの移行に最適です。
- **Azure VM 上の SQL Server**: SQL Server のワークロードを簡単に移行 (リフト アンド シフト) し、SQL Server との 100% の互換性とオペレーティング システム レベルのアクセスを維持します。 
 
Azure SQL は使い慣れた SQL Server エンジンに基づいて構築されているので、アプリケーションを簡単に移行し、使い慣れたツール、言語、リソースを使い続けることができます。 お客様のスキルと経験をクラウドに移すことにより、既に持っているスキルと経験でさらに多くのことを行うことができます。 

ビジネス要件に適したオプションを判断するために、各製品が Microsoft のどの Azure SQL データ プラットフォームに適しているかについて学ぶことができます。 この記事を参照することで、コスト削減と管理の最小化のいずれを優先するかに関係なく、最大の関心事項であるビジネス要件に適したアプローチを判断できます。

Azure SQL を初めて使用する場合は、[Azure SQL ビデオ シリーズ](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)の "*Azure SQL の概要*" に関するビデオをご覧ください。
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]

> [!TIP]
> Azure SQL を改善するために、 [アンケートにご協力ください。](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456)

## <a name="overview"></a>概要

現在のデータ ドリブン環境では、デジタル変革の推進は、大量のデータを管理し、その可能性を活用する能力に依存しています。 しかし、今日のデータ資産は、オンプレミス、クラウド、またはネットワークの端でホストされているデータを使用して、ますます複雑になっています。 インテリジェントかつイマーシブなアプリケーションを構築している開発者は、さまざまな制限による制約を受け、最終的にはそのエクスペリエンスに影響を与える場合があります。 互換性のないプラットフォーム、不十分なデータ セキュリティ、リソースの不足、コスト パフォーマンスの障壁に起因する制限によって複雑さが生じ、アプリの最新化や開発が妨げられます。 

Azure とオンプレミス SQL Server データベースの話を始める前にまず、それらがすべてが利用可能であるということを理解しておいてください。 Microsoft のデータ プラットフォームは、SQL Server テクノロジを活用し、物理的なオンプレミスマシン、プライベート クラウド環境、サード パーティにホストされているプライベート クラウド環境、パブリック クラウドのすべてでそのテクノロジを使用できるようにします。 


### <a name="fully-managed-and-always-up-to-date"></a>完全に管理され、常に最新の状態 

革新により多くの時間をかけて、修正プログラムの適用、更新、データベースのバックアップにかける時間を短縮します。 Azure は、最新の更新プログラムとパッチが自動的に適用される常に最新の SQL を使用した唯一のクラウドです。データベースが常に最新の状態になり、サポート終了の問題がなくなります。 パフォーマンスのチューニング、高可用性、ディザスター リカバリー、バックアップなどの複雑なタスクも自動化されているため、アプリケーションに専念できます。  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>組み込みのインテリジェント セキュリティでデータを保護する 

Azure は絶えずデータの脅威を監視します。 Azure SQL を使用すると、次のことができます。

- インテリジェントで、かつ[高度な脅威検出](../security/fundamentals/threat-detection.md#threat-protection-features-other-azure-services)とプロアクティブな脆弱性評価アラートを使用して、潜在的な脅威をリアルタイムで修復します。 
- T-SQL、認証、ネットワーク、キー管理など、[組み込みのセキュリティ制御](https://azure.microsoft.com/overview/security/)を使用して、業界をリードする多層的な保護を実現します。 
- クラウド データベース サービスの中で最も包括的な[コンプライアンス](https://azure.microsoft.com/overview/trusted-cloud/compliance/)を活用します。 


### <a name="business-motivations"></a>ビジネスの動機

異なるデータ オファリングのどちらを選択するかの決定に影響する可能性のある要素がいくつかあります。

- [コスト](#cost): PaaS オプションと IaaS オプションのどちらにも、基になるインフラストラクチャとライセンスを対象とする基本価格が含まれます。 ただし、IaaS オプションではデータベースを管理するために追加の時間とリソースを投資する必要がありますが、PaaS ではこれらの管理機能が価格に含まれています。 IaaS では、使用していないときにリソースをシャットダウンしてコストを削減できますが、PaaS では、リソースを削除して必要なときに再作成しない限り常に実行されます。
- [管理](#administration): PaaS オプションでは、データベースを管理するために投資する必要のある時間が短縮されます。 ただし、実行できるカスタムの管理タスクおよびスクリプトの範囲も制限されます。 たとえば、CLR は SQL Database ではサポートされませんが、SQL Managed Instance のインスタンスではサポートされています。 また、PaaS のデプロイ オプションでは、トレース フラグの使用がサポートされていません。
- [サービス レベル アグリーメント](#service-level-agreement-sla): IaaS と PaaS の両方で、高度な業界標準の SLA が提供されます。 PaaS オプションでは 99.99% の SLA が保証されますが、IaaS ではインフラストラクチャについて 99.95% の SLA が保証されます。これは、データベースの可用性を確保するために追加のメカニズムを実装する必要があることを意味します。 99.99% の SLA を達成するには、追加の SQL 仮想マシンを作成し、SQL Server Always On 可用性グループの高可用性ソリューションを実装します。 
- [Azure に移行するための時間](#market): Azure VM 上の SQL Server はお客様の環境と完全に一致するため、オンプレミスから Azure VM への移行はオンプレミス サーバー間でのデータベースの移行と何も変わりません。 SQL Managed Instance では簡単に移行できますが、移行する前に適用する必要がある変更がいくつかあります。 


## <a name="service-comparison"></a>サービスの比較

   ![クラウド SQL Server オプション:IaaS 上の SQL Server、またはクラウド内の SaaS SQL Database。](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

図に示すように、各サービス内容は、インフラストラクチャに及ぼす管理のレベルと、コスト効率の度合いによって特徴付けることができます。

Azure では、ホストされるサービス ([PaaS](https://azure.microsoft.com/overview/what-is-paas/))、またはホストされるインフラストラクチャ ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) として、SQL Server のワークロードを実行することができます。 PaaS 内には、複数の製品オプションと、各オプション内のサービス レベルがあります。 PaaS か IaaS かを決定するときにたずねる必要がある重要な質問は、データベースの管理、修正プログラムの適用、バックアップの実行を自分で行うか、これらの操作を Azure に委任するかということです。

### <a name="azure-sql-database"></a>Azure SQL データベース

[Azure SQL Database](database/sql-database-paas-overview.md) は、Azure でホストされるリレーショナル DBaaS (サービスとしてのデータベース) であり、*サービスとしてのプラットフォーム (PaaS)* の業界カテゴリに分類されます。 
- 最新のクラウド アプリケーションで、最新の安定した SQL Server 機能を使用する必要があり、開発とマーケティングに時間の制約がある場合に最適です。 
- 最新の安定した SQL Server Enterprise Edition に基づいた、フル マネージドの SQL Server データベース エンジンです。 SQL Database には、Microsoft が所有、ホスト、保守する標準化されたハードウェアとソフトウェアに基づいて構築された 2 つのデプロイ オプションがあります。 

SQL Server では、(オンプレミスまたは Azure 仮想マシン内の) 広範な構成を必要とする組み込みの機能を使用できます。 SQL Database は従量課金制で利用でき、中断することなくスケールアップまたはスケールアウトして強化できるオプションもあります。 SQL Database には、組み込みの高可用性、インテリジェンス、管理など、SQL Server では使用できない追加の機能がいくつかあります。


Azure SQL Database には、次のデプロイの選択肢があります。
  - "[*単一データベース*](database/single-database-overview.md)" としてデプロイし、それ専用の各種リソースを [論理 SQL サーバー](database/logical-servers.md)を介して管理する。 単一データベースは SQL Server の[包含データベース](/sql/relational-databases/databases/contained-databases)に似ています。 このオプションは、クラウド生まれの新しいアプリケーションの最新のアプリケーション開発用に最適化されています。 [ハイパースケール](database/service-tier-hyperscale.md)と[サーバーレス](database/serverless-tier-overview.md)のオプションを使用できます。
  - "[*エラスティック プール*](database/elastic-pool-overview.md)"。データベースのコレクションで、各種の共有リソースを [論理 SQL サーバー](database/logical-servers.md)を介して管理します。 単一データベースはエラスティック プールの内外に移動できます。 このオプションは、マルチテナント SaaS アプリケ―ション パターンを使用して、クラウド生まれの新しいアプリケーションの最新のアプリケーション開発用に最適化されています。 エラスティック プールは、多様な使用パターンを持つ複数のデータベースのパフォーマンスを管理するための、コスト効率に優れたソリューションを提供します。

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

[Azure SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) は *サービスとしてのプラットフォーム (PaaS)* の業界カテゴリに分類され、クラウドへのほとんどの移行に最適です。 SQL Managed Instance は、各種の共有リソースを格納するシステムおよびユーザー データベースのコレクションで、リフト アンド シフトする準備ができています。  
- 最新の新しい SQL Server 機能を使用する必要があり、最小限の変更でクラウドに移行する新しいアプリケーションまたは既存のオンプレミス アプリケーションに最も適しています。 SQL Managed Instance のインスタンスは、[Microsoft SQL Server データベース エンジン](/sql/database-engine/sql-server-database-engine-overview)のインスタンスと同様に、データベースのための共有リソースや追加のインスタンス スコープ機能を提供します。 
- SQL Managed Instance では、オンプレミスからのデータベースの移行がサポートされ、データベースの変更は発生しないか最小限に抑えられます。 このオプションは Azure SQL Database の PaaS のすべての利点を提供しますが、以前は SQL Server VM でのみ使用できた機能を追加します。 これには、ネイティブ仮想ネットワークや、オンプレミスの SQL Server との 100% 近い互換性が含まれます。 SQL Managed Instance のインスタンスは、SQL サーバーを Azure に移行するための SQL Server のフル アクセスと機能の互換性を提供します。

### <a name="sql-server-on-azure-vm"></a>Azure VM 上の SQL Server

[Azure VM 上の SQL Server](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) は *サービスとしてのインフラストラクチャ (IaaS)* の業界カテゴリに分類され、Azure のフル マネージドの仮想マシン (VM) 内で SQL Server を実行できるようにします。 
- 移行と、OS レベルのアクセスを必要とするアプリケーションに最適です。 Azure 内の SQL 仮想マシンは、最小限の変更または変更なしでクラウドに迅速に移行する必要がある既存のアプリケーションに対して、リフト アンド シフトする準備ができています。 SQL 仮想マシンは、Azure に移行するための SQL Server インスタンスと基になる OS に対する完全な管理制御を提供します。 
- 運用環境以外のオンプレミス SQL Server のハードウェアを購入しない場合の迅速な開発およびテスト シナリオ。 SQL 仮想マシンは、Microsoft が所有、ホスト、保守する標準的なハードウェア上でも実行できます。 SQL 仮想マシンを使用する場合は、SQL Server イメージに含まれている SQL Server ライセンスを使用して従量課金で支払いを行うか、単に既存のライセンスを使用することができます。 必要に応じて、VM を停止または再開することもできます。 
- クラウド上にインストールされてホストされている SQL Server は、Azure 上で実行されている Windows Server または Linux 仮想マシンで実行され、サービスとしてのインフラストラクチャ (IaaS) とも呼ばれます。 SQL 仮想マシンは、データベースの変更を行わずにオンプレミスの SQL Server データベースとアプリケーションを移行する場合に適したオプションです。 SQL Server のすべての最新バージョンとエディションは、IaaS 仮想マシンでのインストールに使用できます。 

    SQL Database と SQL Managed Instance の最も大きな違いは、Azure Virtual Machines における SQL Server ではデータベース エンジンを完全に制御できることです。 メンテナンスまたは修正プログラムの適用を開始するタイミングを選択したり、復旧モデルを単純なログまたは一括ログに変更したりできます。また、必要に応じてサービスを一時停止または開始することができるほか、SQL Server データベース エンジンを完全にカスタマイズすることができます。 この制御を追加する場合、仮想マシンを管理する責任も追加で伴います。
- 既存のアプリケーションを Azure に移行したり、ハイブリッド デプロイで既存のオンプレミス アプリケーションをクラウドに拡張したりできるように最適化されています。 さらに、仮想マシン上の SQL Server は従来の SQL Server アプリケーションを開発、テストするためにも使用できます。 SQL 仮想マシンでは、専用の SQL Server インスタンスとクラウドベースの VM に対して完全な管理者権限があります。 仮想マシンの維持に使用できる IT リソースが組織に既にある場合は、Azure VM 内の SQL Server を選択することをお勧めします。 これらの機能を使用すると、高度にカスタマイズされたシステムを構築して、アプリケーション固有のパフォーマンスや可用性の要件に対処できます。

次の表にその他の違いを示しますが、"*SQL Database と SQL Managed Instance は両方とも、多くのデータベースをプロビジョニングおよび管理するための全体的な管理コストを最小限に抑えるように最適化されています。* " どの仮想マシン、オペレーティング システム、データベース ソフトウェアも管理する必要がないため、継続的な管理コストが削減されます。 アップグレード、高可用性、 [バックアップ](database/automated-backups-overview.md)を管理する必要はありません。 

一般的には、SQL Database と SQL Managed Instance を使用すると、単一の IT または開発リソースが管理するデータベースの数を大幅に増やすことができます。 [エラスティック プール](database/elastic-pool-overview.md)では、SaaS マルチ テナント アプリケーション アーキテクチャもサポートします。これにはテナントの分離や、データベース間でリソースを共有することでコストを削減するようにスケーリングする機能などが含まれます。 [SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) ではインスタンス スコープ機能のサポートが提供されるため、既存のアプリケーションを簡単に移行したり、データベース間でリソースを共有したりできるようになります。

### <a name="comparison-table"></a>比較表

| Azure SQL データベース | Azure SQL Managed Instance | Azure VM 上の SQL Server |
| :--- | :--- | :--- |
|ほとんどのオンプレミス データベース レベルの機能をサポートしています。 最もよく使用される SQL Server 機能を利用できます。<br/>99.995% の可用性を保証。<br/>組み込みのバックアップ、修正プログラムの適用、回復。<br/>最新の安定版データベース エンジン バージョン。<br/>個々のデータベースに必要なリソース (CPU/ストレージ) を割り当てる機能。<br/>組み込みの高度なインテリジェンスとセキュリティ。<br/>オンラインでのリソース (CPU/ストレージ) の変更。| ほとんどすべてのオンプレミス インスタンス レベルおよびデータベース レベルの機能をサポートしています。 SQL Server との高い互換性。<br/>99.99% の可用性を保証。<br/>組み込みのバックアップ、修正プログラムの適用、回復。<br/>最新の安定版データベース エンジン バージョン。<br/>SQL Server からの移行が簡単。<br/>Azure Virtual Network 内のプライベート IP アドレス。<br/>組み込みの高度なインテリジェンスとセキュリティ。<br/>オンラインでのリソース (CPU/ストレージ) の変更。| SQL Server エンジンを完全に制御できます。 すべてのオンプレミス機能をサポートしています。<br/>最大 99.99% の可用性。<br/>オンプレミスの SQL Server の対応するバージョンと完全に一致。<br/>固定された、既知のデータベース エンジン バージョン。<br/>SQL Server からの移行が簡単。<br/>Azure Virtual Network 内のプライベート IP アドレス。<br/>SQL Server が配置されているホストでアプリケーションまたはサービスをデプロイできます。|
|SQL Server からの移行が難しい可能性があります。<br/>一部の SQL Server 機能は使用できません。<br/>正確なメンテナンス時間の保証はありません (ただし、ほぼ透過的です)。<br/>データベース互換性レベルをのみを使用して、SQL Server バージョンとの互換性を実現できます。<br/>[Azure Private Link](database/private-endpoint-overview.md) を使用したプライベート IP アドレスのサポート。|利用できない最小限の数の SQL Server 機能がまだあります。<br/>正確なメンテナンス時間の保証はありません (ただし、ほぼ透過的です)。<br/>データベース互換性レベルをのみを使用して、SQL Server バージョンとの互換性を実現できます。|バックアップと修正プログラムを管理する必要があります。<br>高可用性ソリューションを独自に実装する必要があります。<br/>リソース (CPU/ストレージ) の変更中にダウンタイムが発生|
| 最大 100 TB のデータベース。 | 最大 8 TB。 | 記憶域が 256 TB までの SQL Server インスタンス。 インスタンスでは必要な数のデータベースがサポートされます。 |
| オンプレミス アプリケーションから Azure SQL Database 内のデータにアクセスできます。 | Azure Express Route または VPN Gateway を使った、オンプレミス環境への[ネイティブ仮想ネットワークの実装](managed-instance/vnet-existing-add-subnet.md)と接続。 | SQL 仮想マシンを使用して、アプリケーションの一部をクラウド上で実行し、一部をオンプレミスで実行することができます。 たとえば、 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)を介してオンプレミス ネットワークと Active Directory ドメインをクラウドに拡張できます。 ハイブリッド クラウド ソリューションについて詳しくは、「[オンプレミス データ ソリューションのクラウドへの拡張](/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud)」をご覧ください。 |


## <a name="cost"></a>コスト

現金が潤沢にないスタートアップ企業や厳しい予算で経営している老舗企業では、資金調達での限界がデータベースをホストする方法を決定する主な要因になることがあります。 このセクションでは、Azure SQL ファミリのサービスに関連付けられている Azure の課金とライセンスの基本について説明します。  また、アプリケーションの総コストの計算についても説明します。

### <a name="billing-and-licensing-basics"></a>課金とライセンスの基礎

現在、**SQL Database** と **SQL Managed Instance** は、どちらもサービスとして販売され、リソースの料金が異なる複数のオプションと複数のサービス レベルで利用できます。いずれの場合も、選択したサービス レベルとコンピューティング サイズに基づく固定レートで時間単位で課金されます。 現在のサポートされているサービス レベル、コンピューティング サイズ、ストレージ容量の最新情報については、[SQL Database の DTU ベースの購入モデル](database/service-tiers-dtu.md)、および [SQL Database と SQL Managed Instance 両方の仮想コアベースの購入モデル](database/service-tiers-vcore.md)に関する記事を参照してください。

- SQL Database では、Basic レベルの 5 ドル/月から始まる幅広い価格からニーズに合ったサービス レベルを選択できます。また、[エラスティック プール](database/elastic-pool-overview.md)を作成してデータベース間でリソースを共有することにより、コストを削減したり、使用量の急増に対応したりできます。
- SQL Managed Instance では、ライセンスの持ち込みも可能です。 現在所有しているライセンス持ち込みの詳細については、「[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)」をご覧になるか、[Azure ハイブリッド特典計算ツール](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database)を使用して **最大 40% 節約する** 方法をご確認ください。

さらに、インターネット トラフィックの送信も通常の [データ転送料金](https://azure.microsoft.com/pricing/details/data-transfers/)で課金されます。 サービス レベルとコンピューティング サイズを動的に調整して、アプリケーションのさまざまなスループット ニーズを満たすことができます。

**SQL Database** と **SQL Managed Instance** では、データベース ソフトウェアの自動的な構成、修正プログラムの適用、およびアップグレードが Azure によって行われるため、管理コストが軽減されます。 また、 [組み込みのバックアップ](database/automated-backups-overview.md) 機能は、特に、多数のデータベースがある場合の大幅なコスト削減に役立ちます。

**Azure VM 上の SQL** では、プラットフォームによって提供される、いずれかの SQL Server イメージ (ライセンスが付属) を使用するか、所有している SQL Server ライセンスを移行することができます。 サポートされている SQL Server のバージョン (2008R2、2012、2014、2016、2017、2019) とエディション (Developer、Express、Web、Standard、Enterprise) はすべて利用できます。 さらに、ライセンス持ち込み (BYOL) バージョンのイメージも利用できます。 Azure によって提供されるイメージを使用する場合、運用コストは、VM のサイズと選択する SQL Server のエディションによって決まります。 VM のサイズや SQL Server のエディションにかかわらず、ユーザーは、SQL Server および Windows Server または Linux Server の 1 分あたりのライセンス費用と VM ディスクの Azure Storage コストを支払います。 1 分あたりの課金のオプションでは、SQL Server の追加ライセンスを購入することなく、必要なだけ SQL Server を使用することができます。 Azure に SQL Server のライセンスを移行する場合は、サーバー コストとストレージ コストのみに対して課金されます。 現在所有しているライセンスの移行の詳細については、「 [Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)」を参照してください。 さらに、インターネット トラフィックの送信も通常の [データ転送料金](https://azure.microsoft.com/pricing/details/data-transfers/)で課金されます。

#### <a name="calculating-the-total-application-cost"></a>アプリケーションの総コストの計算

クラウド プラットフォームの使用を開始する際のアプリケーションのランニング コストには、新規開発コストと継続的管理コストのほか、パブリック クラウド プラットフォームのサービス コストが含まれます。

価格の詳細については、次のリソースを参照してください。

- [SQL Database および SQL Managed Instance の価格](https://azure.microsoft.com/pricing/details/sql-database/)
- [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) および [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows) 向けの[仮想マシンの価格](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>管理

多くの企業にとって、クラウド サービスに切り替えるかどうかの決定には、コストだけでなく、管理の複雑さの軽減も重要な要素です。 IaaS および PaaS では、基盤となるインフラストラクチャを Azure が管理します。すべてのデータを自動的にレプリケートしてディザスター リカバリーを提供し、データベース ソフトウェアの構成とアップグレードを行い、負荷分散を管理します。データ センター内のサーバーに障害が発生した場合は、透過的なフェールオーバーを実行します。

- **SQL Database** および **SQL Managed Instance** を使用すると、ユーザーは引き続きデータベースを管理できますが、データベース エンジン、オペレーティング システム、ハードウェアを管理する必要はありません。 引き続き管理できるアイテムの例として、データベースとログイン、インデックスとクエリのチューニング、監査とセキュリティなどがあります。 さらに、別のデータ センターへの高可用性を構成するには、最小限の構成と管理が必要です。
- **Azure VM 上の SQL** では、オペレーティング システムと SQL Server インスタンスの構成を完全に制御できます。 VM を使用する場合、オペレーティング システムとデータベース ソフトウェアの更新やアップグレードのタイミングに加えて、ウイルス対策などの追加ソフトウェアのインストールのタイミングは、ユーザーが決定します。 修正プログラムの適用、バックアップ、高可用性の実現を大幅に簡素化するために、自動化された機能がいくつか用意されています。 また、VM のサイズ、ディスクの数、ストレージの構成を制御できます。 Azure では、必要に応じて VM のサイズを変更できます。 詳細については、「 [Azure の仮想マシンおよびクラウド サービスのサイズ](../virtual-machines/sizes.md)」を参照してください。

## <a name="service-level-agreement-sla"></a>サービス レベル アグリーメント (SLA)

多くの IT 部門では、サービス レベル アグリーメント (SLA) の稼働時間に関する義務を果たすことが最優先事項です。 このセクションでは、各データベースのホスト オプションに適用される SLA について説明します。

**Azure SQL Database** と **Azure SQL Managed Instance** の両方について、可用性 99.99% の SLA が提供されます。 最新情報については、[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/sql-database/)に関するページを参照してください。

**Azure VM 上の SQL** では、仮想マシンのみを対象に 99.95% の可用性 SLA が提供されます。 この SLA は VM 上で実行される (SQL Server などの) プロセスは対象としておらず、可用性セットに少なくとも 2 つの VM インスタンスをホストしている必要があります。 最新情報については、 [VM の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)に関するページを参照してください。 VM 内のデータベース高可用性 (HA) を実現するには、[Always On 可用性グループ](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)など、SQL Server でサポートされているいずれかの高可用性オプションを構成する必要があります。 サポートされている高可用性オプションを使用した場合、SLA を追加することはできないものの、ほぼ 99.99% のデータベース可用性が実現されます。

## <a name="time-to-move-to-azure"></a><a name="market"></a>Azure へ移行するタイミング

**Azure SQL Database** は、開発者の生産性と新しいソリューションの製品化に要する時間の短縮が重要な場合において、クラウド用に設計されたアプリケーションに最適なソリューションです。 プログラムによる DBA のような機能を備えることで、基になるオペレーティング システムとデータベースを管理する必要性が減少するため、クラウドの設計者と開発者に最適です。

**Azure SQL Managed Instance** は、Azure への既存のアプリケーションの移行を大幅に簡素化し、移行されたデータベース アプリケーションを Azure ですばやく市場に投入できるようにします。

**Azure VM 上の SQL** は、既存または新規のアプリケーションに大規模なデータベースか、あるいは SQL Server または Windows/Linux のすべての機能へのアクセスが必要であり、新しいオンプレミス ハードウェアの取得にかかる時間と経費を回避したい場合に最適です。 また、既存のオンプレミス アプリケーションとデータベースを Azure にそのまま移行する場合で、SQL Database または SQL Managed Instance が適合しない場合にも適しています。 プレゼンテーション層、アプリケーション層、およびデータ層を変更する必要がないため、既存のソリューションを再設計する時間と予算が節約されます。 その一方で、すべてのソリューションを Azure に移行し、Azure プラットフォームで必要な一部のパフォーマンスの最適化に集中できます。 詳細については、 [Azure 仮想マシン上の SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines/windows/performance-guidelines-best-practices.md)に関するページを参照してください。

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>次のステップ

- SQL Database の使用を開始するには、「[初めての Azure SQL Database](database/single-database-create-quickstart.md)」を参照してください。
- SQL Managed Instance の使用を開始するには、[初めての Azure SQL Managed Instance](managed-instance/instance-create-quickstart.md) に関する記事を参照してください。 
- 「 [SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」を参照してください。
- Azure VM での SQL Server の基本的な使い方については、 [Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines/windows/create-sql-vm-portal.md) に関する記事を参照してください。
- [オンプレミス データベースに適した SQL Database または SQL Managed Instance の SKU を特定します](/sql/dma/dma-sku-recommend-sql-db/)。