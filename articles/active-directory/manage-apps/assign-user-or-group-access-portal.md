---
title: Azure Active Directory のアプリに対するユーザー割り当ての管理
description: ID 管理のために Azure Active Directory を使用して、アプリに対してユーザーとグループの割り当ておよび割り当て解除を行う方法について説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: 50dcde478b708cd53d8229d70a9ddf4b1ff271be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259736"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Azure Active Directory のアプリに対するユーザー割り当ての管理

この記事では、Azure portal 内から、または PowerShell を使用して、Azure Active Directory (Azure AD) 内のエンタープライズ アプリケーションにユーザーとグループを割り当てる方法について説明します。 アプリケーションにユーザーを割り当てると、簡単にアクセスできるように、アプリケーションがユーザーの [[マイ アプリ]](https://myapps.microsoft.com/) に表示されます。 アプリケーションでロールが公開されている場合は、ユーザーに特定のロールを割り当てることもできます。

制御を強化するために、[ユーザー割り当てを必要とする](#configure-an-application-to-require-user-assignment)ように、特定の種類のエンタープライズ アプリケーションを構成できます。 

> [!IMPORTANT]
> グループをアプリケーションに割り当てると、そのグループ内のユーザーのみがアクセスできるようになります。 割り当ては、入れ子になったグループにはカスケードされません。

> [!NOTE]
> グループベースの割り当てには、Azure Active Directory Premium P1 または P2 エディションが必要です。 グループ ベースの割り当てがサポートされるのはセキュリティ グループのみです。 入れ子になったグループ メンバーシップと Microsoft 365 グループは、現在サポートされていません。 この記事で説明されている機能に必要なライセンスの詳細については、[Azure Active Directory の価格のページ](https://azure.microsoft.com/pricing/details/active-directory)を参照してください。 

## <a name="configure-an-application-to-require-user-assignment"></a>ユーザー割り当てを要求するようにアプリケーションを構成する

次の種類のアプリケーションでは、アクセス可能になる前にアプリケーションにユーザーを割り当てることを要求できます。

- SAML ベースの認証を使用したフェデレーション シングル サインオン (SSO) 用に構成されたアプリケーション
- Azure Active Directory 事前認証を使用するアプリケーション プロキシのアプリケーション
- ユーザーまたは管理者がそのアプリケーションに同意した後に OAuth 2.0/OpenID Connect 認証を使用する Azure AD アプリケーション プラットフォームに構築されたアプリケーション。

ユーザー割り当てが必要な場合は、アプリケーションに (直接ユーザー割り当てを使用して、またはグループ メンバーシップに基づいて) 明示的に割り当てたユーザーのみがサインインできます。 アプリには、各自の [マイ アプリ] ページで、または直接リンクを使用してアクセスできます。 

このオプションを **[いいえ]** に設定したため、またはアプリケーションで別の SSO モードが使用されているため割り当てが "*不要*" な場合、ユーザーは、アプリケーションへの直接リンクがあるか、アプリケーションの **[プロパティ]** ページに **[ユーザーのアクセス URL]** がある場合にアプリケーションにアクセスできます。 

この設定は、アプリケーションが [マイ アプリ] に表示されるかどうかには影響しません。 アプリケーションにユーザーまたはグループを割り当てると、アプリケーションはユーザーの [マイ アプリ] アクセス パネルに表示されます。 背景については、「[アプリへのアクセスの管理](what-is-access-management.md)」を参照してください。

アプリケーションにユーザーの割り当てを要求するには:
1. 管理者アカウントを使用して、またはアプリケーションの所有者として [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]** を選択します。 左側のナビゲーション メニューで、 **[エンタープライズ アプリケーション]** を選択します。
3. 一覧からアプリケーションを選択します。 アプリケーションが表示されていない場合は、検索ボックスにその名前の入力を開始します。 または、フィルター コントロールを使用して、アプリケーションの種類、状態、または可視性を選択し、 **[適用]** を選択します。
4. 左側のナビゲーション メニューから、 **[プロパティ]** を選択します。
5. **[ユーザーの割り当てが必要ですか?]** トグルが **[はい]** に設定されていることを確認します。
   > [!NOTE]
   > **[ユーザーの割り当てが必要ですか?]** トグルが使用可能でない場合は、PowerShell を利用し、サービス プリンシパルで appRoleAssignmentRequired プロパティを設定できます。
6. 画面上部にある **[保存]** ボタンを選択します。

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Azure portal を使用して、アプリに対してユーザーとグループの割り当ておよび割り当て解除を行う
Azure portal を使用してユーザーまたはグループの割り当てまたは割り当て解除を行う方法については、[アプリケーション管理に関するクイック スタート シリーズ](add-application-portal-assign-users.md)を参照してください。

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Graph API を使用して、アプリに対してユーザーとグループの割り当ておよび割り当て解除を行う
Graph API を使用して、アプリのユーザーとグループを割り当てたり、割り当てを解除したりすることができます。 詳細については、「[アプリの役割の割り当て](/graph/api/resources/approleassignment)」を参照してください。

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>PowerShell を使用してアプリに対してユーザーとグループを割り当てる
1. 管理者特権での Windows PowerShell コマンド プロンプトを開きます。
   > [!NOTE]
   > Azure AD モジュールをインストールする必要があります (`Install-Module -Name AzureAD` コマンドを使用してください)。 NuGet モジュールまたは新しい Azure Active Directory V2 PowerShell モジュールをインストールするように求められたら「Y」と入力し、ENTER キーを押します。
2. `Connect-AzureAD` を実行してグローバル管理者ユーザー アカウントでサインインします。
3. 次のスクリプトを使用して、アプリケーションにユーザーとロールを割り当てます。

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```
アプリケーション ロールにユーザーを割り当てる方法の詳細については、「[AzureADUserAppRoleAssignment](/powershell/module/azuread/new-azureaduserapproleassignment)」のドキュメントをご覧ください。

グループをエンタープライズ アプリに割り当てるには、`Get-AzureADUser` を `Get-AzureADGroup` に置き換え、`New-AzureADUserAppRoleAssignment` を `New-AzureADGroupAppRoleAssignment` に置き換える必要があります。

アプリケーション ロールにグループを割り当てる方法の詳細については、「[AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment)」のドキュメントをご覧ください。

### <a name="example"></a>例

この例では PowerShell を使用して、ユーザー Britta Simon を [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) アプリケーションに割り当てます。

1. PowerShell で、変数 $username、$app_name および $app_role_name に対応する値を割り当てます。

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```
2. この例では、Britta Simon に割り当てるアプリケーション ロールの正確な名前はわかりません。 次のコマンドを実行し、ユーザーの UPN とサービス プリンシパル表示名を使用してユーザー ($user) と サービス プリンシパル ($sp) を取得します。

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
3. `$sp.AppRoles` コマンドを実行して、Workplace Analytics アプリケーションで使用できるロールを表示します。 この例では、Britta Simon にアナリスト (制限付きアクセス) のロールを割り当てます。
   ![Workplace Analytics ロールを使用してユーザーが使用できるロールを表示する](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. `$app_role_name` 変数にロール名を割り当てます。

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```
5. 次のコマンドを実行して、アプリのロールにユーザーを割り当てます。

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>PowerShell を使用して、アプリからユーザーとグループの割り当てを解除する

1. 管理者特権での Windows PowerShell コマンド プロンプトを開きます。
   > [!NOTE]
   > Azure AD モジュールをインストールする必要があります (`Install-Module -Name AzureAD` コマンドを使用してください)。 NuGet モジュールまたは新しい Azure Active Directory V2 PowerShell モジュールをインストールするように求められたら「Y」と入力し、ENTER キーを押します。
2. `Connect-AzureAD` を実行してグローバル管理者ユーザー アカウントでサインインします。
3. 次のスクリプトを使用して、アプリケーションからユーザーとロールを削除します。

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```


## <a name="related-articles"></a>関連記事

- [エンドユーザーによるアプリケーションへのアクセスに関する詳細情報](end-user-experiences.md)
- [Azure AD マイ アプリのデプロイを計画する](my-apps-deployment-plan.md)
- [アプリへのアクセスの管理](what-is-access-management.md)
 
## <a name="next-steps"></a>次のステップ

- [自分のグループをすべて表示する](../fundamentals/active-directory-groups-view-azure-portal.md)
- [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する]()
- [エンタープライズ アプリケーションのユーザー サインインを無効にする](disable-user-sign-in-portal.md)
- [エンタープライズ アプリケーションの名前またはロゴを変更する](./add-application-portal-configure.md)
