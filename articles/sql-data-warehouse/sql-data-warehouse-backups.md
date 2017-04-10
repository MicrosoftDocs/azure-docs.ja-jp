---
title: "Azure SQL Data Warehouse のバックアップ - スナップショット、地理冗長 | Microsoft Docs"
description: "Azure SQL Data Warehouse を復元ポイントまたは別の地理的な領域に復元できる、SQL Data Warehouse の組み込みデータベースのバックアップについて説明します。"
services: sql-data-warehouse
documentationcenter: 
author: lakshmi1812
manager: jhubbard
editor: 
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 43ab6a2f71ab51c50847b1ba5249f51c48e03fea
ms.openlocfilehash: 94b92f05af30734de727a12fd99271aa9769723a
ms.lasthandoff: 01/24/2017


---
# <a name="sql-data-warehouse-backups"></a>SQL Data Warehouse のバックアップ
SQL Data Warehouse には、データ ウェアハウスのバックアップ機能の一部として、ローカル バックアップと地理的バックアップの両方が用意されています。 これらのバックアップには、Azure Storage BLOB のスナップショットと地理冗長ストレージが含まれます。 データ ウェアハウスをプライマリ リージョンの復元ポイントに復元する、または別の地理的リージョンに復元するには、データ ウェアハウスのバックアップを使用します。 この記事では、SQL Data Warehouse のバックアップの詳細について説明します。

## <a name="what-is-a-data-warehouse-backup"></a>データ ウェアハウスのバックアップとは
データ ウェアハウスのバックアップは、データ ウェアハウスを特定の時間に復元するために使用できるデータです。  SQL Data Warehouse は分散システムなので、データ ウェアハウスのバックアップは Azure BLOB に保存されている多くのファイルで構成されます。 

データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性および障害復旧戦略の最も重要な部分です。 詳細については、「[Azure SQL Database によるビジネス継続性の概要](../sql-database/sql-database-business-continuity.md)」をご覧ください。

## <a name="data-redundancy"></a>データの冗長性
SQL Data Warehouse では、ローカル冗長 (LRS) の Azure Premium Storage にデータを保存して、データを保護します。 この Azure Storage 機能は、複数の同期されたデータ コピーがローカル データ センターに保持され、ローカルで障害が発生した場合には透過的なデータ保護が保証されます。 データの冗長性により、ディスク障害などのインフラストラクチャの問題でデータを失う心配がなくなります。 データの冗長性を確保することで、フォールト トレラントで可用性の高いインフラストラクチャによるビジネス継続性が実現できます。

関連情報:

