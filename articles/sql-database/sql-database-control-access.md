---
title: Azure SQL Database と Data Warehouse へのアクセスの許可 | Microsoft Doc
description: Microsoft Azure SQL Database と SQL Data Warehouse へのアクセスを許可する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: vanmsft
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 05/08/2019
ms.openlocfilehash: 783a8f0bc25717f1c2bf78a9c0d40b209a07939b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65473342"
---
# <a name="azure-sql-database-and-sql-data-warehouse-access-control"></a>Azure SQL Database と SQL Data Warehouse へのアクセスの制御

Azure の [SQL Database](sql-database-technical-overview.md) と [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) では、セキュリティを提供するために、IP アドレスで接続を制限するファイアウォール規則、ユーザーに ID の指定を要求する認証メカニズム、ユーザーを特定の操作とデータに限定する承認メカニズムによってアクセスを制御します。 

> [!IMPORTANT]
> SQL Database のセキュリティ機能の概要については、[SQL のセキュリティの概要](sql-database-security-overview.md)に関するページを参照してください。 チュートリアルについては、「[Azure SQL データベースのセキュリティ保護](sql-database-security-tutorial.md)」を参照してください。 SQL Data Warehouse のセキュリティ機能の概要については、[SQL Data Warehouse のセキュリティの概要](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)に関するページを参照してください。

## <a name="firewall-and-firewall-rules"></a>ファイアウォールとファイアウォール規則

Microsoft Azure SQL Database は、Azure およびその他のインターネット ベースのアプリケーション用のリレーショナル データベース サービスを提供します。 データを保護するため、ファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを遮断します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。 詳細については、「[Azure SQL Database ファイアウォール規則の概要](sql-database-firewall-configure.md)」を参照してください。

Azure SQL Database サービスは TCP ポート 1433 経由でのみ利用できます。 コンピューターから SQL Database にアクセスするには、クライアント コンピューターのファイアウォールで、TCP ポート 1433 での TCP 通信の発信を許可する必要があります。 他のアプリケーションで必要ない場合は、TCP ポート 1433 での着信接続をブロックします。 

