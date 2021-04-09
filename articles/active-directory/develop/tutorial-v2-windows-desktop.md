---
title: チュートリアル:認証に Microsoft ID プラットフォームを使用する Windows Presentation Foundation (WPF) アプリを作成する | Azure
titleSuffix: Microsoft identity platform
description: このチュートリアルでは、ユーザーのサインインに Microsoft ID プラットフォームを使用し、そのユーザーに代わって Microsoft Graph API を呼び出すためのアクセス トークンを取得する WPF アプリケーションをビルドします。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 60a29efc4d2daa9d1bc90f00e71094da382a83b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686888"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>チュートリアル:Windows デスクトップ アプリから Microsoft Graph API を呼び出す

このチュートリアルでは、ユーザーのサインインを処理して Microsoft Graph API を呼び出すためのアクセス トークンを取得するネイティブ Windows デスクトップ .NET (XAML) アプリを作成します。 

このガイドを完了すると、アプリケーションで個人アカウント (outlook.com、live.com など) を使用する保護された API を呼び出すことができるようになります。 このアプリケーションでは、Azure Active Directory を使用する会社または組織の職場および学校のアカウントも使用します。

このチュートリアルの内容:

> [!div class="checklist"]
> * Visual Studio で *Windows Presentation Foundation (WPF)* プロジェクトを作成する
> * .NET 用 Microsoft 認証ライブラリ (MSAL) をインストールする
> * Azure portal でアプリケーションを登録する
> * ユーザーのサインインとサインアウトをサポートするコードを追加する
> * Microsoft Graph API を呼び出すコードを追加する
> * アプリケーションをテストする

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>このガイドで生成されたサンプル アプリの動作

![このチュートリアルで生成されたサンプル アプリの動作の紹介](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

このガイドで作成するサンプル アプリケーションにより、Windows デスクトップ アプリケーションにおいて、Microsoft ID プラットフォーム エンドポイントからのトークンを受け付ける Microsoft Graph API または Web API に対してクエリを実行できるようになります。 このシナリオでは、Authorization ヘッダーを使用して HTTP 要求にトークンを追加します。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) で処理されます。

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>保護された Web API にアクセスするためのトークン取得処理

ユーザーが認証されると、サンプル アプリケーションは、Microsoft Graph API または Microsoft ID プラットフォームによって保護されている Web API でのクエリに使用できるトークンを受け取ります。

Microsoft Graph などの API では、特定のリソースへのアクセスを許可するためにトークンが必要になります。 たとえば、トークンは、ユーザーのプロファイルの読み取り、ユーザーの予定表へのアクセス、メールの送信などに必要です。 アプリケーションでは、MSAL を使用してアクセス トークンを要求し、API スコープを指定することによってこれらのリソースにアクセスできます。 このアクセス トークンは、保護されたリソースに対するすべての呼び出しで HTTP Authorization ヘッダーに追加されます。

アクセス トークンのキャッシュと更新は MSAL が管理するため、アプリケーションが管理する必要はありません。

## <a name="nuget-packages"></a>NuGet パッケージ

このガイドでは、次の NuGet パッケージを使用します。

|ライブラリ|説明|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL.NET)|

## <a name="set-up-your-project"></a>プロジェクトの設定

このセクションでは、Windows デスクトップ .NET アプリケーション (XAML) に "*Microsoft でサインイン*" を統合して、トークンを必要とする Web API でクエリを実行できるようにする方法を示すために、新しいプロジェクトを作成します。

このガイドで作成したアプリケーションには、グラフを呼び出すボタン、結果を画面に表示する領域、およびサインアウト ボタンが表示されます。

