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
ms.openlocfilehash: 646c2216c3d71aa441d33dde0ab3e2ef7bb4fd89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643560"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>アプリケーションに対してテナント全体の管理者の同意を付与する

  アプリケーションにテナント全体の管理者の同意を付与する方法について説明します。 この記事では、さまざまな方法でこれを実現します。

アプリケーションに同意する行為の詳細については、「[Azure Active Directory 同意フレームワーク](../develop/consent-framework.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

テナント全体の管理者の同意を付与するには、組織を代表して同意する権限を持つユーザーとしてサインインする必要があります。 これには、[全体管理者](../roles/permissions-reference.md#global-administrator)と[特権ロール管理者](../roles/permissions-reference.md#privileged-role-administrator)、および一部のアプリケーションでは、[アプリケーション管理者](../roles/permissions-reference.md#application-administrator)と[クラウド アプリケーション管理者](../roles/permissions-reference.md#cloud-application-administrator)が含まれます。 [アプリケーションにアクセス許可を付与するアクセス許可](../roles/custom-consent-permissions.md)を含む[カスタム ディレクトリ ロール](../roles/custom-create.md)が割り当てられているユーザーも、テナント全体の同意を付与することが許可されます。

> [!WARNING]
> テナント全体の管理者の同意をアプリケーションに付与すると、アプリおよびアプリの発行者に、組織のデータへのアクセスが許可されます。 同意を付与する前に、アプリケーションで要求されているアクセス許可をよく確認してください。

> [!IMPORTANT]
> アプリケーションにテナント全体の管理者の同意が付与されていると、ユーザーの割り当てが必要であると構成されていない限り、すべてのユーザーがアプリにサインインできるようになります。 アプリケーションにサインインできるユーザーを制限するには、ユーザー割り当てを要求してから、アプリケーションにユーザーまたはグループを割り当てます。 詳細については、[ユーザーとグループの割り当て方法](./assign-user-or-group-access-portal.md)に関するページを参照してください。

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

[Microsoft Q&A の Azure AD](/answers/topics/azure-active-directory.html)
