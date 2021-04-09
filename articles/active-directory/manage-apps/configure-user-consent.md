---
title: Azure AD を使用してエンド ユーザーがアプリケーションに同意する方法を構成する
description: 組織のデータにアクセスするアプリケーションにユーザーがいつどのように同意できるかについて管理する方法を説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 68bb846ebb0199691161bc501441df908eb8ad87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643611"
---
# <a name="configure-how-end-users-consent-to-applications"></a>エンド ユーザーがアプリケーションに同意する方法を構成する

アプリケーションを Microsoft ID プラットフォームと統合することにより、ユーザーは職場または学校のアカウントを使用してサインインすることや、組織のデータにアクセスして豊富なデータドリブン エクスペリエンスを実現することができます。

アプリケーションが組織のデータにアクセスできるようにするには、そのためのアプリケーションのアクセス許可をユーザーに付与する必要があります。 異なるアクセス許可によって、さまざまなレベルのアクセスが可能になります。 既定では、すべてのユーザーが、管理者の同意を必要としないアクセス許可のアプリケーションに同意することが許可されています。 たとえば、既定では、ユーザーはアプリが自分のメールボックスにアクセスすることを許可することができますが、組織内のすべてのファイルを自由に読み取りと書き込みできるアクセスをアプリに許可することに同意することはできません。

ユーザーがアプリにデータへのアクセスを許可できるようにすることで、ユーザーは便利なアプリケーションを簡単に取得し、生産性を高めることができます。 ただし、状況によっては、この構成が監視および制御されていない場合にリスクとなる可能性があります。

> [!IMPORTANT]
> 悪意のあるアプリケーションがユーザーに組織のデータへのアクセス付与を誘導しようとした場合のリスクを軽減するために、[確認済み発行者](../develop/publisher-verification-overview.md)によって発行されたアプリケーションに対してのみユーザーの同意を許可することをお勧めします。

## <a name="user-consent-settings"></a>ユーザーの同意設定

アプリの同意ポリシーを使用して、アプリを同意する前に満たす必要がある条件を記述します。 これらのポリシーには、アクセスを要求するアプリの条件、およびアプリが要求しているアクセス許可を含めることができます。

すべてのユーザーに適用するアプリの同意ポリシーを選択することにより、エンドユーザーがアプリに同意することを許可する場合と、管理者の確認と承認を要求する必要がある場合について制限を設けることができます。

* **ユーザーの同意を無効にする** - ユーザーはアプリケーションにアクセス許可を付与できません。 ユーザーは、以前に同意していたアプリ、または管理者によって代理で同意されているアプリにサインインし続けることができますが、新しいアクセス許可や新しいアプリに独自に同意することは許可されません。 同意するアクセス許可を含むディレクトリ ロールが付与されているユーザーのみが、新しいアプリに同意できます。

* **ユーザーは、確認済み発行者または自分の組織からのアプリに対し、選択されたアクセス許可にのみ同意できる** - すべてのユーザーが同意できるのは、[確認済み発行者](../develop/publisher-verification-overview.md) によって発行されたアプリと、テナントに登録されているアプリのみです。 ユーザーは、"低影響" として分類されたアクセス許可に対してのみ同意できます。 ユーザーが同意を許可されるアクセス許可を選択するには、[アクセス許可を分類](configure-permission-classifications.md)する必要があります。

* **ユーザーはすべてのアプリに同意できる** - すべてのユーザーが、すべてのアプリケーションに対し、管理者の同意を必要としないすべてのアクセス許可に同意することができます。

