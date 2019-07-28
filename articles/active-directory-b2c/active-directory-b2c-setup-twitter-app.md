---
title: Azure Active Directory B2C を使用して Twitter アカウントでのサインアップおよびサインインを設定する | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで Twitter アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: 706807039ac7c13cd88bc8ac99acf0d7ece3b384
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508158"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Twitter アカウントでのサインアップおよびサインインを設定する

## <a name="create-an-application"></a>アプリケーションの作成

Azure AD B2C で ID プロバイダーとして Twitter を使用するには、Twitter アプリケーションを作成する必要があります。 まだ Twitter アカウントを持っていない場合は、[https://twitter.com/signup](https://twitter.com/signup) で取得できます。

1. Twitter アカウント資格情報を使用して [Twitter Developers](https://developer.twitter.com/en/apps) Web サイトにサインインします。
2. **[アプリの作成]** を選択します。
3. **アプリ名**と**アプリケーションの説明**を入力します。
4. **Web サイトの URL** で、`https://your-tenant.b2clogin.com`を入力します。 `your-tenant` をテナントの名前に置き換えます。 たとえば、「 https://contosob2c.b2clogin.com 」のように入力します。
5. **[Callback URL]** に「`https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`」と入力します。 `your-tenant` を自分のテナント名に置き換え、`your-user-flow-Id` を自分のユーザー フローの識別子に置き換えます。 たとえば、「 `b2c_1A_signup_signin_twitter` 」のように入力します。 テナントが Azure AD B2C に大文字で定義されている場合でも、テナント名を入力するときに、すべての小文字を使用する必要があります。
6. ページ下部の使用条件を確認して同意し、 **[作成]** を選択します。
7. **[アプリの詳細]** ページで、 **[編集 > 詳細の編集]** を選択して、 **[Twitter でサインインを有効にする]** のチェック ボックスにチェックを入れ、 **[保存]** を選択します。
8. **[キーとトークン]** を選択し、後で使用される **[コンシューマー API キー]** と **[コンシューマー API のシークレット キー]** の値を記録します。

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>テナントで ID プロバイダーとして Twitter を構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]** 、 **[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*Twitter*」と入力します。
6. **[ID プロバイダーの種類]** 、 **[Twitter]** の順に選択し、 **[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップする]** を選択し、 **[クライアント ID]** のAPI キーと **[クライアント シークレット]** のAPI シークレット キーを入力します。
8. **[OK]** をクリックし、 **[作成]** をクリックして Twitter の構成を保存します。
