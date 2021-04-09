---
title: 単一データベースとは
description: Azure SQL Database の単一データベースのリソースの種類について学習します。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: 8f92fe8e4a4ebbc2d970bf28e415859249b9f67c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84343321"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Azure SQL Database の単一データベースとは
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

単一データベースのリソースの種類では、Azure SQL Database 内にその独自のリソース セットでデータベースが作成され、[サーバー](logical-servers.md)経由で管理されます。 単一データベースでは、各データベースが分離され、移植可能となります。 それぞれに、[DTU ベースの購入モデル](service-tiers-dtu.md)または[仮想コアベースの購入モデル](service-tiers-vcore.md)内で独自のサービス レベルが与えられ、特定のコンピューティング サイズが保証されます。

> [!IMPORTANT]
> 単一データベースは、Azure SQL Database 用のリソースの種類の 1 つです。 もう 1 つは[エラスティック プール](elastic-pool-overview.md)です。

## <a name="dynamic-scalability"></a>動的スケーラビリティ

最初のアプリは、サーバーレス コンピューティング レベルの小規模な単一データベース上に低コストで、またはプロビジョニングされたコンピューティング層の小さなコンピューティング サイズで構築できます。 ソリューションのニーズに合わせて、いつでも[コンピューティング層またはサービス層](single-database-scale.md)を手動またはプログラムで変更します。 アプリにも顧客にもダウンタイムを発生させずにパフォーマンスを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができ、必要なときに必要な分のリソースにのみ課金されます。

## <a name="single-databases-and-elastic-pools"></a>単一データベースとエラスティック プール

単一データベースは、リソース共有のために[エラスティック プール](elastic-pool-overview.md)内に移動したり、エラスティック プールから出したりできます。 多くのビジネスとアプリケーションにとって、特に使用パターンが比較的予測可能である場合、単一データベースを作成し、要求に応じてパフォーマンスを調整することができれば、それで十分です。 しかし、使用パターンが予測できない場合、コストおよびビジネス モデルを管理するのが難しくなる可能性があります。 エラスティック プールは、この問題を解決するように設計されています。 概念は単純です。 パフォーマンス リソースを個々のデータベースではなくプールに割り当て、課金は単一のデータベースのパフォーマンスではなくプールの全体的なパフォーマンス リソースに対して行われます。

## <a name="monitoring-and-alerting"></a>監視とアラート

組み込みの[パフォーマンス監視](performance-guidance.md)および[アラート ツール](alerts-insights-configure-portal.md)と、パフォーマンス評価とを組み合わせて使用します。 これらのツールを使用すると、現在または今後のパフォーマンスのニーズに基づいて、スケールアップとスケールダウンの影響をすばやく評価することができます。 さらに、SQL Database では、監視を容易にするために[メトリックとリソース ログを出力する](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)ことができます。

## <a name="availability-capabilities"></a>可用性に関する機能

単一データベースとエラスティック プールでは、多くの可用性特性が提供されます。 詳細については、[可用性の特性](sql-database-paas-overview.md#availability-capabilities)に関する記事を参照してください。

## <a name="transact-sql-differences"></a>Transact-SQL の相違点

アプリケーションが使用する Transact-SQL 機能の大半は、Microsoft SQL Server と Azure SQL Database の両方で完全にサポートされます。 たとえば、データ型、演算子、文字列、算術演算子、論理、およびカーソル機能などのコア SQL コンポーネントは、SQL Server および SQL Database で同様に動作します。 ただし、DDL (データ定義言語) と DML (データ操作言語) 要素における T-SQL のいくつかの相違点により、T-SQL ステートメントとクエリは部分的にしかサポートされません (これについてはこの記事で後ほど説明します)。

また、Azure SQL Database はマスター データベースとオペレーティング システムへの依存関係から機能を分離するように設計されているため、サポートされていない機能と構文がいくつかあります。 そのため、サーバー レベルの大半のアクティビティは SQL Database には不適切です。 T-SQL ステートメントとオプションは、サーバー レベルのオプションを構成するか、オペレーティング システムのコンポーネントを構成するか、またはファイル システムの構成を指定する場合は利用できません。 このような機能が必要な場合は、SQL Database や別の Azure 機能またはサービスから代わりの適切な機能を使用できることがあります。

詳細については、「[SQL Database への移行時に Transact-SQL の相違点を解決する](transact-sql-tsql-differences-sql-server.md)」を参照してください。

## <a name="security"></a>Security

SQL Database は、アプリケーションがさまざまなセキュリティとコンプライアンスの要件を満たすために役立つ、幅広い[組み込みのセキュリティとコンプライアンス](security-overview.md)の機能を備えています。

> [!IMPORTANT]
> Azure SQL Database は、多くのコンプライアンス標準に対して認定されています。 詳細については、[Microsoft Azure セキュリティ センター](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)に関するページを参照してください。ここから最新の SQL Database コンプライアンス証明書の一覧を入手できます。

## <a name="next-steps"></a>次のステップ

- 単一データベースをすぐに使い始めるには、[単一データベースのクイック スタート ガイド](quickstart-content-reference-guide.md)から始めてください。
- SQL Server データベースを Azure に移行する方法については、「[Azure SQL Database に移行](migrate-to-database-from-sql-server.md)」を参照してください。
- サポートされている機能については、[機能](features-comparison.md)に関する記事をご覧ください。
