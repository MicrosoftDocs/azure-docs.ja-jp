---
title: "T-SQL: Azure SQL Database ファイアウォール規則 | Microsoft Docs"
description: "Transact-SQL を使用して、Azure SQL データベースにアクセスする IP アドレス用にサーバーレベルおよびデータベースレベルのファイアウォール規則を構成する方法について説明します。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: 71e692a1-5e2f-4a18-a6d6-527b849cf68e
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/07/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 84efba61fbb0248edef3036de702e3a1aa8ae903
ms.lasthandoff: 02/17/2017


---
# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>TSQL を使用して Azure SQL Database ファイアウォールを構成する

Microsoft Azure SQL Database では、サーバーとデータベースの接続許可に、ファイアウォール規則を使用します。 データベースへのアクセスを選択的に許可するには、Azure SQL Database サーバーのマスター データベースまたはユーザー データベースに、サーバーレベルおよびデータベースレベルのファイアウォール設定を定義します。

> [!IMPORTANT]
> Azure のアプリケーションからデータベース サーバーに接続を許可するには、Azure の接続を有効にする必要があります。 ファイアウォール規則と Azure からの接続を有効にする方法については、「 [Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)」を参照してください。 Azure クラウド境界内で接続を行う場合、追加の TCP ポートを開くことが必要な場合があります。 詳細については、「 **ADO.NET 4.5 と SQL Database V12 の 1433 以外のポート** 」の「 [SQL Database V12: 外部と内部](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

## <a name="server-level-firewall-rules"></a>サーバーレベルのファイアウォール規則
Transact-SQL を使用してサーバーレベルのファイアウォール規則を作成できるのは、Azure SQL サーバー管理者ログインまたは Azure Active Directory 管理者のみです。

1. クエリ ウィンドウを起動し、仮想マスター データベースに接続するには、SQL Server Management Studio を使用します。
2. サーバーレベルのファイアウォール規則は、クエリ ウィンドウから選択、作成、更新、または削除することができます。
3. サーバーレベルのファイアウォール規則を作成または更新するには、`sp_set_firewall_rule` ストアド プロシージャを実行します。 次の例では、サーバー Contoso の IP アドレス範囲を有効にします。<br/>まず、既に存在する規則を確認します。
   
        SELECT * FROM sys.firewall_rules ORDER BY name;
   
    次に、ファイアウォール規則を追加します。
   
        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
   
    サーバーレベルのファイアウォール規則を削除するには、sp_delete_firewall_rule ストアド プロシージャを実行します。 次の例では、ContosoFirewallRule という名前の規則を削除します。
   
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
   
   このストアド プロシージャの詳細については、[sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) に関するページと [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) に関するページをご覧ください。

## <a name="database-level-firewall-rules"></a>データベース レベルのファイアウォール規則
データベース レベルのファイアウォール規則を作成できるのは、 **CONTROL** 権限が付与されているデータベース ユーザー (データベース所有者など) のみです。

1. IP アドレスに対してサーバーレベルのファイアウォールを作成した後、Azure Portal または SQL Server Management Studio からクエリ ウィンドウを起動します。
2. データベースレベルのファイアウォール規則を作成するデータベースに接続します。
   
    データベースレベルのファイアウォール規則を新規作成するか、既存のものを更新するには、 `sp_set_database_firewall_rule` ストアド プロシージャを実行します。 次の例では ContosoFirewallRule という名前の新しいファイアウォール規則を作成します。
   
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
   
    既存のデータベースレベルのファイアウォール規則を削除するには、 `sp_delete_database_firewall_rule` ストアド プロシージャを実行します。 次の例では、ContosoFirewallRule という名前の規則を削除します。
   `
   
        EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

このストアド プロシージャの詳細については、[sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) に関するページと [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) に関するページをご覧ください。

> [!NOTE]
> データベース レベルのファイアウォールの使い方がわかるチュートリアルについては、[SQL の認証と承認](sql-database-control-access-sql-authentication-get-started.md)に関するページをご覧ください。
>


## <a name="next-steps"></a>次のステップ
他の方法でサーバー レベルのファイアウォール規則を作成する方法については、次の記事をご覧ください。 

* [Azure ポータルを使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings.md)
* [PowerShell を使用して Azure SQL Database のサーバー レベルのファイアウォール規則を構成する](sql-database-configure-firewall-settings-powershell.md)
* [REST API を使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings-rest.md)

データベース作成のチュートリアルについては、「[初めての Azure SQL Database](sql-database-get-started.md)」をご覧ください。
オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL Database に接続する方法の詳細については、 [SQL Database のクライアント クイック スタート コード サンプル](https://msdn.microsoft.com/library/azure/ee336282.aspx)に関する記事をご覧ください。
データベースに移動する方法については、 [データベースへのアクセスとログイン セキュリティの管理](https://msdn.microsoft.com/library/azure/ee336235.aspx)に関する記事をご覧ください。

## <a name="additional-resources"></a>その他のリソース
* [データベースの保護](sql-database-security-overview.md)
* [SQL Server Database エンジンと Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)


