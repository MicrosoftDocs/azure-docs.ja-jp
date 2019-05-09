---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 3f72f6a5097221c904faff633b5a4ee5a6e023c1
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59532783"
---
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>MSAL を使用して Microsoft Graph API のトークンを取得する

このセクションでは、MSAL を使用して Microsoft Graph API のトークンを取得します。

1. *MainWindow.xaml.cs* ファイルで、クラスに MSAL の参照を追加します。

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. `MainWindow` クラス コードを次のコードに置き換えます。

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
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
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    ```

<!--start-collapse-->
### <a name="more-information"></a>詳細情報

#### <a name="get-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する

`AcquireTokenInteractive` メソッドを呼び出すと、ユーザーにサインインを求めるウィンドウが表示されます。 アプリケーションは通常、ユーザーが保護されたリソースに初めてアクセスするときに、対話形式でユーザーにサインインを求めます。 また、自動でのトークンの取得に失敗した場合 (ユーザーのパスワードが期限切れになっている場合など) にも、ユーザーはサインインする必要があります。

#### <a name="get-a-user-token-silently"></a>ユーザー トークンを自動で取得する

`AcquireTokenSilent` メソッドは、ユーザーの操作なしでトークンの取得と更新を処理します。 `AcquireTokenInteractive` が初めて実行された後、以降の呼び出しでは、保護されたリソースへのアクセスに使用するトークンを取得する際に、通常は `AcquireTokenSilent` メソッドを使用します。トークンを要求または更新する呼び出しが自動で行われるからです。

最終的に、`AcquireTokenSilent` メソッドは失敗します。 この失敗は、ユーザーがサインアウトしたか、別のデバイスでパスワードを変更したことが原因と考えられます。 ユーザーの操作によって解決できる問題が MSAL によって検出された場合、MSAL は `MsalUiRequiredException` 例外を発行します。 アプリケーションでは、この例外を 2 つの方法で処理できます。

* すぐに `AcquireTokenInteractive` を呼び出します。 この呼び出しにより、ユーザーにサインインを求めます。 ユーザーが使用できるオフライン コンテンツがないオンライン アプリケーションでは、通常、このパターンを使用します。 このガイド付きセットアップで生成されるサンプルでは、このパターンを使用します。サンプルの初回実行時に、実際の動作を確認できます。 

* アプリケーションはユーザーによって使用されたことがないため、`PublicClientApp.Users.FirstOrDefault()` には null 値が含まれ、`MsalUiRequiredException` 例外がスローされます。 

* サンプルのコードでは、`AcquireTokenInteractive` を呼び出してユーザーにサインインを求めることにより、この例外を処理します。

* 対話形式でのサインインが必要であることをユーザーに視覚的に示すことで、ユーザーが適切なタイミングでサインインできるようにします。 または、アプリケーションが後で `AcquireTokenSilent` を再試行します。 アプリケーションでオフライン コンテンツを使用できる場合など、ユーザーが中断なしでアプリケーションの他の機能を使用できる場合に、このパターンがよく使用されます。 この場合、保護されたリソースにアクセスしたり、古くなった情報を更新したりするために、サインインするタイミングをユーザーが決定できます。 また、一時的に使用できなくなっていたネットワークが回復したときに、アプリケーションが `AcquireTokenSilent` の再試行を決定することもできます。
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>取得したトークンを使用して Microsoft Graph API を呼び出す

次の新しいメソッドを `MainWindow.xaml.cs` に追加します。 このメソッドは、Authorize ヘッダーを使用した Graph API に対する `GET` 要求の実行に使用されます。

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
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>保護された API に対する REST 呼び出しの実行についての詳細

このサンプル アプリケーションでは、`GetHttpContentWithToken` メソッドを使用して、トークンが必要な保護されたリソースに対して HTTP `GET` 要求を実行し、呼び出し元にその内容を返します。 このメソッドは、取得したトークンを HTTP Authorization ヘッダーに追加します。 このサンプルで使用するリソースは、ユーザーのプロファイル情報を表示する Microsoft Graph API *me* エンドポイントです。
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>ユーザーをサインアウトさせるメソッドを追加する

ユーザーをサインアウトさせるには、次のメソッドを `MainWindow.xaml.cs` ファイルに追加します。

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync(); 

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
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
### <a name="more-information-about-user-sign-out"></a>ユーザーのサインアウトに関する詳細情報

`SignOutButton_Click` メソッドは、MSAL ユーザー キャッシュからユーザーを削除します。これは実質的に MSAL に現在のユーザーを忘れさせることになり、以降の要求が対話形式で行われる場合のみトークンの取得が成功します。

このサンプルのアプリケーションは単一ユーザーに対応していますが、MSAL は複数のアカウントを使用して同時にサインインするケースに対応しています。 たとえば、メール アプリケーションで 1 人のユーザーが複数のアカウントを持っているケースです。
<!--end-collapse-->

## <a name="display-basic-token-information"></a>基本的なトークン情報を表示する

トークンについての基本的な情報を表示するには、次のメソッドを *MainWindow.xaml.cs* ファイルに追加します。

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>詳細情報

Microsoft Graph API の呼び出しに使用するアクセス トークンに加えて、MSAL はユーザーのサインイン後に ID トークンも取得します。 このトークンには、ユーザー関連情報の少量のサブセットが含まれています。 `DisplayBasicTokenInfo` メソッドは、このトークンに含まれている基本的な情報を表示します。 たとえば、トークンの有効期限やアクセス トークンそのものを表す文字列に加えて、ユーザーの表示名や ID です。 *[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)* ボタンを複数回押すと、後の要求で同じトークンが再利用されてことが確認できます。 また、MSAL がトークンの更新時期だと判断したときに、有効期限が延長されることも確認できます。
<!--end-collapse-->
