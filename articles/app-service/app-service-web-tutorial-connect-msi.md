---
title: マネージド ID を使用した SQL Database 接続のセキュリティ保護 - Azure App Service | Microsoft Docs
description: マネージド ID を使用してデータベース接続をより安全にする方法と、これを他の Azure サービスに適用する方法について説明します。
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
ms.date: 11/30/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: dd84f9b3b68d7a34903241caed7f1f93e685fb57
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482525"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>チュートリアル:マネージド ID を使用した App Service からの Azure SQL Database 接続のセキュリティ保護

[App Service](overview.md) では、Azure の高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 さらに、[Azure SQL Database](/azure/sql-database/) やその他の Azure サービスへのアクセスをセキュリティ保護するためのターンキー ソリューションである[マネージド ID](overview-managed-identity.md) もアプリ向けに提供しています。 App Service のマネージド ID を使用すると、接続文字列内の認証情報などのシークレットをアプリから排除することで、アプリのセキュリティを強化できます。 このチュートリアルでは、「[チュートリアル: SQL Database を使用して Azure に ASP.NET アプリを作成する](app-service-web-tutorial-dotnet-sqldatabase.md)」で構築したサンプル ASP.NET Web アプリにマネージド ID を追加します。 作業が完了すると、サンプル アプリは、ユーザー名とパスワードを必要とせずに SQL Database に安全に接続するようになります。

