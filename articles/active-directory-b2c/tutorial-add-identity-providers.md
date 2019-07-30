---
title: チュートリアル - アプリケーションに ID プロバイダーを追加する - Azure Active Directory B2C
description: Azure portal を使用して Azure Active Directory B2C 内のアプリケーションに ID プロバイダーを追加する方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 33f595dd36ac9448cc1276647f9943326b0b74c1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655218"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C でアプリケーションに ID プロバイダーを追加する

アプリケーションでは、ユーザーが異なる ID プロバイダーでサインインできるようにすることができます。 "*ID プロバイダー*" では、ID 情報の作成、保守、管理、およびアプリケーションへの認証サービスの提供が行われます。 Azure portal を使用して、Azure Active Directory (Azure AD) B2C によってサポートされる ID プロバイダーを、[ユーザー フロー](active-directory-b2c-reference-policies.md)に追加することができます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * ID プロバイダー アプリケーションを作成する
> * ID プロバイダーをテナントに追加する
> * ID プロバイダーをユーザー フローに追加する

通常、アプリケーションでは ID プロバイダーを 1 つだけ使用しますが、さらに追加することもできます。 このチュートリアルでは、Azure AD の ID プロバイダーと Facebook の ID プロバイダーをアプリケーションに追加する方法を示します。 これらの ID プロバイダーを両方ともアプリケーションに追加することはオプションです。 [Amazon](active-directory-b2c-setup-amzn-app.md)、[GitHub](active-directory-b2c-setup-github-app.md)、[Google](active-directory-b2c-setup-goog-app.md)、[LinkedIn](active-directory-b2c-setup-li-app.md)、[Microsoft](active-directory-b2c-setup-msa-app.md)、[Twitter](active-directory-b2c-setup-twitter-app.md).などの他の ID プロバイダーを追加することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

[ユーザー フローを作成](tutorial-create-user-flows.md)して、ユーザーがアプリケーションにサインアップおよびサインインできるようにします。

## <a name="create-applications"></a>アプリケーションの作成

ID プロバイダー アプリケーションでは、Azure AD B2C テナントと通信できるようにするための識別子とキーが提供されます。 チュートリアルのこのセクションでは、テナントに ID プロバイダーを追加するための識別子とキーの取得元になる、Azure AD アプリケーションと Facebook アプリケーションを作成します。 ID プロバイダーを 1 つだけ追加する場合は、そのプロバイダー用のアプリケーションを作成するだけでかまいません。

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory アプリケーションを作成する

Azure AD のユーザーのサインインを有効にするには、Azure AD テナント内でアプリケーションを登録する必要があります。 Azure AD テナントは、Azure AD B2C テナントと同じものではありません。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. お使いの Azure AD テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いの Azure AD テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
1. **[新規登録]** を選択します。
1. アプリケーションの名前を入力します。 たとえば、「 `Azure AD B2C App` 」のように入力します。
1. このアプリケーションには **[この組織のディレクトリ内のアカウントのみ]** の選択をそのまま使用します。
1. **[リダイレクト URI]** では、値 **[Web]** をそのまま使用し、次の URL をすべて小文字で入力します。`your-B2C-tenant-name` は、お使いの Azure AD B2C テナントの名前に置き換えます。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    たとえば、「 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` 」のように入力します。

    ここでは、すべての URL で [b2clogin.com](b2clogin.md) を使用してください。

1. **[登録]** を選択し、後の手順で使用する **[Application (client) ID]\(アプリケーション (クライアント) ID\)** をメモします。
1. アプリケーション メニューの **[管理]** で **[証明書とシークレット]** を選択してから、 **[新しいクライアント シークレット]** を選択します。
1. クライアント シークレットの **[説明]** を入力します。 たとえば、「 `Azure AD B2C App Secret` 」のように入力します。
1. 有効期限を選択します。 このアプリケーションには、 **[1 年]** の選択をそのまま使用します。
1. **[追加]** を選択し、後の手順で使用する新しいクライアント シークレットの値をメモします。

### <a name="create-a-facebook-application"></a>Facebook アプリケーションを作成する

Azure AD B2C で ID プロバイダーとして Facebook アカウントを使用するには、Facebook でアプリケーションを作成する必要があります。 まだ Facebook アカウントを持っていない場合は、[https://www.facebook.com/](https://www.facebook.com/) で取得できます。

1. Facebook アカウントの資格情報を使用して、[開発者向けの Facebook](https://developers.facebook.com/)にサインインします。
1. まだ登録していない場合は、Facebook 開発者として登録する必要があります。 これを行うには、ページの右上隅にある **[Get Started]\(スタートガイド\)** を選択し、Facebook のポリシーに同意して登録手順を完了します。
1. **[マイ アプリ]** を選択し、 **[アプリの作成]** を選択します。
1. **[表示名]** および有効な **[連絡先の電子メール]** を入力します。
1. **[Create App ID] \(アプリ ID の作成)** をクリックします。 Facebook プラットフォームのポリシーを受け入れ、オンライン セキュリティ チェックを完了する必要があります。
1. **[設定]**  >  **[基本]** を選択します。
1. **カテゴリ**を選択します。たとえば`Business and Pages`。 この値は Facebook では必須ですが、Azure AD B2C では使用されません。
1. ページの下部で、 **[プラットフォームの追加]** 、 **[Web サイト]** の順に選択します。
1. **サイト URL** に、`https://your-tenant-name.b2clogin.com/`を入力して`your-tenant-name`をお使いのテナントの名前に置き換えてください。
1. **[Privacy Policy URL] (プライバシー ポリシーの URL)** に URL (`http://www.contoso.com/` など) を入力します。 プライバシー ポリシーの URL は、アプリケーションのプライバシーに関する情報を提供するために維持されるページです。
1. **[変更の保存]** を選択します。
1. ページの上部にある **[App ID]\(アプリ ID\)** の値をメモします。
1. **[App Secret]\(アプリケーション シークレット\)** の横にある **[Show]\(表示\)** を選択し、その値をメモします。 アプリ ID とアプリケーション シークレットの両方を使用して、テナントで ID プロバイダーとして Facebook を構成します。 **[App Secret]\(アプリケーション シークレット\)** は、安全に保管する必要がある重要なセキュリティ資格情報です。
1. **[Products]\(製品\)** を選択し、 **[Facebook Login]\(Facebook ログイン\)** で **[Set up]\(セットアップ\)** を選択します。
1. 左側のメニューの **[Facebook Login]\(Facebook ログイン\)** で **[Settings]\(設定\)** を選択します。
1. **[有効な OAuth リダイレクト URI]** に「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」を入力します。 `your-tenant-name` をテナントの名前に置き換えます。 ページの下部にある **[Save Changes]\(変更の保存\)** を選択します。
1. Facebook アプリケーションを Azure AD B2C で使用できるようにするには、ページの右上にある**状態**セレクターをクリックし、 **[オン]** に設定してアプリケーションを公開し、 **[確認]** をクリックします。 この時点で、状態は**開発**から**ライブ**に変更されます。

