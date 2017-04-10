---
title: "SQL Data Warehouse へのソリューションの移行 | Microsoft Docs"
description: "Azure SQL Data Warehouse プラットフォームにソリューションを組み込むための移行ガイダンス。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 198365eb-7451-4222-b99c-d1d9ef687f1b
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 79a2cb3739ebb13792a60a9b55761a054bf89e7a


---
# <a name="migrate-your-solution-to-sql-data-warehouse"></a>SQL Data Warehouse へのソリューションの移行
SQL Data Warehouse は、ニーズに合わせて柔軟に拡張できる分散データベース システムです。 パフォーマンスと拡張性の両方を維持するために、SQL Server 内のすべての機能が SQL Data Warehouse に実装されるわけではありません。 次の移行に関するトピックでは、SQL Data Warehouse にソリューションを移行する際のいくつかの重要な要素について説明します。 拡張性を考えてデータ ウェアハウスを設計すると、さまざまな設計パターンが導入されるため、従来の手法が常に最適であるとは限りません。 そのため、既存のソリューションを採用すると、SQL Data Warehouse によって提供される分散プラットフォームを最大限に活用できる場合もあります。

また、SQL Data Warehouse は Microsoft Azure に基づいたプラットフォームであることに留意することも重要です。 このため、移行の一環としてクラウドへのデータ転送が含まれる場合もよくあります。 データ転送はそれ自体が問題になるものであり、特に容量が増加する場合には慎重に検討する必要があります。 データ転送とデータの読み込みは別々のトピックです。

## <a name="migration-guidance"></a>移行ガイダンス
移行を開始する前に、次の記事を読んで製品の相違点と基本的な概念を必ず理解しておいてください。

* [スキーマの移行][スキーマの移行]
* [データの移行][データの移行]
* [コードの移行][コードの移行]

## <a name="next-steps"></a>次のステップ
また、CAT (Customer Advisory Team) には、ブログを通じて発行する SQL Data Warehouse の優れたガイダンスもあります。  移行に関する追加のガイダンスについては、[「Azure SQL Data Warehouse へのデータの実践移行」][Migrating data to Azure SQL Data Warehouse in practice (Azure SQL Data Warehouse へのデータの実践移行) (Azure SQL Data Warehouse へのデータの実践移行)] の記事をご覧ください。

<!--Image references-->

<!--Article references-->
[スキーマの移行]: sql-data-warehouse-migrate-schema.md
[データの移行]: sql-data-warehouse-migrate-data.md
[コードの移行]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice (Azure SQL Data Warehouse へのデータの実践移行) (Azure SQL Data Warehouse へのデータの実践移行)]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/



<!--HONumber=Nov16_HO3-->


