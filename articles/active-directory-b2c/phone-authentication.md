---
title: カスタム ポリシーを使用した電話のサインアップとサインイン
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でカスタム ポリシーを使用して、アプリケーション ユーザーの電話にワンタイム パスワード (OTP) をテキスト メッセージで送信します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2600ea3488c643bcf215b058425de42cd439dcff
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660269"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>Azure AD B2C でカスタム ポリシーを使用した電話のサインアップとサインインを設定する

Azure Active Directory B2C (Azure AD B2C) での電話のサインアップとサインインを使用すると、ユーザーは、テキスト メッセージで電話に送信されたワンタイム パスワード (OTP) を使用して、アプリケーションにサインアップしてサインインできます。 ワンタイム パスワードを使用すると、ユーザーがパスワードを忘れたり、パスワードが侵害されたりするリスクを最小限に抑えることができます。

この記事の手順に従ってカスタム ポリシーを使用することで、お客様が電話に送信されたワンタイム パスワードを使用してサインアップし、アプリケーションにサインインできるようになります。

## <a name="pricing"></a>価格

ワンタイム パスワードは SMS テキスト メッセージを使用してお客様のユーザーに送信されるため、お客様は送信されたメッセージごとに課金される場合があります。 価格情報については、「[Azure Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)」の「**別料金**」セクションを参照してください。

## <a name="user-experience-for-phone-sign-up-and-sign-in"></a>電話のサインアップとサインインのユーザー エクスペリエンス

電話のサインアップとサインインを使用すると、ユーザーはプライマリ ID として電話番号を使用してアプリにサインアップできます。 サインアップとサインイン時のエンド ユーザーのエクスペリエンスについては、以下で説明します。

