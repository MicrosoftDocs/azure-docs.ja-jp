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
ms.openlocfilehash: 0832c1e5f10cdb8e1d7a2edbb88162230ab13401
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233087"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Microsoft アカウント ログインを使用するように App Service アプリケーションを構成する方法
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、認証プロバイダーとして Microsoft アカウントを使用するように Azure App Services を構成する方法を示します。 

## <a name="register-microsoft-account"> </a>Microsoft アカウントにアプリを登録する
1. [Azure portal] にサインインし、アプリケーションに移動します。 

<!-- Copy your **URL**, which you will use later to configure your app with Microsoft Account. -->
1. [ **[アプリの登録]** ](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) に移動し、要求された場合は Microsoft アカウントでサインインします。

1. **[New registration]\(新規登録\)** をクリックし、アプリケーション名を入力します。

1. **[リダイレクト URI]** で **[Web]** を選択し、「`https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application`」と入力します。 *\<app-domain-name>* をアプリのドメイン名に置き換えます。  たとえば、「 `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback` 」のように入力します。 

   > [!NOTE]
   > URL には HTTPS スキームを使用します。

1. **[登録]** を選択します 

1. **アプリケーション (クライアント) ID** をコピーします。 この情報は後で必要になります。 
   
7. 新しいアプリの登録の左側のナビゲーションで、 **[Certificates & secrets]\(証明書とシークレット\)**  >  **[New client secret]\(新しいクライアント シークレット\)** を選択します。 説明を入力し、有効期間を選択して、 **[追加]** を選択します。

1. **[Certificates & secrets]\(証明書とシークレット\)** ページに表示される値をコピーします。 ページから移動すると、このパスワードが再度表示されることはありません。

    > [!IMPORTANT]
    > パスワードは重要なセキュリティ資格情報です。 このパスワードを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。

## <a name="secrets"> </a>Microsoft アカウントの情報を App Service アプリケーションに追加する
1. [Azure Portal]で、アプリケーションに移動します。 左側のナビゲーションで、 **[認証/承認]** をクリックします。

2. [認証/承認] 機能が有効になっていない場合は、 **[オン]** を選択します。

3. **[認証プロバイダー]** で、 **[Microsoft アカウント]** を選択します。 前の手順で取得したアプリケーション (クライアント) ID とクライアント シークレットを貼り付け、必要に応じてアプリケーションに必要なスコープを有効にします。 次に、 **[OK]** をクリックします

    App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。

4. (省略可能) Microsoft アカウント ユーザーへのアクセスを制限するには、 **[要求が認証されない場合に実行するアクション]** を **[Microsoft アカウントでのログイン]** に設定します。 この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Microsoft アカウントにリダイレクトされます。

> [!NOTE]
> この方法でのアクセスの制限は、アプリへのすべての呼び出しに適用されますが、これは、多くのシングルページ アプリケーションのように、一般公開されているホーム ページを必要とするアプリには適切でない場合があります。 このようなアプリケーションの場合は、[ここ](overview-authentication-authorization.md#authentication-flow)で説明しているように、アプリが手動で自身のログインを開始する、 **[匿名要求を許可する (操作不要)]** が望ましいと考えられます。

5. **[Save]** をクリックします。

これで、アプリケーションで認証に Microsoft アカウントを使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
