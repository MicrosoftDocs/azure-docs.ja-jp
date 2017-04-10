---
title: "Azure SQL Database サービスとは | Microsoft Docs"
description: 'Get an introduction to SQL Database: technical details and capabilities of Microsoft''s relational database management system (RDBMS) in the cloud.'
keywords: "sql の概要,sql の紹介,sql database とは"
services: sql-database
documentationcenter: 
author: shontnew
manager: jhubbard
editor: cgronlun
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/17/2017
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: c505844cc2b7c745a1106b3c446833fb206ca98a
ms.lasthandoff: 03/18/2017

---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>SQL Database とは SQL Database の概要
SQL Database は、市場をリードする Microsoft SQL Server エンジンとミッション クリティカルなワークロードを処理する機能を基盤とする、Microsoft Cloud のリレーショナル データベース サービスです。 SQL Database は、複数のサービス レベルでの予測可能なパフォーマンス、ダウンタイムなしの動的なスケーラビリティ、組み込みのビジネス継続性、およびデータ保護を提供します。どの機能も、管理作業をほとんど必要としません。 これらの機能を使用すると、貴重な時間とリソースを仮想マシンとインフラストラクチャの管理に奪われることなく、迅速なアプリケーション開発や、製品化に要する時間の短縮化に専念することができます。 SQL Database は [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) エンジンを基盤としているので、既存の SQL Server ツール、ライブラリ、および API がサポートされています。 そのため、新たなスキルを学習しなくても、新しいソリューションの開発、既存の SQL Server ソリューションの移動、Microsoft Cloud への既存の SQL Server ソリューションの拡張を簡単に行うことができます。

この記事では、パフォーマンス、スケーラビリティ、および管理容易性に関連する、SQL Database の中心概念および機能について紹介し、詳細を参照するためのリンクも提供します。 すぐに始めるには、次のクイック スタートをご覧ください。
 - [Azure Portal で SQL データベースを作成する](sql-database-get-started-portal.md)  
 - [Azure CLI で SQL データベースを作成する](sql-database-get-started-cli.md)
 - [PowerShell を使用して SQL データベースを作成する](sql-database-get-started-powershell.md)

