---
title: Azure SQL データベースのコピーの作成 | Microsoft Docs
description: トランザクション上一貫性のある既存の Azure SQL データベースのコピーを、同じサーバーまたは別のサーバーのいずれかに作成します。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 06/03/2019
ms.openlocfilehash: dc82ace4c5bf1204075bfa5e34d0b1489a3acb98
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876268"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>トランザクション上一貫性のある Azure SQL データベースのコピーを作成する

Azure SQL Database では、同じサーバーまたは別のサーバーのいずれかに、トランザクション上一貫性のある、既存の Azure SQL データベース ([単一データベース](sql-database-single-database.md)) のコピーを作成するための複数の方法が用意されています。 Azure Portal、PowerShell、または T-SQL を使って、SQL Database をコピーできます。 

## <a name="overview"></a>概要

データベースのコピーとは、コピーが要求された時点でのソース データベースのスナップショットのことです。 同じサーバーまたは別のサーバーを選択できます。 また、そのサービス レベルとコンピューティング サイズを維持するか、同じサービス レベル (エディション) 内の別のコンピューティング サイズを使用することもできます。 コピーの完了後、コピーは完全に機能する独立したデータベースになります。 この時点では、任意のエディションにアップグレードまたはダウン グレードできます。 ログイン、ユーザー、アクセス許可は非依存で管理できます。  

> [!NOTE]
> データベースのコピーを作成するときは、[自動データベース バックアップ](sql-database-automated-backups.md)が使用されます。

## <a name="logins-in-the-database-copy"></a>データベースのコピーへのログイン

データベースを同じ SQL Database サーバーにコピーすると、両方のデータベースで同じログインを利用できます。 データベースをコピーするために使うセキュリティ プリンシパルが、新しいデータベースのデータベース所有者になります。 すべてのデータベース ユーザー、アクセス許可、セキュリティ識別子 (SID) がデータベースのコピーにコピーされます。  

別の SQL Database サーバーにデータベースをコピーする場合は、新しいサーバーのセキュリティ プリンシパルが新しいデータベースのデータベース所有者になります。 データのアクセスに[包含データベース ユーザー](sql-database-manage-logins.md)を使う場合は、プライマリとセカンダリの両方のデータベースが常に同じユーザー資格情報を持つようにします。そうすることで、コピーの完了後すぐに同じ資格情報でアクセスできるようになります。 

[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) を使用すると、コピーで資格情報を管理する必要がなくなります。 ただし、新しいサーバーにデータベースをコピーする場合、ログインに基づくアクセスは動作しないことがあります。これは、そのログインが新しいサーバーに存在しないためです。 別の SQL Database サーバーにデータベースをコピーする場合のログイン管理の詳細については、[ディザスター リカバリー後に Azure SQL データベース セキュリティを管理する方法](sql-database-geo-replication-security-config.md)に関するページをご覧ください。 

