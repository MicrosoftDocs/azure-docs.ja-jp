---
title: SQL Data Warehouse へのソリューションの移行 | Microsoft Docs
description: Azure SQL Data Warehouse プラットフォームにソリューションを組み込むための移行ガイダンス。
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: 04c921282d3591e7326d326c230bf72e7f5c1812
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313023"
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse にソリューションを移行する
既存のデータベース ソリューションを Azure SQL Data Warehouse に移行するときに行うことについて説明します。 

## <a name="profile-your-workload"></a>ワークロードをプロファイリングする
移行の前に、SQL Data Warehouse がワークロードの適切なソリューションであることを確認する必要があります。 SQL Data Warehouse は、大規模なデータの分析を目的として設計された分散システムです。  SQL Data Warehouse に移行するには、理解するのはそれほど難しくありませんが、実装するには時間がかかる場合がある設計変更が必要です。 ビジネスでエンタープライズ クラスのデータ ウェアハウスが必要な場合は、労力に見合うだけの利点があります。 一方、SQL Data Warehouse ほどの能力を必要としない場合は、SQL Server または Azure SQL Database を使う方が高い費用対効果を得られます。

次のような場合は、SQL Data Warehouse の使用を検討してください。
- テラバイトに達するデータが 1 つでもある
- 大量のデータに対する分析の実行を計画している
- コンピューティングと記憶域をスケーリングできる必要がある 
- 必要のないときはコンピューティング リソースを一時停止してコストを節約したい

次のような特徴の運用 (OLTP) ワークロードには SQL Data Warehouse を使わないでください。
- 読み取りと書き込みの頻度が高い
- 単一選択の数が多い
- 単一行挿入の量が多い
- 行単位の処理が必要である
- 形式に互換性がない (JSON、XML)


## <a name="plan-the-migration"></a>移行を計画する

既存のソリューションを SQL Data Warehouse に移行することを決めたら、作業を始める前に移行の計画を立てることが重要です。 

計画の目的の 1 つは、データ、テーブル スキーマ、コードに SQL Data Warehouse と互換性があることを確認することです。 現在のシステムと SQL Data Warehouse の間には、回避する必要のある互換性の相違がいくつかあります。 さらに、Azure に大量のデータを移行するには時間がかかります。 慎重に計画すれば、Azure へのデータ移行時間を短縮できます。 

計画のもう 1 つの目的は、SQL Data Warehouse の設計目標である高いクエリ パフォーマンスをソリューションが確実に利用するように、設計を調整することです。 拡張性を考えてデータ ウェアハウスを設計すると、さまざまな設計パターンが導入されるため、従来の手法が常に最適であるとは限りません。 移行の後で一部の設計を調整することはできますが、プロセスですぐに変更を行うと、後で時間を節約できます。

移行を成功させるには、テーブルのスキーマ、コード、およびデータを移行する必要があります。 これらの移行トピックに関するガイダンスについては、以下をご覧ください。

-  [スキーマの移行](sql-data-warehouse-migrate-schema.md)
-  [コードの移行](sql-data-warehouse-migrate-code.md)
-  [データの移行](sql-data-warehouse-migrate-data.md) 

<!--
## Perform the migration


## Deploy the solution


## Validate the migration

-->

## <a name="next-steps"></a>次の手順
CAT (Customer Advisory Team) には、ブログを通じて発行する SQL Data Warehouse の優れたガイダンスもあります。  移行に関するその他のガイダンスについては、「[Migrating data to Azure SQL Data Warehouse in practice][Migrating data to Azure SQL Data Warehouse in practice]」(Azure SQL Data Warehouse へのデータの実践移行) の記事を参照してください。

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/
