---
title: Azure Active Directory B2C を使用して Twitter アカウントでのサインアップおよびサインインを設定する
description: Azure Active Directory B2C を使用するアプリケーションで Twitter アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5cb5c5813bff8c0df5b119ea1cadf4f2e7c1696a
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811399"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Twitter アカウントでのサインアップおよびサインインを設定する

## <a name="create-an-application"></a>アプリケーションの作成

Azure AD B2C で ID プロバイダーとして Twitter を使用するには、Twitter アプリケーションを作成する必要があります。 まだ Twitter アカウントを持っていない場合は、[https://twitter.com/signup](https://twitter.com/signup) でサインアップできます。

1. Twitter アカウント資格情報を使用して [Twitter Developers](https://developer.twitter.com/en/apps) Web サイトにサインインします。
1. **[アプリの作成]** を選択します。
1. **アプリ名**と**アプリケーションの説明**を入力します。
1. **Web サイトの URL** で、`https://your-tenant.b2clogin.com`を入力します。 `your-tenant` をテナントの名前に置き換えます。 たとえば、「 https://contosob2c.b2clogin.com 」のように入力します。
1. **[Callback URL]** に「`https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`」と入力します。 `your-tenant` を自分のテナント名に置き換え、`your-user-flow-Id` を自分のユーザー フローの識別子に置き換えます。 たとえば、「 `b2c_1A_signup_signin_twitter` 」のように入力します。 テナント名とユーザー フロー ID が Azure AD B2C に大文字で定義されている場合でも、それらを入力するときに、すべて小文字を使用する必要があります。
1. ページ下部の使用条件を確認して同意し、 **[作成]** を選択します。
1. **[アプリの詳細]** ページで、 **[編集 > 詳細の編集]** を選択して、 **[Twitter でサインインを有効にする]** のチェック ボックスにチェックを入れ、 **[保存]** を選択します。
1. **[キーとトークン]** を選択し、後で使用される **[コンシューマー API キー]** と **[コンシューマー API のシークレット キー]** の値を記録します。

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>テナントで ID プロバイダーとして Twitter を構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[Twitter]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Twitter*」とします。
1. **[クライアント ID]** には、前に作成した Twitter アプリケーションのコンシューマー API キーを入力します。
1. **[クライアント シークレット]** には、記録したコンシューマー API シークレット キーを入力します。
1. **[保存]** を選択します。
