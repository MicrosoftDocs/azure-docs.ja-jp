---
title: "Azure App Service での API Apps のサービス プリンシパルの認証 | Microsoft Docs"
description: "Azure App Service でサービス間シナリオで API アプリを保護する方法について説明します。"
services: app-service\api
documentationcenter: .net
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 7ca0bab2-1d29-4d51-b779-dce0edd34f8b
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 06/30/2016
ms.author: alkarche
translationtype: Human Translation
ms.sourcegitcommit: b75f7aa757679a29a42cdfc04799873ee30bab2e
ms.openlocfilehash: 232446806309148f7958609608d4afc28ffea98d
ms.lasthandoff: 02/16/2017


---
# <a name="service-principal-authentication-for-api-apps-in-azure-app-service"></a>Azure App Service での API Apps のサービス プリンシパル認証
## <a name="overview"></a>概要
この記事では、App Service 認証を使用して API アプリへの *内部的* なアクセスを実現する方法について説明します。 ここでいう "内部的" とは、特定の API アプリを自分のアプリケーション コードからしか利用できないようにすることを指します。 このシナリオを App Service で実装する推奨される方法は、呼び出し先の API アプリを Azure AD で保護することです。 Azure AD にアプリケーション ID (サービス プリンシパル) の資格情報を提示することによって取得したベアラー トークンを使って保護対象の API アプリを呼び出します。 Azure AD 以外の方法については、「 **Azure App Service での認証および承認** 」の「 [サービス間認証](../app-service/app-service-authentication-overview.md#service-to-service-authentication)」セクションを参照してください。

この記事では、次の内容について説明します。

* Azure Active Directory (Azure AD) を使用し、認証されていないアクセスから API アプリを保護する方法。
* API アプリ、Web アプリ、モバイル アプリから Azure AD のサービス プリンシパル (アプリ ID) の資格情報を使用して、保護対象の API アプリを使用する方法。 ロジック アプリから API を利用する方法については、「 [App Service でホストされたカスタム API のロジック アプリでの使用](../logic-apps/logic-apps-custom-hosted-api.md)」を参照してください。
* ログオンしたユーザーのブラウザーから、保護対象の API アプリを呼び出すことができないようにする方法。
* 保護対象の API アプリを特定の Azure AD サービス プリンシパルからのみ呼び出すことができるようにする方法。

この記事には&2; つのセクションがあります。

* 「 [Azure App Service でサービス プリンシパル認証を構成する方法](#authconfig) 」セクションでは、一般的に、API アプリの認証を構成する方法と、保護対象の API アプリを使用する方法について説明します。 このセクションは、.NET、Node.js、Java など、App Service でサポートされるすべてのフレームワークに同様に適用されます。
* 「 [.NET 入門チュートリアルの続行](#tutorialstart) 」セクション以降では、App Service で動作する .NET サンプル アプリケーションに対して "内部的なアクセス" を構成する手順を説明します。 

## <a id="authconfig"></a> Azure App Service でサービス プリンシパル認証を構成する方法
ここでは、すべての API アプリに当てはまる一般的な手順を説明します。 To Do List .NET サンプル アプリケーション固有の手順については、「 [.NET 入門チュートリアルの続行](#tutorialstart)」を参照してください。

1. [Azure Portal](https://portal.azure.com/) で、保護する API アプリの **[設定]** ブレードに移動して、**[機能]** セクションを探し、**[認証/承認]** をクリックします。
   
    ![Azure ポータルでの認証と承認](./media/app-service-api-dotnet-user-principal-auth/features.png)
2. **[認証/承認]** ブレードで、**[オン]** をクリックします。
3. **[要求が認証されていない場合のアクション]** ボックスの一覧で、**[Azure Active Directory でのログイン]** を選択します。
4. **[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。
   
    ![Azure ポータルでの [認証/承認] ブレード](./media/app-service-api-dotnet-user-principal-auth/authblade.png)
5. **[Azure Active Directory の設定]** ブレードを構成して新しい Azure AD アプリケーションを作成するか、使用する機能が既に含まれている Azure AD アプリケーションがある場合はそれを使用します。
   
    通常、内部シナリオには、API アプリを呼び出す API アプリが含まれます。 各 API アプリに別の Azure AD アプリケーションを使用するか、1 つの Azure AD アプリケーションのみを使用することができます。
   
    このブレードの詳細な手順については、「 [Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)」を参照してください。
6. 認証プロバイダーの構成ブレードを完了したら、 **[OK]**をクリックします。
7. **[認証/承認]** ブレードで、**[保存]** をクリックします。
   
    ![[保存] をクリックします。](./media/app-service-api-dotnet-service-principal-auth/authsave.png)

この作業が完了すると、App Service は、構成されている Azure AD テナント内の呼び出し元から送信された要求のみを許可するようになります。 保護対象の API アプリでは、認証または承認コードは必要ありません。 API アプリには、使用頻度の高い要求と共にベアラー トークンが HTTP ヘッダーに格納されて渡されます。その情報をコード内で読み取ることによって、特定の呼び出し元 (サービス プリンシパルなど) から送信された要求であることを確認できます。

この認証機能は、.NET、Node.js、Java など、App Service がサポートするすべての言語に対して同様に動作します。 

#### <a name="how-to-consume-the-protected-api-app"></a>保護対象の API アプリを使用する方法
呼び出し元は、API の呼び出しに Azure AD ベアラー トークンを示す必要があります。 呼び出し元がサービス プリンシパル資格情報を使用してベアラー トークンを取得するには、Active Directory Authentication Library (ADAL for [.NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)、[Node.js](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)、または [Java](https://github.com/AzureAD/azure-activedirectory-library-for-java)) を使用します。 トークンを取得するには、ADAL を呼び出すコードから、次の情報を ADAL に示します。

* Azure AD テナントの名前。
* 呼び出し元に関連付けられている Azure AD アプリのクライアント ID とクライアント シークレット (アプリ キー)。
* 保護対象の API アプリに関連付けられている Azure AD アプリケーションのクライアント ID (1 つの Azure AD アプリケーションのみが使用されている場合、呼び出し元と同じクライアント ID です)。

これらの値は、 [Azure クラシック ポータル](https://manage.windowsazure.com/)の Azure AD ページで確認できます。

トークンを取得したら、呼び出し元で Authorization ヘッダーの HTTP 要求に含めます。  App Service はトークンを検証し、保護対象の API アプリに到達する要求を許可します。

#### <a name="how-to-protect-the-api-app-from-access-by-users-in-the-same-tenant"></a>同じテナント内のユーザーからのアクセスに対して API アプリを保護する方法
同じテナント内のユーザーのベアラー トークンは、保護対象の API アプリでは有効と見なされます。  保護対象となる API アプリの呼び出しをサービス プリンシパルに限定するには、トークンに含まれている以下の要求を検証するコードを保護対象の API アプリに追加します。

* `appid` が、呼び出し元に関連付けられている Azure AD アプリケーションのクライアント ID であること。 
* `oid` (`objectidentifier`) が、呼び出し元のサービス プリンシパル ID であること。 

App Service から提供される `objectidentifier` 要求は、X-MS-CLIENT-PRINCIPAL-ID ヘッダーにも格納されます。

### <a name="how-to-protect-the-api-app-from-browser-access"></a>ブラウザー アクセスから API アプリを保護する方法
保護対象の API アプリのコードで要件を検証せず、保護対象の API アプリに別の Azure AD アプリケーションを使用する場合は、Azure AD アプリケーションの Reply URL が API アプリのベース URL と同じにならないようにします。 Reply URL が保護対象の API アプリを直接示している場合、同じ Azure AD テナント内のユーザーは、API アプリの閲覧、ログオン、API の呼び出しを実行できるようになります。

## <a id="tutorialstart"></a> .NET API アプリ チュートリアル シリーズの続行
API アプリの Node.js または Java チュートリアル シリーズを読んでいる場合は、「 [次のステップ](#next-steps) 」セクションに進みます。 

この記事では、引き続き .NET API アプリ チュートリアル シリーズについて説明します。また、[ユーザー認証のチュートリアル](app-service-api-dotnet-user-principal-auth.md)を完了し、ユーザー認証を有効にして Azure でサンプル アプリケーションを実行している前提で話を進めます。

## <a name="set-up-authentication-in-azure"></a>Azure で認証を設定する
このセクションでは、データ層 API アプリに到達できる HTTP 要求のみが有効な Azure AD ベアラー トークンを持つように App Service を構成します。 

次のセクションでは、アプリケーションの資格情報を Azure AD に送信し、ベアラー トークンを取得し、ベアラー トークンをデータ層 API アプリに送信する中間層 API アプリを構成します。 このプロセスを次の図に示します。

![サービス認証のダイアグラム](./media/app-service-api-dotnet-service-principal-auth/appdiagram.png)

チュートリアルの手順に従う際に問題が発生した場合は、チュートリアルの末尾の「 [トラブルシューティング](#troubleshooting) 」を参照してください。 

1. [Azure Portal](https://portal.azure.com/) で、ToDoListDataAPI (データ層) API アプリ用に作成した API アプリの **[設定]** ブレードに移動し、**[設定]** をクリックします。
2. **[設定]** ブレードで、**[機能]** セクションを探し、**[認証/承認]** をクリックします。
   
    ![Azure ポータルでの認証と承認](./media/app-service-api-dotnet-user-principal-auth/features.png)
3. **[認証/承認]** ブレードで、**[オン]** をクリックします。
4. **[要求が認証されていない場合のアクション]** ボックスの一覧で、**[Azure Active Directory でのログイン]** を選択します。
   
    この設定を使用すると、App Service では認証済みの要求のみが API アプリに到達するようになります。 有効なベアラー トークンがある要求の場合、App Service はトークンを API アプリに渡し、一般的に使用される要求が指定された HTTP ヘッダーを生成します。その結果、コードから使いやすい情報になります。
5. **[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。
   
    ![Azure ポータルでの [認証/承認] ブレード](./media/app-service-api-dotnet-user-principal-auth/authblade.png)
6. **[Azure Active Directory 設定]** ブレードで **[Express (簡易)]** をクリックします。
   
    **[Express (簡易)]** オプションを選択すると、Azure は Azure AD [テナント](https://msdn.microsoft.com/en-us/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)に AAD アプリケーションを自動的に作成できます。 
   
    すべての Azure アカウントにテナントが自動的に作成されるので、ユーザーがテナントを作成する必要はありません。
7. まだ選択されていない場合は、**[管理モード]** で **[新しい AD アプリを作成する]** をクリックします。
   
    ポータルは **[アプリの作成]** 入力ボックスを既定値と結びつけます。 既定では、Azure AD アプリケーションの名前は API アプリと同じです。 必要に応じて、別の名前を入力できます。
   
    ![Azure AD の設定](./media/app-service-api-dotnet-service-principal-auth/aadsettings.png)
   
    **注**: また、もう&1; つの方法として、呼び出し元の API アプリと保護対象の API アプリの両方に&1; つの Azure AD アプリケーションを使用することもできます。 この方法を選択する場合、既に前のユーザー認証チュートリアルで Azure AD アプリケーションを作成しているので、ここでは **[新しい AD アプリを作成する]** オプションを選択する必要はありません。 このチュートリアルでは、呼び出し元の API アプリと保護対象の API アプリには別の Azure AD アプリケーションを使用します。
8. **[アプリの作成]** 入力ボックスの値を書き留めておきます。後で、この AAD アプリケーションを Azure クラシック ポータルで検索します。
9. **[OK]**をクリックします。
10. **[認証/承認]** ブレードで、**[保存]** をクリックします。
    
    ![[保存] をクリックします。](./media/app-service-api-dotnet-service-principal-auth/saveauth.png)
    
    **[サインオン URL]** と **[応答 URL]** に API アプリの URL が自動的に設定された Azure Active Directory アプリケーションが App Service により作成されます。 [応答 URL] を使用すると、AAD テナントのユーザーは API アプリにログインしてアクセスできるようになります。

### <a name="verify-that-the-api-app-is-protected"></a>API アプリが保護されていることを確認します。
1. ブラウザーで API アプリの URL に移動します。Azure Portal の **[API アプリ]** ブレードで、**[URL]** の下にあるリンクをクリックします。 
   
    認証されていない要求は API アプリへのアクセスを許可されないため、ログイン画面にリダイレクトされます。 
   
    ブラウザーに Swagger UI が表示される場合、ブラウザーでログオンが既に完了している可能性があります。この場合は、[InPrivate] または [Incognito] ウィンドウを開き、Swagger UI の URL にアクセスします。
2. AAD テナントで、ユーザーの資格情報を使用してログインします。
   
   ログオンすると、ブラウザーに "正常に作成されました" ページが表示されます。

## <a name="configure-the-todolistapi-project-to-acquire-and-send-the-azure-ad-token"></a>Azure AD トークンを取得して送信するように ToDoListAPI プロジェクトを構成します。
このセクションでは、次のタスクを実行します。

* 中間層 API アプリに、Azure AD アプリケーションの資格情報を使用してトークンを取得し、HTTP 要求と共にデータ層 API アプリに送信するコードを追加します。
* Azure AD から必要な資格情報を取得します。
* 中間層 API アプリで Azure App Service ランタイム環境の設定に資格情報を入力します。 

### <a name="configure-the-todolistapi-project-to-acquire-and-send-the-azure-ad-token"></a>Azure AD トークンを取得して送信するように ToDoListAPI プロジェクトを構成します。
Visual Studio で、ToDoListAPI プロジェクトを次のように変更します。

1. *ServicePrincipal.cs* ファイルに含まれるすべてのコードのコメントを解除します。
   
    これは、ADAL for .NET を使用して Azure AD ベアラー トークンを取得するコードです。  このコードでは、後で Azure ランタイム環境に設定するいくつかの構成値を使用します。 コードは次のとおりです。 
   
        public static class ServicePrincipal
        {
            static string authority = ConfigurationManager.AppSettings["ida:Authority"];
            static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
            static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
            static string resource = ConfigurationManager.AppSettings["ida:Resource"];
   
            public static AuthenticationResult GetS2SAccessTokenForProdMSA()
            {
                return GetS2SAccessToken(authority, resource, clientId, clientSecret);
            }
   
            static AuthenticationResult GetS2SAccessToken(string authority, string resource, string clientId, string clientSecret)
            {
                var clientCredential = new ClientCredential(clientId, clientSecret);
                AuthenticationContext context = new AuthenticationContext(authority, false);
                AuthenticationResult authenticationResult = context.AcquireToken(
                    resource,
                    clientCredential);
                return authenticationResult;
            }
        }
   
    **注:** このコードには、ADAL for .NET NuGet パッケージ (Microsoft.IdentityModel.Clients.ActiveDirectory) が必要です。このパッケージはプロジェクトに既にインストールされています。 一からこのプロジェクトを作成している場合は、このパッケージをインストールする必要があります。 API app new-project テンプレートでは、このパッケージは自動的にインストールされません。
2. *Controllers/ToDoListController* で、承認ヘッダーで HTTP 要求にトークンを追加する `NewDataAPIClient` メソッドのコードのコメントを解除します。
   
        client.HttpClient.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
3. ToDoListAPI プロジェクトをデプロイします (プロジェクトを右クリックし、**[発行]、[発行]** の順にクリックします)。
   
    Visual Studio によってプロジェクトがデプロイされ、Web アプリのベース URL がブラウザーで開きます。 これにより 403 エラー ページが表示されます。これは、ブラウザーから Web API ベース URL への移動を試行した場合の通常の動作です。
4. ブラウザーを閉じます。

### <a name="get-azure-ad-configuration-values"></a>Azure AD の構成値を取得する
1. [Azure クラシック ポータル](https://manage.windowsazure.com/)で **[Azure Active Directory]**に移動します。
2. **[ディレクトリ]** タブで AAD テナントをクリックします。
3. **[アプリケーション]、[自分の会社が所有するアプリケーション]** の順にクリックし、チェック マークをクリックします。
4. アプリケーションの一覧から、ToDoListDataAPI (データ層) API アプリの認証を有効にしたときに Azure によって作成されたアプリケーション名をクリックします。
5. [ **構成** ] タブをクリックします。
6. **[クライアント ID]** の値をコピーし、後で取得できる場所に保存します。 
7. Azure クラシック ポータルで、 **[自分の会社が所有するアプリケーション]**の一覧に戻り、中間層 ToDoListAPI API アプリ用に作成した AAD アプリケーション (このチュートリアルではなく、前のチュートリアルで作成したもの) をクリックします。
8. [ **構成** ] タブをクリックします。
9. **[クライアント ID]** の値をコピーし、後で取得できる場所に保存します。
10. **[キー]** の **[時間の選択]** ボックスの一覧で **[1 年間]** を選択します。
11. **[Save]**をクリックします。
    
     ![アプリ キーの生成](./media/app-service-api-dotnet-service-principal-auth/genkey.png)
12. キー値をコピーし、後で取得できる場所に保存します。
    
     ![新しいアプリ キーのコピー](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

### <a name="configure-azure-ad-settings-in-the-middle-tier-api-apps-runtime-environment"></a>中間層 API アプリのランタイム環境で Azure AD 設定を構成する
1. [Azure ポータル](https://portal.azure.com/)を開き、TodoListAPI (中間層) プロジェクトをホストする API アプリの **[API アプリ]** ブレードに移動します。
2. **[設定]、[アプリケーションの設定]** の順にクリックします。
3. **[アプリ設定]** セクションで、次のキーと値を追加します。
   
   | **キー** | ida:Authority |
   | --- | --- |
   | **値** |https://login.microsoftonline.com/\<Azure AD テナント名> |
   | **例** |https://login.microsoftonline.com/contoso.onmicrosoft.com |
   
   | **キー** | ida:ClientId |
   | --- | --- |
   | **値** |呼び出し元のアプリケーションのクライアント ID (中間層 - ToDoListAPI) |
   | **例** |960adec2-b74a-484a-960adec2-b74a-484a |
   
   | **キー** | ida:ClientSecret |
   | --- | --- |
   | **値** |呼び出し元のアプリケーションのアプリ キー (中間層 - ToDoListAPI) |
   | **例** |e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |
   
   | **キー** | ida:Resource |
   | --- | --- |
   | **値** |呼び出し先のアプリケーションのクライアント ID (データ層 - ToDoListDataAPI) |
   | **例** |e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |
   
    **注:** `ida:Resource` に、呼び出し先のアプリケーションの**アプリ ID URI** ではなく**クライアント ID** を使用していることを確認してください。
   
    `ida:ClientId` と `ida:Resource` はこのチュートリアルでは異なる値になります。これは、中間層とデータ層に別々の Azure AD アプリケーションを使用しているためです。 呼び出し元 API アプリと保護対象の API アプリに&1; つの Azure AD アプリケーションを使用した場合、`ida:ClientId` と `ida:Resource` の両方に同じ値を使用します。
   
    このコードでは、ConfigurationManager を使用してこれらの値を取得するので、プロジェクトの Web.config ファイルまたは Azure ランタイム環境に保存できます。 ASP.NET アプリケーションは Azure App Service で実行されますが、環境設定は、Web.config の設定よりも自動的に優先されます。 一般的に、 [機密情報を保存する場合、環境設定の方が Web.config ファイルよりも安全](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)です。
4. **[保存]**をクリックします。
   
    ![[保存] をクリックします。](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

### <a name="test-the-application"></a>アプリケーションをテストする
1. ブラウザーで、AngularJS フロント エンド Web アプリの HTTPS URL にアクセスします。
2. **[To Do List]** タブをクリックし、ユーザーの資格情報を使用して Azure AD テナントにログインします。 
3. to-do 項目を追加し、アプリケーションが動作していることを確認します。
   
    ![To Do List ページ](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)
   
    期待どおりにアプリケーションが動作しない場合は、Azure ポータルで入力したすべての設定を確認します。 すべての設定が正しいと考えられる場合は、このチュートリアルの後半にある「 [トラブルシューティング](#troubleshooting) 」セクションを参照してください。

## <a name="protect-the-api-app-from-browser-access"></a>ブラウザー アクセスから API アプリを保護する
このチュートリアルでは、ToDoListDataAPI (データ層) API アプリ用に別の Azure AD アプリケーションを作成しました。 これまで説明したように、App Service で AAD アプリケーションを作成すると、ユーザーがブラウザーで API アプリの URL にアクセスしてログオンできるように AAD アプリケーションが構成されます。 つまり、サービス プリンシパルだけでなく、Azure AD テナントのエンド ユーザーも API にアクセスすることができます。 

保護対象の API アプリにコードを作成せずに、ブラウザーによるアクセスを防ぐには、AAD アプリケーションの **[応答 URL]** を変更して、API アプリのベース URL とは異なる URL にします。 

### <a name="disable-browser-access"></a>ブラウザー アクセスを無効にする
1. クラシック ポータルの **[構成]** タブで、TodoListService 用に作成した AAD アプリケーションの **[応答 URL]** フィールドの値を、API アプリの URL 以外の有効な URL に変更します。
2. **[Save]**をクリックします。

### <a name="verify-browser-access-no-longer-works"></a>ブラウザー アクセスが無効になったことを確認する
以前のセクションでは、ブラウザーで API アプリの URL にアクセスし、個々のユーザーの資格情報を使用してログオンできることを確認しました。 このセクションでは、同様の方法でログオンできないことを確認します。 

1. 新しいブラウザー ウィンドウで、API アプリの URL にもう一度アクセスします。
2. ログインを求められたらログインします。
3. ログインは成功しますが、エラー ページにリダイレクトされます。
   
    AAD テナントのユーザーがブラウザーからログインして API にアクセスできないように AAD アプリを構成しました。 サービス プリンシパル トークンを使用して API アプリにアクセスすることはできます。確認のために、Web アプリの URL にアクセスし、to-do 項目を追加してみてください。

## <a name="restrict-access-to-a-particular-service-principal"></a>特定のサービス プリンシパルに対するアクセスを制限する
現在のところ、Azure AD テナントのユーザーまたはサービス プリンシパルのトークンを取得できる呼び出し元は、TodoListDataAPI (データ層) API アプリを呼び出すことができます。 たとえば、データ層 API アプリで、TodoListAPI (中間層) API アプリからの呼び出しや、特定のサービス プリンシパルからの呼び出しのみを受け入れるようにすることができます。 

このような制限を追加するには、着信呼び出しの `appid` と `objectidentifier` の各要求を検証するコードを追加します。

このチュートリアルでは、コントローラー アクションでアプリ ID とサービス プリンシパル ID を直接検証するコードを設定します。  または、カスタムの `Authorize` 属性を使用するか、スタートアップ シーケンスでこの検証を実行します (OWIN ミドルウェアなど)。 後者の例については、 [こちらのサンプル アプリケーション](https://github.com/mohitsriv/EasyAuthMultiTierSample/blob/master/MyDashDataAPI/Startup.cs)を参照してください。 

TodoListDataAPI プロジェクトを次のように変更します。

1. *Controllers/TodoListController.cs* ファイルを開きます。
2. `trustedCallerClientId` と `trustedCallerServicePrincipalId` を設定する行のコメントを解除します。
   
        private static string trustedCallerClientId = ConfigurationManager.AppSettings["todo:TrustedCallerClientId"];
        private static string trustedCallerServicePrincipalId = ConfigurationManager.AppSettings["todo:TrustedCallerServicePrincipalId"];
3. CheckCallerId メソッドのコードのコメントを解除します。 このメソッドは、コントローラーでアクション メソッドが開始されるたびに呼び出されます。 
   
        private static void CheckCallerId()
        {
            string currentCallerClientId = ClaimsPrincipal.Current.FindFirst("appid").Value;
            string currentCallerServicePrincipalId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            if (currentCallerClientId != trustedCallerClientId || currentCallerServicePrincipalId != trustedCallerServicePrincipalId)
            {
                throw new HttpResponseException(new HttpResponseMessage { StatusCode = HttpStatusCode.Unauthorized, ReasonPhrase = "The appID or service principal ID is not the expected value." });
            }
        }
4. Azure App Service に ToDoListDataAPI プロジェクトを再デプロイします。
5. ブラウザーで AngularJS フロントエンド Web アプリケーションの HTTPS URL にアクセスし、ホーム ページの **[To Do List]** タブをクリックします。
   
    バック エンドの呼び出しは失敗しているので、アプリケーションは動作していません。 新しいコードは、実際の appid と objectidentifier を確認していますが、確認に使用する正しい値はまだ取得していません。 ブラウザーの開発者ツール コンソールには、サーバーから HTTP 401 エラーが返されているというレポートが表示されます。
   
    ![開発者用ツール コンソールのエラー](./media/app-service-api-dotnet-service-principal-auth/webapperror.png)
   
    次の手順では、目的の値を構成します。
6. Azure AD PowerShell を使用して、TodoListWebApp プロジェクト用に作成した Azure AD アプリケーションのサービス プリンシパル値を取得します。
   
    a. Azure PowerShell のインストール手順とサブスクリプションへの接続方法については、「[Azure Resource Manager での Azure PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。
   
    b. サービス プリンシパルの一覧を取得するために、`Login-AzureRmAccount` コマンド、`Get-AzureRmADServicePrincipal` コマンドの順に実行します。
   
    c. TodoListAPI アプリケーションのサービス プリンシパルの objectid を検索し、後でコピーできる場所に保存します。
7. Azure ポータルで ToDoListDataAPI プロジェクトをデプロイした API アプリ用の API アプリ ブレードに移動します。
8. **[設定]、[アプリケーションの設定]** の順にクリックします。
9. **[アプリ設定]** セクションで、次のキーと値を追加します。
   
   | **キー** | todo:TrustedCallerServicePrincipalId |
   | --- | --- |
   | **値** |呼び出し元アプリケーションのサービス プリンシパル ID |
   | **例** |4f4a94a4-6f0d-4072-4f4a94a4-6f0d-4072 |
   
   | **キー** | todo:TrustedCallerClientId |
   | --- | --- |
   | **値** |呼び出し元アプリケーションのクライアント ID (TodoListAPI Azure AD アプリケーションからコピー) |
   | **例** |960adec2-b74a-484a-960adec2-b74a-484a |
10. **[保存]**をクリックします。
    
     ![[保存] をクリックします。](./media/app-service-api-dotnet-service-principal-auth/trustedcaller.png)
11. ブラウザーで Web アプリケーションの URL に戻り、ホーム ページの **[To Do List]** タブをクリックします。
    
     今回は、信頼済みの呼び出し元アプリ ID とサービス プリンシパル ID が期待される値なので、アプリケーションは期待どおりに動作します。
    
     ![To Do List ページ](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

## <a name="building-the-projects-from-scratch"></a>一からのプロジェクトのビルド
**Azure API アプリケーション** プロジェクト テンプレートを使用し、既定の Values コントローラーを ToDoList コントローラーに置き換えることによって、2 つの Web API プロジェクトを作成しました。 ToDoListAPI プロジェクトで Azure AD サービス プリンシパル トークンを取得するために、 [Active Directory Authentication Library (ADAL) for .NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) NuGet パッケージをインストールしました。

ToDoListAngular のような Web API バックエンドで AngularJS 単一ページ アプリケーションを作成する方法については、「[Hands On Lab: Build a Single Page Application (SPA) with ASP.NET Web API and Angular.js (ハンズオン ラボ: ASP.NET Web API と Angular.js で単一ページ アプリケーション (SPA) を構築する)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs)」を参照してください。 Azure AD 認証コードを追加する方法については、「 [Azure AD で AngularJS シングル ページ アプリをセキュリティで保護する](../active-directory/active-directory-devquickstarts-angular.md)」を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング
[!INCLUDE [troubleshooting](../../includes/app-service-api-auth-troubleshooting.md)]

* ToDoListAPI (中間層) と ToDoListDataAPI (データ層) を混同しないよう注意してください。 たとえば、このチュートリアルでは認証をデータ層 API アプリに追加しますが、 **アプリ キーは、中間層 API アプリに作成した Azure AD アプリケーションからのものである必要があります。**

## <a name="next-steps"></a>次のステップ
これは、API アプリ シリーズの最後のチュートリアルです。 

Azure Active Directory の詳細については、次のリソースを参照してください。

* [Azure AD 開発者ガイド](http://aka.ms/aaddev)
* [Azure AD のシナリオ](http://aka.ms/aadscenarios)
* [Azure AD のサンプル](http://aka.ms/aadsamples)
  
    [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) サンプルは、このチュートリアルで紹介した内容と似ていますが、App Service 認証は使用しません。

Visual Studio を使用するか、[ソース管理システム](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)から[デプロイを自動化](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)して、Visual Studio プロジェクトを API アプリにデプロイする他の方法については、[Azure App Service アプリのデプロイ](../app-service-web/web-sites-deploy.md)に関する記事を参照してください。


