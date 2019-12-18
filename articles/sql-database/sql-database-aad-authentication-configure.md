---
title: Azure Active Directory 認証を構成する
description: Azure Active Directory を構成した後で、Azure AD 認証を使って SQL Database、マネージド インスタンス、および SQL Data Warehouse に接続する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 11/06/2019
ms.openlocfilehash: 76ca8a5d781c22279ccad633cc7c5bc98d645df8
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901358"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>SQL による Azure Active Directory 認証の構成と管理

この記事では、Azure AD を作成して設定した後、Azure [SQL Database](sql-database-technical-overview.md)、[マネージド インスタンス](sql-database-managed-instance.md)、[SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) で Azure AD を使用する方法を示します。 概要については、「[Azure Active Directory 認証](sql-database-aad-authentication.md)」を参照してください。

> [!NOTE]
> この記事は Azure SQL サーバーのほか、その Azure SQL サーバーに作成される SQL Database と SQL Data Warehouse の両方に当てはまります。 わかりやすいように、SQL Database という言葉で SQL Database と SQL Data Warehouse の両方を言い表します。

> [!IMPORTANT]  
> Azure Active Directory アカウントを使用している場合、Azure VM 上で実行されている SQL Server への接続はサポートされていません。 代わりにドメインの Active Directory アカウントを使用してください。

## <a name="create-and-populate-an-azure-ad"></a>Azure AD を作成して設定する

Azure AD を作成し、ユーザーとグループを設定します。 Azure AD は初期の Azure AD のマネージド ドメインにすることができます。 また、Azure AD とフェデレーションされるオンプレミスの Active Directory Domain Services にすることもできます。

詳細については、「[オンプレミス ID と Azure Active Directory の統合](../active-directory/hybrid/whatis-hybrid-identity.md)」、[Azure AD への独自のドメイン名の追加](../active-directory/active-directory-domains-add-azure-portal.md)に関するページ、「[Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)」(Microsoft Azure が Windows Server Active Directory とのフェデレーションに対応)、「[Azure AD ディレクトリの管理](../active-directory/fundamentals/active-directory-administer.md)」、[Windows PowerShell を使用した Azure AD の管理](/powershell/azure/overview)に関するページ、および「[Hybrid Identity Required Ports and Protocols](../active-directory/hybrid/reference-connect-ports.md)」(ハイブリッド ID の必須ポートとプロトコル) を参照してください。

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Azure サブスクリプションの Azure Active Directory への関連付けまたは追加

1. Azure サブスクリプションを Azure Active Directory に関連付けるには、ディレクトリを、データベースをホストする Azure サブスクリプションの信頼できるディレクトリにします。 詳しくは、[Azure サブスクリプションを Azure AD に関連付ける方法](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)に関するページをご覧ください。

