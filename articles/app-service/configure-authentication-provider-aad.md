---
title: Azure Active Directory 認証の構成 - Azure App Service
description: App Service アプリに対して Azure Active Directory 認証を構成する方法について説明します。
author: cephalin
services: app-service
documentationcenter: ''
manager: gwallace
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service
ms.workload: web,mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/03/2019
ms.author: cephalin
ms.custom: fasttrack-edit
ms.openlocfilehash: 8de464a00867dd397f28de1dc35cf264244f6905
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743252"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Azure Active Directory サインインを使用するように App Service アプリを構成する

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

> [!NOTE]
> 現在は、Azure App Service および Azure Functions では AAD V2 (MSAL を含む) はサポートされていません。
>

この記事では、Azure Active Directory を認証プロバイダーとして使用するよう Azure App Service を構成する方法を示します。

各 App Service アプリに独自の登録を構成し、独自のアクセス許可と同意が使用されるようにすることをお勧めします。 また、個別のデプロイ スロットに対して個別のアプリ登録を使用することも考慮してください。 このようにすると、アクセス許可が環境間で共有されず、テストしている新しいコードの問題が運用環境に影響を与えません。

## <a name="express"> </a>簡単設定を構成する

1. [Azure Portal] で App Service アプリに移動します。 左側のナビゲーションで、 **[認証/承認]** を選択します。
2. **[認証/承認]** が有効になっていない場合は、 **[オン]** を選択します。
3. **[Azure Active Directory]** を選択し、 **[管理モード]** の **[高速]** を選択します。
4. **[OK]** を選択して、Azure Active Directory に App Service アプリを登録します。 これで、新しいアプリケーションの登録が作成されます。 代わりに既存のアプリの登録を選択する場合は、 **[既存のアプリを選ぶ]** をクリックし、テナント内で以前に作成したアプリの登録の名前を検索します。 アプリの登録をクリックして選択し、 **[OK]** をクリックします。 Azure Active Directory 設定ページで **[OK]** をクリックします。
App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。
5. (省略可能) アプリに対するアクセスを、Azure Active Directory で認証されたユーザーに制限するには、 **[要求が認証されない場合に実行するアクション]** を **[Azure Active Directory でのログイン]** に設定します。 この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Azure Active Directory にリダイレクトされます。

    > [!NOTE]
    > この方法でのアクセスの制限は、アプリへのすべての呼び出しに適用されますが、これは、多くのシングルページ アプリケーションのように、一般公開されているホームページを必要とするアプリには適切でない場合があります。 このようなアプリケーションの場合は、[ここ](overview-authentication-authorization.md#authentication-flow)で説明しているように、アプリが手動で自身のログインを開始する、 **[匿名要求を許可する (操作不要)]** が望ましいと考えられます。
6. **[Save]** をクリックします。

## <a name="advanced"> </a>詳細設定を構成する

また、使用する Azure Active Directory テナントが、Azure へのサインインに使用するテナントと異なる場合は、手動で構成設定を提供することもできます。 構成を完了するには、まず Azure Active Directory で登録を作成し、登録の一部の詳細を App Service に提供する必要があります。

### <a name="register"> </a>App Service アプリに対するアプリ登録を Azure AD で作成する

アプリの登録を手動で作成する場合は、後で App Service アプリを構成するときに必要になる 3 つの情報を記録しておきます。クライアント ID、テナント ID、および必要に応じてクライアント シークレットとアプリケーション ID の URI です。

1. [Azure portal] で、App Service アプリに移動し、アプリの **URL** を記録します。 Azure Active Directory アプリの登録を構成するときに、それを使用します。
1. [Azure portal] で、左側のメニューから **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新規登録]** を選択します。 
1. **[アプリケーションの登録]** ページで、アプリの登録の **[名前]** を入力します。
1. **[リダイレクト URI]** で、 **[Web]** を選択し、App Service アプリの URL を入力して、パス `/.auth/login/aad/callback` を追加します。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/aad/callback` 」のように入力します。 **[作成]** を選択します。
1. アプリの登録が作成されたら、後のために **[アプリケーション (クライアント) ID]** と **[ディレクトリ (テナント) ID]** をコピーします。
1. **[ブランド]** を選択します。 **[ホーム ページ URL]** に App Service アプリの URL を入力し、 **[保存]** を選択します。
1. **[API の公開]**  >  **[設定]** を選択します。 App Service アプリの URL を貼り付けて、 **[保存]** を選択します。

    > [!NOTE]
    > この値は、アプリの登録の**アプリケーション ID の URI** です。 たとえばフロントエンド Web アプリがバックエンド API にアクセスできるようにするため、バックエンドでフロントエンドにアクセス権を明示的に付与する必要がある場合は、"*バックエンド*" の App Service アプリ リソースを構成するときに、"*フロントエンド*" の**アプリケーション ID の URI** が必要です。
1. **[Scope の追加]** を選択します。 **[スコープ名]** に、「*user_impersonation*」と入力します。 "*アプリにアクセスする*" などの同意ページでユーザーに表示する同意スコープの名前と説明をテキスト ボックスに入力します。 終わったら、 **[スコープの追加]** をクリックします。
1. (省略可能) クライアント シークレットを作成するには、 **[Certificates & secrets]\(証明書とシークレット\)**  >  **[New client secret]\(新しいクライアント シークレット\) > ** **[追加]** を選択します。 ページに表示されるクライアント シークレットの値をコピーします。 別の場所に移動すると、再び表示されることはありません。
1. (省略可能) 複数の**応答 URL** を追加するには、メニューの **[認証]** を選択します。

### <a name="secrets"> </a>Azure Active Directory の情報を App Service アプリに追加する

1. [Azure Portal] で App Service アプリに移動します。 左側のメニューで、 **[認証/承認]** を選択します。 [認証/承認] 機能が有効になっていない場合は、 **[オン]** を選択します。 
1. (省略可能) 既定では、App Service 認証によってアプリへの認証されていないアクセスが許可されます。 ユーザー認証を強制するには、 **[要求が認証されない場合に実行するアクション]** を **[Azure Active Directory でのログイン]** に設定します。
1. [認証プロバイダー] で **[Azure Active Directory]** を選択します。
1. **[管理モード]** で **[Advanced]\(詳細\)** を選択し、次の表に従って App Service 認証を構成します。

    |フィールド|説明|
    |-|-|
    |クライアント ID| アプリの登録の**アプリケーション (クライアント) ID** を使用します。 |
    |Issuer ID (発行者 ID)| `https://login.microsoftonline.com/<tenant-id>` を使用し、 *\<tenant-id>* をアプリの登録の**ディレクトリ (テナント) ID** に置き換えます。 |
    |クライアント シークレット (省略可能)| アプリの登録で生成したクライアント シークレットを使用します。|
    |許可されるトークン対象ユーザー| これが "*バックエンド*" アプリで、フロントエンド アプリからの認証トークンを許可する場合は、"*フロントエンド*" の**アプリケーション ID の URI** をここに追加します。 |

    > [!NOTE]
    > **許可されるトークン対象ユーザー**をどのように構成したかに関係なく、構成された**クライアント ID** は "*常に*"、許可される対象ユーザーであると暗黙的に見なされます。
