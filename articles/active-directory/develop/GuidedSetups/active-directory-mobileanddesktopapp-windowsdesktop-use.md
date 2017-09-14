---
title: "Azure AD v2 Windows デスクトップの概要 - 使用 |Microsoft Docs"
description: "Windows デスクトップ .NET (XAML) アプリケーションで、アクセス トークンを必要とする API を Azure Active Directory v2 エンドポイントから呼び出す方法"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 826ba0a00b26993d4f37f0a8ce587d7bb77e7eb4
ms.contentlocale: ja-jp


---

## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Microsoft Authentication Library (MSAL) を使用して Microsoft Graph API のトークンを取得する

このセクションでは、MSAL を使用して Microsoft Graph API のトークンを取得する方法を示します。

1.  `MainWindow.xaml.cs` で、次の手順に従ってクラスに MSAL の参照を追加します。

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
<code>MainWindow</code> クラス コードを以下と置き換えます。
</li>
</ol>

```csharp
public partial class MainWindow : Window
{
    //Set the API Endpoint to Graph 'me' endpoint
    string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

    //Set the scope for API call to user.read
    string[] _scopes = new string[] { "user.read" };


    public MainWindow()
    {
        InitializeComponent();
    }

    /// <summary>
    /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
    /// </summary>
    private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
    {
        AuthenticationResult authResult = null;

        try
        {
            authResult = await App.PublicClientApp.AcquireTokenSilentAsync(_scopes, App.PublicClientApp.Users.FirstOrDefault());
        }
        catch (MsalUiRequiredException ex)
        {
            // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
            System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

            try
            {
                authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
            }
            catch (MsalException msalex)
            {
                ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
            }
        }
        catch (Exception ex)
        {
            ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
            return;
        }

        if (authResult != null)
        {
            ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>詳細情報
#### <a name="getting-a-user-token-interactive"></a>ユーザー トークンを対話形式で取得する
`AcquireTokenAsync` メソッドを呼び出すと、ユーザーにサインインを求めるウィンドウが表示されます。 アプリケーションは通常、ユーザーが保護されたリソースに初めてアクセスするとき、または自動でのトークンの取得に失敗したとき (ユーザーのパスワードが期限切れになっている場合など) に、対話形式でユーザーにサインインを求めます。

#### <a name="getting-a-user-token-silently"></a>ユーザー トークンを自動で取得する
`AcquireTokenSilentAsync` は、ユーザーの操作なしでトークンの取得や更新を行います。 最初に `AcquireTokenAsync` が実行されたあと、保護されたリソースへのアクセスに使用するトークンを取得するには、通常、`AcquireTokenSilentAsync` メソッドが使用されます。トークンの要求や更新などの後続の呼び出しが自動で行われるからです。
`AcquireTokenSilentAsync` は最終的に失敗することになります。例えば、ユーザーがサインアウトした場合や、パスワードが別のデバイスで変更された場合などです。 ユーザーの操作によって解決できる問題が MSAL によって検出された場合、MSAL は `MsalUiRequiredException` を発行します。 アプリケーションでは、この例外を 2 つの方法で処理できます。

1.  すぐに `AcquireTokenAsync` を呼び出し、ユーザーにサインインを求める。 オンライン アプリケーション (ユーザーが使用できるオフライン コンテンツが含まれていないアプリケーション) の場合は、通常、この方法で処理します。 このガイドのセットアップで生成されたサンプルではこの方法を使用します。サンプルを最初に実行するときは、まだ誰もそのアプリケーションを使用していない状態であるため、サインインを求めるウィンドウが表示されます。`PublicClientApp.Users.FirstOrDefault()` は null 値を含み、`MsalUiRequiredException` 例外がスローされます。 サンプルのコードでは、ユーザーのサインインを求める `AcquireTokenAsync` を呼び出すことで、この例外を処理します。

2.  ユーザーに対してアプリケーションで視覚的に対話形式でのサインインを求めることで、ユーザーが適切なタイミングでサインインできるようにし、アプリケーションがあとで `AcquireTokenSilentAsync` を再試行できるようにします。 アプリケーションにユーザーが使用できるオフライン コンテンツが含まれている場合など、アプリケーションの他の機能を中断せずに使用できる場合は、通常、この方法で処理します。 この方法では、ユーザーは保護されたリソースにサインインしたり、古い情報を更新したりするタイミングを決めることができます。また、アプリケーションで、ネットワークが一時的に使用できなくなってから回復した場合に `AcquireTokenSilentAsync` を再試行できます。
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>取得したトークンを使用して Microsoft Graph API を呼び出す

1. `MainWindow.xaml.cs` に下記の新しいメソッドを追加します。 このメソッドは、Graph API に対する Authorize ヘッダーを使用した `GET` 要求の実行に使用されます。

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```
<!--start-collapse-->
### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>保護された API に対する REST 呼び出しの実行についての詳細

このサンプル アプリケーションでは、`GetHttpContentWithToken` メソッドを使用して、トークンが必要な保護されたリソースに対して HTTP `GET` 要求を実行し、呼び出し元にその内容を返します。 このメソッドは、取得したトークンを *HTTP Authorization ヘッダー*に追加します。 このサンプルの場合、リソースは Microsoft Graph API *me* エンドポイントで、ユーザーのプロファイル情報を表示します。
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>ユーザーをサインアウトするメソッドの追加

1. 次のメソッドを `MainWindow.xaml.cs` に追加し、ユーザーをサインアウトさせます。

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    if (App.PublicClientApp.Users.Any())
    {
        try
        {
            App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>サインアウトに関する詳細情報

`SignOutButton_Click` は、MSAL ユーザー キャッシュからユーザーを削除します。これによって MSAL は現在のユーザーを忘れることになり、後続の要求が対話形式で行われた場合にのみトークンを取得できるようになります。
このサンプルのアプリケーションでは単一のユーザーのみがサポートされていますが、MSAL では複数のアカウントを使用して同時にサインインするケース (たとえば、電子メール アプリケーションなどで 1 人のユーザーが複数のアカウントを持つケース) がサポートされています。
<!--end-collapse-->

## <a name="display-basic-token-information"></a>基本的なトークン情報の表示

1. トークンについての基本的な情報を表示するには、次のメソッドを `MainWindow.xaml.cs` に追加します。

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
        TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```
<!--start-collapse-->
### <a name="more-information"></a>詳細情報

*OpenID 接続*を使用して取得したトークンにも、ユーザー関連情報の少量のサブセットが含まれています。 `DisplayBasicTokenInfo` は、トークンに含まれている基本的な情報を表示します。たとえば、トークンの有効期限やアクセス トークンそのものを表す文字列に加えて、ユーザーの表示名や ID などです。 この情報は、参照用に表示されます。 *[Call Microsoft Graph API]* (Microsoft Graph API の呼び出し) ボタンを複数回押すと、後の要求で同じトークンが再利用されてことが確認できます。 また、MSAL がトークンの更新時期だと判断したときに、有効期限が延長されることも確認できます。
<!--end-collapse-->


