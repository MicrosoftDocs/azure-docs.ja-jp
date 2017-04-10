---
title: "Premium Storage への既存の Azure データ ウェアハウスの移行 | Microsoft Docs"
description: "Premium Storage へ既存のデータ ウェアハウスを移行する手順"
services: sql-data-warehouse
documentationcenter: NA
author: happynicolle
manager: barbkess
editor: 
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 11/29/2016
ms.author: rortloff;barbkess
translationtype: Human Translation
ms.sourcegitcommit: e66f808da8d301e0adc393ba0ae67ab8618ce814
ms.openlocfilehash: e73e52665dd22e33054745907613c269b6d57915
ms.lasthandoff: 12/16/2016


---
# <a name="migrate-your-data-warehouse-to-premium-storage"></a>Premium Storage へのデータ ウェアハウスの移行
Azure SQL Data Warehouse では最近、パフォーマンス予測能力の向上を目的として [Premium Storage][premium storage for greater performance predictability] が導入されました。 これにより、現在 Standard Storage 上に存在するデータ ウェアハウスを Premium Storage に移行できるようになりました。 移行は自動で行われますが、移行のタイミングを制御する必要がある場合は、手動で行うこともできます (移行の際にはダウンタイムが生じます)。

データ ウェアハウスが複数ある場合は、[自動移行スケジュール][automatic migration schedule]を参照して、それらの移行時期についても確認してください。

## <a name="determine-storage-type"></a>ストレージの種類を確認する
次に示す日付より前にデータ ウェアハウスを作成した場合、現在は Standard Storage を使用していることになります。

| **[リージョン]** | **この日付より前にデータ ウェアハウスを作成** |
|:--- |:--- |
| オーストラリア東部 |Premium Storage はまだ利用できません |
| 中国 (東部) |2016 年 11 月 1 日 |
| 中国 (北部) |2016 年 11 月 1 日 |
| ドイツ中部 |2016 年 11 月 1 日 |
| ドイツ北東部 |2016 年 11 月 1 日 |
| インド西部 |Premium Storage はまだ利用できません |
| 西日本 |Premium Storage はまだ利用できません |
| 米国中北部 |2016 年 11 月 10 日 |

## <a name="automatic-migration-details"></a>自動移行の詳細
データベースの移行は、既定では[自動移行スケジュール][automatic migration schedule]に示した期間内の、各リージョンの現地時刻で午後 6 時から午前 6 時の間に行われます。 移行中は、既存のデータ ウェアハウスが使用できなくなります。 移行にかかる時間は、各データ ウェアハウスで 1 TB (テラバイト) ストレージあたり約 1 時間です。 また、自動移行中のどの段階においても課金は行われません。

> [!NOTE]
> 移行が完了すると、データ ウェアハウスはオンラインに戻ります。
>
>

Microsoft は、次の手順に従って移行を完了します (お客様による操作は必要ありません) 。 この例では、現在 Standard Storage 上に "MyDW" という名前のデータ ウェアハウスが存在していると仮定します。

1. Microsoft により、この名前は "MyDW" から "MyDW_ DO_NOT_USE_[タイムスタンプ]" に変更されます。
2. Microsoft により、"MyDW_DO_NOT_USE_[タイムスタンプ]" が一時停止されます。 この期間中に、バックアップが実行されます。 この処理の実行中に何らかの問題が発生すると、一時停止と再開が何度も繰り返される可能性があります。
3. Microsoft により、手順 2 で作成したバックアップから "MyDW" という名前の新しいデータ ウェアハウスが Premium Storage 上に作成されます。 "MyDW" は、復元が完了するまで表示されません。
4. 復元が完了すると、"MyDW" は移行前と同じ DWU に戻り、一時停止またはアクティブ状態になります。
5. 移行が完了すると、Microsoft によって "MyDW_DO_NOT_USE_[タイムスタンプ]" が削除されます。

> [!NOTE]
> 移行処理の一部として、次の各設定が引き継がれることはありません。
>
> * データベース レベルでの監査は再度有効化する必要があります。
> * データベース レベルのファイアウォール規則は再追加する必要があります。 サーバー レベルのファイアウォール規則は影響を受けません。
>
>

### <a name="automatic-migration-schedule"></a>自動移行スケジュール
自動移行は、次の停止スケジュール期間内の午後 6 時～午前 6 時 (該当するリージョンの現地時刻) の間に行われます。