* Azure Premium Storage については、[Azure Premium Storage の概要](../storage/storage-premium-storage.md)に関するページをご覧ください。
* ローカルの冗長ストレージの詳細については、[Azure Storage のレプリケーション](../storage/storage-redundancy.md#locally-redundant-storage)に関するページをご覧ください。

## <a name="azure-storage-blob-snapshots"></a>Azure Storage BLOB のスナップショット
Azure Premium Storage を使用する利点として、SQL Data Warehouse は Azure Storage BLOB のスナップショットを使用して、ローカルでデータ ウェアハウスをバックアップします。 データ ウェアハウスをスナップショットの復元ポイントに復元できます。 スナップショットは最小&8; 時間ごとに開始され、7 日間使用できます。  

関連情報:

* Azure BLOB のスナップショットについては、「[BLOB のスナップショットの作成](../storage/storage-blob-snapshots.md)」をご覧ください。

## <a name="geo-redundant-backups"></a>地理冗長のバックアップ
24 時間ごとに、SQL Data Warehouse は、Standard ストレージに完全なデータ ウェアハウスを保存します。 完全なデータ ウェアハウスは、最新のスナップショットの時間に一致するように作成されます。 標準ストレージは、読み取りアクセス (RA GRS) を持つ地理冗長ストレージ アカウントに属しています。 Azure Storage RA-GRS 機能は、バックアップ ファイルを [ペアのデータ センター](../best-practices-availability-paired-regions.md)にレプリケートします。 この geo レプリケーションにより、プライマリ リージョンのスナップショットにアクセスできない場合でも、データ ウェアハウスを復元できます。 

この機能は、既定で装備されています。 geo 冗長バックアップを使用しない場合は [使用を中止] することもできます (https://docs.microsoft.com/powershell/resourcemanager/Azurerm.sql/v2.1.0/Set-AzureRmSqlDatabaseGeoBackupPolicy?redirectedfrom=msdn)。 

> [!NOTE]
> Azure Storage の " *レプリケーション* " という用語は、ある場所から別の場所にファイルをコピーすることを表します。 SQL の " *データベース レプリケーション* " は、複数のセカンダリ データベースとプライマリ データベースとの同期を保つことを意味します。 
> 
> 

関連情報:

* geo 冗長ストレージの詳細については、「 [Azure Storage のレプリケーション](../storage/storage-redundancy.md)」を参照してください。
* RA-GRS ストレージの詳細については、「 [読み取りアクセス geo 冗長ストレージ](../storage/storage-redundancy.md#read-access-geo-redundant-storage)」を参照してください。

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>データ ウェアハウスのバックアップのスケジュールとリテンション期間
SQL Data Warehouse では、4 ～&8; 時間ごとにオンラインのデータ ウェアハウスにスナップショットを作成し、各スナップショットを&7; 日間保持します。 過去&7; 日間の復元ポイントのいずれかに、オンラインのデータベースを復元できます。 

最新のスナップショットが開始された時間を表示するには、オンラインの SQL Data Warehouse で次のクエリを実行します。 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

スナップショットを7 日以上保持する必要がある場合は、復元ポイントを新しいデータ ウェアハウスに復元することができます。 復元が完了すると、SQL Data Warehouse は新しいデータ ウェアハウスでスナップショットの作成を開始します。 新しいデータ ウェアハウスに変更を加えなければ、スナップショットは空のままとなるため、スナップショットのコストが最小限になります。 また、データベースを一時停止して、SQL Data Warehouse がスナップショットを作成することを防ぐこともできます。

### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>データ ウェアハウスの一時停止中、バックアップの保有期間に影響はありますか?
データ ウェアハウスの一時停止中、SQL Data Warehouse ではスナップショットが作成されないため、スナップショットの有効期限は切れません。 データ ウェアハウスが一時停止されている間、スナップショットの有効期間は変更されません。 スナップショットの保持は、データ ウェアハウスがオンラインになっている日数に基づきます。カレンダーの日数ではありません。

たとえば、スナップショットが 10 月 1 日午後 4 時に開始され、データ ウェアハウスが 10 月 3 日午後 4 時に一時停止された場合、スナップショットは 2 日間保持された状態です。 データ ウェアハウスがいつオンラインに戻っても、スナップショットは&2; 日の状態です。 データ ウェアハウスが 10 月 5 日午後 4 時にオンラインになった場合、スナップショットは 2 日の状態であるため、あと 5 日間保持されます。

データ ウェアハウスがオンラインに戻ると、SQL Data Warehouse は新しいスナップショットを再開し、データが&7; 日以上経過すると、スナップショットの有効期限が切れます。

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>ドロップしたデータ ウェアハウスのリテンション期間
データ ウェアハウスをドロップすると、データ ウェアハウスとスナップショットは&7; 日間保存され、その後削除されます。 データ ウェアハウスを保存された任意の復元ポイントに復元できます。

> [!IMPORTANT]
> 論理的な SQL Server インスタンスを削除すると、そのインスタンスに属するデータベースもすべて削除されます。これを回復することはできません。 削除されたサーバーを復元することはできません。
> 
> 

## <a name="data-warehouse-backup-costs"></a>データ ウェアハウスのバックアップのコスト
プライマリ データ ウェアハウスと&7; 日間の Azure BLOB のスナップショットの総コストは、TB 単位で四捨五入されます。 たとえば、データ ウェアハウスが 1.5 TB で、スナップショットが 100 GB を使用する場合、Azure Premium Storage の料金で 2 TB のデータが課金されます。 

> [!NOTE]
> 各スナップショットは最初は空で、プライマリ データ ウェアハウスに変更を加えるたびに増加します。 データ ウェアハウスが変更されると、すべてのスナップショットのサイズが増加します。 そのため、スナップショットのストレージ コストは、変化率に合わせて増加します。
> 
> 

地理冗長ストレージを使用している場合は、個別のストレージ料金がかかります。 geo 冗長ストレージは、標準の読み取りアクセスの地理冗長ストレージ (RA-GRS) の料金で課金されます。

SQL Data Warehouse の価格の詳細については、「[SQL Data Warehouse の価格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)」をご覧ください。

## <a name="using-database-backups"></a>データベースのバックアップを使用する
SQL データ ウェアハウスのバックアップの主な用途は、リテンション期間内のいずれかの復元ポイントに、データ ウェアハウスを復元することです。  

* SQL Data Warehouse を復元するには、「[Azure SQL Data Warehouse の復元](sql-data-warehouse-restore-database-overview.md)」をご覧ください。

## <a name="related-topics"></a>関連トピック
### <a name="scenarios"></a>シナリオ
* ビジネス継続性の概要については、「[Azure SQL Database によるビジネス継続性の概要](../sql-database/sql-database-business-continuity.md)」をご覧ください。

<!-- ### Tasks -->

* データ ウェアハウスを復元するには、「[Azure SQL Data Warehouse の復元](sql-data-warehouse-restore-database-overview.md)」をご覧ください。

<!-- ### Tutorials -->


