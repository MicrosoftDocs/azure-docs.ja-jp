---
title: "Azure Active Directory B2C: 組み込みのポリシー | Microsoft Docs"
description: "Azure Active Directory B2C の拡張可能なポリシー フレームワークと、さまざまなポリシー タイプの作成方法に関するトピック。"
services: active-directory-b2c
documentationcenter: 
author: sama
manager: mbaldwin
editor: PatAltimore
ms.assetid: 0d453e72-7f70-4aa2-953d-938d2814d5a9
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: sama
ms.openlocfilehash: daad3af089afdf76b930053728bb11a5cf4c2a92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C: 組み込みのポリシー


Azure Active Directory (Azure AD) B2C の拡張可能なポリシー フレームワークは、このサービスの中核となる強みです。 ポリシーには、サインアップ、サインイン、プロファイル編集などのコンシューマーの ID エクスペリエンスが完全に記述されています。 たとえば、サインアップのポリシーを使用して次の設定を構成し、動作を制御できます。

* コンシューマーがアプリケーションにサインアップするために使用できるアカウントの種類 (Facebook などのソーシャル アカウント、または電子メール アドレスなどのローカル アカウント)
* サインアップ中にコンシューマーから収集される属性 (名、郵便番号、靴のサイズなど)
* Azure Multi-Factor Authentication の使用
* すべてのサインアップ ページの外観
* ポリシーの実行が完了したときにアプリケーションが受信する情報 (トークン内で要求として明示される)

テナント内でさまざまな種類のポリシーを複数作成し、必要に応じてそれらをアプリケーションで使用できます。 ポリシーは、アプリケーション間で再利用することができます。 この柔軟性により、開発者は最小限のコード変更で、またはコードをまったく変更せずにコンシューマーの ID エクスペリエンスを定義および変更できます。

ポリシーは、簡単な開発者インターフェイスを使用して入手できます。 アプリケーションは (要求でポリシー パラメーターを渡す) 標準の HTTP 認証要求を使用してポリシーをトリガーし、カスタマイズされたトークンを応答として受信します。 たとえば、サインアップ ポリシーを呼び出す要求とサインイン ポリシーを呼び出す要求の唯一の違いは、"p" クエリ文字列パラメーターで使用されるポリシー名です。

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

ポリシー フレームワークの詳細については、[Enterprise Mobility and Security ブログにある Azure AD B2C に関するこのブログの投稿](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx)を参照してください。

## <a name="create-a-sign-up-or-sign-in-policy"></a>サインアップまたはサインイン ポリシーを作成する

このポリシーは、1 つの構成でコンシューマーのサインアップ エクスペリエンスとサインイン エクスペリエンスの両方を処理します。 コンシューマーは、状況に応じて正しいパス (サインアップまたはサインイン) に誘導されます。 このポリシーはまた、サインアップまたはサインインが成功したときにアプリケーションが受け取るトークンのコンテンツを記述します。サインアップまたはサインイン ポリシーのサンプル コードは、 [こちらで入手できます](active-directory-b2c-devquickstarts-web-dotnet-susi.md)。  サインアップ ポリシーとサインイン ポリシーにはこのポリシーを使用することをお勧めします。  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>サインアップ ポリシーを作成する

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>サインイン ポリシーを作成する

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>プロファイル編集ポリシーを作成する

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>パスワードのリセット ポリシーを作成する

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>サインアップまたはサインイン ポリシーをパスワード リセット ポリシーにリンクするにはどうすればよいですか?
サインアップまたはサインイン ポリシーを (ローカル アカウントで) 作成すると、エクスペリエンスの最初のページに **[Forgot password?] \(パスワードを忘れた場合)** リンクが表示されます。 このリンクをクリックしても、パスワード リセット ポリシーは自動的にはトリガーされません。 

代わりに、エラー コード **`AADB2C90118`** がアプリに返されます。 アプリは、特定のパスワード リセット ポリシーを呼び出すことによって、このエラー コードを処理する必要があります。 詳細については、[ポリシーをリンクする方法を示すサンプル](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)を参照してください。

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>サインアップまたはサインイン ポリシーと、サインアップ ポリシーおよびサインイン ポリシーのどちらを使用すればよいですか?
サインアップ ポリシーおよびサインイン ポリシーより、サインアップまたはサインイン ポリシーを使用することをお勧めします。  

サインアップまたはサインイン ポリシーは、サインイン ポリシーより多くの機能を備えています。 また、ページ UI のカスタマイズを使用したり、ローカライズのより適切なサポートを受けたりすることもできます。 

ポリシーをローカライズする必要がなく、必要なのはブランドのマイナーなカスタマイズ機能だけであり、それにパスワード リセットを組み込みたい場合は、サインイン ポリシーをお勧めします。

## <a name="next-steps"></a>次のステップ
* [トークン、セッション、およびシングル サインオンの構成](active-directory-b2c-token-session-sso.md)
* [コンシューマーのサインアップ時の電子メール検証の無効化](active-directory-b2c-reference-disable-ev.md)

