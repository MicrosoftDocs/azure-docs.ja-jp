---
title: Azure Active Directory B2C を使用して Weibo アカウントでのサインアップおよびサインインを設定する
description: Azure Active Directory B2C を使用するアプリケーションで Weibo アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4a019c3f197f123486dd28dc2bdb46e34f6cabc4
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064914"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Weibo アカウントでのサインアップおよびサインインを設定する

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Weibo アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で ID プロバイダーとして Weibo アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Weibo アカウントを持っていない場合は、[https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us) でサインアップできます。

1. Weibo アカウントの資格情報を使用して[Weibo 開発者ポータル](https://open.weibo.com/)にサインインします。
1. サインインしたら、右上隅にある表示名を選択します。
1. ドロップダウンで **[编辑开发者信息 (開発者情報の編集)]** を選択します。
1. 必要な情報を入力し、 **[提交 (送信)]** を選択します。
1. 電子メールによる確認プロセスを完了します。
1. [ID の確認ページ](https://open.weibo.com/developers/identity/edit)に移動します。
1. 必要な情報を入力し、 **[提交 (送信)]** を選択します。

### <a name="register-a-weibo-application"></a>Weibo アプリケーションを登録する

1. [新しい Weibo アプリの登録ページ](https://open.weibo.com/apps/new)に移動します。
1. 必要なアプリケーション情報を入力します。
1. **[创建 (作成)]** を選択します。
1. **アプリ キー**と**アプリケーション シークレット**の値をコピーします。 ID プロバイダーをテナントに追加するには、これらの両方が必要です。
1. 必要な写真をアップロードし、必要な情報を入力します。
1. **[保存以上信息 (保存)]** を選択します。
1. **[高级信息 (詳細情報)]** を選択します。
1. OAuth2.0 の **[授权设置 (リダイレクト URL)]** フィールドの横の **[编辑 (編集)]** を選択します。
1. OAuth2.0 の **[授权设置 (リダイレクト URL)]** に、「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 たとえば、テナント名が contoso の場合、URL を`https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`に設定します。
1. **[提交 (送信)]** を選択します。

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>ID プロバイダーとして Weibo アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[Weibo (プレビュー)]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Weibo*」とします。
1. **[クライアント ID]** には、前に作成した Weibo アプリケーションのアプリ キーを入力します。
1. **[クライアント シークレット]** には、記録したアプリ シークレットを入力します。
1. **[保存]** を選択します。