| **[リージョン]** | **開始予定日** | **終了予定日** |
|:--- |:--- |:--- |
| オーストラリア東部 |未定 |未定 |
| 中国 (東部) |2017 年 1 月 9 日 |2017 年 1 月 13 日 |
| 中国 (北部) |2017 年 1 月 9 日 |2017 年 1 月 13 日 |
| ドイツ中部 |2017 年 1 月 9 日 |2017 年 1 月 13 日 |
| ドイツ北東部 |2017 年 1 月 9 日 |2017 年 1 月 13 日 |
| インド西部 |未定 |未定 |
| 西日本 |未定 |未定 |
| 米国中北部 |2017 年 1 月 9 日 |2017 年 1 月 13 日 |

## <a name="self-migration-to-premium-storage"></a>Premium Storage への手動移行
ダウンタイムの発生するタイミングを制御する必要がある場合は、以下の手順に従って Standard Storage 上の既存のデータ ウェアハウスを Premium Storage に移行することができます。 手動移行を選択した場合、対象のリージョンで自動移行が開始される前に、手動移行を完了する必要があります。 そうすることで、自動移行によって競合が引き起こされる危険を回避することができます (「[自動移行スケジュール][automatic migration schedule]」を参照)。

### <a name="self-migration-instructions"></a>手動移行の手順
データ ウェアハウスを手動で移行するには、バックアップと復元の機能を使用します。 移行処理の復元部分にかかる時間は、各データ ウェアハウスで 1 TB (テラバイト) ストレージあたり約 1 時間と予想されています。 移行後に同じ名前を保持する必要がある場合は、[移行時の名前変更][steps to rename during migration]の手順に従ってください。

1. データ ウェアハウスを[一時停止][Pause]します。 自動バックアップが表示されます。
2. 最新のスナップショットから[復元][Restore]します。
3. Standard Storage 上の既存のデータ ウェアハウスを削除します。 **この手順を行わないと、両方のデータ ウェアハウスに対して課金されることになります。**

> [!NOTE]
> 移行処理の一部として、次の各設定が引き継がれることはありません。
>
> * データベース レベルでの監査は再度有効化する必要があります。
> * データベース レベルのファイアウォール規則は再追加する必要があります。 サーバー レベルのファイアウォール規則は影響を受けません。
>
>

#### <a name="rename-data-warehouse-during-migration-optional"></a>移行中にデータ ウェアハウスの名前を変更する (オプション)
同じ論理サーバー上の 2 つのデータベースが同じ名前を持つことはできません。 SQL Data Warehouse で、データ ウェアハウスの名前の変更機能がサポートされるようになりました。

この例では、現在 Standard Storage 上に "MyDW" という名前のデータ ウェアハウスが存在していると仮定します。

1. 次の ALTER DATABASE コマンドを使用して、"MyDW" を変更します (この例では、"MyDW_BeforeMigration" に変更します)。このコマンドにより、既存のトランザクションがすべて終了します。この操作を成功させるには、master データベース内で行う必要があります。
   ```
   ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
   ```
2. "MyDW_BeforeMigration" を[一時停止][Pause]します。 自動バックアップが表示されます。
3. 最新のスナップショットから、以前の名前 ("MyDW" など) の新しいデータベースを[復元][Restore]します。
4. "MyDW_BeforeMigration" を削除します。 **この手順を行わないと、両方のデータ ウェアハウスに対して課金されることになります。**


## <a name="next-steps"></a>次のステップ
Premium Storage の変更により、データ ウェアハウスの基になるアーキテクチャでデータベース BLOB ファイルの数も増えています。 この変更のパフォーマンスの利点を最大化するには、以下のスクリプトを使用して、クラスター化列ストア インデックスを再構築します。 このスクリプトにより、既存のデータの一部が追加の BLOB に転送されます。 何も行わない場合、データ ウェアハウスのテーブルに読み込むデータが増えるにつれて、データは自然に再配布されます。

**前提条件:**

- データ ウェアハウスは 1,000 DWU 以上で実行する必要があります (「[コンピューティング能力のスケーリング][scale compute power]」セクションを参照)。
- このスクリプトを実行するユーザーは、[mediumrc ロール][mediumrc role]以上である必要があります。 このロールにユーザーを追加するには、次のコマンドを実行します。    ````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create table to control index rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go

--------------------------------------------------------------------------------
-- Step 2: Execute index rebuilds. If script fails, the below can be re-run to restart where last left off.
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Clean up table created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

データ ウェアハウスで問題が発生した場合は、[サポート チケットを作成][create a support ticket]し、考えられる原因を "Premium Storage への移行" としてください。

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md#pause-compute
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[mediumrc role]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com

