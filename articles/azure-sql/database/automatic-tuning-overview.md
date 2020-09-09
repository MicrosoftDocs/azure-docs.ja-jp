---
title: 自動チューニングの概要
description: Azure SQL Database および Azure SQL Managed Instance では、SQL クエリが分析され、ユーザーのワークロードに自動的に適応されます。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/30/2020
ms.openlocfilehash: d2a00be4d08a7a2dfa8e11a22593d017d184a368
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982713"
---
# <a name="automatic-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database および Azure SQL Managed Instance での自動チューニング
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database および Azure SQL Managed Instance の自動チューニングでは、AI と機械学習に基づく継続的なパフォーマンス チューニングによって、最大限のパフォーマンスと安定したワークロードが実現されます。

自動チューニングはフル マネージドのインテリジェントなパフォーマンス サービスであり、組み込みのインテリジェンスを使用してデータベースに対して実行されるクエリを継続的に監視し、パフォーマンスを自動的に改善します。 これは変動するワークロードに対してデータベースを適応させること、およびチューニングの推奨事項を適用することを動的に実施することで実現しています。 自動チューニングは、AI を介して Azure 上のすべてのデータベースから水平方向に学習し、チューニング操作を動的に改善します。 自動チューニングが有効な状態で Azure SQL Database を実行している時間が長くなるほど、パフォーマンスが向上します。

Azure SQL Database および Azure SQL Managed Instance の自動チューニングは、安定した最大限のデータベース ワークロードの実行を可能にするために利用できる機能の中で、最も重要な機能の 1 つと言えます。

## <a name="what-can-automatic-tuning-do-for-you"></a>自動チューニングでできること

- データベースのパフォーマンスの自動チューニング
- パフォーマンス向上の自動検証
- 自動ロールバックと自己修正
- チューニングの履歴
- 手動でデプロイするためのアクション Transact-SQL (T-SQL) スクリプトのチューニング
- ワークロードのパフォーマンスのプロアクティブな監視
- 数十万のデータベースのスケールアウト機能
- DevOps リソースと総保有コストに対する良い影響

## <a name="safe-reliable-and-proven"></a>安全性、信頼性、実証済み

Azure SQL Database のデータベースに適用されるチューニング操作は、最も負荷の高いワークロードのパフォーマンスに対して、安全性の高い操作になっています。 このシステムは、ユーザーのワークロードに干渉しないように注意して設計されています。 自動チューニングの推奨事項は、使用率が低い時間帯にのみ適用されます。 ワークロードのパフォーマンスを損なわないように、自動チューニング操作を、システムによって一時的に無効にすることもできます。 その場合、"システムによる無効化" メッセージが Azure portal に表示されます。 自動チューニングはリソースの優先順位が最も高いワークロードに注意を払います。

自動チューニングは非常に成熟したしくみですが、Azure で実行されている数百万のデータベース上でさらに洗練されたものになりました。 適用される自動チューニング操作は自動的に検証され、ワークロードのパフォーマンスに積極的な改善がみられるようにします。 パフォーマンスの後退に関する推奨事項が動的に検出され、すばやく以前の設定に戻されます。 記録されたチューニングの履歴を見ると、Azure SQL Database および Azure SQL Managed Instance 内の各データベースに対して実行されたチューニングによる改善の痕跡をはっきりと確認できます。

![自動チューニングのしくみ](./media/automatic-tuning-overview/how-does-automatic-tuning-work.png)

Azure SQL Database の自動チューニングの核となるロジックは、データベース エンジンの SQL Server 自動チューニング機能でも使用されています。 組み込みインテリジェンスのしくみに関する技術的な情報の詳細については、[SQL Server の自動チューニング](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)に関する記事をご覧ください。

自動チューニングのしくみの概要と一般的な使用シナリオについては、埋め込みのビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>自動チューニングの有効化

