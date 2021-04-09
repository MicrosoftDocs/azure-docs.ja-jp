---
title: Azure Active Directory 認証を構成する
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Azure Active Directory を構成した後で、Azure AD 認証を使用して SQL Database、SQL Managed Instance、Azure Synapse Analytics に接続する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 08/17/2020
ms.openlocfilehash: f3c34526fd4005dbbb0be7e763721e125ed7828e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201212"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>Azure SQL での Azure AD 認証を構成して管理する

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

この記事では、Azure Active Directory (Azure AD) インスタンスを作成して設定した後、[Azure SQL Database](sql-database-paas-overview.md)、[Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)、[Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) で Azure AD を使用する方法を示します。 概要については、「[Azure Active Directory 認証](authentication-aad-overview.md)」を参照してください。

## <a name="azure-ad-authentication-methods"></a>Azure AD の認証方法

Azure AD 認証では、次の認証方法がサポートされます。

- Azure AD クラウド専用 ID
- サポートされる Azure AD ハイブリッド ID:
  - シームレス シングル サインオン (SSO) と組み合わせた 2 つのオプションによるクラウド認証
    - Azure AD パスワード ハッシュ認証
    - Azure AD パススルー認証
  - フェデレーション認証

Azure AD 認証の方法の詳細については、「[Azure Active Directory ハイブリッド ID ソリューションの適切な認証方法を選択する](../../active-directory/hybrid/choose-ad-authn.md)」を参照してください。

Azure AD ハイブリッド ID、セットアップ、および同期に関する詳細については、次の記事を参照してください。

- パスワード ハッシュ認証 - 「[Azure AD Connect 同期を使用したパスワード ハッシュ同期の実装](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)」
- パススルー認証 - 「[Azure Active Directory パススルー認証](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)」
- フェデレーション認証 - 「[Azure での Active Directory フェデレーション サービス (AD FS) のデプロイ](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)」および「[Azure AD Connect とフェデレーション](../../active-directory/hybrid/how-to-connect-fed-whatis.md)」

## <a name="create-and-populate-an-azure-ad-instance"></a>Azure AD インスタンスを作成して設定する

Azure AD インスタンスを作成し、ユーザーとグループを設定します。 Azure AD は初期の Azure AD のマネージド ドメインにすることができます。 また、Azure AD とフェデレーションされるオンプレミスの Active Directory Domain Services にすることもできます。

詳細については、「[オンプレミス ID と Azure Active Directory の統合](../../active-directory/hybrid/whatis-hybrid-identity.md)」、[Azure AD への独自のドメイン名の追加](../../active-directory/fundamentals/add-custom-domain.md)に関するページ、「[Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)」(Microsoft Azure が Windows Server Active Directory とのフェデレーションに対応)、「[Azure AD ディレクトリの管理](../../active-directory/fundamentals/active-directory-whatis.md)」、[Windows PowerShell を使用した Azure AD の管理](/powershell/azure/)に関するページ、および「[Hybrid Identity Required Ports and Protocols](../../active-directory/hybrid/reference-connect-ports.md)」(ハイブリッド ID の必須ポートとプロトコル) を参照してください。

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Azure サブスクリプションの Azure Active Directory への関連付けまたは追加

