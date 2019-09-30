---
title: マネージド ID を使用した SQL Database 接続のセキュリティ保護 - Azure App Service | Microsoft Docs
description: マネージド ID を使用してデータベース接続をより安全にする方法と、それを他の Azure サービスに適用する方法について説明します。
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/16/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: d4e0d632fe476df159710f800eca3a2a283f7908
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018281"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>チュートリアル:マネージド ID を使用した App Service からの Azure SQL Database 接続のセキュリティ保護

[App Service](overview.md) では、Azure の高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 さらに、[Azure SQL Database](/azure/sql-database/) やその他の Azure サービスへのアクセスをセキュリティ保護するためのターンキー ソリューションである[マネージド ID](overview-managed-identity.md) もアプリ向けに提供しています。 App Service のマネージド ID を使用すると、接続文字列内の認証情報などのシークレットをアプリから排除することで、アプリのセキュリティを強化できます。 このチュートリアルでは、次のいずれかのチュートリアルで作成したサンプル Web アプリにマネージド ID を追加します。 

- [チュートリアル:SQL Database を使用して Azure に ASP.NET アプリを作成する](app-service-web-tutorial-dotnet-sqldatabase.md)
- [チュートリアル:Azure App Service での ASP.NET Core および SQL Database アプリの作成](app-service-web-tutorial-dotnetcore-sqldb.md)

作業が完了すると、サンプル アプリは、ユーザー名とパスワードを必要とせずに SQL Database に安全に接続するようになります。

> [!NOTE]
> このチュートリアルで説明する手順は、以下のバージョンをサポートしています。
> 
> - .NET Framework 4.7.2 以降。
> - .NET Core 2.2 以降。
>

学習内容

> [!div class="checklist"]
> * マネージド ID を有効にする
> * SQL Database へのアクセスをマネージド ID に付与する
> * SQL Database で Azure AD 認証を使用するように Entity Framework を構成する
> * Azure AD 認証を使用して Visual Studio から SQL Database に接続する

