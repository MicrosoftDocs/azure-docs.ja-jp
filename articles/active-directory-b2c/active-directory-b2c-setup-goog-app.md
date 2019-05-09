---
title: Google アカウントでのサインアップとサインインを設定する - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで Google アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1d4f7460d53e721ca1cba7aba6c6ef9c45111ee5
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58435442"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Google アカウントでのサインアップおよびサインインを設定する

## <a name="create-a-google-application"></a>Google アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で [ID プロバイダー](active-directory-b2c-reference-oauth-code.md)として Google アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Google アカウントを持っていない場合は、[https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) で取得できます。

1. Google アカウントの資格情報で [Google Developers Console](https://console.developers.google.com/) にサインインします。
2. ページの左上隅にあるプロジェクトの一覧を選択し、**[新しいプロジェクト]** を選択します。
3. **プロジェクト名**を入力し、**[作成]** をクリックして、新しいプロジェクトを使用していることを確認します。
4. 左側のメニューで **[Credentials (資格情報)]** を選択して、**[Create credentials (資格情報を作成)]**  >  **[Oauth client ID (Oauth クライアント ID)]** を選択します。
5. **[アプリケーションの種類]** で **[Web アプリケーション]** を選択します。
6. アプリケーションの**名前**を指定します。**[承認済みの JavaScript 生成元]** に「`https://your-tenant-name.b2clogin.com`」と入力し、**[承認済みのリダイレクト URI]** に「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-tenant-name` をテナントの名前に置き換えます。 テナントが Azure AD B2C に大文字で定義されている場合でも、テナント名を入力するときに、すべての小文字を使用する必要があります。
7. **Create** をクリックしてください。
8. **[クライアント ID]** と **[クライアント シークレット]** の値をコピーします。 テナントで ID プロバイダーとして Google を構成するには、両方の値が必要です。 **[クライアント シークレット]** は、重要なセキュリティ資格情報です。

## <a name="configure-a-google-account-as-an-identity-provider"></a>ID プロバイダーとして Google アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]**、**[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*Google*」などと入力します。
6. **[ID プロバイダーの種類]** を選択し、**[Google]** を選択して、**[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップします]** を選択し、**[クライアント ID]** として前に記録したクライアント ID を入力し、前に作成した Google アカウント アプリケーションの **[クライアント シークレット]** として記録したクライアント シークレットを入力します。
8. **[OK]** をクリックし、**[作成]** をクリックして Google の構成を保存します。

