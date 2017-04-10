---
title: "Azure App Service での API Apps のユーザー認証 | Microsoft Docs"
description: "認証されたユーザーのみがアクセスできるよう Azure App Service で API アプリを保護する方法について説明します。"
services: app-service\api
documentationcenter: .net
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 3896760d-46ff-4b67-b98a-edd233f24758
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 06/30/2016
ms.author: alkarche
translationtype: Human Translation
ms.sourcegitcommit: 16a2c6f8e704528c38054bba394e3d11098077e0
ms.openlocfilehash: c39e7bca6f339a1797a9344778e6a01ffa8adecc
ms.lasthandoff: 01/24/2017


---
# <a name="user-authentication-for-api-apps-in-azure-app-service"></a>Azure App Service の API Apps でのユーザー認証
## <a name="overview"></a>概要
この記事では、認証されたユーザーのみが呼び出せるように Azure API アプリを保護する方法を説明します。 この記事を読む前に、「 [Azure App Service での認証および承認](../app-service/app-service-authentication-overview.md)」をお読みください。

学習内容:

* 認証プロバイダーを構成する方法と、Azure Active Directory (Azure AD) の詳細。
* [Active Directory Authentication Library (ADAL) for JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js)を使用して保護された API アプリを使用する方法。

この記事には&2; つのセクションがあります。