コピーが成功した後、他のユーザーが再マップされるまでは、コピーを開始したログイン、つまりデータベース所有者のみが新しいデータベースにログインできます。 コピー操作が完了した後でログインを解決するには、「[ログインの解決](#resolve-logins)」をご覧ください。

## <a name="copy-a-database-by-using-the-azure-portal"></a>Azure Portal を使ってデータベースをコピーする

Azure Portal を使ってデータベースをコピーするには、データベースのページを開き、 **[コピー]** をクリックします。 

   ![データベースのコピー](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>PowerShell を使ってデータベースをコピーする

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell を使用してデータベースをコピーするには、[New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) コマンドレットを使用します。 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

完全なサンプル スクリプトについては、「[新しいサーバーにデータベースをコピーする](scripts/sql-database-copy-database-to-new-server-powershell.md)」をご覧ください。

## <a name="copy-a-database-by-using-transact-sql"></a>Transact-SQL を使ってデータベースをコピーする

サーバーレベル プリンシパル ログインか、コピーするデータベースを作成したときのログインを使って、マスター データベースにログオンします。 データベースを正常にコピーするには、サーバーレベル プリンシパルではないログインが dbmanager ロールのメンバーである必要があります。 ログインおよびサーバーへの接続の詳細については、 [ログインの管理](sql-database-manage-logins.md)に関するページを参照してください。

[CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) ステートメントを使用して、コピー元データベースのコピーを開始します。 このステートメントを実行すると、データベースのコピー処理が始まります。 データベースのコピーは非同期的な処理であるため、CREATE DATABASE ステートメントからは、データベースのコピーが完了する前に戻ります。

### <a name="copy-a-sql-database-to-the-same-server"></a>同じサーバーへの SQL データベースのコピー

サーバーレベル プリンシパル ログインか、コピーするデータベースを作成したときのログインを使って、マスター データベースにログオンします。 データベースを正常にコピーするには、サーバーレベル プリンシパルではないログインが dbmanager ロールのメンバーである必要があります。

このコマンドは、Database1 を、同じサーバー上の Database2 という名前の新しいデータベースにコピーします。 データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>別のサーバーへの SQL データベースのコピー

コピー先サーバー (新しいデータベースを作成する SQL Database サーバー) のマスター データベースにログインします。 コピー元の SQL Database サーバー上のコピー元データベースのデータベース所有者と同じ名前とパスワードを持つログインを使います。 コピー先サーバーでのログインも、dbmanager ロールのメンバーであるか、サーバーレベル プリンシパル ログインである必要があります。

このコマンドは、server1 上の Database1 を、server2 上の Database2 という新しいデータベースにコピーします。 データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> T-SQL COPY コマンドを発行するクライアントの IP からのインバウンド接続を許可するように、両方のサーバーのファイアウォールを構成する必要があります。

### <a name="copy-a-sql-database-to-a-different-subscription"></a>別のサブスクリプションに SQL データベースをコピーする

前のセクションに示された手順を使用して、別のサブスクリプションの SQL Database サーバーにデータベースをコピーすることができます。 名前とパスワードがソース データベースのデータベース所有者と同じであるログインを使用しており、それが dbmanager ロールのメンバーであるか、サーバーレベルのプリンシパル ログインであることを確認してください。 

> [!NOTE]
> [Azure portal](https://portal.azure.com) では、別のサブスクリプションへのコピーはサポートされていません。ポータルでは ARM API を呼び出し、サブスクリプション資格情報を使用して、geo レプリケーションに関係する両方のサーバーにアクセスするためです。  

### <a name="monitor-the-progress-of-the-copying-operation"></a>コピー操作の進行状況を監視する

sys.databases ビューと sys.dm_database_copies ビューを照会して、コピー操作を監視します。 コピー操作の進行中は、新しいデータベースの sys.databases ビューの **state_desc** 列が **COPYING** に設定されます。

* コピー操作が失敗すると、新しいデータベースの sys.databases ビューの **state_desc** 列が **SUSPECT** に設定されます。 新しいデータベースに対して DROP ステートメントを実行した後でもう一度やり直してください。
* コピー操作が成功すると、新しいデータベースの sys.databases ビューの **state_desc** 列が **ONLINE** に設定されます。 コピー操作は完了しています。新しいデータベースは通常のデータベースであり、コピー元データベースから独立して変更することができます。

> [!NOTE]
> 進行中のコピー操作を取り消すには、新しいデータベースに対して [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) ステートメントを実行します。 また、コピー元のデータベースに対して DROP DATABASE ステートメントを実行することによっても、コピー操作を取り消すことができます。

## <a name="resolve-logins"></a>ログインの解決

新しいデータベースがコピー先サーバーでオンラインになった後、 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) ステートメントを使用して、ユーザーを新しいデータベースからコピー先サーバーのログインに再マップします。 孤立したユーザーを解決するには、「 [孤立ユーザーのトラブルシューティング](https://msdn.microsoft.com/library/ms175475.aspx)」をご覧ください。 「 [障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」もご覧ください。

新しいデータベースのすべてのユーザーのアクセス許可は、コピー元データベースで保持していたものと同じです。 データベースのコピーを開始したユーザーが新しいデータベースのデータベース所有者になり、新しいセキュリティ識別子 (SID) が割り当てられます。 コピーが成功した後、他のユーザーが再マップされるまでは、コピーを開始したログイン、つまりデータベース所有者のみが新しいデータベースにログインできます。

別の SQL Database サーバーにデータベースをコピーする場合のユーザーとログインの管理の詳細については、[ディザスター リカバリー後に Azure SQL データベース セキュリティを管理する方法](sql-database-geo-replication-security-config.md)に関する方法をご覧ください。

## <a name="next-steps"></a>次の手順

* ログインについては、「[ログインの管理](sql-database-manage-logins.md)」および「[障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」をご覧ください。
* データベースをエクスポートするには、「[BACPAC へのデータベースのエクスポート](sql-database-export.md)」をご覧ください。