接続処理の一部として、Azure 仮想マシンからの接続は worker ロールごとに異なる別の IP アドレスとポートにリダイレクトされます。 ポート番号の範囲は、11000 ～ 11999 です。 TCP ポートの詳細については、「[ADO.NET 4.5 用の 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」を参照してください。

## <a name="authentication"></a>Authentication

SQL Database は、2 種類の認証をサポートしています。

- **SQL 認証**:

  この認証方法では、ユーザー名とパスワードを使用します。 ご自分のデータベース用に SQL Database サーバーを作成したときに、ユーザー名とパスワードを使用して "サーバー管理" ログインを指定しました。 これらの資格情報を使用すると、データベース所有者、つまり "dbo" として、そのサーバーにある任意のデータベースを認証できます。 
- **Azure Active Directory 認証**:

  この認証方法では、Azure Active Directory で管理されている ID を使用し、管理、統合されたドメインをサポートしています。 Azure Active Directory 認証を使用する場合は、"Azure AD 管理者" という、Azure AD ユーザーとグループを管理できるサーバー管理者を別に作成する必要があります。 この管理者は、通常のサーバー管理者が実行できるすべての操作も実行できます。 Azure AD 管理者を作成して Azure Active Directory 認証を有効にする方法のチュートリアルについては、「 [Azure Active Directory の認証を使用して SQL Database に接続する](sql-database-aad-authentication.md) 」を参照してください。

データベース エンジンは、30 分以上アイドル状態が続くと接続を閉じます。 接続する前に、もう一度ログインする必要があります。 SQL Database への継続したアクティブな接続では、少なくとも 10 時間ごとに再認証が必要になります (データベース エンジンによって実行されます)。 データベース エンジンは最初に送信されたパスワードを使用して再認証を試みるため、ユーザー入力は必要ありません。 パフォーマンス上の理由により、パスワードが SQL Database でリセットされると、接続プールが原因で接続がリセットされた場合でも、接続は再認証されません。 これは、オンプレミスの SQL Server とは異なる動作です。 接続が最初に承認されて以来パスワードが変更されている場合は、その接続を終了し、新しいパスワードを使用して新しい接続を作成する必要があります。 `KILL DATABASE CONNECTION` アクセス許可を持つユーザーは、[KILL](https://docs.microsoft.com/sql/t-sql/language-elements/kill-transact-sql) コマンドを使用して SQL Database への接続を明示的に終了できます。

ユーザー アカウントを master データベースに作成し、サーバーのすべてのデータベースでアクセス許可を付与することができます。または、ユーザー アカウントをデータベース自体に作成することができます (包含ユーザーと呼ばれます)。 ログインの作成と管理については、[ログインの管理](sql-database-manage-logins.md)に関するページを参照してください。 包含データベースを使用して、移植性とスケーラビリティを高めます。 包含ユーザーの詳細については、「[包含データベース ユーザー - データベースの可搬性を確保する](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)」、「[CREATE USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)」、「[包含データベース](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)」を参照してください。

ベスト プラクティスとしては、アプリケーションで専用アカウントを使用して認証することをお勧めします。この方法により、アプリケーションに付与されるアクセス許可を制限し、アプリケーション コードが SQL インジェクション攻撃に対して脆弱な場合に、悪意のあるアクティビティのリスクを軽減できます。 [包含データベース ユーザー](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)を作成する方法をお勧めします。この方法により、アプリがデータベースから直接認証を受けることができます。 

## <a name="authorization"></a>Authorization

承認とは、Azure SQL Database でユーザーが許可される操作を示すものであり、ユーザー アカウントのデータベース [ロールのメンバーシップ](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)と[オブジェクト レベルのアクセス許可](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)によって制御されます。 ベスト プラクティスとして、必要最低限の特権をユーザーに付与することをお勧めします。 接続しているサーバー管理者のアカウントは db_owner のメンバーであり、データベース内ですべての操作を実行する権限を持ちます。 スキーマのアップグレードやその他の管理操作をデプロイするために、このアカウントを保存します。 アクセス許可が限定された "ApplicationUser" アカウントを使用して、アプリケーションで必要な最小限の特権により、アプリケーションをデータベースに接続します。 詳細については、[ログインの管理](sql-database-manage-logins.md)に関するページを参照してください。

通常、`master` データベースへのアクセスが必要なのは管理者のみです。 各ユーザー データベースに定期的にアクセスするには、各データベースで作成された包含データベース ユーザー (管理者以外) を使用する必要があります。 包含データベース ユーザーを使用する場合は、`master` データベースにログインを作成する必要はありません。 詳細については、「 [包含データベース ユーザー - データベースの可搬性を確保する](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)」を参照してください。

アクセス許可を制限したり昇格させたりするために使用できる次の機能について理解を深める必要があります。

- [権限借用](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server)と[モジュール署名](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)を使用すると、安全にアクセス許可を一時的に昇格できます。
- [Row-Level Security](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) を使用すると、ユーザーがアクセスできる行を制限できます。
- [データのマスキング](sql-database-dynamic-data-masking-get-started.md) を使用すると、機密データの公開を制限できます。
- [ストアド プロシージャ](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) を使用すると、データベースで実行できるアクションを制限できます。

## <a name="next-steps"></a>次の手順

- SQL Database のセキュリティ機能の概要については、[SQL のセキュリティの概要](sql-database-security-overview.md)に関するページを参照してください。
- ファイアウォール規則の詳細については、[ファイアウォール規則](sql-database-firewall-configure.md)に関するページを参照してください。
- ユーザーとログインの詳細については、[ログインの管理](sql-database-manage-logins.md)に関するページを参照してください。 
- プロアクティブな監視の詳細については、[データベース監査](sql-database-auditing.md)と[SQL Database の脅威の検出](sql-database-threat-detection.md)に関するページを参照してください。
- チュートリアルについては、「[Azure SQL データベースのセキュリティ保護](sql-database-security-tutorial.md)」を参照してください。
