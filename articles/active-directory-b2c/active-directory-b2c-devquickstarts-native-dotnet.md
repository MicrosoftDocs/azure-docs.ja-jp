---
title: Azure Active Directory B2C | Microsoft Docs
description: "Azure Active Directory B2C を使用してサインイン、サインアップ、プロファイル管理を行う Windows デスクトップ アプリケーションを構築する方法。"
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 9da14362-8216-4485-960e-af17cd5ba3bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: a977cb509fb64d7c986e2e0f7e2b5e4e3e45dec0
ms.openlocfilehash: 9a78daac2269c9d44558e92b80c869603e014fb6


---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C: Windows デスクトップ アプリを作成する
Azure Active Directory (Azure AD) B2C を使用すると、強力なセルフサービス方式の ID 管理機能を、わずかな手順でデスクトップ アプリに追加できます。 この記事では、ユーザーのサインアップ、サインイン、プロファイル管理などの処理を含む .NET Windows Presentation Foundation (WPF) の "To-Do List" アプリの作成方法について説明します。 このアプリには、ユーザー名または電子メールを使用したサインアップとサインインのサポートが含まれます。 また、Facebook や Google などのソーシャル アカウントを使用したサインアップとサインインのサポートも含まれます。

## <a name="get-an-azure-ad-b2c-directory"></a>Azure AD B2C ディレクトリの取得
Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。  ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。 まだディレクトリを作成していない場合は、先に進む前に [B2C ディレクトリを作成](active-directory-b2c-get-started.md) してください。

## <a name="create-an-application"></a>アプリケーションの作成
次に、B2C ディレクトリにアプリを作成する必要があります。 これにより、アプリと安全に通信するために必要な情報を Azure AD に提供します。 アプリを作成するには、 [こちらの手順](active-directory-b2c-app-registration.md)に従ってください。  次を行ってください。

* アプリケーションに **ネイティブ クライアント** を含めます。
* **[リダイレクト URI]** `urn:ietf:wg:oauth:2.0:oob` をコピーします。 これはこのサンプル コードで使用する既定の URL です。
* アプリに割り当てられた **アプリケーション ID** をコピーしておきます。 この情報は後で必要になります。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>ポリシーの作成
Azure AD B2C では、すべてのユーザー エクスペリエンスが [ポリシー](active-directory-b2c-reference-policies.md)によって定義されます。 このコード サンプルには、3 つの ID エクスペリエンス (サインアップ、サインイン、プロファイル編集) が含まれています。 [ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)で説明されているように、種類ごとにポリシーを作成する必要があります。 3 つのポリシーを作成するときは、以下の点に注意してください。

* ID プロバイダーのブレードで、**[User ID sign-up (ユーザー ID サインアップ)]** または **[Email sign-up (電子メール サインアップ)]** を選択します。
* サインアップ ポリシーで、 **[表示名]** と他のサインアップ属性を選択します。
* すべてのポリシーで、アプリケーション要求として **[表示名]** 要求と **[オブジェクト ID]** 要求を選択します。 その他のクレームも選択できます。
* ポリシーの作成後、各ポリシーの **名前** をコピーしておきます。 名前には、 `b2c_1_`というプレフィックスが付加されています。  これらのポリシー名は後で必要になります。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

3 つのポリシーの作成が正常に完了したら、いつでもアプリをビルドできます。

## <a name="download-the-code"></a>コードのダウンロード
このチュートリアルのコードは、 [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet)で管理されています。 手順に従ってサンプルをビルドする場合は、 [スケルトン プロジェクトを .zip ファイルとしてダウンロード](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip)できます。 スケルトンを複製することもできます。

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

また、完成済みのアプリも、[.zip ファイルとして入手する](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)か、同じリポジトリの `complete` ブランチで入手できます。

サンプル コードをダウンロードした後、Visual Studio の .sln ファイルを開いて作業を開始します。 `TaskClient` プロジェクトは、ユーザーと対話する WPF デスクトップ アプリケーションです。 このチュートリアルでは、Azure でホストされ、各ユーザーの To-Do List を格納しているバックエンド タスク Web API を、TaskClient プロジェクトで呼び出します。  この Web API については既に実行中のものを用意しているため、作成する必要はありません。

Azure AD B2C を使用して、Web API で要求を安全に認証する方法については、 [Web API の概要についての記事](active-directory-b2c-devquickstarts-api-dotnet.md)を参照してください。

## <a name="execute-policies"></a>ポリシーの実行
アプリは、認証メッセージを送信することで、Azure AD B2C と通信します。このとき、HTTP 要求の一環として実行するポリシーを指定します。 .NET デスクトップ アプリケーションでは、プレビュー版の Microsoft Authentication Library (MSAL) を使用して OAuth 2.0 認証メッセージを送信し、ポリシーを実行して、Web API を呼び出すトークンを取得できます。

### <a name="install-msal"></a>MSAL をインストールする
Visual Studio Package Manager Console を使用して、MSAL を `TaskClient` プロジェクトに追加します。

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>B2C の詳細情報を入力する
`Globals.cs` ファイルを開き、各プロパティ値を実際の値に置き換えます。 このクラスは、共通に使用される値を参照するために `TaskClient` 全体で使用されます。

