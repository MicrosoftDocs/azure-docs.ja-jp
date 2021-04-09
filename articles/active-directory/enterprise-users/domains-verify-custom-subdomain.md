---
title: PowerShell と Graph を使用したサブドメイン認証の種類の変更 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory のルート ドメイン設定から継承された既定のサブドメイン認証設定を変更します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 734e6824f13e62ad080500eff18c4892e1f76807
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95503673"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Azure Active Directory でのサブドメイン認証の種類の変更

ルート ドメインが Azure Active Directory (Azure AD) に追加されると、Azure AD 組織内のそのルートに追加された後続のすべてのサブドメインは、ルート ドメインから認証設定を自動的に継承します。 ただし、ルート ドメイン設定とは別にドメイン認証設定を管理する場合は、現在、Microsoft Graph API を使用できます。 たとえば、フェデレーション ルート ドメイン (contoso.com など) がある場合、この記事を参照すると、child.contoso.com などのサブドメインをフェデレーションではなくマネージドとして検証するために役立てることができます。

Azure AD ポータルで、親ドメインがフェデレーションされていて、管理者が **[カスタム ドメイン名]** ページでマネージド サブドメインを検証しようとすると、"1 つ以上のプロパティに無効な値が含まれている" という理由でドメインの追加に失敗したことを示すエラーが表示されます。 Microsoft 365 管理センターからこのサブドメインを追加しようとすると、同様のエラーが表示されます。 このエラーの詳細については、「[子ドメインは、Office 365、Azure、または Intune で親ドメインの変更を継承しない](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes)」を参照してください。

## <a name="how-to-verify-a-custom-subdomain"></a>カスタム サブドメインを検証する方法

サブドメインは既定でルート ドメインの認証の種類を継承するため、Microsoft Graph を使用してサブドメインを Azure AD のルート ドメインに昇格させる必要があります。これにより、認証の種類を目的の種類に設定できます。

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>サブドメインを追加し、その認証の種類を表示する

1. PowerShell を使用して、ルート ドメインの既定の認証の種類を持つ新しいサブドメインを追加します。 Azure AD および Microsoft 365 管理センターでは、この操作はまだサポートされていません。

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net) を使用して、ドメインに GET を実行します。 ドメインはルート ドメインではないため、ルート ドメインの認証の種類を継承します。 コマンドと結果は次のようになります。独自のテナント ID を使用しています。

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>Azure AD Graph Explorer API を使用してこれをルート ドメインにする

次のコマンドを使用して、サブドメインを昇格します。

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>サブドメインの認証の種類を変更する

1. 次のコマンドを使用して、サブドメインの認証の種類を変更します。

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Azure AD Graph Explorer で GET を使って、サブドメイン認証の種類がマネージドになったことを確認します。

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>次のステップ

- [カスタム ドメイン名を追加する](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [ドメイン名を管理する](domains-manage.md)
- [Microsoft Graph API でカスタム ドメイン名を強制削除する](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)