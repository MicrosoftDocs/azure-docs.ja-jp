---
title: サービス プリンシパルを使用して Azure AD ユーザーを作成する
description: このチュートリアルでは、Azure AD アプリケーション (サービス プリンシパル) を使用して、Azure SQL Database と Azure Synapse Analytics に Azure AD ユーザーを作成する手順について説明します。
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 13e049d3e7e0c87bd0a214a92491e10d652a3619
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380612"
---
# <a name="tutorial-create-azure-ad-users-using-azure-ad-applications"></a>チュートリアル:Azure AD アプリケーションを使用して Azure AD ユーザーを作成する

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

> [!NOTE]
> この記事は **パブリック プレビュー** 段階です。 詳細については、「[Azure SQL での Azure Active Directory のサービス プリンシパル](authentication-aad-service-principal.md)」を参照してください。 この記事は、Azure SQL Database を使用して、必要なチュートリアルの手順を紹介していますが、[Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) にも同じように適用することができます。

この記事では、Azure のサービス プリンシパル (Azure AD アプリケーション) を使用して、Azure SQL Database に Azure AD ユーザーを作成する手順を説明します。 Azure SQL Managed Instance にはこの機能が既に存在しますが、今後は Azure SQL Database と Azure Synapse Analytics にもこの機能が導入されることとなります。 このシナリオをサポートするためには、Azure AD Identity を生成して Azure SQL 論理サーバーに割り当てる必要があります。

Azure SQL に対する Azure AD 認証の詳細については、記事「[Azure Active Directory 認証を使用する](authentication-aad-overview.md)」を参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> - Azure SQL 論理サーバーに ID を割り当てる
> - SQL 論理サーバーの ID にディレクトリ閲覧者のアクセス許可を割り当てる
> - Azure AD にサービス プリンシパル (Azure AD アプリケーション) を作成する
> - Azure SQL Database にサービス プリンシパル ユーザーを作成する
> - Azure AD サービス プリンシパル ユーザーを使用して別の Azure AD ユーザーを SQL Database に作成する

## <a name="prerequisites"></a>前提条件

