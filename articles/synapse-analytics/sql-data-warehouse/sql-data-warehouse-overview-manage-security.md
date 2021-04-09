---
title: 専用 SQL プール (旧称 SQL DW) をセキュリティで保護する
description: Azure Synapse Analytics での専用 SQL プール (旧称 SQL DW) のセキュリティ保護とソリューションの開発に関するヒント。
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: ad19c976cceab76d6eb0dbfbea5840d9764bffec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092953"
---
# <a name="secure-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics の専用 SQL プール (旧称 SQL DW) をセキュリティで保護する

> [!div class="op_single_selector"]
>
> * [セキュリティの概要](sql-data-warehouse-overview-manage-security.md)
> * [認証](sql-data-warehouse-authentication.md)
> * [暗号化 (ポータル)](sql-data-warehouse-encryption-tde.md)
> * [暗号化 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

この記事では、専用 SQL プール (旧称 SQL DW) をセキュリティで保護するための基本事項について説明します。 特にこの記事では、専用 SQL プール (旧称 SQL DW) を使用してアクセスの制限、データの保護、アクティビティの監視を行うためのリソースの概要を説明します。

## <a name="connection-security"></a>接続のセキュリティ

接続のセキュリティとは、ファイアウォール ルールと接続の暗号化を使用して、データベースへの接続を制限し、保護する方法のことです。

ファイアウォール規則は[論理 SQL サーバー](../../azure-sql/database/logical-servers.md)とそのデータベースの両方で使用され、明示的に承認されていない IP アドレスからの接続試行を拒否します。 アプリケーションまたはクライアント コンピューターのパブリック IP アドレスからの接続を許可するには、まず Azure Portal、REST API、または PowerShell を使用して、サーバーレベルのファイアウォール ルールを作成する必要があります。

ベスト プラクティスとして、可能な限りサーバーレベルのファイアウォールにより許可される IP アドレスの範囲を制限する必要があります。  ローカル コンピューターから専用 SQL プール (旧称 SQL DW) にアクセスするには、TCP ポート 1433 での発信を許可するようにネットワークとローカル コンピューターのファイアウォールを設定します。  

専用 SQL プール (旧称 SQL DW) では、サーバー レベルの IP ファイアウォール規則が使用されます。 データベース レベルの IP ファイアウォール規則はサポートされていません。 詳細については、「[Azure SQL Database ファイアウォール規則](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)」を参照してください

専用 SQL プール (旧称 SQL DW) への接続は、既定で暗号化されます。  暗号化を無視するように接続の設定を変更しても、その変更は無視されます。

## <a name="authentication"></a>認証

認証とは、データベースへの接続時に ID を証明する方法のことです。 専用 SQL プール (旧称 SQL DW) では現在、ユーザー名とパスワードを使用した SQL Server 認証と、Azure Active Directory による認証がサポートされています。

データベースのサーバーを作成したときに、ユーザー名とパスワードによる "サーバー管理" ログインを指定しています。 これらの資格情報を使用すると、データベース所有者、つまり "dbo" として、そのサーバーにある任意のデータベースを SQL Server 認証を通して認証できます。

ただし、ベスト プラクティスとして、組織のユーザーは別のアカウントを使用して認証する必要があります。 この方法により、アプリケーションに付与されるアクセス許可を制限でき、アプリケーション コードが SQL インジェクション攻撃に対して脆弱な場合に、悪意のあるアクティビティのリスクを軽減できます。

SQL Server 認証済みユーザーを作成するには、サーバーの **マスター** データベースにサーバー管理ログインを使用して接続し、新しいサーバー ログインを作成します。  また、マスター データベースでもユーザーを作成することをお勧めします。 マスターでユーザーを作成すると、ユーザーはデータベース名を指定せずに SSMS のようなツールを使用してログインできます。  また、オブジェクト エクスプ ローラーを使用して、サーバーのすべてのデータベースを表示することもできます。

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

次に、サーバー管理者ログインを使用して **専用 SQL プール (旧称 SQL DW)** に接続し、作成したサーバー ログインに基づいてデータベース ユーザーを作成します。

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

ログインの作成や新しいデータベースの作成などの追加の操作を実行するアクセス許可をユーザーに与えるには、master データベース内の `Loginmanager` ロールと `dbmanager` ロールにユーザーを割り当てます。

これらの追加のロールと SQL Database の認証の詳細については、[Azure SQL Database におけるデータベースとログインの管理](../../azure-sql/database/logins-create-manage.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)に関する記事をご覧ください。  Azure Active Directory を使用した接続の詳細については、[Azure Active Directory Authentication を使用した接続](sql-data-warehouse-authentication.md)に関する記事を参照してください。

## <a name="authorization"></a>承認

承認とは、ユーザーが認証され、接続された後にデータベース内で実行できることを意味します。 承認の特権は、ロール メンバーシップとアクセス許可によって指定されます。 ベスト プラクティスとして、必要最低限の特権をユーザーに付与することをお勧めします。 ロールを管理するには、次のストアド プロシージャを使用します。

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

接続しているサーバー管理者のアカウントは db_owner のメンバーであり、データベース内ですべての操作を実行する権限を持ちます。 スキーマのアップグレードやその他の管理操作をデプロイするために、このアカウントを保存します。 アクセス許可が限定された "ApplicationUser" アカウントを使用して、アプリケーションで必要な最小限の特権により、アプリケーションをデータベースに接続します。

ユーザーがデータベースで実行できる操作をさらに制限する方法がいくつかあります。

* 詳細な[アクセス許可](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)により、データベース内の個々の列、テーブル、ビュー、スキーマ、プロシージャ、およびその他のオブジェクトで実行できる操作を制御できます。 詳細なアクセス許可を使用して最大限の制御を行い、必要最小限のアクセス許可を付与します。
* db_datareader と db_datawriter 以外の[データベース ロール](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)を使用して、より権限の大きなアプリケーション ユーザー アカウント、またはより権限の小さな管理アカウントを作成できます。 組み込みの固定データベース ロールを使用すると、簡単にアクセス許可を付与できますが、その結果、必要以上のアクセス許可を付与することになる可能性があります。
* [ストアド プロシージャ](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) を使用すると、データベースで実行できるアクションを制限できます。

次の例では、ユーザー定義スキーマに対する読み取りアクセス許可を付与します。

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Azure portal または Azure Resource Manager API を使用したデータベースとサーバーの管理は、ポータル ユーザー アカウントのロールの割り当てによって制御されます。 詳細については、[Azure portal を使用して Azure ロールを割り当てる方法](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)に関するページを参照してください。

## <a name="encryption"></a>暗号化

Transparent Data Encryption (TDE) を使用すると、保存データを暗号化および暗号化解除することにより、悪意のあるアクティビティの脅威から保護できます。 データベースを暗号化すると、アプリケーションに変更を加える必要なく、関連付けられているバックアップとトランザクション ログ ファイルが暗号化されます。 TDE は、データベース暗号化キーと呼ばれる対称キーを使用してデータベース全体のストレージを暗号化します。

SQL Database では、データベース暗号化キーは組み込まれているサーバー証明書によって保護されます。 組み込みのサーバー証明書は、サーバーごとに一意です。 Microsoft は、少なくとも 90 日ごとにこれらの証明書を自動的に回転します。 使用される暗号化アルゴリズムは、AES-256 です。 TDE の一般的な説明については、「 [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」を参照してください。

データベースは、[Azure portal](sql-data-warehouse-encryption-tde.md) または [T-SQL](sql-data-warehouse-encryption-tde-tsql.md) を使用して暗号化できます。

## <a name="next-steps"></a>次のステップ

さまざまなプロトコルでのウェアハウスへの接続の詳細と例については、[専用 SQL プール (旧称 SQL DW) への接続](sql-data-warehouse-connect-overview.md)に関するページを参照してください。
