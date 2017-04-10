---
title: "Azure Data Warehouse を復元する - ローカル冗長と geo 冗長 | Microsoft Docs"
description: "Azure SQL Data Warehouse でデータベースを復旧するためのデータベース復元オプションの概要。"
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: 3e01c65c-6708-4fd7-82f5-4e1b5f61d304
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 43ab6a2f71ab51c50847b1ba5249f51c48e03fea
ms.openlocfilehash: 104986e88ededf2137725fe258b6ce51f608b37d
ms.lasthandoff: 01/24/2017


---
# <a name="sql-data-warehouse-restore"></a>SQL Data Warehouse の復元
> [!div class="op_single_selector"]
> * [概要][Overview]
> * [ポータル][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

SQL Data Warehouse には、データ ウェアハウスの障害復旧機能の一部として、ローカル バックアップからの復元と地理的バックアップからの復元の両方が用意されています。 データ ウェアハウスをプライマリ リージョンの復元ポイントに復元するには、データ ウェアハウスのバックアップを使用します。別のリージョンに復元するには、geo 冗長バックアップを使用します。 この記事では、データ ウェアハウスの復元の詳細について説明します。

## <a name="what-is-a-data-warehouse-restore"></a>データ ウェアハウスの復元とは
データ ウェアハウスの復元とは、既存または削除済みのデータ ウェアハウスのバックアップから作成された新しいデータ ウェアハウスのことです。 復元されたデータ ウェアハウスは、特定の時点でバックアップされたデータ ウェアハウスを再作成します。 SQL Data Warehouse は分散システムなので、データ ウェアハウスの復元は Azure BLOB に保存されている多くのファイルから作成されます。 

データの不慮の破損または削除から保護するデータベース復元は、ビジネス継続性および障害復旧戦略の最も重要な部分です。

詳細については、次を参照してください。

* [SQL Data Warehouse のバックアップ](sql-data-warehouse-backups.md)
* [ビジネス継続性の概要](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>データ ウェアハウスの復元ポイント
Azure Premium Storage を使用する利点として、SQL Data Warehouse は Azure Storage BLOB のスナップショットを使用して、プライマリ データ ウェアハウスをバックアップします。 各スナップショットには、スナップショットの開始時刻を表す復元ポイントがあります。 データ ウェアハウスを復元するには、復元ポイントを選択して restore コマンドを実行します。  

SQL Data Warehouse は、常に新しいデータ ウェアハウスにバックアップを復元します。 復元されたデータ ウェアハウスと現在のデータ ウェアハウスの両方を保持するか、いずれか&1; つを削除することができます。 現在のデータ ウェアハウスを復元されたデータ ウェアハウスに置き換える場合は、名前を変更します。

削除済みまたは一時停止中のデータ ウェアハウスを復元する必要がある場合は、[サポート チケットを作成](sql-data-warehouse-get-started-create-support-ticket.md)します。 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>geo 冗長復元
geo 冗長ストレージを使用している場合は、異なるリージョンにある[ペアのデータ センター](../best-practices-availability-paired-regions.md)にデータ ウェアハウスを復元できます。 データ ウェアハウスは、最新の日次バックアップから復元されます。 

## <a name="restore-timeline"></a>タイムライン復元
過去&7; 日間の任意の復元ポイントにデータベースを復元できます。 スナップショットは&4; ～&8; 時間ごとに開始され、7 日間使用できます。 スナップショットが&7; 日間以上経過した場合、期限切れとなり、復元ポイントは使用できなくなります。

## <a name="restore-costs"></a>復元コスト
復元されたデータ ウェアハウスのストレージ料金は、Azure Premium Storage レートで請求されます。 

復元されたデータ ウェアハウスを一時停止した場合、Azure Premium Storage レートでストレージに対して課金されます。 一時停止の利点は、DWU コンピューティング リソースに対しては課金されないことです。

SQL Data Warehouse の価格の詳細については、「[SQL Data Warehouse の価格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)」をご覧ください。

## <a name="uses-for-restore"></a>復元の用途
データ ウェアハウスの復元の主な用途は、偶発的なデータの消失や破損が生じた際にデータを回復することです。

バックアップを&8; 日以上保持する場合にも、データ ウェアハウスの復元を使用できます。 バックアップが復元されたら、データ ウェアハウスをオンラインにして、データベースを無期限に一時停止してコンピューティング コストを節約することができます。 一時停止したデータベースについては、Azure Premium Storage レートでストレージに対して課金されます。 

## <a name="related-topics"></a>関連トピック
### <a name="scenarios"></a>シナリオ
* ビジネス継続性の概要については、「[Azure SQL Database によるビジネス継続性の概要](../sql-database/sql-database-business-continuity.md)」をご覧ください。

<!-- ### Tasks -->

データ ウェアハウスの復元を実行する方法:

* Azure Portal の場合、[Azure Portal を使用したデータ ウェアハウスの復元](sql-data-warehouse-restore-database-portal.md)に関するページをご覧ください。
* PowerShell コマンドレットの場合、[PowerShell コマンドレットを使用したデータ ウェアハウスの復元](sql-data-warehouse-restore-database-powershell.md)に関するページをご覧ください。
* REST API の場合、[REST API を使用したデータ ウェアハウスの復元](sql-data-warehouse-restore-database-rest-api.md)に関するページをご覧ください。

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->

