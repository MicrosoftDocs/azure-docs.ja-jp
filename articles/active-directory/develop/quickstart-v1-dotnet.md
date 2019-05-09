---
title: .NET Desktop (WPF) アプリでユーザーのサインインと Microsoft Graph API の呼び出しを行う | Microsoft Docs
description: サインインするために Azure AD と統合され、OAuth 2.0 を使用して Azure AD によって保護されている API を呼び出す .NET Windows Desktop アプリケーションを構築する方法を説明します。
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b55f7e615f2c2edb604d5b9433db6cc48d9f36f
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223395"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>クイック スタート:.NET Desktop (WPF) アプリでユーザーのサインインと Microsoft Graph API の呼び出しを行う

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Azure Active Directory (Azure AD) には、保護されたリソースにアクセスする必要がある .NET ネイティブ クライアント向けに、Active Directory 認証ライブラリ (ADAL) が用意されています。 ADALは、アプリがアクセス トークンを簡単に取得できるようにします。 

このクイック スタートでは、以下の操作を行う .NET WPF To-Do List アプリケーションを構築する方法について説明します。

* OAuth 2.0 認証プロトコルを使用して Azure AD Graph API を呼び出すためのアクセス トークンを取得します。
* 指定されたエイリアスを持つユーザーをディレクトリで検索します。
* ユーザーのサインアウト処理を行います。

完全に動作するアプリケーションを構築するには、次の手順を行う必要があります。

1. Azure AD にアプリケーションを登録する
2. ADAL をインストールして構成する
3. ADAL を使用して、Azure AD からトークンを取得する

## <a name="prerequisites"></a>前提条件

最初に、以下の前提条件を完了します。

* [アプリのスケルトン](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip)をダウンロードするか、[完全なサンプル](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)をダウンロードします。
* ユーザーを作成してアプリケーションを登録するための Azure AD テナントがある。 テナントを所有していない場合は、「 [How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](quickstart-create-new-tenant.md)」を参照して取得してください。

## <a name="step-1-register-the-directorysearcher-application"></a>手順 1:DirectorySearcher アプリケーションを登録する

アプリがトークンを取得できるようにするには、アプリを Azure AD テナントに登録し、Azure AD Graph API にアクセスするためのアクセス許可を付与する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 上部のバーにある自分のアカウントを選択し、**[ディレクトリ]** の一覧から、アプリケーションを登録する Active Directory テナントを選択します。
3. 左側のナビゲーション バーで **[すべてのサービス]** を選択し、**[Azure Active Directory]** を選択します。
4. **[アプリの登録]** で、**[追加]** を選択します。
5. 画面の指示に従って、新しい**ネイティブ クライアント アプリケーション**を作成します。
    * アプリケーションの **[名前]** には、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
    * **[リダイレクト URI]** には、Azure AD がトークン応答を返すために使用するスキームと文字列の組み合わせを設定します。 `http://DirectorySearcher` など、アプリケーションに固有の値を入力してください。

6. 登録が完了すると、AAD により、アプリに一意のアプリケーション ID が割り当てられます。 この値は次のセクションで必要になるので、アプリケーション ページからコピーします。
7. **[設定]** ページで、**[必要なアクセス許可]** を選択し、**[追加]** を選択します。 API として **[Microsoft Graph]** を選択し、**[委任されたされたアクセス許可]** の下に **[ディレクトリ データの読み取り]** アクセス許可を追加します。 このアクセス許可の設定によって、アプリケーションが Graph API を使用してユーザーをクエリできるようになります。

## <a name="step-2-install-and-configure-adal"></a>手順 2:ADAL をインストールして構成する

アプリケーションを Azure AD に登録したので、ADAL をインストールし、ID 関連のコードを記述できます。 ADAL が Azure AD と通信するには、アプリの登録に関するいくつかの情報を ADAL に提供する必要があります。

1. まず、パッケージ マネージャー コンソールを使用して、ADAL を `DirectorySearcher` プロジェクトに追加します。

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1. `DirectorySearcher` プロジェクトで、`app.config` ファイルを開きます。
1. Azure portal で入力した値が反映されるように、`<appSettings>` セクションの要素の値を置き換えます。 これらの値は、コードで ADAL を使用する際に常に参照されます。
   * `ida:Tenant` は、Azure AD テナントのドメイン (contoso.onmicrosoft.com など) です。
   * `ida:ClientId` は、ポータルからコピーしたアプリケーションのクライアント ID である必要があります。
   * `ida:RedirectUri` は、ポータルに登録したリダイレクト URL です。

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>手順 3:ADAL を使用して、Azure AD からトークンを取得する

ADAL を使用することの基本的なメリットは、アプリがアクセス トークンを必要とする場合、アプリは `authContext.AcquireTokenAsync(...)` を呼び出すだけで、残りの処理は ADAL が実行してくれることです。

1. `DirectorySearcher` プロジェクトで、`MainWindow.xaml.cs` ファイルを開きます。
1. `MainWindow()` メソッドを見つけます。 
1. アプリの `AuthenticationContext` (ADAL のプライマリ クラス) を初期化します。 `AuthenticationContext` は、ADAL が Azure AD と通信し、トークンをキャッシュする方法を通知するために必要な調整項目を ADAL に渡す場所です。

    ```csharp
    public MainWindow()
    {
        InitializeComponent();

        authContext = new AuthenticationContext(authority, new FileCache());

        CheckForCachedToken();
    }
    ```