## <a name="add-the-identity-providers"></a>ID プロバイダーを追加する

追加する ID プロバイダー用のアプリケーションを作成した後、ID プロバイダーをテナントに追加します。

### <a name="add-the-azure-active-directory-identity-provider"></a>Azure Active Directory の ID プロバイダーを追加する

1. Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いの Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** 、 **[追加]** の順に選択します。
1. **[名前]** を入力します。 たとえば､「*Contoso Azure AD*」と入力します。
1. **[ID プロバイダーの種類]** を選択し、 **[Open ID Connect (Preview)]\(Open ID Connect (プレビュー))** を選択して、 **[OK]** をクリックします。
1. **[この ID プロバイダーをセットアップします]** をクリックします。
1. **[メタデータ URL]** には、次の URL を入力します。`your-AD-tenant-domain` は、Azure AD テナントのドメイン名で置き換えます。

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    たとえば、「 `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration` 」のように入力します。

1. **[クライアント ID]** に、以前にメモした *[Application (client) ID]\(アプリケーション (クライアント) ID\)* を入力します。
1. **[クライアント シークレット]** に、以前にメモした "*クライアント シークレット*" の値を入力します。
1. 必要に応じて、**Domain_hint** に値を入力します。 たとえば、「 `ContosoAD` 」のように入力します。 [ドメイン ヒント](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)は、アプリケーションからの認証要求に含まれるディレクティブです。 ドメイン ヒントを使用して、ユーザーのフェデレーション IdP サインイン ページへの移動を高速化できます。 または、マルチテナント アプリケーションで使用して、テナント用にブランディングされた Azure AD サインイン ページをすぐに表示することができます。
1. **[OK]** を選択します。
1. **[この ID プロバイダーの要求をマップする]** を選択し、次の要求を設定します。

    - **User ID** には `oid` を入力します｡
    - **Display Name** には `name` を入力します｡
    - **Given name** には `given_name` を入力します｡
    - **Surname** には `family_name` を入力します｡
    - **Email** には `unique_name` を入力します｡

1. **[OK]** を選択し、 **[作成]** を選択して構成を保存します。

### <a name="add-the-facebook-identity-provider"></a>Facebook の ID プロバイダーを追加する

1. **[ID プロバイダー]** 、 **[追加]** の順に選択します。
1. **[名前]** を入力します。 たとえば、「*Facebook*」と入力します。
1. **[ID プロバイダーの種類]** 、 **[Facebook]** 、 **[OK]** の順に選択します。
1. **[この ID プロバイダーをセットアップします]** を選択し、以前にメモした "*アプリ ID*" を **[クライアント ID]** に入力します。
1. メモした "*アプリケーション シークレット*" を **[クライアント シークレット]** に入力します。
1. **[OK]** 、 **[作成]** の順に選択し、Facebook の構成を保存します。

## <a name="update-the-user-flow"></a>ユーザー フローを更新する

前提条件の一部として完了したチュートリアルでは、*B2C_1_signupsignin1* という名前の、サインアップおよびサインイン用のユーザー フローを作成しました。 このセクションでは、ID プロバイダーを *B2C_1_signupsignin1* ユーザー フローに追加します。

1. **[ユーザー フロー (ポリシー)]** を選択し、*B2C_1_signupsignin1* ユーザー フローを選択します。
2. **[ID プロバイダー]** を選択し、追加した **Facebook** および **Contoso Azure AD** の ID プロバイダーを選択します。
3. **[保存]** を選択します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. 作成したユーザー フローの [概要] ページで、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** を選択し、前に追加した ID プロバイダーでサインインします。
1. 追加した別の ID プロバイダーを使用して、ステップ 1 から 3 を繰り返します。

サインイン操作が成功した場合は、`https://jwt.ms` にリダイレクトされ、次のようなデコードされたトークンが表示されます。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * ID プロバイダー アプリケーションを作成する
> * ID プロバイダーをテナントに追加する
> * ID プロバイダーをユーザー フローに追加する

次に、アプリケーションの ID エクスペリエンスの一部としてユーザーに表示されるページの UI をカスタマイズする方法について説明します。

> [!div class="nextstepaction"]
> [Azure Active Directory B2C 内のアプリケーションのユーザー インターフェイスをカスタマイズする](tutorial-customize-ui.md)
