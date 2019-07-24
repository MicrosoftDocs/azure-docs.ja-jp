---
title: カスタム ポリシーの概要 - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C でのカスタム ポリシーの概要について説明します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b414529d7756812f1e1e16d2d0184c8472c0c55f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798062"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でのカスタム ポリシーの概要   

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[カスタム ポリシー](active-directory-b2c-overview-custom.md)は、Azure Active Directory (Azure AD) B2C テナントの動作を定義する構成ファイルです。 この記事では、電子メール アドレスとパスワードを使用してローカル アカウント サインアップまたはサインインをサポートするカスタム ポリシーを作成します。 また、ID プロバイダーを追加するために環境を準備します。

## <a name="prerequisites"></a>前提条件

- 既に持っていない場合は、[ご使用の Azure サブスクリプションにリンクされている Azure AD B2C テナント](tutorial-create-tenant.md)を作成する必要があります。
- Azure AD B2C と通信できるように、自分が作成した[テナントにアプリケーションを登録](tutorial-register-applications.md)します。

## <a name="add-signing-and-encryption-keys"></a>署名および暗号化キーを追加します。

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューで **[ディレクトリとサブスクリプション] フィルター**をクリックし、ご利用のテナントが含まれているディレクトリを選択します。 
3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
4. [概要] ページで、**[Identity Experience Framework - プレビュー]** を選択します。

### <a name="create-the-signing-key"></a>署名キーを作成します。

1. **[ポリシー キー]** を選択し、**[追加]** を選択します。
2. **オプション**については、`Generate`を選択してください。
3. **名前**に`TokenSigningKeyContainer`を入力します。 プレフィックス `B2C_1A_` が自動的に追加される場合があります。
4. **キー タイプ**については、**RSA** を選択します。
5. **[キー使用法]** には **[署名]** を選択します。
6. **Create** をクリックしてください。

### <a name="create-the-encryption-key"></a>暗号化キーを作成します。

1. **[ポリシー キー]** を選択し、**[追加]** を選択します。
2. **オプション**については、`Generate`を選択してください。
3. **名前**に`TokenEncryptionKeyContainer`を入力します。 プレフィックス `B2C_1A`_ が自動的に追加される場合があります。
4. **キー タイプ**については、**RSA** を選択します。
5. **[キー使用法]** には **[暗号化]** を選択します。
6. **Create** をクリックしてください。

### <a name="create-the-facebook-key"></a>Facebook のキーを作成します。

 [Facebook アプリケーション シークレット](active-directory-b2c-setup-fb-app.md)が既にある場合は、それをポリシー キーとしてテナントに追加します。 ない場合は、ポリシーが検証にパスするように、プレースホルダー値を含むキーを作成する必要があります。

1. **[ポリシー キー]** を選択し、**[追加]** を選択します。
2. **オプション**については、`Manual`を選択します。
3. **名前**には、`FacebookSecret`を入力します。 プレフィックス `B2C_1A_` が自動的に追加される場合があります。
4. **シークレット**で、developers.facebook.com から Facebook シークレットを入力するか、または`0`をプレースホルダーとして入力します。 この値はシークレットであり、アプリケーション ID ではありません。
5. **[キー使用法]** には **[署名]** を選択します。
6. **Create** をクリックしてください。

## <a name="register-identity-experience-framework-applications"></a>Identity Experience Framework アプリケーションを登録する

Azure AD B2C では、ユーザーのサインアップとサインインのために使用される 2 つのアプリケーションを登録する必要があります。IdentityExperienceFramework (Web アプリ) と、IdentityExperienceFramework アプリからアクセス許可を委任された ProxyIdentityExperienceFramework (ネイティブ アプリ) です。 ローカル アカウントは、テナント内にのみ存在します。 ユーザーは電子メール アドレスとパスワードの一意の組み合わせを使用してサインアップし、テナントに登録されたアプリケーションにアクセスします。

### <a name="register-the-identityexperienceframework-application"></a>IdentityExperienceFramework アプリケーションを登録します

1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、**[アプリの登録]** を検索して選択します。
2. **[新しいアプリケーションの登録]** を選択します。
3. **名前**には、`IdentityExperienceFramework`を入力します。
4. **アプリケーションの種類**については、**Web アプリケーション/ API** を選択します。
5. **サインオン URL** には、`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`を入力します。ここで、`your-tenant-name`は、Azure AD B2C テナント ドメイン名です。
6. **Create** をクリックしてください。 
7. 作成した後は、アプリケーション ID をコピーし、後で使用するために保存します。

### <a name="register-the-proxyidentityexperienceframework-application"></a>ProxyIdentityExperienceFramework アプリケーションを登録する

1. **[アプリの登録]** を選択し、**[新しいアプリケーションの登録]** を選択します。
2. **名前**には、`ProxyIdentityExperienceFramework`を入力します。
3. **アプリケーションの種類**については、**ネイティブ**を選択します。
4. **リダイレクト URI** には、`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`を入力します。ここで、`yourtenant`は Azure AD B2C テナントです。
5. **Create** をクリックしてください。 作成した後は、アプリケーション ID をコピーし、後で使用するために保存します。
6. [設定] ページで、**必要な権限**を選択し、**追加**を選択します。
7. **[API を選択します]** を選択し、**IdentityExperienceFramework** を検索して選択してから、**[選択]** をクリックします。
9. **[IdentityExperienceFramework にアクセスする]** の横のチェックボックスにチェックを入れて、**[選択する]** をクリックし、**[完了]** をクリックします。
10. **[Grant Permissions] \(アクセス許可の付与)** を選択してから、**[はい]** を選択して確認します。