1. `Search(...)` メソッドを見つけます。これは、ユーザーがアプリの UI で **[検索]** ボタンを選択したときに呼び出されます。 このメソッドは、指定された検索用語で UPN が始まるユーザーをクエリするための、Azure AD Graph API に対する GET 要求を実行します。
1. Graph API をクエリするためには、要求の `Authorization` ヘッダーに access_token を含めます。ここで ADAL が登場します。

    ```csharp
    private async void Search(object sender, RoutedEventArgs e)
    {
        // Validate the Input String
        if (string.IsNullOrEmpty(SearchText.Text))
        {
            MessageBox.Show("Please enter a value for the To Do item name");
            return;
        }

        // Get an Access Token for the Graph API
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
            UserNameLabel.Content = result.UserInfo.DisplayableId;
            SignOutButton.Visibility = Visibility.Visible;
        }
        catch (AdalException ex)
        {
            // An unexpected error occurred, or user canceled the sign in.
            if (ex.ErrorCode != "access_denied")
                MessageBox.Show(ex.Message);

            return;
        }

        ...
    }
    ```

    アプリが `AcquireTokenAsync(...)`を呼び出すことによってトークンを要求すると、ADAL はユーザーに資格情報を要求することなく、トークンを返そうとします。
    * ADAL は、トークンを取得するにはユーザーのサインインが必要であると判断した場合、ログイン ダイアログを表示し、ユーザーの資格情報を収集し、認証が成功するとトークンを返します。 
    * また、何らかの理由によりトークンを返せない場合、 `AdalException`をスローします。

1. `AuthenticationResult` オブジェクトには、アプリが必要とする可能性のある情報を収集するために使用される `UserInfo` オブジェクトが含まれていることに注意してください。 DirectorySearcher で、`UserInfo` は、ユーザーの ID を使用してアプリの UI をカスタマイズするために使用されます。
1. ユーザーが **[サインアウト]** ボタンを選択した場合は、`AcquireTokenAsync(...)` への次回の呼び出しで、ユーザーに必ずサインインを求めるようにします。 これは、トークン キャッシュをクリアすることによって、ADAL で簡単に実行できます。

    ```csharp
    private void SignOut(object sender = null, RoutedEventArgs args = null)
    {
        // Clear the token cache
        authContext.TokenCache.Clear();

        ...
    }
    ```

    ユーザーが **[サインアウト]** ボタンをクリックしていない場合は、ユーザーによる次回の DirectorySearcher の実行に備えて、ユーザーのセッションを維持する必要があります。 アプリが起動したら、既存のトークン用の ADAL のトークン キャッシュをチェックし、必要に応じて UI を更新できます。

1. `CheckForCachedToken()` メソッドで `AcquireTokenAsync(...)` に対して別の呼び出しを行います。その際に `PromptBehavior.Never` パラメーターを渡します。 `PromptBehavior.Never` は ADAL に対して、トークンを返せない場合、ユーザーにサインイン用の認証情報の入力を求めるのではなく、代わりに ADAL は例外をスローする必要があることを通知します。

    ```csharp
    public async void CheckForCachedToken() 
    {
        // As the application starts, try to get an access token without prompting the user. If one exists, show the user as signed in.
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "user_interaction_required")
            {
                // An unexpected error occurred.
                MessageBox.Show(ex.Message);
            }

            // If user interaction is required, proceed to main page without signing the user in.
            return;
        }

        // A valid token is in the cache
        SignOutButton.Visibility = Visibility.Visible;
        UserNameLabel.Content = result.UserInfo.DisplayableId;
    }
    ```

お疲れさまでした。 これで、作業中の .NET WPF アプリケーションで、ユーザーの認証、OAuth 2.0 を使用した Web API の安全な呼び出し、ユーザーに関する基本情報の取得が可能になりました。 テナントに一連のユーザーを設定します (設定していない場合)。 DirectorySearcher アプリを実行し、それらのユーザーの一人としてサインインします。 UPN に基づいて、他のユーザーを検索します。 アプリを閉じて、再び実行します。 ユーザーのセッションがそのまま維持されていることに注意します。 サインアウトし、別のユーザーとしてサインインします。

ADAL を使用することにより、これらの共通 ID 機能を容易にアプリケーションに組み込むことができます。 キャッシュ管理、OAuth プロトコルのサポート、ログイン UI を使用してのユーザーの提示、有効期限切れとなったトークンの更新などの煩わしい操作を容易に実装できます。 習得する必要があるのは、単一の API 呼び出し、 `authContext.AcquireTokenAsync(...)`のみです。

リファレンスについては、[GitHub](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip) の完全なサンプル (環境に応じた構成値を除く) をご覧ください。

## <a name="next-steps"></a>次の手順

OAuth 2.0 ベアラー アクセス トークンを使用して Web API を保護する方法について説明します。
> [!div class="nextstepaction"]
> [Protect a Web API using Bearer tokens from Azure AD](quickstart-v1-dotnet-webapi.md) (Azure AD からのベアラー トークンを使用することによる Web API の保護)
