---
title: Azure Active Directory B2C のユーザー フローを通じてアクセス トークンをご自身のアプリケーションに渡す | Microsoft Docs
description: Azure Active Directory B2C のユーザー フローを通じて、OAuth2.0 ID プロバイダーのアクセス トークンを要求として渡す方法について学習します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: fd0f8241c07f603089b896dcfc9ece29f1e33d1c
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428206"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のユーザー フローを通じてアクセス トークンをご自身のアプリケーションに渡す

> [!NOTE]
> 現在、この機能はパブリック プレビュー段階にあります。

Azure Active Directory (Azure AD) B2C の[ユーザー フロー](active-directory-b2c-reference-policies.md)は、ご自身のアプリケーションのユーザーに、ID プロバイダーを使用してサインアップまたはサインインする機会を提供します。 ジャーニーが開始されると、Azure AD B2C は ID プロバイダーから[アクセス トークン](active-directory-b2c-reference-tokens.md)を受け取ります。 Azure AD B2C はそのトークンを使用して、そのユーザーに関する情報を取得します。 ユーザー フローで要求を有効にして、Azure AD B2C に登録するアプリケーションにそのトークンを渡します。

Azure AD B2C では、[OAuth 2.0](active-directory-b2c-reference-oauth-code.md) および [OpenID Connect](active-directory-b2c-reference-oidc.md) ID プロバイダーのアクセス トークンを渡すことがサポートされています。 その他すべての ID プロバイダーについては、要求が空で返されます。

## <a name="prerequisites"></a>前提条件

- 対象のアプリケーションが [v2 のユーザー フロー](user-flow-versions.md) を使用している。
- ユーザー フローが OAuth 2.0 または OpenID Connect の ID プロバイダーを使用して構成されている。

## <a name="enable-the-claim"></a>要求を有効にする

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
4. **[ユーザー フロー]** を選択し、ご自身のユーザー フローを選択します。 たとえば、**B2C_1_SignupSignIn** などです。
5. **[アプリケーション クレーム]** を選択します。
6. **[ID プロバイダーのアクセス トークン]** を有効にします。

    ![アプリケーションの要求](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. **[保存]** をクリックしてユーザー フローを保存します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

Azure AD B2C でアプリケーションをテスト中に、Azure AD B2C トークンを `https://jwt.ms` に返して、その中の要求を見直すと便利なことがあります。

1. ユーザー フローの [概要] ページで、**[ユーザー フローを実行します]** を選択します。
2. **[アプリケーション]** には、前に登録したアプリケーションを選択します。 以下の例でトークンを表示するには、**[応答 URL]** に `https://jwt.ms` が表示される必要があります。
3. **[ユーザー フローを実行します]** をクリックし、その後ご自身のアカウントの資格情報でサインインします。 ID プロバイダーのアクセス トークンは **idp_access_token** 要求に表示されます。

    次の例のようなコードが表示されます。

    ![デコードされたトークン](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.png)

## <a name="next-steps"></a>次の手順

[Azure Active Directory のトークン リファレンス](active-directory-b2c-reference-tokens.md)でトークンの詳細について学習する。