## <a name="download-starter-pack-and-modify-policies"></a>スターター パックをダウンロードしてポリシーを変更する

カスタム ポリシーは、Azure AD B2C テナントにアップロードする必要のある XML ファイルのセットです。 ファイルのスターターパックが提供されているため、すぐに使えるようになります。 次の一覧の各スターター パックには、説明したシナリオを実現するために必要な最低限の技術プロファイルとユーザー体験が含まれています。

- LocalAccounts - ローカル アカウントのみの使用を可能にします。
- SocialAccounts - ソーシャル (フェデレーション) アカウントのみの使用を可能にします。
- SocialAndLocalAccounts - ローカル アカウントとソーシャル アカウントの両方の使用を可能にします。
- SocialAndLocalAccountsWithMFA - ソーシャル、ローカル、および多要素認証オプションすべての使用を可能にします。

各スターター パックには以下が含まれています。

- 基本ファイルです。 ベースにはいくつかの変更が必要です。
- 拡張ファイルです。  このファイルは、ほとんどの構成変更が行われる場所です。
- 証明書利用者ファイルです。 ご使用のアプリケーションによって呼び出される、タスク固有のファイルです。

>[!NOTE]
>XML エディターが検証をサポートする場合、スターター パックのルート ディレクトリにある TrustFrameworkPolicy_0.3.0.0.xsd という XML スキーマに対してファイルを検証します。 XML スキーマ検証では、アップロードする前にエラーを識別します。

1. [zip ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)するか、次を実行します。

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

2. SocialAndLocalAccounts フォルダーで、`yourtenant`をテナントの名前に置き換えたすべてのファイルを編集します。 たとえば、「 `contosoTenant.onmicrosoft.com` 」のように入力します。 XML エディターが必要な場合は、軽量のクロスプラットフォーム エディターである [Visual Studio Code](https://code.visualstudio.com/download) をお試しください。

### <a name="add-application-ids-to-the-custom-policy"></a>カスタム ポリシーにアプリケーション ID を追加します。

拡張子ファイル *TrustFrameworkExtensions.xml*  にアプリケーション ID を追加します。

1. *TrustFrameworkExtensions.xml* ファイルを開き、要素`<TechnicalProfile Id="login-NonInteractive">`を検索します。
2. `IdentityExperienceFrameworkAppId` の両方のインスタンスを、前に作成した Identity Experience Framework アプリケーションのアプリケーション ID に置き換えます。 `ProxyIdentityExperienceFrameworkAppId` の両方のインスタンスを、前に作成した Proxy Identity Experience Framework アプリケーションのアプリケーション ID に置き換えます。
3. 拡張ファイルを保存します。

## <a name="upload-the-policies"></a>ポリシーをアップロードします。

1. Identity Experience Framework のカスタム ポリシーページで、**ポリシーのアップロード**を選択します。
1. この順序で、*TrustFrameworkBase.xml*、*TrustFrameworkExtensions.xml*、*SignUpOrSignin.xml*、*ProfileEdit.xml*、および *PasswordReset .xml* をアップロードします。 ファイルがアップロードされると、ポリシー ファイルの名前の前に `B2C_1A_` が付きます。

## <a name="test-the-custom-policy"></a>カスタム ポリシーをテストする

1. 「カスタムポリシー」ページで、 **B2C_1A_signup_signin** を選択します。
2. カスタム ポリシーの概要ページの **[アプリケーションの選択]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[返信 URL]** が `https://jwt.ms` であることを確認します。
3. **[今すぐ実行]** を選択します。
4. メール アドレスを使用してサインアップできることを確認します。
5. 同じアカウントでサインインし、構成が正しく行われていることを確認します。

## <a name="add-facebook-as-an-identity-provider"></a>Facebook を ID プロバイダーとして追加する

1. [Facebook アプリケーション](active-directory-b2c-setup-fb-app.md)を構成します。
2. *TrustFrameworkExtensions.xml* ファイルで、`client_id`の値を Facebook アプリケーション ID に置き換えます。

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
3. *TrustFrameworkExtensions.xml* ファイルをテナントにアップロードします。
4. **[今すぐ実行]** を使用するか、登録済みアプリケーションからポリシーを直接呼び出してテストします。

## <a name="next-steps"></a>次の手順

- Azure Active Directory を ID プロバイダーとして追加します。 このファースト ステップ ガイドで使用したベース ファイルには、他の ID プロバイダーを追加するために必要な内容の一部が既に含まれています。 サインインの設定については、[Active Directory B2C カスタム ポリシーを使用して Azure Active Directory アカウントでサインアップとサインインを設定する](active-directory-b2c-setup-aad-custom.md)記事を参照してください。
