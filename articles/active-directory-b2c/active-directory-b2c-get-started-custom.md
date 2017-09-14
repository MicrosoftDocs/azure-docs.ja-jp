---
title: "Azure Active Directory B2C: カスタム ポリシーの概要 | Microsoft Docs"
description: "Azure Active Directory B2C のカスタム ポリシーの使用を開始する方法"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja;parahk;gsacavdm
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 4f14dbf4b66f10290cd4f98d56a005f97cc6a207
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシーの概要

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事の手順を完了すると、電子メール アドレスとパスワードによる "ローカル アカウント" サインアップまたはサインインがカスタム ポリシーでサポートされるようになります。 また、ID プロバイダー (Facebook や Azure Active Directory など) を追加するために環境を準備します。 Azure Active Directory (Azure AD) B2C Identity Experience Framework の他の使い方に関する説明を読む前に、これらの手順を完了することをお勧めします。

## <a name="prerequisites"></a>前提条件

作業を進める前に、すべてのユーザー、アプリ、ポリシーなどのコンテナーである Azure AD B2C テナントを準備しておくようにします。 まだない場合は、[Azure AD B2C テナントを作成する](active-directory-b2c-get-started.md)必要があります。 先に進む前に、Azure AD B2C 組み込みポリシーのチュートリアルを完了し、組み込みポリシーを使用してアプリケーションを構成することを、すべての開発者に強くお勧めします。 アプリケーションは、カスタム ポリシーを呼び出すようにポリシー名をわずかに変更すれば、どちらの種類のポリシーでも動作します。

>[!NOTE]
>カスタム ポリシーの編集にアクセスするには、テナントにリンクされている有効な Azure サブスクリプションが必要です。 [Azure AD B2C テナントを Azure サブスクリプションにリンクしていない場合](active-directory-b2c-how-to-enable-billing.md)、または Azure サブスクリプションが無効になっている場合は、[Identity Experience Framework] ボタンは使用できません。

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>カスタム ポリシーで使用する署名キーと暗号化キーを B2C テナントに追加する

1. Azure AD B2C テナント設定の **[Identity Experience Framework]** ブレードを開きます。
2. **[ポリシー キー]** を選択して、テナント内で利用できるキーを表示します。
3. 存在しない場合は、B2C_1A_TokenSigningKeyContainer を作成します。<br>
    a. **[追加]**を選択します。 <br>
    b. **[Generate] \(生成)** を選択します。<br>
    c. **[名前]** には `TokenSigningKeyContainer` を使用します。 <br> 
    プレフィックス `B2C_1A_` が自動的に追加される場合があります。<br>
    d. **[キーの種類]** には **[RSA]** を使用します。<br>
    e. **[日付]** には既定値を使用します。 <br>
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[キー使用法]** には **[署名]** を使用します。<br>
    g. **[作成]**を選択します。<br>
4. 存在しない場合は、B2C_1A_TokenEncryptionKeyContainer を作成します。<br>
 a. **[追加]**を選択します。<br>
 b. **[Generate] \(生成)** を選択します。<br>
 c. **[名前]** には `TokenEncryptionKeyContainer` を使用します。 <br>
   プレフィックス `B2C_1A`_ が自動的に追加される場合があります。<br>
 d. **[キーの種類]** には **[RSA]** を使用します。<br>
 e. **[日付]** には既定値を使用します。<br>
 f. **[キー使用法]** には **[暗号化]** を使用します。<br>
 g. **[作成]**を選択します。<br>
5. B2C_1A_FacebookSecret を作成します。 <br>
Facebook アプリケーション シークレットが既にある場合は、それをポリシー キーとしてテナントに追加します。 ない場合は、ポリシーが検証にパスするように、プレースホルダー値を含むキーを作成する必要があります。<br>
 a. **[追加]**を選択します。<br>
 b. **[オプション]** には **[Manual] \(手動)** を使用します。<br>
 c. **[名前]** には `FacebookSecret` を使用します。 <br>
 プレフィックス `B2C_1A_` が自動的に追加される場合があります。<br>
 d. **[シークレット]** ボックスに、developers.facebook.com の FacebookSecret を入力するか、プレースホルダーとして「`0`」を入力します。 "*Facebook App ID ではない*" ので注意してください。 <br>
 e. **[キー使用法]** には **[署名]** を使用します。 <br>
 f. **[作成]** を選択し、作成を確認します。

## <a name="register-identity-experience-framework-applications"></a>Identity Experience Framework アプリケーションを登録する

Azure AD B2C では、ユーザーのサインアップとサインインのためにエンジンが使用する 2 つの追加アプリケーションを登録する必要があります。

>[!NOTE]
>ローカル アカウントを使用したサインインを可能にする 2 つのアプリケーションを作成する必要があります。これは、IdentityExperienceFramework (Web アプリ) と、IdentityExperienceFramework アプリからアクセス許可を委任された ProxyIdentityExperienceFramework (ネイティブ アプリ) です。 ローカル アカウントは、テナント内にのみ存在します。 ユーザーは電子メール アドレスとパスワードの一意の組み合わせを使用してサインアップし、テナントに登録されたアプリケーションにアクセスします。