> [!NOTE]
>Azure AD 認証は、オンプレミスの Active Directory (AD DS) の[統合 Windows 認証](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10))とは_異なります_。 AD DS と Azure AD はまったく異なる認証プロトコルを使用しています。 詳細については、「[Azure AD Domain Services のドキュメント](https://docs.microsoft.com/azure/active-directory-domain-services/)」を参照してください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

この記事は、「[チュートリアル:SQL Database を使用して Azure に ASP.NET アプリを作成する](app-service-web-tutorial-dotnet-sqldatabase.md)」または「[チュートリアル: Azure App Service での ASP.NET Core および SQL Database アプリの作成](app-service-web-tutorial-dotnetcore-sqldb.md)」の続きです。 まだどちらも完了していない場合は、先に 2 つのチュートリアルのうちのいずれかに従って作業してください。 または、SQL Database を使用して独自の .NET アプリに合わせた手順を実行することもできます。

SQL Database をバックエンドとして使用してご自分のアプリをデバッグするには、ご使用のコンピューターからのクライアント接続を許可していることを確認してください。 そうなっていない場合は、「[Azure portal を使用してサーバーレベルの IP ファイアウォール規則を管理する](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)」の手順に従ってください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>データベースへのアクセスを Azure AD ユーザーに許可する

まず、Azure AD ユーザーを SQL Database サーバーの Active Directory 管理者として割り当てることで SQL Database への Azure AD 認証を有効にします。 このユーザーは、ご使用の Azure サブスクリプションのサインアップに使用した Microsoft アカウントと異なります。 Azure AD に作成、インポート、同期、または招待したユーザーである必要があります。 許可されている Azure AD ユーザーの詳細については、[Azure AD の機能と SQL Database の制限事項](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)に関するセクションを参照してください。

Azure AD テナントにまだユーザーが作成されていない場合は、「[Azure Active Directory を使用してユーザーを追加または削除する](../active-directory/fundamentals/add-users-azure-active-directory.md)」の手順に従ってユーザーを作成します。

[`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) を使用して Azure AD ユーザーのオブジェクト ID を見つけます。 *\<user-principal-name>* は置き換えてください。 結果は変数に保存されます。

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Azure AD 内のすべてのユーザー プリンシパル名の一覧を表示するには、`az ad user list --query [].userPrincipalName` を実行します。
>

Cloud Shell で [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) コマンドを使用して、この Azure AD ユーザーを Active Directory 管理者として追加します。 次のコマンドで、 *\<server-name>* を、SQL Database サーバー名 (`.database.windows.net` サフィックスなし) に置き換えます。

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Active Directory 管理者の追加の詳細については、「[Azure SQL Database サーバーの Azure Active Directory 管理者をプロビジョニングする](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)」を参照してください

## <a name="set-up-visual-studio"></a>Visual Studio を設定する

### <a name="windows"></a>Windows
Visual Studio for Windows は Azure AD 認証と統合されています。 Visual Studio で開発とデバッグを有効にするには、Visual Studio のメニューから **[ファイル]**  >  **[アカウント設定]** の順に選択し、 **[アカウントを追加します]** をクリックして、実際の Azure AD ユーザーを追加します。

Azure サービス認証用に Azure AD ユーザーを設定するには、メニューから **[ツール]**  >  **[オプション]** の順に選択した後、 **[Azure Service Authentication]\(Azure サービス認証\)**  >  **[アカウントの選択]** の順に選択します。 追加した Azure AD ユーザーを選択し、 **[OK]** をクリックします。

これで、Azure AD 認証を使用し、SQL Database をバックエンドとして利用して、ご自分のアプリを開発およびデバッグする準備ができました。

### <a name="macos"></a>MacOS

Visual Studio for Mac は Azure AD 認証と統合されていません。 ただし、後で使用する [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) ライブラリでは、Azure CLI からのトークンを使用することができます。 Visual Studio での開発とデバッグを可能にするには、まず、ご利用のローカル コンピューター上に [Azure CLI をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)する必要があります。

ローカル コンピューターに Azure CLI がインストールされたら、Azure AD ユーザーを使用して、次のコマンドで Azure CLI にサインインします。

```bash
az login --allow-no-subscriptions
```
これで、Azure AD 認証を使用し、SQL Database をバックエンドとして利用して、ご自分のアプリを開発およびデバッグする準備ができました。

## <a name="modify-your-project"></a>プロジェクトを変更する

プロジェクトに対して実行する手順は、ASP.NET プロジェクトであるか ASP.NET Core プロジェクトであるかによって異なります。

- [ASP.NET を変更する](#modify-aspnet)
- [ASP.NET Core を変更する](#modify-aspnet-core)

### <a name="modify-aspnet"></a>ASP.NET を変更する

Visual Studio で、パッケージ マネージャー コンソールを開き、NuGet パッケージ [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) を追加します。

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

*Web.config* で、ファイルの先頭から作業を行い、次の変更を加えます。

- `<configSections>` に、次の section 宣言を追加します。

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- 閉じる `</configSections>` タグの下に、`<SqlAuthenticationProviders>` の次の XML コードを追加します。

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- `MyDbConnection` という接続文字列を見つけ、その `connectionString` 値を `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"` に置き換えます。 _\<server-name>_ と _\<db-name>_ を実際のサーバー名とデータベース名に置き換えます。

これが、SQL Database に接続するために必要な作業のすべてです。 Visual Studio でデバッグする場合は、「[Visual Studio を設定する](#set-up-visual-studio)」で構成した Azure AD ユーザーが、コードによって使用されます。 後で、App Service アプリのマネージド ID からの接続を許可するように SQL Database サーバーを設定します。

`Ctrl+F5` キーを押してアプリをもう一度実行します。 これで、お使いのブラウザー内で同じ CRUD アプリが Azure AD 認証を使用して Azure SQL Database に直接接続します。 この設定により、Visual Studio からのデータベースの移行を実行できます。

### <a name="modify-aspnet-core"></a>ASP.NET Core を変更する

Visual Studio で、パッケージ マネージャー コンソールを開き、NuGet パッケージ [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) を追加します。

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

[ASP.NET Core および SQL Database のチュートリアル](app-service-web-tutorial-dotnetcore-sqldb.md)の場合、ローカル開発環境では Sqlite データベース ファイルが使用され、Azure 運用環境では App Service の接続文字列が使用されるため、`MyDbConnection` 接続文字列はまったく使用されません。 Active Directory 認証では、両方の環境で同じ接続文字列を使用することが望まれます。 *appsettings.json* で、`MyDbConnection` 接続文字列の値を次のように置き換えます。

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

*Startup.cs* で、前に追加した以下のコード セクションを削除します。

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

そして、以下のコードに置き換えます。

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

次に、SQL Database のアクセス トークンを使用して、Entity Framework データベース コンテキストを指定します。 *Data\MyDatabaseContext.cs* で、以下のコードを空の `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` コンストラクターの中かっこ内に追加します。

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!TIP]
> このデモ コードは、わかりやすくするために同期型になっています。 詳細については、[コンストラクターの非同期ガイド](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#constructors)のページを参照してください。

これが、SQL Database に接続するために必要な作業のすべてです。 Visual Studio でデバッグする場合は、「[Visual Studio を設定する](#set-up-visual-studio)」で構成した Azure AD ユーザーが、コードによって使用されます。 後で、App Service アプリのマネージド ID からの接続を許可するように SQL Database サーバーを設定します。 `AzureServiceTokenProvider` クラスは、トークンをメモリ内にキャッシュし、有効期限の直前に Azure AD から取得します。 トークンを更新するためのカスタム コードは必要ありません。

`Ctrl+F5` キーを押してアプリをもう一度実行します。 これで、お使いのブラウザー内で同じ CRUD アプリが Azure AD 認証を使用して Azure SQL Database に直接接続します。 この設定により、Visual Studio からのデータベースの移行を実行できます。

## <a name="use-managed-identity-connectivity"></a>マネージド ID の接続の使用

次に、システムによって割り当てられたマネージド ID を使用して SQL Database に接続するよう、ご自分の App Service アプリを構成します。

### <a name="enable-managed-identity-on-app"></a>アプリのマネージド ID を有効にする

Azure アプリのマネージド ID を有効にするには、Cloud Shell で [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) コマンドを使用します。 次のコマンドの *\<app name>* を置き換えます。

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

出力の例を次に示します。

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Azure AD グループにマネージド ID を追加する

ご使用の SQL Database へのアクセスをこの ID に許可するには、それを [Azure AD グループ](../active-directory/fundamentals/active-directory-manage-groups.md)に追加する必要があります。 Cloud Shell 内で、次のスクリプトに示すように、それを _myAzureSQLDBAccessGroup_ という新しいグループに追加します。

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

各コマンドの完全な JSON 出力を表示するには、パラメーターの `--query objectId --output tsv` を削除します。

### <a name="grant-permissions-to-azure-ad-group"></a>Azure AD グループにアクセス許可を付与する

Cloud Shell 内で、SQLCMD コマンドを使用して SQL Database にサインインします。 _\<server-name>_ を実際の SQL Database サーバー名に置き換え、 _\<db-name>_ をご自分のアプリが使用するデータベースの名前に置き換え、 _\<aad-user-name>_ と _\<aad-password>_ を実際の Azure AD ユーザーの資格情報に置き換えます。

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

対象のデータベースの SQL プロンプトで次のコマンドを実行して、Azure AD グループを追加し、ご自分のアプリに必要なアクセス許可を付与します。 たとえば、次のように入力します。 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

「`EXIT`」と入力して Cloud Shell プロンプトに戻ります。

### <a name="modify-connection-string"></a>接続文字列を変更する

*Web.config* または *appsettings.json* 内で行ったのと同じ変更をマネージド ID に対して使用できるため、必要な作業は App Service 内の既存の接続文字列 (ご自分のアプリを初めてデプロイすると Visual Studio によって作成されます) を削除することだけである点に注意してください。 次のコマンドを使用しますが、 *\<app-name>* をご自分のアプリの名前に置き換えます。

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>変更を発行する

あとは、Azure に変更を発行するだけです。

**「[チュートリアル: SQL Database を使用して Azure に ASP.NET アプリを作成する](app-service-web-tutorial-dotnet-sqldatabase.md)」からこのチュートリアルに進んできた場合は**、Visual Studio での変更を発行します。 **ソリューション エクスプローラー**で **DotNetAppSqlDb** プロジェクトを右クリックし、 **[発行]** を選択します。

![ソリューション エクスプローラーから発行する](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

発行ページで **[発行]** をクリックします。 

**「[チュートリアル: Azure App Service での ASP.NET Core および SQL Database アプリの作成](app-service-web-tutorial-dotnetcore-sqldb.md)」からこのチュートリアルに進んできた場合は**、以下のコマンドで、Git を使用して変更を発行します。

```bash
git commit -am "configure managed identity"
git push azure master
```

新しい Web ページに To-Do リストを表示するとき、アプリはマネージド ID を使用してデータベースに接続しています。

![Code First Migration の手順後の Azure アプリ](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

以前と同様に To-Do リストを編集できるようになりました。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次の手順

ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * マネージド ID を有効にする
> * SQL Database へのアクセスをマネージド ID に付与する
> * SQL Database で Azure AD 認証を使用するように Entity Framework を構成する
> * Azure AD 認証を使用して Visual Studio から SQL Database に接続する

次のチュートリアルに進み、カスタム DNS 名を Web アプリにマップする方法を学習してください。

> [!div class="nextstepaction"]
> [既存のカスタム DNS 名を Azure App Service にマップする](app-service-web-tutorial-custom-domain.md)
