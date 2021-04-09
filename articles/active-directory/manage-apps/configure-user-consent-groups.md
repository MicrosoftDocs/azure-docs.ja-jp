---
title: Azure AD を使用し、グループ データにアクセスするアプリに対するグループ所有者の同意を構成する
description: グループまたはチームのデータにアクセスするアプリケーションにグループ所有者とチーム所有者が同意できるかどうかを管理する方法について説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 8d8604a1dd54ed819bb9e27c46d61a46466bf3da
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548803"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>グループ データにアクセスするアプリに対するグループ所有者の同意を構成する

グループ所有者とチーム所有者は、アプリケーション (たとえば、サードパーティ ベンダーによって発行されたアプリケーション) が、グループに関連付けられている組織のデータにアクセスすることを承認できます。 たとえば、Microsoft Teams のチーム所有者は、アプリがチーム内のすべての Teams メッセージを読み取ること、またはグループのメンバーの基本プロファイルを一覧表示することを許可できます。 詳細については、「[Microsoft Teams でのリソース固有の同意](/microsoftteams/resource-specific-consent)」を参照してください。

## <a name="manage-group-owner-consent-to-apps"></a>アプリに対するグループ所有者の同意を管理する

グループ データまたはチーム データにアクセスするアプリへの同意を許可するユーザーを構成することも、すべてのユーザーを対象にこれを無効にすることもできます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

次の手順でグループ データにアクセスするアプリに対するグループ所有者の同意を管理します。

1. [Azure portal](https://portal.azure.com) に[グローバル管理者](../roles/permissions-reference.md#global-administrator)としてサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  >  **[同意とアクセス許可]**  >  **[ユーザーの同意設定]** を選択します。
3. **[アプリがデータにアクセスすることへのグループ所有者の同意]** で、有効にするオプションを選択します。
4. **[Save]\(保存\)** を選択して設定を保存します。

この例では、すべてのグループ所有者が、そのグループのデータにアクセスするアプリに同意することが許可されています。

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="グループ所有者の同意設定":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure AD PowerShell プレビュー モジュールである [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) を使用して、グループ所有者が所有するグループに関連する組織のデータにアクセスするアプリケーションに対してグループ所有者が同意する機能を有効または無効にできます。

1. [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) モジュールを使用していることを確認します。 この手順は、[AzureAD](/powershell/module/azuread/) モジュールと [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) モジュールの両方がインストールされている場合に重要です。

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

---

## <a name="next-steps"></a>次のステップ

詳細については、以下を参照してください。

* [ユーザーの同意設定を構成する](configure-user-consent.md)
* [管理者の同意ワークフローの構成](configure-admin-consent-workflow.md)
* [アプリケーションへの同意を管理する方法と同意要求を評価する方法](manage-consent-requests.md)
* [アプリケーションへのテナント全体の管理者の同意の付与](grant-admin-consent.md)
* [Microsoft ID プラットフォームでのアクセス許可と同意](../develop/v2-permissions-and-consent.md)

ヘルプを表示したり、質問に対する回答を検索したりするには、以下を参照してください。
* [Microsoft Q&A の Azure AD](/answers/topics/azure-active-directory.html)