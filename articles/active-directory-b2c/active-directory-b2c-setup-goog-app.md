---
title: "Azure Active Directory B2C: Google+ の構成 | Microsoft Docs"
description: "Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、Google+ アカウントを使用するコンシューマーにサインアップとサインインを提供します。"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 4dcca66f-29e4-4b4d-8840-50baad736bd7
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: e37c48d6c92a8a2cd480458abdff0a3a1ca9338f
ms.openlocfilehash: dbc544ad4dbb651aeb24faf2f89074c7aa59cf2a


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure Active Directory B2C: Google+ アカウントでコンシューマーにサインアップおよびサインインを提供する
## <a name="create-a-google-application"></a>Google+ アプリケーションを作成する
Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Google+ を使用するには、Google+ アプリケーションを作成し、適切なパラメーターを提供する必要があります。 そのためには Google+ アカウントが必要です。 アカウントがない場合は、 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)で取得できます。

1. [Google Developers Console](https://console.developers.google.com/) に移動し、Google+ アカウントの資格情報でサインインします。
2. **[プロジェクトの作成]** をクリックし、**[プロジェクト名]** を入力して、**[作成]** をクリックします。
   
    ![Google+ - 作業開始](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![Google+ - 新しいプロジェクト](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. 左側のナビゲーションで、**[API マネージャー]** をクリックし、**[認証情報]** をクリックします。
4. 上部の **[OAuth 同意画面]** タブをクリックします。
   
    ![Google+ - 認証情報](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. 有効な **[メール アドレス]** を選択または指定し、**サービス名**を入力して、**[保存]** をクリックします。
   
    ![Google+ - OAuth 同意画面](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. **[新しい資格情報]** をクリックし、**[OAuth クライアント ID]** を選択します。
   
    ![Google+ - OAuth 同意画面](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. **[アプリケーションの種類]** で **[Web アプリケーション]** を選択します。
   
    ![Google+ - OAuth 同意画面](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. アプリケーションの**名前**を指定します。**[承認済みの JavaScript 生成元]** フィールドに「`https://login.microsoftonline.com`」と入力し、**[承認済みのリダイレクト URI]** フィールドに「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」と入力します。 **{tenant}** は、実際のテナントの名前 (例: contosob2c.onmicrosoft.com) に置き換えます。 **{tenant}** の値は大文字小文字が区別されます。 **[作成]**をクリックします。
   
    ![Google+ - クライアント ID の作成](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. **[クライアント ID]** と **[クライアント シークレット]** の値をコピーします。 テナントで ID プロバイダーとして Google+ を構成するには、両方の値が必要です。 **[クライアント シークレット]** は、重要なセキュリティ資格情報です。
   
    ![Google+ - クライアント シークレット](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>テナントで ID プロバイダーとして Google+ を構成する
1. この手順に従って、Azure Portal で [B2C 機能ブレードに移動](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) します。
2. B2C 機能ブレードで、 **[ID プロバイダー]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。 たとえば、「G+」などと入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[Google]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーを設定する]** をクリックし、前に作成した Google+ アプリケーションのクライアント ID とクライアント シークレットを入力します。
7. **[OK]** をクリックし、**[作成]** をクリックして Google+ の構成を保存します。




<!--HONumber=Dec16_HO5-->


