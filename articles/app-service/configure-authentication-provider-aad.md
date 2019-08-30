---
title: Azure Active Directory 認証の構成 - Azure App Service
description: App Services アプリケーションに Azure Active Directory 認証を構成する方法について説明します。
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/20/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 41084c3532e29b3a52c121d48226c5a45857d5dc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088231"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Azure Active Directory サインインを使用するように App Service アプリを構成する

> [!NOTE]
> 現時点では、Azure App Services および Azure Functions で AAD V2 (MSAL を含む) はサポートされていません。 更新プログラムがないかどうか確認してください。
>

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

この記事では、Azure Active Directory を認証プロバイダーとして使用するよう Azure App Services を構成する方法を示します。

## <a name="express"> </a>簡単設定を構成する

1. [Azure Portal] で App Service アプリに移動します。 左側のナビゲーションで、 **[認証/承認]** を選択します。
2. **[認証/承認]** が有効になっていない場合は、 **[オン]** を選択します。
3. **[Azure Active Directory]** を選択し、 **[管理モード]** の **[高速]** を選択します。
4. **[OK]** を選択して、Azure Active Directory に App Service アプリを登録します。 これで、新しいアプリケーションの登録が作成されます。 代わりに既存のアプリの登録を選択する場合は、 **[既存のアプリを選ぶ]** をクリックし、テナント内で以前に作成したアプリの登録の名前を検索します。
   アプリの登録をクリックして選択し、 **[OK]** をクリックします。 Azure Active Directory 設定ページで **[OK]** をクリックします。
   App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。
5. (省略可能) サイトに対するアクセスを、Azure Active Directory で認証されたユーザーに限定するには、 **[要求が認証されない場合に実行するアクション]** を **[Azure Active Directory でのログイン]** に設定します。 この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Azure Active Directory にリダイレクトされます。

