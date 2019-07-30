---
title: Azure AD サーバー プリンシパル (ログイン) を使用した Azure SQL Database マネージド インスタンスのセキュリティ | Microsoft Docs
description: Azure SQL Database のマネージド インスタンスをセキュリティで保護し、Azure AD サーバー プリンシパル (ログイン) を使用するための手法と機能について説明します
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 02/20/2019
ms.openlocfilehash: 87bd22ec4f2cfae62d1f80284ad8346ca292d016
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567673"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-server-principals-logins"></a>チュートリアル:Azure AD サーバー プリンシパル (ログイン) を使用した Azure SQL Database におけるマネージド インスタンスのセキュリティ

マネージド インスタンスには、オンプレミスの最新の SQL Server (Enterprise Edition) データベース エンジンにあるセキュリティ機能がほとんどすべて備わっています。

- 分離環境におけるアクセスの制限
- ID を要求する認証メカニズム (Azure AD、SQL 認証) の使用
- ロールベースのメンバーシップとアクセス許可による認可の使用
- セキュリティ機能の有効化

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> - マネージド インスタンス用に Azure Active Directory (AD) サーバー プリンシパル (ログイン) を作成する
> - マネージド インスタンスの Azure AD サーバー プリンシパル (ログイン) にアクセス許可を付与する
> - Azure AD サーバー プリンシパル (ログイン) から Azure AD ユーザーを作成する
> - Azure AD ユーザーにアクセス許可を割り当ててデータベースのセキュリティを管理する
> - Azure AD ユーザーで偽装を使用する
> - Azure AD ユーザーでデータベース間クエリを使用する
> - 脅威の防止、監査、データ マスク、暗号化などのセキュリティ機能について学習する

> [!NOTE]
> マネージド インスタンスの Azure AD サーバー プリンシパル (ログイン) は、**パブリック プレビュー**段階にあります。

