---
title: 顧客のサインアップ時のメール検証を無効にする
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で顧客のサインアップ時のメール検証を無効にする方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0f70c8d501a7d56f4bc29e0f2b065760cad625e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585022"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で顧客のサインアップ時のメール検証を無効にする

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

既定で、Azure Active Directory B2C (Azure AD B2C) では、顧客のローカル アカウント (メール アドレスまたはユーザー名を使用してサインアップしたユーザーのアカウント) のメール アドレスが検証されます。 Azure AD B2C では、メール アドレスが有効であることを確認するために、サインアップ時に顧客が検証することを必須にしています。 また、悪意のあるアクターが自動化されたプロセスを使用してアプリケーションに不正なアカウントを生成することを防ぐことができます。

アプリケーション開発者によっては、サインアップ プロセス時にはメールの検証をスキップし、代わりに後で顧客にメール アドレスを検証させることを好みます。 それをサポートするために、電子メールの検証を無効にするように Azure AD B2C を構成することができます。 そうすることで、サインアップ プロセスがよりスムーズになり、開発者はメール アドレスを検証した顧客と検証していない顧客を柔軟に区別することができます。

> [!WARNING]
> サインアップ プロセスでの電子メールの検証を無効にすると、スパムにつながる場合があります。 既定の Azure AD B2C で提供されるメールの検証を無効にする場合は、代替の検証システムを実装することをお勧めします。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>メールの確認を無効にする

::: zone pivot="b2c-user-flow"

次の手順に従って、メールの検証を無効にします。

1. [Azure ポータル](https://portal.azure.com)
1. 上部のメニューにある **[ディレクトリとサブスクリプション]** フィルターを使用して、お使いの Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. メールの検証を無効にするユーザー フローを選択します。 たとえば、*B2C_1_signinsignup* です。
1. **[Page layouts]\(ページ レイアウト\)** を選択します。
1. **[ローカル アカウント サインアップ ページ]** を選択します。
1. **[ユーザー属性]** で **[メール アドレス]** を選択します。
1. **[確認が必要]** ドロップダウンで **[いいえ]** を選択します。
1. **[保存]** を選択します。 このユーザー フローでは、メールの検証が無効になりました。

::: zone-end

::: zone pivot="b2c-custom-policy"
**LocalAccountSignUpWithLogonEmail** 技術プロファイルは [セルフアサート](self-asserted-technical-profile.md)であり、サインアップ フローの間に呼び出されます。 メールの検証を無効にするには、`EnforceEmailVerification` メタデータを false に設定します。 拡張ファイルで LocalAccountSignUpWithLogonEmail 技術プロファイルをオーバーライドします。 

1. お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**</em>です。
1. `ClaimsProviders` 要素を見つけます。 要素が存在しない場合は追加します。
1. 次のクレーム プロバイダーを `ClaimsProviders` 要素に追加します。

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>ポリシーのテスト 

1. [Azure ポータル](https://portal.azure.com)
1. 上部のメニューにある **[ディレクトリとサブスクリプション]** フィルターを使用して、お使いの Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. メールの検証を無効にするユーザー フローを選択します。 たとえば、*B2C_1_signinsignup* です。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** をクリックします
1. 検証なしに、メール アドレスを使用してサインアップできるはずです。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>証明書利用者ファイルを更新し、テストする

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご利用の Azure AD テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターをクリックして、ご利用の Azure AD テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
1. **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシーのアップロード]** を選択し、変更した 2 つのポリシー ファイルをアップロードします。
1. アップロードしたサインアップまたはサインイン ポリシーを選択し、 **[今すぐ実行]** ボタンをクリックします。
1. 検証なしに、メール アドレスを使用してサインアップできるはずです。

::: zone-end


## <a name="next-steps"></a>次のステップ

- [Azure Active Directory B2C 内のユーザー インターフェイスをカスタマイズする](customize-ui-with-html.md)方法を学習する

