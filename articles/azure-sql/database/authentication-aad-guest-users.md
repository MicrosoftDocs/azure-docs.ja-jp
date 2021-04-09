---
title: Azure AD ゲスト ユーザーを作成する
description: Azure SQL Database、Azure SQL Managed Instance、および Azure Synapse Analytics で Azure AD グループを使用せずに Azure AD ゲストユーザーを作成し、Azure AD 管理者として設定する方法
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: 7a4d9fb9f803a497e84fa189d9a89c2d9097bb70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92675046"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>Azure AD ゲスト ユーザーを作成し、Azure AD 管理者として設定する

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> この記事は **パブリック プレビュー** 段階です。

Azure Active Directory (Azure AD) のゲスト ユーザーは、他の Azure Active Directory またはその外部から現在の Azure AD にインポートされているユーザーです。 たとえば、ゲスト ユーザーには、他の Azure Active Directory から、または *\@outlook.com*、 *\@hotmail.com*、 *\@live.com*、 *\@gmail.com* などのアカウントからのユーザーを含めることができます。 この記事では、Azure AD ゲスト ユーザーを作成し、Azure AD 内のグループにそのゲスト ユーザーを含める必要なしに、そのユーザーを Azure SQL 論理サーバーの Azure AD 管理者として設定する方法について説明します。

## <a name="feature-description"></a>機能の説明

この機能により、ゲスト ユーザーが、Azure AD で作成されたグループのメンバーである場合にのみ、Azure SQL Database、SQL Managed Instance、または Azure Synapse Analytics に接続できるという現在の制限が解除されます。 グループは、特定のデータベースで [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) ステートメントを使用して、手動でユーザーにマップする必要がありました。 ゲスト ユーザーを含む Azure AD グループに対してデータベース ユーザーが作成されると、ゲスト ユーザーは、Azure Active Directory を使用して MFA 認証でデータベースにサインインできます。 この **パブリック プレビュー** の一部として、ゲスト ユーザーを作成して SQL Database、SQL Managed Instance、または Azure Synapse に直接接続することができ、最初に Azure AD グループに追加してから、その Azure AD グループのデータベース ユーザーを作成する必要はありません。

この機能の一部として、Azure AD ゲスト ユーザーを Azure SQL 論理サーバーの AD 管理者として直接設定する機能もあります。 ゲスト ユーザーを Azure AD グループに含めてから、そのグループを Azure SQL 論理サーバーの Azure AD 管理者として設定できる既存の機能には影響ありません。 Azure AD グループに含まれているデータベース内のゲスト ユーザーにも、この変更による影響はありません。

Azure AD グループを使用するゲスト ユーザーに対する既存のサポートの詳細については、「[Azure Active Directory の多要素認証の使用](authentication-mfa-ssms-overview.md)」を参照してください。

## <a name="prerequisite"></a>前提条件

