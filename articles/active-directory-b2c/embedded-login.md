---
title: カスタム ポリシーを使用して Azure Active Directory B2C ユーザー インターフェイスをアプリに埋め込む
titleSuffix: Azure AD B2C
description: カスタム ポリシーを使用して Azure Active Directory B2C ユーザー インターフェイスをアプリに埋め込む方法について説明します
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4fbb09a2687673ef96252d8bdb0523d0c5f235ae
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772027"
---
# <a name="embedded-sign-in-experience"></a>埋め込みサインイン エクスペリエンス

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

より簡単なサインイン エクスペリエンスを実現するために、ユーザーを別のサインイン ページにリダイレクトしたり、ポップアップ ウィンドウを生成したりすることを回避できます。 インライン フレーム要素 `<iframe>` を使用すると、Azure AD B2C サインイン ユーザー インターフェイスを Web アプリケーションに直接埋め込むことができます。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="web-application-embedded-sign-in"></a>Web アプリケーションの埋め込みサインイン

インライン フレーム要素 `<iframe>` は、HTML5 Web ページにドキュメントを埋め込むために使用されます。 次の例に示すように、iframe 要素を使用して、Azure AD B2C サインイン ユーザー インターフェイスを Web アプリケーションに直接埋め込むことができます。

![ホバリング DIV エクスペリエンスを使用したログイン](media/embedded-login/login-hovering.png)

iframe を使用する場合は、次の点を考慮してください。

- 埋め込みサインインでサポートされるのは、ローカル アカウントのみです。 ほとんどのソーシャル ID プロバイダー (Google や Facebook など) は、サインイン ページがインライン フレーム内にレンダリングされるのをブロックします。
- iframe 内の Azure AD B2C セッション Cookie はサードパーティの Cookie と見なされるため、特定のブラウザー (シークレット モードの Safari や Chrome など) はこれらの Cookie をブロックまたはクリアし、結果的に望ましくないユーザー エクスペリエンスになります。 この問題を回避するには、アプリケーション ドメイン名と Azure AD B2C ドメインが "*同じオリジン*" を持つようにします。 同じオリジンを使用するには Azure AD B2C テナントの[カスタム ドメインを有効にし](custom-domain.md)、同じオリジンで Web アプリを構成します。 たとえば、 https://app.contoso.com でホストされているアプリケーションは、 https://login.contoso.com で実行されている Azure AD B2C と同じオリジンを持ちます。

## <a name="prerequisites"></a>前提条件

* 「[Active Directory B2C でのカスタム ポリシーの概要](custom-policy-get-started.md)」にある手順を完了する。
* ポリシーの[カスタム ドメインを有効にする](custom-domain.md)。

## <a name="configure-your-policy"></a>ポリシーを構成する

Azure AD B2C ユーザー インターフェイスの iframe への埋め込みを許可するには、コンテンツ セキュリティ ポリシー `Content-Security-Policy` とフレーム オプション `X-Frame-Options` を Azure AD B2C HTTP 応答ヘッダーに含める必要があります。 これらのヘッダーにより、アプリケーション ドメイン名で Azure AD B2C ユーザー インターフェイスを実行できるようになります。

[RelyingParty](relyingparty.md) 要素内に **JourneyFraming** 要素を追加します。  **UserJourneyBehaviors** 要素は、**DefaultUserJourney** の後にする必要があります。 **UserJourneyBehavors** 要素は、次の例のようになります。

```xml
<!--
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" /> -->
  <UserJourneyBehaviors> 
    <JourneyFraming Enabled="true" Sources="https://somesite.com https://anothersite.com" /> 
  </UserJourneyBehaviors>
<!--
</RelyingParty> -->
```

**Sources** 属性には、Web アプリケーションの URI が含まれます。 各 URI の間にはスペースを追加します。 各 URI は次の要件を満たしている必要があります。

- URI はアプリケーションによって信頼および所有されている必要があります。
- URI には、https スキームを使用する必要があります。  
- Web アプリの完全な URI を指定する必要があります。 ワイルドカードはサポートされていません。

さらに、アプリケーションのページで Content-Security-Policy および X-Frame-Options ヘッダーをそれぞれ設定して、iframe への独自のドメイン名の埋め込みをブロックすることもお勧めします。 これにより、iframe の入れ子になった埋め込みに関連する古いブラウザーに関するセキュリティ上の懸念が軽減されます。

## <a name="adjust-policy-user-interface"></a>ポリシー ユーザー インターフェイスを調整する

Azure AD B2C の[ユーザー インターフェイスのカスタマイズ](customize-ui.md)により、ユーザーに表示される HTML および CSS のコンテンツをほぼ完全に制御できます。 コンテンツ定義を使用して HTML ページをカスタマイズするための手順に従います。 Azure AD B2C ユーザー インターフェイスを iframe のサイズに合わせるには、背景と余分なスペースのないクリーンな HTML ページを準備します。  

