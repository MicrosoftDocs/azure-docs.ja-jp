---
title: カスタム ポリシーを使用した電話でのサインアップとサインイン (プレビュー)
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でカスタム ポリシーを使用して、アプリケーション ユーザーの電話にワンタイム パスワード (OTP) をテキスト メッセージで送信します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d432912cb0442744061500fc01bdd86a4c5d97ef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385350"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Azure AD B2C でカスタム ポリシーを使用した電話でのサインアップとサインインを設定する (プレビュー)

Azure Active Directory B2C (Azure AD B2C) での電話のサインアップとサインインを使用すると、ユーザーは、テキスト メッセージで電話に送信されたワンタイム パスワード (OTP) を使用して、アプリケーションにサインアップしてサインインできます。 ワンタイム パスワードを使用すると、ユーザーがパスワードを忘れたり、パスワードが侵害されたりするリスクを最小限に抑えることができます。

この記事の手順に従ってカスタム ポリシーを使用することで、お客様が電話に送信されたワンタイム パスワードを使用してサインアップし、アプリケーションにサインインできるようになります。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>価格

ワンタイム パスワードは SMS テキスト メッセージを使用してお客様のユーザーに送信されるため、お客様は送信されたメッセージごとに課金される場合があります。 価格情報については、「[Azure Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)」の「**別料金**」セクションを参照してください。

## <a name="prerequisites"></a>前提条件

OTP を設定する前に、次のリソースを用意しておく必要があります。

* [Azure AD B2C テナント](tutorial-create-tenant.md)
* ご利用のテナントに[登録済みの Web アプリケーション](tutorial-register-applications.md)
* テナントにアップロード済みの[カスタム ポリシー](custom-policy-get-started.md)

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>電話でのサインアップとサインインのスターター パックを入手する

まず、電話でのサインアップとサインインのためのカスタム ポリシー ファイルを更新して、Azure AD B2C テナントと連携させます。

次の手順では、[前提条件](#prerequisites)を完了し、[カスタム ポリシー スターター パック][starter-pack] リポジトリをローカル コンピューターに既に複製済みであることを前提としています。

1. [電話のサインアップとサインインのカスタム ポリシー ファイル][starter-pack-phone]をローカルにあるスターター パック リポジトリの複製で検索するか、直接ダウンロードします。 XML ポリシー ファイルは次のディレクトリにあります。

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. 各ファイル内で、文字列 `yourtenant` を、使用している Azure AD B2C テナントの名前に置き換えます。 たとえば、B2C テナントの名前が *contosob2c* であれば、`yourtenant.onmicrosoft.com` のすべてのインスタンスは `contosob2c.onmicrosoft.com` になります。

1. 「[Azure Active Directory B2C でのカスタム ポリシーの概要](custom-policy-get-started.md)」の「[カスタム ポリシーにアプリケーション ID を追加します](custom-policy-get-started.md#add-application-ids-to-the-custom-policy)」の手順を完了します。 この場合は、前提条件を完了したときに登録した 2 つのアプリケーション (*IdentityExperienceFramework* と *ProxyIdentityExperienceFramework*) の**アプリケーション (クライアント) ID** を使用して、`/phone-number-passwordless/`**`Phone_Email_Base.xml`** を更新します。

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

1. **[カスタム ポリシー]** で、 **[B2C_1A_SignUpOrSignInWithPhone]** を選択します。
1. **[アプリケーションの選択]** で、前提条件の完了時に登録した *webapp1* アプリケーションを選択します。
1. **[応答 URL の選択]** で、`https://jwt.ms` を選択します。
1. **[今すぐ実行]** を選択し、電子メール アドレスまたは電話番号を使用してサインアップします。
1. もう一度 **[今すぐ実行]** を選択し、同じアカウントでサインインして、構成が正しく行われていることを確認します。

## <a name="get-user-account-by-phone-number"></a>電話番号でユーザー アカウントを取得する

電話番号でサインアップするが、回復メール アドレスを提供しないユーザーは、サインイン名として電話番号を使用して Azure AD B2C ディレクトリに記録されます。 その後、ユーザーが電話番号の変更を希望する場合は、まずヘルプ デスクまたはサポート チームが彼らのアカウントを見つけて、次にその電話番号を更新する必要があります。

[Microsoft Graph](manage-user-accounts-graph-api.md) を使用すれば、電話番号 (サインイン名) でユーザーを検索することができます。

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

次に例を示します。

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>次のステップ

電話のサインアップとサインインのカスタム ポリシー スターター パック (およびその他のスターター パック) を GitHub で見つけることができます。

[Azure-Samples/active-directory-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless][starter-pack-phone]

スターター パックのポリシー ファイルでは、多要素認証の技術プロファイルと電話番号要求変換が使用されています。

* [Azure Multi-Factor Authentication 技術プロファイルを定義する](multi-factor-auth-technical-profile.md)
* [電話番号要求変換を定義する](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
