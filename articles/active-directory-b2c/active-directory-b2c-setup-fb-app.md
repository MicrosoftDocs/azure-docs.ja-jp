---
title: Facebook アカウントでのサインアップとサインインを設定する - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで Facebook アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 56534589c89fefb38f206f1c57da7996ae43e81d
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428501"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Facebook アカウントでのサインアップおよびサインインを設定する

## <a name="create-a-facebook-application"></a>Facebook アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で [ID プロバイダー](active-directory-b2c-reference-oauth-code.md)として Facebook アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Facebook アカウントを持っていない場合は、[https://www.facebook.com/](https://www.facebook.com/) で取得できます。

1. Facebook アカウントの資格情報を使用して、[開発者向けの Facebook](https://developers.facebook.com/)にサインインします。
2. まだ登録していない場合は、Facebook 開発者として登録する必要があります。 これを行うには、ページの右上隅にある **[登録]** を選択し、Facebook のポリシーに同意して登録手順を完了します。
3. **[マイ アプリ]** を選択し、**[新しいアプリの追加]** をクリックします。 
4. **[表示名]** および有効な **[連絡先の電子メール]** を入力します。
5. **[Create App ID] \(アプリ ID の作成)** をクリックします。 Facebook プラットフォームのポリシーを受け入れ、オンライン セキュリティ チェックを完了する必要があります。
6. **[設定]** > **[基本]** を選択します。
7. **カテゴリ**を選択します。たとえば`Business and Pages`。 この値は Facebook では必須ですが、Azure AD B2C では使用されません。
8. ページの下部で、**[プラットフォームの追加]**、**[Web サイト]** の順に選択します。
9. **サイト URL** に、`https://your-tenant-name.b2clogin.com/`を入力して`your-tenant-name`をお使いのテナントの名前に置き換えてください。 **[Privacy Policy URL] (プライバシー ポリシーの URL)** に URL (`http://www.contoso.com` など) を入力します。 ポリシーの URL は、アプリケーションのプライバシーに関する情報を提供するために維持されるページです。
10. **[変更の保存]** を選択します。
11. ページの上部で、**[App ID] (アプリ ID)** の値をコピーします。 
12. **[Show (表示)]** をクリックし、**[App Secret (アプリ シークレット)]** の値をコピーします。 テナントで ID プロバイダーとして Facebook を構成するには、この両方を使用します。 **[App Secret]** は、重要なセキュリティ資格情報です。
13. **[Products] (製品)** を選択し、**[Facebook Login] (Facebook ログイン)** で **[セットアップ]** を選択します。
14. **[Facebook Login] (Facebook ログイン)** の **[設定]** を選択します。
15. **[有効な OAuth リダイレクト URI]** に「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」を入力します。 `your-tenant-name` をテナントの名前に置き換えます。 ページの下部にある **[Save Changes]** をクリックします。
16. Facebook アプリケーションを Azure AD B2C で使用できるようにするには、ページの右上にある状態セレクターをクリックし、**[オン]** に設定してアプリケーションを公開し、**[確認]** をクリックします。  この時点で、状態は**開発**から**ライブ**に変更されます。

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>ID プロバイダーとして Facebook アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。 
3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]**、**[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*Facebook*」と入力します。
6. **[ID プロバイダーの種類]**、**[Facebook]** の順に選択し、**[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップします]** を選択し、**[クライアント ID]** として前に記録したアプリ ID を入力し、前に作成した Facebook アプリケーションの **[クライアント シークレット]** として記録したアプリ シークレットを入力します。
8. **[OK]** をクリックし、**[作成]** をクリックして Facebook の構成を保存します。