> [!NOTE]
> 代わりにこのサンプルの Visual Studio プロジェクトをダウンロードすることもできます。 [プロジェクトをダウンロード](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)したら、[構成手順](#register-your-application)に進んでサンプル コードを構成してから実行してください。
>

アプリケーションを作成するには、次の手順を実行します。

1. Visual Studio で、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択します。
2. **[テンプレート]** で **[Visual C#]** を選択します。
3. 使用している Visual Studio バージョンに応じて、 **[WPF アプリ (.NET Framework)]** を選択します。

## <a name="add-msal-to-your-project"></a>プロジェクトに MSAL を追加する

1. Visual Studio で、 **[ツール]**  >  **[NuGet パッケージ マネージャー]** >  **[パッケージ マネージャー コンソール]** の順に選択します。
2. [パッケージ マネージャー コンソール] ウィンドウで、次の Azure PowerShell コマンドを貼り付けます。

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE]
    > このコマンドを実行すると、Microsoft Authentication Library がインストールされます。 MSAL は、Azure Active Directory v2.0 で保護される API へのアクセスで使用するユーザー トークンの取得、キャッシュ、更新を処理します
    >

## <a name="register-your-application"></a>アプリケーションの登録

2 つのどちらかの方法でアプリケーションを登録できます。

### <a name="option-1-express-mode"></a>オプション 1: 簡易モード

次の手順を実行すると、アプリケーションをすばやく登録できます。
1. <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs" target="_blank">Azure portal のアプリの登録</a>クイックスタート エクスペリエンスに移動します。
1. アプリケーションの名前を入力し、 **[登録]** を選択します。
1. 画面の指示に従ってダウンロードし、1 回クリックするだけで、新しいアプリケーションが自動的に構成されます。

### <a name="option-2-advanced-mode"></a>オプション 2:詳細設定モード

アプリケーションを登録し、ソリューションにアプリケーション登録情報を追加するには、次の手順を実行します。
1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
1. アプリケーションの **名前** を入力します (例: `Win-App-calling-MsGraph`)。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
1. **[サポートされているアカウントの種類]** セクションで、 **[Accounts in any organizational directory (Any Azure AD directory - Multitenant) and personal Microsoft accounts (e.g. Skype, Xbox)]\(任意の組織ディレクトリ (任意の Azure AD ディレクトリ - マルチテナント) 内のアカウントと、個人用の Microsoft アカウント (Skype、Xbox など)\)** を選択します。
1. **[登録]** を選択します。
1. **[管理]** で、 **[認証]**  >  **[プラットフォームの追加]** の順に選択します。
1. **[モバイル アプリケーションとデスクトップ アプリケーション]** を選択します。
1. **[リダイレクト URI]** セクションで、 **https://login.microsoftonline.com/common/oauth2/nativeclient** を選択します。
1. **[構成]** をクリックします。
1. Visual Studio に移動し、*App.xaml.cs* ファイルを開き、以下のコード スニペットの `Enter_the_Application_Id_here` を、登録してコピーしたアプリケーション ID に置き換えます。

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>MSAL を初期化するコードの追加

この手順では、トークンの処理など MSAL ライブラリとのやり取りを処理するクラスを作成します。

1. *App.xaml.cs* ファイルを開き、クラスに MSAL の参照を追加します。

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. App クラスを以下のように更新します。

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .WithDefaultRedirectUri()
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>アプリケーション UI を作成する

このセクションでは、アプリケーションで、Microsoft Graph のような保護されたバックエンド サーバーに対してクエリを実行する方法を示します。

*MainWindow.xaml* ファイルは、プロジェクト テンプレートの一部として自動的に作成されます。 このファイルを開き、アプリケーションの *\<Grid>* ノードを次のコードに置き換えます。

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

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
        }
    ```

### <a name="more-information"></a>詳細情報

#### <a name="get-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する

`AcquireTokenInteractive` メソッドを呼び出すと、ユーザーにサインインを求めるウィンドウが表示されます。 通常、アプリケーションは、ユーザーが保護されたリソースに初めてアクセスするときに、対話形式でユーザーにサインインを求めます。 また、自動でのトークンの取得に失敗した場合 (ユーザーのパスワードが期限切れになっている場合など) にも、ユーザーはサインインする必要があります。

#### <a name="get-a-user-token-silently"></a>ユーザー トークンを自動で取得する

`AcquireTokenSilent` メソッドは、ユーザーの操作なしでトークンの取得と更新を処理します。 `AcquireTokenInteractive` が初めて実行された後、以降の呼び出しでは、保護されたリソースへのアクセスに使用するトークンを取得する際に、通常は `AcquireTokenSilent` メソッドを使用します。トークンを要求または更新する呼び出しが自動で行われるからです。

最終的に、`AcquireTokenSilent` メソッドは失敗します。 この失敗は、ユーザーがサインアウトしたか、別のデバイスでパスワードを変更したことが原因と考えられます。 ユーザーの操作によって解決できる問題が MSAL によって検出された場合、MSAL は `MsalUiRequiredException` 例外を発行します。 アプリケーションでは、この例外を 2 つの方法で処理できます。

* すぐに `AcquireTokenInteractive` を呼び出します。 この呼び出しにより、ユーザーにサインインを求めます。 ユーザーが使用できるオフライン コンテンツがないオンライン アプリケーションでは、通常、このパターンを使用します。 このガイド付きセットアップで生成されるサンプルでは、このパターンを使用します。サンプルの初回実行時に、実際の動作を確認できます。

* アプリケーションはユーザーによって使用されたことがないため、`PublicClientApp.Users.FirstOrDefault()` には null 値が含まれ、`MsalUiRequiredException` 例外がスローされます。

* サンプルのコードでは、`AcquireTokenInteractive` を呼び出してユーザーにサインインを求めることにより、この例外を処理します。

* 対話形式でのサインインが必要であることをユーザーに視覚的に示すことで、ユーザーが適切なタイミングでサインインできるようにします。 または、アプリケーションが後で `AcquireTokenSilent` を再試行します。 アプリケーションでオフライン コンテンツを使用できる場合など、ユーザーが中断なしでアプリケーションの他の機能を使用できる場合に、このパターンがよく使用されます。 この場合、保護されたリソースにアクセスしたり、古くなった情報を更新したりするために、サインインするタイミングをユーザーが決定できます。 また、一時的に使用できなくなっていたネットワークが回復したときに、アプリケーションが `AcquireTokenSilent` の再試行を決定することもできます。

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

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>保護された API に対する REST 呼び出しの実行についての詳細

このサンプル アプリケーションでは、`GetHttpContentWithToken` メソッドを使用して、トークンが必要な保護されたリソースに対して HTTP `GET` 要求を実行し、呼び出し元にその内容を返します。 このメソッドは、取得したトークンを HTTP Authorization ヘッダーに追加します。 このサンプルで使用するリソースは、ユーザーのプロファイル情報を表示する Microsoft Graph API *me* エンドポイントです。

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

### <a name="more-information-about-user-sign-out"></a>ユーザーのサインアウトに関する詳細情報

`SignOutButton_Click` メソッドは、MSAL ユーザー キャッシュからユーザーを削除します。これは実質的に MSAL に現在のユーザーを忘れさせることになり、以降の要求が対話形式で行われる場合のみトークンの取得が成功します。

このサンプルのアプリケーションは単一ユーザーに対応していますが、MSAL は複数のアカウントで同時にサインインするシナリオをサポートしています。 例として、電子メール アプリケーションで 1 人のユーザーが複数のアカウントを持っている場合が挙げられます。

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

### <a name="more-information"></a>詳細情報

Microsoft Graph API の呼び出しに使用するアクセス トークンに加えて、MSAL はユーザーのサインイン後に ID トークンも取得します。 このトークンには、ユーザー関連情報の少量のサブセットが含まれています。 `DisplayBasicTokenInfo` メソッドは、このトークンに含まれている基本的な情報を表示します。 たとえば、トークンの有効期限やアクセス トークンそのものを表す文字列に加えて、ユーザーの表示名や ID です。 *[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)* ボタンを複数回押すと、後の要求で同じトークンが再利用されてことが確認できます。 また、MSAL がトークンの更新時期だと判断したときに、有効期限が延長されることも確認できます。

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

## <a name="next-steps"></a>次の手順

保護された Web API を呼び出すデスクトップ アプリをビルドするには、複数のパートで構成される次のシナリオ シリーズを参照してください。

> [!div class="nextstepaction"]
> [シナリオ:Web API を呼び出すデスクトップ アプリ](scenario-desktop-overview.md)
