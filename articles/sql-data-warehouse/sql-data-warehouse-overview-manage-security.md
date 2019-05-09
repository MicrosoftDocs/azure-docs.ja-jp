---
title: SQL Data Warehouse でのデータベースの保護 | Microsoft Docs
description: ソリューション開発のための Azure SQL Data Warehouse でのデータベース保護に関するヒント。
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 179925fc7411a1ccf3de02d7b6298cc66f93bc66
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58175719"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>SQL Data Warehouse でのデータベース保護
> [!div class="op_single_selector"]
> * [セキュリティの概要](sql-data-warehouse-overview-manage-security.md)
> * [認証](sql-data-warehouse-authentication.md)
> * [暗号化 (ポータル)](sql-data-warehouse-encryption-tde.md)
> * [暗号化 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

この記事では、Azure SQL Data Warehouse データベースの保護に関する基本事項を説明します。 特にこの記事では、アクセスの制限、データの保護、データベースでのアクティビティの監視を行うためのリソースの概要を説明します。

## <a name="connection-security"></a>接続のセキュリティ
接続のセキュリティとは、ファイアウォール ルールと接続の暗号化を使用して、データベースへの接続を制限し、保護する方法のことです。

ファイアウォール ルールはサーバーとデータベースの両方で使用され、明示的にホワイト リストに登録されていない IP アドレスからの接続試行を拒否します。 アプリケーションまたはクライアント コンピューターのパブリック IP アドレスからの接続を許可するには、まず Azure Portal、REST API、または PowerShell を使用して、サーバーレベルのファイアウォール ルールを作成する必要があります。 ベスト プラクティスとして、可能な限りサーバーのファイアウォールにより許可される IP アドレスの範囲を制限する必要があります。  ローカル コンピューターから Azure SQL Data Warehouse にアクセスするには、ネットワークとローカル コンピューターのファイアウォールで、TCP ポート 1433 での送信方向の通信が許可されていることを確認します。  

SQL Data Warehouse では、サーバーレベルのファイアウォール規則を使用します。 データベースレベルのファイアウォール規則はサポートされていません。 詳しくは、[Azure SQL Database ファイアウォール][Azure SQL Database firewall] に関するページや [sp_set_firewall_rule][sp_set_firewall_rule] に関するページをご覧ください。

SQL Data Warehouse への接続は、既定で暗号化されます。  暗号化を無視するように接続の設定を変更しても、その変更は無視されます。

## <a name="authentication"></a>Authentication
認証とは、データベースへの接続時に ID を証明する方法のことです。 SQL Data Warehouse では現在、ユーザー名とパスワードを使用した SQL Server 認証と、Azure Active Directory による認証がサポートされています。 

データベースの論理サーバーを作成したときに、ユーザー名とパスワードによる "サーバー管理" ログインを指定したとします。 これらの資格情報を使用すると、データベース所有者、つまり "dbo" として、そのサーバーにある任意のデータベースを SQL Server 認証を通して認証できます。

ただし、ベスト プラクティスとして、組織のユーザーは別のアカウントを使用して認証する必要があります。 この方法により、アプリケーションに付与されるアクセス許可を制限でき、アプリケーション コードが SQL インジェクション攻撃に対して脆弱な場合に、悪意のあるアクティビティのリスクを軽減できます。 

SQL Server 認証済みユーザーを作成するには、サーバーの **マスター** データベースにサーバー管理ログインを使用して接続し、新しいサーバー ログインを作成します。  また、Azure SQL Data Warehouse ユーザーの master データベースにユーザーを作成することをお勧めします。 マスターでユーザーを作成すると、ユーザーはデータベース名を指定せずに SSMS のようなツールを使用してログインできます。  また、オブジェクト エクスプ ローラーを使用して、SQL Server のすべてのデータベースを表示することもできます。

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

次に、サーバー管理者ログインを使用して **SQL Data Warehouse データベース** に接続し、先ほど作成したサーバー ログインに基づいてデータベース ユーザーを作成します。

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

ログインの作成や新しいデータベースの作成などの追加の操作を実行するアクセス許可をユーザーに与えるには、master データベース内の `Loginmanager` ロールと `dbmanager` ロールにユーザーを割り当てます。 これらの追加のロールと SQL Database の認証の詳細については、[Azure SQL Database におけるデータベースとログインの管理][Managing databases and logins in Azure SQL Database]に関する記事をご覧ください。  詳細については、[Azure Active Directory 認証を使用した SQL Data Warehouse への接続][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]に関する記事をご覧ください。

## <a name="authorization"></a>Authorization
承認とは、Azure SQL Data Warehouse データベース内で実行できる操作を指します。 承認の特権は、ロール メンバーシップとアクセス許可によって指定されます。 ベスト プラクティスとして、必要最低限の特権をユーザーに付与することをお勧めします。 ロールを管理するには、次のストアド プロシージャを使用します。

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

接続しているサーバー管理者のアカウントは db_owner のメンバーであり、データベース内ですべての操作を実行する権限を持ちます。 スキーマのアップグレードやその他の管理操作をデプロイするために、このアカウントを保存します。 アクセス許可が限定された "ApplicationUser" アカウントを使用して、アプリケーションで必要な最小限の特権により、アプリケーションをデータベースに接続します。

ユーザーが Azure SQL Data Warehouse で実行できる操作をさらに制限する方法がいくつかあります。

* 詳細な[アクセス許可][Permissions]により、個々の列、テーブル、ビュー、スキーマ、プロシージャ、その他のデータベース内のオブジェクトで実行できる操作を制御できます。 詳細なアクセス許可を使用して最大限の制御を行い、必要最小限のアクセス許可を付与します。 
* db_datareader と db_datawriter 以外の[データベース ロール][Database roles]を使用して、権限の高いアプリケーション ユーザー アカウントや権限の低い管理アカウントを作成できます。 組み込みの固定データベース ロールを使用すると、簡単にアクセス許可を付与できますが、その結果、必要以上のアクセス許可を付与することになる可能性があります。
* [ストアド プロシージャ][Stored procedures]を使用すると、データベースで実行できるアクションを制限できます。

次の例では、ユーザー定義スキーマに対する読み取りアクセス許可を付与します。
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Azure Portal または Azure Resource Manager API を使用したデータベースと論理サーバーの管理は、ポータル ユーザー アカウントのロールの割り当てによって制御されます。 詳細については、[Azure Portal でのロールベースのアクセス制御][Role-based access control in Azure portal]に関するページをご覧ください。

## <a name="encryption"></a>暗号化
Azure SQL Data Warehouse の Transparent Data Encryption (TDE) を使用すると、保存データを暗号化および暗号化解除することにより、悪意のあるアクティビティの脅威から保護できます。  データベースを暗号化すると、アプリケーションに変更を加える必要なく、関連付けられているバックアップとトランザクション ログ ファイルが暗号化されます。 TDE は、データベース暗号化キーと呼ばれる対称キーを使用してデータベース全体のストレージを暗号化します。 

SQL Database では、データベース暗号化キーは組み込まれているサーバー証明書によって保護されます。 組み込みのサーバー証明書は、SQL Database サーバーごとに一意です。 Microsoft は、少なくとも 90 日ごとにこれらの証明書を自動的にローテーションします。 SQL Data Warehouse で使用される暗号化アルゴリズムは AES-256 です。 TDE の概要については、[Transparent Data Encryption][Transparent Data Encryption] に関するページをご覧ください。

データベースは、[Azure Portal][Encryption with Portal] または [T-SQL][Encryption with TSQL] を使用して暗号化できます。

## <a name="next-steps"></a>次の手順
さまざまなプロトコルでの SQL Data Warehouse への接続の詳細と例については、[SQL Data Warehouse への接続][Connect to SQL Data Warehouse]に関する記事をご覧ください。

<!--Image references-->

<!--Article references-->
[Connect to SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL Database firewall]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Database roles]: https://msdn.microsoft.com/library/ms189121.aspx
[Managing databases and logins in Azure SQL Database]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissions]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedures]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Role-based access control in Azure portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