Azure CLI と PowerShell の各種サンプルについては、以下のページをご覧ください。
 - [Azure SQL Database 用の Azure CLI サンプル](sql-database-cli-samples.md)
 - [Azure SQL Database 用の Azure PowerShell サンプル](sql-database-powershell-samples.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>ダウンタイムなしでのパフォーマンスの調整とスケール
SQL Database サービスには、Basic、Standard、Premium という&3; つのサービス レベルが用意されています。 各サービス階層では、軽量のデータベース ワークロードから重量のデータベース ワークロードに至るまでサポートできるように、 [さまざまなレベルのパフォーマンスと機能](sql-database-service-tiers.md) を提供しています。 最初にアプリを月数ドルの小規模データベースでビルドし、後でいつでもソリューションのニーズに合わせて手動またはプログラムで[サービス レベルを変更](sql-database-service-tiers.md)することができます。 その際に、アプリにも顧客にも、ダウンタイムは発生しません。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができ、必要なときに必要な分のリソースにのみ課金されます。

## <a name="elastic-pools-to-maximize-resource-utilization"></a>リソース使用率を最大化するためのエラスティック プール
特に使用パターンが比較的予測可能である場合、多くのビジネスとアプリにおいて、単一データベースを作成し、要求に応じてパフォーマンスを調整する能力は十分です。 しかし、使用パターンが予測できない場合、コストおよびビジネス モデルを管理するのが難しくなる可能性があります。 [エラスティック プール](sql-database-elastic-pool.md)は、この問題を解決するように設計されています。 概念は単純です。 パフォーマンス リソースを個々のデータベースではなくプールに割り当て、課金は単一のデータベースのパフォーマンスではなくプールの全体的なパフォーマンス リソースに対して行われます。 エラスティック プールを使用すると、リソースの需要が変動しても、データベース パフォーマンスの調整に気を配る必要がなくなります。 プールされたデータベースは、必要に応じて、エラスティック プールのパフォーマンス リソースを使用します。 しかし、プールされたデータベースの使用は、プールの上限を超えることはありません。したがって、個々のデータベースの使用状況が予測できなくても、コストが予測可能なことに変わりはありません。 さらに、[プールに対してデータベースの追加および削除を行う](sql-database-elastic-pool-manage-portal.md)ことで、完全に設定予算内で、アプリケーションを数個のデータベースから何千ものデータベースに及ぶ範囲でスケーリングすることができます。 また、プール内のデータベースが使用できるリソースの下限と上限を制御して、プール内のいずれかのデータベースがプールのすべてのリソースを使用してしまわないようにしたり、プールされているすべてのデータベースに最小限のリソースが保証されるようにしたりすることもできます。 エラスティック プールを使用する SaaS アプリケーションの設計パターンの詳細については、「 [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)」を参照してください。

## <a name="blend-single-databases-with-pooled-databases"></a>単一データベースとプールされたデータベースの組み合わせ
単一データベースとエラスティック プールのどちらの方法を採っても、その方法に固定されることはありません。 単一データベースをエラスティック プールと組み合わせると、状況に合わせて単一データベースとエラスティック プールのサービス レベルをすばやく簡単に変更することができます。 さらに、Azure の強力さと幅広さを利用して、他の Azure サービスを SQL Database とうまく組み合わせることにより、独自のアプリ設計のニーズを満たし、コストとリソースの効率性を向上させ、新たなビジネス チャンスを開くことができます。

## <a name="monitoring-and-alerting"></a>監視とアラート
しかし、単一データベースとエラスティック プールの相対的なパフォーマンスは、どのような方法で比較すればよいでしょうか? パフォーマンスの高低を調整するとき、何に基づいて右クリックでストップをかけますか? [組み込みのパフォーマンス監視ツール](sql-database-performance.md)と[アラート ツール](sql-database-insights-alerts-portal.md)を、パフォーマンス評価と組み合わせて使用してください。この評価は、[単一データベースの場合はデータベース トランザクション ユニット (DTU)、エラスティック プールの場合はエラスティック DTU (eDTU)](sql-database-what-is-a-dtu.md) に基づいています。 これらのツールを使用すると、現在または今後のパフォーマンスのニーズに基づいて、スケールアップとスケールダウンの影響をすばやく評価することができます。 詳細については、「 [SQL Database のオプションとパフォーマンス: 各サービス階層で使用できる内容について理解します](sql-database-service-tiers.md) 」を参照してください。

## <a name="keep-your-app-and-business-running"></a>アプリケーションとビジネスの継続的な稼働
Microsoft が管理するデータセンターのグローバル ネットワークによって強化された、Azure の業界をリードする可用性 99.99% のサービス レベル アグリーメント [(SLA)](http://azure.microsoft.com/support/legal/sla/) により、アプリケーションの 24 時間 365 日の継続的な稼働が可能になります。 すべての SQL Database で、組み込みのセキュリティ、フォールト トレランス、[データ保護](sql-database-automated-backups.md)を利用できます。これらは、本来なら自身で購入または設計し、構築、管理する必要があります。 SQL Database では、サービス レベルごとに、包括的な一連のビジネス継続性に関する機能とオプションが用意されており、これらを使用すると、稼働させてその状態を維持することができます。 [ポイントインタイム リストア](sql-database-recovery-using-backups.md)を使用すると、データベースを 35 日間分さかのぼって以前の状態に戻すことができます。 [長期的なバックアップ保有期間](sql-database-long-term-retention.md)を構成すると、セキュリティで保護されたコンテナーにバックアップを最大で 10 年間格納しておくことができます。 さらに、データベースをホストしているデータ センターで障害が発生した場合は、[最新のバックアップの geo 冗長コピー](sql-database-recovery-using-backups.md)からデータベースを復元することができます。 必要であれば、データ センターの停止時に迅速にフェールオーバーできるように、1 つまたは複数のリージョンに [geo 冗長読み取り可能レプリカ](sql-database-geo-replication-overview.md)を構成することもできます。 別の地理的リージョンでの読み取りパフォーマンスを向上させたり、[ダウンタイムなしでアプリケーションをアップグレード](sql-database-manage-application-rolling-upgrade.md)したりするために、これらのレプリカを使用することもできます。 

![SQL Database Geo-Replication](./media/sql-database-technical-overview/azure_sqldb_map.png)

それぞれのサービス階層で使用できるさまざまなビジネス継続性機能の詳細については、「 [ビジネス継続性](sql-database-business-continuity.md) 」をご覧ください。

## <a name="secure-your-data"></a>データのセキュリティ保護
SQL Server は、アクセスの制限、データの保護、および監視アクティビティの支援を行う機能を備えた SQL Database によって、データ セキュリティを維持してきました。 SQL Database で提供されているセキュリティ オプションの迅速なランダウンについては、「 [SQL Database のセキュリティ保護](sql-database-security-overview.md) 」をご覧ください。 セキュリティ機能をより包括的に把握したい場合は、「 [SQL Server データベース エンジンと SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589) 」をご覧ください。 Azure のプラットフォーム セキュリティについては、「 [Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/security/) 」をご覧ください。

## <a name="next-steps"></a>次のステップ
ここでは、SQL Database の概要を紹介し、"SQL Database とは何か" という問いに対する答えを示したので、次のステップに進むことができます。

* 単一データベースとエラスティック プールのコストの比較と計算ツールについては、[価格に関するページ](https://azure.microsoft.com/pricing/details/sql-database/)を参照してください。
* [エラスティック プール](sql-database-elastic-pool.md)について学習します。
* [最初のデータベースを作成する](sql-database-get-started.md)ことによって作業を開始します。
* 初めてのアプリを C#、Java、Node.js、PHP、Python、Ruby で作成します。「[SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)」を参照してください。