> [!CAUTION]
> この方法でのアクセスの制限は、アプリへのすべての呼び出しに適用されますが、これは、多くのシングルページ アプリケーションのように、一般公開されているホームページを必要とするアプリには適切でない場合があります。 このようなアプリケーションの場合は、[ここ](overview-authentication-authorization.md#authentication-flow)で説明しているように、アプリが手動で自身のログインを開始する、 **[匿名要求を許可する (操作不要)]** が望ましいと考えられます。

6. **[Save]** をクリックします。

## <a name="advanced"> </a>詳細設定を構成する

構成設定を手動で指定することもできます。 これは、使用する Azure Active Directory テナントが Azure へのサインインに使用するテナントと異なる場合に推奨されるソリューションです。 構成を完了するには、まず Azure Active Directory で登録を作成し、登録の一部の詳細を App Service に提供する必要があります。

### <a name="register"> </a>Azure Active Directory に App Service アプリを登録する

1. [Azure portal] にサインインし、ご自身の App Service アプリに移動します。 アプリの **URL** をコピーします。 これを使用して、Azure Active Directory アプリの登録を構成します。
2. **[Active Directory]** に移動し、 **[アプリの登録]** を選び、上部の **[新しいアプリケーションの登録]** をクリックして新しいアプリの登録を開始します。 
3. **[作成]** ページで、アプリの登録の **[名前]** を入力し、 **[Web アプリ/API]** の種類を選んで、 **[サインオン URL]** ボックスに手順 1. のアプリケーション URL を貼り付けます。 **[作成]** をクリックします。
4. 数秒後に、作成した新しいアプリの登録が表示されます。
5. アプリの登録が追加された後、アプリの登録名をクリックし、上部の **[設定]** をクリックして、 **[プロパティ]** をクリックします。 
6. **[アプリケーション ID/URI]** ボックスと **[ホーム ページ URL]** ボックスの両方に手順 1 のアプリケーション URL を貼り付け、 **[保存]** をクリックします。
7. 次に、 **[応答 URL]** をクリックし、 **[応答 URL]** を編集し、(手順 1. の) アプリケーション URL を貼り付けてから、 */.auth/login/aad/callback* を URL の最後に追加します (たとえば、`https://contoso.azurewebsites.net/.auth/login/aad/callback`)。 **[Save]** をクリックします。

   > [!NOTE]
   > **[応答 URL]** を追加することによって、複数のドメインに同じアプリ登録を使用できます。 各 App Service インスタンスを独自の登録でモデル化して、独自のアクセス許可と同意が割り当てられるようにしてください。 また、個別のサイト スロットに別のアプリ登録を使用することも考慮してください。 これは、アクセス許可が環境間で共有されないようにすることにより、テストしている新しいコードのバグが運用環境に影響を与えないようにするためです。
    
8. この時点で、アプリの**アプリケーション ID** をコピーします。 これは後で使うために保存しておきます。 App Service アプリの構成に必要になります。
9. **[登録済みのアプリ]** ページを閉じます。 **[アプリの登録]** ページで、上部にある **[エンドポイント]** ボタンをクリックし、**WS-FEDERATION SIGN-ON ENDPOINT** URL を、URL の末尾の `/wsfed` を除いてコピーします。 結果は `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000` のようになります。 ドメイン名は、ソブリン クラウドによって異なる可能性があります。 これは、後で発行者の URL として使用されます。

### <a name="secrets"> </a>Azure Active Directory の情報を App Service アプリに追加する

1. [Azure Portal] に戻って、App Service アプリに移動します。 **[認証/承認]** をクリックします。 [認証/承認] 機能が有効になっていない場合は、スイッチを **[オン]** に切り替えます。 **[Azure Active Directory]** をクリックし、[認証プロバイダー] でアプリを構成します。

    (省略可能) App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。 **[要求が認証されない場合に実行するアクション]** を、 **[Azure Active Directory でのログイン]** に設定します。 このオプションでは、要求はすべて認証される必要があり、認証されていないすべての要求は、認証のために Azure Active Directory にリダイレクトされます。
2. Active Directory の認証の構成で、 **[管理モード]** の **[詳細]** をクリックします。 手順 8 のアプリケーション ID を [クライアント ID] ボックスに貼り付け、手順 9 の URL を [発行者の URL] の値に貼り付けます。 次に、 **[OK]** をクリックします
3. Active Directory 認証構成ページで、 **[保存]** をクリックします。

これで、App Service アプリで認証に Azure Active Directory を使用する準備ができました。

## <a name="configure-a-native-client-application"></a>ネイティブ クライアント アプリケーションを構成する
ネイティブ クライアントを登録して、アクセス許可のマッピングを詳細に制御できます。 **Active Directory 認証ライブラリ**などのクライアント ライブラリを使用してサインインを実行する場合は、これが必要です。

1. [Azure Portal] で **[Azure Active Directory]** に移動します。
2. 左側のナビゲーションで、 **[アプリの登録]** を選択します。 上部にある **[新しいアプリケーションの登録]** をクリックします。
4. **[作成]** ページで、アプリの登録の **[名前]** を入力します。 **[アプリケーションの種類]** で **[ネイティブ]** を選択します。
5. **[リダイレクト URI]** に、HTTPS スキームを使用してサイトの */.auth/login/done* エンドポイントを入力します。 これは、 *https://contoso.azurewebsites.net/.auth/login/done* のような値である必要があります。 Windows アプリケーションを作成する場合は、 [パッケージ SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) を URI として使用します。
5. **Create** をクリックしてください。
6. アプリの登録が追加されたら、それを選択して開きます。 **[アプリケーション ID]** を検索し、その値をメモします。
7. **[すべての設定]**  >  **[必要なアクセス許可]**  >  **[追加]**  >  **[API を選択します]** の順にクリックします。
8. 前に登録した App Service アプリの名前を入力して検索し、それを選択して **[選択]** をクリックします。
9. **[\<アプリ名> へアクセス]** を選択します。 **[選択]** をクリックします。 次に、 **[Done]** をクリックします。

これで、App Service アプリにアクセスできるネイティブ クライアント アプリケーションが構成されました。

## <a name="related-content"> </a>関連コンテンツ

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[alternative method]:#advanced
