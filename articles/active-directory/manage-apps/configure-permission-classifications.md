---
title: Azure AD を使用してアクセス許可の分類を構成する
description: 委任されたアクセス許可の分類を管理する方法について説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: phsignor
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 12a4ffb311e01ebb78b1ae392d1243c5d67eff6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644566"
---
# <a name="configure-permission-classifications"></a>アクセス許可の分類を構成する

アクセス許可の分類を使用すると、組織のポリシーとリスク評価に応じて、さまざまなアクセス許可の影響を特定できます。 たとえば、同意ポリシーでアクセス許可の分類を使用して、ユーザーが同意を許可された一連のアクセス許可を識別できます。

## <a name="manage-permission-classifications"></a>アクセス許可の分類を管理する

現時点では、"低影響" のアクセス許可の分類のみがサポートされています。 管理者の同意を必要としない委任されたアクセス許可のみを "低影響" として分類できます。

> [!TIP]
> 基本サインインに必要な最小アクセス許可は `openid`、`profile`、`email`、`User.Read`、`offline_access` です。これらはすべて、Microsoft Graph の委任さまたアクセス許可です。 これらのアクセス許可を使用すると、アプリはサインインしているユーザーの完全プロファイルの詳細を読み取ることができ、ユーザーがアプリを使用しなくなった場合でもこのアクセスを維持できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

次の手順で Azure portal を使用し、アクセス許可を分類します。

1. [グローバル管理者](../roles/permissions-reference.md#global-administrator)、[アプリケーション管理者](../roles/permissions-reference.md#application-administrator)、または[クラウド アプリケーション管理者](../roles/permissions-reference.md#cloud-application-administrator)として、[Azure portal](https://portal.azure.com) にサインインします
1. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  >  **[同意とアクセス許可]**  >  **[Permission classifications]\(アクセス許可の分類\)** を選択します。
1. **[アクセス許可の追加]** を選択して、別のアクセス許可を "低影響" として分類します。
1. API を選択し、委任されたアクセス許可を選択します。

この例では、シングル サインオンに必要な最小限のアクセス許可セットを分類しました。

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="アクセス許可の分類":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

最新の Azure AD PowerShell プレビュー モジュールである [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) を使用して、アクセス許可を分類できます。 アクセス許可の分類は、アクセス許可を発行する API の **ServicePrincipal** オブジェクトに構成されます。

#### <a name="list-the-current-permission-classifications-for-an-api"></a>API の現在のアクセス許可の分類をリストアップします。

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

#### <a name="classify-a-permission-as-low-impact"></a>アクセス許可を "低影響" として分類する

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

#### <a name="remove-a-delegated-permission-classification"></a>委任されたアクセス許可の分類を削除する

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