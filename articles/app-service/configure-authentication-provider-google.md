---
title: Google 認証の構成 - Azure App Service
description: App Services アプリに Google 認証を構成する方法について説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fcbb284a0807ef88c5f40a7c8b65398d45bf73d7
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232132"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Google ログインを使用するように App Service アプリケーションを構成する方法
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、認証プロバイダーとして Google を使用するように Azure App Services を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。 新しい Google アカウントを作成するには、 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)にアクセスしてください。

## <a name="register"> </a>Google にアプリケーションを登録する
1. 「[サーバー側アプリの Google サインイン](https://developers.google.com/identity/sign-in/web/server-side-flow)」で Google のドキュメントに従い、次の情報を使用して、クライアント ID とクライアント シークレットを作成します (コードを変更する必要はありません)。
    - **[Authorized JavaScript Origins]\(承認済みの JavaScript 生成元\)** には、`https://<app-name>.azurewebsites.net` を使用し、 *\<app-name>* にアプリの名前を指定します。
    - **[Authorized Redirect URI] (承認済みのリダイレクト URI)** には `https://<app-name>.azurewebsites.net/.auth/login/google/callback` を使用します。
1. クライアント ID とクライアント シークレットが作成されたら、それらの値をコピーします。

    > [!IMPORTANT]
    > クライアント シークレットは、重要なセキュリティ資格情報です。 このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。


## <a name="secrets"> </a>Google の情報をアプリケーションに追加する
1. [Azure Portal] で App Service アプリに移動します。 左側のメニューで、 **[認証/承認]** を選択します。
2. [認証/承認] 機能が有効になっていない場合は、スイッチを **[オン]** に切り替えます。
3. **[Google]** にアクセスしてください。 前の手順で取得した App ID と App Secret の値を貼り付けます。アプリケーションで必要なスコープを有効にします (省略可能)。 次に、 **[OK]** をクリックします

   App Service は認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 詳細については、「[ユーザーを承認または拒否する](app-service-authentication-how-to.md#authorize-or-deny-users)」を参照してください。
4. (省略可能) Google によって認証されたユーザーしかサイトにアクセスできないように制限するには、 **[要求が認証されない場合に実行するアクション]** を **[Google]** に設定します。 この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Google にリダイレクトされます。

    > [!NOTE]
    > この方法でのアクセスの制限は、アプリへのすべての呼び出しに適用されますが、これは、多くのシングルページ アプリケーションのように、一般公開されているホームページを必要とするアプリには適切でない場合があります。 このようなアプリケーションの場合は、[ここ](overview-authentication-authorization.md#authentication-flow)で説明しているように、アプリが手動で自身のログインを開始する、 **[匿名要求を許可する (操作不要)]** が望ましいと考えられます。
    
5. **[Save]** をクリックします。

これで、アプリケーションで認証に Google を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

