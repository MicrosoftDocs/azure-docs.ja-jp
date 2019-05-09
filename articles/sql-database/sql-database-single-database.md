---
title: Azure SQL Database の単一データベースとは | Microsoft Docs
description: Azure SQL Database の単一データベースについて説明します
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 034/08/2019
ms.openlocfilehash: 0437ba24727c06e874b49908a5a22d0063de72fd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799101"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Azure SQL Database の単一データベースとは

単一データベース デプロイ オプションでは、Azure SQL Database 内にその独自のリソース セットでデータベースが作成され、SQL Database サーバー経由で管理されます。 単一データベースでは、各データベースは互いに分離しており、移植可能です。各データベースでは、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)または[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)内に独自のサービス レベルがあり、コンピューティング サイズが保証されています。

> [!IMPORTANT]
> 単一データベースは、Azure SQL Database の 3 つのデプロイ オプションの 1 つです。 他の 2 つは、[エラスティック プール](sql-database-elastic-pool.md)と[マネージド インスタンス](sql-database-managed-instance.md)です。
> [!NOTE]
> Azure SQL Database の用語集については、[SQL Database 用語集](sql-database-glossary-terms.md)を参照してください

## <a name="dynamic-scalability"></a>動的スケーラビリティ

最初に低い価格/パフォーマンスのサービス レベルで月額の低い小さな単一データベースにアプリをビルドし、後でいつでもソリューションのニーズに合わせて高い価格/パフォーマンスのサービス レベルに手動またはプログラムで[サービス レベルを変更する](sql-database-single-database-scale.md)ことができます。 アプリにも顧客にもダウンタイムを発生させずにパフォーマンスを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができ、必要なときに必要な分のリソースにのみ課金されます。

## <a name="single-databases-and-elastic-pools"></a>単一データベースとエラスティック プール

単一データベースは、リソース共有のために[エラスティック プール](sql-database-elastic-pool.md)内に移動したり、エラスティック プールから出したりできます。 多くのビジネスとアプリケーションにとって、特に使用パターンが比較的予測可能である場合、単一データベースを作成し、要求に応じてパフォーマンスを調整することができれば、それで十分です。 しかし、使用パターンが予測できない場合、コストおよびビジネス モデルを管理するのが難しくなる可能性があります。 エラスティック プールは、この問題を解決するように設計されています。 概念は単純です。 パフォーマンス リソースを個々のデータベースではなくプールに割り当て、課金は単一のデータベースのパフォーマンスではなくプールの全体的なパフォーマンス リソースに対して行われます。

## <a name="monitoring-and-alerting"></a>監視とアラート

組み込みの[パフォーマンス監視](sql-database-performance.md)および[アラート ツール](sql-database-insights-alerts-portal.md)と、パフォーマンス評価とを組み合わせて使用します。 これらのツールを使用すると、現在または今後のパフォーマンスのニーズに基づいて、スケールアップとスケールダウンの影響をすばやく評価することができます。 さらに、SQL Database では、監視を容易にするための[メトリックと診断ログを出力](sql-database-metrics-diag-logging.md)することができます。

## <a name="availability-capabilities"></a>可用性に関する機能

単一データベース、エラスティック プール、マネージド インスタンスのいずれでも、多くの可用性特性が提供されます。 詳細については、[可用性の特性](sql-database-technical-overview.md#availability-capabilities)に関する記事を参照してください。

## <a name="transact-sql-differences"></a>Transact-SQL の相違点

アプリケーションが使用する Transact-SQL 機能の大半は、Microsoft SQL Server と Azure SQL Database の両方で完全にサポートされます。 たとえば、データ型、演算子、文字列、算術演算子、論理、およびカーソル機能などのコア SQL コンポーネントは、SQL Server および SQL Database で同様に動作します。 ただし、DDL (データ定義言語) と DML (データ操作言語) 要素における T-SQL のいくつかの相違点により、T-SQL ステートメントとクエリは部分的にしかサポートされません (これについてはこの記事で後ほど説明します)。
また、Azure SQL Database はマスター データベースとオペレーティング システムへの依存から機能を分離するように設計されているため、まったくサポートされていない機能と構文がいくつかあります。 そのため、サーバー レベルの大半のアクティビティは SQL Database には不適切です。 T-SQL ステートメントとオプションは、サーバー レベルのオプション、オペレーティング システムのコンポーネントを構成した場合、またはファイル システムの構成を指定した場合は使用できません。 このような機能が必要な場合は、SQL Database や別の Azure 機能またはサービスから代わりの適切な機能を使用できることがあります。

詳細については、「[SQL Database への移行時に Transact-SQL の相違点を解決する](sql-database-transact-sql-information.md)」を参照してください。

## <a name="security"></a>セキュリティ

SQL Database は、アプリケーションがさまざまなセキュリティとコンプライアンスの要件を満たすために役立つ、幅広い[組み込みのセキュリティとコンプライアンス](sql-database-security-overview.md)の機能を備えています。

> [!IMPORTANT]
> Azure SQL Database (すべてのデプロイ オプション) は、さまざまなコンプライアンス標準に対して認定されています。 詳細については、[Microsoft Azure セキュリティ センター](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)に関するページを参照してください。ここから最新の SQL Database コンプライアンス証明書の一覧を入手できます。

## <a name="next-steps"></a>次の手順

- 単一データベースをすぐに使い始めるには、[単一データベースのクイック スタート ガイド](sql-database-single-database-quickstart-guide.md)に関する記事をご覧ください。
- SQL Server データベースを Azure に移行する方法については、「[Azure SQL Database に移行](sql-database-single-database-migrate.md)」を参照してください。
- サポートされている機能については、[機能](sql-database-features.md)に関する記事をご覧ください。
