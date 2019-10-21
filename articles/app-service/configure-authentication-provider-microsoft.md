---
title: Microsoft アカウント認証の構成 - Azure App Service
description: App Service アプリに対して Microsoft アカウント認証を構成する方法について説明します。
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/08/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 70af534e6bcd0039dbc602a5ebc3fc35fb145e79
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176930"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>Microsoft アカウント ログインを使用するように App Service アプリを構成する

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、認証プロバイダーとして Microsoft アカウントを使用するように Azure App Services を構成する方法を示します。 

## <a name="register-microsoft-account"> </a>Microsoft アカウントにアプリを登録する

1. Azure portal で [ **[アプリの登録]** ](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) に移動します。 必要であれば、Microsoft アカウントを使ってサインインします。
1. **[New registration]\(新規登録\)** を選択し、アプリケーション名を入力します。
1. **[リダイレクト URI]** で **[Web]** を選択し、「`https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application`」と入力します。 *\<app-domain-name>* をアプリのドメイン名に置き換えます。  たとえば、「 `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback` 」のように入力します。 URL には HTTPS スキームを必ず使用します。

1. **[登録]** を選択します。
1. **アプリケーション (クライアント) ID** をコピーします。 この情報は後で必要になります。
1. 左側のウィンドウで、 **[Certificates & secrets]\(証明書とシークレット\)** を選択し、 **[New client secret]\(新しいクライアント シークレット\)** を選択します。 説明を入力し、有効期間を選択し、 **[追加]** を選択します。
1. **[Certificates & secrets]\(証明書とシークレット\)** ページに表示される値をコピーします。 この値はページから移動すると再び表示されません。

    > [!IMPORTANT]
    > パスワードは重要なセキュリティ資格情報です。 このパスワードを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。

## <a name="secrets"> </a>Microsoft アカウントの情報を App Service アプリケーションに追加する

1. [Azure portal] で自分のアプリケーションに移動します。
1. **[設定]** 、 **[認証/承認]** の順に選択し、 **[App Service 認証]** が **[オン]** になっていることを確認します。
1. **[認証プロバイダー]** で、 **[Microsoft アカウント]** を選択します。 前に取得したアプリケーション (クライアント) ID とクライアント シークレットを貼り付けます。 アプリケーションに必要な任意のスコープを有効にします。
1. **[OK]** を選択します。

   App Service は認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。

1. (省略可能) Microsoft アカウント ユーザーへのアクセスを制限するには、 **[要求が認証されない場合に実行するアクション]** を **[Microsoft アカウントでのログイン]** に設定します。 この機能を設定すると、お使いのアプリでは、すべての要求を認証する必要があります。 また、認証されていない要求はすべて、Microsoft アカウントに認証のためにリダイレクトされます。

   > [!CAUTION]
   > この方法でのアクセスの制限は、アプリへのすべての呼び出しに適用されますが、これは、多くのシングルページ アプリケーションのように、一般公開されているホーム ページが与えられているアプリには適切でない場合があります。 このようなアプリケーションの場合は、アプリ自体が手動で認証を開始する、 **[匿名要求を許可する (操作不要)]** が推奨されることがあります。 詳細については、「[認証フロー](overview-authentication-authorization.md#authentication-flow)」をご覧ください。

1. **[保存]** を選択します。

これで、アプリケーションで認証に Microsoft アカウントを使用する準備ができました。

## <a name="related-content"> </a>次のステップ

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
