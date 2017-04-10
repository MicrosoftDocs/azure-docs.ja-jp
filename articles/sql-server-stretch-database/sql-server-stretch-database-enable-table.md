---
title: "デーブルの Stretch Database を有効にする | Microsoft Docs"
description: "Stretch Database のテーブルを設定する方法について説明します。"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 63724114-82c1-4b00-ac50-c3ade6a69d47
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/05/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3c9aa9a1abb4f436cb44e67d236682ff58a92531


---
# <a name="enable-stretch-database-for-a-table"></a>テーブルの Stretch Database を有効にする
Stretch Database のテーブルを設定するには、SQL Server Management Studio でテーブルの **[Stretch]、[有効化]** の順に選択し、**[Stretch テーブルを有効にする]** ウィザードを起動します。 Transact\-SQL を利用し、既存のテーブルで Stretch Database を有効にしたり、新しいテーブルを作成して Stretch Database を有効にしたりすることもできます。

* 別個のテーブルにコールド データを格納する場合、テーブル全体を移行できます。
* テーブルにホット データとコールド データの両方が含まれている場合、移行する行を選択するフィルター関数を指定できます。

**前提条件**。 テーブルに **[Stretch]、[有効化]** を選択したとき、データベースの Stretch Database を有効にしていない場合、ウィザードにより Stretch Database のデータベースが最初に設定されます。 このトピックの手順ではなく、「 [[Enable Database for Stretch (Stretch Database を有効にする)] ウィザードを実行する方法の概要](sql-server-stretch-database-wizard.md) 」の手順に従ってください。

**アクセス許可**。 データベースまたはテーブルで Stretch Database を有効にするには、db\_owner アクセス許可が必要です。 テーブルで Stretch Database を有効にするには、テーブルの ALTER アクセス許可も必要です。

> [!NOTE]
> 後で Strech Database を無効にする場合、テーブルまたはデータベースの Stretch Database を無効にしても、リモート オブジェクトは削除されないことに注意してください。 リモート テーブルまたはリモート データベースを削除する場合は、Microsoft Azure 管理ポータルを使用して削除する必要があります。 リモート オブジェクトを手動で削除するまで、Azure のコストが引き続き発生します。
> 
> 

## <a name="a-nameenablewizardtableause-the-wizard-to-enable-stretch-database-on-a-table"></a><a name="EnableWizardTable"></a>ウィザードを使用してテーブルで Stretch Database を有効にする
**ウィザードを起動する**

1. SQL Server Management Studio のオブジェクト エクスプローラーで、Stretch を有効にするテーブルを選択します。
2. 右クリックして **[Stretch]** を選択し、**[有効化]** を選択してウィザードを起動します。

**はじめに**

ウィザードの目的と前提条件を確認します。

**データベース テーブルを選択する**

有効にするテーブルが表示され、選択されていることを確認します。

テーブル全体を移行することも、ウィザードで単純なフィルター関数を指定することもできます。 別の種類のフィルター関数を使用して、移行する行を選択する場合は、次のいずれかの操作を行います。

* ウィザードを終了して ALTER TABLE ステートメントを実行し、テーブルの Stretch を有効にしてフィルター関数を指定する。
* ウィザードを終了してから、ALTER TABLE ステートメントを実行して、フィルター関数を指定します。 必要な手順については、「 [ウィザードの実行後にフィルター関数を追加する](sql-server-stretch-database-predicate-function.md#addafterwiz)」をご覧ください。

ALTER TABLE 構文については、このトピックの後の方で説明しています。

**まとめ**

入力した値とウィザードで選択したオプションを確認します。 **[完了]** を選択して、Stretch を有効にします。

**結果**

結果を確認します。

## <a name="a-nameenabletsqltableause-transact-sql-to-enable-stretch-database-on-a-table"></a><a name="EnableTSQLTable"></a>Transact\-SQL を使用してテーブルで Stretch Database を有効にする
Transact-SQL を利用し、既存のテーブルで Stretch Database を有効にしたり、新しいテーブルを作成して Stretch Database を有効にしたりできます。

### <a name="options"></a>オプション
CREATE TABLE または ALTER TABLE を実行し、テーブルで Stretch Database を有効にするとき、次のオプションを利用します。

* テーブルにホット データとコールド データの両方が含まれている場合、 `FILTER_PREDICATE = <function>` 句を使用して、移行する行を選択する関数を指定することもできます。 述語では、インライン テーブル値関数を呼び出す必要があります。 詳細については、 [フィルター関数を使用した移行する行の選択](sql-server-stretch-database-predicate-function.md)に関する記事をご覧ください。 フィルター関数を指定しない場合、テーブル全体が移行されます。
  
  > [!NOTE]
  > 指定したフィルター関数のパフォーマンスが悪いと、データ移行のパフォーマンスも悪くなります。 Stretch Database は CROSS APPLY 演算子を利用し、テーブルにフィルター関数を適用します。
  > 
  > 
* データ移行をすぐに開始する場合は `MIGRATION_STATE = OUTBOUND` を指定し、データ移行の開始を先送りする場合は `MIGRATION_STATE = PAUSED` を指定します。

### <a name="enable-stretch-database-for-an-existing-table"></a>既存テーブルの Stretch Database を有効にする
既存テーブルを Stretch Database 用に設定するには、ALTER TABLE コマンドを実行します。

テーブル全体を移行し、データ移行をすぐに開始する例を次に示します。

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <table name>  
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;  
GO
```
`dbo.fn_stretchpredicate` インライン テーブル値関数で特定された行のみを移行し、データ移行を先送りする例を次に示します。 フィルター関数の詳細については、 [移行する行の選択におけるフィルター関数の使用](sql-server-stretch-database-predicate-function.md)に関するページを参照してください。

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <table name>  
    SET ( REMOTE_DATA_ARCHIVE = ON (  
        FILTER_PREDICATE = dbo.fn_stretchpredicate(),  
        MIGRATION_STATE = PAUSED ) ) ;  
 GO
```

詳細については、「 [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)」をご覧ください。

### <a name="create-a-new-table-with-stretch-database-enabled"></a>新しいテーブルを作成し、Stretch Database を有効にする
新しいテーブルを作成し、Stretch Database を有効にするには、CREATE TABLE コマンドを実行します。

テーブル全体を移行し、データ移行をすぐに開始する例を次に示します。

```tsql
USE <Stretch-enabled database name>;
GO
CREATE TABLE <table name>
    ( ... )  
    WITH ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;  
GO
```

`dbo.fn_stretchpredicate` インライン テーブル値関数で特定された行のみを移行し、データ移行を先送りする例を次に示します。 フィルター関数の詳細については、 [移行する行の選択におけるフィルター関数の使用](sql-server-stretch-database-predicate-function.md)に関するページを参照してください。

```tsql
USE <Stretch-enabled database name>;
GO
CREATE TABLE <table name>
    ( ... )  
    WITH ( REMOTE_DATA_ARCHIVE = ON (  
        FILTER_PREDICATE = dbo.fn_stretchpredicate(),  
        MIGRATION_STATE = PAUSED ) ) ;  
GO  
```

詳細については、「 [CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx)」をご覧ください。

## <a name="see-also"></a>関連項目
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

[CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx)




<!--HONumber=Nov16_HO3-->


