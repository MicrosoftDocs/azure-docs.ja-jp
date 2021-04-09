---
title: Azure AD 認証を構成する
description: App Service または Azure Functions アプリの ID プロバイダーとして Azure Active Directory 認証を構成する方法を学びます。
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 2805500e4a4c98ad7b8360393e7d69ad9fb704a3
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563338"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Azure AD ログインを使用するように App Service または Azure Functions アプリを構成する

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

この記事では、アプリで Azure Active Directory (Azure AD) を認証プロバイダーとして使用してユーザーがサインインするように、Azure App Service または Azure Functions の認証を構成する方法について説明します。

## <a name="configure-with-express-settings"></a><a name="express"> </a>簡単設定を構成する

**[簡易]** オプションでは、認証を簡単にするように設計されていて、必要なのは数回のクリックのみです。

簡易設定では、Azure Active Directory V1 エンドポイントを使用するアプリケーション登録が自動的に作成されます。 [Azure Active Directory v2.0](../active-directory/develop/v2-overview.md) ([MSAL](../active-directory/develop/msal-overview.md) を含む) を使用するには、[詳細構成の手順](#advanced)に従ってください。

> [!NOTE]
> **[Express]\(簡易\)** オプションは、政府機関向けクラウドでは使用できません。

**[簡易]** オプションを使用して認証を有効にするには、次の手順に従います。

1. [Azure portal] で、 **[App Services]** を探して選択してから、アプリを選択します。
2. 左側のナビゲーションから、 **[認証/承認]**  >  **[On]\(オン\)** を選択します。
3. **[Azure Active Directory]**  >  **[Express]\(簡易\)** を選択します。

   代わりに既存のアプリ登録を選択する場合は、次のようにします。

   1. **[既存の AD アプリを選択する]** を選択し、 **[Azure AD アプリ]** をクリックします。
   2. 既存のアプリ登録を選択して **[OK]** をクリックします。

4. **[OK]** を選択して、Azure Active Directory に App Service アプリを登録します。 新しいアプリ登録が作成されます。

    ![Azure Active Directory での簡易設定](./media/configure-authentication-provider-aad/express-settings.png)

5. (省略可能) App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。 Azure Active Directory で認証されたユーザーのみにアプリ アクセスを限定するには、 **[要求が認証されない場合に実行するアクション]** を **[Azure Active Directory でのログイン]** に設定します。 この機能を設定すると、お使いのアプリでは、すべての要求を認証する必要があります。 また、認証されていないものはすべて、Azure Active Directory に認証のためにリダイレクトされます。

    > [!CAUTION]
    > この方法でのアクセスの制限は、アプリへのすべての呼び出しに適用されますが、これは、多くのシングルページ アプリケーションのように、一般公開されているホーム ページが与えられているアプリには適切でない場合があります。 このようなアプリケーションの場合は、アプリが手動で自身のログインを開始する、 **[匿名要求を許可する (操作不要)]** が望ましいと考えられます。 詳細については、「[認証フロー](overview-authentication-authorization.md#authentication-flow)」をご覧ください。
6. **[保存]** を選択します。

Azure Storage と Microsoft Graph にアクセスする Web アプリの Azure AD ログインを構成する例については、[こちらのチュートリアル](scenario-secure-app-authentication-app-service.md)を参照してください。

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>詳細設定を構成する

Azure AD がアプリの認証プロバイダーとして機能するためには、アプリを登録する必要があります。 簡易オプションでは、これが自動的に行われます。 [詳細] オプションを使用すると、アプリを手動で登録できるため、登録をカスタマイズして、登録の詳細を App Service に手動で入力することができます。 これは、たとえば App Service が含まれているものとは異なる Azure AD テナントからアプリの登録を使用する場合に便利です。

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>App Service アプリに対するアプリ登録を Azure AD で作成する

まず、アプリの登録を作成します。 この場合は、後で App Service アプリで認証を構成するときに必要になる次の情報を収集します。

- クライアント ID
- テナント ID
- クライアント シークレット (省略可能)
- アプリケーション ID URI

アプリを登録するには、次の手順に従います。

1. [Azure portal] にサインインし、 **[App Services]** を探して選択してから、アプリを選択します。 アプリの **URL** をメモしておきます。 Azure Active Directory アプリの登録を構成するときにそれを使用します。
1. ポータル メニューから **[Azure Active Directory]** を選択し、 **[アプリの登録]** タブにアクセスして、 **[新規登録]** を選択します。
1. **[アプリケーションの登録]** ページで、アプリの登録の **[名前]** を入力します。
1. **[リダイレクト URI]** で、 **[Web]** を選択し、「`<app-url>/.auth/login/aad/callback`」と入力します。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/aad/callback` 」のように入力します。
1. **[登録]** を選択します。
1. アプリの登録が作成されたら、後のために **[アプリケーション (クライアント) ID]** と **[ディレクトリ (テナント) ID]** をコピーします。
1. **[認証]** を選択します。 **[暗黙的な許可]** で **[ID トークン]** を有効にして、App Service からの OpenID Connect ユーザーのサインインを許可します。
1. (省略可能) **[ブランド]** を選択します。 **[ホーム ページ URL]** に App Service アプリの URL を入力し、 **[保存]** を選択します。
1. **[API の公開]**  >  **[設定]** を選択します。 シングルテナント アプリの場合は、App Service アプリの URL を貼り付けて **[保存]** を選択します。マルチテナント アプリの場合は、テナントで検証済みのいずれかのドメインに基づく URL を貼り付けて、 **[保存]** を選択します。

   > [!NOTE]
   > この値は、アプリの登録の **アプリケーション ID の URI** です。 Web アプリでクラウドの API にアクセスする必要がある場合、クラウド App Service リソースの構成時、**Web アプリのアプリケーション ID URI** が必要になります。 たとえば、クラウド サービスで Web アプリにアクセスを明示的に与える場合にこれを利用できます。

1. **[Scope の追加]** を選択します。
   1. **[スコープ名]** に、「*user_impersonation*」と入力します。
   1. 同意ページでユーザーに表示する同意スコープの名前と説明をテキスト ボックスに入力します。 たとえば、「*Access my app*」と入力します。
   1. **[スコープの追加]** を選択します。
1. (省略可能) クライアント シークレットを作成するには、 **[Certificates & secrets]\(証明書とシークレット)**  >  **[New client secret]\(新しいクライアント シークレット)**  >  **[追加]** を選択します。 ページに表示されるクライアント シークレットの値をコピーします。 二度と表示されることはありません。
1. (省略可能) 複数の **応答 URL** を追加するには、 **[認証]** を選択します。

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>App Service アプリで Azure Active Directory を有効にする

1. [Azure portal] で、 **[App Services]** を探して選択してから、アプリを選択します。
1. 左側のウィンドウの **[設定]** で、 **[認証/承認]**  >  **[On]\(オン\)** を選択します。
1. (省略可能) 既定では、App Service 認証によってアプリへの認証されていないアクセスが許可されます。 ユーザー認証を強制するには、 **[要求が認証されない場合に実行するアクション]** を **[Azure Active Directory でのログイン]** に設定します。
1. **[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。
1. **[管理モード]** で **[Advanced]\(詳細\)** を選択し、次の表に従って App Service 認証を構成します。

    |フィールド|説明|
    |-|-|
    |クライアント ID| アプリの登録の **アプリケーション (クライアント) ID** を使用します。 |
    |発行者の URL| `<authentication-endpoint>/<tenant-id>/v2.0` を使用し、 *\<authentication-endpoint>* を [クラウド環境の認証エンドポイント](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (たとえば、グローバル Azure の場合、"https://login.microsoftonline.com") に置き換え、さらに、 *\<tenant-id>* を、アプリ登録が作成された **ディレクトリ (テナント) ID** に置き換えます。 この値は、ユーザーを正しい Azure AD テナントにリダイレクトするため、および適切なメタデータをダウンロードして、適切なトークン署名キーやトークン発行者のクレーム値などを特定するために使用されます。 Azure AD v1 を使用するアプリケーションと Azure Functions アプリの場合は、URL の `/v2.0` を省略します。|
    |クライアント シークレット (省略可能)| アプリの登録で生成したクライアント シークレットを使用します。|
    |許可されるトークン対象ユーザー| これがクラウドまたはサーバー アプリで、Web アプリからの認証トークンを許可する場合は、Web アプリの **アプリケーション ID URI** をここに追加します。 構成された **[クライアント ID]** は、*常に*、許可された対象ユーザーであると暗黙的に見なされます。 |

2. **[OK]** を選択し、 **[保存]** を選択します。

これで、App Service アプリで認証に Azure Active Directory を使用する準備ができました。

## <a name="configure-client-apps-to-access-your-app-service"></a>App Service にアクセスするようにクライアント アプリを構成する

前のセクションでは、ユーザーを認証するために App Service または Azure 関数を登録しました。 このセクションでは、ネイティブ クライアントまたはデーモン アプリを登録して、ユーザーまたはユーザーの代わりに App Service によって公開される API へのアクセスを要求できるようにする方法について説明します。 ユーザーを認証するだけの場合は、このセクションの手順を完了する必要はありません。

### <a name="native-client-application"></a>ネイティブ クライアント アプリケーション

サインインしているユーザーの代わりに App Service アプリの API へのアクセスを要求するようにネイティブ クライアントを登録できます。

1. [Azure portal] で、 **[Active Directory]** 、 **[アプリの登録]** 、 **[新規登録]** の順に選択します。
1. **[アプリケーションの登録]** ページで、アプリの登録の **[名前]** を入力します。
1. **[リダイレクト URI]** で、 **[パブリック クライアント (モバイルとデスクトップ)]** を選択し、URL「`<app-url>/.auth/login/aad/callback`」を入力します。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/aad/callback` 」のように入力します。

    > [!NOTE]
    > Microsoft Store アプリケーションの場合は、代わりに[パッケージ SID](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#package-sid) を URI として使用します。
1. **［作成］** を選択します
1. アプリの登録が作成されたら、 **[アプリケーション (クライアント) ID]** の値をコピーします。
1. **[API のアクセス許可]** 、 **[アクセス許可の追加]** 、 **[自分の API]** の順に選択します。
1. App Service アプリ用に以前に作成したアプリの登録を選択します。 アプリの登録が表示されない場合は、「[App Service アプリに対するアプリ登録を Azure AD で作成する](#register)」で **user_impersonation** スコープを追加したことを確認します。
1. **[委任されたアクセス許可]** で、 **[user_impersonation]** を選択し、 **[アクセス許可の追加]** を選択します。

これで、ユーザーに代わって App Service アプリにアクセスを要求できるネイティブ クライアント アプリケーションが構成されました。

### <a name="daemon-client-application-service-to-service-calls"></a>デーモン クライアント アプリケーション (サービス間の呼び出し)

アプリケーションは、(ユーザーの代わりではなく) それ自体の代わりに App Service または関数アプリでホストされる Web API を呼び出すトークンを取得できます。 このシナリオは、ログイン ユーザーなしでタスクを実行する非対話型デーモン アプリケーションに役立ちます。 これには標準の OAuth 2.0 [クライアント資格情報](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)の付与が使用されます。

1. [Azure portal] で、 **[Active Directory]** 、 **[アプリの登録]** 、 **[新規登録]** の順に選択します。
1. **[アプリケーションの登録]** ページで、デーモン アプリの登録の **[名前]** を入力します。
1. デーモン アプリケーションの場合、リダイレクト URI は不要であるため、空のままでかまいません。
1. **［作成］** を選択します
1. アプリの登録が作成されたら、 **[アプリケーション (クライアント) ID]** の値をコピーします。
1. **[証明書とシークレット]**  >  **[新しいクライアント シークレット]**  >  **[追加]** を選択します。 ページに表示されるクライアント シークレットの値をコピーします。 二度と表示されることはありません。

これで、`resource` パラメーターをターゲット アプリの **アプリケーション ID URI** に設定することで、[クライアント ID とクライアント シークレットを使用してアクセス トークンを要求](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret)できます。 その後、標準の [OAuth 2.0 Authorization ヘッダー](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource)を使用して、結果として得られたアクセス トークンをターゲット アプリに提示できます。また、App Service の認証/承認によって、通常どおりトークンが検証および使用され、呼び出し元 (この場合はユーザーではなくアプリケーション) が認証されたことが示されます。

現時点で、これによって Azure AD テナント内の _すべて_ のクライアント アプリケーションがアクセス トークンを要求し、ターゲット アプリに対して認証を行うことができます。 また、"_承認_" を実施して特定のクライアント アプリケーションのみを許可する場合は、追加の構成を行う必要があります。

1. 保護する App Service または関数アプリを表すアプリ登録のマニフェストで、[アプリ ロールを定義](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)します。
1. 承認する必要があるクライアントを表すアプリの登録で、 **[API のアクセス許可]**  >  **[アクセス許可の追加]**  >  **[自分の API]** を選択します。
1. 前に作成したアプリの登録を選択します。 アプリの登録が表示されない場合は、[アプリ ロールを追加](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)したことを確認してください。
1. **[アプリケーションのアクセス許可]** で、前に作成したアプリ ロールを選択し、 **[アクセス許可の追加]** を選択します。
1. クライアント アプリケーションがアクセス許可を要求することを承認するために、必ず **[管理者の同意を与える]** をクリックしてください。
1. (ロールを追加する前の) 前述のシナリオと同様に、これで、同じターゲット `resource` の[アクセス トークンを要求](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret)できます。アクセス トークンには、クライアント アプリケーションのために承認されたアプリ ロールを含む `roles` 要求が含まれます。
1. これで、ターゲット App Service または関数アプリのコード内で、必要なロールがトークンに存在しているか検証できます (これは App Service 認証/承認では実行されません)。 詳しくは、「[ユーザー要求へのアクセス](app-service-authentication-how-to.md#access-user-claims)」をご覧ください。

これで、独自の ID を使用して App Service アプリにアクセスできるデーモン クライアント アプリケーションが構成されました。

## <a name="best-practices"></a>ベスト プラクティス

認証の設定に使用する構成に関係なく、次のベスト プラクティスによってテナントとアプリケーションのセキュリティが強化されます。

- App Service アプリごとに独自のアクセス許可と同意を付与します。
- それぞれの App Service アプリを独自の登録で構成します。
- デプロイ スロットごとに個別のアプリ登録を使用することで、環境間でアクセス許可を共有することを回避します。 新しいコードをテストするとき、このプラクティスは、問題が運用アプリに影響を与えることを回避する上で役立つことがあります。

## <a name="next-steps"></a><a name="related-content"> </a>次の手順

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [チュートリアル: Azure Storage と Microsoft Graph にアクセスする Web アプリでユーザーを認証および認可する](scenario-secure-app-authentication-app-service.md)
* [チュートリアル:Azure App Service でユーザーをエンド ツー エンドで認証および承認する](tutorial-auth-aad.md)
<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