次の CSS コードは、Azure AD B2C HTML 要素を非表示にして、iframe を埋めるようにパネルのサイズを調整します。

```css
div.social, div.divider {
    display: none;
}

div.api_container{
    padding-top:0;
}

.panel {
    width: 100%!important
}
```

場合によっては、現在表示されている Azure AD B2C ページをアプリケーションに通知することが必要になります。 たとえば、ユーザーがサインアップ オプションを選択したときに、ソーシャル アカウントでサインインするためのリンクを非表示にするか、iframe のサイズを調整することで、アプリケーションに応答させることができます。

現在の Azure AD B2C ページをアプリケーションに通知するには、[JavaScript のポリシーを有効にし](./javascript-and-page-layout.md)、HTML5 post メッセージを使用します。 次の JavaScript コードは、`signUp` を含む post メッセージをアプリに送信し ます。

```javascript
window.parent.postMessage("signUp", '*');
```

## <a name="configure-a-web-application"></a>Web アプリケーションを構成する

ユーザーがサインイン ボタンを選択すると、ユーザーを Azure AD B2C サインイン操作に移動する認可要求が [Web アプリ](code-samples.md#web-apps-and-apis)によって生成されます。 サインインが完了すると、Azure AD B2C から、アプリケーション内の構成済みリダイレクト URI に ID トークン (認証コード) が返されます。

埋め込みログインをサポートするため、iframe **src** プロパティは、`/account/SignUpSignIn` などのサインイン コ ントローラーを指します。これにより、認可要求が生成され、ユーザーが Azure AD B2C ポリシーにリダイレクトされます。

```html
<iframe id="loginframe" frameborder="0" src="/account/SignUpSignIn"></iframe>
``` 

ID トークンがアプリケーションによって受信され、検証されると、認可フローが完了し、アプリケーションでユーザーが認識および信頼されます。 認可フローは iframe 内で発生するため、メイン ページを再度読み込む必要があります。 ページの再読み込みの後、サインイン ボタンが "サインアウト" に変わり、ユーザー名が UI に表示されます。  

次の例は、サインインのリダイレクト URI によりメイン ページが更新される方法を示しています。

```javascript
window.top.location.reload();
```

### <a name="add-sign-in-with-social-accounts-to-a-web-app"></a>Web アプリに ソーシャル アカウントでのサインインを追加する

ソーシャル ID プロバイダーは、インライン フレームでのサインイン ページのレンダリングをブロックします。 ソーシャル アカウントに個別のポリシーを使用することも、ローカルとソーシャルのアカウントによるサインインとサインアップの両方に 1 つのポリシーを使用することもできます。 そのとき、`domain_hint` クエリ文字列パラメーターを使用できます。 ドメイン ヒント パラメーターを設定すると、ユーザーはソーシャル ID プロバイダーのサインイン ページに直接移動します。

アプリケーションに、ソーシャル アカウントによるサインインのボタンを追加します。 ユーザーがいずれかのソーシャル アカウントのボタンをクリックしたときに、そのコントロールによってポリシー名を変更するか、ドメイン ヒントパラメーターを設定する必要があります。

<!-- TBD: add a diagram -->

リダイレクト URI は、iframe で使用されるリダイレクト URI と同じにすることができます。 ページの再読み込みは、スキップできます。

## <a name="configure-a-single-page-application"></a>シングルページ アプリケーションを構成する

シングルページ アプリケーションの場合は、iframe に読み込む 2 つ目の "サインイン" HTML ページも必要になります。 このサインイン ページは、認証コードを生成してトークンを返す認証ライブラリ コードをホスティングします。

シングルページ アプリケーションにアクセス トークンが必要な場合は、JavaScript コードを使用して、iframe およびそれを含むオブジェクトからアクセス トークンを取得します。

> [!NOTE]
> 現在、iframe での MSAL 2.0 の実行はサポートされていません。

次のコードは、メイン ページで実行され、iframe の JavaScript コードを呼び出す例です。

```javascript
function getToken()
{
  var token = document.getElementById("loginframe").contentWindow.getToken("adB2CSignInSignUp");

  if (token === "LoginIsRequired")
    document.getElementById("tokenTextarea").value = "Please login!!!"
  else
    document.getElementById("tokenTextarea").value = token.access_token;
}

function logOut()
{
  document.getElementById("loginframe").contentWindow.policyLogout("adB2CSignInSignUp", "B2C_1A_SignUpOrSignIn");
}
```

## <a name="next-steps"></a>次のステップ

次の関連記事を参照してください。

- [ユーザー インターフェイスのカスタマイズ](customize-ui.md)
- [RelyingParty](relyingparty.md) 要素のリファレンス
- [JavaScript のポリシーを有効にする](./javascript-and-page-layout.md)
- [コード サンプル](code-samples.md)

::: zone-end