詳細については、[Azure SQL Database マネージド インスタンスの概要](sql-database-managed-instance-index.yml)と[機能](sql-database-managed-instance.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、前提条件として次のものが必要です。

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Azure SQL Database マネージド インスタンス
  - 次の記事に従います。[クイック スタート:Azure SQL Database マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)
- マネージド インスタンスにアクセスでき、[マネージド インスタンスの Azure AD 管理者をプロビジョニング済み](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)であること。 詳細については、次を参照してください。
    - [マネージド インスタンスへのアプリケーションの接続](sql-database-managed-instance-connect-app.md)に関するページ 
    - [マネージド インスタンスの接続アーキテクチャ](sql-database-managed-instance-connectivity-architecture.md)に関するページ
    - [SQL による Azure Active Directory 認証の構成と管理](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>マネージド インスタンスへのアクセスの制限

マネージド インスタンスには、プライベート IP アドレスを介してのみアクセスできます。 マネージド インスタンスのネットワーク外からマネージド インスタンスに接続できるサービス エンドポイントはありません。 分離された SQL Server のオンプレミス環境とほぼ同様に、接続を確立するには、アプリケーションまたはユーザーがマネージド インスタンスのネットワーク (VNet) にアクセスする必要があります。 詳細については、[マネージド インスタンスへのアプリケーションの接続](sql-database-managed-instance-connect-app.md)に関する記事を参照してください。

> [!NOTE] 
> マネージド インスタンスにはその VNET の内部のみでアクセスできるため、[SQL Database のファイアウォール規則](sql-database-firewall-configure.md)は適用されません。 マネージド インスタンスには、[組み込みのファイアウォール](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)が独自に用意されています。

## <a name="create-an-azure-ad-server-principal-login-for-a-managed-instance-using-ssms"></a>SSMS を使用してマネージド インスタンス用に Azure AD サーバー プリンシパル (ログイン) を作成する

最初の Azure AD サーバー プリンシパル (ログイン) は、標準の SQL Server アカウント (Azure AD 以外) である `sysadmin` で作成する必要があります。 マネージド インスタンスに接続する例については、次の記事を参照してください。

- [クイック スタート:マネージド インスタンスに接続するように Azure VM を構成する](sql-database-managed-instance-configure-vm.md)
- [クイック スタート:オンプレミスからマネージド インスタンスへのポイント対サイト接続を構成する](sql-database-managed-instance-configure-p2s.md)

> [!IMPORTANT]
> マネージド インスタンスの設定に使用された Azure AD 管理者は、マネージド インスタンス内での Azure AD サーバー プリンシパル (ログイン) の作成には使用できません。 SQL Server アカウント `sysadmin` を使用して、最初の Azure AD サーバー プリンシパル (ログイン) を作成する必要があります。 これは一時的な制限事項で、Azure AD サーバー プリンシパル (ログイン) が一般提供されると削除されます。 次のエラーは、Azure AD 管理者アカウントを使用してログインを作成しようとした場合に表示されます。`Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`

1. [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance) を使用して、標準の SQL Server アカウント (Azure AD 以外) である `sysadmin` を使ってマネージド インスタンスにログインします。

2. **オブジェクト エクスプローラー**で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。

3. クエリ ウィンドウで、次の構文を使用して、ローカルの Azure AD アカウント用のログインを作成します。

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    この例では、nativeuser@aadsqlmi.onmicrosoft.com アカウントのログインが作成されます。

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. ツール バーの **[実行]** を選択して、ログインを作成します。

5. 次の T-SQL コマンドを実行して、新しく追加されたログインを確認します。

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![native-login.png](media/sql-database-managed-instance-security-tutorial/native-login.png)

詳細については、「[CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)」を参照してください。

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>マネージド インスタンスのログインを作成できるようにするためのアクセス許可の付与

他の Azure AD サーバー プリンシパル (ログイン) を作成するには、SQL Server のロールまたはアクセス許可をプリンシパル (SQL または Azure AD) に付与する必要があります。

### <a name="sql-authentication"></a>SQL 認証

- ログインが SQL プリンシパルの場合、作成コマンドを使用して Azure AD アカウントのログインを作成できるのは、`sysadmin` ロールに属しているログインのみです。

### <a name="azure-ad-authentication"></a>Azure AD 認証

- 新しく作成した Azure AD サーバー プリンシパル (ログイン) が他の Azure AD ユーザー、グループ、またはアプリケーションのログインを作成できるように、そのログインに `sysadmin` または `securityadmin` サーバー ロールを付与します。 
- 少なくとも、他の Azure AD サーバー プリンシパル (ログイン) を作成するには **ALTER ANY LOGIN** アクセス許可を Azure AD サーバー プリンシパル (ログイン) に付与する必要があります。 
- 既定では、マスターに新しく作成された Azure AD サーバー プリンシパル (ログイン) に付与される標準的なアクセス許可は **CONNECT SQL** と **VIEW ANY DATABASE** です。
- `sysadmin` サーバー ロールは、マネージド インスタンス内の多くの Azure AD サーバー プリンシパル (ログイン) に付与できます。

`sysadmin` サーバー ロールにログインを追加するには:

1. マネージド インスタンスにもう一度ログインするか、`sysadmin` である SQL プリンシパルとの既存の接続を使用します。

1. **オブジェクト エクスプローラー**で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。

1. 次の T-SQL 構文を使用して、Azure AD サーバー プリンシパル (ログイン) に `sysadmin` サーバー ロールを付与します。

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    次の例では、ログイン nativeuser@aadsqlmi.onmicrosoft.com に `sysadmin` サーバー ロールを付与します。

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>SSMS を使用して追加の Azure AD サーバー プリンシパル (ログイン) を作成する

Azure AD サーバー プリンシパル (ログイン) が作成され、`sysadmin` の特権が付与されると、そのログインは、**CREATE LOGIN** で **FROM EXTERNAL PROVIDER** 句を使用して追加のログインを作成できます。

1. SQL Server Management Studio を使用して、Azure AD サーバー プリンシパル (ログイン) でマネージド インスタンスに接続します。 マネージド インスタンスのホスト名を入力します。 SSMS での認証の場合、Azure AD アカウントを使ってログインするときに選択できるオプションは 3 つあります。

   - Active Directory - MFA サポートで汎用
   - Active Directory - パスワード
   - Active Directory - 統合 </br>

     ![ssms-login-prompt.png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

     詳細については、次の記事を参照してください。[SQL Database と SQL Data Warehouse でのユニバーサル認証 (MFA 対応の SSMS サポート)](sql-database-ssms-mfa-authentication.md)

1. **[Active Directory - MFA サポートで汎用]** を選択します。 これで、Multi-Factor Authentication (MFA) のログイン ウィンドウが表示されます。 Azure AD のパスワードを使ってサインインします。

    ![mfa-login-prompt.png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. SSMS の**オブジェクト エクスプローラー**で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。
1. クエリ ウィンドウで、次の構文を使用して、別の Azure AD アカウント用のログインを作成します。

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    この例では、Azure AD ユーザー bob@aadsqlmi.net のログインが作成されます。このユーザーのドメイン aadsqlmi.net は、Azure AD の aadsqlmi.onmicrosoft.com とフェデレーションされます。

    次の T-SQL コマンドを実行します。 フェデレーションされた Azure AD アカウントは、マネージド インスタンスでオンプレミスの Windows のログインとユーザーの代わりに使用されます。

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) 構文を使用して、マネージド インスタンスにデータベースを作成します。 このデータベースは、次のセクションでユーザー ログインをテストする際に使用されます。
    1. **オブジェクト エクスプローラー**で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。
    1. クエリ ウィンドウで、次の構文を使用して、**MyMITestDB** という名前のデータベースを作成します。

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Azure AD 内のグループにマネージド インスタンスのログインを作成します。 マネージド インスタンスにログインを追加するには、事前にこのグループが Azure AD に存在する必要があります。 「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。 _mygroup_ グループを作成し、このグループにメンバーを追加します。

1. SQL Server Management Studio で新しいクエリ ウィンドウを開きます。

    この例では、_mygroup_ というグループが Azure AD に存在することを前提としています。 次のコマンドを実行します。

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. テストとして、新しく作成したログインまたはグループを使用してマネージド インスタンスにログインします。 マネージド インスタンスへの新しい接続を開き、認証時に新しいログインを使用します。
1. **オブジェクト エクスプローラー**で、サーバーを右クリックし、新しい接続に **[新しいクエリ]** を選択します。
1. 次のコマンドを実行して、新しく作成した Azure AD サーバー プリンシパル (ログイン) のサーバー アクセス許可を確認します。

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Azure AD のゲスト ユーザーは、Azure AD グループの一員として追加されている場合のみ、マネージド インスタンスのログインがサポートされます。 Azure AD のゲスト ユーザーは、マネージド インスタンスが属している Azure AD に別の Azure AD から招待されるアカウントです。 たとえば、joe@contoso.com (Azure AD アカウント) または steve@outlook.com (MSA アカウント) は、Azure AD aadsqlmi 内のグループに追加できます。 ユーザーがグループに追加されると、**CREATE LOGIN** 構文を使用して、そのグループのマネージド インスタンス **master** データベースにログインを作成できます。 このグループのメンバーであるゲスト ユーザーは、現在のログイン (joe@contoso.com や steve@outlook.com など) を使用してマネージド インスタンスに接続できます。

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login-and-give-permissions"></a>Azure AD サーバー プリンシパル (ログイン) から Azure AD ユーザーを作成してアクセス許可を付与する

個々のデータベースに対する承認は、オンプレミスの SQL Server の場合とほぼ同じようにマネージド インスタンスで動作します。 ユーザーは、既存のログインからデータベースに作成し、そのデータベースに対するアクセス許可を付与するかデータベース ロールに追加することができます。

**MyMITestDB** という名前のデータベースと、既定のアクセス許可のみを持つログインを作成したので、次の手順では、そのログインからユーザーを作成します。 この時点では、ログインはマネージド インスタンスに接続し、すべてのデータベースを表示できますが、そのデータベースを操作することはできません。 既定のアクセス許可を持つ Azure AD アカウントでサインインし、新しく作成したデータベースを展開しようとすると、次のエラーが表示されます。

![ssms-db-not-accessible.png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

データベースのアクセス許可の付与の詳細については、「[データベース エンジンの権限の概要](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions)」を参照してください。

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Azure AD ユーザーを作成してサンプル テーブルを作成する

1. SQL Server Management Studio で、`sysadmin` アカウントを使用してマネージド インスタンスにログインします。
1. **オブジェクト エクスプローラー**で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。
1. クエリ ウィンドウで、次の構文を使用して、Azure AD サーバー プリンシパル (ログイン) から Azure AD ユーザーを作成します。

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    次の例では、ログイン bob@aadsqlmi.net: からユーザー bob@aadsqlmi.net を作成します。

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. また、グループである Azure AD サーバー プリンシパル (ログイン) から Azure AD ユーザーを作成することもサポートされています。

    次の例では、Azure AD に存在する _mygroup_ という名前の Azure AD グループのログインを作成します。

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    **mygroup** に属しているすべてのユーザーが **MyMITestDB** データベースにアクセスできます。

    > [!IMPORTANT]
    > AD サーバー プリンシパル (ログイン) から **USER** を作成するときは、user_name を **LOGIN** と同じ login_name として指定します。

    詳細については、「[CREATE USER](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current)」を参照してください。

1. 新しいクエリ ウィンドウで、次の T-SQL コマンドを使用してテスト テーブルを作成します。

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. 作成されたユーザーとの接続を SSMS で作成します。 以前に `sysadmin` が作成した **TestTable** テーブルが表示されないことがわかります。 ユーザーに、データベースからデータを読み取るためのアクセス許可を付与する必要があります。

1. 次のコマンドを実行すると、ユーザーが所有する現在のアクセス許可を確認できます。

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>データベースレベルのロールにユーザーを追加する

ユーザーがデータベース内のデータを表示できるように、[データベースレベルのロール](/sql/relational-databases/security/authentication-access/database-level-roles)をユーザーに付与します。

1. SQL Server Management Studio で、`sysadmin` アカウントを使用してマネージド インスタンスにログインします。

1. **オブジェクト エクスプローラー**で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。

1. 次の T-SQL 構文を使用して、Azure AD ユーザーに `db_datareader` データベース ロールを付与します。

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    次の例では、ユーザー bob@aadsqlmi.net とグループ _mygroup_ に **MyMITestDB** データベースに対する `db_datareader` アクセス許可を付与します。

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. 次のコマンドを実行して、データベースに作成された Azure AD ユーザーが存在することを確認します。

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. `db_datareader` ロールに追加されたユーザーを使用して、マネージド インスタンスへの新しい接続を作成します。
1. **オブジェクト エクスプローラー**でデータベースを展開してテーブルを表示します。

    ![ssms-test-table.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. 新しいクエリ ウィンドウを開き、次の SELECT ステートメントを実行します。

    ```sql
    SELECT *
    FROM TestTable
    ```

    テーブルのデータを確認できますか。 列が返されていることがわかるはずです。

    ![ssms-test-table-query.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Azure AD サーバーレベル プリンシパル (ログイン) を偽装する

マネージド インスタンスは、Azure AD サーバーレベル プリンシパル (ログイン) の偽装をサポートしています。

### <a name="test-impersonation"></a>偽装のテスト

1. SQL Server Management Studio で、`sysadmin` アカウントを使用してマネージド インスタンスにログインします。

1. **オブジェクト エクスプローラー**で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。

1. クエリ ウィンドウで、次のコマンドを使用して新しいストアド プロシージャを作成します。

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. 次のコマンドを使用して、このストアド プロシージャの実行時に偽装しているユーザーが **bob\@aadsqlmi.net** であることを確認します。

    ```sql
    Exec dbo.usp_Demo
    ```

1. EXECUTE AS LOGIN ステートメントを使用して偽装をテストします。

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Azure AD プリンシパルを対象とした次の操作を実行できるのは、`sysadmin` ロールに属している SQL サーバーレベル プリンシパル (ログイン) のみです。 
> - EXECUTE AS USER
> - EXECUTE AS LOGIN

## <a name="using-cross-database-queries-in-managed-instances"></a>マネージド インスタンスでの複数データベース間クエリの使用

データベース間クエリは、Azure AD サーバー プリンシパル (ログイン) を使用する Azure AD アカウントのためにサポートされています。 Azure AD グループでデータベース間クエリをテストするには、データベースとテーブルをもう 1 つ作成する必要があります。 データベースとテーブルが既にもう 1 つ存在する場合はその作成をスキップできます。

1. SQL Server Management Studio で、`sysadmin` アカウントを使用してマネージド インスタンスにログインします。
1. **オブジェクト エクスプローラー**で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。
1. クエリ ウィンドウで、次のコマンドを使用して、**MyMITestDB2** という名前のデータベースと **TestTable2** という名前のテーブルを作成します。

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. 新しいクエリ ウィンドウで、次のコマンドを実行して、新しいデータベース **MyMITestDB2** にユーザー _mygroup_ を作成し、そのデータベースに対する SELECT アクセス許可を _mygroup_ に付与します。

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. SQL Server Management Studio を使用して、Azure AD グループ _mygroup_ のメンバーとしてマネージド インスタンスにサインインします。 新しいクエリ ウィンドウを開き、データベース間の SELECT ステートメントを実行します。

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    **TestTable2** からテーブル結果が表示されます。

## <a name="additional-scenarios-supported-for-azure-ad-server-principals-logins-public-preview"></a>Azure AD サーバー プリンシパル (ログイン) でサポートされているその他のシナリオ (パブリック プレビュー) 

- SQL エージェントの管理とジョブの実行が、Azure AD サーバー プリンシパル (ログイン) に対してサポートされています。
- データベースのバックアップと復元操作は、Azure AD サーバー プリンシパル (ログイン) が実行できます。
- Azure AD サーバー プリンシパル (ログイン) と認証イベントに関連するすべてのステートメントの[監査](sql-database-managed-instance-auditing.md)。
- `sysadmin` サーバー ロールのメンバーである Azure AD サーバー プリンシパル (ログイン) の専用管理者接続。
- Azure AD サーバー プリンシパル (ログイン) は、[sqlcmd ユーティリティ](/sql/tools/sqlcmd-utility)と [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ツールの使用でサポートされています。
- Azure AD サーバー プリンシパル (ログイン) によるログオン イベントでは、ログオン トリガーがサポートされています。
- Service Broker とデータベース メールは、Azure AD サーバー プリンシパル (ログイン) を使用して設定できます。


## <a name="next-steps"></a>次の手順

### <a name="enable-security-features"></a>セキュリティ機能の有効化

データベースをセキュリティで保護する方法をまとめた一覧については、次の[マネージド インスタンスのセキュリティ機能](sql-database-managed-instance.md#azure-sql-database-security-features)に関する記事を参照してください。 次のセキュリティ機能について説明しています。

- [マネージド インスタンスの監査](sql-database-managed-instance-auditing.md) 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [脅威の検出](sql-database-managed-instance-threat-detection.md) 
- [動的データ マスク](/sql/relational-databases/security/dynamic-data-masking)
- [行レベルのセキュリティ](/sql/relational-databases/security/row-level-security) 
- [透過的なデータ暗号化 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>マネージド インスタンスの機能

マネージド インスタンスの機能全体の概要については、以下を参照してください。

> [!div class="nextstepaction"]
> [マネージド インスタンスの機能](sql-database-managed-instance.md)
