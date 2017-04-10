---
title: "App Services アプリケーションに Facebook 認証を構成する方法"
description: "App Services アプリケーションに Facebook 認証を構成する方法について説明します。"
services: app-service
documentationcenter: 
author: mattchenderson
manager: adrianha
editor: 
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: e23725539a354b0c813e5206e8ca4de37be0b49b
ms.lasthandoff: 03/09/2017


---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>App Service アプリケーションを Facebook ログインを使用するように構成する方法
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、認証プロバイダーとして Facebook を使用するように Azure App Service を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Facebook アカウントと携帯電話番号が必要になります。 新しい Facebook アカウントを作成するには、 [facebook.com]にアクセスしてください。

## <a name="register"> </a>Facebook にアプリケーションを登録する
1. [Azure ポータル]にログオンし、目的のアプリケーションに移動します。 **[URL]**をコピーします。 この URL は、Facebook アプリの構成で使用します。
2. 他のブラウザー ウィンドウで、 [Facebook Developers] の Web サイトに移動し、Facebook アカウントの資格情報でサインインします。
3. (省略可能) まだ登録していない場合は、**[Apps (アプリ)]** > **[Register as a Developer (開発者として登録)]** の順にクリックし、ポリシーに同意して、登録手順に従います。
4. **[My Apps (自分のアプリ)]** > **[Add a New App (新しいアプリの追加)]** > **[Website (Web サイト)]** > **[Skip and Create App ID (スキップしてアプリ ID を作成)]** の順にクリックします。 
5. **[Display Name (表示名)]** にアプリの一意の名前を入力し、**[Contact Email (連絡先の電子メール)]** を入力して、**[Category (カテゴリ)]** でアプリのカテゴリを選択します。次に、**[Create App ID (アプリ ID の作成)]** をクリックし、セキュリティ チェックを完了します。 これで、新しい Facebook アプリケーションの開発者向けダッシュボードに移動します。
6. "Facebook ログイン" で **[Get Started]**をクリックします。 **[Valid OAuth redirect URIs (有効な OAuth リダイレクト URI)]** にアプリケーションの**リダイレクト URI** を追加して、**[Save Changes (変更の保存)]** をクリックします。 
   
   > [!NOTE]
   > リダイレクト URI は、アプリケーションの URL にパス */.auth/login/facebook/callback* を追加したものです。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/facebook/callback`」のように入力します。 HTTPS スキームを使用していることを確認します。
   > 
   > 
7. 左側のナビゲーションで、クリックして **[Settings]**をクリックします。 **[App Secret (アプリケーション シークレット)]** フィールドで **[Show (表示)]** をクリックし、要求された場合はパスワードを入力して、**[App ID (アプリ ID)]** と **[App Secret (アプリケーション シークレット)]** の値をメモしておきます。 後でアプリケーションを Azure で構成するときにこれらの値を使用します。
   
   > [!IMPORTANT]
   > アプリケーション シークレットは、重要なセキュリティ資格情報です。 このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。
   > 
   > 
8. アプリケーションの登録に使用した Facebook アカウントがアプリケーションの管理者になります。 この時点では、管理者のみがこのアプリケーションにサインインできます。 他の Facebook アカウントを認証するには、**[App Review (アプリのレビュー)]** をクリックし、**[Make <your-app-name> public (<アプリ名> をパブリックにする)]** を有効にして、Facebook 認証を使用した汎用パブリック アクセスを有効にします。

## <a name="secrets"> </a>Facebook の情報をアプリケーションに追加する
1. [Azure ポータル]に戻り、アプリケーションに移動します。 **[設定]** > **[認証/承認]** の順にクリックし、**[App Service 認証]** が **[オン]** になっていることを確認します。
2. **[Facebook]** をクリックし、前の手順で取得したアプリ ID とアプリケーション シークレットの値を貼り付けます。必要に応じて、アプリケーションで必要なスコープを有効にし、**[OK]** をクリックします。
   
    ![][0]
   
    App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。
3. (省略可能) Facebook によって認証されたユーザーしかサイトにアクセスできないように制限するには、**[要求が認証されない場合に実行するアクション]** を **[Facebook]** に設定します。 この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Facebook にリダイレクトされます。
4. 認証の構成が終了したら、 **[保存]**をクリックします。

これで、アプリケーションで認証に Facebook を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure ポータル]: https://portal.azure.com/