* 「[Azure App Service でユーザー認証を構成する方法](#authconfig)」セクションでは、API アプリ用にユーザー認証を構成する一般的な方法について説明します。この説明は、.NET、Node.js、Java など、App Service でサポートされるすべてのフレームワークに適用されます。
* 「 [.NET API アプリ チュートリアルの続行](#tutorialstart) 」セクション以降では、ユーザー認証に Azure Active Directory を使用するように、.NET バックエンドと AngularJS フロントエンドによりサンプル アプリケーションを構成する方法について説明します。 

## <a id="authconfig"></a> Azure App Service でユーザー認証を構成する方法
ここでは、すべての API アプリに当てはまる一般的な手順を説明します。 To Do List .NET サンプル アプリケーション固有の手順については、「 [.NET 入門チュートリアルの続行](#tutorialstart)」を参照してください。

1. [Azure Portal](https://portal.azure.com/) で、保護する API アプリの **[設定]** ブレードに移動して、**[機能]** セクションを探し、**[認証/承認]** をクリックします。
   
    ![Azure ポータルの認証と承認](./media/app-service-api-dotnet-user-principal-auth/features.png)
2. **[認証/承認]** ブレードで、**[オン]** をクリックします。
3. **[要求が認証されていない場合のアクション]** ドロップダウン リストから、いずれかのオプションを選択します。
   
   * 認証された呼び出しのみが API アプリにアクセスできるようにする場合は、 **[... でのログイン]** オプションのいずれかを選択します。 このオプションを指定すると、API アプリで実行するコードを記述することなく、API アプリを保護できます。
   * すべての呼び出しが API アプリにアクセスできるようにする場合は、 **[要求の許可 (操作不要)]**を選択します。 このオプションを使用すると、認証されていない呼び出し元に認証プロバイダーを選択させることができます。 このオプションを使用する場合は、承認を処理するコードを記述する必要があります。
     
     詳細については、「 [Azure App Service での API Apps の認証と承認](app-service-api-authentication.md#multiple-protection-options)」を参照してください。
4. **[認証プロバイダー]**を&1; つ以上選択します。
   
    下の図では、すべての呼び出し元が Azure AD によって認証される必要がある選択肢が示されています。
   
    ![Azure ポータルの [認証/承認] ブレード](./media/app-service-api-dotnet-user-principal-auth/authblade.png)
   
    認証プロバイダーを選択すると、そのプロバイダーの構成ブレードが表示されます。 
   
    認証プロバイダー構成ブレードの構成方法の詳細については、「[Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)」を参照してください。 (リンクで Azure AD に関する記事に移動しますが、記事自体に他の認証プロバイダーの記事にリンクするタブが含まれています)。
5. 認証プロバイダーの構成ブレードを完了したら、 **[OK]**をクリックします。
6. **[認証/承認]** ブレードで、**[保存]** をクリックします。

これが終わると、すべての API 呼び出しは API アプリに到達する前に App Service によって認証されるようになります。 認証サービスの動作は、.NET、Node.js、Java など、App Service がサポートするすべての言語に対して同じです。 

呼び出し元が認証された API 呼び出しを行うには、認証プロバイダーの OAuth 2.0 ベアラー トークンを、HTTP 要求の承認ヘッダーに組み込みます。 トークンは、認証プロバイダーの SDK を使用して取得できます。

## <a id="tutorialstart"></a> .NET API アプリ チュートリアルの続行
API アプリの Node.js または Java チュートリアルに従って学習している場合は、次の記事「 [Azure App Service での API Apps のサービス プリンシパル認証](app-service-api-dotnet-service-principal-auth.md)」に進みます。 

API アプリの .NET チュートリアルに従って学習していて、[1 番目](app-service-api-dotnet-get-started.md)と [2 番目](app-service-api-cors-consume-javascript.md)のチュートリアルで説明されているようにサンプル アプリケーションを既にデプロイしている場合は、「[App Service と Azure AD での認証の設定](#azureauth)」セクションに進みます。

1 番目と&2; 番目のチュートリアルをとばしてこのチュートリアルを読みたい場合は、自動プロセスでサンプル アプリケーションをデプロイして始める方法が説明されている以下の手順に従ってください。

> [!NOTE]
> 以下の手順の開始点は、最初の&2; つのチュートリアルを実行した場合と同じになります。例外が&1; つあります。それは Visual Studio では各プロジェクトがどの Web アプリまたは API アプリにデプロイされるのかまだ識別できないという点です。 つまり、正しいターゲットにデプロイする方法はこのチュートリアルではわかりません。 自分でデプロイする方法がよくわからない場合は、この自動デプロイ プロセスで始めるのではなく、チュートリアル シリーズの [1 番目のチュートリアル](app-service-api-dotnet-get-started.md)から従うことをお勧めします。
> 
> 

1. [1 番目のチュートリアル](app-service-api-dotnet-get-started.md)に一覧されている前提条件がすべて満たされていることを確認します。 前提条件が満たされていることに加えて、これらの認証チュートリアルでは、App Service Web アプリと API アプリが Visual Studio および Azure ポータルで操作されていることを前提とします。
2. [To Do List サンプル リポジトリ Readme ファイル](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/readme.md)の **[Deploy to Azure (Azure へのデプロイ)]** ボタンを使用して API アプリと Web アプリをデプロイしてください。 作成された Azure リソース グループをメモしておきます。これは後で Web アプリ名と API アプリ名を検索する場合に使用できます。
3. [To Do List サンプル リポジトリ](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) をダウンロードまたは複製して、Visual Studio でローカルに使用するコードを取得します。

## <a id="azureauth"></a> App Service と Azure AD での認証の設定
現在は、Azure App Service で実行しているアプリケーションはユーザーの認証を必要としません。 このセクションでは、次のタスクを実行して認証を追加します。

* 中間層 API アプリを呼び出すために Azure Active Directory (Azure AD) 認証を必要とするように App Service を構成します。
* Azure AD アプリケーションを作成します。
* AngularJS フロントエンドにログオンした後でベアラー トークンを送信するように Azure AD アプリケーションを構成します。 

チュートリアルの手順に従う際に問題が発生した場合は、チュートリアルの末尾の「 [トラブルシューティング](#troubleshooting) 」を参照してください。 

### <a name="configure-authentication-for-the-middle-tier-api-app"></a>中間層 API アプリ用に認証を構成する
1. [Azure Portal](https://portal.azure.com/) で、ToDoListAPI プロジェクト用に作成した API アプリの **[設定]** ブレードに移動して、**[機能]** セクションを探し、**[認証/承認]** をクリックします。
   
    ![Azure ポータルの認証と承認](./media/app-service-api-dotnet-user-principal-auth/features.png)
2. **[認証/承認]** ブレードで、**[オン]** をクリックします。
3. **[要求が認証されていない場合のアクション]** ボックスの一覧で、**[Azure Active Directory でのログイン]** を選択します。
   
    このオプションを指定すると、認証されていない要求は API アプリに到達しなくなります。 
4. **[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。
   
    ![Azure ポータルの [認証/承認] ブレード](./media/app-service-api-dotnet-user-principal-auth/authblade.png)
5. **[Azure Active Directory 設定]** ブレードで **[Express (簡易)]** をクリックします。
   
    ![Azure ポータルの認証/承認の [Express] オプション](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)
   
    **[Express (簡易)]** オプションを選択すると、App Service は Azure AD [テナント](https://msdn.microsoft.com/en-us/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)に Azure AD アプリケーションを自動的に作成できます。 
   
    すべての Azure アカウントにテナントが自動的に作成されるので、ユーザーがテナントを作成する必要はありません。
6. 選択されていない場合は **[管理モード]** で **[新しい AD アプリを作成する]** をクリックし、**[アプリの作成]** ボックス内の値を書き留めておきます。後で、この AAD アプリケーションを Azure クラシック ポータルで検索します。
   
    ![Azure ポータルの Azure AD 設定](./media/app-service-api-dotnet-user-principal-auth/aadsettings2.png)
   
    Azure が Azure AD テナントに Azure AD アプリケーションを、指定した名前で自動的に作成します。 既定では、Azure AD アプリケーションの名前は API アプリと同じです。 必要に応じて、別の名前を入力できます。
7. **[OK]**をクリックします。
8. **[認証/承認]** ブレードで、**[保存]** をクリックします。
   
    ![[保存] をクリックします。](./media/app-service-api-dotnet-user-principal-auth/authsave.png)

これで、Azure AD テナントのユーザーだけが API アプリを呼び出せるようになりました。

### <a name="optional-test-the-api-app"></a>省略可能: API アプリをテストする
1. ブラウザーで API アプリの URL に移動します。Azure Portal の **[API アプリ]** ブレードで、**[URL]** の下にあるリンクをクリックします。  
   
    認証されていない要求は API アプリへのアクセスを許可されないため、ログイン画面にリダイレクトされます。
   
    ブラウザーに "正常に作成されました" ページが表示される場合、ブラウザーは既にログインしている可能性があります。その場合は、InPrivate または Incognito ウィンドウを開き、API アプリの URL に移動します。
2. Azure AD テナントのユーザーの資格情報を使用してログオンします。
   
    ログオンすると、ブラウザーに "正常に作成されました" ページが表示されます。
3. ブラウザーを閉じます。

### <a name="configure-the-azure-ad-application"></a>Azure AD アプリケーションを構成する
Azure AD 認証を構成すると、App Service によって Azure AD アプリケーションが自動的に作成されました。 既定では、新しい Azure AD アプリケーションは API アプリの URL にベアラー トークンを提供するように構成されます。 このセクションでは、中間層 API アプリに直接提供するのではなく、AngularJS フロントエンドにベアラー トークンを提供するように、Azure AD アプリケーションを構成します。 AngularJS フロントエンドは、API アプリを呼び出すときに、トークンを API アプリに送信します。

> [!NOTE]
> プロセスをできるだけシンプルにするために、このチュートリアルではフロントエンドと中間層の両方の API アプリに&1; つの Azure AD アプリケーションを使用します。 別の選択肢として、2 つの Azure AD アプリケーションを使用します。 この場合は、中間層の Azure AD アプリケーションを呼び出すための、フロントエンドの Azure AD アプリケーション アクセス許可の付与が必要になる場合があります。 このマルチ アプリケーションの手法では、各階層へのアクセス許可をより細かく制御できます。
> 
> 

1. [Azure クラシック ポータル](https://manage.windowsazure.com/)で **[Azure Active Directory]**に移動します。
   
   アクセスする必要のある特定の Azure Active Directory 設定が現在の Azure ポータルでまだ利用できないため、クラシック ポータルを使用する必要があります。
2. **[ディレクトリ]** タブで AAD テナントをクリックします。
   
   ![クラシック ポータルでの Azure AD](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)
3. **[アプリケーション]、[自分の会社が所有するアプリケーション]** の順にクリックし、チェック マークをクリックします。
   
   新しいアプリケーションを確認するには、ページを更新する必要がある場合があります。
4. アプリケーションの一覧から、API アプリの認証を有効にしたときに Azure によって作成されたアプリケーション名をクリックします。
   
   ![[Azure AD アプリケーション] タブ](./media/app-service-api-dotnet-user-principal-auth/appstab.png)
5. **[構成]**をクリックします。
   
   ![[Azure AD 構成] タブ](./media/app-service-api-dotnet-user-principal-auth/configure.png)
6. **[サインオン URL]** に、AngularJS Web アプリケーションの URL から末尾のスラッシュを除いたものを設定します。
   
   例: https://todolistangular.azurewebsites.net
   
   ![[サインオン URL]](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)
7. **[応答 URL]** に、Web アプリケーションの URL から末尾のスラッシュを除いたものを設定します。
   
   例: https://todolistsangular.azurewebsites.net
8. [ **Save**] をクリックします。
   
   ![[応答 URL]](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)
9. ページ下部で **[マニフェストの管理]、[マニフェストのダウンロード]** の順にクリックします。
   
   ![マニフェストのダウンロード](./media/app-service-api-dotnet-user-principal-auth/downloadmanifest.png)
10. 編集できる場所にファイルをダウンロードします。
11. ダウンロードしたマニフェスト ファイル内で、`oauth2AllowImplicitFlow` プロパティを検索します。 このプロパティの値を `false` から `true` に変更し、ファイルを保存します。
    
    この設定は、JavaScript のシングル ページ アプリケーションからのアクセスに必要です。 これにより、Oauth 2.0 ベアラー トークンが URL フラグメントで返されるようになります。
12. **[マニフェストの管理]、[マニフェストのアップロード]** の順にクリックし、前の手順で更新したファイルをアップロードします。
    
    ![マニフェストのアップロード](./media/app-service-api-dotnet-user-principal-auth/uploadmanifest.png)
13. **[クライアント ID]** の値をコピーし、後で取得できる場所に保存します。

## <a name="configure-the-todolistangular-project-to-use-authentication"></a>認証を使用するように ToDoListAngular プロジェクトを構成する
このセクションでは、AngularJS フロントエンドを、ログオンしているユーザーのベアラー トークンを Active Directory Authentication Library (ADAL) for JS を使用して Azure AD から取得するように変更します。 次の図で示すように、コードには中間層に送信される HTTP 要求のトークンが含まれます。 

![ユーザー認証のダイアグラム](./media/app-service-api-dotnet-user-principal-auth/appdiagram.png)

ToDoListAngular プロジェクトのファイルを次のように変更します。

1. *index.cshtml* ファイルを開きます。
2. Active Directory Authentication Library (ADAL) for JS スクリプトを参照している行のコメントを外します。
   
        <script src="app/scripts/adal.js"></script>
        <script src="app/scripts/adal-angular.js"></script>
3. *app/scripts/app.js* ファイルを開きます。
4. "without authentication" とマークされているコード ブロックをコメント化し、"with authentication" とマークされているコード ブロックのコメントを解除します。
   
    この変更により、ADAL JS 認証プロバイダーが参照されるようになり、その構成値が提供されます。 次の手順では、API アプリと Azure AD アプリケーションの構成値を設定します。
5. `endpoints` 変数を設定するコードで、ToDoListAPI プロジェクトに作成した API アプリの URL を API URL に設定し、Azure クラシック ポータルからコピーしたクライアント ID を Azure AD アプリケーション ID に設定します。
   
    コードは、現在次の例のようです。
   
        var endpoints = {
            "https://todolistapi0121.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
        };
6. `adalProvider.init` の呼び出しで、`tenant` にテナント名を設定し、`clientId` に前のステップで使用したのと同じ値を設定します。
   
    コードは、現在次の例のようです。
   
        adalProvider.init(
            {
                instance: 'https://login.microsoftonline.com/', 
                tenant: 'contoso.onmicrosoft.com',
                clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
                extraQueryParameter: 'nux=1',
                endpoints: endpoints
            },
            $httpProvider
            );
   
    `app.js` に対するこれらの変更では、呼び出し元のコードと呼び出される API の両方が同じ Azure AD アプリケーションに存在していることを指定します。
7. *app/scripts/homeCtrl.js* ファイルを開きます。
8. "without authentication" とマークされているコード ブロックをコメント化し、"with authentication" とマークされているコード ブロックのコメントを解除します。
9. *app/scripts/indexCtrl.js* ファイルを開きます。
10. "without authentication" とマークされているコード ブロックをコメント化し、"with authentication" とマークされているコード ブロックのコメントを解除します。

### <a name="deploy-the-todolistangular-project-to-azure"></a>ToDoListAngular プロジェクトを Azure にデプロイする
1. **ソリューション エクスプローラー**で ToDoListAngular プロジェクトを右クリックし、**[発行]** をクリックします。
2. **[発行]**をクリックします。
   
    Visual Studio によってプロジェクトがデプロイされ、Web アプリのベース URL がブラウザーで開きます。 これにより 403 エラー ページが表示されます。これは、ブラウザーから Web API ベース URL への移動を試行した場合の通常の動作です。
   
    アプリケーションをテストする前に、中間層 API アプリを変更する必要があります。
3. ブラウザーを閉じます。

## <a name="configure-the-todolistapi-project-to-use-authentication"></a>認証を使用するように ToDoListAPI プロジェクトを構成する
現在、ToDoListAPI プロジェクトは `owner` の値として "*" を ToDoListDataAPI に送信します。 このセクションでは、ログオン ユーザーの ID を送信するようにコードを変更します。

ToDoListAPI プロジェクトで次の変更を行います。

1. *Controllers/ToDoListController.cs* ファイルを開き、各アクション メソッドで `owner` を Azure AD の `NameIdentifier` 要求値に設定する行のコメントを解除します。 次に例を示します。
   
        owner = ((ClaimsIdentity)User.Identity).FindFirst(ClaimTypes.NameIdentifier).Value;
   
    **重要:** `ToDoListDataAPI` メソッドのコードをコメント解除しないでください。これは、後でサービス プリンシパルの認証チュートリアルで実行します。

### <a name="deploy-the-todolistapi-project-to-azure"></a>ToDoListAPI プロジェクトを Azure にデプロイする
1. **ソリューション エクスプローラー**で ToDoListAPI プロジェクトを右クリックし、**[発行]** をクリックします。
2. **[発行]**をクリックします。
   
    Visual Studio によってプロジェクトがデプロイされ、API アプリのベース URL がブラウザーで開きます。
3. ブラウザーを閉じます。

### <a name="test-the-application"></a>アプリケーションをテストする
1. **HTTP ではなく HTTPS を使用して**、Web アプリケーションの URL にアクセスします。
2. **[To Do List]** タブをクリックします。
   
    ログインを求められます。
3. AAD テナントのユーザー資格情報でログインします。
4. **[To Do List]** ページが表示されます。
   
   ![To Do List ページ](./media/app-service-api-dotnet-user-principal-auth/webappindex.png)
   
   これまではすべてが所有者 "*" に対するものであったため、To Do 項目は表示されません。 現在、中間層はログオン ユーザーの項目を要求するようになっていますが、まだ何も作成されていません。
5. 新しい To Do 項目を追加し、アプリケーションが動作していることを確認します。
6. 別のブラウザー ウィンドウで、ToDoListDataAPI API アプリの Swagger UI URL に移動し、**[ToDoList]、[Get]** の順にクリックします。 `owner` パラメーターにアスタリスクを入力し、 **[Try it out]**をクリックします。
   
   応答では、新しい To Do 項目に、Owner プロパティの実際の Azure AD ユーザー ID が設定されていることが示されます。
   
   ![JSON 応答の所有者 ID](./media/app-service-api-dotnet-user-principal-auth/todolistapiauth.png)

## <a name="building-the-projects-from-scratch"></a>一からのプロジェクトのビルド
**Azure API アプリケーション** プロジェクト テンプレートを使用し、既定の Values コントローラーを ToDoList コントローラーに置き換えることによって、2 つの Web API プロジェクトを作成しました。 

Web API 2 バックエンドで AngularJS 単一ページ アプリケーションを作成する方法については、「[Hands On Lab: Build a Single Page Application (SPA) with ASP.NET Web API and Angular.js (ハンズオン ラボ: ASP.NET Web API と Angular.js で単一ページ アプリケーション (SPA) を構築する)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs)」を参照してください。 Azure AD 認証コードを追加する方法については、次のリソースを参照してください。

* [Azure AD で AngularJS シングル ページ アプリをセキュリティで保護する](../active-directory/active-directory-devquickstarts-angular.md)。
* [Introducing ADAL JS v1 (ADAL JS v1 の概要)](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)

## <a name="troubleshooting"></a>トラブルシューティング
[!INCLUDE [troubleshooting](../../includes/app-service-api-auth-troubleshooting.md)]

* ToDoListAPI (中間層) と ToDoListDataAPI (データ層) を混同しないよう注意してください。 たとえば、認証をデータ層ではなく中間層 API アプリに追加していることを確認します。 
* また、AngularJS ソース コードが中間層 API アプリ URL (ToDoListDataAPI ではなく、ToDoListAPI) と正しい Azure AD クライアント ID を参照していることを確認します。 

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、API アプリで App Service 認証を使用する方法、および ADAL JS ライブラリを使用して API アプリを呼び出す方法を説明しました。 次のチュートリアルでは、 [サービス間シナリオで API アプリへのアクセスをセキュリティで保護する](app-service-api-dotnet-service-principal-auth.md)方法を学習します。


