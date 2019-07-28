---
title: ユーザー フローを通じてアクセス トークンをご自身のアプリケーションに渡す - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C のユーザー フローを通じて、OAuth2.0 ID プロバイダーのアクセス トークンを要求として渡す方法について学習します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: db4b799aa31a4132609b0dd158b65070fb2474b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510965"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のユーザー フローを通じてアクセス トークンをご自身のアプリケーションに渡す

> [!NOTE]
> 現在、この機能はパブリック プレビュー段階にあります。

Azure Active Directory (Azure AD) B2C の[ユーザー フロー](active-directory-b2c-reference-policies.md)は、ご自身のアプリケーションのユーザーに、ID プロバイダーを使用してサインアップまたはサインインする機会を提供します。 ジャーニーが開始されると、Azure AD B2C は ID プロバイダーから[アクセス トークン](active-directory-b2c-reference-tokens.md)を受け取ります。 Azure AD B2C はそのトークンを使用して、そのユーザーに関する情報を取得します。 ユーザー フローで要求を有効にして、Azure AD B2C に登録するアプリケーションにそのトークンを渡します。

Azure AD B2C は現在、[Facebook](active-directory-b2c-setup-fb-app.md) や [Google](active-directory-b2c-setup-goog-app.md) などの [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) ID プロバイダーのアクセス トークンのみを渡すことができます。 その他すべての ID プロバイダーについては、要求が空で返されます。

## <a name="prerequisites"></a>前提条件

- 対象のアプリケーションが [v2 のユーザー フロー](user-flow-versions.md) を使用している。
- 対象のユーザー フローが OAuth 2.0 ID プロバイダーを使用して構成されている。

## <a name="enable-the-claim"></a>要求を有効にする

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリとサブスクリプション] フィルター**を選択し、ご利用のテナントが含まれるディレクトリを選択します。
3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
4. **[ユーザー フロー (ポリシー)]** を選択し、ご自身のユーザー フローを選択します。 たとえば、**B2C_1_signupsignin1** などです。
5. **[アプリケーション クレーム]** を選択します。
6. **[ID プロバイダーのアクセス トークン]** 要求を有効にします。

    ![[ID プロバイダーのアクセス トークン] 要求を有効にする](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. **[保存]** をクリックしてユーザー フローを保存します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

Azure AD B2C でアプリケーションをテスト中に、Azure AD B2C トークンを `https://jwt.ms` に返して、その中の要求を見直すと便利なことがあります。

1. ユーザー フローの [概要] ページで、 **[ユーザー フローを実行します]** を選択します。
2. **[アプリケーション]** には、前に登録したアプリケーションを選択します。 以下の例でトークンを表示するには、 **[応答 URL]** に `https://jwt.ms` が表示される必要があります。
3. **[ユーザー フローを実行します]** をクリックし、その後ご自身のアカウントの資格情報でサインインします。 ID プロバイダーのアクセス トークンは **idp_access_token** 要求に表示されます。

    次の例のようなコードが表示されます。

    ![デコードされたトークン](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.png)

## <a name="next-steps"></a>次の手順

[Azure AD B2C トークンの概要](active-directory-b2c-reference-tokens.md)に関する記事で詳細を参照してください。




