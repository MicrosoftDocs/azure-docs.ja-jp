---
title: Azure AD でアプリ同意ポリシーを管理する
description: 組み込みおよびカスタムのアプリ同意ポリシーを管理して、どのような場合に同意を許可できるかを制御する方法について説明します。
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
ms.openlocfilehash: 9c269e2ab37a08e48eedd3ee468080a382f9a8e3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558731"
---
# <a name="manage-app-consent-policies"></a>アプリ同意ポリシーを管理する

Azure AD PowerShell を使用すると、アプリ同意ポリシーを表示および管理できます。

アプリ同意ポリシーは、1 つ以上の "包含" 条件セットと、0 個以上の "除外" 条件セットで構成されます。 イベントがアプリ同意ポリシーにおいて考慮されるには、"*いずれか*" の "除外" 条件セットにではなく、"*少なくとも*" 1 つの "包含" 条件セットに一致している必要があります。

各条件セットは、いくつかの条件で構成されます。 イベントが条件セットに一致するためには、条件セット内の "*すべての*" 条件が満たされる必要があります。

ID が "microsoft-" で始まるアプリ同意ポリシーは、組み込みのポリシーです。 これらの組み込みポリシーの一部は、既存の組み込みディレクトリ ロールで使用されます。 たとえば、`microsoft-application-admin` というアプリ同意ポリシーには、アプリケーション管理者とクラウド アプリケーション管理者のロールが、テナント全体にわたる管理者の同意を許可される条件が記述されています。 組み込みのポリシーは、カスタム ディレクトリ ロール内で、また、ユーザーの同意設定を構成するために使用できますが、編集または削除することはできません。

## <a name="pre-requisites"></a>前提条件

1. [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) モジュールを使用していることを確認します。 この手順は、[AzureAD](/powershell/module/azuread/) モジュールと [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) モジュールの両方がインストールされている場合に重要です。

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Azure AD PowerShell に接続します。

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>既存のアプリ同意ポリシーを一覧表示する

まず、組織内の既存のアプリ同意ポリシーについて理解しておくことをお勧めします。

1. すべてのアプリ同意ポリシーを一覧表示します。

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. ポリシーの "包含" 条件セットを表示します。

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. "除外" 条件セットを表示します。

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>カスタムのアプリ同意ポリシーを作成する

カスタムのアプリ同意ポリシーを作成するには、次の手順に従います。

1. 新しい空のアプリ同意ポリシーを作成します。

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. "包含" 条件セットを追加します。

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   この手順を繰り返して、さらに "包含" 条件セットを追加します。

1. 必要に応じて、"除外" 条件セットを追加します。

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   この手順を繰り返して、さらに "除外" 条件セットを追加します。

アプリ同意ポリシーが作成されたら、このポリシーに従って、[ユーザーの同意を許可する](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy)ことができます。

## <a name="delete-a-custom-app-consent-policy"></a>カスタムのアプリ同意ポリシーを削除する

1. カスタムのアプリ同意ポリシーを削除する方法を次に示します。 **この削除操作は元に戻すことができません。**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> 削除したアプリ同意ポリシーは復元できません。 カスタムのアプリ同意ポリシーを誤って削除した場合は、ポリシーを再作成する必要があります。

---

### <a name="supported-conditions"></a>サポートされている条件

次の表に、アプリ同意ポリシーでサポートされている条件の一覧を示します。

| 条件 | 説明|
|:---------------|:----------|
| PermissionClassification | 付与されるアクセス許可を表す[アクセス許可の分類](configure-permission-classifications.md)。または、任意のアクセス許可の分類 (分類されていないアクセス許可を含む) と一致する "all"。 既定値は "all" です。 |
| PermissionType | 付与されるアクセス許可を表すアクセス許可の種類。 アプリケーションのアクセス許可 (アプリ ロールなど) を表す "application"、または委任されたアクセス許可を表す "delegated" を使用します。 <br><br>**注**:値 "delegatedUserConsentable" は、管理者の同意を要求するように API 公開元によって構成されていない、委任されたアクセス許可を示します。この値は、組み込みのアクセス許可付与ポリシーで使用できますが、カスタムのアクセス許可付与ポリシーでは使用できません。 必須。 |
| ResourceApplication | アクセス許可が付与されるリソース アプリケーション (API など) の **AppId**。または、任意のリソース アプリケーションや API と一致する "any"。 既定値は "any" です。 |
| アクセス許可 | 一致する特定のアクセス許可のアクセス許可 ID の一覧。または、任意のアクセス許可と一致する "all" という単一の値。 既定値は単一の値 "all" です。 <ul><li>委任されたアクセス許可 ID は、API の ServicePrincipal オブジェクトの **OAuth2Permissions** プロパティで確認できます。</li><li>アプリケーションのアクセス許可 ID は、API の ServicePrincipal オブジェクトの **AppRoles** プロパティで確認できます。</li></ol> |
| ClientApplicationIds | 一致するクライアント アプリケーションの **AppId** 値の一覧。または、任意のクライアント アプリケーションと一致する単一の値 "all" を含む一覧。 既定値は単一の値 "all" です。 |
| ClientApplicationTenantIds | クライアント アプリケーションが登録されている Azure Active Directory テナント ID の一覧。または、任意のテナントに登録されているクライアント アプリと一致する単一の値 "all" を含む一覧。 既定値は単一の値 "all" です。 |
| ClientApplicationPublisherIds | クライアント アプリケーションの[確認済みの発行元](../develop/publisher-verification-overview.md)を表す Microsoft Partner Network (MPN) ID の一覧。または、任意の発行元のクライアント アプリと一致する単一の値 "all" を含む一覧。 既定値は単一の値 "all" です。 |
| ClientApplicationsFromVerifiedPublisherOnly | `$true` に設定すると、[確認済みの発行元](../develop/publisher-verification-overview.md)のクライアント アプリケーションでのみ一致します。 `$false` に設定すると、確認済みの発行元がない場合でも、任意のクライアント アプリで一致します。 既定値は `$false`です。 |

## <a name="next-steps"></a>次のステップ

詳細については、以下を参照してください。

* [ユーザーの同意設定を構成する](configure-user-consent.md)
* [管理者の同意ワークフローの構成](configure-admin-consent-workflow.md)
* [アプリケーションへの同意を管理する方法と同意要求を評価する方法](manage-consent-requests.md)
* [アプリケーションへのテナント全体の管理者の同意の付与](grant-admin-consent.md)
* [Microsoft ID プラットフォームでのアクセス許可と同意](../develop/v2-permissions-and-consent.md)

ヘルプを表示したり、質問に対する回答を検索したりするには、以下を参照してください。
* [Microsoft Q&A の Azure AD](/answers/products/)