- PowerShell を使用して、ゲスト ユーザーを Azure SQL 論理サーバーの Azure AD 管理者として設定する場合、[Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 以上のモジュールが必要です。

## <a name="create-database-user-for-azure-ad-guest-user"></a>Azure AD ゲスト ユーザーのデータベース ユーザーを作成する 

Azure AD ゲスト ユーザーを使用してデータベース ユーザーを作成するには、これらの手順に従います。

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>SQL Database と Azure Synapse でゲスト ユーザーを作成する

1. ゲスト ユーザー (`user1@gmail.com` など) が既に Azure AD に追加されており、データベース サーバーに Azure AD 管理者が設定されていることを確認します。 Azure Active Directory 認証を行うには、Azure AD 管理者が必要です。

1. Azure AD 管理者、またはユーザーを作成するための十分な SQL アクセス許可を持つ Azure AD ユーザーとして SQL Database に接続し、ゲスト ユーザーを追加する必要があるデータベースで次のコマンドを実行します。

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. これで、ゲスト ユーザー `user1@gmail.com` 用にデータベース ユーザーが作成されているはずです。

1. 次のコマンドを実行して、データベース ユーザーが正常に作成されたことを確認します。

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. 接続を解除し、[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) を使用して、 **[Azure Active Directory - MFA で汎用]** の認証方法によって、ゲスト ユーザー `user1@gmail.com` としてデータベースにサインインします。 詳細については、「[Azure Active Directory の多要素認証の使用](authentication-mfa-ssms-overview.md)」を参照してください。

### <a name="create-guest-user-in-sql-managed-instance"></a>SQL Managed Instance でゲスト ユーザーを作成する

> [!NOTE]
> SQL Managed Instance では、Azure AD ユーザーと、Azure AD の包含データベース ユーザーのログインがサポートされています。 以下の手順では、SQL Managed Instance で Azure AD ゲスト ユーザーのログインとユーザーを作成する方法を示します。 また、「[SQL Database と Azure Synapse でゲスト ユーザーを作成する](#create-guest-user-in-sql-database-and-azure-synapse)」セクションの方法を使用して、SQL Managed Instance で[包含データベース ユーザー](/sql/relational-databases/security/contained-database-users-making-your-database-portable)を作成することもできます。

1. ゲスト ユーザー (`user1@gmail.com` など) が既に Azure AD に追加されており、SQL Managed Instance サーバーに Azure AD 管理者が設定されていることを確認します。 Azure Active Directory 認証を行うには、Azure AD 管理者が必要です。

1. Azure AD 管理者、またはユーザーを作成するための十分な SQL アクセス許可を持つ Azure AD ユーザーとして SQL Managed Instance サーバーに接続し、`master` データベースで次のコマンドを実行して、ゲスト ユーザーのログインを作成します。

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. これで、`master` データベースにゲスト ユーザー `user1@gmail.com` 用のログインが作成されているはずです。

1. 次のコマンドを実行して、ログインが正常に作成されたことを確認します。

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. ゲスト ユーザーを追加する必要があるデータベースで、次のコマンドを実行します。 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. これで、ゲスト ユーザー `user1@gmail.com` 用にデータベース ユーザーが作成されているはずです。

1. 接続を解除し、[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) を使用して、 **[Azure Active Directory - MFA で汎用]** の認証方法によって、ゲスト ユーザー `user1@gmail.com` としてデータベースにサインインします。 詳細については、「[Azure Active Directory の多要素認証の使用](authentication-mfa-ssms-overview.md)」を参照してください。

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>ゲスト ユーザーを Azure AD 管理者として設定する

SQL 論理サーバーの Azure AD 管理者として Azure AD ゲスト ユーザーを設定するには、これらの手順に従います。

### <a name="set-azure-ad-admin-for-sql-database-and-azure-synapse"></a>SQL Database と Azure Synapse 用の Azure AD 管理者を設定する

1. ゲスト ユーザー (`user1@gmail.com`など) が既に Azure AD に追加されていることを確認します。

1. 次の PowerShell コマンドを実行して、ゲスト ユーザーを Azure SQL 論理サーバーの Azure AD 管理者として追加します。

    - `<ResourceGroupName>` は、Azure SQL 論理サーバーを含む Azure リソース グループ名に置き換えます。
    - `<ServerName>` は、実際の Azure SQL 論理サーバーの名前に置き換えます。 実際のサーバー名が `myserver.database.windows.net` の場合、`<Server Name>` を `myserver` に置き換えます。
    - `<DisplayNameOfGuestUser>` は、実際のゲスト ユーザー名に置き換えます。

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

    また、Azure CLI コマンド [az sql server ad-admin](/cli/azure/sql/server/ad-admin) を使用して、ゲスト ユーザーを Azure SQL 論理サーバーの Azure AD 管理者として設定することもできます。

### <a name="set-azure-ad-admin-for-sql-managed-instance"></a>SQL Managed Instance の Azure AD 管理者を設定する

1. ゲスト ユーザー (`user1@gmail.com`など) が既に Azure AD に追加されていることを確認します。

1. [Azure portal](https://portal.azure.com) に移動し、**Azure Active Directory** リソースに移動します。 **[管理]** で **[ユーザー]** ペインに移動します。 ゲスト ユーザーを選択し、`Object ID` を記録します。 

1. 次の PowerShell コマンドを実行して、ゲスト ユーザーを SQL Managed Instance の Azure AD 管理者として追加します。

    - `<ResourceGroupName>` は、SQL Managed Instance を含む Azure リソース グループ名に置き換えます。
    - `<ManagedInstanceName>` は、実際の SQL Managed Instance 名に置き換えます。
    - `<DisplayNameOfGuestUser>` は、実際のゲスト ユーザー名に置き換えます。
    - `<AADObjectIDOfGuestUser>` は、前に収集した `Object ID` に置き換えます。

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

    また、Azure CLI コマンド [az sql mi ad-admin](/cli/azure/sql/mi/ad-admin) を使用して、ゲスト ユーザーを SQL Managed Instance の Azure AD 管理者として設定することもできます。

## <a name="limitations"></a>制限事項

Azure portal には、Azure AD ゲストユーザーを SQL Managed Instance の Azure AD 管理者として選択できない制限があります。 *\@outlook.com*、 *\@hotmail.com*、 *\@live.com*、 *\@gmail.com* など、Azure AD 外部のゲスト アカウントの場合、AD 管理者セレクターにはこれらのアカウントは表示されますが、グレー表示され、選択することはできません。 上記の [PowerShell または CLI コマンド](#setting-a-guest-user-as-an-azure-ad-admin)を使用して Azure AD 管理者を設定します。または、ゲスト ユーザーを含む Azure AD グループを SQL Managed Instance の Azure AD 管理者として設定することもできます。

この機能の一般提供が開始される前に、この機能は SQL Managed Instance に対して有効になります。

## <a name="next-steps"></a>次のステップ

- [Azure SQL での Azure AD 認証を構成して管理する](authentication-aad-configure.md)
- [Azure Active Directory の多要素認証の使用](authentication-mfa-ssms-overview.md)
- [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)