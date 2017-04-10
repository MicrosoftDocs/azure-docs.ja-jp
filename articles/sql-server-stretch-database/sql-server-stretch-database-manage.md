---
title: "Stretch Database の管理とトラブルシューティング | Microsoft Docs"
description: "Stretch Database の管理とトラブルシューティングの方法について説明します。"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 8a43c0fc-64d3-4042-8921-a36542aa8933
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 681ad472e53a17600b589d8354d9fdb5c9c3c574


---
# <a name="manage-and-troubleshoot-stretch-database"></a>Stretch Database の管理とトラブルシューティング
Stretch Database を管理し、問題を解決するには、このトピックで説明するツールと方法を利用します。

## <a name="manage-local-data"></a>ローカル データを管理する
### <a name="a-namelocalinfoaget-info-about-local-databases-and-tables-enabled-for-stretch-database"></a><a name="LocalInfo"></a>Stretch Database が有効になっているローカルのデータベースとテーブルに関する情報を取得する
Stretch が有効になっている SQL Server のデータベースとテーブルに関する情報を確認するには、カタログ ビューの **sys.databases** と **sys.tables** を開きます。 詳細については、「[sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx)」と「[sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx)」をご覧ください。

SQL Server で Stretch を有効にしたテーブルにより使用されている領域の量を表示するには、次のステートメントを実行します。

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## <a name="manage-data-migration"></a>データ移行を管理する
### <a name="check-the-filter-function-applied-to-a-table"></a>テーブルに適用されているフィルター関数を確認する
カタログ ビューの **sys.remote\_data\_archive\_tables** を開き、**filter\_predicate** 列の値を確認し、移行する行を選択するために Stretch Database で使用されている関数を特定します。 値が null の場合、テーブル全体が移行の対象になります。 詳細については、「[sys.remote_data_archive_tables (TRANSACT-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)」および[移行する行の選択におけるフィルター関数の使用](sql-server-stretch-database-predicate-function.md)に関する記事をご覧ください。

### <a name="a-namemigrationacheck-the-status-of-data-migration"></a><a name="Migration"></a>データ移行の状態を確認する
SQL Server Management Studio でデータベースに **[タスク]、[Stretch]、[監視]** を選択して、Stretch Database Monitor でデータ移行を監視します。 詳細については、「 [データ移行の監視とトラブルシューティング (Stretch Database)](sql-server-stretch-database-monitor.md)」を参照してください。

または、**sys.dm\_db\_rda\_migration\_status** 動的管理ビューを開き、移行されたデータのバッチ数と行数を確認します。

### <a name="a-namefirewallatroubleshoot-data-migration"></a><a name="Firewall"></a>データ移行のトラブルシューティング
トラブルシューティングのヒントについては、「 [データ移行の監視とトラブルシューティング (Stretch Database)](sql-server-stretch-database-monitor.md)」を参照してください。

## <a name="manage-remote-data"></a>リモート データを管理する
### <a name="a-nameremoteinfoaget-info-about-remote-databases-and-tables-used-by-stretch-database"></a><a name="RemoteInfo"></a>Stretch Database で使用されるリモートのデータベースとテーブルに関する情報を取得する
移行されたデータが保存されているリモートのデータベースとテーブルに関する情報を確認するには、**sys.remote\_data\_archive\_databases** カタログ ビューと **sys.remote\_data\_archive\_tables** カタログ ビューを開きます。 詳細については、「[sys.remote_data_archive_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx)」と「[sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)」をご覧ください。

Azure で Stretch を有効にしたテーブルにより使用されている領域の量を表示するには、次のステートメントを実行します。

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### <a name="delete-migrated-data"></a>移行したデータを削除する
既に Azure に移行されているデータを削除するには、[sys.sp_rda_reconcile_batch](https://msdn.microsoft.com/library/mt707768.aspx) に関する記事の手順に従います。

## <a name="manage-table-schema"></a>テーブル スキーマを管理する
### <a name="dont-change-the-schema-of-the-remote-table"></a>リモート テーブルのスキーマは変更しないでください。
Stretch Database に構成された SQL Server テーブルに関連付けられているリモート Azure テーブルのスキーマは変更しないでください。 特に、列の名前とデータ型は変更しないでください。 Stretch Database 機能では、さまざまな場面で、SQL Server テーブルのスキーマとの関連でリモート テーブルのスキーマを推測します。 リモート スキーマを変更すると、Stretch Database は変更されたテーブルの動作を停止します。

### <a name="reconcile-table-columns"></a>テーブル列を調整する
リモート テーブルから列を間違って削除してしまった場合は、**sp_rda_reconcile_columns** を実行し、Stretch が有効な SQL Server テーブルにあり、リモート テーブルにない列を追加します。 詳細については、[sys.sp_rda_reconcile_columns](https://msdn.microsoft.com/library/mt707765.aspx) に関する記事をご覧ください。  

> [!IMPORTANT]
> [!重要] 間違ってリモート テーブルから削除した列を **sp_rda_reconcile_columns** で再作成する場合、削除した列に以前に存在したデータは復元されません。
> 
> 

**sp_rda_reconcile_columns** を実行しても、リモート テーブルにあり、Stretch が有効な SQL Server テーブルにない列がリモート テーブルから削除されることはありません。 Stretch が有効な SQL Server テーブルにはもう存在しない列がリモート Azure テーブルにあるとしても、そのような列により Stretch Database の通常動作が妨げられることはありません。 余分な列は必要に応じて手動で削除できます。  

## <a name="manage-performance-and-costs"></a>パフォーマンスとコストを管理する
### <a name="troubleshoot-query-performance"></a>クエリ パフォーマンスのトラブルシューティング
Stretch 対応テーブルが含まれるクエリのパフォーマンスは、テーブルの Stretch を有効にする前に比べて遅くなります。 クエリのパフォーマンスが大幅に低下する場合、次の問題が考えられます。

* ご利用の Azure サーバーと SQL Server の地理的リージョンが異なっていませんか。 Azure サーバーと SQL Server の地理的リージョンを同じに設定し、ネットワークの待ち時間を減らしてください。
* ネットワークの状態が低下している可能性があります。 ネットワーク管理者に問い合わせ、最近、問題や機能停止が発生していないか確認してください。

### <a name="increase-azure-performance-level-for-resource-intensive-operations-such-as-indexing"></a>Azure のパフォーマンス レベルを上げ、インデックス作成など、リソースが集中的に利用される操作に対応する
Stretch Database に構成された大規模テーブルでインデックスを作成、再作成、再編成するとき、Azure でその間、移行されたデータに対して大量のクエリが予想される場合、その操作が続く間、対応するリモート Azure データベースのパフォーマンス レベルを上げることを検討してください。 パフォーマンス レベルと価格設定の詳細については、 [SQL Server Stretch Database の価格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)ページを参照してください。

### <a name="you-cant-pause-the-sql-server-stretch-database-service-on-azure"></a>Azure で SQL Server Stretch Database サービスを一時停止することはできません
 適切なパフォーマンス レベルと価格を選択してください。 リソースを集中的に利用する操作のために一時的にパフォーマンス レベルを上げる場合、操作の完了後、前のレベルに戻します。 パフォーマンス レベルと価格設定の詳細については、 [SQL Server Stretch Database の価格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)ページを参照してください。  

## <a name="change-the-scope-of-queries"></a>クエリの範囲を変更する
 Stretch が有効なテーブルにクエリを実行すると、既定ではローカル データとリモート データの両方が返されます。 すべてのユーザーによるすべてのクエリまたは管理者による 1 回だけのクエリを対象にクエリの範囲を変更できます。  

### <a name="change-the-scope-of-queries-for-all-queries-by-all-users"></a>すべてのユーザーによるすべてのクエリのクエリ範囲を変更する
 すべてのユーザーによるすべてのクエリの範囲を変更するには、**sys.sp_rda_set_query_mode** ストアド プロシージャを実行します。 ローカル データのみにクエリ範囲を制限したり、すべてのクエリを無効にしたり、初期設定を復元したりできます。 詳細については、[sys.sp_rda_set_query_mode](https://msdn.microsoft.com/library/mt703715.aspx) に関する記事をご覧ください。  

### <a name="a-namequeryhintsachange-the-scope-of-queries-for-a-single-query-by-an-administrator"></a><a name="queryHints"></a>管理者による 1 回だけのクエリのクエリ範囲を変更する
 db_owner ロールのメンバーによる 1 回だけのクエリの範囲を変更するには、SELECT ステートメントに **WITH \( REMOTE_DATA_ARCHIVE_OVERRIDE = *value* \)** クエリ ヒントを追加します。 REMOTE_DATA_ARCHIVE_OVERRIDE クエリ ヒントには、次の値が入ります。  

* **LOCAL_ONLY**:  ローカル データだけにクエリを実行します。  
* **REMOTE_ONLY**:  リモート データだけにクエリを実行します。  
* **STAGE_ONLY**:  Stretch Database が移行対象の行をステージングし、移行後、移行された行を指定の期間だけ保持するテーブルにあるデータのみにクエリを実行します。 このクエリ ヒントは、ステージング テーブルにクエリを実行する唯一の方法です。  

たとえば、次のクエリはローカル結果のみを返します。  

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="a-nameadminhintsamake-administrative-updates-and-deletes"></a><a name="adminHints"></a>管理者による更新と削除
 既定では、Stretch が有効なテーブルで、移行対象の行や既に移行されている行を更新したり、削除したりすることはできません。 問題を解消する必要がある場合、db_owner ロールのメンバーとして、ステートメントに **WITH \( REMOTE_DATA_ARCHIVE_OVERRIDE = *value* \)** クエリ ヒントを追加することで更新操作や削除操作を実行できます。 REMOTE_DATA_ARCHIVE_OVERRIDE クエリ ヒントには、次の値が入ります。  

* **LOCAL_ONLY**:  ローカル データのみを更新または削除します。  
* **REMOTE_ONLY**:  リモート データのみを更新または削除します。  
* **STAGE_ONLY**:  Stretch Database が移行対象の行をステージングし、移行後、移行された行を指定の期間だけ保持するテーブルにあるデータのみを更新または削除します。  

## <a name="see-also"></a>関連項目
[Stretch Database の監視](sql-server-stretch-database-monitor.md)

[Stretch 対応のデータベースをバックアップする](sql-server-stretch-database-backup.md)

[Stretch 対応のデータベースを復元する](sql-server-stretch-database-restore.md)




<!--HONumber=Nov16_HO3-->


