---
title: Google 認証を構成する
description: App Services アプリまたは Azure Functions アプリ用の ID プロバイダーとして Google 認証を構成する方法について説明します。
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: e8a9fbe6072f3628d755ad3ad5aa5a623fc3ab23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "80519944"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Google ログインを使用するように App Service アプリまたは Azure Functions アプリを構成する

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、認証プロバイダーとして Google を使用するように Azure App Service または Azure Functions を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。 新しい Google アカウントを作成するには、[accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) にアクセスしてください。

## <a name="register-your-application-with-google"></a><a name="register"> </a>Google にアプリケーションを登録する

1. 「[サーバー側アプリの Google サインイン](https://developers.google.com/identity/sign-in/web/server-side-flow)」で Google のドキュメントに従い、クライアント ID とクライアント シークレットを作成します。 コードを変更する必要はありません。 次の情報を使用してください。
    - **[Authorized JavaScript Origins]\(承認済みの JavaScript 生成元\)** には、`https://<app-name>.azurewebsites.net` を使用し、 *\<app-name>* にアプリの名前を指定します。
    - **[Authorized Redirect URI] (承認済みのリダイレクト URI)** には `https://<app-name>.azurewebsites.net/.auth/login/google/callback` を使用します。
1. [App ID]\(アプリ ID\) と [App Secret]\(アプリ シークレット\) の値をコピーします。

    > [!IMPORTANT]
    > アプリ シークレットは重要なセキュリティ資格情報です。 このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Google の情報をアプリケーションに追加する

1. [Azure portal] で App Service アプリに移動します。
1. **[設定]** 、 **[認証/承認]** の順に選択し、 **[App Service 認証]** が **[オン]** になっていることを確認します。
1. **[Google]** を選択し、前に入手したアプリ ID とアプリ シークレットの値を貼り付けます。 アプリケーションに必要な任意のスコープを有効にします。
1. **[OK]** を選択します。

   App Service は認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 詳細については、「[ユーザーを承認または拒否する](app-service-authentication-how-to.md#authorize-or-deny-users)」を参照してください。

1. (省略可能) Google によって認証されたユーザーしかアクセスできないように制限するには、 **[要求が認証されない場合に実行するアクション]** を **[Google]** に設定します。 この機能を設定すると、お使いのアプリでは、すべての要求を認証する必要があります。 また、認証されていない要求はすべて、Google に認証のためにリダイレクトされます。

    > [!CAUTION]
    > この方法でのアクセスの制限は、アプリへのすべての呼び出しに適用されますが、これは、多くのシングルページ アプリケーションのように、一般公開されているホーム ページが与えられているアプリには適切でない場合があります。 このようなアプリケーションの場合は、アプリ自体が手動で認証を開始する、 **[匿名要求を許可する (操作不要)]** が推奨されることがあります。 詳細については、「[認証フロー](overview-authentication-authorization.md#authentication-flow)」をご覧ください。

1. **[保存]** を選択します。

これで、アプリケーションで認証に Google を使用する準備ができました。

## <a name="next-steps"></a><a name="related-content"> </a>次の手順

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