### <a name="create-the-identityexperienceframework-application"></a>IdentityExperienceFramework アプリケーションを作成する

1. [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキスト](active-directory-b2c-navigate-to-b2c-context.md)に切り替えます。
2. **[Azure Active Directory]** ブレード (**[Azure AD B2C]** ブレードではありません) を開きます。 これを見つけるために、**[その他のサービス]** を選択する必要がある場合があります。
3. **[アプリの登録]** を選択します。
4. **[新しいアプリケーションの登録]** を選択します。
   * **[名前]** には `IdentityExperienceFramework` を使用します。
   * **[アプリケーションの種類]** には **[Web アプリ/API]** を使用します。
   * **[サインオン URL]** には `https://login.microsoftonline.com/yourtenant.onmicrosoft.com` を使用します。ここで `yourtenant` は、ご利用の Azure AD B2C テナントのドメイン名です。
5. **[作成]**を選択します。
6. 作成されたら、新しく作成されたアプリケーション **IdentityExperienceFramework** を選択します。<br>
   * **[プロパティ]**を選択します。<br>
   * アプリケーション ID をコピーし、後のために保存します。

### <a name="create-the-proxyidentityexperienceframework-application"></a>ProxyIdentityExperienceFramework アプリケーションを作成する

1. **[アプリの登録]** を選択します。
1. **[新しいアプリケーションの登録]** を選択します。
   * **[名前]** には `ProxyIdentityExperienceFramework` を使用します。
   * **[アプリケーションの種類]** には **[ネイティブ]** を使用します。
   * **[リダイレクト URI]** には `https://login.microsoftonline.com/yourtenant.onmicrosoft.com` を使用します。ここで `yourtenant` は、ご利用の Azure AD B2C テナントです。
1. **[作成]**を選択します。
1. 作成されたら、アプリケーション **ProxyIdentityExperienceFramework** を選択します。<br>
   * **[プロパティ]**を選択します。 <br>
   * アプリケーション ID をコピーし、後のために保存します。
1. **[必要なアクセス許可]** を選択します。
1. **[追加]**を選択します。
1. **[API を選択します]** を選択します。
1. IdentityExperienceFramework という名前を検索します。 結果で **[IdentityExperienceFramework]** を選択して、**[選択]** をクリックします。
1. **[Access IdentityExperienceFramework] \(IdentityExperienceFramework にアクセスする)** の横にあるチェック ボックスをオンにして、**[選択]** を選択します。
1. **[完了]**を選択します。
1. **[Grant Permissions] \(アクセス許可の付与)** を選択してから、**[はい]** を選択して確認します。

## <a name="download-starter-pack-and-modify-policies"></a>スターター パックをダウンロードしてポリシーを変更する

カスタム ポリシーは、Azure AD B2C テナントにアップロードする必要のある XML ファイルのセットです。 操作を簡単にするために、スターター パックが用意されています。 次の一覧の各スターター パックには、説明したシナリオを実現するために必要な最低限の技術プロファイルとユーザー体験が含まれています。
 * LocalAccounts。 ローカル アカウントのみの使用を可能にします。
 * SocialAccounts。 ソーシャル (フェデレーション) アカウントのみの使用を可能にします。
 * **SocialAndLocalAccounts**。 チュートリアルではこのファイルを使います。
 * SocialAndLocalAccountsWithMFA。 ここにはソーシャル、ローカル、および Multi-Factor Authentication のオプションが含まれています。

各スターター パックには以下が含まれています。

