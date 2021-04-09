---
title: カスタム Azure AD アプリのトークン有効期間の既定値を変更する
description: Azure AD で開発しているアプリケーションのトークンの有効期間ポリシーを更新する方法
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: d39f378171443f028ef6b549b120b22f2a3405c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582943"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>独自に開発したアプリケーションのトークン有効期間の既定値を変更する方法

この記事では、Azure AD PowerShell を使用して、アクセス トークンの有効期間ポリシーを設定する方法を示します。 Azure AD Premium では、非機密クライアントに対して発行されたトークンの有効期間をアプリ開発者とテナント管理者が構成できます。 トークンの有効期間ポリシーは、テナント全体またはアクセスされるリソースに対して設定されます。

> [!IMPORTANT]
> 2020 年 5 月以降、テナントで更新とセッション トークンの有効期間を構成できなくなります。  2021 年 1 月 30 日以降は、Azure Active Directory でポリシー内の既存の更新とセッション トークンの構成が考慮されなくなります。 非推奨となった後も、アクセス トークンの有効期間を構成することはできます。 詳細については、[Azure AD の構成可能なトークン有効期間](./active-directory-configurable-token-lifetimes.md)に関する記事を参照してください。
> Azure AD の条件付きアクセスに [認証セッションの管理機能](../conditional-access/howto-conditional-access-session-lifetime.md) を実装しました。 この新機能を使用し、サインインの頻度を設定して更新トークンの有効期間を構成できます。  

アクセス トークンの有効期間ポリシーを設定するには、[Azure AD PowerShell モジュール](https://www.powershellgallery.com/packages/AzureADPreview)をダウンロードします。
**Connect-AzureAD -Confirm** コマンドを実行します。

ここでは、ユーザーが、Web アプリで頻繁に認証を必要とするポリシーの例を示します。 このポリシーにより、Web アプリのサービス プリンシパルへのアクセスの有効期間が設定されます。 ポリシーを作成し、これをサービス プリンシパルに割り当てます。 サービス プリンシパルの ObjectId も取得する必要があります。

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>次のステップ

* 所属組織内のすべてのアプリ、マルチテナント アプリ、または所属組織内の特定のサービス プリンシパルに対するトークンの有効期間を設定する方法を含む、Azure AD によって発行されたトークンの有効期間を構成する方法については、[Azure AD で構成可能なトークンの有効期間](./active-directory-configurable-token-lifetimes.md)に関するページを参照してください。 
* [Azure AD のトークン リファレンス](./id-tokens.md)
