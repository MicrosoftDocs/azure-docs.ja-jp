---
title: Facebook アカウントでのサインアップおよびサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用するアプリケーションで Facebook アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 7e7a99daa169c994a0b9656786926f0715fa17a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580064"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Facebook アカウントでのサインアップおよびサインインを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>Facebook アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) でユーザーが Facebook アカウントを使用してサインインできるようにするには、[Facebook アプリのダッシュボード](https://developers.facebook.com/)でアプリケーションを作成する必要があります。 詳細については、[アプリ開発](https://developers.facebook.com/docs/development)のページを参照してください。 まだ Facebook アカウントを持っていない場合は、[https://www.facebook.com/](https://www.facebook.com/) でサインアップできます。

1. Facebook アカウントの資格情報を使用して、[開発者向けの Facebook](https://developers.facebook.com/)にサインインします。
1. まだ登録していない場合は、Facebook 開発者として登録する必要があります。 これを行うには、ページの右上隅にある **[Get Started]\(スタートガイド\)** を選択し、Facebook のポリシーに同意して登録手順を完了します。
1. **[マイ アプリ]** を選択し、 **[アプリの作成]** を選択します。
1. **[結合されたエクスペリエンスのビルド]** を選択します。
1. **[表示名]** および有効な **[連絡先の電子メール]** を入力します。
1. **[Create App ID]\(アプリ ID の作成\)** を選択します。 Facebook プラットフォームのポリシーを受け入れ、オンライン セキュリティ チェックを完了する必要があります。
1. **[設定]**  >  **[基本]** を選択します。
    1. **カテゴリ** を選択します。たとえば`Business and Pages`。 この値は Facebook では必須ですが、Azure AD B2C では使用されません。
    1. **[サービス利用規約 URL]** に URL を入力します (例: `http://www.contoso.com/tos`)。 ポリシーの URL は、アプリケーションの利用規約を提供するために維持されるページです。
    1. **[Privacy Policy URL] (プライバシー ポリシーの URL)** に URL (`http://www.contoso.com/privacy` など) を入力します。 ポリシーの URL は、アプリケーションのプライバシーに関する情報を提供するために維持されるページです。
1. ページの下部で、 **[プラットフォームの追加]** 、 **[Web サイト]** の順に選択します。
1. **[サイトの URL]** に、Web サイトのアドレス (たとえば `https://contoso.com`) を入力します。 
1. **[変更の保存]** を選択します。
1. ページの上部で、 **[App ID] (アプリ ID)** の値をコピーします。
1. **[Show (表示)]** を選択し、 **[App Secret (アプリ シークレット)]** の値をコピーします。 テナントで ID プロバイダーとして Facebook を構成するには、この両方を使用します。 **[App Secret]** は、重要なセキュリティ資格情報です。
1. メニューから、 **[製品]** の横にある **プラス記号** を選択します。 **[アプリに製品を追加する]** で、 **[Facebook ログイン]** の下にある **[設定]** を選択します。
1. メニューから、 **[Facebook ログイン]** 、 **[設定]** の順に選択します。
1. **[有効な OAuth リダイレクト URI]** に「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」を入力します。 [カスタム ドメイン](custom-domain.md)を使用する場合は、「`https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-tenant-name` を実際のテナントの名前に、`your-domain-name` を実際のカスタム ドメインに置き換えます。 
1. ページの下部にある **[Save Changes]\(変更の保存\)** を選択します。
1. Facebook アプリケーションを Azure AD B2C で使用できるようにするには、ページの右上にある状態セレクターを選択し、 **[オン]** に設定してアプリケーションを公開し、 **[スイッチ モード]** を選択します。  この時点で、状態は **開発** から **ライブ** に変更されます。

::: zone pivot="b2c-user-flow"

## <a name="configure-facebook-as-an-identity-provider"></a>Facebook を ID プロバイダーとして構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[Facebook]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Facebook*」とします。
1. **[クライアント ID]** には、前に作成した Facebook アプリケーションのアプリ ID を入力します。
1. **[クライアント シークレット]** には、記録したアプリ シークレットを入力します。
1. **[保存]** を選択します。

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>ユーザー フローに Facebook ID プロバイダーを追加する 

この時点では、Facebook ID プロバイダーは設定されていますが、サインイン ページではまだ使用できません。 Facebook ID プロバイダーをユーザー フローに追加するには、次の手順を実行します。

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. Facebook ID プロバイダーを追加するユーザー フローをクリックします。
1. **[ソーシャル ID プロバイダー]** から、 **[Facebook]** を選択します。
1. **[保存]** を選択します。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Facebook]** を選択して、Facebook アカウントでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

Azure AD B2C テナントで前に記録したアプリ シークレットを格納する必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用のテナントが含まれるディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. [概要] ページで、 **[Identity Experience Framework]** を選択します。
5. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
6. **オプション** については、`Manual`を選択します。
7. ポリシー キーの **名前** を入力します。 たとえば、「 `FacebookSecret` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
8. **[シークレット]** に、前に記録したアプリ シークレットを入力します。
9. **[キー使用法]** として [`Signature`] を選択します。
10. **Create** をクリックしてください。

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>ID プロバイダーとして Facebook アカウントを構成する

1. `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** ファイルで、`client_id` の値を Facebook アプリケーション ID に置き換えます。

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

## <a name="upload-and-test-the-policy"></a>ポリシーをアップロードしてテストします。

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. *TrustFrameworkExtensions.xml* ファイルをテナントにアップロードします。
1. **[カスタム ポリシー]** ページで、**B2C_1A_signup_signin** を選択します。
1. **[アプリケーションの選択]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Facebook]** を選択して、Facebook アカウントでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

::: zone-end

## <a name="next-steps"></a>次のステップ

[Facebook トークンをアプリケーションに渡す](idp-pass-through-user-flow.md)方法について説明します。
