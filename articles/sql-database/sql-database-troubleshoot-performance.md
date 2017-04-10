---
title: "SQL Database のパフォーマンスのチューニングのヒント | Microsoft Docs"
description: "評価と改善を通じて Azure SQL Database のパフォーマンスをチューニングするためのヒントを紹介します。"
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: "SQL のパフォーマンスのチューニング, データベース パフォーマンスのチューニング, SQL のパフォーマンスのチューニングのヒント, SQL Database のパフォーマンスのチューニング"
ms.assetid: eb7b3f66-3b33-4e1b-84fb-424a928a6672
ms.service: sql-database
ms.custom: monitor and tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 984adf244596578a3301719e5ac2f68a841153bf
ms.openlocfilehash: 3bfcaf4ae29d23754a19a61f2775d1b12e3e69ba
ms.lasthandoff: 02/16/2017


---
# <a name="sql-database-performance-tuning-tips"></a>SQL Database のパフォーマンスのチューニングのヒント
スタンドアロン データベースの [サービス レベル](sql-database-service-tiers.md) を変更するか、エラスティック プールの eDTU を増やすことで、いつでもパフォーマンスを高めることができますが、最初にクエリのパフォーマンスを改善または最適化する機会を特定する必要があります。 データベースのパフォーマンスが悪い一般的な理由は、インデックスの欠落や最適化が不十分なクエリです。 この記事では、SQL Database でのパフォーマンス チューニングに関するガイダンスを提供します。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="steps-to-evaluate-and-tune-database-performance"></a>データベースのパフォーマンスを評価し、チューニングする手順
1. [Azure ポータル](https://portal.azure.com)で、 **[SQL データベース]**をクリックし、データベースを選択してから、監視グラフを使用して上限に近づいているリソースを見つけます。 DTU 消費は、既定で表示されます。 **[編集]** をクリックして、表示される時間の範囲と値を変更します。
2. [Query Performance Insight](sql-database-query-performance.md) を使用して DTU を使用するクエリを評価し、その後 [SQL Database Advisor](sql-database-advisor.md) を使用して、インデックスの作成と削除、クエリのパラメーター化、およびスキーマの問題の解決に関する推奨事項を表示します。
3. 動的管理ビュー (DMV)、拡張イベント (Xevent)、および SSMS のクエリ ストアを使用すると、リアルタイムでパフォーマンス パラメーターを取得できます。 詳細な監視とチューニングに関するヒントについては、 [パフォーマンス ガイダンス トピック](sql-database-performance-guidance.md) を参照してください。

> [!IMPORTANT] 
> 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。
>

## <a name="steps-to-improve-database-performance-with-more-resources"></a>その他のリソースを含むデータベースのパフォーマンスを改善する手順
1. スタンドアロン データベースの場合は、[サービス レベルの変更](sql-database-service-tiers.md) をオンデマンドで行うことで、データベースのパフォーマンスを改善できます。
2. 複数のデータベースの場合は、リソースを自動的にスケーリングするための [エラスティック プール](sql-database-elastic-pool-guidance.md) の使用を検討してください。


