---
title: Amazon アカウントでのサインアップとサインインを設定する - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで Amazon アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: a796826892942879e42b2d8fd22b8cc0208a2174
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508627"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Amazon アカウントでのサインアップおよびサインインを設定する

## <a name="create-an-amazon-application"></a>Amazon アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で [ID プロバイダー](active-directory-b2c-reference-oauth-code.md)として Amazon アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Amazon アカウントを持っていない場合は、[https://www.amazon.com/](https://www.amazon.com/) で取得できます。

1. Amazon アカウントの資格情報で [Amazon Developer Center](https://login.amazon.com/) にサインインします。
2. まだ行っていない場合は、 **[Sign Up (サインアップ)]** をクリックして、開発者登録手順に従い、ポリシーを受け入れます。
3. **[Register new application (新しいアプリケーションの登録)]** を選択します。
4. **[Name (名前)]** 、 **[Description (説明)]** 、および **[Privacy Notice URL (プライバシーに関する声明の URL)]** を入力して、 **[Save (保存)]** をクリックします。 プライバシーに関する声明は、プライバシー情報をユーザーに提供するページです。
5. **[Web Settings (Web 設定)]** セクションで、 **[Client ID (クライアント ID)]** の値をコピーします。 **[Show Secret (シークレットの表示)]** を選択して、クライアント シークレットを取得しコピーします。 テナントで ID プロバイダーとして Amazon アカウントを構成するには、この両方が必要です。 **[Client Secret]** は、重要なセキュリティ資格情報です。
6. **[Web Settings (Web 設定)]** セクションで、 **[Edit (編集)]** を選択し、 **[Allowed JavaScript Origins (許可される JavaScript の配信元)]** で「`https://your-tenant-name.b2clogin.com`」を入力し、 **[Allowed Return URLs (許可されるリターン URL)]** で「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」を入力します。 `your-tenant-name` をテナントの名前に置き換えます。 テナントが Azure AD B2C に大文字で定義されている場合でも、テナント名を入力するときに、すべての小文字を使用する必要があります。
7. **[Save]** をクリックします。

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>ID プロバイダーとして Amazon アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]** 、 **[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*Amazon*」などと入力します。
6. **[ID プロバイダーの種類]** を選択し、 **[Amazon]** を選択して、 **[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップします]** を選択し、 **[クライアント ID]** として前に記録したクライアント ID を入力し、前に作成した Amazon アカウント アプリケーションの **[クライアント シークレット]** として記録したクライアント シークレットを入力します。
8. **[OK]** をクリックし、 **[作成]** をクリックして Amazon の構成を保存します。

