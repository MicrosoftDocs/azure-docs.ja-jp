---
title: スケーラブルなクラウド データベースの構築
description: Elastic Database クライアント ライブラリでスケーラブルな .NET データベース アプリを構築します。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/25/2018
ms.openlocfilehash: bfe5dc00ba0255520c04ea85157f0b8bdc71b590
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84038513"
---
# <a name="building-scalable-cloud-databases"></a>スケーラブルなクラウド データベースの構築
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database 用のスケーラブルなツールと機能を使用して、データベースのスケール アウトを簡単に実現できます。 具体的には、 **Elastic Database クライアント ライブラリ** を使用して、スケールアウトされたデータベースを作成および管理できます。 この機能により、数百から数千ものデータベースを Azure SQL Database で使用し、シャード化されたアプリケーションを簡単に開発できます。

ダウンロードの対象:

* Java バージョンのライブラリは、[Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools) をご覧ください。
* .NET バージョンのライブラリは、[NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) をご覧ください。

## <a name="documentation"></a>ドキュメント

1. [Elastic Database ツールの概要](elastic-scale-get-started.md)
2. [Elastic Database の機能](elastic-scale-introduction.md)
3. [シャード マップの管理](elastic-scale-shard-map-management.md)
4. [既存のデータベースを移行してスケールアウト](elastic-convert-to-use-elastic-tools.md)
5. [データ依存ルーティング](elastic-scale-data-dependent-routing.md)
6. [マルチシャード クエリ](elastic-scale-multishard-querying.md)
7. [Elastic Database ツールを使用してシャードを追加する](elastic-scale-add-a-shard.md)
8. [Elastic Database ツールと行レベルのセキュリティを使用したマルチテナント アプリケーション](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)
9. [クライアント ライブラリ アプリのアップグレード](elastic-scale-upgrade-client-library.md) 
10. [エラスティック クエリの概要](elastic-query-overview.md)
11. [エラスティック データベース ツールの用語集](elastic-scale-glossary.md)
12. [Entity Framework による Elastic Database クライアント ライブラリ](elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Dapper を使用した Elastic Database クライアント ライブラリ](elastic-scale-working-with-dapper.md)
14. [Split-Merge ツール](elastic-scale-overview-split-and-merge.md)
15. [シャード マップ マネージャーのパフォーマンス カウンター](elastic-database-client-library.md) 
16. [Elastic Database ツールに関する FAQ](elastic-scale-faq.md)

## <a name="client-capabilities"></a>クライアントの機能

" *シャーディング* " を使用したアプリケーションのスケーリングにより、開発者と管理者の双方に課題が生じます。 クライアント ライブラリでは、開発者と管理者の両方がスケールアウトされたデータベースを管理できるツールが提供されることで、管理タスクが簡略化されます。 一般的な例では、「シャード」と呼ばれる多数の管理用データベースがあります。 顧客は同じデータベースに共存し、かつ顧客ごとに 1 つのデータベースが存在します (シングル テナント構成)。 クライアント ライブラリには、次の機能が含まれています。

- **シャード マップの管理**: 「シャード マップ マネージャー」と呼ばれる特別なデータベースが作成されます。 シャード マップの管理は、アプリケーションでシャードに関するメタデータを管理する機能です。 開発者は、この機能を使用して、データベースをシャードとして登録したり、個々のシャーディング キーまたはキー範囲の当該データベースへのマッピングを記述したりできます。さらに、このメタデータを管理して、容量の変更に伴ってデータベースの数および構成を発展させることができます。 Elastic Database クライアント ライブラリがないと、シャーディングを実装するにあたり管理コードの記述に多大な時間を費やすことになります。 詳細については、「 [シャード マップの管理](elastic-scale-shard-map-management.md)に移動します。

- **データ依存ルーティング**:アプリケーションに要求が送信されるとします。 アプリケーションでは、要求のシャーディング キー値に基づいて、キーの値に基づく適切なデータベースを特定する必要があります。 その後、要求を処理するデータベースへの接続が開かれます。 データ依存ルーティングでは、アプリケーションのシャード マップへの単一の簡単な呼び出しで接続を開くことができます。 インフラストラクチャ コードのもう 1 つの領域であるデータ依存ルーティングは、現在、Elastic Database クライアント ライブラリの機能でカバーされるようになりました。 詳細については、「 [データ依存ルーティング](elastic-scale-data-dependent-routing.md)」を参照してください。
- **マルチシャード クエリ (MSQ)** :マルチシャード クエリ実行は、要求にいくつか (またはすべて) のシャードが含まれる場合に機能します。 マルチシャード クエリは、同じ T-SQL コードをすべてのシャードまたはシャードのセットで実行します。 参加しているシャードからの結果は、UNION ALL セマンティクスを使用して全体的な結果セットにマージされます。 この機能は、クライアント ライブラリを介して公開され、接続管理、スレッド管理、エラー処理、中間結果処理などの多くのタスクを処理します。 MSQ では、最大で数百個のシャードを照会できます。 詳細については、「 [マルチシャード クエリ実行](elastic-scale-multishard-querying.md)」を参照してください。

一般に、独自のセマンティクスを持つシャード間操作とは異なり、Elastic Database ツールを使用するお客様は、シャードにローカルな操作を送信するときに完全な T-SQL 機能を取得することを期待できます。



## <a name="next-steps"></a>次のステップ

- Elastic Database クライアント ライブラリ ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22)、[.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) - ライブラリを **ダウンロード** します。

- [Elastic Database ツールの概要](elastic-scale-get-started.md) - クライアントの機能がわかる **サンプル アプリ** を試します。

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md)、[.NET](https://github.com/Azure/elastic-db-tools)) - コードの作成に寄与します。
- [Azure SQL Database のエラスティック クエリの概要](elastic-query-overview.md) - エラスティック クエリを使います。

- [スケールアウトされたクラウド データベース間のデータ移動](elastic-scale-overview-split-and-merge.md) - **Split-Merge ツール** の使い方です。



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