> [!NOTE]
> 以下のサンプル テキストと同様に、サインアップおよびサインインのエクスペリエンスに同意情報を含めることを強くお勧めします。 このサンプル テキストは、参考目的でのみ使用してください。 [CTIA Web サイト](https://www.ctia.org/programs)にある「Short Code Monitoring Handbook」(ショート コード監視に関するハンドブック) を参照し、ご自身のコンプライアンス ニーズを満たすための最終的なテキストと機能の構成に関するガイダンスについては、法務またはコンプライアンスの専門家に相談してください。
>
> "*電話番号を入力すると、テキスト メッセージによって送信されるワンタイム パスコードの受信に同意したことになり、 *&lt;挿入: アプリケーション名&gt; にサインインできるようになります*"。標準メッセージとデータの通信料が適用される場合があります。*
>
> *&lt;挿入: プライバシーに関する声明へのリンク&gt;*<br/>*&lt;挿入: サービス利用規約へのリンク&gt;*

独自の同意情報を追加するには、次のサンプルをカスタマイズしてください。 表示コントロールを備えたセルフアサート ページで使用される ContentDefinition の場合に、これを `LocalizedResources` に含めます ([電話のサインアップおよびサインインのスターター パック][starter-pack-phone]に含まれる *Phone_Email_Base.xml* ファイル)。

```xml
<LocalizedResources Id="phoneSignUp.en">        
    <LocalizedStrings>
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_msg_intro">By providing your phone number, you consent to receiving a one-time passcode sent by text message to help you sign into {insert your application name}. Standard message and data rates may apply.</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_text">Privacy Statement</LocalizedString>                
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_url">{insert your privacy statement URL}</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_text">Terms and Conditions</LocalizedString>             
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_url">{insert your terms and conditions URL}</LocalizedString>          
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please verify your country code and phone number</LocalizedString>        
    </LocalizedStrings>      
</LocalizedResources>
   ```

### <a name="phone-sign-up-experience"></a>電話のサインアップ エクスペリエンス

ユーザーがアプリケーションのアカウントをまだ持っていない場合は、 **[今すぐサインアップ]** リンクを選択して作成できます。 サインアップ ページが表示されます。ここでユーザーは **[国]** を選択し、電話番号を入力して、 **[コードの送信]** を選択します。

![ユーザーが電話によるサインアップを開始](media/phone-authentication/phone-signup-start.png)

1 回限りの確認コードがユーザーの電話番号に送信されます。 ユーザーは、サインアップ ページで **確認コード** を入力し、 **[コードの確認]** を選択します (ユーザーがコードを取得できなかった場合は、 **[新しいコードを送信します]** を選択できます)。

![ユーザーが電話のサインアップ時にコードを確認する](media/phone-authentication/phone-signup-verify-code.png)

ユーザーは、サインアップ ページで要求されたその他の情報を入力します。 たとえば、**表示名**、**名**、**姓** などです (国と電話番号は設定されたままです)。 ユーザーが別の電話番号を使用する場合は、 **[番号の変更]** を選択して、サインアップを再開することができます。 完了したら、ユーザーは **[続行]** を選択します。

![ユーザーが追加情報を入力する](media/phone-authentication/phone-signup-additional-info.png)

次に、ユーザーは回復用メールを入力するように求められます。 ユーザーはメール アドレスを入力し、 **[確認コードを送信する]** を選択します。 ユーザーはメールの受信トレイに送信されたコードを取得して、 **[確認コード]** ボックスに入力できます。 次に、ユーザーは **[コードの確認]** を選択します。 

コードが確認されたら、ユーザーは **[作成]** を選択してアカウントを作成します。 または、ユーザーが別のメール アドレスを使用する場合は、 **[電子メールの変更]** を選択できます。

![ユーザーがアカウントを作成する](media/phone-authentication/email-verification.png)

### <a name="phone-sign-in-experience"></a>電話によるサインイン エクスペリエンス

ユーザーが、電話番号を ID として使用している既存のアカウントを持っている場合は、その電話番号を入力し、 **[続行]** を選択します。 **[続行]** を選択して国と電話番号を確定すると、1 回限りの認証コードがそのユーザーの電話に送信されます。 ユーザーが確認コードを入力し、 **[続行]** を選択してサインインします。

![電話によるサインインのユーザー エクスペリエンス](media/phone-authentication/phone-signin-screens.png)

## <a name="deleting-a-user-account"></a>ユーザー アカウントを削除する

場合によっては、Azure AD B2C ディレクトリからユーザーと関連付けられたデータを削除する必要があります。 Azure portal を使用してユーザー アカウントを削除する方法の詳細については、[こちらの手順](/microsoft-365/compliance/gdpr-dsr-azure#step-5-delete)を参照してください。 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]



## <a name="prerequisites"></a>前提条件

OTP を設定する前に、次のリソースを用意しておく必要があります。

* [Azure AD B2C テナント](tutorial-create-tenant.md)
* ご利用のテナントに[登録済みの Web アプリケーション](tutorial-register-applications.md)
* テナントにアップロード済みの[カスタム ポリシー](custom-policy-get-started.md)

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>電話でのサインアップとサインインのスターター パックを入手する

まず、電話でのサインアップとサインインのためのカスタム ポリシー ファイルを更新して、Azure AD B2C テナントと連携させます。

1. [電話のサインアップとサインインのカスタム ポリシー ファイル][starter-pack-phone]をローカルにあるスターター パック リポジトリの複製で検索するか、直接ダウンロードします。 XML ポリシー ファイルは次のディレクトリにあります。

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. 各ファイル内で、文字列 `yourtenant` を、使用している Azure AD B2C テナントの名前に置き換えます。 たとえば、B2C テナントの名前が *contosob2c* であれば、`yourtenant.onmicrosoft.com` のすべてのインスタンスは `contosob2c.onmicrosoft.com` になります。

1. 「[Azure Active Directory B2C でのカスタム ポリシーの概要](custom-policy-get-started.md)」の「[カスタム ポリシーにアプリケーション ID を追加します](custom-policy-get-started.md#add-application-ids-to-the-custom-policy)」の手順を完了します。 この場合は、前提条件を完了したときに登録した 2 つのアプリケーション (*IdentityExperienceFramework* と *ProxyIdentityExperienceFramework*) の **アプリケーション (クライアント) ID** を使用して、`/phone-number-passwordless/`**`Phone_Email_Base.xml`** を更新します。

## <a name="upload-the-policy-files"></a>ポリシー ファイルをアップロードする

1. [Azure portal](https://portal.azure.com) にサインインし、Azure AD B2C テナントに移動します。
1. **[ポリシー]** で **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシーのアップロード]** を選択します。
1. 次の順序でポリシー ファイルをアップロードします。
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

各ファイルをアップロードすると、Azure によって `B2C_1A_` プレフィックスが追加されます。

## <a name="test-the-custom-policy"></a>カスタム ポリシーをテストする

1. **[カスタム ポリシー]** で、**[B2C_1A_SignUpOrSignInWithPhone]** を選択します。
1. **[アプリケーションの選択]** で、前提条件の完了時に登録した *webapp1* アプリケーションを選択します。
1. **[応答 URL の選択]** で、`https://jwt.ms` を選択します。
1. **[今すぐ実行]** を選択し、電子メール アドレスまたは電話番号を使用してサインアップします。
1. もう一度 **[今すぐ実行]** を選択し、同じアカウントでサインインして、構成が正しく行われていることを確認します。

## <a name="get-user-account-by-phone-number"></a>電話番号でユーザー アカウントを取得する

サインアップに電話番号を使用し、回復メール アドレスを使用しないユーザーは、サインイン名として電話番号を使用して Azure AD B2C ディレクトリに記録されます。 この電話番号を変更するには、まずヘルプ デスクまたはサポート チームが彼らのアカウントを見つけて、次にその電話番号を更新する必要があります。

[Microsoft Graph](microsoft-graph-operations.md) を使用すれば、電話番号 (サインイン名) でユーザーを検索することができます。

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

次に例を示します。

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>次のステップ

電話のサインアップとサインインのカスタム ポリシー スターター パック (およびその他のスターター パック) を GitHub で見つけることができます。[Azure-Samples/active-directory-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless][starter-pack-phone] スターター パックのポリシー ファイルには、多要素認証の技術プロファイルと電話番号要求変換が使用されています。
* [Azure AD Multi-Factor Authentication 技術プロファイルを定義する](multi-factor-auth-technical-profile.md)
* [電話番号要求変換を定義する](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
