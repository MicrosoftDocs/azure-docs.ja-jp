---
title: Twitter 認証を構成する
description: App Services アプリまたは Azure Functions アプリ用の ID プロバイダーとして Twitter 認証を構成する方法について説明します。
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 11c913b12b4dcb7d2a5ffa532064b347b82904ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "80519905"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Twitter ログインを使用するように App Service アプリまたは Azure Functions アプリを構成する

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

この記事では、認証プロバイダーとして Twitter を使用するように Azure App Service または Azure Functions を構成する方法を示します。

この記事の手順を完了するには、電子メール アドレスと電話番号を検証済みの Twitter アカウントが必要になります。 新しい Twitter アカウントを作成するには、[twitter.com] にアクセスしてください。

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Twitter にアプリケーションを登録する

1. [Azure portal] にサインインし、お使いのアプリケーションに移動します。 **[URL]** をコピーします。 これは Twitter アプリの構成で使用します。
1. [Twitter Developers] の Web サイトに移動し、Twitter アカウント資格情報でサインインし、 **[Create an app]\(アプリの作成\)** を選択します。
1. 新しいアプリの **[App name]\(アプリ名\)** と **[Application description]\(アプリケーションの説明\)** を入力します。 **[Website URL]\(Web サイトの URL\)** フィールドにアプリケーションの **URL** を貼り付けます。 **[Callback URLs]\(コールバック URL\)** セクションで、App Service アプリの HTTPS URL を入力し、パス `/.auth/login/twitter/callback` を追加します。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/twitter/callback` 」のように入力します。
1. ページの下部にある **[Tell us how this app will be used]\(このアプリの用途の説明\)** に、100 文字以上入力し、 **[Create]\(作成\)** を選択します。 ポップアップでもう一度 **[Create]\(作成\)** をクリックします。 アプリケーションの詳細が表示されます。
1. **[Keys and Access Tokens]** タブをクリックします。

   これらの値を書き留めておきます。
   - API キー
   - API シークレット キー

   > [!NOTE]
   > API シークレット キーは重要なセキュリティ資格情報です。 このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>アプリケーションに Twitter 情報を追加する

1. [Azure portal] で自分のアプリケーションに移動します。
1. **[設定]** 、 **[認証/承認]** の順に選択し、 **[App Service 認証]** が **[オン]** になっていることを確認します。
1. **[Twitter]** を選択します。
1. 前に取得した `API key` 値と `API secret key` 値を貼り付けます。
1. **[OK]** を選択します。

   ![モバイル アプリの Twitter 設定のスクリーンショット][1]

   App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。

1. (省略可能) Twitter によって認証されたユーザーしかサイトにアクセスできないように制限するには、 **[要求が認証されない場合に実行するアクション]** を **[Twitter]** に設定します。 この機能を設定すると、お使いのアプリでは、すべての要求を認証する必要があります。 また、認証されていない要求はすべて、Twitter に認証のためにリダイレクトされます。

   > [!CAUTION]
   > この方法でのアクセスの制限は、アプリへのすべての呼び出しに適用されますが、これは、多くのシングルページ アプリケーションのように、一般公開されているホーム ページが与えられているアプリには適切でない場合があります。 このようなアプリケーションの場合は、アプリ自体が手動で認証を開始する、 **[匿名要求を許可する (操作不要)]** が推奨されることがあります。 詳細については、「[認証フロー](overview-authentication-authorization.md#authentication-flow)」をご覧ください。

1. **[保存]** を選択します。

これで、アプリケーションで認証に Twitter を使用する準備ができました。

## <a name="next-steps"></a><a name="related-content"> </a>次の手順

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
