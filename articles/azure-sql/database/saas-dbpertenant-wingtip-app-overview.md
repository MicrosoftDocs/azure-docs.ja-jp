---
title: マルチテナント アプリの例 - Wingtip SaaS
description: Azure SQL Database を使用するマルチテナント アプリケーションのサンプルを使用して、Wingtip SaaS の例について説明します
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 0efc8105295ca7f97e6f465071c31b193173c5cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588732"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Azure SQL Database によるテナントごとのデータベース パターンを使用するマルチテナント SaaS アプリケーションの概要
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Wingtip SaaS アプリケーションは、サンプルのマルチテナント アプリです。 このアプリは、テナントごとにデータベースを使用する SaaS アプリケーション パターンで、複数のテナントにサービスを提供します。 このアプリは、さまざまな SaaS の設計と管理のパターンを使用する SaaS シナリオを可能にする Azure SQL Database の機能を紹介します。 このアプリをすぐに使い始めるために、Wingtip SaaS アプリは 5 分以内でデプロイできます。

アプリケーションのソース コードと管理スクリプトは、[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub リポジトリから入手できます。 開始する前に、Wingtip Tickets 管理スクリプトをダウンロードしてブロック解除する手順に関する[一般的なガイダンス](saas-tenancy-wingtip-app-guidance-tips.md)を参照してください。

## <a name="application-architecture"></a>アプリケーションのアーキテクチャ

Wingtip SaaS アプリでは、テナントごとのデータベース モデルを使用します。 SQL エラスティック プールを使用して効率を最大化します。 テナントをプロビジョニングしてデータにマッピングするために、カタログ データベースが使用されます。 主要な Wingtip SaaS アプリケーションは、3 つのサンプル テナントと 1 つのカタログ データベースを備えたプールを使用します。 カタログとテナントのサーバーが DNS エイリアスでプロビジョニングされています。 これらの別名は、Wingtip アプリケーションによって使用されているアクティブなリソースへの参照を維持するために使用されます。 これらの別名は、災害復旧のチュートリアルで回復のリソースへのポイントに更新されます。 Wingtip SaaS チュートリアルの多くを完了すると、初期デプロイにアドオンが追加されます。 分析データベースやデータベース間のスキーマ管理などのアドオンが導入されます。


![Wingtip SaaS のアーキテクチャ](./media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


チュートルアルを実行してアプリを操作する際は、SaaS パターンに注目します。これは、SaaS パターンがデータ層に関連付けられているためです。 つまり、データ層に注目し、アプリ自体を過剰に分析しないでください。 これらの SaaS パターンの実装を理解することは、アプリケーションでのこれらのパターンの実装にとって極めて重要です。 特定のビジネス要件を満たすために必要な変更についても検討します。

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL Database Wingtip SaaS のチュートリアル

アプリをデプロイしたら、初期デプロイを前提とする以下のチュートリアルを参照してください。 これらのチュートリアルで、SQL Database、Azure Synapse Analytics、およびその他の Azure サービスに組み込まれた機能を活用する一般的な SaaS パターンを確認できます。 チュートリアルには、PowerShell スクリプトと詳細な説明が含まれています。 説明を参照することで、同じ SaaS 管理パターンの理解とアプリケーションでの実装が容易になります。


| チュートリアル | 説明 |
|:--|:--|
| [Azure SQL Database のマルチテナント SaaS アプリ例のガイダンスとヒント](saas-tenancy-wingtip-app-guidance-tips.md) | PowerShell スクリプトをダウンロードして実行し、アプリケーションの各部分を準備します。 |
|[Wingtip SaaS アプリケーションをデプロイして調査する](./saas-dbpertenant-get-started-deploy.md)|  Wingtip SaaS アプリケーションをお使いの Azure サブスクリプションにデプロイして調査します。 |
|[テナントのプロビジョニングおよびカタログ登録を行う](./saas-dbpertenant-provision-and-catalog.md)| アプリケーションがカタログ データベースを使用してテナントに接続する方法と、カタログがテナントを各自のデータにマッピングする方法について説明します。 |
|[パフォーマンスを監視および管理する](./saas-dbpertenant-performance-monitoring.md)| SQL Database の監視機能の使用方法と、パフォーマンスのしきい値を超過した場合のアラートの設定方法について説明します。 |
|[Azure Monitor ログを使用した監視](./saas-dbpertenant-log-analytics.md) | [Azure Monitor ログ](../../azure-monitor/logs/log-query-overview.md)を使用して、複数のプールにある大量のリソースを監視する方法を説明します。 |
|[シングル テナントを復元する](./saas-dbpertenant-restore-single-tenant.md)| テナント データベースを前のポイント イン タイムに復元する方法を説明します。 既存のテナント データベースをオンラインにしたまま、並列のデータベースに復元する手順についても説明します。 |
|[テナント データベースのスキーマの管理に関するページ](saas-tenancy-schema-management.md)| すべてのテナント データベースに対してスキーマの更新と参照データの更新を行う方法について説明します。 |
|[テナント間で分散クエリを実行する](saas-tenancy-cross-tenant-reporting.md) | アドホック分析データベースを作成し、すべてのテナントに対して実行されるリアルタイムの分散クエリを実行します。  |
|[抽出されたテナント データに対して分析を実行する](saas-tenancy-tenant-analytics.md) | オフライン分析クエリに対応する分析データベースまたはデータ ウェアハウスにテナント データを抽出します。 |


## <a name="next-steps"></a>次のステップ

- [Wingtip Tickets SaaS サンプル アプリをデプロイして使用する際の一般的なガイダンスとヒント](saas-tenancy-wingtip-app-guidance-tips.md)
- [Wingtip SaaS アプリケーションのデプロイ](./saas-dbpertenant-get-started-deploy.md)