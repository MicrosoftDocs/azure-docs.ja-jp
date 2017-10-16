---
title: "Azure SQL Database - 自動チューニング | Microsoft Docs"
description: "Azure SQL Database は SQL クエリを分析し、ユーザーのワークロードに自動的に適応します。"
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/19/2017
ms.author: jovanpop
ms.openlocfilehash: 64abcf680199938c54be5005ee96670a8e580270
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL Database での自動チューニング

Azure SQL Database は完全に管理されたデータ サービスです。データベースで実行されるクエリを監視し、データベース ワークロードのパフォーマンスを自動的に向上させます。 Azure SQL Database には[自動チューニング](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) インテリジェンス メカニズムが組み込まれており、ワークロードにデータベースを動的に適応することで、クエリのパフォーマンスを自動的に調整して向上させることができます。 Azure SQL Database の自動チューニングは、クエリのパフォーマンスを最適化するために Azure SQL Database で有効にできる最も重要な機能の 1 つかもしれません。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="automatic-tuning-options"></a>自動チューニング オプション

Azure SQL Database で使用可能な[自動チューニング](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) オプションは次のとおりです。
 1. **CREATE INDEX**: ワークロードのパフォーマンスを向上させる可能性のあるインデックスを識別し、インデックスを作成してクエリのパフォーマンスを向上させることを確認します。
 2. **DROP INDEX**: 冗長および重複インデックスと、長期間使用されていないインデックスを識別します。
 3. **PLAN REGRESSION CORRECTION**: 以前の良好なプランより速度が低下した実行プランを使用している SQL クエリを識別し、後退したプランの代わりに最後に確認された良好なプランを使用します。

Azure SQL Database は、データベースを最適化できる **CREATE INDEX**、**DROP INDEX**、および **PLAN REGRESSION CORRECTION** の推奨事項を識別し、Azure Portal でそれらを表示します。 変更する必要があるインデックスの識別の詳細については、「[Azure Portal を使用した SQL Database Advisor](sql-database-advisor-portal.md)」参照してください。 ポータルを使用して推奨事項を手動で適用することも、Azure SQL Database で自動的に推奨事項を適用し、変更後のワークロードを監視して、推奨事項によりワークロードのパフォーマンスが向上したことを確認することもできます。

自動チューニング オプションをデータベースごとに個別にオンまたはオフにすることも、論理サーバーで構成し、サーバーから設定を継承するすべてのデータベースに適用することもできます。 サーバーでの[自動チューニング](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) オプションの構成と、サーバーのデータベースでの設定の継承は、自動チューニングを構成するための推奨される方法です。この方法では、多数のデータベースでの自動チューニング オプションの管理が簡素化されます。

こちらの記事で、Azure Portal を使用して[自動チューニングを有効にする](sql-database-automatic-tuning-enable.md)手順を確認してださい。

## <a name="next-steps"></a>次のステップ

- Azure SQL Database で自動チューニングを有効にし、自動チューニング機能でワークロードを完全に管理できるようにするには、「[Enable automatic tuning (自動チューニングの有効化)](sql-database-automatic-tuning-enable.md)」を参照してください。
- 手動チューニングを使用するには、[Azure Portal のチューニングに関する推奨事項](sql-database-advisor-portal.md)を確認したうえで、クエリのパフォーマンスを向上させる推奨事項を手動で適用してください。
- Azure SQL Database を調整する組み込みインテリジェンスの詳細については、[こちら](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)を参照してください。
- Azure SQL Database および SQL Server 2017 での自動チューニングの詳細については、[こちら](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)を参照してください。