> [!NOTE]
> このシナリオは、現在、.NET Framework 4.6 以降でサポートされています。[.NET Core 2.1](https://www.microsoft.com/net/learn/get-started/windows) ではサポートされていません。 [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) はこのシナリオをサポートしていますが、まだ App Service の既定のイメージには含まれていません。 
>

学習内容は次のとおりです。

> [!div class="checklist"]
> * マネージド ID を有効にする
> * SQL Database へのアクセスをマネージド ID に付与する
> * Azure Active Directory 認証を使用して SQL Database で認証するようにアプリケーション コードを構成する
> * SQL Database 内でマネージド ID に最小限の特権を付与する

> [!NOTE]
>Azure Active Directory 認証は、オンプレミスの Active Directory (AD DS) の[統合 Windows 認証](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10))とは_異なります_。 AD DS と Azure Active Directory はまったく異なる認証プロトコルを使用しています。 詳細については、「[Azure AD Domain Services のドキュメント](https://docs.microsoft.com/azure/active-directory-domain-services/)」を参照してください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

この記事は、「[チュートリアル:SQL Database を使用して Azure に ASP.NET アプリを作成する](app-service-web-tutorial-dotnet-sqldatabase.md)」の続きです。 このチュートリアルを完了していない場合は、最初にこのチュートリアルに従って作業してください。 または、SQL Database を使用して独自の ASP.NET アプリに合わせた手順を実行することもできます。

<!-- ![app running in App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png) -->

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-managed-identities"></a>マネージド ID を有効にする

Azure アプリのマネージド ID を有効にするには、Cloud Shell で [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) コマンドを使用します。 次のコマンドで、*\<app name>* を置き換えます。

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app name>
```

Azure Active Directory に ID が作成された後の出力の例を次に示します。

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

次の手順では、`principalId` の値を使用します。 Azure Active Directory 内の新しい ID の詳細を表示するには、`principalId` の値を指定して次のオプション コマンドを実行します。

```azurecli-interactive
az ad sp show --id <principalid>
```

## <a name="grant-database-access-to-identity"></a>データベースへのアクセスを ID に許可する

次に、Cloud Shell 内で [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest) コマンドを使用して、データベースへのアクセスをアプリのマネージド ID に許可します。 次のコマンドで、*\<server_name>* と <principalid_from_last_step> を置き換えます。 *\<admin_user>* には管理者名を入力します。

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server_name> --display-name <admin_user> --object-id <principalid_from_last_step>
```

これで、マネージド ID を使用して Azure SQL Database サーバーにアクセスできるようになりました。

## <a name="modify-connection-string"></a>接続文字列を変更する

Cloud Shell 内で [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用して、以前にアプリに設定した接続を変更します。 次のコマンドで、*\<app name>* をアプリ名に置き換え、*\<server_name>* と *\<db_name>* を SQL Database の対応する情報に置き換えます。

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='Server=tcp:<server_name>.database.windows.net,1433;Database=<db_name>;' --connection-string-type SQLAzure
```

## <a name="modify-aspnet-code"></a>ASP.NET コードを変更する

Visual Studio で、パッケージ マネージャー コンソールを開き、NuGet パッケージ [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) を追加します。

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.1.0-preview
```

_Models\MyDatabaseContext.cs_ を開き、ファイルの先頭に次の `using` ステートメントを追加します。

```csharp
using System.Data.SqlClient;
using Microsoft.Azure.Services.AppAuthentication;
using System.Web.Configuration;
```

`MyDatabaseContext` クラスに、次のコンストラクターを追加します。

```csharp
public MyDatabaseContext(SqlConnection conn) : base(conn, true)
{
    conn.ConnectionString = WebConfigurationManager.ConnectionStrings["MyDbConnection"].ConnectionString;
    // DataSource != LocalDB means app is running in Azure with the SQLDB connection string you configured
    if(conn.DataSource != "(localdb)\\MSSQLLocalDB")
        conn.AccessToken = (new AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;

    Database.SetInitializer<MyDatabaseContext>(null);
}
```

このコンストラクターは、App Service からの Azure SQL Database のアクセス トークンを使用するためのカスタム SqlConnection オブジェクトを構成します。 このアクセス トークンにより、App Service アプリは、マネージド ID を使用して Azure SQL Database で認証されます。 詳細については、「[Azure リソースのトークンの取得](overview-managed-identity.md#obtaining-tokens-for-azure-resources)」を参照してください。 `if` ステートメントにより、続いて LocalDB を使用してアプリをローカルでテストできます。

> [!NOTE]
> `SqlConnection.AccessToken` は、現在、.NET Framework 4.6 以降と [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) だけでサポートされており、[.NET Core 2.1](https://www.microsoft.com/net/learn/get-started/windows) ではサポートされていません。
>

この新しいコンストラクターを使用するには、`Controllers\TodosController.cs` を開き、`private MyDatabaseContext db = new MyDatabaseContext();` 行を見つけます。 既存のコードでは、既定の `MyDatabaseContext` コントローラーを使用して、標準接続文字列を使用してデータベースを作成します。この接続文字列には、[変更](#modify-connection-string)前はユーザー名とパスワードがクリア テキストで含まれていました。

この行全体を次のコードに置き換えます。

```csharp
private MyDatabaseContext db = new MyDatabaseContext(new System.Data.SqlClient.SqlConnection());
```

### <a name="publish-your-changes"></a>変更を発行する

あとは、Azure に変更を発行するだけです。

**ソリューション エクスプローラー**で **DotNetAppSqlDb** プロジェクトを右クリックし、**[発行]** を選択します。

![ソリューション エクスプローラーから発行する](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

発行ページで **[発行]** をクリックします。 新しい Web ページに To-Do リストを表示するとき、アプリはマネージド ID を使用してデータベースに接続しています。

![Code First Migration の手順後の Azure アプリ](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

以前と同様に To-Do リストを編集できるようになりました。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="grant-minimal-privileges-to-identity"></a>ID に最小限の特権を付与する

これまでの手順で、マネージド ID が Azure AD 管理者として SQL Server に接続されていることに気付いたことでしょう。 マネージド ID に最小限の特権を付与するには、Azure AD 管理者として Azure SQL Database サーバーにサインインし、対象のマネージド ID を含む Azure Active Directory グループを追加する必要があります。 

### <a name="add-managed-identity-to-an-azure-active-directory-group"></a>Azure Active Directory グループにマネージド ID を追加する

Cloud Shell 内で、次のスクリプトに示すように、アプリのマネージド ID を _myAzureSQLDBAccessGroup_ という新しい Azure Active Directory グループに追加します。

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group <group_name> --name <app_name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

各コマンドの完全な JSON 出力を表示するには、パラメーターの `--query objectId --output tsv` を削除します。

### <a name="reconfigure-azure-ad-administrator"></a>Azure AD 管理者を再構成する

以前の手順では、マネージド ID を SQL Database の Azure AD 管理者として割り当てました。 この ID は (データベース ユーザーを追加するための) 対話型サインインに使用できないため、実際の Azure AD ユーザーを使用する必要があります。 Azure AD ユーザーを追加するには、「[Azure SQL Database サーバーの Azure Active Directory 管理者をプロビジョニングする](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)」の手順を実行します。 

> [!IMPORTANT]
> 追加した後は、すべての Azure AD アカウントから SQL Database への Azure AD アクセスを完全に無効にしたい場合を除き、SQL Database に対するこの Azure AD 管理者を削除しないでください。
> 

### <a name="grant-permissions-to-azure-active-directory-group"></a>Azure Active Directory グループにアクセス許可を付与する

Cloud Shell 内で、SQLCMD コマンドを使用して SQL Database にサインインします。 _\<server\_name>_ を実際の SQL Database サーバー名に置き換え、_\<db\_name>_ をアプリが使用するデータベースの名前に置き換え、_\<AADuser\_name>_ と _\<AADpassword>_ を Azure AD ユーザーの資格情報に置き換えます。

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -d <db_name> -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

対象のデータベースの SQL プロンプトで次のコマンドを実行して、前に作成した Azure Active Directory グループを追加し、アプリに必要なアクセス許可を付与します。 たとえば、次のように入力します。 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

「`EXIT`」と入力して Cloud Shell プロンプトに戻ります。 

## <a name="next-steps"></a>次の手順

ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * マネージド ID を有効にする
> * SQL Database へのアクセスをマネージド ID に付与する
> * Azure Active Directory 認証を使用して SQL Database で認証するようにアプリケーション コードを構成する
> * SQL Database 内でマネージド ID に最小限の特権を付与する

次のチュートリアルに進み、カスタム DNS 名を Web アプリにマップする方法を学習してください。

> [!div class="nextstepaction"]
> [既存のカスタム DNS 名を Azure App Service にマップする](app-service-web-tutorial-custom-domain.md)
