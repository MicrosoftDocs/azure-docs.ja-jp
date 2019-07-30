---
title: Azure Active Directory でエンタープライズ アプリにユーザーまたはグループを割り当てる | Microsoft Docs
description: Azure Active Directory プレビューでエンタープライズ アプリケーションを選択し、このアプリケーションにユーザーまたはグループを割り当てる方法
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 249dfeeb231c61b05af2e89f0dc02822cc18e627
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702185"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory でエンタープライズ アプリにユーザーまたはグループを割り当てる

ユーザーまたはグループをエンタープライズ アプリに割り当てるには、適切なアクセス許可でエンタープライズ アプリを管理する必要があります。また、ディレクトリの全体管理者である必要があります。 Microsoft のアプリケーション (Office 365 アプリなど) では、PowerShell を使用してエンタープライズ アプリにユーザーを割り当てます。

> [!NOTE]
> この記事で説明されている機能に必要なライセンスについては、[Azure Active Directory の価格のページ](https://azure.microsoft.com/pricing/details/active-directory)を参照してください。

## <a name="assign-a-user-to-an-app---portal"></a>アプリへのユーザーの割り当て - ポータル

1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
1. **[すべてのサービス]** を選択し、テキスト ボックスに「Azure Active Directory」と入力して、**Enter** キーを押します。
1. **[エンタープライズ アプリケーション]** を選択します。
1. **[エンタープライズ アプリケーション - すべてのアプリケーション]** ウィンドウには、管理できるアプリのリストが表示されます。 アプリを選択します。
1. ***[appname]*** ウィンドウ (つまり、選択したアプリの名前がタイトルに含まれるページ) で、 **[ユーザーとグループ]** を選択します。
1. ***[appname*** **- ユーザーとグループ]** ウィンドウで、 **[ユーザーの追加]** を選択します。
1. **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

   ![アプリケーションにユーザーまたはグループを割り当てる](./media/assign-user-or-group-access-portal/assign-users.png)

1. **[ユーザーとグループ]** ウィンドウで、リストから 1 つまたは複数のユーザーまたはグループを選択し、ウィンドウの下部にある **[選択]** ボタンを選びます。
1. **[割り当ての追加]** ウィンドウで、 **[ロール]** を選択します。 次に、 **[ロールを選択する]** ウィンドウで、選択したユーザーまたはグループに適用するロールを選択して、ウィンドウの下部にある **[OK]** を選びます。
1. **[割り当ての追加]** ウィンドウで、ウィンドウの下部にある **[割り当て]** ボタンを選択します。 割り当て対象のユーザーまたはグループに、選択したエンタープライズ アプリケーションのロールにより定義されるアクセス許可が付与されます。

## <a name="allow-all-users-to-access-an-app---portal"></a>すべてのユーザーにアプリへのアクセスを許可する - ポータル

1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
1. **[すべてのサービス]** を選択し、テキスト ボックスに「Azure Active Directory」と入力して、**Enter** キーを押します。
1. **[エンタープライズ アプリケーション]** を選択します。
1. **[エンタープライズ アプリケーション]** ウィンドウで、 **[すべてのアプリケーション]** を選択します。 これにより、管理できるアプリが一覧で表示されます。
1. **[エンタープライズ アプリケーション - すべてのアプリケーション]** ウィンドウで、アプリを選択します。
1. ***[appname]*** ウィンドウで、 **[プロパティ]** を選択します。
1. ***[appname* - プロパティ]** ウィンドウで、 **[ユーザーの割り当てが必要ですか?]** 設定を **[いいえ]** に設定します。

**[ユーザーの割り当てが必要ですか?]** オプション:

- アプリケーションがアプリケーション アクセス パネルに表示されるかどうかには影響しません。 アクセス パネルにアプリケーションを表示するには、適切なユーザーまたはグループをアプリケーションに割り当てる必要があります。
- SAML シングル サインオン用に構成されたクラウド アプリケーションと、アプリケーション プロキシを使用して構成されたオンプレミス アプリケーションでのみ機能します。 [アプリケーションのシングル サインオン](what-is-single-sign-on.md)に関するページを参照してください。
- ユーザーはアプリケーションに同意する必要があります。 管理者はすべてのユーザーについて同意を許可することができます。  [アプリケーションに対するエンドユーザーの同意の構成](configure-user-consent.md)に関するページを参照してください。

## <a name="assign-a-user-to-an-app---powershell"></a>アプリへのユーザーの割り当て - PowerShell

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

グループをエンタープライズ アプリに割り当てるには、`Get-AzureADUser` を `Get-AzureADGroup` に置き換える必要があります。

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

## <a name="next-steps"></a>次の手順

- [自分のグループをすべて表示する](../fundamentals/active-directory-groups-view-azure-portal.md)
- [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](remove-user-or-group-access-portal.md)
- [エンタープライズ アプリケーションのユーザー サインインを無効にする](disable-user-sign-in-portal.md)
- [エンタープライズ アプリケーションの名前またはロゴを変更する](change-name-or-logo-portal.md)