* **カスタム アプリの同意ポリシー** - ユーザーが同意するタイミングを管理する条件に関してさらに多くのオプションが必要な場合は、[カスタム アプリの同意ポリシーを作成](manage-app-consent-policies.md#create-a-custom-app-consent-policy)し、それらをユーザーの同意に適用するように構成することができます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用してユーザーの同意設定を構成するには:

1. [Azure portal](https://portal.azure.com) に[グローバル管理者](../roles/permissions-reference.md#global-administrator)としてサインインします。
1. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  >  **[同意とアクセス許可]**  >  **[ユーザーの同意設定]** を選択します。
1. **[User consent for applications]\(アプリケーションに対するユーザーの同意\)** で、すべてのユーザーに対して構成する同意設定を選択します。
1. **[Save]\(保存\)** を選択して設定を保存します。

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="ユーザーの同意設定":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

最新の Azure AD PowerShell プレビュー モジュールである [AzureADPreview](/powershell/azure/active-directory/install-adv2?preserve-view=true&view=azureadps-2.0-preview) を使用して、アプリケーションに対するユーザーの同意を制御するアプリの同意ポリシーを選択できます。

#### <a name="disable-user-consent"></a>ユーザーの同意を無効にする

ユーザーの同意を無効にするには、ユーザーの同意を制御する同意ポリシーを空に設定します。

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>アプリの同意ポリシーに従ってユーザーの同意を許可する

ユーザーの同意を許可するには、アプリに同意するユーザーの承認を管理するアプリの同意ポリシーを選択します。

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

`{consent-policy-id}` を、適用するポリシーの ID に置き換えます。 作成した[カスタム アプリの同意ポリシー](manage-app-consent-policies.md#create-a-custom-app-consent-policy)を選択することも、次の組み込みポリシーから選択することもできます。

| id | 説明 |
|:---|:------------|
| microsoft-user-default-low | **確認済みの発行元からのアプリに対して選択されたアクセス許可を与えることへのユーザーの同意を許可する**<br /> 確認済みの発行元からのアプリとテナントに登録されているアプリ、および "影響が少ない" として分類したアクセス許可に対してのみ、制限付きのユーザーの同意を許可します (ユーザーが同意できるアクセス許可を選択するには、忘れずに[アクセス許可を分類](configure-permission-classifications.md)してください)。 |
| microsoft-user-default-legacy | **アプリに対するユーザーの同意を許可する**<br /> このオプションを使用すると、すべてのユーザーが、すべてのアプリケーションに対し、管理者の同意を必要としないすべてのアクセス許可に同意することができます。 |
  
たとえば、組み込みのポリシー `microsoft-user-default-low` に従ってユーザーの同意を有効にするには、次のようにします。

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> ユーザーの同意が許可されていないアプリケーションを管理者がレビューおよび承認するようにユーザーが要求できるようにするには (たとえば、ユーザーの同意が無効になっているとき、またはユーザーに付与することが許可されていないアクセス許可をアプリケーションが要求しているとき)、[管理者の同意ワークフローを有効にします](configure-admin-consent-workflow.md)。

## <a name="risk-based-step-up-consent"></a>リスクに基づくステップアップ同意

リスクに基づくステップアップ同意を使用すると、[違法な同意要求](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)を行う悪質なアプリへのユーザーの露出を減らすことができます。 危険なエンドユーザー同意要求が Microsoft によって検出されると、管理者の同意への "ステップアップ" が求められます。 この機能は既定で有効になりますが、動作変更が生じるのは、エンドユーザーの同意が有効な場合だけです。

危険な同意要求が検出されると、管理者の承認が必要であることを示すメッセージが同意プロンプトに表示されます。 [管理者の同意要求ワークフロー](configure-admin-consent-workflow.md)が有効になっている場合、ユーザーは同意プロンプトから直接その要求を管理者に送信してさらなる確認を求めることができます。 有効になっていない場合は、次のメッセージが表示されます。

* **AADSTS90094:** &lt;clientAppDisplayName&gt; には、組織内のリソースへのアクセス許可が必要です。このアクセス許可を付与できるのは管理者のみです。 アプリケーションを使用するには、まず管理者に依頼してこのアプリにアクセス許可を付与してください。

この場合、"ApplicationManagement" というカテゴリ、"Consent to application" (アプリケーションへの同意) というアクティビティの種類、"Risky application detected" (危険なアプリケーションの検出) という状態の理由で、監査イベントもログに記録されます。

> [!IMPORTANT]
> 管理者は、特に Microsoft によってリスクが検出された場合に、慎重に[すべての同意要求を評価](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent)したうえで要求を承認する必要があります。

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>PowerShell を使用して、リスクに基づくステップアップ同意を無効にしたり再度有効にしたりする

Microsoft によってリスクが検出された場合に必要となる管理者の同意へのステップアップは、Azure AD PowerShell プレビュー モジュールである [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) を使用して無効にしたり、過去に無効にされていた場合は再度有効にしたりすることができます。

1. [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) モジュールを使用していることを確認します。 この手順は、[AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) モジュールと [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) モジュールの両方がインストールされている場合に重要です。

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Azure AD PowerShell に接続します。

   ```powershell
   Connect-AzureAD
   ```

1. テナントの **同意ポリシー設定** ディレクトリ設定の現在の値を取得します。 このために、この機能のディレクトリ設定が作成されているかどうかを確認する必要があります。作成されていない場合は、対応するディレクトリ設定テンプレートの値を使用します。

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. 設定の値を理解します。

    | 設定       | Type         | 説明  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolean |  危険な要求が検出されたときにユーザーの同意をブロックするかどうかを示すフラグ |

1. 必要な構成の設定値を更新します。

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
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

## <a name="next-steps"></a>次のステップ

詳細については、以下を参照してください。

* [ユーザーの同意設定を構成する](configure-user-consent.md)
* [アプリの同意ポリシーを管理する](manage-app-consent-policies.md)
* [管理者の同意ワークフローの構成](configure-admin-consent-workflow.md)
* [アプリケーションへの同意を管理する方法と同意要求を評価する方法](manage-consent-requests.md)
* [アプリケーションへのテナント全体の管理者の同意の付与](grant-admin-consent.md)
* [Microsoft ID プラットフォームでのアクセス許可と同意](../develop/v2-permissions-and-consent.md)

ヘルプを表示したり、質問に対する回答を検索したりするには、以下を参照してください。
* [Microsoft Q&A の Azure AD。](/answers/topics/azure-active-directory.html)