1. **[OK]** を選択し、 **[保存]** を選択します。

これで、App Service アプリで認証に Azure Active Directory を使用する準備ができました。

## <a name="configure-a-native-client-application"></a>ネイティブ クライアント アプリケーションを構成する
**Active Directory 認証ライブラリ**などのクライアント ライブラリを使用してサインインを実行する場合は、ネイティブ クライアントを登録できます。

1. [Azure portal] で、左側のメニューから **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新規登録]** を選択します。 
1. **[アプリケーションの登録]** ページで、アプリの登録の **[名前]** を入力します。
1. **[リダイレクト URI]** で、 **[パブリック クライアント (モバイルとデスクトップ)]** を選択し、App Service アプリの URL を入力して、パス `/.auth/login/aad/callback` を追加します。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/aad/callback` 」のように入力します。 **[作成]** を選択します。

    > [!NOTE]
    > Windows アプリケーションの場合は、代わりに[パッケージ SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) を URI として使用します。
1. アプリの登録が作成されたら、 **[アプリケーション (クライアント) ID]** の値をコピーします。
1. 左側のメニューから **[API のアクセス許可]**  >  **[アクセス許可の追加]**  >  **[自分の API]** を選択します。
1. App Service アプリ用に以前に作成したアプリの登録を選択します。 アプリの登録が表示されない場合は、「[App Service アプリに対するアプリ登録を Azure AD で作成する](#register)」で **user_impersonation** スコープを追加したことを確認します。
1. **user_impersonation** を選択し、 **[アクセス許可の追加]** をクリックします。

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
