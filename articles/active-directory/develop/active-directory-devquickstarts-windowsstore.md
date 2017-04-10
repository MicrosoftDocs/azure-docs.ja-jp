---
title: "Azure AD Windows ストアの概要 | Microsoft Docs"
description: "サインインのために Azure AD と統合され、OAuth を用いて Azure AD の保護対象 API を呼び出す Windows ストア アプリを構築します。"
services: active-directory
documentationcenter: windows
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 3b96a6d1-270b-4ac1-b9b5-58070c896a68
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 09/16/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 3a78fe76119d1c748b5b75c00148a3e2a6f4a2b8
ms.openlocfilehash: adfc28c7e41f49ce65309a316703fba57463040f
ms.lasthandoff: 02/17/2017


---
# <a name="integrate-azure-ad-with-windows-store-apps"></a>Azure AD と Windows ストア アプリの統合
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Windows ストア用アプリを開発する場合、Azure Active Directory (Azure AD) を使用すると、Active Directory アカウントを使用してユーザーの認証処理を容易に行うことができます。 Azure AD との統合により、Azure AD で保護される任意の Web API (Office 365 API や Azure API など) をアプリで安全に使用できるようになります。

保護されたリソースにアクセスする必要がある Windows ストア デスクトップ アプリのために、Azure AD には Active Directory 認証ライブラリ (ADAL) が用意されています。 ADAL の唯一の目的は、アプリがアクセス トークンを容易に取得できるようにすることです。 これがいかに簡単かを示すために、この記事では次のような機能を備えた DirectorySearcher Windows ストア アプリの構築方法について説明します。

