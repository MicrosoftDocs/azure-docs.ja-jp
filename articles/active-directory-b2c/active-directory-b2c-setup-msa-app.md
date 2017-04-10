---
title: "Azure Active Directory B2C: Microsoft アカウントの構成 | Microsoft Docs"
description: "Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、Microsoft アカウントを使用するコンシューマーにサインアップとサインインを提供します。"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 06407322-142c-4cb3-9106-a8d752c4c853
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: e37c48d6c92a8a2cd480458abdff0a3a1ca9338f
ms.openlocfilehash: 866d2b54b0b517cb9984fd5e98840fe3a6c69965


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure Active Directory B2C: Microsoft アカウントでコンシューマーにサインアップおよびサインインを提供する
## <a name="create-a-microsoft-account-application"></a>Microsoft アカウント アプリケーションを作成する
Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Microsoft アカウントを使用するには、Microsoft アカウント アプリケーションを作成し、適切なパラメーターを提供する必要があります。 そのためには Microsoft アカウントが必要です。 アカウントがない場合は、 [https://www.live.com/](https://www.live.com/)で取得できます。

1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) に移動し、Microsoft アカウントの資格情報でサインインします。
2. **[アプリの追加]**をクリックします。
   
    ![Microsoft アカウント - 新しいアプリケーションの追加](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. アプリケーションの**名前**を入力し、**[アプリケーションの作成]** をクリックします。
   
    ![Microsoft アカウント - アプリケーション名](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. **[アプリケーション ID]**の値をコピーします。 この値は、テナントの ID プロバイダーとして Microsoft アカウントを構成するために必要となります。
   
    ![Microsoft account - Application Id](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. **[プラットフォームの追加]** をクリックし、**[Web]** を選択します。
   
    ![Microsoft account - Add platform](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![Microsoft account - Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. **[リダイレクト URI]** フィールドに、「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」と入力します。 **{tenant}** は、実際のテナントの名前 (例: contosob2c.onmicrosoft.com) に置き換えます。
   
    ![Microsoft アカウント - リダイレクト URL](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. **[アプリケーション シークレット]** セクションの **[新しいパスワードを生成]** をクリックします。 画面に表示される新しいパスワードをコピーします。 この値は、テナントの ID プロバイダーとして Microsoft アカウントを構成するために必要となります。 このパスワードは重要なセキュリティ資格情報です。
   
    ![Microsoft account - Generate new password](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![Microsoft account - New password](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. **[詳細オプション]** セクションの **[Live SDK サポート]** というチェック ボックスをオンにします。 [ **Save**] をクリックします。
   
    ![Microsoft account - Live SDK support](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>テナントで ID プロバイダーとして Microsoft アカウントを構成する
1. この手順に従って、Azure Portal で [B2C 機能ブレードに移動](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) します。
2. B2C 機能ブレードで、 **[ID プロバイダー]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。 たとえば、「MSA」と入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[Microsoft アカウント]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーを設定する]** をクリックし、前に作成した Microsoft アカウント アプリケーションのアプリケーション ID とパスワードを入力します。
7. **[OK]** をクリックし、**[作成]** をクリックして Microsoft アカウントの構成を保存します。




<!--HONumber=Feb17_HO2-->


