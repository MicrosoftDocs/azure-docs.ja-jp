---
title: Twitter 認証の構成 - Azure App Service
description: App Services アプリケーションに Twitter 認証を構成する方法について説明します。
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 51a2ac93fd2d863855c820ba147418c5397c2a89
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53411416"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Twitter ログインを使用するように App Service アプリケーションを構成する方法
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、認証プロバイダーとして Twitter を使用するように Azure App Services を構成する方法を示します。

このトピックの手順を完了するには、電子メール アドレスと電話番号を検証済みの Twitter アカウントが必要になります。 新しい Twitter アカウントを作成するには、<a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a> にアクセスしてください。

## <a name="register"> </a>Twitter にアプリケーションを登録する
1. [Azure Portal]にログオンし、アプリケーションに移動します。 **[URL]** をコピーします。 この URL は、Twitter アプリの構成で使用します。
2. [Twitter Developers] の Web サイトに移動し、Twitter アカウント資格情報でサインインして、 **[Create New App]** をクリックします。
3. 新しいアプリの **[Name (名前)]** と **[Description (説明)]** を入力します。 **[Website (Web サイト)]** 値にアプリケーションの **URL** を貼り付けます。 次に、**[Callback URL (コールバック URL)]** に先ほどコピーした**コールバック URL** を貼り付けます。 この URL は、モバイル アプリ ゲートウェイの後にパス */.auth/login/twitter/callback* を追加したフォーマットです。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/twitter/callback`」のように入力します。 HTTPS スキームを使用していることを確認します。
4. ページの下部で、条項を読み、同意します。 **[Create your Twitter application]** をクリックします。 これでアプリケーションが登録され、アプリケーションの詳細が表示されます。
5. **[Settings (設定)]** タブをクリックし、**[Allow this application to be used to sign in with Twitter (このアプリケーションが Twitter でのサインインに使用されることを許可する)]** チェック ボックスをオンにして、**[Update Settings (設定の更新)]** をクリックします。
6. **[Keys and Access Tokens]** タブをクリックします。**[Consumer Key (API Key) (コンシューマー キー (API キー))]** と **[Consumer secret (API Secret) (コンシューマー シークレット (API シークレット))]** の値を書き留めます。
   
   > [!NOTE]
   > コンシューマー シークレットは、重要なセキュリティ資格情報です。 このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。
   > 
   > 

## <a name="secrets"> </a>アプリケーションに Twitter 情報を追加する
1. [Azure Portal]に戻り、アプリケーションに移動します。 **[設定]**、**[認証/承認]** の順にクリックします。
2. [認証/承認] 機能が有効になっていない場合は、スイッチを **[オン]** に切り替えます。
3. **[Twitter]** をクリックします。 以前に入手したアプリ ID とアプリ シークレットの値を貼り付けます。 次に、 **[OK]** をクリックします
   
   ![][1]
   
   App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。
4. (省略可能) Twitter によって認証されたユーザーしかサイトにアクセスできないように制限するには、**[要求が認証されない場合に実行するアクション]** を **[Twitter]** に設定します。 この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Twitter にリダイレクトされます。
5. **[Save]** をクリックします。

これで、アプリケーションで認証に Twitter を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
