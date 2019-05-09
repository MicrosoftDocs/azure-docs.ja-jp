---
title: Azure Active Directory B2C のユーザー フロー | Microsoft Docs
description: Azure Active Directory B2C の拡張可能なポリシー フレームワークと、さまざまなユーザー フローの作成方法の詳細を説明します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 67db13c8a36977f2614ba7b0e263919bd0405bc7
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2019
ms.locfileid: "56585595"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のユーザー フロー

Azure Active Directory (Azure AD) B2C の拡張可能なポリシー フレームワークは、このサービスの中核となる強みです。 ポリシーには、サインアップ、サインイン、プロファイル編集などの ID エクスペリエンスが完全に記述されています。 最も一般的な ID タスクを設定しやすくするために、Azure AD B2C ポータルには、**ユーザー フロー**という事前定義済みで構成できるポリシーが用意されています。 

## <a name="what-are-user-flows"></a>ユーザー フローとは

ユーザー フローを使用すると、次の設定を構成することで、アプリケーション内の動作を制御できます。

- サインインに使用するアカウントの種類 (Facebook などのソーシャル アカウントやローカル アカウント)
- コンシューマーから収集される属性 (名、郵便番号、靴のサイズなど)
- Azure Multi-Factor Authentication
- ユーザー インターフェイスのカスタマイズ
- アプリケーションがトークンで要求として受け取る情報 

テナント内でさまざまな種類のユーザー フローを多数作成し、必要に応じてそれらをアプリケーションで使用できます。 ユーザー フローは、アプリケーション間で再利用することができます。 この柔軟性により、最小限のコード変更で、またはコードをまったく変更せずに、ID エクスペリエンスを定義および変更できます。 アプリケーションでは、ユーザー フロー パラメーターを含む標準の HTTP 認証要求を使用して、ユーザー フローがトリガーされます。 カスタマイズされた[トークン](active-directory-b2c-reference-tokens.md)は応答として受信されます。 

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

Azure portal では、[ユーザー フローの新しいバージョン](user-flow-versions.md)が常時追加されています。 Azure AD B2C を使用して開始する場合は、テスト済みのユーザー フローを使用することをお勧めします。 新しいユーザー フローを作成するときに、必要なユーザー フローを **[推奨]** タブから選択します。

現在推奨されているユーザー フローは次のとおりです。

- **サインアップとサインイン**: サインアップ エクスペリエンスとサインイン エクスペリエンスの両方を 1 つの構成で処理します。 ユーザーは、状況に応じて正しいパスに誘導されます。 **サインアップ** ユーザー フローまたは**サインイン** ユーザー フローには、このユーザー フローを使用することをお勧めします。
- **プロファイル編集**: ユーザーが自身のプロファイル情報を編集できるようにします。
- **パスワードのリセット**: ユーザーが自分のパスワードをリセットできるかどうか、およびその方法を構成できるようにします。

## <a name="linking-user-flows"></a>ユーザー フローのリンク

ローカル アカウントを使用した**サインアップまたはサインイン** ユーザー フローには、エクスペリエンスの最初のページに **[パスワードを忘れた場合]** リンクが含まれます。 このリンクをクリックしても、パスワード リセット ユーザー フローは自動的にはトリガーされません。 

代わりに、エラー コード `AADB2C90118` がアプリケーションに返されます。 アプリケーションは、パスワードをリセットする特定のユーザー フローを実行してこのエラー コードを処理する必要があります。 例を参照するには、ユーザー フローへのリンクを示す[簡単な ASP.NET サンプル](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)をご覧ください。

## <a name="email-address-storage"></a>メール アドレスの保存

メール アドレスは、ユーザー フローの一部として必要な場合があります。 ユーザーがソーシャル ID プロバイダーを使用して認証する場合、メール アドレスは **otherMails** プロパティに格納されます。 ローカル アカウントがユーザー名に基づいている場合、メール アドレスは強力な認証の詳細プロパティに格納されます。 ローカル アカウントがメール アドレスに基づいている場合、メール アドレスは **signInNames** プロパティに格納されます。
 
これらのいずれの場合でも、メール アドレスが検証される保証はありません。 テナント管理者は、ローカル アカウントの基本ポリシーで電子メールの検証を無効にできます。 メール アドレスの検証が有効になっている場合でも、アドレスがソーシャル ID プロバイダーから提供されているもので、変更されていない場合は、検証されません。
 
**otherMails** プロパティと **signInNames** プロパティのみが Active Directory Graph API を介して公開されます。 強力な認証の詳細プロパティ内のメール アドレスは使用できません。

## <a name="next-steps"></a>次の手順

推奨されるユーザー フローを作成する手順については、[チュートリアル:ユーザー フローの作成](tutorial-create-user-flows.md)の指示に従います。


