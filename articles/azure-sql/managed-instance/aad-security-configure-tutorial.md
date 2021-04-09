---
title: Azure AD サーバー プリンシパル (ログイン) による SQL Managed Instance のセキュリティ
description: Azure SQL Managed Instance をセキュリティで保護し、Azure AD サーバー プリンシパル (ログイン) を使用するための手法と機能について説明します
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: 9161bf4f99ddfed479451d2091458ab309aa2c17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92788623"
---
# <a name="tutorial-security-in-azure-sql-managed-instance-using-azure-ad-server-principals-logins"></a>チュートリアル:Azure AD サーバー プリンシパル (ログイン) を使用した Azure SQL Managed Instance のセキュリティ
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance には、最新の SQL Server (Enterprise Edition) データベース エンジンにあるセキュリティ機能がほとんどすべて備わっています。

- 分離環境におけるアクセスの制限
- ID の入力を求める認証メカニズムの使用: Azure Active Directory (Azure AD) および SQL 認証
- ロールベースのメンバーシップとアクセス許可による認可の使用
- セキュリティ機能の有効化

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> - マネージド インスタンス用に Azure AD サーバー プリンシパル (ログイン) を作成する
> - マネージド インスタンスの Azure AD サーバー プリンシパル (ログイン) にアクセス許可を付与する
> - Azure AD サーバー プリンシパル (ログイン) から Azure AD ユーザーを作成する
> - Azure AD ユーザーにアクセス許可を割り当ててデータベースのセキュリティを管理する
> - Azure AD ユーザーで偽装を使用する
> - Azure AD ユーザーでデータベース間クエリを使用する
> - 脅威の防止、監査、データ マスク、暗号化などのセキュリティ機能について学習する

詳細については、[Azure SQL Managed Instance の概要](sql-managed-instance-paas-overview.md)に関する記事を参照してください。 

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、前提条件として次のものが必要です。

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- マネージド インスタンス
  - 次の記事に従います。[クイック スタート: マネージド インスタンスを作成する](instance-create-quickstart.md)
- マネージド インスタンスにアクセスでき、[マネージド インスタンスの Azure AD 管理者をプロビジョニング済み](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)であること。 詳細については、次を参照してください。
  - [マネージド インスタンスへのアプリケーションの接続](connect-application-instance.md)に関するページ
  - [SQL Managed Instance の接続アーキテクチャ](connectivity-architecture-overview.md)に関するページ
  - [SQL による Azure Active Directory 認証の構成と管理](../database/authentication-aad-configure.md)

## <a name="limit-access"></a>アクセスを制限する 

マネージド インスタンスには、プライベート IP アドレスを介してのみアクセスできます。 分離された SQL Server 環境とほぼ同様に、接続を確立するには、アプリケーションまたはユーザーが SQL Managed Instance のネットワーク (VNet) にアクセスする必要があります。 詳細については、[SQL Managed Instance へのアプリケーションの接続](connect-application-instance.md)に関するページを参照してください。

また、マネージド インスタンスでサービス エンドポイントを構成することもできます。これにより、Azure SQL Database の場合と同じ方法でパブリック接続を使用できるようになります。
詳細については、「[Azure SQL Managed Instance でパブリック エンドポイントを構成する](public-endpoint-configure.md)」を参照してください。

> [!NOTE]
> サービス エンドポイントが有効な状態であっても、[Azure SQL Database のファイアウォール規則](../database/firewall-configure.md)は適用されません。 Azure SQL Managed Instance には、接続を管理するための[組み込みのファイアウォール](management-endpoint-verify-built-in-firewall.md)が独自に備わっています。

## <a name="create-an-azure-ad-server-principal-login-using-ssms"></a>SSMS を使用して Azure AD サーバー プリンシパル (ログイン) を作成する

最初の Azure AD サーバー プリンシパル (ログイン) は、標準の SQL 管理者アカウント (Azure AD 以外) である `sysadmin` によって、またはプロビジョニング処理中に作成されたマネージド インスタンスの Azure AD 管理者によって作成できます。 詳細については、[SQL Managed Instance の Azure Active Directory 管理者のプロビジョニング](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)に関するページを参照してください。