2. Azure Portal のディレクトリ スイッチャーを使用して、ドメインに関連付けられたサブスクリプションに切り替えます。

   > [!IMPORTANT]
   > すべての Azure サブスクリプションには、Azure AD インスタンスとの間に信頼関係があります。 つまり、ディレクトリを信頼してユーザー、サービス、デバイスを認証します。 複数のサブスクリプションが同じディレクトリを信頼できますが、1 つのサブスクリプションは 1 つのディレクトリだけを信頼します。 このサブスクリプションとディレクトリの間の信頼関係は、サブスクリプションと Azure 内の他のすべてのリソース (Web サイト、データベースなど) の間の関係と異なります。後者は、サブスクリプションの子リソースにより近いものです。 サブスクリプションの有効期限が切れた場合、サブスクリプションに関連付けられたこれらの他のリソースへのアクセスも停止します。 一方、ディレクトリは Azure 内に残っており、別のサブスクリプションをそのディレクトリと関連付けて、ディレクトリ ユーザーの管理を継続できます。 リソースの詳細については、[Azure でのリソース アクセスの説明](../active-directory/active-directory-b2b-admin-add-users.md)に関するページを参照してください。 この信頼関係について詳しくは、「[Azure サブスクリプションを Azure Active Directory に関連付けるまたは追加する方法](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」をご覧ください。

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Azure SQL Server の Azure AD 管理者を作成する

(SQL Database または SQL Data Warehouse をホストする) 各 Azure SQL Server は、Azure SQL Server 全体の管理者である 1 つのサーバー管理者アカウントから開始します。 Azure AD アカウントであるもう 1 つの SQL Server 管理者を作成する必要があります。 このプリンシパルは、master データベースの包含データベース ユーザーとして作成されます。 管理者の場合、サーバー管理者アカウントは、すべてのユーザー データベースの **db_owner** ロールのメンバーであり、**dbo** ユーザーとして各ユーザー データベースに入ります。 サーバー管理者アカウントについて詳しくは、「[Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)」をご覧ください。

geo レプリケーションで Azure Active Directory を使用する場合は、プライマリ サーバーとセカンダリ サーバーの両方で Azure Active Directory 管理者を構成する必要があります。 サーバーに Azure Active Directory 管理者がいない場合、Azure Active Directory へのログイン時に、"接続できません" サーバー エラーが発生します。

> [!NOTE]
> Azure AD アカウント (Azure SQL Server の管理者アカウントを含みます) に基づいていないユーザーは、Azure AD に基づくユーザーを作成できません。これは、Azure AD で提示されるデータベース ユーザーを検証するアクセス許可がないためです。

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>マネージド インスタンスの Azure Active Directory 管理者をプロビジョニングする

> [!IMPORTANT]
> 次の手順は、マネージド インスタンスをプロビジョニングする場合にのみに実行します。 この操作は、Azure AD 内のグローバル/会社の管理者か、特権ロール管理者だけが実行できます。 次の手順では、ディレクトリ内の異なる権限を持ったユーザーにアクセス許可を付与するプロセスについて説明します。

> [!NOTE]
> 一般提供より前に作成され、一般提供の後も引き続き運用される、MI の Azure AD 管理者については、既存の動作に対する機能的な変更はありません。 詳細については、「[MI の新しい Azure AD 管理者機能](#new-azure-ad-admin-functionality-for-mi)」セクションをご覧ください。

セキュリティ グループ メンバーシップを通じたユーザーの認証や、新しいユーザーの作成などといったタスクを正常に実行するには、マネージド インスタンスに Azure AD の読み取りアクセス許可が必要です。 そのためには、マネージド インスタンスに Azure AD の読み取りアクセス許可を付与する必要があります。 これには 2 つの方法があります。ポータルから付与する方法と、PowerShell を使用する方法です。 いずれの場合も、次の手順を実行します。

1. Azure Portal の右上隅にあるユーザー アイコンを選択すると、Active Directory 候補の一覧がドロップダウンで表示されます。

2. 既定の Azure AD として適切な Active Directory を選択します。

   この手順では、Active Directory に関連付けられたサブスクリプションを Managed Instance とリンクすることで、Azure AD とマネージド インスタンスの両方に同じサブスクリプションが使用されるようにします 。

3. マネージド インスタンスに移動し、Azure AD 統合に使用するものを選択します。

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Active Directory 管理者ページの上部でバナーを選択し、現在のユーザーにアクセス許可を付与します。 Azure AD のグローバル/会社の管理者としてログインしていれば、Azure portal からこれを行うことができます。PowerShell と以下のスクリプトを使用する方法もあります。

    ![アクセス許可の付与 (ポータル)](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

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
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. 操作が正常に完了すると、右上隅に次の通知が表示されます。

    ![success](./media/sql-database-aad-authentication/success.png)

6. これで、マネージド インスタンスの Azure AD 管理者を選択できるようになりました。 選択するには、[Active Directory 管理者] ページで **[管理者の設定]** を選択します。

    ![管理者の設定](./media/sql-database-aad-authentication/set-admin.png)

7. [AAD 管理者] ページで、ユーザーを検索し、管理者にするユーザーまたはグループを選択してから **[選択]** を選択します。

   [Active Directory 管理者] ページには、Active Directory のメンバーとグループがすべて表示されます。 淡色表示されているユーザーまたはグループは、Azure AD 管理者としてサポートされていないため選択できません サポートされている管理者の一覧については、「[Azure AD の機能と制限事項](sql-database-aad-authentication.md#azure-ad-features-and-limitations)」をご覧ください。 ロール ベースのアクセス制御 (RBAC) は Azure portal にのみ適用され、SQL Server には反映されません。

    ![Azure Active Directory 管理者を追加する](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. [Active Directory 管理者] ページの上部にある **[保存]** を選択します。

    ![[保存]](./media/sql-database-aad-authentication/save.png)

    管理者を変更する処理には数分かかる場合があります。 処理が完了すると、 [Active Directory 管理者] ボックスに新しい管理者が表示されます。

マネージド インスタンスに Azure AD 管理者をプロビジョニングしたら、<a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> 構文を利用し、Azure AD サーバー プリンシパル (ログイン) の作成を開始できます。 詳細については、[マネージド インスタンスの概要](sql-database-managed-instance.md#azure-active-directory-integration)に関する記事を参照してください。

> [!TIP]
> 後で管理者を削除するには、[Active Directory 管理者] ページの上部にある **[管理者の削除]** を選択し、 **[保存]** を選択します。

### <a name="new-azure-ad-admin-functionality-for-mi"></a>MI の新しい Azure AD 管理者機能

次の表は、MI のパブリック プレビュー Azure AD ログイン管理者の機能と、Azure AD ログインについて一般提供で提供される新機能をまとめたものです。

| パブリック プレビュー期間中の MI の Azure AD ログイン管理者 | MI の Azure AD 管理者の一般提供機能 |
| --- | ---|
| SQL Database の Azure AD 管理者と同様の動作になります。これにより Azure AD 認証が有効になりますが、Azure AD 管理者は、MI のマスター DB に Azure AD ログインまたは SQL ログインを作成することはできません。 | Azure AD 管理者には sysadmin アクセス許可があり、MI のマスター DB に AAD ログインと SQL ログインを作成できます。 |
| sys.server_principals ビューに存在しません | sys.server_principals ビューに存在します |
| 個々の Azure AD ゲスト ユーザーを MI の Azure AD 管理者として設定できます。 詳細については、「[Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../active-directory/b2b/add-users-administrator.md)」を参照してください。 | ゲスト ユーザーをメンバーとして含む Azure AD グループを作成して、 MI の Azure AD 管理者としてこのグループを設定する必要があります。 詳細については、「[Azure AD の企業間サポート](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support)」を参照してください。 |

一般提供の前に作成され、一般提供の後も引き続き運用される、MI の既存の Azure AD 管理者のベスト プラクティスとして、同じ Azure AD ユーザーまたはグループに対して Azure portal の [管理者の削除] オプションと [管理者の設定] オプションを使用して Azure AD 管理者をリセットしてください。

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>MI の Azure AD ログインの一般提供に関する既知の問題

- T-SQL コマンド `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER` を使用して作成された MI のマスター データベースに Azure AD ログインが存在する場合、それを MI の Azure AD 管理者として設定することはできません。 Azure AD ログインを作成するために、Azure portal、PowerShell、または CLI コマンドを使用してそのログインを Azure AD 管理者として設定すると、エラーが発生します。
  - そのアカウントを Azure AD 管理者として作成するには、コマンド `DROP LOGIN [myaadaccount]` を使用して、マスター データベースでそのログインを削除する必要があります。
  - `DROP LOGIN` が成功したら、Azure portal で Azure AD 管理者アカウントを設定します。 
  - Azure AD 管理者アカウントを設定できない場合は、そのログインのマネージド インスタンスのマスター データベースをチェックインします。 コマンド `SELECT * FROM sys.server_principals` を使用します。
  - MI の Azure AD 管理者を設定すると、このアカウントのマスター データベースにログインが自動的に作成されます。 Azure AD 管理者を削除すると、マスター データベースからログインが自動的に削除されます。

- 個々の Azure AD ゲスト ユーザーは、MI の Azure AD 管理者としてサポートされません。 ゲスト ユーザーを Azure AD 管理者として設定するには、 Azure AD グループの一員にする必要があります。現在のところ、Azure portal ブレードでは、別の Azure AD のゲスト ユーザーは淡色表示されず、ユーザーは管理者のセットアップを続行できるようになっています。 ゲスト ユーザーを Azure AD 管理者として保存すると、セットアップは失敗します。
  - ゲスト ユーザーを MI の Azure AD 管理者にする場合は、ゲスト ユーザーを Azure AD グループに含めて、このグループを Azure AD 管理者として設定します。

### <a name="powershell-for-sql-managed-instance"></a>SQL マネージド インスタンス用の PowerShell

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell コマンドレットを実行するには、Azure PowerShell をインストールし、実行している必要があります。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」をご覧ください。

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 AzureRM モジュールのバグ修正は、少なくとも 2020 年 12 月までは引き続き受け取ることができます。  Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 その互換性の詳細については、「[新しい Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)」を参照してください。

Azure AD 管理者をプロビジョニングするには、次のような Azure PowerShell コマンドを実行する必要があります。

- Connect-AzAccount
- Select-AzSubscription

SQL マネージド インスタンスの Azure AD 管理者のプロビジョニングと管理に使用するコマンドレットは、次のとおりです。

| コマンドレット名 | 説明 |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |現在のサブスクリプションでの SQL マネージド インスタンスの Azure AD 管理者をプロビジョニングします (現在のサブスクリプションから実行する必要があります)。|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |現在のサブスクリプションでの SQL マネージド インスタンスの Azure AD 管理者を削除します。 |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |現在のサブスクリプションでの SQL マネージド インスタンスの Azure AD 管理者に関する情報を返します。|

次のコマンドでは、ResourceGroup01 というリソース グループに関連付けられている、ManagedInstance01 というマネージド インスタンスの Azure AD 管理者に関する情報が取得されます。

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

次のコマンドでは、ManagedInstance01 というマネージド インスタンスの、DBAs という Azure AD 管理者グループがプロビジョニングされます。 このサーバーは、リソース グループ ResourceGroup01 に関連付けられています。

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

次のコマンドを実行すると、リソース グループ ResourceGroup01 に関連付けられている、ManagedInstanceName01 というマネージド インスタンスの Azure AD 管理者が削除されます。

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

次の CLI コマンドを呼び出して、SQL マネージド インスタンスの Azure AD 管理者をプロビジョニングすることもできます。

| command | 説明 |
| --- | --- |
|[az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | SQL マネージド インスタンスの Azure Active Directory 管理者をプロビジョニングします (現在のサブスクリプションから実行する必要があります)。 |
|[az sql mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | SQL マネージド インスタンスの Azure Active Directory 管理者を削除します。 |
|[az sql mi ad-admin list](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | 現在 SQL マネージド インスタンス用に構成されている Azure Active Directory 管理者に関する情報を返します。 |
|[az sql mi ad-admin update](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | SQL マネージド インスタンスの Active Directory 管理者を更新します。 |

CLI コマンドの詳細については、「[az sql mi](/cli/azure/sql/mi)」を参照してください。

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Azure SQL Database サーバーの Azure Active Directory 管理者をプロビジョニングする

> [!IMPORTANT]
> 次の手順は、Azure SQL Database サーバーまたは Data Warehouse をプロビジョニングする場合にのみ実行します。

次の 2 つの手順は、Azure Portal と PowerShell を使用して、Azure SQL Server の Azure Active Directory 管理者をプロビジョニングする方法を示しています。

### <a name="azure-portal"></a>Azure ポータル

1. [Azure Portal](https://portal.azure.com/) の右上隅にあるユーザー アイコンをクリックすると、Active Directory 候補の一覧がドロップダウンで表示されます。 既定の Azure AD として適切な Active Directory を選択します。 この手順では、サブスクリプションに関連付けられている Active Directory を Azure SQL Server とリンクすることで、Azure AD と SQL Server の両方に同じサブスクリプションが使用されるようにします (Azure SQL Server は、Azure SQL Database または Azure SQL Data Warehouse をホストしている可能性があります)。

    ![choose-ad][8]

2. **[SQL サーバー]** を探して選択します。

    ![[SQL サーバー] を探して選択する](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > このページでは、 **[SQL Server]** を選択する前に、名前の横にある**星マーク**を選択してそのカテゴリを "*お気に入りに追加*" し、 **[SQL Server]** を左側のナビゲーション バーに追加することができます。

3. **[SQL Server]** ページで、 **[Active Directory 管理者]** を選択します。

4. **[Active Directory 管理者]** ページで、 **[管理者の設定]** を選択します。

    ![[SQL サーバー] の Active Directory 管理者の設定](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. **[管理者の追加]** ページで、ユーザーを検索し、管理者にするユーザーまたはグループを選択してから **[選択]** を選択します。 [Active Directory 管理者] ページには、Active Directory のメンバーとグループがすべて表示されます。 淡色表示されているユーザーまたはグループは、Azure AD 管理者としてサポートされていないため選択できません (「[Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse を認証する](sql-database-aad-authentication.md)」の「**Azure AD の機能と制限事項**」セクションでサポートされている管理者の一覧を参照してください)。ロール ベースのアクセス制御 (RBAC) はポータルにのみ適用され、SQL Server には反映されません。

    ![Azure Active Directory 管理者を選択する](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. **[Active Directory 管理者]** ページの上部にある **[保存]** を選択します。

    ![管理者の保存](./media/sql-database-aad-authentication/save-admin.png)

管理者を変更する処理には数分かかる場合があります。 処理が完了すると、 **[Active Directory 管理者]** ボックスに新しい管理者が表示されます。

   > [!NOTE]
   > Azure AD 管理者をセットアップする場合、新しい管理者名 (ユーザーまたはグループ) が SQL Server 認証ユーザーとして仮想マスター データベースに既に存在していてはいけません。 存在する場合、Azure AD 管理者のセットアップは失敗します。その作成をロールバックされ、そのような管理者 (名前) が既に存在していることが示されます。 そのような SQL Server 認証ユーザーは Azure AD に属していないため、Azure AD 認証を使用してサーバーに接続しようとしても失敗します。

後で管理者を削除するには、 **[Active Directory 管理者]** ページの上部にある **[管理者の削除]** を選択し、 **[保存]** を選択します。

### <a name="powershell-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Azure SQL Database と Azure SQL Data Warehouse 用の PowerShell

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell コマンドレットを実行するには、Azure PowerShell をインストールし、実行している必要があります。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」をご覧ください。 Azure AD 管理者をプロビジョニングするには、次のような Azure PowerShell コマンドを実行する必要があります。

- Connect-AzAccount
- Select-AzSubscription

Azure SQL Database および Azure SQL Data Warehouse の Azure AD 管理者のプロビジョニングと管理に使用するコマンドレットは、次のとおりです。

| コマンドレット名 | 説明 |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Azure SQL Server または Azure SQL Data Warehouse の Azure Active Directory 管理者をプロビジョニングします (現在のサブスクリプションから実行する必要があります)。 |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Azure SQL Server または Azure SQL Data Warehouse の Azure Active Directory 管理者を削除します。 |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |現在 Azure SQL Server または Azure SQL Data Warehouse 用に構成されている Azure Active Directory 管理者に関する情報を返します。 |

これらの各コマンドの情報を確認するには、PowerShell の get-help コマンドを使用します。 たとえば、「 `get-help Set-AzSqlServerActiveDirectoryAdministrator` 」のように入力します。

次のスクリプトでは、**Group-23** という名前のリソース グループ内にあるサーバー **demo_server** に対して、**DBA_Group** という名前の Azure AD 管理者グループ (オブジェクト ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`) をプロビジョニングします。

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** 入力パラメーターには、Azure AD の表示名またはユーザー プリンシパル名を使用できます。 たとえば、``DisplayName="John Smith"`` や ``DisplayName="johns@contoso.com"``す。 Azure AD グループの場合は、Azure AD の表示名のみがサポートされています。

> [!NOTE]
> Azure PowerShell コマンド ```Set-AzSqlServerActiveDirectoryAdministrator``` では、サポートされていないユーザーに対する Azure AD 管理者のプロビジョニングは禁止されていません。 サポートされていないユーザーのプロビジョニングは可能ですが、このようなユーザーはデータベースに接続できません

次の例では、オプションとして **ObjectID**を使用します。

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> **DisplayName** が一意でない場合は、Azure AD の **ObjectID** が必要です。 **ObjectID** と **DisplayName** の値を取得するには、Azure クラシック ポータルの [Active Directory] セクションを使用して、ユーザーまたはグループのプロパティを確認します。

次の例では、Azure SQL Server の現在の Azure AD 管理者に関する情報が返されます。

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

次の例では、Azure AD 管理者が削除されます。

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

以下の CLI コマンドを呼び出して、Azure AD 管理者をプロビジョニングできます。

| command | 説明 |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Azure SQL Server または Azure SQL Data Warehouse の Azure Active Directory 管理者をプロビジョニングします (現在のサブスクリプションから実行する必要があります)。 |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Azure SQL Server または Azure SQL Data Warehouse の Azure Active Directory 管理者を削除します。 |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | 現在 Azure SQL Server または Azure SQL Data Warehouse 用に構成されている Azure Active Directory 管理者に関する情報を返します。 |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Azure SQL Server または Azure SQL Data Warehouse の Active Directory 管理者を更新します。 |

CLI コマンドの詳細については、「[az sql server](/cli/azure/sql/server)」を参照してください。

* * *

> [!NOTE]
> Azure Active Directory 管理者は、REST API を使用してプロビジョニングすることもできます。 詳細については、[Service Management REST API リファレンスと Azure SQL Database の操作](/rest/api/sql/)に関するページを参照してください。

## <a name="configure-your-client-computers"></a>クライアント コンピューターを構成する

Azure AD の ID を使用して Azure SQL Database または Azure SQL Data Warehouse に接続するアプリケーションまたはユーザーが存在するすべてのクライアント コンピューターには、次のソフトウェアをインストールする必要があります。

- [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx) の .NET Framework 4.6 以降。
- SQL Server 用の Azure Active Directory 認証ライブラリ (*ADALSQL.DLL*)。ダウンロード センターの「[Microsoft SQL Server 用の Microsoft Active Directory 認証ライブラリ](https://www.microsoft.com/download/details.aspx?id=48742)」から複数の言語版 (x86 と amd64 の両方) を入手できます。

これらの要件は、次の操作を行うことで満たすことができます。

- [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) または [SQL Server Data Tools for Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) をインストールすると、.NET Framework 4.6 の要件が満たされます。
- SSMS の場合、x86 バージョンの *ADALSQL.DLL*がインストールされます。
- SSDT の場合、amd64 バージョンの *ADALSQL.DLL*がインストールされます。
- 「 [Visual Studio のダウンロード](https://www.visualstudio.com/downloads/download-visual-studio-vs) 」にある最新の Visual Studio をインストールすると、.NET Framework 4.6 の要件は満たされますが、必要な amd64 バージョンの *ADALSQL.DLL*はインストールされません。

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Azure AD の ID にマップされている包含データベース ユーザーをデータベースに作成する

> [!IMPORTANT]
> マネージド インスタンスで Azure AD サーバー プリンシパル (ログイン) がサポートされるようになったため、Azure AD ユーザー、グループ、アプリケーションからログインを作成できます。 Azure AD サーバー プリンシパル (ログイン) では、包含データベース ユーザーとしてデータベース ユーザーを作成することを要求せずにマネージド インスタンスで認証を受ける機能が提供されます。 詳細については、[マネージド インスタンスの概要](sql-database-managed-instance.md#azure-active-directory-integration)に関する記事を参照してください。 Azure AD サーバー プリンシパル (ログイン) の作成の構文については、「<a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>」を参照してください。

Azure Active Directory 認証では、データベース ユーザーを包含データベース ユーザーとして作成することが必要です。 Azure AD の ID に基づく包含データベース ユーザーは、master データベースにログインを持たないデータベース ユーザーで、そのデータベースに関連付けられている Azure AD ディレクトリの ID にマップされています。 Azure AD の ID には、個々のユーザー アカウントにもグループ アカウントにもなります。 包含データベース ユーザーの詳細については、「 [包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」を参照してください。

> [!NOTE]
> Azure Portal を使用して、データベース ユーザー (管理者を除く) を作成することはできません。 RBAC ロールは、SQL Server、SQL Database、または SQL Data Warehouse には反映されません。 Azure RBAC ロールは Azure リソースの管理に使用され、データベースのアクセス許可には適用されません。 たとえば、 **SQL Server 共同作成者** ロールでは、SQL Database または SQL Data Warehouse に接続するためのアクセス権は付与されません。 Transact-SQL ステートメントを使用して、アクセス許可をデータベースで直接付与する必要があります。

> [!WARNING]
> T-SQL CREATE LOGIN および CREATE USER ステートメントにユーザー名として含まれているコロン `:` やアンパサンド `&` などの特殊文字はサポートされていません。

Azure AD ベースの包含データベース ユーザー (データベースを所有するサーバー管理者以外) を作成するには、少なくとも **ALTER ANY USER** アクセス許可を持つユーザーとして、Azure AD の ID でデータベースに接続します。 その後、次の Transact-SQL 構文を使用します。

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* には、Azure AD ユーザーのユーザー プリンシパル名または Azure AD のグループの表示名を指定できます。

**例:** Azure AD のフェデレーション ドメインまたはマネージド ドメインのユーザーを表す包含データベース ユーザーを作成するには、次のようにします。

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Azure AD またはフェデレーション ドメインのグループを表す包含データベース ユーザーを作成するには、セキュリティ グループの表示名を指定します。

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Azure AD トークンを使用して接続するアプリケーションを表す包含データベース ユーザーを作成するには、次のようにします。

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> このコマンドでは、SQL がログイン ユーザーの代わりに Azure AD (「外部プロバイダー」) にアクセスすることが必要です。 場合によっては、Azure AD によって SQL に例外が返されることがあります。 このような場合、ユーザーには AAD 固有のエラー メッセージを含む SQL エラー 33134 が表示されます。 このエラーはほとんどの場合、アクセスが拒否されたこと、ユーザーがリソースにアクセスするために MFA に登録する必要があること、あるいはファーストパーティのアプリケーション間のアクセスを事前承認によって処理する必要があることを示します。 最初の 2 つのケースでは、問題は通常、ユーザーの AAD テナントで設定されている条件付きアクセス ポリシーによって発生し、ポリシーによってユーザーは外部プロバイダーにアクセスできなくなります。 アプリケーション '00000002-0000-0000-c000-000000000000' (AAD Graph API のアプリケーション ID) へのアクセスを許可するように CA ポリシーを更新すると、問題が解決されます。 「ファーストパーティのアプリケーション間のアクセスを事前承認で処理する必要がある」というエラーが発生した場合、この問題は、ユーザーがサービス プリンシパルとしてサインインしていることが原因です。 代わりにユーザーによってコマンドを実行した場合、コマンドは成功するはずです。

> [!TIP]
> 使用している Azure サブスクリプションに関連付けられている Azure Active Directory とは別の Azure Active Directory から、ユーザーを直接作成することはできません。 ただし、別の Active Directory のメンバーのうち、関連付けられている Active Directory にインポート済みのユーザー (外部ユーザーと呼ばれます) は、テナント Active Directory の Active Directory グループに追加できます。 外部の Active Directory のユーザーは、この AD グループの包含データベース ユーザーを作成することで SQL Database にアクセスできるようになります。

Azure Active Directory の ID に基づく包含データベース ユーザーの作成の詳細については、「 [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx)」を参照してください。

> [!NOTE]
> Azure SQL Server の Azure Active Directory 管理者を削除すると、Azure AD 認証ユーザーはサーバーに接続できなくなります。 必要に応じて、SQL Database 管理者は不要な Azure AD ユーザーを手動で削除できます。

> [!NOTE]
> "**接続がタイムアウトしました**" と表示された場合は、接続文字列の `TransparentNetworkIPResolution` パラメーターを false に設定することが必要になる場合があります。 詳しくは、「[Connection timeout issue with .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/)」(.NET Framework 4.6.1 での接続タイムアウトの問題 - TransparentNetworkIPResolution) をご覧ください。

データベース ユーザーを作成すると、そのユーザーには **CONNECT** アクセス許可が付与され、**PUBLIC** ロールのメンバーとしてそのデータベースに接続できるようになります。 最初にこのユーザーが利用できるアクセス許可は、**PUBLIC** ロールに付与されているアクセス許可か、またはそのユーザーが属している Azure AD グループに付与されているアクセス許可のみです。 Azure AD ベースの包含データベース ユーザーをプロビジョニングすると、他の種類のユーザーにアクセス許可を付与する場合と同様に、そのユーザーに追加のアクセス許可を付与できます。 通常は、データベース ロールにアクセス許可を付与し、そのロールにユーザーを追加します。 詳細については、 [データベース エンジンのアクセス許可の基本知識](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)に関するページを参照してください。 特殊な SQL Database ロールの詳細については、「 [Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)」を参照してください。
外部ユーザーとしてマネージド ドメインにインポートされるフェデレーション ドメイン ユーザー アカウントは、マネージド ドメインの ID を使用する必要があります。

> [!NOTE]
> データベースのメタデータでは、Azure AD ユーザーはタイプ E (EXTERNAL_USER) 、グループはタイプ X (EXTERNAL_GROUPS) でマークされます。 詳細については、「[sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)」を参照してください。

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>SSMS または SSDT を使用して、ユーザー データベースまたはデータ ウェアハウスに接続する  

Azure AD 管理者が正しく設定されていることを確認するには、Azure AD の管理者アカウントを使用して **master** データベースに接続します。
Azure AD ベースの包含データベース ユーザー (データベースを所有しているサーバー管理者以外) をプロビジョニングするには、そのデータベースへのアクセス権を持つ Azure AD の ID を使用してデータベースに接続します。

> [!IMPORTANT]
> Azure Active Directory 認証は、[SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) および Visual Studio 2015 の [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) でサポートされています。 SSMS の 2016 年 8 月のリリースには、Active Directory Universal 認証のサポートも含まれます。これにより、管理者は、電話、テキスト メッセージ、スマート カードと暗証番号 (PIN)、またはモバイル アプリ通知を使用する Multi-Factor Authentication を要求できます。

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Azure AD の ID で SSMS または SSDT を利用して接続する

次の手順は、SQL Server Management Studio または SQL Server Database Tools で Azure AD の ID を使用して SQL Database に接続する方法を示しています。

### <a name="active-directory-integrated-authentication"></a>Active Directory 統合認証

フェデレーション ドメインから Azure Active Directory の資格情報を使用して Windows にログオンしている場合は、この方法を使用します。

1. Management Studio または Data Tools を起動し、 **[サーバーへの接続]** (または **[データベース エンジンへの接続]** ) ダイアログ ボックスの **[認証]** ボックスで、 **[Active Directory - 統合]** を選択します。 接続用の既存の資格情報が表示されるため、パスワードは不要であるか、入力できません。

    ![AD 統合認証を選択する][11]

2. **[オプション]** ボタンを選択し、 **[接続プロパティ]** ページの **[データベースへの接続]** ボックスに、接続先となるユーザー データベースの名前を入力します。 **[AD ドメインの名前またはテナントの ID]** オプションは **MFA ユニバーサル接続**オプションでのみサポートされており、それ以外の場合はグレーで表示されます。  

    ![データベース名を選択する][13]

## <a name="active-directory-password-authentication"></a>Active Directory パスワード認証

Azure AD のマネージド ドメインを使用して Azure AD のプリンシパル名で接続する場合は、この方法を使用します。 また、リモートで作業する場合など、ドメインにアクセスできないフェデレーション アカウントにもこの方法を使用できます。

Azure AD のネイティブ ユーザーまたはフェデレーション ユーザーのために、SQL DB/DW を Azure AD で認証するには、この方法を使用します。 ネイティブ ユーザーとは、Azure AD に明示的に作成され、ユーザー名とパスワードを使用して認証されるユーザーです。これに対し、フェデレーション ユーザーとは、所属ドメインが Azure AD との間でフェデレーションされている Windows ユーザーをいいます。 後者の (ユーザーとパスワードを使用した) 方法は、ユーザーが自分の Windows 資格情報の使用を希望しているものの、そのローカル コンピューターがドメインに参加していない (つまりリモート アクセスを使用している) 場合に使用できます。 このケースでは、Windows ユーザーが自分のドメイン アカウントとパスワードを指定し、フェデレーションされた資格情報を使用して SQL DB/DW に対する認証を行うことができます。

1. Management Studio または Data Tools を起動し、 **[サーバーへの接続]** (または **[データベース エンジンへの接続]** ) ダイアログ ボックスの **[認証]** ボックスで、 **[Active Directory - パスワード]** を選択します。

2. **[ユーザー名]** ボックスに、Azure Active Directory のご自分のユーザー名を **username\@domain.com** の形式で入力します。 ユーザー名は、Azure Active Directory のアカウントか、Azure Active Directory とフェデレーションするドメインのアカウントである必要があります。

3. **[パスワード]** ボックスに、Azure Active Directory アカウントまたはフェデレーション ドメイン アカウントのユーザー パスワードを入力します。

    ![AD パスワード認証を選択する][12]

4. **[オプション]** ボタンを選択し、 **[接続プロパティ]** ページの **[データベースへの接続]** ボックスに、接続先となるユーザー データベースの名前を入力します。 (前のオプションの図を参照してください)。

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>クライアント アプリケーションからの Azure AD の ID を使用した接続

次の手順は、クライアント アプリケーションから Azure AD の ID を使用して SQL データベースに接続する方法を示しています。

### <a name="active-directory-integrated-authentication"></a>Active Directory 統合認証

統合 Windows 認証を使用するには、ドメインの Active Directory が Azure Active Directory とフェデレーションする必要があります。 データベースに接続するクライアント アプリケーション (またはサービス) は、ユーザーのドメイン資格情報を使用してドメインに参加しているコンピューターで実行されている必要があります。

統合認証と Azure AD の ID を使用してデータベースに接続するには、データベース接続文字列内の Authentication キーワードを "Active Directory Integrated" に設定する必要があります。 次の C# のコード サンプルでは、ADO .NET を使用します。

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

接続文字列キーワード `Integrated Security=True` は、Azure SQL Database への接続ではサポートされていません。 ODBC 接続を行うには、スペースを削除して Authentication を 'ActiveDirectoryIntegrated' に設定する必要があります。

### <a name="active-directory-password-authentication"></a>Active Directory パスワード認証

統合認証と Azure AD の ID を使用してデータベースに接続するには、Authentication キーワードを "Active Directory Password" に設定する必要があります。 接続文字列にユーザー ID (UID) とパスワード (PWD) のキーワードと値を含める必要があります。 次の C# のコード サンプルでは、ADO .NET を使用します。

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

[GitHub の Azure AD 認証のデモ](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)で入手できるデモ コード サンプルを使用して、Azure AD の認証方法の詳細を確認してください。

## <a name="azure-ad-token"></a>Azure AD トークン

この認証方法を使用すると、中間層サービスは Azure Active Directory (AAD) からトークンを取得して、Azure SQL Database または Azure SQL Data Warehouse に接続できます。 この方法では、証明書ベースの認証などの高度なシナリオが可能になります。 Azure AD トークンの認証を使用するには、4 つの基本的な手順を完了する必要があります。

1. Azure Active Directory にアプリケーションを登録し、コードのクライアント ID を取得します。
2. アプリケーションを表すデータベース ユーザーを作成します (上の手順 6. で完了しています)。
3. アプリケーションを実行するクライアント コンピューターで証明書を作成します。
4. アプリケーションのキーとして、証明書を追加します。

サンプルの接続文字列を次に示します。

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

詳細については、「 [SQL Server Security Blog (SQL Server のセキュリティに関するブログ)](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)」をご覧ください。 証明書の追加の詳細については、「[Azure Active Directory の証明書ベースの認証の概要](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)」を参照してください。

### <a name="sqlcmd"></a>sqlcmd

次のステートメントは、sqlcmd のバージョン 13.1 を使用して接続しています。これは、[ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53591)から入手できます。

> [!NOTE]
> `-G` を使用する `sqlcmd` コマンドは、システム ID で動作しないため、ユーザー プリンシパル ログインが必要です。

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>次の手順

- SQL Database でのアクセスおよび制御の概要については、[SQL Database のアクセスと制御](sql-database-control-access.md)に関するページを参照してください。
- SQL Database のログイン、ユーザー、データベース ロールの概要については、[ログイン、ユーザー、およびデータベース ロール](sql-database-manage-logins.md)に関するページを参照してください。
- データベース プリンシパルの詳細については、「[プリンシパル](https://msdn.microsoft.com/library/ms181127.aspx)」を参照してください。
- データベース ロールの詳細については、[データベース ロール](https://msdn.microsoft.com/library/ms189121.aspx)に関するページを参照してください。
- SQL Database のファイアウォール規則の詳細については、[SQL Database のファイアウォール規則](sql-database-firewall-configure.md)に関するページを参照してください。

<!--Image references-->
[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
