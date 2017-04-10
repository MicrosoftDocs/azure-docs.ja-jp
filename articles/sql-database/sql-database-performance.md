---
title: Azure SQL Database Performance Insight | Microsoft Docs
description: "Azure SQL Database では、現在のクエリのパフォーマンスを向上させる余地がある領域を識別するのに役立つパフォーマンス ツールを提供します。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: a60b75ac-cf27-4d73-8322-ee4d4c448aa2
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 071cc504cc4111e20391af86ffdc7819644ec434


---
# <a name="sql-database-performance-insight"></a>SQL Database Performance Insight
Azure SQL Database では、インテリジェントなチューニング操作と推奨事項を提供することにより、データベースのパフォーマンスを識別、向上するためのパフォーマンス ツールを提供します。 

1. [Azure Portal](http://portal.azure.com) でデータベースを参照し、**[All settings]** > **[パフォーマンス]** > **[概要]** の順にクリックし、**[パフォーマンス]**ページを開きます。 
2. **[Recommendations]** をクリックして [[SQL Database Advisor]](#sql-database-advisor) を開き、**[Queries]** をクリックして [[Query Performance Insight]](#query-performance-insight) を開きます。
   
    ![ビュー パフォーマンス](./media/sql-database-performance/entries.png)

## <a name="performance-overview"></a>パフォーマンスの概要
**[Overview]** か **[Performance]** タイルをクリックすると、データベースのパフォーマンス ダッシュボードが表示されます。 このビューにデータベースのパフォーマンスの概要が表示され、パフォーマンスのチューニングとトラブルシューティングを実行できます。 

![[パフォーマンス]](./media/sql-database-performance/performance.png)

* **[Recommendations]** タイルには、データベースのチューニングに関する推奨事項の内訳が表示されます (推奨事項が 4 つ以上ある場合は、上から 3 番目までが表示されます)。 このタイルをクリックすると、 **[SQL Database Advisor]**が表示されます。 
* **[Tuning activity]** タイルには、データベースに対して進行中のチューニング操作と完了したチューニング操作の概要が表示され、チューニング操作の履歴を簡単に確認できます。 このタイルをクリックすると、データベースのチューニング履歴がすべて表示されます。
* **[Auto-tuning]** タイルには、データベースの自動チューニング構成 (どのチューニング操作をデータベースに自動的に適用するか) が表示されます。 このタイルをクリックすると、自動構成のダイアログが開きます。
* **[Database queries]** タイルには、データベースのクエリ パフォーマンスの概要 (全体的な DTU 使用量と上位のリソース消費に関するクエリ) が表示されます。 このタイルをクリックすると、 **[Query Performance Insight]**が表示されます。

## <a name="sql-database-advisor"></a>[SQL Database Advisor]
[SQL Database Advisor](sql-database-advisor.md) では、データベースのパフォーマンスの向上に役立つインテリジェントなチューニングの推奨事項を提供します。 

* 作成または削除の対象となるインデックスの推奨事項 (およびユーザーが介入することなく自動的にインデックスの推奨事項を適用するためのオプション、パフォーマンスに悪影響を与える推奨事項を自動的にロールバックするためのオプション)。
* データベースでスキーマの問題が識別された場合の推奨事項。
* パラメーター化されたクエリがクエリに役立つ場合の推奨事項。

## <a name="query-performance-insight"></a>[Query Performance Insight]
[Query Performance Insight](sql-database-query-performance.md) を使用すると、短時間でデータベース パフォーマンスのトラブルシューティングを行うことができます。以下の情報が提供されます。

* データベース リソース (DTU) の消費量の詳細な情報。 
* CPU 消費の多いクエリ。パフォーマンス向上のためのチューニングの対象となる可能性があります。 
* クエリの詳細にドリルダウンする機能。 

## <a name="additional-resources"></a>その他のリソース
* [データベースが&1; 台の場合の Azure SQL Database のパフォーマンス ガイダンス](sql-database-performance-guidance.md)
* [エラスティック プールの使用に適した状況](sql-database-elastic-pool-guidance.md)




<!--HONumber=Dec16_HO2-->