SQL Managed Instance に接続する例については、以下の記事を参照してください。

- [クイック スタート: SQL Managed Instance に接続するように Azure VM を構成する](connect-vm-instance-configure.md)
- [クイック スタート: オンプレミスから SQL Managed Instance へのポイント対サイト接続を構成する](point-to-site-p2s-configure.md)

1. [SQL Server Management Studio](point-to-site-p2s-configure.md#connect-with-ssms) を利用し、標準の SQL ログイン アカウント (Azure AD 以外) である `sysadmin` を使用するか、SQL Managed Instance 用の Azure AD 管理者を使用して、マネージド インスタンスにログインします。

2. **オブジェクト エクスプローラー** で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。

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

    ![SSMS オブジェクト エクスプローラーの [結果] タブのスクリーンショット。新しく追加されたログインの name、principal_id、sid、type、および type_desc が表示されています。](./media/aad-security-configure-tutorial/native-login.png)

詳細については、「[CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)」を参照してください。

## <a name="grant-permissions-to-create-logins"></a>ログインを作成するためのアクセス許可を付与する

他の Azure AD サーバー プリンシパル (ログイン) を作成するには、SQL Server のロールまたはアクセス許可をプリンシパル (SQL または Azure AD) に付与する必要があります。

### <a name="sql-authentication"></a>SQL 認証

- ログインが SQL プリンシパルの場合、作成コマンドを使用して Azure AD アカウントのログインを作成できるのは、`sysadmin` ロールに属しているログインのみです。

### <a name="azure-ad-authentication"></a>Azure AD 認証

- 新しく作成した Azure AD サーバー プリンシパル (ログイン) が他の Azure AD ユーザー、グループ、またはアプリケーションのログインを作成できるように、そのログインに `sysadmin` または `securityadmin` サーバー ロールを付与します。
- 少なくとも、他の Azure AD サーバー プリンシパル (ログイン) を作成するには **ALTER ANY LOGIN** アクセス許可を Azure AD サーバー プリンシパル (ログイン) に付与する必要があります。
- 既定では、マスターに新しく作成された Azure AD サーバー プリンシパル (ログイン) に付与される標準的なアクセス許可は **CONNECT SQL** と **VIEW ANY DATABASE** です。
- `sysadmin` サーバー ロールは、マネージド インスタンス内の多くの Azure AD サーバー プリンシパル (ログイン) に付与できます。

`sysadmin` サーバー ロールにログインを追加するには:

1. マネージド インスタンスにもう一度ログインするか、Azure AD 管理者または SQL プリンシパルである `sysadmin` で既存の接続を使用します。

1. **オブジェクト エクスプローラー** で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。

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

1. SQL Server Management Studio を使用して、Azure AD サーバー プリンシパル (ログイン) でマネージド インスタンスに接続します。 SQL Managed Instance のホスト名を入力します。 SSMS での認証の場合、Azure AD アカウントを使ってログインするときに選択できるオプションは 3 つあります。

   - Active Directory - MFA サポートで汎用
   - Active Directory - パスワード
   - Active Directory - 統合 </br>

     ![SSMS の [サーバーへの接続] ダイアログのスクリーンショット。[認証] ドロップダウンで [Active Directory - MFA サポートで汎用] が選択されています。](./media/aad-security-configure-tutorial/ssms-login-prompt.png)

     詳細については、[ユニバーサル認証 (Multi-Factor Authentication 対応の SSMS サポート)](../database/authentication-mfa-ssms-overview.md) に関するページを参照してください。

1. **[Active Directory - MFA サポートで汎用]** を選択します。 これで、Multi-Factor Authentication のログイン ウィンドウが表示されます。 Azure AD のパスワードを使ってサインインします。

    ![Multi-Factor Authentication のログイン ウィンドウのスクリーンショット。[パスワードの入力] フィールドにカーソルがあります。](./media/aad-security-configure-tutorial/mfa-login-prompt.png)

1. SSMS の **オブジェクト エクスプローラー** で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。
1. クエリ ウィンドウで、次の構文を使用して、別の Azure AD アカウント用のログインを作成します。

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    この例では、Azure AD ユーザー bob@aadsqlmi.net のログインが作成されます。このユーザーのドメイン aadsqlmi.net は、Azure AD の aadsqlmi.onmicrosoft.com ドメインとフェデレーションされます。

    次の T-SQL コマンドを実行します。 フェデレーションされた Azure AD アカウントは、SQL Managed Instance でオンプレミスの Windows のログインとユーザーの代わりに使用されます。

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) 構文を使用して、マネージド インスタンスにデータベースを作成します。 このデータベースは、次のセクションでユーザー ログインをテストする際に使用されます。
    1. **オブジェクト エクスプローラー** で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。
    1. クエリ ウィンドウで、次の構文を使用して、**MyMITestDB** という名前のデータベースを作成します。

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Azure AD 内のグループに SQL Managed Instance のログインを作成します。 SQL Managed Instance にログインを追加するには、事前にこのグループが Azure AD に存在する必要があります。 「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。 _mygroup_ グループを作成し、このグループにメンバーを追加します。

