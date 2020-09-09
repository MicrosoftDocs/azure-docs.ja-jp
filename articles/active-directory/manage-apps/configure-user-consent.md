---
title: Azure AD を使用してエンド ユーザーがアプリケーションに同意する方法を構成する
description: 組織のデータにアクセスするアプリケーションにユーザーがいつどのように同意できるかについて管理する方法を説明します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0c9844d5e3f65dba5e51170367cfd16715a08883
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763467"
---
# <a name="configure-how-end-users-consent-to-applications"></a>エンド ユーザーがアプリケーションに同意する方法を構成する

アプリケーションを Microsoft ID プラットフォームと統合することにより、ユーザーは職場または学校のアカウントを使用してサインインすることや、組織のデータにアクセスして豊富なデータドリブン エクスペリエンスを実現することができます。

アプリケーションが組織のデータにアクセスできるようにするには、そのためのアプリケーションのアクセス許可をユーザーに付与する必要があります。 異なるアクセス許可によって、さまざまなレベルのアクセスが可能になります。 既定では、すべてのユーザーが、管理者の同意を必要としないアクセス許可のアプリケーションに同意することが許可されています。 たとえば、既定では、ユーザーはアプリが自分のメールボックスにアクセスすることを許可することができますが、組織内のすべてのファイルを自由に読み取りと書き込みできるアクセスをアプリに許可することに同意することはできません。

ユーザーがアプリにデータへのアクセスを許可できるようにすることで、ユーザーは便利なアプリケーションを簡単に取得し、生産性を高めることができます。 ただし、状況によっては、この構成が監視および制御されていない場合にリスクとなる可能性があります。

## <a name="user-consent-settings"></a>ユーザーの同意設定

ユーザーがアプリケーションに同意できるケースを制御するには、すべてのユーザーに適用される同意ポリシーを選択します。 次に、3 つの同意ポリシー オプションを示します。

* **ユーザーの同意を無効にする** - ユーザーはアプリケーションにアクセス許可を付与できません。 ユーザーは、以前に同意していたアプリ、または管理者によって代理で同意されているアプリにサインインし続けることができますが、新しいアクセス許可や新しいアプリに独自に同意することは許可されません。 同意を許可するアクセス許可を含むディレクトリ ロールが付与されているユーザーのみが、新しいアクセス許可または新しいアプリに同意できます。

