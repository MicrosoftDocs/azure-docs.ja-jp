---
title: "Transact-SQL を使用した Azure SQL Database のコピー | Microsoft Docs"
description: "Transact-SQL を使用した Azure SQL Database のコピーの作成"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 40ea3718-33f8-41af-90cb-3aa15059365e
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: e8d71d7b6b4302f177732381ad19e6a645bc5474
ms.lasthandoff: 03/28/2017


---
# <a name="copy-an-azure-sql-database-using-transact-sql"></a>Transact-SQL を使用した Azure SQL Database のコピー

次の手順では、Transact-SQL を使用して、同じサーバーまたは別のサーバーに SQL データベースをコピーする方法を説明します。 このデータベースのコピー操作では、 [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) ステートメントを使用します。 

> [!NOTE]
> [Azure Portal](sql-database-copy-portal.md) または [PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md) を使用して SQL データベースをコピーすることもできます。
>

この記事の手順を完了するには、次のものが必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合、このページの上部の **無料評価版** をクリックしてからこの記事に戻り、最後まで完了してください。
* Azure SQL Database。 SQL Database がない場合は、「 [最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。
* [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx)。 SSMS を入手していない場合、またはこの記事で説明する機能を使用できない場合は、 [最新版をダウンロード](https://msdn.microsoft.com/library/mt238290.aspx)してください。

## <a name="copy-your-sql-database"></a>SQL データベースのコピー
サーバーレベル プリンシパル ログインか、コピーするデータベースを作成したときのログインを使用して、マスター データベースにログオンします。 サーバーレベル プリンシパルではないログインでデータベースをコピーするには、dbmanager ロールのメンバーである必要があります。 ログインおよびサーバーへの接続の詳細については、 [ログインの管理](sql-database-manage-logins.md)に関するページを参照してください。

[CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) ステートメントを使用して、コピー元データベースのコピーを開始します。 このステートメントを実行すると、データベースのコピー処理が始まります。 データベースのコピーは非同期的な処理であるため、CREATE DATABASE ステートメントは、データベースのコピーが完了する前に復帰します。

### <a name="copy-a-sql-database-to-the-same-server"></a>同じサーバーへの SQL データベースのコピー
サーバーレベル プリンシパル ログインか、コピーするデータベースを作成したときのログインを使用して、マスター データベースにログオンします。 サーバーレベル プリンシパルではないログインでデータベースをコピーするには、dbmanager ロールのメンバーである必要があります。

このコマンドは、Database1 を、同じサーバー上の Database2 という名前の新しいデータベースにコピーします。 データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>別のサーバーへの SQL データベースのコピー
コピー先サーバー (新しいデータベースを作成する Azure SQL Database サーバー) のマスター データベースにログオンします。 コピー元の Azure SQL Database サーバー上のコピー元データベースのデータベース所有者 (DBO) と同じ名前とパスワードを持つログインを使用します。 コピー先サーバーでのログインも、dbmanager ロールのメンバーであるか、サーバーレベル プリンシパル ログインである必要があります。

このコマンドは、server1 上の Database1 を、server2 上の Database2 という新しいデータベースにコピーします。 データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;


## <a name="monitor-the-progress-of-the-copy-operation"></a>コピー操作の進行状況の監視
sys.databases ビューと sys.dm_database_copies ビューを照会して、コピー操作を監視します。 コピー操作の進行中は、新しいデータベースの sys.databases ビューの state_desc 列が COPYING に設定されます。

* コピー操作に失敗すると、新しいデータベースの sys.databases ビューの state_desc 列が SUSPECT に設定されます。 この場合は、新しいデータベースに対して DROP ステートメントを実行した後でもう一度やり直してください。
* コピー操作に成功すると、新しいデータベースの sys.databases ビューの state_desc 列が ONLINE に設定されます。 この場合、コピー操作は完了しています。新しいデータベースは通常のデータベースであり、コピー元データベースから独立して変更することができます。

> [!NOTE]
> * 進行中のコピー操作を取り消すには、新しいデータベースに対して [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) ステートメントを実行します。 また、コピー元のデータベースに対して DROP DATABASE ステートメントを実行することによっても、コピー操作を取り消すことができます。
> 
> 

## <a name="resolve-logins-after-the-copy-operation-completes"></a>コピー操作完了後のログインの解決
新しいデータベースがコピー先サーバーでオンラインになった後、 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) ステートメントを使用して、ユーザーを新しいデータベースからコピー先サーバーのログインに再マップします。 孤立したユーザーを解決するには、「 [孤立ユーザーのトラブルシューティング](https://msdn.microsoft.com/library/ms175475.aspx)」をご覧ください。 「 [障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」もご覧ください。

新しいデータベースのすべてのユーザーは、コピー元データベースで保持していたアクセス許可を保持します。 データベースのコピーを開始したユーザーが新しいデータベースのデータベース所有者になり、新しいセキュリティ識別子 (SID) が割り当てられます。 コピーが成功した後、他のユーザーが再マップされるまでは、コピーを開始したログイン、つまりデータベース所有者 (DBO) のみが新しいデータベースにログオンできます。

## <a name="next-steps"></a>次のステップ
* 別の論理サーバーにデータベースをコピーする場合のユーザーおよびログインの管理の詳細については、「[障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」をご覧ください。
* [ビジネス継続性の概要](sql-database-business-continuity.md)
* [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)

