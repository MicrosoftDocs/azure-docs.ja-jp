---
title: "データベースの Stretch Database を有効にする | Microsoft Docs"
description: "Stretch Database のデータベースを設定する方法について説明します。"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 55fc4142-4be9-4664-8ea9-48a5e177838f
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/05/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c6d3a81c3c919e50c123cef00dfb3e4a5ab23a44


---
# <a name="enable-stretch-database-for-a-database"></a>データベースの Stretch Database を有効にする
Stretch Database の既存のデータベースを設定するには、SQL Server Management Studio でデータベースの **[タスク]、[Stretch]、[有効化]** の順に選択し、**[Stretch Database を有効にする]** ウィザードを起動します。 Transact\-SQL を利用し、データベースの Stretch Database を有効にすることもできます。

個々のテーブルに **[タスク]、[Stretch]、[有効化]** を選択したとき、Stretch Database のデータベースを有効にしていない場合、ウィザードにより Stretch Database のデータベースが設定され、プロセスの一環としてテーブルを選択できるようになります。 「 [テーブルの Stretch Database を有効にする](sql-server-stretch-database-enable-database.md)」の手順ではなく、このトピックの手順に従ってください。

データベースまたはテーブルで Stretch Database を有効にするには、db\_owner アクセス許可が必要です。 データベースで Stretch Database を有効にするには、CONTROL DATABASE アクセス許可も必要です。

> [!NOTE]
> 後で Strech Database を無効にする場合、テーブルまたはデータベースの Stretch Database を無効にしても、リモート オブジェクトは削除されないことに注意してください。 リモート テーブルまたはリモート データベースを削除する場合は、Microsoft Azure 管理ポータルを使用して削除する必要があります。 リモート オブジェクトを手動で削除するまで、Azure のコストが引き続き発生します。
> 
> 