* **ユーザーは、確認済み発行者からのアプリに対し、選択されたアクセス許可にのみ同意できる (プレビュー)** - すべてのユーザーが同意できるのは、[確認済み発行者](../develop/publisher-verification-overview.md) によって発行されたアプリと、テナントに登録されているアプリのみです。 ユーザーは、"低影響" として分類されたアクセス許可に対してのみ同意できます。

  [アクセス許可を分類](#configure-permission-classifications-preview)して、ユーザーが同意を許可できるアクセス許可を選択してください。

* **ユーザーはすべてのアプリに同意できる** - すべてのユーザーが、すべてのアプリケーションに対し、管理者の同意を必要としないすべてのアクセス許可に同意することができます。 

   悪意のあるアプリケーションがユーザーに組織のデータへのアクセス付与を誘導しようとした場合のリスクを軽減するために、[確認済み発行者](../develop/publisher-verification-overview.md)によって発行されたアプリケーションに対してのみユーザーの同意を許可することをお勧めします。

### <a name="configure-user-consent-settings-from-the-azure-portal"></a>Azure portal からユーザーの同意設定を構成する

Azure portal を使用してユーザーの同意設定を構成するには:

1. [Azure portal](https://portal.azure.com) に[グローバル管理者](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)としてサインインします。
1. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  >  **[同意とアクセス許可]**  >  **[ユーザーの同意設定]** を選択します。
1. **[User consent for applications]\(アプリケーションに対するユーザーの同意\)** で、すべてのユーザーに対して構成する同意設定を選択します。
1. **[Save]\(保存\)** を選択して設定を保存します。

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="ユーザーの同意設定":::

> [!TIP]
> ユーザーの同意が許可されていないアプリケーションを管理者がレビューおよび承認するようにユーザーが要求できるようにするには (たとえば、ユーザーの同意が無効になっているとき、またはユーザーに付与することが許可されていないアクセス許可をアプリケーションが要求しているとき)、[管理者の同意ワークフローを構成すること](configure-admin-consent-workflow.md)を検討します。

### <a name="configure-user-consent-settings-using-powershell"></a>PowerShell を使用してユーザーの同意設定を構成する

最新の Azure AD PowerShell プレビュー モジュールである [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) を使用して、アプリケーションに対するユーザーの同意を制御する同意ポリシーを選択できます。

* **ユーザーの同意を無効にする** - ユーザーの同意を無効にするには、ユーザーの同意を制御する同意ポリシーを空に設定します。

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

* **確認済みの発行者からのアプリに対して選択されたアクセス許可を与えることへのユーザーの同意を許可する (プレビュー)** - "低影響" として分類されたアクセス許可についてのみ、確認済みの発行者によって発行されたアプリとテナントに登録されているアプリに限って制限付きでユーザーの同意を許可するには、`microsoft-user-default-low` という名前の組み込み同意ポリシーを構成します。

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-low")
  ```

   [アクセス許可を分類](#configure-permission-classifications-preview)して、ユーザーが同意を許可できるアクセス許可を選択してください。

* **すべてのアプリに対してユーザーの同意を許可する** - すべてのアプリに対してユーザーの同意を許可します。

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-legacy")
  ```

   すべてのユーザーが、すべてのアプリケーションに対し、管理者の同意を必要としないすべてのアクセス許可に同意することができます。 確認済み発行者のアプリに対してのみユーザーの同意を許可することをお勧めします。

## <a name="configure-permission-classifications-preview"></a>アクセス許可の分類を構成する (プレビュー)

アクセス許可の分類を使用すると、組織のポリシーとリスク評価に応じて、さまざまなアクセス許可の影響を特定できます。 たとえば、同意ポリシーでアクセス許可の分類を使用して、ユーザーが同意を許可された一連のアクセス許可を識別できます。

> [!NOTE]
> 現時点では、"低影響" のアクセス許可の分類のみがサポートされています。 管理者の同意を必要としない委任されたアクセス許可のみを "低影響" として分類できます。

### <a name="classify-permissions-using-the-azure-portal"></a>Azure portal を使用してアクセス許可を分類する

1. [Azure portal](https://portal.azure.com) に[グローバル管理者](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)としてサインインします。
1. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  >  **[同意とアクセス許可]**  >  **[Permission classifications]\(アクセス許可の分類\)** を選択します。
1. **[アクセス許可の追加]** を選択して、別のアクセス許可を "低影響" として分類します。 
1. API を選択し、委任されたアクセス許可を選択します。

この例では、シングル サインオンに必要な最小限のアクセス許可セットを分類しました。

:::image type="content" source="media/configure-user-consent/permission-classifications.png" alt-text="アクセス許可の分類":::

> [!TIP]
> Microsoft Graph API の場合、基本的なシングル サインオンを実行するために必要な最低限のアクセス許可は、`openid`、`profile`、`User.Read`、および `offline_access` です。 これらのアクセス許可を使用すると、アプリはサインインしているユーザーのプロファイルの詳細を読み取ることができ、ユーザーがアプリを使用しなくなった場合でもこのアクセスを維持できます。

### <a name="classify-permissions-using-powershell"></a>PowerShell を使用してアクセス許可を分類する

最新の Azure AD PowerShell プレビュー モジュールである [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) を使用して、アクセス許可を分類できます。 アクセス許可の分類は、アクセス許可を発行する API の **ServicePrincipal** オブジェクトに構成されます。

#### <a name="to-read-the-current-permission-classifications-for-an-api"></a>API の現在のアクセス許可の分類を読み取るには、次のようにします。

1. API の **ServicePrincipal** オブジェクトを取得します。 ここでは、Microsoft Graph API の ServicePrincipal オブジェクトを取得します。

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. API の委任されたアクセス許可の分類を読み取ります。

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="to-classify-a-permission-as-low-impact"></a>アクセス許可を "低影響" として分類するには、次のようにします。

1. API の **ServicePrincipal** オブジェクトを取得します。 ここでは、Microsoft Graph API の ServicePrincipal オブジェクトを取得します。

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 分類対象となる委任されたアクセス許可を検索します。

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. アクセス許可の名前と ID を使用して、アクセス許可の分類を設定します。

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="to-remove-a-delegated-permission-classification"></a>委任されたアクセス許可の分類を削除するには、次のようにします。

1. API の **ServicePrincipal** オブジェクトを取得します。 ここでは、Microsoft Graph API の ServicePrincipal オブジェクトを取得します。

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 削除対象となる委任されたアクセス許可の分類を検索します。

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. アクセス許可の分類を削除します。

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>グループ データにアクセスするアプリに対するグループ所有者の同意を構成する

グループ所有者は、アプリケーション (たとえば、サードパーティ ベンダーによって発行されたアプリケーション) が、グループに関連付けられている組織のデータにアクセスすることを承認できます。 たとえば、Microsoft Teams のチーム所有者は、アプリがチーム内のすべての Teams メッセージを読み取ること、またはグループのメンバーの基本プロファイルを一覧表示することを許可できます。

グループ データにアクセスするアプリへの同意を許可するユーザーを構成することも、この機能を無効にすることもできます。

### <a name="configure-group-owner-consent-using-the-azure-portal"></a>Azure portal を使用してグループ所有者の同意を構成する

1. [Azure portal](https://portal.azure.com) に[グローバル管理者](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)としてサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  >  **[同意とアクセス許可]**  >  **[ユーザーの同意設定]** を選択します。
3. **[アプリがデータにアクセスすることへのグループ所有者の同意]** で、有効にするオプションを選択します。
4. **[Save]\(保存\)** を選択して設定を保存します。

この例では、すべてのグループ所有者が、そのグループのデータにアクセスするアプリに同意することが許可されています。

:::image type="content" source="media/configure-user-consent/group-owner-consent.png" alt-text="グループ所有者の同意設定":::

### <a name="configure-group-owner-consent-using-powershell"></a>PowerShell を使用してグループ所有者の同意を構成する

Azure AD PowerShell プレビュー モジュールである [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) を使用して、グループ所有者が所有するグループに関連する組織のデータにアクセスするアプリケーションに対してグループ所有者が同意する機能を有効または無効にできます。

1. [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) モジュールを使用していることを確認します。 この手順は、[AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) モジュールと [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) モジュールの両方がインストールされている場合に重要です。

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Azure AD PowerShell に接続します。

   ```powershell
   Connect-AzureAD
   ```

1. テナントの**同意ポリシー設定**ディレクトリ設定の現在の値を取得します。 このために、この機能のディレクトリ設定が作成されているかどうかを確認する必要があります。作成されていない場合は、対応するディレクトリ設定テンプレートの値を使用します。

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

1. 設定値を理解します。 アプリがグループのデータにアクセスすることを許可できるユーザーを定義する設定値は、次の 2 つです。

    | 設定       | Type         | 説明  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolean | グループ所有者がグループ固有のアクセス許可を付与できるかどうかを示すフラグです。 |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | _EnableGroupSpecificConsent_ が "True" に設定され、この値がグループのオブジェクト ID に設定されている場合、指定されているグループのメンバーは、所有しているグループにグループ固有のアクセス許可を付与する権限を与えられます。 |

1. 必要な構成の設定値を更新します。

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

1. 設定を保存します。

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="configure-risk-based-step-up-consent"></a>リスクに基づくステップアップ同意を構成する

リスクに基づくステップアップ同意を使用すると、[違法な同意要求](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)を行う悪質なアプリへのユーザーの露出を減らすことができます。 危険なエンドユーザー同意要求が Microsoft によって検出されると、管理者の同意への "ステップアップ" が求められます。 この機能は既定で有効になりますが、動作変更が生じるのは、エンドユーザーの同意が有効な場合だけです。

危険な同意要求が検出されると、管理者の承認が必要であることを示すメッセージが同意プロンプトに表示されます。 [管理者の同意要求ワークフロー](configure-admin-consent-workflow.md)が有効になっている場合、ユーザーは同意プロンプトから直接その要求を管理者に送信してさらなる確認を求めることができます。 有効になっていない場合は、次のメッセージが表示されます。

* **AADSTS90094:** &lt;clientAppDisplayName&gt; には、組織内のリソースへのアクセス許可が必要です。このアクセス許可を付与できるのは管理者のみです。 アプリケーションを使用するには、まず管理者に依頼してこのアプリにアクセス許可を付与してください。

この場合、"ApplicationManagement" というカテゴリ、"Consent to application" (アプリケーションへの同意) というアクティビティの種類、"Risky application detected" (危険なアプリケーションの検出) という状態の理由で、監査イベントもログに記録されます。

> [!IMPORTANT]
> 管理者は、特に Microsoft によってリスクが検出された場合に、慎重に[すべての同意要求を評価](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent)したうえで要求を承認する必要があります。

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>PowerShell を使用して、リスクに基づくステップアップ同意を無効にしたり再度有効にしたりする

Microsoft によってリスクが検出された場合に必要となる管理者の同意へのステップアップは、Azure AD PowerShell プレビュー モジュールである [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) を使用して無効にしたり、過去に無効にされていた場合は再度有効にしたりすることができます。

上記の「[PowerShell を使用してグループの所有者の同意を構成する](#configure-group-owner-consent-using-powershell)」と同じ手順で実行できますが、設定する値が異なります。 手順には 3 つの違いがあります。 

1. リスクに基づくステップアップ同意の設定値を理解します。

    | 設定       | Type         | 説明  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolean |  危険な要求が検出されたときにユーザーの同意をブロックするかどうかを示すフラグ |

1. 手順 3. で次の値を使用します。

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
    
1. 手順 5. で次のいずれかを使用します。

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>次のステップ

詳細については、以下を参照してください。

* [管理者の同意ワークフローの構成](configure-admin-consent-workflow.md)
* [アプリケーションへの同意を管理する方法と同意要求を評価する方法](manage-consent-requests.md)
* [アプリケーションへのテナント全体の管理者の同意の付与](grant-admin-consent.md)
* [Microsoft ID プラットフォームでのアクセス許可と同意](../develop/active-directory-v2-scopes.md)

ヘルプを表示したり、質問に対する回答を検索したりするには、以下を参照してください。
* [StackOverflow での Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
