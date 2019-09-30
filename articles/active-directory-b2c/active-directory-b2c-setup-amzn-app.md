---
title: Amazon アカウントでのサインアップおよびサインインを設定する - Azure Active Directory B2C
description: Azure Active Directory B2C を使用するアプリケーションで Amazon アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bddbd9b588d8ffc2ace3a937fa2c72876eb5e4e9
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065223"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Amazon アカウントでのサインアップおよびサインインを設定する

## <a name="create-an-amazon-application"></a>Amazon アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で [ID プロバイダー](active-directory-b2c-reference-oauth-code.md)として Amazon アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Amazon アカウントを持っていない場合は、[https://www.amazon.com/](https://www.amazon.com/) でサインアップできます。

1. Amazon アカウントの資格情報で [Amazon Developer Center](https://login.amazon.com/) にサインインします。
1. まだ行っていない場合は、 **[Sign Up (サインアップ)]** をクリックして、開発者登録手順に従い、ポリシーを受け入れます。
1. **[Register new application (新しいアプリケーションの登録)]** を選択します。
1. **[Name (名前)]** 、 **[Description (説明)]** 、および **[Privacy Notice URL (プライバシーに関する声明の URL)]** を入力して、 **[Save (保存)]** をクリックします。 プライバシーに関する声明は、プライバシー情報をユーザーに提供するページです。
1. **[Web Settings (Web 設定)]** セクションで、 **[Client ID (クライアント ID)]** の値をコピーします。 **[Show Secret (シークレットの表示)]** を選択して、クライアント シークレットを取得しコピーします。 テナントで ID プロバイダーとして Amazon アカウントを構成するには、この両方が必要です。 **[Client Secret]** は、重要なセキュリティ資格情報です。
1. **[Web Settings (Web 設定)]** セクションで、 **[Edit (編集)]** を選択し、 **[Allowed JavaScript Origins (許可される JavaScript の配信元)]** で「`https://your-tenant-name.b2clogin.com`」を入力し、 **[Allowed Return URLs (許可されるリターン URL)]** で「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」を入力します。 `your-tenant-name` をテナントの名前に置き換えます。 テナントが Azure AD B2C に大文字で定義されている場合でも、テナント名を入力するときに、すべての小文字を使用する必要があります。
1. **[Save]** をクリックします。

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>ID プロバイダーとして Amazon アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[Amazon]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Amazon*」とします。
1. **[クライアント ID]** には、前に作成した Amazon アプリケーションのクライアント ID を入力します。
1. **[クライアント シークレット]** には、記録したクライアント シークレットを入力します。
1. **[保存]** を選択します。
