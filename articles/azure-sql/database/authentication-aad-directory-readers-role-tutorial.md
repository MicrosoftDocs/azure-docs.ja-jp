---
title: Azure AD グループにディレクトリ閲覧者ロールを割り当てて、ロールの割り当てを管理する
description: この記事では、Azure AD グループを使用してディレクトリ閲覧者ロールを有効にし、Azure SQL Database、Azure SQL Managed Instance、および Azure Synapse Analytics で Azure AD のロールの割り当てを管理する方法について説明します
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: bc809cf02b827b7498890cb7d929c44bd360ab53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99094711"
---
# <a name="tutorial-assign-directory-readers-role-to-an-azure-ad-group-and-manage-role-assignments"></a>チュートリアル:Azure AD グループにディレクトリ閲覧者ロールを割り当てて、ロールの割り当てを管理する

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> この記事のグループへの **ディレクトリ閲覧者** ロールの割り当ては、**パブリック プレビュー** 段階にあります。 

この記事では、Azure Active Directory (Azure AD) でグループを作成し、そのグループに [**ディレクトリ閲覧者**](../../active-directory/roles/permissions-reference.md#directory-readers)ロールを割り当てる方法について説明します。 ディレクトリ閲覧者アクセス許可により、グループの所有者は、[Azure SQL Database](sql-database-paas-overview.md)、[Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)、および [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) の[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) など、グループにメンバーを追加できます。 これにより、[全体管理者](../../active-directory/roles/permissions-reference.md#global-administrator)または[特権ロール管理者](../../active-directory/roles/permissions-reference.md#privileged-role-administrator)が、テナント内の各 Azure SQL 論理サーバー ID にディレクトリ閲覧者ロールを直接割り当てる必要がなくなります。

このチュートリアルでは、「[クラウド グループを使用して Azure Active Directory でロールの割り当てを管理する (プレビュー)](../../active-directory/roles/groups-concept.md)」で紹介されている機能を使用します。 

ディレクトリ閲覧者ロールを Azure SQL の Azure AD グループに割り当てる利点の詳細については、「[Azure SQL の Azure Active Directory のディレクトリ閲覧者ロール](authentication-aad-directory-readers-role.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure AD インスタンス。 詳細については、「[Azure SQL での Azure AD 認証を構成して管理する](authentication-aad-configure.md)」を参照してください。
- SQL Database、SQL Managed Instance、または Azure Synapse。

## <a name="directory-readers-role-assignment-using-the-azure-portal"></a>Azure portal を使用したディレクトリ閲覧者ロールの割り当て

### <a name="create-a-new-group-and-assign-owners-and-role"></a>新しいグループを作成し、所有者とロールを割り当てる

1. この初期設定には、[全体管理者](../../active-directory/roles/permissions-reference.md#global-administrator)または[特権ロール管理者](../../active-directory/roles/permissions-reference.md#privileged-role-administrator)のアクセス許可を持つユーザーが必要です。
1. 特権ユーザーに [Azure portal](https://portal.azure.com) にサインインしてもらいます。
1. **Azure Active Directory** リソースに移動します。 **[マネージド]** で、 **[グループ]** に移動します。 新しいグループを作成するために、 **[新しいグループ]** を選択します。
1. グループの種類として **[セキュリティ]** を選択し、残りのフィールドに入力します。 **[グループに Azure AD ロールを割り当てることができる (プレビュー)]** 設定が **[はい]** に切り替わっていることを確認します。 次に、Azure AD の **ディレクトリ閲覧者** ロールをグループに割り当てます。
1. 作成されたグループに、Azure AD ユーザーを所有者として割り当てます。 グループの所有者には、Azure AD 管理者ロールが割り当てられていない通常の AD ユーザーを指定できます。 所有者は、SQL Database、SQL Managed Instance、または Azure Synapse を管理しているユーザーである必要があります。

   :::image type="content" source="media/authentication-aad-directory-readers-role/new-group.png" alt-text="aad-new-group":::

1. **[作成]**

### <a name="checking-the-group-that-was-created"></a>作成されたグループの確認

> [!NOTE]
> **[グループの種類]** が **[セキュリティ]** であることを確認します。 *Microsoft 365* グループは、Azure SQL ではサポートされていません。

作成されたグループを確認および管理するには、Azure portal の **[グループ]** ペインに戻り、対象のグループ名を検索します。 グループを選択したら、 **[管理]** 設定の **[所有者]** および **[メンバー]** メニューで追加の所有者とメンバーを追加できます。 また、グループの **割り当てられたロール** を確認することもできます。

:::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-group-created.png" alt-text="[メンバー]、[所有者]、[割り当てられたロール (プレビュー)] の設定メニューを開くリンクが強調表示されている [グループ] ウィンドウのスクリーンショット。":::

### <a name="add-azure-sql-managed-identity-to-the-group"></a>Azure SQL マネージド ID をグループに追加する

> [!NOTE]
> この例では SQL Managed Instance を使用していますが、SQL Database または Azure Synapse に同様の手順を適用して同じ結果を得ることができます。

以降の手順では、全体管理者または特権ロール管理者のユーザーは必要ありません。

1. SQL Managed Instance を管理し、前に作成したグループの所有者であるユーザーとして Azure portal にログインします。

1. Azure portal で、対象の **SQL Managed Instance** リソースの名前を見つけます。

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance.png" alt-text="SQL インスタンス名 ssomitest とサブネット名 ManagedInstance が強調表示されている [SQL マネージド インスタンス] 画面のスクリーンショット。":::

   SQL Managed Instance の作成中に、そのインスタンスの Azure ID が作成されています。 作成された ID は、対象の SQL Managed Instance 名のプレフィックスと同じ名前になります。 次の手順に従って、Azure AD アプリケーションとして作成された SQL Managed Instance ID のサービス プリンシパルを見つけることができます。

    - **Azure Active Directory** リソースに移動します。 **[管理]** 設定で、 **[エンタープライズ アプリケーション]** を選択します。 **オブジェクト ID** が、インスタンスの ID です。
    
    :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance-service-principal.png" alt-text="SQL マネージド インスタンスのオブジェクト ID が強調表示されている、Azure Active Directory リソースの [エンタープライズ アプリケーション] ページのスクリーンショット。":::

1. **Azure Active Directory** リソースに移動します。 **[マネージド]** で、 **[グループ]** に移動します。 自分が作成したグループを選択します。 対象のグループの **[管理]** 設定で、 **[メンバー]** を選択します。 **[メンバーを追加]** を選択し、上記で見つかった名前を検索して、対象の SQL Managed Instance のサービス プリンシパルをグループのメンバーとして追加します。

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-add-managed-instance-service-principal.png" alt-text="SQL マネージド インスタンスを新しいメンバーとして追加するためのオプションが強調表示されている、Azure Active Directory リソースの [メンバー] ページのスクリーンショット。":::

> [!NOTE]
> Azure システムによってサービス プリンシパルのアクセス許可が伝達され、Azure AD Graph API へのアクセスが許可されるまで、数分かかる場合があります。 SQL Managed Instance に対して Azure AD 管理者をプロビジョニングする前に、数分間待つ必要がある場合があります。

### <a name="remarks"></a>注釈

SQL Database と Azure Synapse では、Azure SQL 論理サーバーの作成中または作成後にサーバー ID を作成できます。 SQL Database または Azure Synapse でサーバー ID を作成または設定する方法の詳細については、「[サービス プリンシパルが Azure AD ユーザーを作成できるようにする](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users)」を参照してください。

SQL Managed Instance では、[マネージド インスタンスの Azure AD 管理者を設定する](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)前に、**ディレクトリ閲覧者** ロールをマネージド インスタンス ID に割り当てる必要があります。

論理サーバーの Azure AD 管理者を設定する場合、SQL Database または Azure Synapse では **ディレクトリ閲覧者** ロールをサーバー ID に割り当てる必要はありません。 ただし、Azure AD アプリケーションの代わりに SQL Database または Azure Synapse で Azure AD オブジェクトを作成できるようにするには、**ディレクトリ閲覧者** ロールが必要になります。 このロールが SQL 論理サーバー ID に割り当てられていない場合、Azure SQL で Azure AD ユーザーを作成することはできません。 詳細については、「[Azure SQL での Azure Active Directory のサービス プリンシパル](authentication-aad-service-principal.md)」を参照してください。

## <a name="directory-readers-role-assignment-using-powershell"></a>PowerShell を使用したディレクトリ閲覧者ロールの割り当て

> [!IMPORTANT]
> [全体管理者](../../active-directory/roles/permissions-reference.md#global-administrator)または[特権ロール管理者](../../active-directory/roles/permissions-reference.md#privileged-role-administrator)は、これらの初期手順を実行する必要があります。 PowerShell に加えて、Azure AD には、[Azure AD でロールの割り当てが可能なグループを作成する](../../active-directory/roles/groups-create-eligible.md#using-microsoft-graph-api)ための Microsoft Graph API が用意されています。

1. 次のコマンドを使用して、Azure AD プレビュー PowerShell モジュールをダウンロードします。 場合によっては、管理者として PowerShell を実行する必要があります。

    ```powershell
    Install-Module azureadpreview
    Import-Module azureadpreview
    #To verify that the module is ready to use, use the following command:
    Get-Module azureadpreview
    ```

1. Azure AD テナントに接続します。

    ```powershell
    Connect-AzureAD
    ```

1. **ディレクトリ閲覧者** ロールを割り当てるセキュリティ グループを作成します。

    - `DirectoryReaderGroup`、`Directory Reader Group`、`DirRead` は、好みに応じて変更できます。

    ```powershell
    $group = New-AzureADMSGroup -DisplayName "DirectoryReaderGroup" -Description "Directory Reader Group" -MailEnabled $False -SecurityEnabled $true -MailNickName "DirRead" -IsAssignableToRole $true
    $group
    ```

1. **ディレクトリ閲覧者** ロールをグループに割り当てます。

    ```powershell
    # Displays the Directory Readers role information
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Directory Readers'" 
    $roleDefinition
    ```

    ```powershell
    # Assigns the Directory Readers role to the group
    $roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id
    $roleAssignment
    ```

1. 所有者をグループに割り当てます。

    - `<username>` を、このグループを所有するユーザーに置き換えます。 これらの手順を繰り返すことで、複数の所有者を追加できます。

    ```powershell
    $RefObjectID = Get-AzureADUser -ObjectId "<username>"
    $RefObjectID
    ```

    ```powershell
    $GrOwner = Add-AzureADGroupOwner -ObjectId $group.ID -RefObjectId $RefObjectID.ObjectID
    ```

    次のコマンドを使用して、グループの所有者を確認します。

    ```powershell
    Get-AzureADGroupOwner -ObjectId $group.ID
    ```

    [Azure portal](https://portal.azure.com) でグループの所有者を確認することもできます。 「[作成されたグループの確認](#checking-the-group-that-was-created)」の手順に従います。

### <a name="assigning-the-service-principal-as-a-member-of-the-group"></a>グループのメンバーとしてのサービス プリンシパルの割り当て

以降の手順では、全体管理者または特権ロール管理者のユーザーは必要ありません。

1. Azure SQL リソースも管理するグループの所有者を使用して次のコマンドを実行して、Azure AD に接続します。

    ```powershell
    Connect-AzureAD
    ```

1. 作成されたグループのメンバーとしてサービス プリンシパルを割り当てます。

    - `<ServerName>` は、実際の Azure SQL 論理サーバーまたはマネージド インスタンスの名前に置き換えます。 詳細については、[Azure SQL サービス ID のグループへの追加](#add-azure-sql-managed-identity-to-the-group)に関するセクションを参照してください

    ```powershell
    # Returns the service principal of your Azure SQL resource
    $miIdentity = Get-AzureADServicePrincipal -SearchString "<ServerName>"
    $miIdentity
    ```

    ```powershell
    # Adds the service principal to the group as a member
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId 
    ```

    次のコマンドを実行すると、グループに追加されたことを示すサービス プリンシパルのオブジェクト ID が返されます。

    ```powershell
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId
    ```

## <a name="next-steps"></a>次の手順

- [Azure SQL の Azure Active Directory のディレクトリ閲覧者ロール](authentication-aad-directory-readers-role.md)
- [チュートリアル:Azure AD アプリケーションを使用して Azure AD ユーザーを作成する](authentication-aad-service-principal-tutorial.md)
- [Azure SQL での Azure AD 認証を構成して管理する](authentication-aad-configure.md)