* [OAuth 2.0 認証プロトコル](https://msdn.microsoft.com/library/azure/dn645545.aspx)を使用して、Azure AD Graph API を呼び出すためのアクセス トークンを取得します。
* 指定されたユーザー プリンシパル名 (UPN) を持つユーザーをディレクトリで検索します。
* ユーザーのサインアウト処理を行います。

## <a name="before-you-get-started"></a>開始する前に
* [スケルトン プロジェクトをダウンロード](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip)するか、または[完全なサンプル](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip)をダウンロードします。 どちらのダウンロードも Visual Studio 2015 ソリューションです。
* ユーザーの作成先となりアプリの登録先となる Azure AD テナントも必要です。 テナントを所有していない場合は、「 [How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](active-directory-howto-tenant.md)」を参照して取得してください。

準備ができたら、次の&3; つのセクションの手順を実行します。

## <a name="step-1-register-the-directorysearcher-app"></a>手順 1: DirectorySearcher アプリを登録する
アプリでトークンを取得できるようにするには、まず、アプリを Azure AD テナントに登録し、Azure AD Graph API にアクセスするためのアクセス許可を付与する必要があります。 その方法は次のとおりです。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 上部のバーで、自分のアカウントをクリックします。 次に、**[ディレクトリ]** の一覧から、アプリを登録する Active Directory テナントを選択します。
3. 左側のウィンドウで **[その他のサービス]** をクリックし、**[Azure Active Directory]** を選択します。
4. **[アプリの登録]** をクリックし、**[追加]** を選択します。
5. 画面の指示に従い、**ネイティブ クライアント アプリケーション**を作成します 
  * **[名前]** は、ユーザーに対して表示されるアプリ名です。
  * **[リダイレクト URI]** には、Azure AD がトークン応答を返すために使用するスキームと文字列の組み合わせを設定します。 この時点では、プレースホルダー値を入力します (**http://DirectorySearcher** など)。 この値は後で置き換えます。
6. 登録が完了すると、Azure AD によって、一意のアプリケーション ID がアプリに割り当てられます。 後で必要になるため、**[アプリケーション]** タブの値をコピーします。
7. **[設定]** ページで、**[必要なアクセス許可]** を選択し、**[追加]** を選択します。
8. **Azure Active Directory** アプリに対し、API として **Microsoft Graph** を選択します。
9. **[デリゲートされたアクセス許可]** で **[Access the directory as the signed-in user] (サインイン ユーザーとしてディレクトリにアクセスする)** アクセス許可を追加します。 この操作によって、アプリが Graph API にユーザーを照会できるようになります。

## <a name="step-2-install-and-configure-adal"></a>手順 2: ADAL をインストールして構成する
アプリを Azure AD に登録したので、ADAL をインストールし、ID 関連のコードを記述できます。 ADAL が Azure AD と通信できるようにするには、ADAL にアプリの登録に関する情報を入力する必要があります。

1. パッケージ マネージャー コンソールを使用して ADAL を DirectorySearcher プロジェクトに追加します。

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

2. DirectorySearcher プロジェクトで、MainPage.xaml.cs を開きます。
3. **Config Values** 領域の値を Azure Portal で入力した値に置き換えます。 これらの値は、コードで ADAL を使用する際に常に参照されます。
  * *tenant* には、Azure AD テナントのドメイン (contoso.onmicrosoft.com など) を指定します。
  * *clientId* には、ポータルからコピーしたアプリのクライアント ID を指定します。
4. ここで、Windows ストア アプリのコールバック URI を調べる必要があります。 `MainPage` メソッドの次の行にブレークポイントを設定します。
    ```
    redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
    ```
5. ソリューションをビルドし、すべてのパッケージ参照が復元されていることを確認します。 パッケージが不足している場合は、Nuget パッケージ マネージャーを開いて復元します。
6. アプリを実行し、ブレークポイントに到達したら `redirectUri` の値をコピーします。 この値は次のようになります。

    ```
    ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
    ```

7. Azure Portal でアプリケの **[設定]** タブに戻り、**RedirectUri** に前の手順の値を指定して追加します。  

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>手順 3: ADAL を使用して Azure AD からトークンを取得する
ADAL を使用する際の基本方針は、アプリがアクセス トークンを必要とする場合は常に、アプリでは `authContext.AcquireToken(…)` を呼び出すだけで、残りの処理は ADAL にまかせることです。  

1. アプリの `AuthenticationContext` を初期化します。これは ADAL のプライマリ クラスです。 このアクションにより、ADAL が Azure AD と通信してトークンのキャッシュ方法を通知するために必要な調整項目が ADAL に渡されます。

    ```C#
    public MainPage()
    {
        ...

        authContext = new AuthenticationContext(authority);
    }
    ```

2. `Search(...)` メソッドを見つけます。これは、ユーザーがアプリの UI の **[Search] (検索)** ボタンをクリックしたときに呼び出されます。 このメソッドは、指定された検索語句で UPN が始まるユーザーをクエリするための、Azure AD Graph API に対する GET 要求を実行します。 Graph API に対してクエリを実行するために、要求の **Authorization** ヘッダーにアクセス トークンを設定します。 ここで、ADAL の出番です。

    ```C#
    private async void Search(object sender, RoutedEventArgs e)
    {
        ...
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "authentication_canceled")
            {
                ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
            }
            return;
        }
        ...
    }
    ```
    アプリが `AcquireTokenAsync(...)` を呼び出すことによってトークンを要求すると、ADAL はユーザーに資格情報を要求することなく、トークンを返そうとします。 ADAL は、トークンの取得にはユーザーのサインインが必要であると判断した場合、サインイン ダイアログ ボックスを表示し、ユーザーの資格情報を収集し、認証が成功するとトークンを返します。 何らかの理由により ADAL がトークンを返せない場合、 *AuthenticationResult* ステータスはエラーになります。
3. 次に、先ほど取得したアクセス トークンを使用します。 また、`Search(...)` メソッドで、Graph API GET 要求の **Authorization** ヘッダーにトークンを設定します。

    ```C#
    // Add the access token to the Authorization header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

    ```
4. `AuthenticationResult` オブジェクトを使用すると、アプリのユーザーに関する情報 (ユーザー ID など) を表示できます。

    ```C#
    // Update the page UI to represent the signed-in user
    ActiveUser.Text = result.UserInfo.DisplayableId;
    ```
5. また、ADAL を使用してユーザーをアプリからサインアウトさせることもできます。 ユーザーが **[Sing Out] (サインアウト)** ボタンをクリックした場合、次回の `AcquireTokenAsync(...)` の呼び出しではサインイン ビューが表示されるようにします。 ADAL を使用すると、この操作は、トークン キャッシュをクリアするだけで実現できます。

    ```C#
    private void SignOut()
    {
        // Clear session state from the token cache.
        authContext.TokenCache.Clear();

        ...
    }
    ```

## <a name="whats-next"></a>参照トピック
これで、ユーザーの認証、OAuth 2.0 を使用した Web API の安全な呼び出し、ユーザーについての基本情報の取得を行うことができる実用的な Windows ストア アプリが完成しました。

まだテナントにユーザーを設定していない場合は、ここで設定しましょう。
1. DirectorySearcher アプリを実行し、ユーザーの&1; 人としてサインインします。
2. UPN に基づいて、他のユーザーを検索します。
3. アプリを閉じて、再び実行します。 ユーザーのセッションがそのままに維持されていることに注意します。
4. 右クリックして下部のバーを表示することによりサインアウトし、別のユーザーとしてもう一度サインインします。

ADAL を使用することにより、一般的な ID 機能すべてを容易にアプリに組み込むことができます。 キャッシュ管理、OAuth プロトコル サポート、ユーザーに対するログイン UI の表示、有効期限切れとなったトークンの更新など、面倒な操作を容易に実装できます。 知っておく必要があるものは、API 呼び出し `authContext.AcquireToken*(…)` だけです。

リファレンスについては、[完全なサンプル](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip) (環境に応じた構成値を除く) をダウンロードしてください。

ここからは、さらに ID シナリオに進むことができます。 たとえば、[Azure AD を使用して .NET Web API を保護](active-directory-devquickstarts-webapi-dotnet.md)してみましょう。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

