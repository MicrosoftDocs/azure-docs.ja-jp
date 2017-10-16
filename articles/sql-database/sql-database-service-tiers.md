---
title: "Azure SQL Database サービス | Microsoft Docs"
description: "SQL Database のサービス レベルについて説明します"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/20/2017
ms.author: carlrab
ms.openlocfilehash: 0183e9c60da7c216a6268dfec3874eb76270f71d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Azure SQL Database サービス レベル概要

[Azure SQL Database](sql-database-technical-overview.md) では、[単一データベース](sql-database-single-database-resources.md)と[エラスティック プール](sql-database-elastic-pool.md)の両方に対し、**Basic**、**Standard**、**Premium**、**Premium RS** の各サービス レベルが提供されます。 サービス レベルは、主に、パフォーマンス レベルとストレージ サイズの選択の範囲および価格によって区別されます。  すべてのサービス レベルでは、パフォーマンス レベルとストレージのサイズを柔軟に変更できます。  単一データベースとエラスティック プールは、サービス レベル、パフォーマンス レベル、およびストレージのサイズに基づいて、時間単位で課金されます。   

## <a name="choosing-a-service-tier"></a>サービス階層の選択

サービス レベルの選択は、主に、ビジネス継続性、ストレージ、およびパフォーマンスの要件に依存します。
| | **Basic** | **Standard** |**Premium** |**Premium RS** |
| :-- | --: |--:| --:| --:| 
|対象のワークロード|開発、運用|開発、運用|開発、運用|サービス障害による最大 5 分までのデータ損失を許容できるワークロード|
|アップタイム SLA|99.99%|99.99%|99.99%|プレビューは対象外|
|バックアップ保有期間|7 日|35 日|35 日|35 日|
|CPU|低|低、中、高|中、高|中|
|IO スループット|低  | 中 | Standard より 1 桁高い|Premium と同じ|
|IO 待機時間|Premium より高い|Premium より高い|Basic および Standard より低い|Premium と同じ|
|列ストア インデックス作成とインメモリ OLTP|該当なし|該当なし|サポートされています|サポートされています|
|||||

## <a name="performance-level-and-storage-size-limits"></a>パフォーマンス レベルとストレージ サイズの制限

パフォーマンス レベルは、単一データベースの場合はデータベース トランザクション ユニット (DTU) で、エラスティック プールの場合はエラスティック データベース トランザクション ユニット (eDTU) で表されます。 DTU と eDTU の詳細については、[DTU と eDTU の概要](sql-database-what-is-a-dtu.md)に関する記事を参照してください。

### <a name="single-databases"></a>Single Database

|  | **Basic** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| 最大ストレージ サイズ* | 2 GB | 1 TB (テラバイト) | 4 TB  | 1 TB (テラバイト)  |
| 最大 DTU | 5 | 3000 | 4000 | 1,000 |
||||||

### <a name="elastic-pools"></a>エラスティック プール

| | **Basic** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| データベースあたりの最大ストレージ サイズ*  | 2 GB | 1 TB (テラバイト) | 1 TB (テラバイト) | 1 TB (テラバイト) |
| プールあたりの最大ストレージ サイズ* | 156 GB | 4 TB | 4 TB | 1 TB (テラバイト) |
| データベースあたりの最大 eDTU 数 | 5 | 3000 | 4000 | 1,000 |
| プールあたりの最大 eDTU 数 | 1600 | 3000 | 4000 | 1,000 |
| プールあたりのデータベースの最大数 | 500  | 500 | 100 | 100 |
||||||

> [!IMPORTANT]
> \* 付属のストレージ容量を超えるストレージ サイズはプレビュー段階であり、追加料金が適用されます。 詳細については、「 [SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。 
>
> \* 現在、Premium レベルでは、米国東部 2、米国西部、米国政府バージニア、西ヨーロッパ、ドイツ中部、東南アジア、東日本、オーストラリア東部、カナダ中部、およびカナダ東部の各リージョンにおいて、1 TB を超えるストレージが利用できます。 [P11 ～ P15 の現時点での制限](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)に関するページを参照してください。  
> 

選択できる具体的なパフォーマンス レベルとストレージ サイズについて詳しくは、「[Azure SQL Database のリソース制限](sql-database-resource-limits.md)」をご覧ください。


## <a name="next-steps"></a>次のステップ

- [単一データベースのリソース](sql-database-single-database-resources.md)について学習してください。
- [エラスティック プール](sql-database-elastic-pool.md)について学習してください。
- [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)について学習してください。
* [DTU と eDTU](sql-database-what-is-a-dtu.md) の詳細について学習してください。
* DTU の使用状況の監視については、「[監視とパフォーマンスのチューニング](sql-database-troubleshoot-performance.md)」をご覧ください。

