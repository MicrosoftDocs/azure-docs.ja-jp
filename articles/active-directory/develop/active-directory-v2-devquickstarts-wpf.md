---
title: "Azure Active Directory v2.0 .NET ネイティブ アプリ | Microsoft Docs"
description: "サインインに個人の Microsoft アカウントと職場/学校アカウントの両方を使用する .NET ネイティブ アプリを構築する方法を説明します。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 46d81e09-bad0-44ce-9026-881805976e72
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/30/2016
ms.author: dastrock; vittorib
translationtype: Human Translation
ms.sourcegitcommit: 3e0bb32a6c60011d71606c896cc506f430bc3c27
ms.openlocfilehash: 52a4c326c7618b9a31df539c8b0eb0ced44ce609


---
# <a name="add-sign-in-to-a-windows-desktop-app"></a>サインインを Windows デスクトップ アプリに追加する
v2.0 エンドポイントを使用すると、Microsoft の個人および職場/学校アカウントの両方に対応したデスクトップ アプリに認証をすばやく追加できます。  また、アプリとバックエンド Web API や [Microsoft Graph](https://graph.microsoft.io) と一部の [Office 365 Unified API](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) との安全な通信を可能にします。

> [!NOTE]
> Azure Active Directory (AD) のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。  v2.0 エンドポイントを使用する必要があるかどうかを判断するには、 [v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。
> 
> 

[デバイスで実行する .NET ネイティブ アプリ](active-directory-v2-flows.md#mobile-and-native-apps)に対しては、Azure AD により、Microsoft ID 認証ライブラリ (MSAL) が提供されます。  MSAL の唯一の目的は、アプリが Web サービスを呼び出すためのトークンを容易に取得できるようにすることです。  どれほど簡単かを示すために、ここで、次のような、.NET WPF To-Do List アプリを構築します。

* ユーザーのサインイン処理を行い、[OAuth 2.0 認証プロトコル](active-directory-v2-protocols.md)を使用してアクセス トークンを取得します。
* バックエンド To-Do List Web サービスを安全に呼び出します。これは、OAuth 2.0 でも保護されます。
* ユーザーをサインアウトさせます。

## <a name="download-sample-code"></a>サンプル コードのダウンロード
このチュートリアルのコードは、 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet)で管理されています。  追加の参考資料として、 [アプリのスケルトン (.zip) をダウンロード](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) したり、スケルトンを複製したりすることができます:

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

完成したアプリは、このチュートリアルの終わりにも示しています。

## <a name="register-an-app"></a>アプリを登録します
[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) で新しいアプリを作成するか、この[詳細な手順](active-directory-v2-app-registration.md)に従います。  次のことを確認します。

* アプリに割り当てられた**アプリケーション ID** をメモしておきます。これは後で必要になります。
* アプリ用の **モバイル** プラットフォームを追加します。

## <a name="install--configure-msal"></a>MSAL のインストールと構成
Microsoft へのアプリの登録が完了したら、MSAL をインストールし、自分の ID 関連コードを記述できます。  MSAL が v2.0 エンドポイントと通信できるようにするには、アプリの登録に関するいくつかの情報を MSAL に提供する必要があります。

* 最初に、パッケージ マネージャー コンソールを使用して、MSAL を TodoListClient プロジェクトに追加します。

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

* TodoListClient プロジェクトで `app.config`を開きます。  アプリ登録ポータルで入力した値が反映されるように、 `<appSettings>` セクションの要素の値を置き換えます。  これらの値は、コードで MSAL を使用する際に常に参照されます。
  
  * `ida:ClientId` は、ポータルからコピーしたアプリの **アプリケーション ID** です。
* TodoList-Service プロジェクトで、プロジェクトのルートにある `web.config` を開きます。  
  
  * `ida:Audience` 値をポータルから取得した **アプリケーション ID** に置き換えます。

## <a name="use-msal-to-get-tokens"></a>MSAL を使用してトークンを取得する
MSAL を使用することの基本的なメリットは、アプリがアクセス トークンを必要とする場合、必要な操作は `app.AcquireToken(...)`を呼び出すことだけで、残りの処理は MSAL が実行することです。  

* `TodoListClient` プロジェクトで、`MainWindow.xaml.cs` を開き、`OnInitialized(...)` メソッドを見つけます。  最初の手順は、アプリの `PublicClientApplication` (ネイティブ アプリケーションを表す MSAL の主要なクラス) を初期化することです。  ここでは、MSAL が Azure AD と通信し、トークンをキャッシュする方法を通知するために必要な調整項目を MSAL に渡します。

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

* アプリの起動時に、ユーザーが既にアプリにサインインしているかどうかを確認する必要があります。  ただし、まだサインイン UI は呼び出しません。これを行うには、ユーザーに [サインイン] をクリックさせます。  また、`OnInitialized(...)` メソッドで次のコードを実行します。

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.

    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

* ユーザーがサインインしておらず、[サインイン] ボタンをクリックした場合、ログイン UI を呼び出し、ユーザーに資格情報を入力させます。  次のように、[サインイン] ボタン ハンドラーを実装します:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

* ユーザーが正常にサインインすると、MSAL によりトークンが自動的に受信してキャッシュされ、 `GetTodoList()` メソッドの呼び出しを実行できます。  ユーザーのタスクを取得するために必要な操作は、 `GetTodoList()` メソッドの実装だけです。

```C#
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>実行
ご利用ありがとうございます。 ユーザーを認証し、OAuth 2.0 を使用して安全に Web API を呼び出すことができる、.NET WPF アプリが動作するようになりました。  両方のプロジェクトを実行し、個人用の Microsoft アカウントか職場または学校アカウントでサインインしてください。  ユーザーの To Do リストにタスクを追加します。  いったんサインアウトしてから、別のユーザーとして再度サインインし、ユーザーの To Do リストを表示します。  アプリを閉じて、再び実行します。  ユーザーのセッションに影響がないことを確認してください。これは、アプリがトークンをローカル ファイルにキャッシュしているためです。

MSAL では、個人用アカウントと職場アカウントの両方を使用して、一般的な ID 機能をアプリに簡単に組み込むことができます。  キャッシュ管理、OAuth プロトコル サポート、ログイン UI を使用してのユーザーの提示、有効期限切れとなったトークンの更新など、面倒な操作を容易に実装できます。  習得する必要があるのは、単一の API 呼び出し、 `app.AcquireTokenAsync(...)`のみです。

参照用に、完成したサンプル (構成値を除く) が[ここに .zip として提供されています](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)。または、GitHub から複製することもできます。

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>次のステップ
これ以降は、さらに高度なトピックに進むことができます。  次のチュートリアルを試してみてください。

* [v2.0 エンドポイントでの TodoListService Web API の保護](active-directory-v2-devquickstarts-dotnet-api.md)

その他のリソースについては、以下を参照してください。  

* [v2.0 開発者向けガイド >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow "msal" タグ >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Microsoft 製品のセキュリティ更新プログラムの取得
セキュリティの問題が発生したときに通知を受け取ることをお勧めします。そのためには、[このページ](https://technet.microsoft.com/security/dd252948)にアクセスし、セキュリティ アドバイザリ通知を受信登録してください。




<!--HONumber=Nov16_HO3-->