## <a name="before-you-get-started"></a>開始する前に
* Stretch のデータベースを設定する前に、Stretch Database Advisor を実行し、Stretch の対象となるデータベースとテーブルを特定することが推奨されます。 Stretch Database Advisor はブロック問題も特定します。 詳細については、 [Stretch Database のデータベースとテーブルの特定](sql-server-stretch-database-identify-databases.md)に関する記事をご覧ください。
* 「 [Stretch Database の制限事項](sql-server-stretch-database-limitations.md)」を確認します。
* Stretch Database はデータを Azure に移行します。 そのため、請求のために Azure アカウントとサブスクリプションが必要になります。 Azure アカウントを取得するには、 [ここをクリック](http://azure.microsoft.com/pricing/free-trial/)してください。
* 新しい Azure サーバーの作成または既存の Azure サーバーの選択に必要となる、接続およびログインの情報を用意します。

## <a name="a-nameenabletsqlserveraprerequisite-enable-stretch-database-on-the-server"></a><a name="EnableTSQLServer"></a>前提条件: サーバーで Stretch Database を有効にする
データベースまたはテーブルで Stretch Database を有効にする前に、ローカル サーバーでそれを有効にする必要があります。 この操作にはアクセス許可として sysadmin または serveradmin が必要になります。

* 必要な管理アクセス許可がある場合は、 **データベースのストレッチの有効化** ウィザードでサーバーが Stretch 用に構成されます。
* 必要なアクセス許可がない場合は、ウィザードを実行する前に、管理者が **sp\_configure** を実行してオプションを手動で有効にするか、管理者がウィザードを実行する必要があります。

サーバーで Stretch Database を手動で有効にするには、**sp\_configure** を実行し、**remote data archive** オプションを有効にします。 次の例では、値を 1 に設定することで、 **remote data archive** オプションを有効にしています。

```
EXEC sp_configure 'remote data archive' , '1';
GO

RECONFIGURE;
GO
```
詳細については、「[Configure the remote data archive Server Configuration Option (サーバー構成オプションの remote data archive を構成する)](https://msdn.microsoft.com/library/mt143175.aspx)」および「[sp_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx)」をご覧ください。

## <a name="a-namewizardause-the-wizard-to-enable-stretch-database-on-a-database"></a><a name="Wizard"></a>ウィザードを使用してデータベースで Stretch Database を有効にする
入力する必要がある情報や必要な選択など、データベースのストレッチの有効化ウィザードの詳細については、「 [データベースのストレッチの有効化ウィザードを実行する方法の概要](sql-server-stretch-database-wizard.md)」をご覧ください。

## <a name="a-nameenabletsqldatabaseause-transact-sql-to-enable-stretch-database-on-a-database"></a><a name="EnableTSQLDatabase"></a>Transact\-SQL を使用してデータベースで Stretch Database を有効にする
個々のテーブルで Stretch Database を有効にする前に、データベースでそれを有効にする必要があります。

データベースまたはテーブルで Stretch Database を有効にするには、db\_owner アクセス許可が必要です。 データベースで Stretch Database を有効にするには、CONTROL DATABASE アクセス許可も必要です。

1. 開始する前に、Stretch Database が移行するデータの既存の Azure サーバーを選択するか、新しい Azure サーバーを作成します。
2. Azure サーバーで、SQL Server とリモート サーバーの通信を可能にするファイアウォール規則を SQL Server の IP アドレス範囲で作成します。
   
   SQL Server Management Studio (SSMS) のオブジェクト エクスプローラーから Azure サーバーへの接続を試みることで、必要な値を簡単に見つけて、ファイアウォール規則の作成することができます。 SSMS では、ルールを作成するときに便利な次のダイアログ ボックスが開きます。このダイアログ ボックスには、必要な IP アドレスの値が既に入力されています。
   
   ![SSMS でのファイアウォール規則の作成][FirewallRule]
3. Stretch Database の SQL Server データベースを構成するには、データベースにデータベース マスター キーを与える必要があります。 データベース マスター キーにより、Stretch Database がリモート データベースの接続に使用する資格情報が守られます。 新しいデータベース マスター キーを作成する例を次に示します。
   
   ```tsql
   USE <database>;
   GO
   
   CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>';
   GO
   ```
   
   データベース マスター キーの詳細については、「[CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx)」および「[データベース マスター キーの作成](https://msdn.microsoft.com/library/aa337551.aspx)」を参照してください。
4. Stretch Database のデータベースを設定するとき、オンプレミス SQL Server とリモート Azure サーバーの間で通信するための Stretch Database の資格情報を入力する必要があります。 2 つのオプションがあります。
   
   * 管理者の資格情報を指定できます。
     
     * ウィザードを実行して Stretch Database を有効にする場合、その時点で資格情報を作成できます。
     * **ALTER DATABASE** を実行して Stretch Database を有効にする場合、**ALTER DATABASE** を実行して Stretch Database を有効にする前に、資格情報を手動で作成する必要があります。
     
     新しい資格情報を作成する例を次に示します。
     
     ```tsql
     CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
         WITH IDENTITY = '<identity>' , SECRET = '<secret>';
     GO
     ```
     
     資格情報の詳細については、「 [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx)」をご覧ください。 資格情報を作成するには、ALTER ANY CREDENTIAL というアクセス許可が必要です。
   * SQL Server のフェデレーション サービス アカウントを使用すれば、次の条件に該当するとき、リモート Azure サーバーと通信できます。
     
     * SQL Server のインスタンスを実行しているサービス アカウントがドメイン アカウントです。
     * Active Directory が Azure Active Directory との連合になっているドメインにドメイン アカウントが属しています。
     * Azure Active Directory 認証をサポートするようにリモート Azure サーバーが設定されます。
     * SQL Server のインスタンスを実行しているサービス アカウントは、リモート Azure サーバーで dbmanager または sysadmin アカウントとして設定する必要があります。
5. Stretch Database のデータベースを設定するには、ALTER DATABASE コマンドを実行します。
   
   1. SERVER 引数には、既存の Azure サーバーの名前を指定します。名前の `.database.windows.net` 部分も含めます。たとえば、\- のようになります。`MyStretchDatabaseServer.database.windows.net`
   2. CREDENTIAL 引数で既存の管理者資格情報を指定するか、FEDERATED\_SERVICE\_ACCOUNT = ON を指定します。 次の例では、既存の資格情報が指定されています。
   
   ```tsql
   ALTER DATABASE <database name>
       SET REMOTE_DATA_ARCHIVE = ON
           (
               SERVER = '<server_name>',
               CREDENTIAL = <db_scoped_credential_name>
           ) ;
   GO
   ```

## <a name="next-steps"></a>次のステップ
* [テーブルの Stretch Database を有効にして](sql-server-stretch-database-enable-table.md) 追加テーブルを有効にします。
* [Stretch Database を監視](sql-server-stretch-database-monitor.md) してデータ移行の状態を確認します。
* [Stretch Database を一時停止し、再開します。](sql-server-stretch-database-pause.md)
* [Stretch Database を管理し、問題を解決します。](sql-server-stretch-database-manage.md)
* [Stretch 対応データベースをバックアップする](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>関連項目
[Stretch Database のデータベースとテーブルを特定する](sql-server-stretch-database-identify-databases.md)

[ALTER DATABASE SET のオプション (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[FirewallRule]: ./media/sql-server-stretch-database-enable-database/firewall.png



<!--HONumber=Nov16_HO3-->


