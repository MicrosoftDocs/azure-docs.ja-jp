---
title: Azure Active Directory B2C のユーザー フロー | Microsoft Docs
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C の拡張可能なポリシー フレームワークと、さまざまなユーザー フローの作成方法の詳細を説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 06253b571fd71623501c27fd5b0d9d4013727fc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94840193"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のユーザー フロー

アプリケーションの最も一般的な ID タスクの設定を容易にするため、Azure AD B2C ポータルには、**ユーザー フロー** という事前定義済みで構成できるポリシーが用意されています。 ユーザー フローを使用することで、ユーザーがサインイン、サインアップ、プロファイルの編集、パスワードのリセットなどを行うときに、アプリケーションとどのように対話するかを決定できます。 ユーザー フローを使用すると、次の機能を制御できます。

- サインインに使用するアカウントの種類 (Facebook などのソーシャル アカウントやローカル アカウント)
- コンシューマーから収集される属性 (名、郵便番号、靴のサイズなど)
- Azure AD Multi-Factor Authentication
- ユーザー インターフェイスのカスタマイズ
- アプリケーションがトークンで要求として受け取る情報

テナント内でさまざまな種類のユーザー フローを多数作成し、必要に応じてそれらをアプリケーションで使用できます。 ユーザー フローは、アプリケーション間で再利用することができます。 この柔軟性により、最小限のコード変更で、またはコードをまったく変更せずに、ID エクスペリエンスを定義および変更できます。 アプリケーションでは、ユーザー フロー パラメーターを含む標準の HTTP 認証要求を使用して、ユーザー フローがトリガーされます。 カスタマイズされた[トークン](tokens-overview.md)は応答として受信されます。

次の例では、使用するユーザー フローを指定する "p" クエリ文字列パラメーターを示しています。

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>ユーザー フローのバージョン

Azure AD B2C には、いくつかの種類のユーザー フローがあります。

- **サインアップとサインイン**: サインアップ エクスペリエンスとサインイン エクスペリエンスの両方を 1 つの構成で処理します。 ユーザーは、状況に応じて正しいパスに誘導されます。 また、個別の **サインアップ** または **サインイン** ユーザー フローも含まれています。 ただし、通常は、サインアップとサインインのユーザー フローを組み合わせることをお勧めします。
- **プロファイル編集**: ユーザーが自身のプロファイル情報を編集できるようにします。
- **パスワードのリセット**: ユーザーが自分のパスワードをリセットできるかどうか、およびその方法を構成できるようにします。

ほとんどのユーザー フローの種類には、**推奨** バージョンと **標準** バージョンの両方があります。 詳細については、「[ユーザー フローのバージョン](user-flow-versions.md)」を参照してください。

> [!IMPORTANT]
> 以前に Azure AD B2C のユーザー フローを使用したことがある場合は、ユーザー フローのバージョンを参照する方法が変更されたことにお気づきかもしれません。 これまでは、V1 (実稼働可能) バージョンと V1.1 および V2 (プレビュー) バージョンが提供されていました。 今後は、ユーザー フローが 2 つのバージョンに統合されます。
>
>- **推奨** ユーザー フローは、ユーザー フローの新しいプレビュー バージョンです。 これらは徹底的にテストされ、レガシ **V2** と **v1.1** バージョンのすべての機能が組み合わさっています。 今後は、この新しい推奨ユーザー フローが保守および更新されます。 これらの新しい推奨ユーザー フローに移動すると、新機能がリリースされる度にアクセスできるようになります。
>- **標準** ユーザー フロー (以前の **V1**) は、一般提供されており、実稼働可能なユーザー フローです。 ユーザー フローがミッション クリティカルで、非常に安定したバージョンに依存している場合、これらのバージョンは保守および更新されないことを理解した上で、標準ユーザー フローを使用し続けることができます。
>
>すべてのレガシ プレビュー ユーザー フロー (v1.1 および V2) は、**2021 年 8 月 1 日** までに非推奨となる予定です。 可能な限り、最新の機能と更新プログラムをいつでも利用できるように、できるだけ早く [新しい **推奨** ユーザー フローに切り替える](user-flow-versions.md#how-to-switch-to-a-new-recommended-user-flow)ことを強くお勧めします。

## <a name="linking-user-flows"></a>ユーザー フローのリンク

ローカル アカウントを使用した **サインアップまたはサインイン** ユーザー フローには、エクスペリエンスの最初のページに **[パスワードを忘れた場合]** リンクが含まれます。 このリンクをクリックしても、パスワード リセット ユーザー フローは自動的にはトリガーされません。

代わりに、エラー コード `AADB2C90118` がアプリケーションに返されます。 アプリケーションは、パスワードをリセットする特定のユーザー フローを実行してこのエラー コードを処理する必要があります。 例を参照するには、ユーザー フローへのリンクを示す[簡単な ASP.NET サンプル](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)をご覧ください。

## <a name="email-address-storage"></a>メール アドレスの保存

メール アドレスは、ユーザー フローの一部として必要な場合があります。 ユーザーがソーシャル ID プロバイダーを使用して認証する場合、メール アドレスは **otherMails** プロパティに格納されます。 ローカル アカウントがユーザー名に基づいている場合、メール アドレスは強力な認証の詳細プロパティに格納されます。 ローカル アカウントがメール アドレスに基づいている場合、メール アドレスは **signInNames** プロパティに格納されます。

これらのいずれの場合でも、メール アドレスが検証される保証はありません。 テナント管理者は、ローカル アカウントの基本ポリシーで電子メールの検証を無効にできます。 メール アドレスの検証が有効になっている場合でも、アドレスがソーシャル ID プロバイダーから提供されているもので、変更されていない場合は、検証されません。

**otherMails** プロパティと **signInNames** プロパティのみが Microsoft Graph API を介して公開されます。 強力な認証の詳細プロパティ内のメール アドレスは使用できません。

## <a name="next-steps"></a>次のステップ

推奨されるユーザー フローを作成する手順については、[チュートリアル:ユーザー フローの作成](tutorial-create-user-flows.md)の指示に従います。
