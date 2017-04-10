---
title: "Azure SQL Database のバックアップから&1; つのテーブルを復元する | Microsoft Docs"
description: "Azure SQL Database のバックアップから&1; つのテーブルを復元する方法を説明します。"
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: 340b41bd-9df8-47fb-adfc-03216de38a5e
ms.service: sql-database
ms.custom: migrate and move
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2017
ms.author: daleche
translationtype: Human Translation
ms.sourcegitcommit: 9992b6a2bf73fd84c7c47783d1f4f13e10889a81
ms.openlocfilehash: 8c074243db2ae729c4fd1a483e5ac40fbbebd750


---
# <a name="how-to-restore-a-single-table-from-an-azure-sql-database-backup"></a>Azure SQL Database のバックアップから&1; つのテーブルを復元する方法
SQL Database のデータを誤って変更してしまい、影響を受けた&1; つのテーブルを回復することが必要になる場合があります。 この記事では、SQL Database [自動バックアップ](sql-database-automated-backups.md)のいずれかからデータベース内の&1; つのテーブルを復元する方法を説明します。

## <a name="preparation-steps-rename-the-table-and-restore-a-copy-of-the-database"></a>準備手順: テーブルの名前を変更し、データベースのコピーを復元する
1. Azure SQL Database で、復元したコピーで置き換えるテーブルを識別します。 テーブルの名前を変更するには、Microsoft SQL Management Studio を使用します。 たとえば、&lt;テーブル名&gt;_old のような名前に変更します。
   
   > [!NOTE]
   > ブロックされるのを防ぐため、名前を変更するテーブルでアクティビティが実行されていないことを確認します。 問題が発生する場合は、メンテナンス期間中にこの手順を実行するようにしてください。
   >

2. [ポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)手順を使用して、データベースのバックアップを復元したい時点に復元します。
   
   > [!NOTE]
   > 復元されたデータベースの名前は、"データベース名 + タイムスタンプ" という形式になります (例: **Adventureworks2012_2016-01-01T22-12Z**)。 この手順では、サーバー上の既存のデータベース名は上書きされません。 これは安全のためであり、ユーザーは現在のデータベースを削除して実稼働用に復元されたデータベースの名前を変更する前に、復元されたデータベースを確認できます。
   
## <a name="copying-the-table-from-the-restored-database-by-using-the-sql-database-migration-tool"></a>SQL Database 移行ツールを使用して、復元されたデータベースからテーブルをコピーする

1. [SQL Database 移行ウィザード](https://sqlazuremw.codeplex.com)をダウンロードしてインストールします。
2. SQL Database 移行ウィザードを開き、**[処理の選択]** ページの **[分析と移行] の下にある [データベース]** を選択して、**[次へ]** をクリックします。

   ![SQL Database 移行ウィザード - プロセスの選択](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)

3. **[サーバーへの接続]** ダイアログで、次の設定を適用します。

   * [サーバー名]: **SQL Server 名**
   * [認証]: **[SQL Server 認証]**
   * [ログイン]: **ログイン名**
   * [パスワード]: **パスワード**
   * [データベース]: **[Master DB (データベース一覧を表示する)]**
   
   > [!NOTE]
   > 既定では、ウィザードによってログイン情報が保存されます。 保存したくない場合は、 **[ログイン情報を忘れてください]**を選択します。
   >
   
     ![SQL Database 移行ウィザード - ソースの選択 - ステップ 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. **[移行元の選択]** ダイアログ ボックスで、「**準備手順**」セクションで復元したデータベースの名前を移行元として選択し、**[次へ]** をクリックします。
   
    ![SQL Database 移行ウィザード - ソースの選択 - ステップ 2](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)
5. **[オブジェクトの選択]** ダイアログ ボックスで **[スクリプト化するオブジェクトの選択]** オプションを選択し、対象サーバーに移行するテーブル (複数選択可) を選択します。
   ![SQL Database 移行ウィザード - オブジェクトの選択](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)
6. **[スクリプトウィザード設定確認]** ページで、SQL スクリプト生成の準備ができたことの確認を求められたら、**[はい]** をクリックします。 後で使用するために TSQL スクリプトを保存するオプションもあります。
   ![SQL Database 移行ウィザード - スクリプト ウィザードの概要](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)
7. **[結果]** ページで、**[次へ]** をクリックします。
   ![SQL Database 移行ウィザード - 結果の概要](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)
8. **[対象サーバーへの接続情報の設定]** ページで、**[サーバーへ接続]** をクリックし、詳細を次のように入力します。
   
   * **[サーバー名]**: 対象サーバーのインスタンス
   * **認証**: **SQL Server 認証**。 ログイン資格情報を入力します。
   * **[Database]**: **[Master DB (データベース一覧を表示する)]**。 ターゲット サーバー上のすべてのデータベースが一覧表示されます。
     
     ![SQL Database 移行ウィザード - ターゲット サーバー接続のセットアップ](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)
9. **[接続]** をクリックし、テーブルの移行先とする対象データベースを選択して、**[次へ]** をクリックします。 以前に生成されたスクリプトの実行が終了し、ターゲット データベースに新たにコピーされたテーブルが表示されます。

## <a name="verification-step"></a>確認手順

- 新しくコピーしたテーブルをクエリしてテストし、データが正しいことを確認します。 確認できたら、「**準備手順**」セクションで名前を変更したテーブルを削除できます  (例: &lt;テーブル名&gt;_old)。

## <a name="next-steps"></a>次のステップ
[SQL Database 自動バックアップ](sql-database-automated-backups.md)




<!--HONumber=Jan17_HO3-->


