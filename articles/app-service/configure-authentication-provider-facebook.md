---
title: Facebook 認証を構成する
description: App Service または Azure Functions アプリの ID プロバイダーとして Facebook 認証を構成する方法について説明します。
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: b6aad323c0d6fa8f59c9fad203640c477b162503
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "80519957"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Facebook ログインを使用するように App Service または Azure Functions アプリを構成する

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

この記事では、認証プロバイダーとして Facebook を使用するように Azure App Service または Azure Functions を構成する方法について説明します。

この記事の手順を完了するには、検証済みの電子メール アドレスを持つ Facebook アカウントと携帯電話番号が必要になります。 新しい Facebook アカウントを作成するには、 [facebook.com]にアクセスしてください。

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Facebook にアプリケーションを登録する

1. [Facebook Developers] の Web サイトに移動し、Facebook アカウントの資格情報でサインインします。

   Facebook for Developers アカウントを持っていない場合は、 **[スタートガイド]** を選択して登録の手順に従います。
1. **[マイアプリ]**  >  **[新しいアプリを追加]** を選択します。
1. **[表示名]** フィールド:
   1. アプリの一意の名前を入力します。
   1. **[連絡先メールアドレス]** を指定します。
   1. **[Create App ID]\(アプリ ID の作成\)** を選択します。
   1. セキュリティ チェックを完了します。

   新しい Facebook アプリ用の開発者ダッシュボードが開きます。
1. **[ダッシュボード]**  >  **[Facebook ログイン]**  >  **[設定]**  >  **[ウェブ]** を選択します。
1. 左側のナビゲーションで、 **[Facebook ログイン]** の下にある **[設定]** を選択します。
1. **[有効な OAuth リダイレクト URI]** フィールドに「`https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`」を入力します。 `<app-name>` を、ご利用の Azure App Service アプリの名前に置き換えることを忘れないでください。
1. **[変更の保存]** を選択します。
1. 左側のウィンドウで、 **[設定]**  >  **[Basic]\(ベーシック\)** を選択します。 
1. **[app secret]** フィールドで、 **[表示]** を選択します。 **[アプリ ID]** と **[App Secret]** の値をコピーします。 後で App Service アプリを Azure 内で構成するときにこれらの値を使用します。

   > [!IMPORTANT]
   > アプリケーション シークレットは、重要なセキュリティ資格情報です。 このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。
   >

1. アプリケーションの登録に使用した Facebook アカウントがアプリケーションの管理者になります。 この時点では、管理者のみがこのアプリケーションにサインインできます。

   他の Facebook アカウントを認証するには、 **[アプリのレビュー]** を選択し、 **[\<your-app-name> をパブリックにする]** を有効にして、Facebook 認証を使用することで一般公衆がアクセスできるようにします。

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Facebook の情報をアプリケーションに追加する

1. [Azure portal] にサインインし、ご自身の App Service アプリに移動します。
1. **[設定]** 、 **[認証/承認]** の順に選択し、 **[App Service 認証]** が **[オン]** になっていることを確認します。
1. **[Facebook]** を選択し、以前に入手したアプリ ID とアプリ シークレットの値を貼り付けます。 アプリケーションに必要な任意のスコープを有効にします。
1. **[OK]** を選択します。

   ![モバイル アプリの Facebook 設定のスクリーンショット][0]

    既定では、App Service で認証を行いますが、サイトのコンテンツと API への承認済みアクセスについては制限されません。 アプリ コードでユーザーを承認する必要があります。
1. (省略可能) Facebook によって認証されたユーザーしかサイトにアクセスできないように制限するには、 **[要求が認証されない場合に実行するアクション]** を **[Facebook]** に設定します。 この機能を設定すると、お使いのアプリでは、すべての要求を認証する必要があります。 また、認証されていない要求はすべて、認証のために Facebook にリダイレクトされます。

   > [!CAUTION]
   > この方法でのアクセスの制限は、アプリへのすべての呼び出しに適用されますが、これは、多くのシングルページ アプリケーションのように、一般公開されているホーム ページが与えられているアプリには適切でない場合があります。 このようなアプリケーションの場合は、アプリ自体が手動で認証を開始する、 **[匿名要求を許可する (操作不要)]** が推奨されることがあります。 詳細については、「[認証フロー](overview-authentication-authorization.md#authentication-flow)」をご覧ください。

1. **[保存]** を選択します。

これで、アプリで認証に Facebook を使用する準備ができました。

## <a name="next-steps"></a><a name="related-content"> </a>次の手順

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
