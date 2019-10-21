---
title: Microsoft アカウントでのサインアップおよびサインインを設定する - Azure Active Directory B2C
description: Azure Active Directory B2C を使用するアプリケーションで Microsoft アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 134ca92671a41eb53201101a3622215466a60c09
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065093"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Microsoft アカウントでのサインアップおよびサインインを設定する

## <a name="create-a-microsoft-account-application"></a>Microsoft アカウント アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で [ID プロバイダー](active-directory-b2c-reference-oidc.md)として Microsoft アカウントを使用するには、Azure AD テナントにアプリケーションを作成する必要があります。 Azure AD テナントは、Azure AD B2C テナントと同じものではありません。 まだ Microsoft アカウントを持っていない場合は、[https://www.live.com/](https://www.live.com/) で取得できます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. ご利用の Azure AD テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターをクリックして、ご利用の Azure AD テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
1. **[新規登録]** を選択します。
1. アプリケーションの **[名前]** を入力します。 たとえば、 *MSAapp1* です。
1. **[サポートされているアカウントの種類]** で、 **[任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント (Skype、Xbox、Outlook.com など)]** を選択します。 このオプションは、最も広範な Microsoft ID を対象にします。

   アカウントの種類のその他の選択肢の詳細については、「[クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する](../active-directory/develop/quickstart-register-app.md)」を参照してください。
1. **[Redirect URI (optional)]\(リダイレクト URI (省略可能)\)** で、 **[Web]** を選択してテキスト ボックスに「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-tenant-name` を Azure AD B2C テナント名に置き換えます。
1. **[登録]** を選択します
1. アプリケーションの [概要] ページに表示されている **[Application (client) ID]\(アプリケーション (クライアント) ID\)** を記録します。 これは、次のセクションで ID プロバイダーを構成するときに必要です。
1. **[証明書とシークレット]** を選択します
1. **[新しいクライアント シークレット]** をクリックします
1. シークレットの **[説明]** を「*アプリケーション パスワード 1*」のように入力して、 **[追加]** をクリックします。
1. **[値]** 列に表示されているアプリケーション パスワードを記録します。 これは、次のセクションで ID プロバイダーを構成するときに必要です。

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>ID プロバイダーとして Microsoft account アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[Microsoft アカウント]** を選択します。
1. **[名前]** を入力します。 たとえば、「*MSA*」とします。
1. **[クライアント ID]** には、前に作成した Azure AD アプリケーションのアプリ (クライアント) ID を入力します。
1. **[クライアント シークレット]** には、記録したクライアント シークレットを入力します。
1. **[保存]** を選択します。
