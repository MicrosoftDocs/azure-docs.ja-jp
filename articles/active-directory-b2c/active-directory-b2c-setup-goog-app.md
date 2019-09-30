---
title: Google アカウントでのサインアップおよびサインインを設定する - Azure Active Directory B2C
description: Azure Active Directory B2C を使用するアプリケーションで Google アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 158bb3b1329d2ad2cbd55ecf44f980966e2546e2
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065186"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Google アカウントでのサインアップおよびサインインを設定する

## <a name="create-a-google-application"></a>Google アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で [ID プロバイダー](active-directory-b2c-reference-oauth-code.md)として Google アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Google アカウントを持っていない場合は、[https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) でサインアップできます。

1. Google アカウントの資格情報で [Google Developers Console](https://console.developers.google.com/) にサインインします。
1. ページの左上隅にあるプロジェクトの一覧を選択し、 **[新しいプロジェクト]** を選択します。
1. **プロジェクト名**を入力し、 **[作成]** をクリックして、新しいプロジェクトを使用していることを確認します。
1. 左側のメニューで **[Credentials (資格情報)]** を選択して、 **[Create credentials (資格情報を作成)]**  >  **[Oauth client ID (Oauth クライアント ID)]** を選択します。
1. **[アプリケーションの種類]** で **[Web アプリケーション]** を選択します。
1. アプリケーションの**名前**を指定します。 **[承認済みの JavaScript 生成元]** に「`https://your-tenant-name.b2clogin.com`」と入力し、 **[承認済みのリダイレクト URI]** に「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-tenant-name` をテナントの名前に置き換えます。 テナントが Azure AD B2C に大文字で定義されている場合でも、テナント名を入力するときに、すべての小文字を使用する必要があります。
1. **Create** をクリックしてください。
1. **[クライアント ID]** と **[クライアント シークレット]** の値をコピーします。 テナントで ID プロバイダーとして Google を構成するには、両方の値が必要です。 **[クライアント シークレット]** は、重要なセキュリティ資格情報です。

## <a name="configure-a-google-account-as-an-identity-provider"></a>ID プロバイダーとして Google アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[Google]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Google*」とします。
1. **[クライアント ID]** には、前に作成した Google アプリケーションのクライアント ID を入力します。
1. **[クライアント シークレット]** には、記録したクライアント シークレットを入力します。
1. **[保存]** を選択します。