1. Azure サブスクリプションを Azure Active Directory に関連付けるには、ディレクトリを、データベースをホストする Azure サブスクリプションの信頼できるディレクトリにします。 詳細については、「[Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

2. Azure Portal のディレクトリ スイッチャーを使用して、ドメインに関連付けられたサブスクリプションに切り替えます。

   > [!IMPORTANT]
   > すべての Azure サブスクリプションには、Azure AD インスタンスとの間に信頼関係があります。 つまり、ディレクトリを信頼してユーザー、サービス、デバイスを認証します。 複数のサブスクリプションが同じディレクトリを信頼できますが、1 つのサブスクリプションは 1 つのディレクトリだけを信頼します。 このサブスクリプションとディレクトリの間の信頼関係は、サブスクリプションと Azure 内の他のすべてのリソース (Web サイト、データベースなど) の間の関係と異なります。後者は、サブスクリプションの子リソースにより近いものです。 サブスクリプションの有効期限が切れた場合、サブスクリプションに関連付けられたこれらの他のリソースへのアクセスも停止します。 一方、ディレクトリは Azure 内に残っており、別のサブスクリプションをそのディレクトリと関連付けて、ディレクトリ ユーザーの管理を継続できます。 リソースの詳細については、[Azure でのリソース アクセスの説明](../../active-directory/external-identities/add-users-administrator.md)に関するページを参照してください。 この信頼関係について詳しくは、「[Azure サブスクリプションを Azure Active Directory に関連付けるまたは追加する方法](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」をご覧ください。

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>SQL Database でのサーバーの Azure AD 管理者

Azure 内の (SQL Database または Azure Synapse をホストする) 各[サーバー](logical-servers.md)は、サーバー全体の管理者である単一のサーバー管理者アカウントを使用して開始されます。 2 番目の管理者アカウントを Azure AD アカウントとして作成します。 このプリンシパルは、サーバーの master データベースの包含データベース ユーザーとして作成されます。 管理者アカウントは、すべてのユーザー データベースの **db_owner** ロールのメンバーであり、**dbo** ユーザーとして各ユーザー データベースに入ります。 管理者アカウントの詳細については、「[データベースとログインの管理](logins-create-manage.md)」を参照してください。

geo レプリケーションで Azure Active Directory を使用する場合は、プライマリ サーバーとセカンダリ サーバーの両方で Azure Active Directory 管理者を構成する必要があります。 サーバーに Azure Active Directory 管理者がいない場合、Azure Active Directory へのログイン時に、"`Cannot connect`" サーバー エラーが発生します。

> [!NOTE]
> Azure AD アカウント (サーバー管理者アカウントを含みます) に基づいていないユーザーは、Azure AD に基づくユーザーを作成できません。これは、Azure AD で提示されるデータベース ユーザーを検証するアクセス許可がないためです。

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>Azure AD 管理者 (SQL Managed Instance) をプロビジョニングする

> [!IMPORTANT]
> 次の手順は、Azure SQL Managed Instance をプロビジョニングする場合にのみに実行します。 この操作は、Azure AD の全体管理者または特権ロール管理者だけが実行できます。
>
> **パブリック プレビュー** では、Azure AD 内のグループに **ディレクトリ閲覧者** ロールを割り当てることができます。 その後、グループの所有者が、このグループのメンバーとしてマネージ インスタンス ID を追加すると、その SQL Managed Instance に Azure AD 管理者をプロビジョニングできるようになります。 この機能の詳細については、「[Azure SQL の Azure Active Directory のディレクトリ閲覧者ロール](authentication-aad-directory-readers-role.md)」を参照してください。

セキュリティ グループ メンバーシップを通じたユーザーの認証や、新しいユーザーの作成などといったタスクを正常に実行するには、SQL Managed Instance に Azure AD の読み取りアクセス許可が必要です。 そのためには、SQL Managed Instance に Azure AD の読み取りアクセス許可を付与する必要があります。 この操作は、Azure portal または PowerShell から実行できます。

### <a name="azure-portal"></a>Azure portal

Azure portal を使用して SQL Managed Instance に Azure AD の読み取りアクセス許可を付与するには、Azure AD で全体管理者としてログインし、次の手順のようにします。

1. [Azure portal](https://portal.azure.com) の右上隅にある使用可能な Active Directory のドロップダウン リストから、お使いの接続を選択します。

2. 既定の Azure AD として適切な Active Directory を選択します。

   このステップでは、Active Directory に関連付けられたサブスクリプションを SQL Managed Instance とリンクすることで、Azure AD インスタンスと SQL Managed Instance の両方に同じサブスクリプションが使用されるようにします。

3. Azure AD の統合に使用する SQL Managed Instance に移動します。

   ![選択された SQL マネージド インスタンスの [Active Directory 管理者] ページを開いている Azure portal のスクリーンショット。](./media/authentication-aad-configure/aad.png)

4. Active Directory 管理者ページの上部でバナーを選択し、現在のユーザーにアクセス許可を付与します。

    ![Active Directory にアクセスするためのアクセス許可を SQL マネージド インスタンスに付与するダイアログのスクリーンショット。 [アクセス許可の付与] ボタンが選択されています。](./media/authentication-aad-configure/grant-permissions.png)

5. 操作が正常に完了すると、右上隅に次の通知が表示されます。

    ![Active Directory の読み取りアクセス許可がマネージド インスタンスに対して正常に更新されたことを確認する通知のスクリーンショット。](./media/authentication-aad-configure/success.png)

6. これで、SQL Managed Instance に対する Azure AD 管理者を選択できるようになります。 選択するには、[Active Directory 管理者] ページで **[管理者の設定]** を選択します。

    ![選択された SQL マネージド インスタンスの [Active Directory 管理者] ページで [管理者の設定] が強調表示されているスクリーンショット。](./media/authentication-aad-configure/set-admin.png)

7. Azure AD 管理者ページで、ユーザーを検索し、管理者にするユーザーまたはグループを選択してから、 **[選択]** を選択します。

   [Active Directory 管理者] ページには、Active Directory のメンバーとグループがすべて表示されます。 淡色表示されているユーザーまたはグループは、Azure AD 管理者としてサポートされていないため選択できません サポートされている管理者の一覧については、「[Azure AD の機能と制限事項](authentication-aad-overview.md#azure-ad-features-and-limitations)」をご覧ください。 Azure ロール ベースのアクセス制御 (Azure RBAC) は Azure portal にのみ適用され、SQL Database、SQL Managed Instance、または Azure Synapse には反映されません。

    ![Azure Active Directory 管理者を追加する](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. [Active Directory 管理者] ページの上部にある **[保存]** を選択します。

    ![上部で [管理者の設定] と [管理者の削除] のボタンの横に [保存] ボタンがある [Active Directory 管理者] ページのスクリーンショット。](./media/authentication-aad-configure/save.png)

    管理者を変更する処理には数分かかる場合があります。 処理が完了すると、 [Active Directory 管理者] ボックスに新しい管理者が表示されます。

SQL Managed Instance に対する Azure AD 管理者をプロビジョニングした後、<a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> 構文で Azure AD サーバー プリンシパル (ログイン) の作成を始めることができます。 詳細については、[SQL Managed Instance の概要](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration)に関する記事を参照してください。

> [!TIP]
> 後で管理者を削除するには、[Active Directory 管理者] ページの上部にある **[管理者の削除]** を選択し、 **[保存]** を選択します。

### <a name="powershell"></a>PowerShell

PowerShell を使用して SQL Managed Instance に Azure AD の読み取りアクセス許可を付与するには、次のスクリプトを実行します。

```powershell
# Gives Azure Active Directory read permission to a Service Principal representing the SQL Managed Instance.
# Can be executed only by a "Global Administrator" or "Privileged Role Administrator" type of user.

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

# Get service principal for your SQL Managed Instance
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

### <a name="powershell-for-sql-managed-instance"></a>SQL Managed Instance に対する PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell コマンドレットを実行するには、Azure PowerShell をインストールし、実行している必要があります。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/)」をご覧ください。

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) モジュールは Azure SQL Managed Instance によってまだサポートされていますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 AzureRM モジュールのバグ修正は、少なくとも 2020 年 12 月までは引き続き受け取ることができます。  Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 その互換性の詳細については、「[新しい Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)」を参照してください。

Azure AD 管理者をプロビジョニングするには、次のような Azure PowerShell コマンドを実行する必要があります。

- Connect-AzAccount
- Select-AzSubscription

次の表では、SQL Managed Instance に対する Azure AD 管理者のプロビジョニングと管理に使用するコマンドレットの一覧を示します。

| コマンドレット名 | 説明 |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |現在のサブスクリプションで SQL Managed Instance に対する Azure AD 管理者をプロビジョニングします。 (現在のサブスクリプションから実行する必要があります)。|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |現在のサブスクリプションで SQL Managed Instance に対する Azure AD 管理者を削除します。 |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |現在のサブスクリプションでの SQL Managed Instance に対する Azure AD 管理者に関する情報を返します。|

次のコマンドでは、ResourceGroup01 というリソース グループに関連付けられている、ManagedInstance01 という SQL Managed Instance の Azure AD 管理者に関する情報が取得されます。

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

次のコマンドでは、ManagedInstance01 という SQL Managed Instance の、DBAs という Azure AD 管理者グループがプロビジョニングされます。 このサーバーは、リソース グループ ResourceGroup01 に関連付けられています。

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

次のコマンドを実行すると、リソース グループ ResourceGroup01 に関連付けられている、ManagedInstanceName01 という SQL Managed Instance の Azure AD 管理者が削除されます。

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

次の CLI コマンドを呼び出して、SQL Managed Instance に対する Azure AD 管理者をプロビジョニングすることもできます。

| コマンド | 説明 |
| --- | --- |
|[az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | SQL Managed Instance (現在のサブスクリプションのものである必要があります) に対する Azure Active Directory 管理者をプロビジョニングします。 |
|[az sql mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | SQL Managed Instance に対する Azure Active Directory 管理者を削除します。 |
|[az sql mi ad-admin list](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | SQL Managed Instance に対して現在構成されている Azure Active Directory 管理者に関する情報を返します。 |
|[az sql mi ad-admin update](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | SQL Managed Instance に対する Active Directory 管理者を更新します。 |

CLI コマンドの詳細については、「[az sql mi](/cli/azure/sql/mi)」を参照してください。

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>Azure AD 管理者をプロビジョニングする (SQL Database)

> [!IMPORTANT]
> SQL Database または Azure Synapse に対する[サーバー](logical-servers.md)をプロビジョニングする場合にのみ、次の手順に従います。

次の 2 つの手順では、Azure portal と PowerShell を使用し、サーバーに対して Azure Active Directory 管理者をプロビジョニングする方法を示します。

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com/) の右上隅にあるユーザー アイコンをクリックすると、Active Directory 候補の一覧がドロップダウンで表示されます。 既定の Azure AD として適切な Active Directory を選択します。 このステップでは、サブスクリプションに関連付けられている Active Directory をサーバーとリンクすることで、Azure AD とサーバーの両方に同じサブスクリプションが使用されるようにします。

2. **[SQL サーバー]** を探して選択します。

    ![[SQL サーバー] を探して選択する](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > このページでは、 **[SQL Server]** を選択する前に、名前の横にある **星マーク** を選択してそのカテゴリを "*お気に入りに追加*" し、 **[SQL Server]** を左側のナビゲーション バーに追加することができます。

3. **[SQL Server]** ページで、 **[Active Directory 管理者]** を選択します。

4. **[Active Directory 管理者]** ページで、 **[管理者の設定]** を選択します。

    ![[SQL サーバー] の Active Directory 管理者の設定](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. **[管理者の追加]** ページで、ユーザーを検索し、管理者にするユーザーまたはグループを選択してから **[選択]** を選択します。 [Active Directory 管理者] ページには、Active Directory のメンバーとグループがすべて表示されます。 淡色表示されているユーザーまたはグループは、Azure AD 管理者としてサポートされていないため選択できません (「[Azure Active Directory 認証を使用して SQL Database または Azure Synapse を認証する](authentication-aad-overview.md)」の「**Azure AD の機能と制限事項**」セクションでサポートされている管理者の一覧を参照してください)。Azure のロール ベースのアクセス制御 (Azure RBAC) はポータルにのみ適用され、SQL Server には反映されません。

    ![Azure Active Directory 管理者を選択する](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. **[Active Directory 管理者]** ページの上部にある **[保存]** を選択します。

    ![管理者の保存](./media/authentication-aad-configure/save-admin.png)

管理者を変更する処理には数分かかる場合があります。 処理が完了すると、 **[Active Directory 管理者]** ボックスに新しい管理者が表示されます。

   > [!NOTE]
   > Azure AD 管理者を設定するときは、新しい管理者名 (ユーザーまたはグループ) がサーバー認証ユーザーとして仮想 master データベースに既に存在していてはいけません。 存在する場合、Azure AD 管理者のセットアップは失敗します。その作成をロールバックされ、そのような管理者 (名前) が既に存在していることが示されます。 そのようなサーバー認証ユーザーは Azure AD に属していないため、Azure AD 認証を使用してサーバーに接続しようとしても失敗します。

後で管理者を削除するには、 **[Active Directory 管理者]** ページの上部にある **[管理者の削除]** を選択し、 **[保存]** を選択します。

### <a name="powershell-for-sql-database-and-azure-synapse"></a>SQL Database と Azure Synapse 用の PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell コマンドレットを実行するには、Azure PowerShell をインストールし、実行している必要があります。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/)」をご覧ください。 Azure AD 管理者をプロビジョニングするには、次のような Azure PowerShell コマンドを実行する必要があります。

- Connect-AzAccount
- Select-AzSubscription

SQL Database および Azure Synapse に対する Azure AD 管理者のプロビジョニングと管理に使用するコマンドレット:

| コマンドレット名 | 説明 |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |SQL Server または Azure Synapse をホストするサーバーに対する Azure Active Directory 管理者をプロビジョニングします。 (現在のサブスクリプションから実行する必要があります)。 |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |SQL Server または Azure Synapse をホストするサーバーに対する Azure Active Directory 管理者を削除します。|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |SQL Database または Azure Synapse をホストするサーバーに対して現在構成されている Azure Active Directory 管理者に関する情報を返します。 |

これらの各コマンドの情報を確認するには、PowerShell の get-help コマンドを使用します。 たとえば、「 `get-help Set-AzSqlServerActiveDirectoryAdministrator` 」のように入力します。

次のスクリプトでは、**Group-23** という名前のリソース グループ内にあるサーバー **demo_server** に対して、**DBA_Group** という名前の Azure AD 管理者グループ (オブジェクト ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`) をプロビジョニングします。

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** 入力パラメーターには、Azure AD の表示名またはユーザー プリンシパル名を使用できます。 たとえば、``DisplayName="John Smith"`` や ``DisplayName="johns@contoso.com"``す。 Azure AD グループの場合は、Azure AD の表示名のみがサポートされています。

> [!NOTE]
> Azure PowerShell コマンド ```Set-AzSqlServerActiveDirectoryAdministrator``` では、サポートされていないユーザーに対する Azure AD 管理者のプロビジョニングは禁止されていません。 サポートされていないユーザーのプロビジョニングは可能ですが、このようなユーザーはデータベースに接続できません

次の例では、オプションとして **ObjectID** を使用します。

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> **DisplayName** が一意でない場合は、Azure AD の **ObjectID** が必要です。 **ObjectID** と **DisplayName** の値を取得するには、Azure クラシック ポータルの [Active Directory] セクションを使用して、ユーザーまたはグループのプロパティを確認します。

次の例では、サーバーに対する現在の Azure AD 管理者に関する情報が返されます。

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

次の例では、Azure AD 管理者が削除されます。

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

以下の CLI コマンドを呼び出して、Azure AD 管理者をプロビジョニングできます。

| コマンド | 説明 |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | SQL Server または Azure Synapse をホストするサーバーに対する Azure Active Directory 管理者をプロビジョニングします。 (現在のサブスクリプションから実行する必要があります)。 |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | SQL Server または Azure Synapse をホストするサーバーに対する Azure Active Directory 管理者を削除します。 |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | SQL Database または Azure Synapse をホストするサーバーに対して現在構成されている Azure Active Directory 管理者に関する情報を返します。 |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | SQL Server または Azure Synapse をホストするサーバーに対する Active Directory 管理者を更新します。 |

CLI コマンドの詳細については、「[az sql server](/cli/azure/sql/server)」を参照してください。

* * *

> [!NOTE]
> Azure Active Directory 管理者は、REST API を使用してプロビジョニングすることもできます。 詳細については、[Service Management REST API リファレンスと Azure SQL Database の操作](/rest/api/sql/)に関するページを参照してください。

## <a name="configure-your-client-computers"></a>クライアント コンピューターを構成する

Azure AD の ID を使用して SQL Database または Azure Synapse に接続するアプリケーションまたはユーザーが存在するすべてクライアント コンピューターには、次のソフトウェアをインストールする必要があります。

- [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers) の .NET Framework 4.6 以降。
- SQL Server 用 Azure Active Directory 認証ライブラリ (*ADAL.DLL*)。 *ADAL.DLL* ライブラリを含む最新の SSMS、ODBC、OLE DB ドライバーをインストールするためのダウンロード リンクを以下に示します。
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [ODBC Driver 17 for SQL Server](/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15)
  - [OLE DB Driver 18 for SQL Server](/sql/connect/oledb/download-oledb-driver-for-sql-server?view=sql-server-ver15)

これらの要件は、次の操作を行うことで満たすことができます。

- 最新バージョンの [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) または [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) をインストールすると、.NET Framework 4.6 の要件が満たされます。
  - SSMS の場合、x86 バージョンの *ADAL.DLL* がインストールされます。
  - SSDT の場合、amd64 バージョンの *ADAL.DLL* がインストールされます。
  - [Visual Studio の「ダウンロード」](https://www.visualstudio.com/downloads/download-visual-studio-vs)にある最新の Visual Studio をインストールすると、.NET Framework 4.6 の要件は満たされますが、必要な amd64 バージョンの *ADAL.DLL* はインストールされません。

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>Azure AD ID にマップされる包含ユーザーを作成する

SQL Managed Instance では Azure AD サーバー プリンシパル (ログイン) がサポートされているため、包含データベース ユーザーを使用する必要はありません。 Azure AD サーバー プリンシパル (ログイン) を使用すると、Azure AD ユーザー、グループ、またはアプリケーションからログインを作成できます。 つまり、包含データベース ユーザーではなく、Azure AD サーバー ログインを使用して、SQL Managed Instance での認証を行うことができます。 詳細については、[SQL Managed Instance の概要](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration)に関する記事を参照してください。 Azure AD サーバー プリンシパル (ログイン) の作成の構文については、「<a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>」を参照してください。

ただし、SQL Database と Azure Synapse で Azure Active Directory 認証を使用するには、Azure AD ID に基づく包含データベース ユーザーを使用する必要があります。 包含データベース ユーザーは、master データベース内にログインを持たず、データベースに関連付けられている Azure AD の ID にマップされます。 Azure AD の ID には、個々のユーザー アカウントにもグループ アカウントにもなります。 包含データベース ユーザーの詳細については、「 [包含データベース ユーザー - データベースの可搬性を確保する](/sql/relational-databases/security/contained-database-users-making-your-database-portable)」を参照してください。

> [!NOTE]
> Azure Portal を使用して、データベース ユーザー (管理者を除く) を作成することはできません。 Azure ロールは、SQL Database、SQL Managed Instance、または Azure Synapse 内のデータベースには反映されません。 Azure ロールは Azure リソースの管理に使用され、データベースのアクセス許可には適用されません。 たとえば、**SQL Server 共同作成者** ロールでは、SQL Database、SQL Managed Instance、または Azure Synapse 内のデータベースに接続するためのアクセス権は付与されません。 Transact-SQL ステートメントを使用して、アクセス許可をデータベースで直接付与する必要があります。

> [!WARNING]
> T-SQL の `CREATE LOGIN` ステートメントと `CREATE USER` ステートメントのユーザー名に含まれるコロン `:` やアンパサンド `&` などの特殊文字は、サポートされていません。

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
> このコマンドでは、SQL がログイン ユーザーの代わりに Azure AD (「外部プロバイダー」) にアクセスすることが必要です。 場合によっては、Azure AD によって SQL に例外が返されることがあります。 このような場合、ユーザーには Azure AD 固有のエラー メッセージを含む SQL エラー 33134 が表示されます。 このエラーはほとんどの場合、アクセスが拒否されたこと、ユーザーがリソースにアクセスするために MFA に登録する必要があること、あるいはファーストパーティのアプリケーション間のアクセスを事前承認によって処理する必要があることを示します。 最初の 2 つのケースでは、問題は通常、ユーザーの Azure AD テナントで設定されている条件付きアクセス ポリシーによって発生し、ポリシーによってユーザーは外部プロバイダーにアクセスできなくなります。 アプリケーション '00000002-0000-0000-c000-000000000000' (Azure AD Graph API のアプリケーション ID) へのアクセスを許可するように条件付きアクセス ポリシーを更新すると、問題が解決されます。 「ファーストパーティのアプリケーション間のアクセスを事前承認で処理する必要がある」というエラーが発生した場合、この問題は、ユーザーがサービス プリンシパルとしてサインインしていることが原因です。 代わりにユーザーによってコマンドを実行した場合、コマンドは成功するはずです。

> [!TIP]
> 使用している Azure サブスクリプションに関連付けられている Azure Active Directory とは別の Azure Active Directory から、ユーザーを直接作成することはできません。 ただし、別の Active Directory のメンバーのうち、関連付けられている Active Directory にインポート済みのユーザー (外部ユーザーと呼ばれます) は、テナント Active Directory の Active Directory グループに追加できます。 外部の Active Directory のユーザーは、この AD グループの包含データベース ユーザーを作成することで SQL Database にアクセスできるようになります。

Azure Active Directory の ID に基づく包含データベース ユーザーの作成の詳細については、「 [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)」を参照してください。

> [!NOTE]
> サーバーの Azure Active Directory 管理者を削除すると、Azure AD 認証ユーザーはサーバーに接続できなくなります。 必要に応じて、SQL Database 管理者は不要な Azure AD ユーザーを手動で削除できます。

> [!NOTE]
> "**接続がタイムアウトしました**" と表示された場合は、接続文字列の `TransparentNetworkIPResolution` パラメーターを false に設定することが必要になる場合があります。 詳しくは、「[Connection timeout issue with .NET Framework 4.6.1 - TransparentNetworkIPResolution](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution)」(.NET Framework 4.6.1 での接続タイムアウトの問題 - TransparentNetworkIPResolution) をご覧ください。

データベース ユーザーを作成すると、そのユーザーには **CONNECT** アクセス許可が付与され、**PUBLIC** ロールのメンバーとしてそのデータベースに接続できるようになります。 最初にこのユーザーが利用できるアクセス許可は、**PUBLIC** ロールに付与されているアクセス許可か、またはそのユーザーが属している Azure AD グループに付与されているアクセス許可のみです。 Azure AD ベースの包含データベース ユーザーをプロビジョニングすると、他の種類のユーザーにアクセス許可を付与する場合と同様に、そのユーザーに追加のアクセス許可を付与できます。 通常は、データベース ロールにアクセス許可を付与し、そのロールにユーザーを追加します。 詳細については、 [データベース エンジンのアクセス許可の基本知識](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)に関するページを参照してください。 特殊な SQL Database ロールの詳細については、「 [Azure SQL Database におけるデータベースとログインの管理](logins-create-manage.md)」を参照してください。
外部ユーザーとしてマネージド ドメインにインポートされるフェデレーション ドメイン ユーザー アカウントは、マネージド ドメインの ID を使用する必要があります。

> [!NOTE]
> データベースのメタデータでは、Azure AD ユーザーはタイプ E (EXTERNAL_USER) 、グループはタイプ X (EXTERNAL_GROUPS) でマークされます。 詳細については、「[sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql)」を参照してください。

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>SSMS または SSDT を使用してデータベースに接続する  

Azure AD 管理者が正しく設定されていることを確認するには、Azure AD の管理者アカウントを使用して **master** データベースに接続します。
Azure AD ベースの包含データベース ユーザー (データベースを所有しているサーバー管理者以外) をプロビジョニングするには、そのデータベースへのアクセス権を持つ Azure AD の ID を使用してデータベースに接続します。

> [!IMPORTANT]
> Azure Active Directory 認証は、[SQL Server 2016 Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) および Visual Studio 2015 の [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) でサポートされています。 SSMS の 2016 年 8 月のリリースには、Active Directory Universal 認証のサポートも含まれます。これにより、管理者は、電話、テキスト メッセージ、スマート カードと暗証番号 (PIN)、またはモバイル アプリ通知を使用する Multi-Factor Authentication を要求できます。

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Azure AD の ID で SSMS または SSDT を利用して接続する

次の手順では、SQL Server Management Studio または SQL Server Database Tools で Azure AD の ID を使用して SQL Database に接続する方法を示します。

### <a name="active-directory-integrated-authentication"></a>Active Directory 統合認証

フェデレーションされているドメイン、またはパススルーおよびパスワード ハッシュ認証のためのシームレスなシングル サインオン用に構成されたマネージド ドメインからの Azure Active Directory 資格情報を使用して Windows にログインしている場合に、この方法を使用します。 詳しくは、「[Azure Active Directory シームレス シングル サインオン](../../active-directory/hybrid/how-to-connect-sso.md)」をご覧ください。

1. Management Studio または Data Tools を起動し、 **[サーバーへの接続]** (または **[データベース エンジンへの接続]** ) ダイアログ ボックスの **[認証]** ボックスで、 **[Azure Active Directory - 統合]** を選択します。 接続用の既存の資格情報が表示されるため、パスワードは不要であるか、入力できません。

   ![AD 統合認証を選択する][11]

2. **[オプション]** ボタンを選択し、 **[接続プロパティ]** ページの **[データベースへの接続]** ボックスに、接続先となるユーザー データベースの名前を入力します。 SSMS 17.x と 18.x の接続プロパティの違いに関する詳細については、[多要素 Azure AD 認証](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter)に関する記事を参照してください。

   ![データベース名を選択する][13]

### <a name="active-directory-password-authentication"></a>Active Directory パスワード認証

Azure AD のマネージド ドメインを使用して Azure AD のプリンシパル名で接続する場合は、この方法を使用します。 また、リモートで作業する場合など、ドメインにアクセスできないフェデレーション アカウントにも、この方法を使用できます。

Azure AD クラウド専用 ID のユーザーまたは Azure AD ハイブリッド ID を使用するユーザーで、SQL Database または SQL Managed Instance 内のデータベースに対する認証を行うには、この方法を使用します。 この方法では、ユーザーが自分の Windows 資格情報の使用を希望しているものの、そのローカル コンピューターがドメインに参加していない (リモート アクセスを使用しているなど) 場合に対応できます。 このケースでは、Windows ユーザーは、自分のドメイン アカウントとパスワードを示して、SQL Database、SQL Managed Instance、または Azure Synapse 内のデータベースに対する認証を行うことができます。

1. Management Studio または Data Tools を起動し、 **[サーバーへの接続]** (または **[データベース エンジンへの接続]** ) ダイアログ ボックスの **[認証]** ボックスで、 **[Azure Active Directory - パスワード]** を選択します。

2. **[ユーザー名]** ボックスに、Azure Active Directory のご自分のユーザー名を **username\@domain.com** の形式で入力します。 ユーザー名は、Azure Active Directory のアカウントか、Azure Active Directory を利用するマネージドまたはフェデレーション ドメインのアカウントになっている必要があります。

3. **[パスワード]** ボックスに、Azure Active Directory アカウントまたはマネージド/フェデレーション ドメイン アカウントのユーザー パスワードを入力します。

    ![AD パスワード認証を選択する][12]

4. **[オプション]** ボタンを選択し、 **[接続プロパティ]** ページの **[データベースへの接続]** ボックスに、接続先となるユーザー データベースの名前を入力します。 (前のオプションの図を参照してください)。

### <a name="active-directory-interactive-authentication"></a>Active Directory 対話型認証

Multi-Factor Authentication (MFA) の有無にかかわらず、対話形式でパスワードが要求される対話型認証には、この方法を使用します。 この方法を使用すると、Azure AD クラウド専用 ID のユーザーまたは Azure AD ハイブリッド ID を使用するユーザーについて、SQL Database、SQL Managed Instance、および Azure Synapse 内のデータベースに対する認証を行うことができます。

詳細については、[SQL Database と Azure Synapse での多要素 Azure AD 認証の使用 (MFA の SSMS サポート)](authentication-mfa-ssms-overview.md)に関するページを参照してください。

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>クライアント アプリケーションからの Azure AD の ID を使用した接続

次の手順では、クライアント アプリケーションから Azure AD の ID を使用して SQL Database に接続する方法を示します。

### <a name="active-directory-integrated-authentication"></a>Active Directory 統合認証

統合 Windows 認証を使用するには、ドメインの Active Directory が Azure Active Directory にフェデレーションされているか、あるいは、パススルー認証またはパスワード ハッシュ認証のシームレス シングル サインオン用に構成されたマネージド ドメインになっている必要があります。 詳しくは、「[Azure Active Directory シームレス シングル サインオン](../../active-directory/hybrid/how-to-connect-sso.md)」をご覧ください。

> [!NOTE]
> 統合 Windows 認証用の [MSAL.NET (Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) は、パススルーおよびパスワード ハッシュ認証のためのシームレスなシングル サインオンではサポートされていません。

データベースに接続するクライアント アプリケーション (またはサービス) は、ユーザーのドメイン資格情報を使ってドメインに参加しているコンピューター上で実行されている必要があります。

統合認証と Azure AD の ID を使用してデータベースに接続するには、データベース接続文字列内の Authentication キーワードを `Active Directory Integrated` に設定する必要があります。 次の C# のコード サンプルでは、ADO .NET を使用します。

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

接続文字列キーワード `Integrated Security=True` は、Azure SQL Database への接続ではサポートされていません。 ODBC 接続を行うには、スペースを削除して Authentication を 'ActiveDirectoryIntegrated' に設定する必要があります。

### <a name="active-directory-password-authentication"></a>Active Directory パスワード認証

Azure AD クラウド専用 ID のユーザー アカウントまたは Azure AD ハイブリッド ID を使用するユーザーを使ってデータベースに接続するには、Authentication キーワードを `Active Directory Password` に設定する必要があります。 接続文字列にユーザー ID (UID) とパスワード (PWD) のキーワードと値を含める必要があります。 次の C# のコード サンプルでは、ADO .NET を使用します。

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

[GitHub の Azure AD 認証のデモ](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)で入手できるデモ コード サンプルを使用して、Azure AD の認証方法の詳細を確認してください。

## <a name="azure-ad-token"></a>Azure AD トークン

この認証方法を使用すると、中間層サービスで、Azure AD からトークンを取得することにより、SQL Database、SQL Managed Instance、または Azure Synapse 内のデータベースに接続するための [JSON Web トークン (JWT)](../../active-directory/develop/id-tokens.md) を取得することができます。 この方法では、証明書ベースの認証を使用したサービス ID、サービス プリンシパル、およびアプリケーションなど、さまざまなアプリケーション シナリオを実現できます。 Azure AD トークンの認証を使用するには、4 つの基本的な手順を完了する必要があります。

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

詳細については、「 [SQL Server Security Blog (SQL Server のセキュリティに関するブログ)](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth)」をご覧ください。 証明書の追加の詳細については、「[Azure Active Directory の証明書ベースの認証の概要](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)」を参照してください。

### <a name="sqlcmd"></a>sqlcmd

次のステートメントは、sqlcmd のバージョン 13.1 を使用して接続しています。これは、[ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53591)から入手できます。

> [!NOTE]
> `-G` を使用する `sqlcmd` コマンドは、システム ID で動作しないため、ユーザー プリンシパル ログインが必要です。

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Azure AD 認証のトラブルシューティング

Azure AD 認証に関する問題のトラブルシューティングのガイダンスについては、ブログ (<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>) を参照してください

## <a name="next-steps"></a>次のステップ

- SQL Database のログイン、ユーザー、データベース ロール、アクセス許可の概要については、[ログイン、ユーザー、データベース ロール、ユーザー アカウント](logins-create-manage.md)に関するページを参照してください。
- データベース プリンシパルの詳細については、「[プリンシパル](/sql/relational-databases/security/authentication-access/principals-database-engine)」を参照してください。
- データベース ロールの詳細については、[データベース ロール](/sql/relational-databases/security/authentication-access/database-level-roles)に関するページを参照してください。
- SQL Database のファイアウォール規則の詳細については、[SQL Database のファイアウォール規則](firewall-configure.md)に関するページを参照してください。
- Azure AD ゲスト ユーザーを Azure AD 管理者として設定する方法については、「[Azure AD ゲスト ユーザーを作成し、Azure AD 管理者として設定する](authentication-aad-guest-users.md)」を参照してください。
- Azure SQL でプリンシパルにサービスを提供する方法の詳細については、「[Azure AD アプリケーションを使用して Azure AD ユーザーを作成する](authentication-aad-service-principal-tutorial.md)」を参照してください

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png