1. SQL Server Management Studio で新しいクエリ ウィンドウを開きます。

    この例では、_mygroup_ というグループが Azure AD に存在することを前提としています。 たとえば、次のコマンドを実行します。

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. テストとして、新しく作成したログインまたはグループを使用してマネージド インスタンスにログインします。 マネージド インスタンスへの新しい接続を開き、認証時に新しいログインを使用します。
1. **オブジェクト エクスプローラー** で、サーバーを右クリックし、新しい接続に **[新しいクエリ]** を選択します。
1. 次のコマンドを実行して、新しく作成した Azure AD サーバー プリンシパル (ログイン) のサーバー アクセス許可を確認します。

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Azure AD のゲスト ユーザーは、Azure AD グループの一員として追加されている場合のみ、SQL Managed Instance のログインがサポートされます。 Azure AD のゲスト ユーザーは、マネージド インスタンスが属している Azure AD インスタンスに別の Azure AD インスタンスから招待されるアカウントです。 たとえば、joe@contoso.com (Azure AD アカウント) または steve@outlook.com (Microsoft アカウント) は、Azure AD aadsqlmi インスタンス内のグループに追加できます。 ユーザーがグループに追加されると、**CREATE LOGIN** 構文を使用して、そのグループの SQL Managed Instance **master** データベースにログインを作成できます。 このグループのメンバーであるゲスト ユーザーは、現在のログイン (joe@contoso.com や steve@outlook.com など) を使用してマネージド インスタンスに接続できます。

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login"></a>Azure AD サーバー プリンシパル (ログイン) から Azure AD ユーザーを作成する

個々のデータベースに対する承認は、SQL Managed Instance でも、SQL Server のデータベースの場合とほぼ同じように動作します。 ユーザーは、既存のログインからデータベースに作成し、そのデータベースに対するアクセス許可を付与するかデータベース ロールに追加することができます。

**MyMITestDB** という名前のデータベースと、既定のアクセス許可のみを持つログインを作成したので、次の手順では、そのログインからユーザーを作成します。 この時点では、ログインはマネージド インスタンスに接続し、すべてのデータベースを表示できますが、そのデータベースを操作することはできません。 既定のアクセス許可を持つ Azure AD アカウントでサインインし、新しく作成したデータベースを展開しようとすると、次のエラーが表示されます。

![SSMS オブジェクト エクスプローラー ウィンドウのエラー メッセージのスクリーンショット。"The database MyMITestDB is not accessible. (ObjectExplorer) (データベース MyMITestDB にアクセスできません。(ObjectExplorer))" と表示されています。](./media/aad-security-configure-tutorial/ssms-db-not-accessible.png)

データベースのアクセス許可の付与の詳細については、「[データベース エンジンの権限の概要](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions)」を参照してください。

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Azure AD ユーザーを作成してサンプル テーブルを作成する

1. SQL Server Management Studio で、`sysadmin` アカウントを使用してマネージド インスタンスにログインします。
1. **オブジェクト エクスプローラー** で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。
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

    次の例では、Azure AD インスタンスに存在する _mygroup_ という名前の Azure AD グループのログインを作成します。

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    *mygroup* に属しているすべてのユーザーが **MyMITestDB** データベースにアクセスできます。

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