```C#
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="create-the-publicclientapplication"></a>PublicClientApplication を作成する
MSAL のプライマリ クラスは `PublicClientApplication`です。 このクラスは、Azure AD B2C システム内のアプリケーションを表します。 アプリの初期化時に、`MainWindow.xaml.cs` 内の `PublicClientApplication` のインスタンスを作成します。 これは、ウィンドウ全体で使用できます。

```C#
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>サインアップ フローを開始する
ユーザーがサインアップを選択した場合、作成したサインアップ ポリシーを使用するサインアップ フローを開始します。 MSAL を使用するので、 `pca.AcquireTokenAsync(...)`を呼び出すだけです。 `AcquireTokenAsync(...)` に渡すパラメーターにより、受け取るトークン、認証要求で使用されるポリシーなどが決まります。

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
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

### <a name="initiate-a-sign-in-flow"></a>サインイン フローを開始する
サインアップ フローの開始と同じ方法で、サインイン フローを開始できます。 ユーザーがサインインしたら、MSAL に対して同じ呼び出しを行いますが、ここではサインイン ポリシーを使用します。

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>プロファイル編集フローを開始する
ここでも、同じ方法でプロファイル編集ポリシーを実行できます。

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

いずれの場合も、MSAL は `AuthenticationResult` でトークンを返すか、または例外をスローします。 MSAL からトークンを取得するたびに、 `AuthenticationResult.User` オブジェクトを使用して、アプリ内のユーザー データ (UI など) を更新できます。 ADAL は、アプリケーションの他の部分で使用するために、トークンのキャッシュも行います。

### <a name="check-for-tokens-on-app-start"></a>アプリの起動時にトークンを確認する
MSAL は、ユーザーのサインイン状態の追跡にも使用することができます。  このアプリでは、ユーザーがアプリを閉じて再度開いたときにも、ユーザーのサインイン状態をそのまま維持する必要があります。  `OnInitialized` オーバーライド内に戻り、キャッシュされたトークンを確認するために MSAL の `AcquireTokenSilent` メソッドを使用します。

```C#
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
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
```

## <a name="call-the-task-api"></a>タスク API を呼び出す
ここまでは、ポリシーを実行してトークンを取得するために MSAL を使用しました。  取得したトークンのいずれかを使用してタスク API を呼び出す場合は、ここでも MSAL の `AcquireTokenSilent` メソッドを使用して、キャッシュされたトークンを確認します。

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
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
    ...
```

`AcquireTokenSilentAsync(...)` の呼び出しが成功し、トークンがキャッシュ内に見つかった場合、トークンを HTTP 要求の `Authorization` ヘッダーに追加します。 タスク Web API では、このヘッダーを使用してユーザーの To-Do List の読み取り要求を認証します。

```C#
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>ユーザーのサインアウト
ユーザーが **サインアウト**を選択した場合は、MSAL を使用して、アプリでのユーザーのセッションを終了することができます。  MSAL を使用してこの処理を実現するには、トークン キャッシュからすべてのトークンをクリアします。

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>サンプル アプリを実行する
最後に、サンプルをビルドして実行します。  電子メール アドレスまたはユーザー名を使用して、アプリにサインアップします。 サインアウトし、同じユーザーとしてもう一度サインインします。 そのユーザーのプロファイルを編集します。 サインアウトし、別のユーザーとしてサインアップします。

## <a name="add-social-idps"></a>ソーシャル IDP を追加する
現時点では、このアプリは **ローカル アカウント**を使用したユーザーのサインアップとサインインのみをサポートしています。 これらは、ユーザー名とパスワードを使用する B2C ディレクトリに格納されているアカウントです。 Azure AD B2C を使用すると、コードを変更せずに、その他の ID プロバイダー (IDP) のサポートを追加することができます。

ソーシャル IDP をアプリに追加するには、まず、以下の記事に記載されている詳細な手順に従います。 サポートする IDP ごとに、そのシステムでアプリケーションを登録してクライアント ID を取得する必要があります。

* [Facebook を IDP として設定する](active-directory-b2c-setup-fb-app.md)
* [Google を IDP として設定する](active-directory-b2c-setup-goog-app.md)
* [Amazon を IDP として設定する](active-directory-b2c-setup-amzn-app.md)
* [LinkedIn を IDP として設定する](active-directory-b2c-setup-li-app.md)

ID プロバイダーを B2C ディレクトリに追加した後、3 つのポリシーをそれぞれ編集し、 [ポリシーに関するリファレンス記事](active-directory-b2c-reference-policies.md)で説明されているように、新しい IDP を加える必要があります。 ポリシーを保存した後、再度アプリを実行します。 各 ID エクスペリエンスに、サインインおよびサインアップの選択肢として新しい IDP が追加されていることがわかります。

いろいろなポリシーを試して、サンプル アプリへの影響を確認できます。 ID を追加または削除したり、アプリケーションの要求を操作したり、サインアップ属性を変更してみます。 ポリシー、認証要求、および MSAL が互いにどのように結び付いているか理解できるまで、いろいろ試してみてください。

参照用に、完成したサンプルが [ここに .zip ファイルとして提供されています](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)。 GitHub から複製することもできます。

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```



<!--HONumber=Dec16_HO4-->


