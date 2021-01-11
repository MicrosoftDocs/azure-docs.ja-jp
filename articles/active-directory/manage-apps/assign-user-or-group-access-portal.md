---
title: Azure AD でエンタープライズ アプリケーションにユーザーまたはグループを割り当てる
description: Azure Active Directory プレビューでエンタープライズ アプリケーションを選択し、このアプリケーションにユーザーまたはグループを割り当てる方法
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7875bfc55d4530f7f56a96599491cab4a98ced04
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642029"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory でエンタープライズ アプリにユーザーまたはグループを割り当てる

この記事では、Azure portal 内から、または PowerShell を使用して、Azure Active Directory (Azure AD) 内のエンタープライズ アプリケーションにユーザーまたはグループを割り当てる方法について説明します。 アプリケーションにユーザーを割り当てると、簡単にアクセスできるように、アプリケーションがユーザーの [[マイ アプリ]](https://myapps.microsoft.com/) に表示されます。 アプリケーションでロールが公開されている場合は、ユーザーに特定のロールを割り当てることもできます。

制御を強化するために、[ユーザー割り当てを必要とする](#configure-an-application-to-require-user-assignment)ように、特定の種類のエンタープライズ アプリケーションを構成できます。 

[エンタープライズ アプリにユーザーまたはグループを割り当てる](#assign-users-or-groups-to-an-app-via-the-azure-portal)には、グローバル管理者、アプリケーション管理者、クラウド アプリケーション管理者のいずれかとしてサインインするか、エンタープライズ アプリの所有者として割り当てられている必要があります。

> [!IMPORTANT]
> グループをアプリケーションに割り当てると、そのグループ内のユーザーのみがアクセスできるようになります。 割り当ては、入れ子になったグループにはカスケードされません。

> [!NOTE]
> グループベースの割り当てには、Azure Active Directory Premium P1 または P2 エディションが必要です。 グループ ベースの割り当てがサポートされるのはセキュリティ グループのみです。 入れ子になったグループ メンバーシップと Office 365 グループは、現在サポートされていません。 この記事で説明されている機能に必要なライセンスの詳細については、[Azure Active Directory の価格のページ](https://azure.microsoft.com/pricing/details/active-directory)を参照してください。 

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

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Azure portal を使用してアプリにユーザーまたはグループを割り当てる

1. グローバル管理者、アプリケーション管理者、クラウド アプリケーション管理者のいずれか、またはエンタープライズ アプリの割り当てられた所有者として、[Azure portal](https://portal.azure.com) にサイン インします。
2. **[Azure Active Directory]** を選択します。 左側のナビゲーション メニューで、 **[エンタープライズ アプリケーション]** を選択します。
3. 一覧からアプリケーションを選択します。 アプリケーションが表示されていない場合は、検索ボックスにその名前の入力を開始します。 または、フィルター コントロールを使用して、アプリケーションの種類、状態、または可視性を選択し、 **[適用]** を選択します。
4. 左側のナビゲーション メニューで **[ユーザーとグループ]** を選択します。
   > [!NOTE]
   > Office 365 アプリなどの Microsoft アプリケーションにユーザーを割り当てる場合は、これらのアプリの一部で PowerShell を使用します。 
5. **[ユーザーの追加]** ボタンを選択します。
6. **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。
7. アプリケーションに割り当てるユーザーまたはグループを選択するか、検索ボックスにユーザーまたはグループの名前の入力を開始します。 複数のユーザーとグループを選択でき、選択した項目が **[選択されたアイテム]** に表示されます。
8. 完了したら、 **[選択]** をクリックします。

   ![アプリケーションにユーザーまたはグループを割り当てる](./media/assign-user-or-group-access-portal/assign-users.png)

9. **[ユーザーとグループ]** ウィンドウで、リストから 1 つまたは複数のユーザーまたはグループを選択し、ウィンドウの下部にある **[選択]** ボタンを選びます。
10. アプリケーションでサポートされている場合は、ユーザーまたはグループにロールを割り当てることができます。 **[割り当ての追加]** ウィンドウで、 **[ロールの選択]** を選択します。 次に、 **[ロールの選択]** ウィンドウで、選択したユーザーまたはグループに適用するロールを選択して、ウィンドウの下部にある **[OK]** を選びます。 

    > [!NOTE]
    > アプリケーションでロールの選択がサポートされていない場合は、既定のアクセス ロールが割り当てられます。 この場合、アプリケーションでユーザーのアクセス レベルを管理します。

2. **[割り当ての追加]** ウィンドウで、ウィンドウの下部にある **[割り当て]** ボタンを選択します。

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>PowerShell を使用してアプリにユーザーまたはグループを割り当てる

1. 管理者特権での Windows PowerShell コマンド プロンプトを開きます。

   > [!NOTE]
   > Azure AD モジュールをインストールする必要があります (`Install-Module -Name AzureAD` コマンドを使用してください)。 NuGet モジュールまたは新しい Azure Active Directory V2 PowerShell モジュールをインストールするように求められたら「Y」と入力し、ENTER キーを押します。

1. `Connect-AzureAD` を実行してグローバル管理者ユーザー アカウントでサインインします。
1. 次のスクリプトを使用して、アプリケーションにユーザーとロールを割り当てます。

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

アプリケーション ロールにユーザーを割り当てる方法の詳細については、「[AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)」のドキュメントをご覧ください。

グループをエンタープライズ アプリに割り当てるには、`Get-AzureADUser` を `Get-AzureADGroup` に置き換え、`New-AzureADUserAppRoleAssignment` を `New-AzureADGroupAppRoleAssignment` に置き換える必要があります。

アプリケーション ロールにグループを割り当てる方法の詳細については、「[AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureadgroupapproleassignment?view=azureadps-2.0)」のドキュメントをご覧ください。

### <a name="example"></a>例

この例では PowerShell を使用して、ユーザー Britta Simon を [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) アプリケーションに割り当てます。

1. PowerShell で、変数 $username、$app_name および $app_role_name に対応する値を割り当てます。

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. この例では、Britta Simon に割り当てるアプリケーション ロールの正確な名前はわかりません。 次のコマンドを実行し、ユーザーの UPN とサービス プリンシパル表示名を使用してユーザー ($user) と サービス プリンシパル ($sp) を取得します。

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. `$sp.AppRoles` コマンドを実行して、Workplace Analytics アプリケーションで使用できるロールを表示します。 この例では、Britta Simon にアナリスト (制限付きアクセス) のロールを割り当てます。

   ![Workplace Analytics ロールを使用してユーザーが使用できるロールを表示する](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. `$app_role_name` 変数にロール名を割り当てます。

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. 次のコマンドを実行して、アプリのロールにユーザーを割り当てます。

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="related-articles"></a>関連記事

- [エンドユーザーによるアプリケーションへのアクセスに関する詳細情報](end-user-experiences.md)
- [Azure AD マイ アプリのデプロイを計画する](access-panel-deployment-plan.md)
- [アプリへのアクセスの管理](what-is-access-management.md)
 
## <a name="next-steps"></a>次のステップ

- [自分のグループをすべて表示する](../fundamentals/active-directory-groups-view-azure-portal.md)
- [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](remove-user-or-group-access-portal.md)
- [エンタープライズ アプリケーションのユーザー サインインを無効にする](disable-user-sign-in-portal.md)
- [エンタープライズ アプリケーションの名前またはロゴを変更する](change-name-or-logo-portal.md)