* ポリシーの[ベース ファイル](active-directory-b2c-overview-custom.md#policy-files)。 ベースにはいくつかの変更が必要です。
* ポリシーの[拡張ファイル](active-directory-b2c-overview-custom.md#policy-files)。  このファイルは、ほとんどの構成変更が行われる場所です。
* [証明書利用者ファイル](active-directory-b2c-overview-custom.md#policy-files)はタスク固有のファイルであり、アプリケーションによって呼び出されます。

>[!NOTE]
>XML エディターが検証をサポートする場合、スターター パックのルート ディレクトリにある TrustFrameworkPolicy_0.3.0.0.xsd という XML スキーマに対してファイルを検証します。 XML スキーマ検証では、アップロードする前にエラーを識別します。

 次の手順で作業を開始します:

1. GitHub から active-directory-b2c-custom-policy-starterpack をダウンロードします。 [zip ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)するか、次を実行します。

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. SocialAndLocalAccounts フォルダーを開きます。  このフォルダー内のベース ファイル (TrustFrameworkBase.xml) には、ローカル アカウントとソーシャル/コーポレート アカウントの両方に必要な内容が含まれています。 ソーシャル関連の内容は、ローカル アカウントを使い始めるための手順には影響しません。
3. Open TrustFrameworkBase.xml。 XML エディターが必要な場合は、軽量のクロスプラットフォーム エディターである [Visual Studio Code](https://code.visualstudio.com/download) をお試しください。
4. ルートの `TrustFrameworkPolicy` 要素で、`TenantId` および `PublicPolicyUri` 属性を更新し、`yourtenant.onmicrosoft.com` を Azure AD B2C テナントのドメイン名に置き換えます。
   ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
   >[!NOTE]
   >`PolicyId` は、ポータルに表示されるポリシー名であり、他のポリシー ファイルがこのポリシー ファイルを参照するときに使う名前です。

5. ファイルを保存します。
6. Open TrustFrameworkExtensions.xml。 `yourtenant.onmicrosoft.com` を Azure AD B2C テナントに置き換えることによって、2 つの同じ変更を加えます。 同じ置き換えを要素 `<TenantId>` で行います。変更は合計 3 つになります。 ファイルを保存します。
7. SignUpOrSignIn.xml を開きます。 `yourtenant.onmicrosoft.com` を Azure AD B2C テナントに置き換えることで、3 つの場所に同じ変更を加えます。 ファイルを保存します。
8. パスワード リセットとプロファイル編集のファイルを開きます。 `yourtenant.onmicrosoft.com` を Azure AD B2C テナントに置き換えることで、各ファイルの 3 つの場所に同じ変更を加えます。 ファイルを保存します。

### <a name="add-the-application-ids-to-your-custom-policy"></a>アプリケーション ID をカスタム ポリシーに追加する
アプリケーション ID を拡張ファイル (`TrustFrameworkExtensions.xml`) に追加します。

1. 拡張ファイル (TrustFrameworkExtensions.xml) で要素 `<TechnicalProfile Id="login-NonInteractive">` を見つけます。
2. `IdentityExperienceFrameworkAppId` の両方のインスタンスを、前に作成した Identity Experience Framework アプリケーションのアプリケーション ID に置き換えます。 たとえば次のようになります。

   ```xml
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. `ProxyIdentityExperienceFrameworkAppId` の両方のインスタンスを、前に作成した Proxy Identity Experience Framework アプリケーションのアプリケーション ID に置き換えます。
4. 拡張ファイルを保存します。

## <a name="upload-the-policies-to-your-tenant"></a>ポリシーをテナントにアップロードする

1. [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキストに切り替え](active-directory-b2c-navigate-to-b2c-context.md)、**[Azure AD B2C]** ブレードを開きます。
2. **[Identity Experience Framework]** を選択します。
3. **[ポリシーのアップロード]** を選択します。

    >[!WARNING]
    >カスタム ポリシー ファイルは、次の順序でアップロードする必要があります:

1. TrustFrameworkBase.xml をアップロードします。
2. TrustFrameworkExtensions.xml をアップロードします。
3. SignUpOrSignin.xml をアップロードします。
4. 他のポリシー ファイルをアップロードします。

ファイルがアップロードされると、ポリシー ファイルの名前の前に `B2C_1A_` が付きます。

## <a name="test-the-custom-policy-by-using-run-now"></a>[今すぐ実行] を使用してカスタム ポリシーをテストする

1. **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。

   >[!NOTE]
   >**[今すぐ実行]** を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 組み込みポリシーとカスタム ポリシーの両方を呼び出すには、Azure AD B2C で **[アプリケーション]** メニュー項目を使用するか、Identity Experience Framework を使用して、アプリケーションを B2C テナントに登録する必要があります。 アプリケーションごとに 1 回登録するだけです。<br><br>
   アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。  

2. アップロードした証明書利用者 (RP) カスタム ポリシーである B2C_1A_signup_signin を開きます。 **[今すぐ実行]** を選択します。

3. メール アドレスを使用してサインアップできることを確認します。

4. 同じアカウントでサインインし、構成が正しく行われていることを確認します。

>[!NOTE]
>サインインが失敗する一般的な原因は、IdentityExperienceFramework アプリが正しく構成されていないことです。


## <a name="next-steps"></a>次のステップ

### <a name="add-facebook-as-an-identity-provider"></a>Facebook を ID プロバイダーとして追加する
Facebook をセットアップするには:
1. [developers.facebook.com で Facebook アプリケーションを構成します](active-directory-b2c-setup-fb-app.md)。
2. [Facebook アプリケーション シークレットを Azure AD B2C テナントに追加します](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies)。
3. TrustFrameworkExtensions ポリシー ファイルで、`client_id` の値を Facebook アプリケーション ID に置き換えます。

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. TrustFrameworkExtensions.xml ポリシー ファイルをテナントにアップロードします。
5. **[今すぐ実行]** を使用するか、登録済みアプリケーションからポリシーを直接呼び出してテストします。

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Azure Active Directory を ID プロバイダーとして追加する
このファースト ステップ ガイドで使用したベース ファイルには、他の ID プロバイダーを追加するために必要な内容の一部が既に含まれています。 サインインをセットアップする方法については、「[Azure Active Directory B2C: Azure AD のアカウントを使用してサインインする](active-directory-b2c-setup-aad-custom.md)」の記事を参照してください。

Identity Experience Framework を使用する Azure AD B2C のカスタムのポリシーの概要については、「[Azure Active Directory B2C: カスタム ポリシー](active-directory-b2c-overview-custom.md)」の記事を参照してください。 

