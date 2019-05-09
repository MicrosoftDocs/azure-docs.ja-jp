---
title: パフォーマンスの監視と向上 - Azure SQL Database | Microsoft Docs
description: Azure SQL Database では、現在のクエリのパフォーマンスを向上させる余地がある領域を識別するのに役立つパフォーマンス ツールを提供します。
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: adbccd5f9cfd5ddd1912e304f800f3ebe04912c7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453010"
---
# <a name="monitor-and-improve-performance"></a>パフォーマンスの監視と向上

Azure SQL Database は、データベース内の潜在的な問題を識別し、インテリジェントなチューニング アクションと推奨事項を提供することによって、ワークロードのパフォーマンスの向上に役立つアクションを提案します。

データベースのパフォーマンスを確認するには、[概要] ページの **[パフォーマンス]** タイルを使用するか、[サポート + トラブルシューティング] セクションに移動します。

   ![ビュー パフォーマンス](./media/sql-database-performance/entries.png)

[サポート + トラブルシューティング] セクションでは、次のページを使用できます。


1. [[パフォーマンスの概要]](#performance-overview): データベースのパフォーマンスを監視します。 
2. [[パフォーマンスの推奨事項]](#performance-recommendations): ワークロードのパフォーマンスの向上に役立つパフォーマンスの推奨事項が表示されます。
3. [[Query Performance Insight]](#query-performance-insight): リソースの消費量が上位のクエリを見つけることができます。
4. [[自動チューニング]](#automatic-tuning): Azure SQL Database はデータベースを自動的に最適化できます。

## <a name="performance-overview"></a>パフォーマンスの概要

このビューにデータベースのパフォーマンスの概要が表示され、パフォーマンスのチューニングとトラブルシューティングを実行できます。 

![パフォーマンス](./media/sql-database-performance/performance.png)

* **[推奨事項]** タイルには、データベースのチューニングに関する推奨事項の内訳が表示されます (推奨事項が 4 つ以上ある場合は、上から 3 番目までが表示されます)。 このタイルをクリックすると、**[[パフォーマンスの推奨事項]](#performance-recommendations)** が表示されます。 
* **[Tuning activity]** タイルには、データベースに対して進行中のチューニング操作と完了したチューニング操作の概要が表示され、チューニング操作の履歴を簡単に確認できます。 このタイルをクリックすると、データベースのチューニング履歴がすべて表示されます。
* **[Auto-tuning]\(自動チューニング\)** タイルには、データベースの[自動チューニング構成](sql-database-automatic-tuning-enable.md) (データベースに自動的に適用されるチューニング オプション) が表示されます。 このタイルをクリックすると、自動構成のダイアログが開きます。
* **[Database queries]** タイルには、データベースのクエリ パフォーマンスの概要 (全体的な DTU 使用量と上位のリソース消費に関するクエリ) が表示されます。 このタイルをクリックすると、**[[Query Performance Insight]](#query-performance-insight)** が表示されます。

## <a name="performance-recommendations"></a>パフォーマンスに関する推奨事項

このページでは、データベースのパフォーマンス向上に役立つインテリジェントな[チューニングに関する推奨事項](sql-database-advisor.md)を紹介します。 このページには、次の種類の推奨事項が表示されます。

* 作成または削除するインデックスに関する推奨事項。
* データベースでスキーマの問題が識別された場合の推奨事項。
* パラメーター化されたクエリがクエリに役立つ場合の推奨事項。

![パフォーマンス](./media/sql-database-performance/recommendations.png)

また、過去に適用されたチューニング アクションの履歴すべてを表示することもできます。

パフォーマンスに関する推奨事項を確認および適用する方法については、「[Find and apply performance recommendations (パフォーマンスに関する推奨事項の確認および適用)](sql-database-advisor-portal.md)」を参照してください。

## <a name="automatic-tuning"></a>自動チューニング

Azure SQL Database では、[パフォーマンスの推奨事項](sql-database-advisor.md)を適用することにより、データベース パフォーマンスを自動的にチューニングできます。 詳細については、「[Automatic tuning (自動チューニング)](sql-database-automatic-tuning.md)」を参照してください。 これを有効にするには、[自動チューニングを有効にする方法](sql-database-automatic-tuning-enable.md)に関するページを参照してください。

## <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) を使用すると、短時間でデータベース パフォーマンスのトラブルシューティングを行うことができます。以下の情報が提供されます。

* データベース リソース (DTU) の消費量の詳細な情報。 
* CPU 消費の多いクエリ。パフォーマンス向上のためのチューニングの対象となる可能性があります。 
* クエリの詳細にドリルダウンする機能。 

  ![パフォーマンス ダッシュボード](./media/sql-database-query-performance/performance.png)

このページの詳細については、**[Query Performance Insight の使用方法](sql-database-query-performance.md)** に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [データベースが 1 台の場合の Azure SQL Database のパフォーマンス ガイダンス](sql-database-performance-guidance.md)
* [エラスティック プールの使用に適した状況](sql-database-elastic-pool-guidance.md)

