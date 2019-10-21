---
title: カスタム ポリシーの概要 - Azure Active Directory B2C
description: Azure Active Directory B2C でのカスタム ポリシーの概要について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8e858869d742120138e7997ce21d9e4cca93ed9b
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71264358"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でのカスタム ポリシーの概要

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[カスタム ポリシー](active-directory-b2c-overview-custom.md)は、Azure Active Directory B2C (Azure AD B2C) テナントの動作を定義する構成ファイルです。 この記事では、電子メール アドレスとパスワードを使用してローカル アカウント サインアップまたはサインインをサポートするカスタム ポリシーを作成します。 また、ID プロバイダーを追加するために環境を準備します。

## <a name="prerequisites"></a>前提条件

- まだ持っていない場合は、[お使いの Azure サブスクリプションにリンクされている Azure AD B2C テナント](tutorial-create-tenant.md)を作成します。
- Azure AD B2C と通信できるように、自分が作成した[テナントにアプリケーションを登録](tutorial-register-applications.md)します。
- [Facebook アカウントでのサインアップとサインインの設定](active-directory-b2c-setup-fb-app.md)に関する記事の手順を完了して、Facebook アプリケーションを構成します。

## <a name="add-signing-and-encryption-keys"></a>署名および暗号化キーを追加します。

