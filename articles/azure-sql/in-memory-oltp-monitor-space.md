---
title: XTP インメモリ ストレージの監視
description: XTP インメモリ ストレージの使用量と容量を推定し、監視します。また、容量不足エラー 41823 を解決します
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 5717c2479c1d894117bae44826a814c3cfeaa98a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96493391"
---
# <a name="monitor-in-memory-oltp-storage-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database と Azure SQL Managed Instance のインメモリ OLTP ストレージを監視する
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

[インメモリ OLTP](in-memory-oltp-overview.md) を使用している場合、メモリ最適化テーブルおよびテーブル変数内のデータは、インメモリ OLTP ストレージに格納されています。

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>データがインメモリ OLTP ストレージの上限に収まるかどうかを判断する

さまざまなサービス レベルのストレージの上限を確認します。 Premium および Business Critical サービス レベルには、それぞれインメモリ OLTP ストレージの最大サイズがあります。

- [DTU ベースのリソース制限 - 単一データベース](database/resource-limits-dtu-single-databases.md)
- [DTU ベースのリソース制限 - エラスティック プール](database/resource-limits-dtu-elastic-pools.md)
- [仮想コアベースのリソース制限 - 単一データベース](database/resource-limits-vcore-single-databases.md)
- [仮想コアベースのリソース制限 - エラスティック プール](database/resource-limits-vcore-elastic-pools.md)
- [vCore ベースのリソース制限 - マネージド インスタンス](managed-instance/resource-limits.md)

メモリ最適化テーブルのメモリ必要量の推定は、Azure SQL Database と Azure SQL Managed Instance で SQL Server の要件を推定する場合と同じように行います。 少し時間を取って[メモリ要件の見積もり](/sql/relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables)に関するページをご確認ください。

テーブル行とテーブル変数行、およびインデックスは、最大ユーザー データ サイズにカウントされます。 また、テーブル全体とそのインデックスの新しいバージョンを作成するには、ALTER TABLE に十分な領域が必要になります。

この上限を超えると、挿入操作と更新操作が失敗し始めることがあります。エラーは、Azure SQL Database の単一データベースと Azure SQL Managed Instance のデータベースの場合は 41823 で、Azure SQL Database のエラスティック プールの場合は 41840 です。 その場合は、データを削除してメモリを解放するか、データベースのサービス レベルまたはコンピューティング サイズをアップグレードする必要があります。

## <a name="monitoring-and-alerting"></a>監視とアラート

[Azure Portal](https://portal.azure.com/) で、インメモリ ストレージの使用量を、コンピューティング サイズのストレージ上限に対するパーセンテージとして監視できます。

1. [データベース] ブレードの [リソース使用率] ボックスで [編集] をクリックします。
2. メトリック `In-Memory OLTP Storage percentage` を選択します。
3. アラートを追加するには、[リソース使用率] チェック ボックスをオンにして [メトリック] ブレードを開き、[アラートの追加] をクリックします。

または、次のクエリを使用して、インメモリ ストレージの使用率を表示します。

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>インメモリ OLTP ストレージが不足する状況を修正する - エラー 41823 および 41840

データベースでインメモリ OLTP ストレージの上限に達すると、INSERT、UPDATE、ALTER、CREATE 操作が、エラー メッセージ 41823 (単一データベースの場合) またはエラー 41840 (エラスティック プールの場合) で失敗します。 どちらのエラーの場合も、アクティブなトランザクションが中止します。

エラー メッセージ 41823 および 41840 は、データベースまたはプールのメモリ最適化テーブルおよびテーブル変数が、インメモリ OLTP ストレージの最大サイズに達したことを示します。

このエラーを解決するには、次のいずれかを実行します。

- 従来のディスク ベース テーブルにデータをオフロードするなどして、メモリ最適化テーブルからデータを削除します。
- メモリ最適化テーブルにデータを残す必要がある場合は、十分なインメモリ ストレージがあるサービス階層にアップグレードします。

> [!NOTE]
> まれに、エラー 41823 および 41840 が一時的なものである場合があります。これは、利用できるインメモリ OLTP ストレージが十分にあり、操作の再試行が成功することを意味します。 したがって、使用可能なインメモリ OLTP ストレージの総量を監視し、かつ、エラー 41823 または 41840 が初めて発生した場合は再試行することをお勧めします。 再試行ロジックについて詳しくは、[インメモリ OLTP での競合の検出と再試行ロジック](/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic)に関する項目をご覧ください。

## <a name="next-steps"></a>次のステップ

管理のガイダンスについては、[動的管理ビューを使用した監視](database/monitoring-with-dmvs.md)に関するページをご覧ください。