- [Azure SQL Database の自動チューニングは Azure portal で有効にするか](automatic-tuning-enable.md)、または [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) T-SQL ステートメントを使用します。
- Azure SQL Managed Instance の自動チューニングを有効にするには、[ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL ステートメントを使用します。

## <a name="automatic-tuning-options"></a>自動チューニング オプション

Azure SQL Database および Azure SQL Managed Instance で使用可能な自動チューニング オプションは次のとおりです。

| 自動チューニング オプション | 単一データベースとプールされたデータベースのサポート | インスタンス データベースのサポート |
| :----------------------------- | ----- | ----- |
| **CREATE INDEX** - ご自分のワークロードのパフォーマンスを向上させる可能性があるインデックスを特定し、インデックスを作成して、クエリのパフォーマンスが向上したことを自動的に確認します。 | はい | いいえ |
| **DROP INDEX** - 一意なインデックスを除く冗長なインデックスや重複するインデックス、また長期間 (90 日超) 使用されていないインデックスを特定します。 なお、このオプションはパーティション切り替えやインデックス ヒントを使用するアプリケーションと互換性がありません。 Premium および Business Critical サービスレベルでは、使われていないインデックスの削除はサポートされていません。 | はい | いいえ |
| **FORCE LAST GOOD PLAN** (自動プラン修正) - 以前の良好なプランよりも速度の低い実行プランを使用している Azure SQL クエリを特定し、その低速なプランの代わりに、最後に確認された良好なプランを使用してクエリを実行します。 | はい | はい |

### <a name="automatic-tuning-for-sql-database"></a>SQL Database の自動チューニング

Azure SQL Database の自動チューニングでは、**CREATE INDEX**、**DROP INDEX**、**FORCE LAST GOOD PLAN** データベース アドバイザーの推奨設定を使用して、データベースのパフォーマンスが最適化されます。 詳細については、[Azure portal での Database Advisor の推奨事項](database-advisor-find-recommendations-portal.md)、[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)、および [REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning) を参照してください。

Azure portal を使用してチューニングの推奨事項を手動で適用するか、または自動チューニングでチューニングの推奨事項を自律的に適用することができます。 システムで自律的にチューニングの推奨事項を適用する利点は、ワークロードのパフォーマンスが向上することが自動的に検証されることです。パフォーマンスの顕著な向上が検出されない場合、チューニングの推奨事項は自動的に元に戻されます。 頻繁に実行されないクエリがチューニングの推奨事項によって影響を受ける場合、設計上、検証フェーズに最大で 72 時間かかる可能性があることに注意してください。

T-SQL を使用してチューニングの推奨事項を適用する場合は、パフォーマンスの自動検証および取り消しメカニズムは使用できません。 そのような方法で適用された推奨事項は、24 時間から 48 時間、アクティブなままになり、チューニングの推奨事項の一覧に表示された後、 自動的に取り消されます。 推奨事項をすぐに削除する場合は、Azure portal から破棄できます。

自動チューニング オプションをデータベースごとに個別に有効または無効にすることも、サーバーレベルで構成し、サーバーから設定を継承するすべてのデータベースに適用することもできます。 サーバーでは、自動チューニングの設定について、Azure の既定値を継承できます。 現時点の Azure の既定値では、FORCE_LAST_GOOD_PLAN と CREATE_INDEX が有効に、DROP_INDEX が無効に設定されています。

> [!IMPORTANT]
> 2020 年 3 月より、自動チューニングに関する Azure の既定値の変更は次のように有効になります。
>
> - 新しい Azure の既定値では、FORCE_LAST_GOOD_PLAN が有効、CREATE_INDEX が無効、DROP_INDEX が無効になります。
> - 自動チューニング設定が構成されていない既存のサーバーは、新しい Azure の既定値を継承するように自動的に構成されます。 これは、現在未定義の状態の自動チューニングのサーバー設定があるすべてのお客様に適用されます。
> - 新しく作成されたサーバーは、新しい Azure の既定値を継承するように自動的に構成されます (新しいサーバーの作成時に自動チューニング構成が未定義の状態だった以前とは異なります)。

多数のデータベースの自動チューニング オプションの管理が簡素化されるため、自動チューニングを構成するために推奨される方法は、サーバーでの自動チューニング オプションの構成と、親サーバーに属するデータベースの設定の継承です。

自動チューニングの推奨情報に関するメール通知の作成については、「[自動チューニングの電子メール通知](automatic-tuning-email-notifications-configure.md)」を参照してください。

### <a name="automatic-tuning-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance の自動チューニング

SQL Managed Instance の自動チューニングでは、**FORCE LAST GOOD PLAN** のみがサポートされます。 T-SQL による自動チューニング オプションの構成の詳細については、[自動チューニングでの自動プラン修正の導入](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/)と[自動プラン修正](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- 自動チューニングに使用されている組み込みインテリジェンスの詳細については、「[人工知能による Azure SQL Database のチューニング](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)」をご覧ください。
- 自動チューニングが内部でどのように機能するかについては、[Microsoft Azure SQL Database での数百万のデータベースの自動インデックス作成](https://www.microsoft.com/research/uploads/prod/2019/02/autoindexing_azuredb.pdf)に関するページを参照してください。
