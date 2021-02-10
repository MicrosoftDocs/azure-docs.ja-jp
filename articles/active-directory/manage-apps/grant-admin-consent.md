---
title: アプリケーションに対してテナント全体の管理者の同意を付与する - Azure AD
description: エンド ユーザーがアプリケーションにサインインするときに同意を求めるメッセージが表示されないように、テナント全体の同意をアプリケーションに付与する方法について説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66a6317b0cb59d656cdb2e402c5ade1b78ed60aa
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258322"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>アプリケーションに対してテナント全体の管理者の同意を付与する

アプリケーションに対してテナント全体の管理者の同意を付与することで、ユーザー エクスペリエンスを簡略化する方法について説明します。 この記事では、さまざまな方法でこれを実現します。 この方法は、Azure Active Directory (Azure AD) テナントのすべてのエンド ユーザーに適用されます。

アプリケーションに同意する行為の詳細については、「[Azure Active Directory 同意フレームワーク](../develop/consent-framework.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

テナント全体の管理者の同意を付与するには、[グローバル管理者](../roles/permissions-reference.md#global-administrator)、[アプリケーション管理者](../roles/permissions-reference.md#application-administrator)、または[クラウド アプリケーション管理者](../roles/permissions-reference.md#cloud-application-administrator)としてサインインする必要があります。

> [!IMPORTANT]
> アプリケーションにテナント全体の管理者の同意が付与されていると、ユーザーの割り当てが必要であると構成されていない限り、すべてのユーザーがアプリにサインインできるようになります。 アプリケーションにサインインできるユーザーを制限するには、ユーザー割り当てを要求してから、アプリケーションにユーザーまたはグループを割り当てます。 詳細については、[ユーザーとグループの割り当て方法](./assign-user-or-group-access-portal.md)に関するページを参照してください。
>
> Microsoft Graph API にアプリケーション許可のための管理者の同意を与えるには、グローバル管理者ロールが必要です。

> [!WARNING]
> テナント全体の管理者の同意をアプリケーションに付与すると、アプリおよびアプリの発行者に、組織のデータへのアクセスが許可されます。 同意を付与する前に、アプリケーションで要求されているアクセス許可をよく確認してください。
>
> Microsoft Graph API にアプリケーション許可のための管理者の同意を与えるには、グローバル管理者ロールが必要です。

## <a name="grant-admin-consent-from-the-azure-portal"></a>Azure portal から管理者の同意を付与する

### <a name="grant-admin-consent-in-enterprise-apps"></a>エンタープライズ アプリで管理者の同意を付与する

テナントでアプリケーションが既にプロビジョニングされている場合、"*エンタープライズ アプリケーション*" を通じて、テナント全体の管理者の同意を付与できます。 たとえば、少なくとも 1 人のユーザーが既にアプリケーションに同意している場合は、アプリがテナントでプロビジョニングされている可能性があります。 詳細については、「[アプリケーションを Azure AD に追加する方法と理由](../develop/active-directory-how-applications-are-added.md)」を参照してください。

**[エンタープライズ アプリケーション]** に一覧表示されているアプリにテナント全体の管理者の同意を付与するには:

1. [グローバル管理者](../roles/permissions-reference.md#global-administrator)、[アプリケーション管理者](../roles/permissions-reference.md#application-administrator)、または[クラウド アプリケーション管理者](../roles/permissions-reference.md#cloud-application-administrator)として、[Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]** を選択し、 **[エンタープライズ アプリケーション]** を選択します。
3. テナント全体の管理者の同意を付与するアプリケーションを選択します。
4. **[アクセス許可]** を選択し、 **[管理者の同意の付与]** をクリックします。
5. アプリケーションに必要なアクセス許可を慎重に確認します。
6. アプリケーションで必要なアクセス許可に同意する場合は、同意を付与します。 そうでない場合は、 **[キャンセル]** をクリックするか、ウィンドウを閉じます。

> [!WARNING]
> **エンタープライズ アプリ** 経由でテナント全体に管理者の同意を与えると、以前にテナント全体に与えられていたアクセス許可が取り消されます。 以前にユーザーが自分で与えたアクセス許可は影響を受けません。 

### <a name="grant-admin-consent-in-app-registrations"></a>アプリの登録で管理者の同意を付与する

組織で開発したアプリケーション、または Azure AD テナントに直接登録されているアプリケーションの場合は、Azure portal の **[アプリの登録]** から、テナント全体の管理者の同意を付与することもできます。

**[アプリの登録]** からテナント全体の管理者の同意を付与するには:

1. [グローバル管理者](../roles/permissions-reference.md#global-administrator)、[アプリケーション管理者](../roles/permissions-reference.md#application-administrator)、または[クラウド アプリケーション管理者](../roles/permissions-reference.md#cloud-application-administrator)として、[Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]** を選択し、 **[アプリの登録]** を選択します。
3. テナント全体の管理者の同意を付与するアプリケーションを選択します。
4. **[API のアクセス許可]** を選択し、 **[管理者の同意を与える]** をクリックします。
5. アプリケーションに必要なアクセス許可を慎重に確認します。
6. アプリケーションで必要なアクセス許可に同意する場合は、同意を付与します。 そうでない場合は、 **[キャンセル]** をクリックするか、ウィンドウを閉じます。

> [!WARNING]
> **アプリの登録** 経由でテナント全体に管理者の同意を与えると、以前にテナント全体に与えられていたアクセス許可が取り消されます。 以前にユーザーが自分で与えたアクセス許可は影響を受けません。 

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>テナント全体の管理者の同意を付与するための URL を作成する

上記のいずれかの方法を使用してテナント全体の管理者の同意を付与すると、Azure portal でウィンドウが開き、テナント全体の管理者の同意を求めるメッセージが表示されます。 アプリケーションのクライアント ID (アプリケーション ID とも呼ばれます) がわかっている場合は、同じ URL を作成して、テナント全体の管理者の同意を付与することができます。

テナント全体の管理者の同意の URL は、次のような形式です。

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

各値の説明:

* `{client-id}` は、アプリケーションのクライアント ID (アプリ ID とも呼ばれます) です。
* `{tenant-id}` は、組織のテナント ID または検証済みのドメイン名です。

この場合も、同意を付与する前に、アプリケーションで要求されているアクセス許可をよく確認してください。

> [!WARNING]
> この URL 経由でテナント全体に管理者の同意を与えると、以前にテナント全体に与えられていたアクセス許可が取り消されます。 以前にユーザーが自分で与えたアクセス許可は影響を受けません。 

## <a name="next-steps"></a>次のステップ

[Azure Active Directory でエンド ユーザーがアプリケーションに同意する方法を構成する](configure-user-consent.md)

[管理者の同意ワークフローの構成](configure-admin-consent-workflow.md)

[Microsoft ID プラットフォームでのアクセス許可と同意](../develop/v2-permissions-and-consent.md)

[Microsoft Q&A の Azure AD](https://docs.microsoft.com/answers/topics/azure-active-directory.html)