1. **オブジェクト エクスプローラー** で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。

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
1. **オブジェクト エクスプローラー** でデータベースを展開してテーブルを表示します。

    ![MyMITestDB の [テーブル] のフォルダー構造を示す SSMS のオブジェクト エクスプローラーのスクリーンショット。 dbo.TestTable folder フォルダーが強調表示されています。](./media/aad-security-configure-tutorial/ssms-test-table.png)

1. 新しいクエリ ウィンドウを開き、次の SELECT ステートメントを実行します。

    ```sql
    SELECT *
    FROM TestTable
    ```

    テーブルのデータを確認できますか。 列が返されていることがわかるはずです。

    ![SSMS のオブジェクト エクスプローラーのスクリーンショット。テーブルの列ヘッダー、AccountNum、City、Name、State が表示されています。](./media/aad-security-configure-tutorial/ssms-test-table-query.png)

## <a name="impersonate-azure-ad-server-level-principals-logins"></a>Azure AD サーバーレベル プリンシパル (ログイン) を偽装する

SQL Managed Instance では、Azure AD サーバーレベル プリンシパル (ログイン) の偽装がサポートされています。

### <a name="test-impersonation"></a>偽装のテスト

1. SQL Server Management Studio で、`sysadmin` アカウントを使用してマネージド インスタンスにログインします。

1. **オブジェクト エクスプローラー** で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。

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
>
> - EXECUTE AS USER
> - EXECUTE AS LOGIN

## <a name="use-cross-database-queries"></a>複数データベース間のクエリを使用する

データベース間クエリは、Azure AD サーバー プリンシパル (ログイン) を使用する Azure AD アカウントのためにサポートされています。 Azure AD グループでデータベース間クエリをテストするには、データベースとテーブルをもう 1 つ作成する必要があります。 データベースとテーブルが既にもう 1 つ存在する場合は、作成をスキップできます。

1. SQL Server Management Studio で、`sysadmin` アカウントを使用してマネージド インスタンスにログインします。
1. **オブジェクト エクスプローラー** で、サーバーを右クリックし、 **[新しいクエリ]** を選択します。
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

## <a name="additional-supported-scenarios"></a>サポートされているその他のシナリオ

- SQL エージェントの管理とジョブの実行が、Azure AD サーバー プリンシパル (ログイン) に対してサポートされています。
- データベースのバックアップと復元操作は、Azure AD サーバー プリンシパル (ログイン) が実行できます。
- Azure AD サーバー プリンシパル (ログイン) と認証イベントに関連するすべてのステートメントの[監査](auditing-configure.md)。
- `sysadmin` サーバー ロールのメンバーである Azure AD サーバー プリンシパル (ログイン) の専用管理者接続。
- Azure AD サーバー プリンシパル (ログイン) は、[sqlcmd ユーティリティ](/sql/tools/sqlcmd-utility)と [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ツールの使用でサポートされています。
- Azure AD サーバー プリンシパル (ログイン) によるログオン イベントでは、ログオン トリガーがサポートされています。
- Service Broker とデータベース メールは、Azure AD サーバー プリンシパル (ログイン) を使用して設定できます。

## <a name="next-steps"></a>次のステップ

### <a name="enable-security-features"></a>セキュリティ機能の有効化

データベースをセキュリティで保護する方法の包括的な一覧については、[SQL Managed Instance のセキュリティ機能](sql-managed-instance-paas-overview.md#security-features)に関する記事を参照してください。 次のセキュリティ機能について説明しています。

- [SQL Managed Instance の監査](auditing-configure.md)
- [常に暗号化](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [脅威の検出](threat-detection-configure.md)
- [動的データ マスク](/sql/relational-databases/security/dynamic-data-masking)
- [行レベルのセキュリティ](/sql/relational-databases/security/row-level-security)
- [透過的なデータ暗号化 (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>SQL Managed Instance の機能

SQL Managed Instance の機能全体の概要については、以下を参照してください。

> [!div class="nextstepaction"]
> [SQL Managed Instance の機能](sql-managed-instance-paas-overview.md)