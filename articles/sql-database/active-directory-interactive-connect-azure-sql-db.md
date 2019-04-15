---
title: ActiveDirectoryInteractive を使用した SQL への接続 | Microsoft Docs
description: SqlAuthenticationMethod.ActiveDirectoryInteractive モードを使用して Azure SQL Database に接続するための C# コード例と説明。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 03/12/2019
manager: craigg
ms.openlocfilehash: bc7274308b8a349d16866f107eac4a57e115be9e
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "58850176"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication を使用して Azure SQL Database に接続する

この記事では、Azure SQL Database に接続する C# プログラムについて説明します。 このプログラムでは、[Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) をサポートする対話モードの認証を使用します。

SQL ツールでの多要素認証のサポートの詳細については、「[SQL Server Data Tools (SSDT) での Azure Active Directory のサポート](https://docs.microsoft.com/sql/ssdt/azure-active-directory)」をご覧ください。

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Azure SQL Database の多要素認証

.NET Framework バージョン 4.7.2 以降、列挙型 [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) に新しい値 `ActiveDirectoryInteractive` が追加されました。 C# クライアント プログラムでこの列挙値を使用すると、Azure SQL Database への接続に、多要素認証をサポートする Azure Active Directory (Azure AD) 対話モードを使用するようシステムに指示します。 プログラムを実行するユーザーには、次のダイアログ ボックスが表示されます。

* Azure AD ユーザー名を表示し、そのユーザーのパスワードの入力を求めるダイアログ ボックス。

   ユーザーのドメインが Azure AD とフェデレーションされている場合、パスワードが不要なため、このダイアログ ボックスは表示されません。

   Azure AD ポリシーでユーザーに多要素認証が強制されている場合は、次の 2 つのダイアログ ボックスが表示されます。

* ユーザーが初めて多要素認証を実行するときに、テキスト メッセージの送信先の携帯電話番号を確認するダイアログ ボックスが表示されます。 各メッセージでは、ユーザーが次のダイアログ ボックスに入力する必要がある*確認コード*が提供されます。

* システムから携帯電話に送信された多要素認証確認コードの入力を求めるダイアログ ボックス。

多要素認証を要求するように Azure AD を構成する方法については、「[クラウドベースの Azure Multi-Factor Authentication をデプロイする](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)」をご覧ください。

これらのダイアログのスクリーンショットについては、「[SQL Server Management Studio と Azure AD 用に多要素認証を構成する](sql-database-ssms-mfa-authentication-configure.md)」をご覧ください。

> [!TIP]
> .NET Framework API は [.NET API ブラウザー ツール ページ](https://docs.microsoft.com/dotnet/api/)で検索できます。
>
> また、省略可能な [?term=&lt;検索値&gt;](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod) パラメーターで直接検索することもできます。

## <a name="configure-your-c-application-in-the-azure-portal"></a>Azure portal で C# アプリケーションを構成する

始める前に、[Azure SQL Database サーバー](sql-database-get-started-portal.md)を作成し、使用できる状態にしておく必要があります。

### <a name="register-your-app-and-set-permissions"></a>アプリを登録してアクセス許可を設定する

Azure AD 認証を使用するには、C# プログラムで Azure AD アプリケーションとして登録する必要があります。 アプリを登録するには、Azure AD 管理者または Azure AD *Application Developer* のロールが割り当てられたユーザーである必要があります。 ロールの割り当ての詳細については、「[Azure Active Directory を使ってユーザーに管理者と管理者以外のロールを割り当てる](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)」を参照してください。

アプリの登録を完了すると、**アプリケーション ID** が生成され、表示されます。 接続するには、プログラムにこの ID を含める必要があります。

アプリケーションに必要なアクセス許可を登録して設定するには:

1. Azure Portal で、**[Azure Active Directory]** > **[アプリの登録]** > **[新しいアプリケーションの登録]** の順に選びます。

    ![アプリの登録](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    アプリの登録が作成されると、**アプリケーション ID** 値が生成され、表示されます。

    ![表示されたアプリ ID](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. **[登録済みのアプリ]** > **[設定]** > **[必要なアクセス許可]** > **[追加]** を選択します。

    ![登録済みのアプリのアクセス許可の設定](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. **[必要なアクセス許可]** > **[追加]** > **[API を選択します]** > **[Azure SQL Database]** を選択します。

    ![Azure SQL Database の API へのアクセスの追加](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. **[API アクセス]** > **[アクセス許可の選択]** > **[委任されたアクセス許可]** を選択します。

    ![Azure SQL Database の API へのアクセス許可の委任](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>SQL Database サーバーの Azure AD 管理者を設定する

C# プログラムを実行するには、Azure SQL サーバー管理者が SQL Database サーバーの Azure AD 管理者を割り当てる必要があります。 

**[SQL Server]** ページで、**[Active Directory 管理者]** > **[管理者の設定]** を選択します。

Azure SQL Database の Azure AD 管理者とユーザーの詳細については、[SQL Database での Azure Active Directory 認証の構成と管理](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)に関する記事のスクリーンショットをご覧ください。

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>管理者以外のユーザーを特定のデータベースに追加する (省略可能)

SQL Database サーバーの Azure AD 管理者は、C# サンプル プログラムを実行できます。 Azure AD ユーザーは、データベースに登録されていればプログラムを実行できます。 データベースに既に登録され、データベースに対して `ALTER ANY USER` アクセス許可を持つ Azure AD SQL 管理者または Azure AD ユーザーは、ユーザーを追加できます。

SQL [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) コマンドを使用してデータベースにユーザーを追加できます。 例: `CREATE USER [<username>] FROM EXTERNAL PROVIDER`。

詳細については、「[Use Azure Active Directory Authentication for authentication with SQL Database, Managed Instance, or SQL Data Warehouse (SQL Database、マネージド インスタンス、または SQL Data Warehouse での認証に Azure Active Directory Authentication を使用する)](sql-database-aad-authentication.md)」をご覧ください。

## <a name="new-authentication-enum-value"></a>新しい認証列挙値

この C# の例は、[`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) 名前空間に依存しています。 多要素認証で特に重要なものは列挙型の `SqlAuthenticationMethod` であり、以下の値があります。

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   この値と Azure AD ユーザー名を使用して、多要素認証を実装します。 この記事ではこの値が重視されます。 ユーザー パスワード用のダイアログを表示し、このユーザーに多要素認証が適用される場合は多要素認証を確認するためのダイアログを表示することで、対話型エクスペリエンスを実現します。 この値は、.NET Framework バージョン 4.7.2 以降で使用できます。

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  *フェデレーション* アカウントにこの値を使用します。 フェデレーション アカウントの場合、ユーザー名は Windows ドメインに認識されています。 この認証方法では、多要素認証はサポートされません。

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Azure AD のユーザー名とパスワードが必要な認証には、この値を使用します。 Azure SQL Database によって認証が実行されます。 この方法では、多要素認証はサポートされません。

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Azure portal から C# パラメーター値を設定する

C# プログラムを正常に実行するには、静的フィールドに適切な値を割り当てる必要があります。 値の例が指定されたフィールドを次に示します。 また、必要な値を取得できる Azure portal の場所も示します。

| 静的フィールド名 | 値の例 | Azure portal 内の場所 |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **[SQL Server]** > **[名前でフィルター]** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **[Azure Active Directory]** > **[ユーザー]** > **[新しいゲスト ユーザー]** |
| Initial_DatabaseName | "myDatabase" | **[SQL Server]** > **[SQL データベース]** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **[Azure Active Directory]** > **[アプリの登録]** > **[名前で検索]** > **[アプリケーション ID]** |
| RedirectUri | 新しい URI ("https://mywebserver.com/") | **[Azure Active Directory]** > **[アプリの登録]** > **[名前で検索]** > *"アプリの登録名"* > **[設定]** > **[RedirectURI]**<br /><br />この記事では使用されていないため、RedirectUri には任意の有効な値を指定できます。 |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>SQL Server Management Studio で確認する

C# プログラムを実行する前に、SQL Server Management Studio (SSMS) で設定と構成が正しいことを確認することをお勧めします。 こうすることで、C# プログラムのエラーをソース コードに絞り込むことができます。

### <a name="verify-sql-database-firewall-ip-addresses"></a>SQL Database のファイアウォールの IP アドレスを確認する

C# プログラムを実行する予定の場合、同じ構成で、同じコンピューターから SSMS を実行します。 このテストの場合は、任意の**認証**モードを使用できます。 データベース サーバーのファイアウォールが、指定した IP アドレスを受け入れていないことを示している場合は、[Azure SQL Database のサーバーレベルとデータベースレベルのファイアウォール規則](sql-database-firewall-configure.md)に関するページを参照してください。

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Azure Active Directory の多要素認証を確認する

**[認証]** を **[Active Directory - MFA サポートで汎用]** に設定して、SSMS をもう一度実行します。 このオプションを使用するには、SSMS バージョン 17.5 以降が必要です。

詳細については、「[SQL Server Management Studio と Azure AD 用に多要素認証を構成する](sql-database-ssms-mfa-authentication-configure.md)」をご覧ください。

> [!NOTE]
> データベースのゲスト ユーザーの場合は、データベースの Azure AD ドメイン名も指定する必要があります。**[オプション]** > **[AD ドメイン名またはテナント ID]** を選択します。 Azure portal でドメイン名を見つけるには、**[Azure Active Directory]** > **[カスタム ドメイン名]** の順に選択します。 この C# サンプル プログラムでは、ドメイン名を指定する必要はありません。

## <a name="c-code-example"></a>C# コード例

この C# サンプル プログラムでは、[*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL アセンブリを利用します。

このパッケージをインストールするには、Visual Studio で **[プロジェクト]** > **[NuGet パッケージの管理]** の順に選択します。 「**Microsoft.IdentityModel.Clients.ActiveDirectory**」を検索してインストールします。

これは C# のソース コードの例です。

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

これは C# のテスト出力の例です。

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>次の手順

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
