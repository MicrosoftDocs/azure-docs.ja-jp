---
title: "監視とパフォーマンスのチューニング - Azure SQL Database | Microsoft Docs"
description: "評価と改善を通じて Azure SQL Database のパフォーマンスをチューニングするためのヒントを紹介します。"
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: "SQL のパフォーマンスのチューニング, データベース パフォーマンスのチューニング, SQL のパフォーマンスのチューニングのヒント, SQL Database のパフォーマンスのチューニング"
ms.assetid: eb7b3f66-3b33-4e1b-84fb-424a928a6672
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: v-shysun
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: 7a2f1199a56e0bd32eafef9f420879c756673e7f
ms.contentlocale: ja-jp
ms.lasthandoff: 06/14/2017


---
# <a name="monitoring-and-performance-tuning"></a>監視とパフォーマンスのチューニング

Azure SQL Database は、自動的に管理される柔軟なデータ サービスです。このサービスでは、使用状況の監視、リソース (CPU、メモリ、I/O) の追加と削除、データベースのパフォーマンス向上に役立つ推奨事項の検索を簡単に行うことができるほか、データベースをワークロードに適合させてパフォーマンスを自動的に最適化することもできます。

この記事では、Azure SQL Database で使用できる、監視およびパフォーマンス チューニングのオプションの概要を示します。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>データベースのパフォーマンスの監視とトラブルシューティング

Azure SQL Database を使用すると、簡単にデータベースの使用状況を監視し、パフォーマンスの問題を引き起こす可能性のあるクエリを特定することができます。 データベースのパフォーマンスは、Azure Portal またはシステム ビューを使用して監視できます。 データベースのパフォーマンスの監視とトラブルシューティングには、次のオプションを使用できます。

1. [Azure Portal](https://portal.azure.com) で、**[SQL データベース]** をクリックし、データベースを選択してから、監視グラフを使用して上限に近づいているリソースを見つけます。 DTU 消費は、既定で表示されます。 **[編集]** をクリックして、表示される時間の範囲と値を変更します。
2. [Query Performance Insight](sql-database-query-performance.md) を使用して、最もリソースを消費しているクエリを特定します。
3. 動的管理ビュー (DMV)、拡張イベント (`XEvents`)、SSMS のクエリ ストアを使用すると、リアルタイムでパフォーマンス パラメーターを取得できます。

これらのレポートまたはビューを使用して問題を特定した場合に Azure SQL Database のパフォーマンス向上に使用できる手法を探すには、[パフォーマンス ガイダンスのトピック](sql-database-performance-guidance.md)を参照してください。

> [!IMPORTANT] 
> 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。
>

## <a name="optimize-database-to-improve-performance"></a>データベースを最適化してパフォーマンスを向上させる

Azure SQL Database では、[パフォーマンス チューニングの推奨事項](sql-database-advisor.md)を確認することで、リソースを変更することなくクエリのパフォーマンスを改善および最適化する機会を見極めることができます。 データベースのパフォーマンスが悪い一般的な理由は、インデックスの欠落や最適化が不十分なクエリです。 このようなチューニングの推奨事項を適用すると、ワークロードのパフォーマンスを向上させることができます。
識別されたすべての推奨事項を適用し、それによってデータベースのパフォーマンスが向上することを確認することで、Azure SQL Database で[クエリのパフォーマンスを自動的に最適化](sql-database-automatic-tuning.md)することもできます。 データベースのパフォーマンス向上には、次のオプションを使用できます。

1. [SQL Database Advisor](sql-database-advisor-portal.md) を使用して、インデックスの作成と削除、クエリのパラメーター化、およびスキーマの問題の修正に関する推奨事項を表示します。
2. [自動チューニングを有効にします](sql-database-automatic-tuning-enable.md)。これにより、Azure SQL Database では、特定されたパフォーマンスの問題を自動的に修正できるようになります。

## <a name="improving-database-performance-with-more-resources"></a>リソースを増やしてデータベースのパフォーマンスを向上させる

最後に、データベースのパフォーマンス向上につながるすぐに実施可能な方法がない場合は、Azure SQL Database で使用できるリソースの量を変更することができます。 スタンドアロン データベースの[サービス レベル](sql-database-service-tiers.md)を変更するか、エラスティック プールの eDTU を増やすことにより、いつでもリソース割り当てを増やすことができます。
1. スタンドアロン データベースの場合は、[サービス レベルの変更](sql-database-service-tiers.md) をオンデマンドで行うことで、データベースのパフォーマンスを改善できます。
2. 複数のデータベースの場合は、リソースを自動的にスケーリングするための [エラスティック プール](sql-database-elastic-pool-guidance.md) の使用を検討してください。

## <a name="tune-and-refactor-application-or-database-code"></a>アプリケーションまたはデータベース コードをチューニングおよびリファクタリングする

データベースの使用、インデックスの変更、計画の強制的な適用、データベースをワークロードに手動で適合させるためのヒントの使用をより最適に行うために、アプリケーション コードを変更することができます。 手動によるチューニングとコードの書き直しに関するガイダンスとヒントについては、[パフォーマンス ガイダンスのトピック](sql-database-performance-guidance.md)を参照してください。


## <a name="next-steps"></a>次のステップ

- Azure SQL Database で自動チューニングを有効にし、自動チューニング機能でワークロードを完全に管理できるようにするには、「[Enable automatic tuning (自動チューニングの有効化)](sql-database-automatic-tuning-enable.md)」を参照してください。
- 手動チューニングを使用するには、[Azure Portal のチューニングに関する推奨事項](sql-database-advisor-portal.md)を確認したうえで、クエリのパフォーマンスを向上させる推奨事項を手動で適用してください。
- [Azure SQL Database のサービス レベル](sql-database-performance-guidance.md)を変更して、データベースで使用可能なリソースを変更します。