- 既にデプロイされている [Azure SQL Database](single-database-create-quickstart.md) または [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)。 このチュートリアルでは、SQL Database が既に稼動していることを前提としています。
- 既にある Azure Active Directory へのアクセス。
- PowerShell を使用して個々の Azure AD アプリケーションを Azure SQL の Azure AD 管理者として設定する場合、[Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 以降のモジュールが必要です。 最新のモジュールにアップグレードしてください。

## <a name="assign-an-identity-to-the-azure-sql-logical-server"></a>Azure SQL 論理サーバーに ID を割り当てる

1. Azure Active Directory に接続します。 テナント ID を見つける必要があります。 これを見つけるには、[Azure portal](https://portal.azure.com) から **Azure Active Directory** リソースにアクセスします。 **[概要]** ペインに **[テナント ID]** が表示されます。 次の PowerShell コマンドを実行します。

    - `<TenantId>` は、実際の **テナント ID** に置き換えます。

    ```powershell
    Connect-AzAccount -Tenant <TenantId>
    ```

    `TenantId` は記録しておいてください。後でこのチュートリアルの中で使用します。

1. Azure AD ID を生成して Azure SQL 論理サーバーに割り当てます。 次の PowerShell コマンドを実行します。

    - `<resource group>` と `<server name>` は、実際の値に置き換えます。 実際のサーバー名が `myserver.database.windows.net` の場合、`<server name>` を `myserver` に置き換えます。

    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <server name> -AssignIdentity
    ```

    詳細については、[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) コマンドを参照してください。

    > [!IMPORTANT]
    > Azure SQL 論理サーバーに対して Azure AD ID を設定した場合は、その ID に [**ディレクトリ閲覧者**](../../active-directory/roles/permissions-reference.md#directory-readers)のアクセス許可を付与する必要があります。 その手順については、次のセクションで取り上げます。 この手順をスキップ **しない** でください。Azure AD 認証が機能しなくなります。

    - 過去に、[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) コマンドに `AssignIdentity` パラメーターを使用して新しい SQL Server を作成してある場合は、Azure ファブリックでこのプロパティを有効にするために、後から別途 [Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) コマンドを実行する必要があります。

1. サーバー ID が正常に割り当てられたことを確認します。 次の PowerShell コマンドを実行します。

    - `<resource group>` と `<server name>` は、実際の値に置き換えます。 実際のサーバー名が `myserver.database.windows.net` の場合、`<server name>` を `myserver` に置き換えます。
    
    ```powershell
    $xyz = Get-AzSqlServer  -ResourceGroupName <resource group> -ServerName <server name>
    $xyz.identity
    ```

    出力結果に `PrincipalId`、`Type`、`TenantId` が表示されます。 `PrincipalId` が、割り当てられた ID です。

1. [Azure portal](https://portal.azure.com) にアクセスして ID を確認することもできます。

    - **Azure Active Directory** リソースの **[エンタープライズ アプリケーション]** に移動します。 SQL 論理サーバーの名前を入力してください。 リソースに **オブジェクト ID** が関連付けられていることがわかります。
    
    :::image type="content" source="media/authentication-aad-service-principals-tutorial/enterprise-applications-object-id.png" alt-text="オブジェクト ID":::

## <a name="assign-directory-readers-permission-to-the-sql-logical-server-identity"></a>SQL 論理サーバーの ID にディレクトリ閲覧者のアクセス許可を割り当てる

Azure AD によって割り当てられた ID が Azure SQL で適切に機能するためには、そのサーバー ID に Azure AD の `Directory Readers` アクセス許可が付与されている必要があります。

この必須のアクセス許可を付与するためには、以下のスクリプトを実行します。

> [!NOTE] 
> このスクリプトは、Azure AD の `Global Administrator` または `Privileged Roles Administrator` が実行する必要があります。
>
> **パブリック プレビュー** では、Azure AD 内のグループに `Directory Readers` ロールを割り当てることができます。 グループの所有者は、このグループのメンバーとしてマネージド ID を追加できます。これにより、`Global Administrator` または `Privileged Roles Administrator` が `Directory Readers` ロールを付与する必要がなくなります。 この機能の詳細については、「[Azure SQL の Azure Active Directory のディレクトリ閲覧者ロール](authentication-aad-directory-readers-role.md)」を参照してください。

- `<TenantId>` は、先ほど確認した `TenantId` に置き換えます。
- `<server name>` は、実際の SQL 論理サーバーの名前に置き換えます。 実際のサーバー名が `myserver.database.windows.net` の場合、`<server name>` を `myserver` に置き換えます。

```powershell
# This script grants Azure "Directory Readers" permission to a Service Principal representing the Azure SQL logical server
# It can be executed only by a "Global Administrator" or "Privileged Roles Administrator" type of user.
# To check if the "Directory Readers" permission was granted, execute this script again

Import-Module AzureAD
Connect-AzureAD -TenantId "<TenantId>"    #Enter your actual TenantId
$AssignIdentityName = "<server name>"     #Enter Azure SQL logical server name
 
# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}
 
# Get service principal for managed instance
$roleMember = Get-AzureADServicePrincipal -SearchString $AssignIdentityName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$($AssignIdentityName)', make sure that AssignIdentityName parameter was entered correctly."
    exit
}

if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$($AssignIdentityName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}
 
# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
 
if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($AssignIdentityName)' to 'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
    Write-Output "'$($AssignIdentityName)' service principal added to 'Directory Readers' role'..."
 
    #Write-Output "Dumping service principal '$($AssignIdentityName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
} else {
    Write-Output "Service principal '$($AssignIdentityName)' is already member of 'Directory Readers' role'."
}
```

> [!NOTE]
> 目的の ID に対してディレクトリ閲覧者のアクセス許可が付与されたかどうかは、このスクリプトの出力を見るとわかります。 アクセス許可が付与されたかどうかが定かでない場合は、スクリプトを再実行してください。

SQL Managed Instance に **ディレクトリ閲覧者** のアクセス許可を設定する方法に関する同様のアプローチについては、「[Azure AD 管理者 (SQL Managed Instance) をプロビジョニングする](authentication-aad-configure.md#powershell)」を参照してください。

## <a name="create-a-service-principal-an-azure-ad-application-in-azure-ad"></a>Azure AD にサービス プリンシパル (Azure AD アプリケーション) を作成する

1. こちらのガイドに従って[アプリを登録し、アクセス許可を設定](active-directory-interactive-connect-azure-sql-db.md#register-your-app-and-set-permissions)します。

    必ず、**アプリケーションのアクセス許可** と **委任されたアクセス許可** を追加してください。

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-apps.png" alt-text="Azure Active Directory の [アプリの登録] ページを示すスクリーンショット。表示名が AppSP のアプリが強調表示されています。":::

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-app-registration-api-permissions.png" alt-text="API のアクセス許可":::

2. サインインに使用するクライアント シークレットも作成する必要があります。 こちらのガイドに従って、[証明書をアップロードするか、サインイン用のシークレットを作成](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)してください。

3. アプリケーションの登録から次のものを記録します。 これは **[概要]** ペインで確認できます。
    - **アプリケーション ID**
    - **テナント ID** - 先ほどと同じ値を使用します

このチュートリアルでは、メイン サービス プリンシパルとして *AppSP* を、2 つ目のサービス プリンシパル ユーザーとして *myapp* を使用します。myapp は、*AppSP* によって Azure SQL に作成されます。 *AppSP* と *myapp* の 2 つのアプリケーションを作成する必要があります。

Azure AD アプリケーションの作成方法について詳しくは、「[方法:リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../../active-directory/develop/howto-create-service-principal-portal.md)」の記事を参照してください。

### <a name="permissions-required-to-set-or-unset-the-azure-ad-admin"></a>Azure AD 管理者を設定または設定解除するために必要なアクセス許可

サービス プリンシパルが Azure SQL の Azure AD 管理者を設定または解除するには、追加の API のアクセス許可が必要です。 [Directory.Read.All](/graph/permissions-reference#application-permissions-18) アプリケーション API アクセス許可を、Azure AD 内で対象のアプリケーションに追加する必要があります。

:::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-directory-reader-all-permissions.png" alt-text="Azure AD 内の Directory.Reader.All アクセス許可":::

サービス プリンシパルには、[**SQL Server 共同作成者**](../../role-based-access-control/built-in-roles.md#sql-server-contributor)ロール (SQL Database の場合) または [**SQL Managed Instance の共同作成者**](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)ロール (SQL Managed Instance の場合) も必要になります。

> [!NOTE]
> Azure AD Graph API は非推奨とされていますが、このチュートリアルには引き続き **Directory.Reader.All** アクセス許可が適用されます。 Microsoft Graph API は、このチュートリアルには適用されません。

## <a name="create-the-service-principal-user-in-azure-sql-database"></a>Azure SQL Database にサービス プリンシパル ユーザーを作成する

Azure AD にサービス プリンシパルを作成したら、SQL Database にユーザーを作成します。 データベースにユーザーを作成するアクセス許可がある有効なログインで SQL Database に接続する必要があります。

1. 次の T-SQL コマンドを使用して、SQL Database にユーザー *AppSP* を作成します。

    ```sql
    CREATE USER [AppSP] FROM EXTERNAL PROVIDER
    GO
    ```

2. *AppSP* に `db_owner` アクセス許可を付与します。これにより、このユーザーは他の Azure AD ユーザーをデータベースに作成できるようになります。

    ```sql
    EXEC sp_addrolemember 'db_owner', [AppSP]
    GO
    ```

    詳細については、「[sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)」を参照してください。

    `db_owner` ロールを割り当てる代わりに、`ALTER ANY USER` アクセス許可を付与してもかまいません。 その場合、サービス プリンシパルは、他の Azure AD ユーザーを追加できるようになります。

    ```sql
    GRANT ALTER ANY USER TO [AppSp]
    GO
    ```

    > [!NOTE]
    > *AppSP* がサーバーの Azure AD 管理者として設定されている場合、上記の設定は必要ありません。 サービス プリンシパルを SQL 論理サーバーの AD 管理者として設定するには、Azure portal、PowerShell、Azure CLI コマンドのいずれかを使用します。 詳細については、「[Azure AD 管理者をプロビジョニングする (SQL Database)](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)」を参照してください。

## <a name="create-an-azure-ad-user-in-sql-database-using-an-azure-ad-service-principal"></a>Azure AD サービス プリンシパルを使用して SQL Database に Azure AD ユーザーを作成する

> [!IMPORTANT]
> SQL Database へのログインに使用するサービス プリンシパルには、クライアント シークレットが必要です。 それがない場合は、「[Azure AD にサービス プリンシパル (Azure AD アプリケーション) を作成する](#create-a-service-principal-an-azure-ad-application-in-azure-ad)」の手順 2. に従ってください。 そのクライアント シークレットを以下のスクリプトの入力パラメーターとして追加する必要があります。

1. 以下のスクリプトとサービス プリンシパル *AppSP* を使用して、Azure AD サービス プリンシパル ユーザー *myapp* を作成します。

    - `<TenantId>` は、先ほど確認した `TenantId` に置き換えます。
    - `<ClientId>` は、先ほど確認した `ClientId` に置き換えます。
    - `<ClientSecret>` は、先ほど作成したクライアント シークレットに置き換えます。
    - `<server name>` は、実際の SQL 論理サーバーの名前に置き換えます。 実際のサーバー名が `myserver.database.windows.net` の場合、`<server name>` を `myserver` に置き換えます。
    - `<database name>` は、SQL Database の名前に置き換えます。

    ```powershell
    # PowerShell script for creating a new SQL user called myapp using application AppSP with secret
    # AppSP is part of an Azure AD admin for the Azure SQL server below
    
    # Download latest  MSAL  - https://www.powershellgallery.com/packages/MSAL.PS
    Import-Module MSAL.PS
    
    $tenantId = "<TenantId>"   # tenantID (Azure Directory ID) were AppSP resides
    $clientId = "<ClientId>"   # AppID also ClientID for AppSP     
    $clientSecret = "<ClientSecret>"   # Client secret for AppSP 
    $scopes = "https://database.windows.net/.default" # The end-point
    
    $result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -ClientSecret (ConvertTo-SecureString $clientSecret -AsPlainText -Force) -TenantId $tenantId -Scopes $scopes
    
    $Tok = $result.AccessToken
    #Write-host "token"
    $Tok
      
    $SQLServerName = "<server name>"    # Azure SQL logical server name 
    $DatabaseName = "<database name>"     # Azure SQL database name
    
    Write-Host "Create SQL connection string"
    $conn = New-Object System.Data.SqlClient.SQLConnection 
    $conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30"
    $conn.AccessToken = $Tok
    
    Write-host "Connect to database and execute SQL script"
    $conn.Open() 
    $ddlstmt = 'CREATE USER [myapp] FROM EXTERNAL PROVIDER;'
    Write-host " "
    Write-host "SQL DDL command"
    $ddlstmt
    $command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn)       
    
    Write-host "results"
    $command.ExecuteNonQuery()
    $conn.Close()
    ``` 

    または、「[SQL DB に対する Azure AD サービス プリンシパルの認証 - コード サンプル](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467)」のブログにあるコード サンプルを使用してもかまいません。 DDL ステートメント `CREATE USER [myapp] FROM EXTERNAL PROVIDER` を実行するようにスクリプトを変更してください。 同じスクリプトを使用して、通常の Azure AD ユーザー グループを SQL Database に作成することができます。

    
2. 次のコマンドを実行して、ユーザー *myapp* がデータベースに存在するかどうかを確認します。

    ```sql
    SELECT name, type, type_desc, CAST(CAST(sid as varbinary(16)) as uniqueidentifier) as appId from sys.database_principals WHERE name = 'myapp'
    GO
    ```

    同様の出力が表示されます。

    ```output
    name    type    type_desc   appId
    myapp   E   EXTERNAL_USER   6d228f48-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

## <a name="next-steps"></a>次のステップ

- [Azure SQL での Azure Active Directory のサービス プリンシパル](authentication-aad-service-principal.md)
- [Azure リソースのマネージド ID とは](../../active-directory/managed-identities-azure-resources/overview.md)
- [App Service と Azure Functions でマネージド ID を使用する方法](../../app-service/overview-managed-identity.md)
- [SQL DB に対する Azure AD サービス プリンシパルの認証 - コード サンプル](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467)
- [Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../../active-directory/develop/app-objects-and-service-principals.md)
- [Azure PowerShell で Azure サービス プリンシパルを作成する](/powershell/azure/create-azure-service-principal-azureps)
- [Azure SQL の Azure Active Directory のディレクトリ閲覧者ロール](authentication-aad-directory-readers-role.md)