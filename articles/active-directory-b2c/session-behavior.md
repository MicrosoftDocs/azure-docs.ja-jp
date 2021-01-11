---
title: セッションの動作を構成する - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C でセッションの動作を構成します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c47b0249824a3683b8f24cc01cc5eea5ccf32585
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87482670"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でセッションの動作を構成する

Azure Active Directory B2C (Azure AD B2C) で[シングル サインオン (SSO) セッション](session-overview.md)管理を使用すると、管理者は、ユーザーが認証された後にそのユーザーとの対話を制御できます。 たとえば、管理者は、選択した ID プロバイダーを表示するかどうか、アカウントの詳細を再入力する必要があるかどうかを制御できます。 この記事では、Azure AD B2C の SSO 設定を構成する方法について説明します。

## <a name="session-behavior-properties"></a>セッションの動作のプロパティ

次のプロパティを使用して、Web アプリケーション セッションを管理できます。

- **[Web アプリのセッションの有効期間 (分)]** - 認証の成功時にユーザーのブラウザーに格納される Azure AD B2C のセッション Cookie の有効期間。
    - 既定値 = 1,440 分。
    - 最小値 (この値を含む) = 15 分。
    - 最大値 (この値を含む) = 1,440 分。
- **[Web アプリのセッション タイムアウト]** - [セッションの有効期限の種類](session-overview.md#session-expiry-type)、 *[ローリング]* または *[絶対]* 。 
- **[シングル サインオン構成]** - Azure AD B2C テナント内の複数のアプリとユーザー フローにまたがるシングル サインオン (SSO) の動作の[セッション スコープ](session-overview.md#session-scope)。


## <a name="configure-the-properties"></a>プロパティを構成する

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[ユーザー フロー]** を選択します。
5. あらかじめ作成しておいたユーザー フローを開きます。
6. **[プロパティ]** を選択します。
7. **[Web アプリのセッションの有効期間 (分)]** 、 **[Web アプリのセッション タイムアウト]** 、 **[シングル サインオン構成]** 、 **[ログアウト要求に ID トークンが必要]** を必要に応じて構成します。

    ![Azure portal のセッション動作プロパティの設定](./media/session-behavior/session-behavior.png)

8. **[保存]** をクリックします。

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C セッション](session-overview.md)の詳細について学習します。