1. [Azure ポータル](https://portal.azure.com)
1. 上部のメニューにある **[ディレクトリとサブスクリプション]** フィルターを使用して、お使いの Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。

### <a name="create-the-signing-key"></a>署名キーを作成します。

1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション**については、`Generate`を選択してください。
1. **名前**に`TokenSigningKeyContainer`を入力します。 プレフィックス `B2C_1A_` が自動的に追加される場合があります。
1. **キー タイプ**については、**RSA** を選択します。
1. **[キー使用法]** には **[署名]** を選択します。
1. **作成** を選択します。

### <a name="create-the-encryption-key"></a>暗号化キーを作成します。

1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション**については、`Generate`を選択してください。
1. **名前**に`TokenEncryptionKeyContainer`を入力します。 プレフィックス `B2C_1A`_ が自動的に追加される場合があります。
1. **キー タイプ**については、**RSA** を選択します。
1. **[キー使用法]** には **[暗号化]** を選択します。
1. **作成** を選択します。

### <a name="create-the-facebook-key"></a>Facebook のキーを作成します。

ポリシー キーとして、Facebook アプリケーションの [[アプリ シークレット]](active-directory-b2c-setup-fb-app.md)を追加します。 この記事の前提条件の一部として作成したアプリケーションのアプリ シークレットを使用できます。

1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション**については、`Manual`を選択します。
1. **名前**には、`FacebookSecret`を入力します。 プレフィックス `B2C_1A_` が自動的に追加される場合があります。
1. **シークレット**で、developers.facebook.com から Facebook アプリケーションの*アプリ シークレット*を入力します。 この値はシークレットであり、アプリケーション ID ではありません。
1. **[キー使用法]** には **[署名]** を選択します。
1. **作成** を選択します。

## <a name="register-identity-experience-framework-applications"></a>Identity Experience Framework アプリケーションを登録する

Azure AD B2C では、ユーザーのサインアップとサインインのために使用される 2 つのアプリケーションを登録する必要があります。IdentityExperienceFramework (Web アプリ) と、IdentityExperienceFramework アプリからアクセス許可を委任された ProxyIdentityExperienceFramework (ネイティブ アプリ) です。 ローカル アカウントは、テナント内にのみ存在します。 ユーザーは電子メール アドレスとパスワードの一意の組み合わせを使用してサインアップし、テナントに登録されたアプリケーションにアクセスします。

### <a name="register-the-identityexperienceframework-application"></a>IdentityExperienceFramework アプリケーションを登録します

1. Azure portal の左上隅の **[すべてのサービス]** を選択します。
1. 検索ボックスに「 `Azure Active Directory`」と入力します。
1. 検索結果で **[Azure Active Directory]** を選択します。
1. 左側のメニューで **[管理]** の下の **[アプリの登録 (レガシー)]** を選択します。
1. **[新しいアプリケーションの登録]** を選択します。
1. **名前**には、`IdentityExperienceFramework`を入力します。
1. **アプリケーションの種類**については、**Web アプリケーション/ API** を選択します。
1. **サインオン URL** には、`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`を入力します。ここで、`your-tenant-name`は、Azure AD B2C テナント ドメイン名です。 ここでは、すべての URL で [b2clogin.com](b2clogin.md) を使用してください。
1. **作成** を選択します。 作成した後は、アプリケーション ID をコピーし、後で使用するために保存します。

### <a name="register-the-proxyidentityexperienceframework-application"></a>ProxyIdentityExperienceFramework アプリケーションを登録する

1. **[アプリの登録 (レガシ)]** で、 **[新しいアプリケーションの登録]** を選択します。
1. **名前**には、`ProxyIdentityExperienceFramework`を入力します。
1. **アプリケーションの種類**については、**ネイティブ**を選択します。
1. **リダイレクト URI** には、`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`を入力します。ここで、`your-tenant-name`は Azure AD B2C テナントです。
1. **作成** を選択します。 作成した後は、アプリケーション ID をコピーし、後で使用するために保存します。
1. **[設定]** を選択し、 **[必要なアクセス許可]** を選択したら、 **[追加]** を選択します。
1. **[API を選択します]** を選択し、**IdentityExperienceFramework** を検索して選択してから、 **[選択]** をクリックします。
1. **[IdentityExperienceFramework にアクセスする]** の横のチェックボックスにチェックを入れて、 **[選択する]** をクリックし、 **[完了]** をクリックします。
1. **[アクセス許可を付与する]** を選択したら、 **[はい]** を選択して確定します。

## <a name="custom-policy-starter-pack"></a>カスタム ポリシー スターター パック

カスタム ポリシーは、技術プロファイルとユーザー体験を定義するために Azure AD B2C テナントにアップロードされる XML ファイルのセットです。 すぐに作業を開始できるように、いくつかの事前に作成されたポリシーが含まれているスターターパックが用意されています。 これらの各スターター パックには、説明したシナリオを実現するために必要な最小数の技術プロファイルとユーザー体験が含まれています。

- **LocalAccounts** - ローカル アカウントのみの使用を可能にします。
- **SocialAccounts** - ソーシャル (フェデレーション) アカウントのみの使用を可能にします。
- **SocialAndLocalAccounts** - ローカル アカウントとソーシャル アカウントの両方の使用を可能にします。
- **SocialAndLocalAccountsWithMFA** - ソーシャル、ローカル、および多要素認証オプションの使用を可能にします。

各スターター パックには以下が含まれています。

- **ベース ファイル** - ベースにはいくつかの変更が必要です。 例:*TrustFrameworkBase.xml*
- **拡張ファイル** - このファイルは、構成変更の大半が実行される場所です。 例:*TrustFrameworkExtensions.xml*
- **証明書利用者ファイル** - アプリケーションによって呼び出される、タスク固有のファイルです。 次に例を示します。*SignUpOrSignin.xml*、*ProfileEdit.xml*、*PasswordReset.xml*

この記事では、**SocialAndLocalAccounts** スターター パック内の XML カスタム ポリシー ファイルを編集します。 XML エディターが必要な場合は、軽量のクロスプラットフォーム エディターである [Visual Studio Code](https://code.visualstudio.com/download) をお試しください。

### <a name="get-the-starter-pack"></a>スターター パックを取得する

GitHub からカスタム ポリシー スターター パックを取得し、SocialAndLocalAccounts スターター パック内の XML ファイルの名前を Azure AD B2C テナントの名前に更新します。

1. [.zip ファイルをダウンロードする](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)か、リポジトリを複製します。

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. **SocialAndLocalAccounts** ディレクトリ内のすべてのファイルで、`yourtenant` 文字列を Azure AD B2C テナントの名前に置き換えます。

    たとえば、B2C テナントの名前が *contosotenant*であれば、`yourtenant.onmicrosoft.com` のすべてのインスタンスは `contosotenant.onmicrosoft.com` になります。

### <a name="add-application-ids-to-the-custom-policy"></a>カスタム ポリシーにアプリケーション ID を追加します。

拡張子ファイル *TrustFrameworkExtensions.xml*  にアプリケーション ID を追加します。

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** を開いて、`<TechnicalProfile Id="login-NonInteractive">` 要素を見つけます。
1. `IdentityExperienceFrameworkAppId` の両方のインスタンスを、前に作成した IdentityExperienceFramework アプリケーションのアプリケーション ID に置き換えます。
1. `ProxyIdentityExperienceFrameworkAppId` の両方のインスタンスを、前に作成した ProxyIdentityExperienceFramework アプリケーションのアプリケーション ID に置き換えます。
1. ファイルを保存します。

## <a name="upload-the-policies"></a>ポリシーをアップロードします。

1. Azure portal で B2C テナントに移動し、 **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシーのアップロード]** を選択します。
1. 次の順序でポリシー ファイルをアップロードします。
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

ファイルをアップロードすると、Azure によって、それぞれに `B2C_1A_` が追加されます。

> [!TIP]
> XML エディターで検証がサポートされている場合は、スターター パックのルート ディレクトリにある `TrustFrameworkPolicy_0.3.0.0.xsd` という XML スキーマに対してファイルを検証します。 XML スキーマ検証では、アップロードする前にエラーを識別します。

## <a name="test-the-custom-policy"></a>カスタム ポリシーをテストする

1. **[カスタム ポリシー]** ページで、**B2C_1A_signup_signin** を選択します。
1. カスタム ポリシーの概要ページの **[アプリケーションの選択]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。
1. **[返信 URL]** が `https://jwt.ms` であることを確認します。
1. **[今すぐ実行]** を選択します。
1. メール アドレスを使用してサインアップします。
1. もう一度 **[今すぐ実行]** を選択します。
1. 同じアカウントでサインインし、構成が正しく行われていることを確認します。

## <a name="add-facebook-as-an-identity-provider"></a>Facebook を ID プロバイダーとして追加する

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** ファイルで、`client_id` の値を Facebook アプリケーション ID に置き換えます。

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. *TrustFrameworkExtensions.xml* ファイルをテナントにアップロードします。
1. **[カスタム ポリシー]** ページで、**B2C_1A_signup_signin** を選択します。
1. **[今すぐ実行]** を選択し、Facebook でサインインする Facebook を選択し、カスタム ポリシーをテストします。

## <a name="next-steps"></a>次の手順

次に、ID プロバイダーとしての Azure Active Directory (Azure AD) の追加を試してください。 このファースト ステップ ガイドで使用したベース ファイルには、Azure AD などの他の ID プロバイダーを追加するために必要な内容の一部が既に含まれています。

ID プロバイダーとしての Azure AD の設定については、「[カスタム ポリシーを使用して Azure Active Directory B2C に Azure Active Directory アカウントでサインインするように設定する](active-directory-b2c-setup-aad-custom.md)」を参照してください。
