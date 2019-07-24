---
title: Facebook 認証の構成 - Azure App Service
description: App Services アプリケーションに Facebook 認証を構成する方法について説明します。
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: f37a0c9e4c664ac9631a0a07fa6f114e62939845
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522891"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>App Service アプリケーションを Facebook ログインを使用するように構成する方法
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、認証プロバイダーとして Facebook を使用するように Azure App Service を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Facebook アカウントと携帯電話番号が必要になります。 新しい Facebook アカウントを作成するには、 [facebook.com]にアクセスしてください。

## <a name="register"> </a>Facebook にアプリケーションを登録する
1. [Azure Portal]にログオンし、目的のアプリケーションに移動します。 **[URL]** をコピーします。 この URL は、Facebook アプリの構成で使用します。
2. 他のブラウザー ウィンドウで、 [Facebook Developers] の Web サイトに移動し、Facebook アカウントの資格情報でサインインします。
3. (省略可能) まだ登録していない場合は、**[Apps (アプリ)]** > **[Register as a Developer (開発者として登録)]** の順にクリックし、ポリシーに同意して、登録手順に従います。
4. **[My Apps] (マイアプリ)** > **[Add a New App] (新しいアプリの追加)** をクリックします。
5. **[Display Name] (表示名)** に、アプリの一意の名前を入力します。 **連絡先の電子メール**を指定してから、**[Create App ID] (アプリ ID の作成)** をクリックし、セキュリティ チェックを完了します。 これで、新しい Facebook アプリケーションの開発者向けダッシュボードに移動します。
6. **[Facebook Login (Facebook ログイン)]** で **[Set up] (セットアップ)** をクリックし、**[Facebook Login] (Facebook ログイン)** の左側のナビゲーションで **[Settings] (設定)** を選択します。
7. **[Valid OAuth redirect URIs (有効な OAuth リダイレクト URI)]** にアプリケーションの**リダイレクト URI** を追加して、**[Save Changes (変更の保存)]** をクリックします。
   
   > [!NOTE]
   > リダイレクト URI は、アプリケーションの URL にパス */.auth/login/facebook/callback* を追加したものです。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/facebook/callback`」のように入力します。 HTTPS スキームを使用していることを確認します。
   > 
   > 
8. 左側のナビゲーションで、**[Settings] (設定)** > **[Basic] (基本)** をクリックします。 **[App Secret (アプリケーション シークレット)]** フィールドで **[Show (表示)]** をクリックし、要求された場合はパスワードを入力して、**[App ID (アプリ ID)]** と **[App Secret (アプリケーション シークレット)]** の値をメモしておきます。 後でアプリケーションを Azure で構成するときにこれらの値を使用します。
   
   > [!IMPORTANT]
   > アプリケーション シークレットは、重要なセキュリティ資格情報です。 このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。
   > 
   > 
9. アプリケーションの登録に使用した Facebook アカウントがアプリケーションの管理者になります。 この時点では、管理者のみがこのアプリケーションにサインインできます。 他の Facebook アカウントを認証するには、**[アプリのレビュー]** をクリックし、**[\<<アプリ名> をパブリックにする]** を有効にして、Facebook 認証を使用した汎用パブリック アクセスを有効にします。

## <a name="secrets"> </a>Facebook の情報をアプリケーションに追加する
1. [Azure Portal]に戻り、アプリケーションに移動します。 **[設定]** > **[認証/承認]** の順にクリックし、**[App Service 認証]** が **[オン]** になっていることを確認します。
2. **[Facebook]** をクリックし、前の手順で取得したアプリ ID とアプリケーション シークレットの値を貼り付けます。必要に応じて、アプリケーションで必要なスコープを有効にし、**[OK]** をクリックします。
   
    ![][0]
   
    App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。
3. (省略可能) Facebook によって認証されたユーザーしかサイトにアクセスできないように制限するには、**[要求が認証されない場合に実行するアクション]** を **[Facebook]** に設定します。 この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Facebook にリダイレクトされます。
4. 認証の構成が終了したら、 **[保存]** をクリックします。

これで、アプリケーションで認証に Facebook を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
