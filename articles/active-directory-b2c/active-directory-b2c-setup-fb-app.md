---
title: Facebook アカウントでのサインアップとサインインを設定する - Azure Active Directory B2C
description: Azure Active Directory B2C を使用するアプリケーションで Facebook アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c13f3166c50d9afc03125d66155ce654c84e5e0a
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065233"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Facebook アカウントでのサインアップおよびサインインを設定する

## <a name="create-a-facebook-application"></a>Facebook アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で [ID プロバイダー](active-directory-b2c-reference-oauth-code.md)として Facebook アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Facebook アカウントを持っていない場合は、[https://www.facebook.com/](https://www.facebook.com/) でサインアップできます。

1. Facebook アカウントの資格情報を使用して、[開発者向けの Facebook](https://developers.facebook.com/)にサインインします。
1. まだ登録していない場合は、Facebook 開発者として登録する必要があります。 これを行うには、ページの右上隅にある **[Get Started]\(スタートガイド\)** を選択し、Facebook のポリシーに同意して登録手順を完了します。
1. **[My Apps]\(マイ アプリ\)** 、 **[Add New App]\(新しいアプリの追加\)** の順に選択します。
1. **[表示名]** および有効な **[連絡先の電子メール]** を入力します。
1. **[Create App ID] \(アプリ ID の作成)** をクリックします。 Facebook プラットフォームのポリシーを受け入れ、オンライン セキュリティ チェックを完了する必要があります。
1. **[設定]**  >  **[基本]** を選択します。
1. **カテゴリ**を選択します。たとえば`Business and Pages`。 この値は Facebook では必須ですが、Azure AD B2C では使用されません。
1. ページの下部で、 **[プラットフォームの追加]** 、 **[Web サイト]** の順に選択します。
1. **サイト URL** に、`https://your-tenant-name.b2clogin.com/`を入力して`your-tenant-name`をお使いのテナントの名前に置き換えてください。 **[Privacy Policy URL] (プライバシー ポリシーの URL)** に URL (`http://www.contoso.com` など) を入力します。 ポリシーの URL は、アプリケーションのプライバシーに関する情報を提供するために維持されるページです。
1. **[変更の保存]** を選択します。
1. ページの上部で、 **[App ID] (アプリ ID)** の値をコピーします。
1. **[Show (表示)]** をクリックし、 **[App Secret (アプリ シークレット)]** の値をコピーします。 テナントで ID プロバイダーとして Facebook を構成するには、この両方を使用します。 **[App Secret]** は、重要なセキュリティ資格情報です。
1. **[Products]\(製品\)** を選択し、 **[Facebook Login]\(Facebook ログイン\)** で **[Set up]\(セットアップ\)** を選択します。
1. **[Facebook Login]\(Facebook ログイン\)** の **[Settings]\(設定\)** を選択します。
1. **[有効な OAuth リダイレクト URI]** に「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」を入力します。 `your-tenant-name` をテナントの名前に置き換えます。 ページの下部にある **[Save Changes]** をクリックします。
1. Facebook アプリケーションを Azure AD B2C で使用できるようにするには、ページの右上にある状態セレクターをクリックし、 **[オン]** に設定してアプリケーションを公開し、 **[確認]** をクリックします。  この時点で、状態は**開発**から**ライブ**に変更されます。

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>ID プロバイダーとして Facebook アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[Facebook]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Facebook*」とします。
1. **[クライアント ID]** には、前に作成した Facebook アプリケーションのアプリ ID を入力します。
1. **[クライアント シークレット]** には、記録したアプリ シークレットを入力します。
1. **[保存]** を選択します。
