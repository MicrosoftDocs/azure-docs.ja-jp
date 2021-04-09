---
title: チュートリアル:認証に Microsoft ID プラットフォームを使用するユニバーサル Windows プラットフォーム (UWP) アプリを作成する | Azure
titleSuffix: Microsoft identity platform
description: このチュートリアルでは、ユーザーのサインインに Microsoft ID プラットフォームを使用し、そのユーザーに代わって Microsoft Graph API を呼び出すためのアクセス トークンを取得する UWP アプリケーションをビルドします。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: 6383f63d2118d8618f07bf3cb6cd08a0b16140f3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102650"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>チュートリアル:ユニバーサル Windows プラットフォーム (UWP) アプリケーションから Microsoft Graph API を呼び出す

このチュートリアルでは、ユーザーのサインインを処理して Microsoft Graph API を呼び出すためのアクセス トークンを取得するネイティブ ユニバーサル Windows プラットフォーム (UWP) アプリを作成します。 

このガイドの最後に、アプリケーションは個人のアカウントを使用して、保護されている API を呼び出します。 例としては、outlook.com、live.com などがあります。 アプリケーションは、Azure Active Directory (Azure AD) を持つ会社または組織の職場または学校アカウントも呼び出します。

このチュートリアルの内容:

> [!div class="checklist"]
> * Visual Studio で "*ユニバーサル Windows プラットフォーム (UWP)* " プロジェクトを作成する
> * Azure portal でアプリケーションを登録する
> * ユーザーのサインインとサインアウトをサポートするコードを追加する
> * Microsoft Graph API を呼び出すコードを追加する
> * アプリケーションをテストする

## <a name="prerequisites"></a>前提条件

* [ユニバーサル Windows プラットフォーム開発](https://visualstudio.microsoft.com/vs/)ワークロードがインストールされた [Visual Studio 2019](/windows/uwp/get-started/get-set-up)

## <a name="how-this-guide-works"></a>このガイドの利用法

![このチュートリアルで生成されたサンプル アプリの動作の紹介](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

このガイドでは、Microsoft Graph API に対してクエリを実行するサンプル UWP アプリケーションを作成します。 このシナリオでは、トークンは Authorization ヘッダーを使って HTTP 要求に追加されます。 トークンの取得と更新は、Microsoft Authentication Library で処理されます。

## <a name="nuget-packages"></a>NuGet パッケージ

このガイドでは、次の NuGet パッケージを使用します。

|ライブラリ|説明|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)| Microsoft Authentication Library|
|[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph)|Microsoft Graph のクライアント ライブラリ|

## <a name="set-up-your-project"></a>プロジェクトの設定

このセクションでは、Windows デスクトップ .NET アプリケーション (XAML) と Microsoft でサインインを統合するための詳細な手順を説明します。 その後、アプリケーションはトークンを必要とする Web API (Microsoft Graph API など) に対してクエリを実行することができます。

このガイドでは、Microsoft Graph API に対してクエリを実行するボタンとサインアウトするボタンを表示するアプリケーションを作成します。また、呼び出しの結果を含むテキスト ボックスも表示します。

> [!Tip]
> このチュートリアルでビルドするプロジェクトの完成バージョンを確認する場合は、[GitHub からダウンロード](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)できます。

### <a name="create-your-application"></a>アプリケーションの作成

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択します。
1. **[新しいプロジェクトの作成]** で、C# の **[空のアプリ (ユニバーサル Windows)]** を選択し、 **[次へ]** を選択します。
1. **[新しいプロジェクトの構成]** で、アプリに名前を付けて、 **[作成]** を選択します。
1. メッセージが表示されたら、 **[新しいユニバーサル Windows プラットフォーム プロジェクト]** で、 **[ターゲット バージョン]** と **[最小バージョン]** に任意のバージョンを選択し、 **[OK]** を選択します。

   ![最小バージョンとターゲット バージョン](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-the-microsoft-authentication-library-to-your-project"></a>プロジェクトへの Microsoft Authentication Library の追加

1. Visual Studio で、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。
1. 以下のコマンドをコピーして、**パッケージ マネージャー コンソール** のウィンドウに貼り付けます。

    ```powershell
    Install-Package Microsoft.Identity.Client
    Install-Package Microsoft.Graph
    ```

   > [!NOTE]
   > 1 つ目のコマンドでは、[Microsoft Authentication Library (MSAL.NET)](https://aka.ms/msal-net) がインストールされます。 Microsoft ID プラットフォームにより保護された API にアクセスするユーザー トークンは、MSAL.NET によって取得、キャッシュ、更新されます。 2 つ目のコマンドでは、Microsoft Graph に対する要求を認証してサービスを呼び出す [Microsoft Graph .NET クライアント ライブラリ](https://github.com/microsoftgraph/msgraph-sdk-dotnet)がインストールされます。

### <a name="create-your-applications-ui"></a>アプリケーションの UI の作成

Visual Studio では、プロジェクト テンプレートの一部として *MainPage.xaml* が作成されます。 このファイルを開き、アプリケーションの **Grid** ノードを次のコードに置き換えます。

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

### <a name="use-the-microsoft-authentication-library-to-get-a-token-for-the-microsoft-graph-api"></a>Microsoft Authentication Library を使用して Microsoft Graph API のトークンを取得する

このセクションでは、Microsoft Authentication Library を使用して Microsoft Graph API のトークンを取得する方法について説明します。 *MainPage.xaml.cs* ファイルに変更を加えます。

1. *MainPage.xaml.cs* で、次の参照を追加します。

    ```csharp
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. `MainPage` クラスを次のコードに置き換えます。

    ```csharp
    public sealed partial class MainPage : Page
    {

        //Set the scope for API call to user.read
        private string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        private const string ClientId = "[Application Id pasted from the application registration portal]";

        private const string Tenant = "common"; // Alternatively "[Enter your tenant, as obtained from the Azure portal, e.g. kko365.onmicrosoft.com]"
        private const string Authority = "https://login.microsoftonline.com/" + Tenant;

        // The MSAL Public client app
        private static IPublicClientApplication PublicClientApp;

        private static string MSGraphURL = "https://graph.microsoft.com/v1.0/";
        private static AuthenticationResult authResult;

        public MainPage()
        {
            this.InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                // Sign in user using MSAL and obtain an access token for Microsoft Graph
                GraphServiceClient graphClient = await SignInAndInitializeGraphServiceClient(scopes);

                // Call the /me endpoint of Graph
                User graphUser = await graphClient.Me.Request().GetAsync();

                // Go back to the UI thread to make changes to the UI
                await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    ResultText.Text = "Display Name: " + graphUser.DisplayName + "\nBusiness Phone: " + graphUser.BusinessPhones.FirstOrDefault()
                                      + "\nGiven Name: " + graphUser.GivenName + "\nid: " + graphUser.Id
                                      + "\nUser Principal Name: " + graphUser.UserPrincipalName;
                    DisplayBasicTokenInfo(authResult);
                    this.SignOutButton.Visibility = Visibility.Visible;
                });
            }
            catch (MsalException msalEx)
            {
                await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalEx}");
            }
            catch (Exception ex)
            {
                await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
                return;
            }
        }
                /// <summary>
        /// Signs in the user and obtains an access token for Microsoft Graph
        /// </summary>
        /// <param name="scopes"></param>
        /// <returns> Access Token</returns>
        private static async Task<string> SignInUserAndGetTokenUsingMSAL(string[] scopes)
        {
            // Initialize the MSAL library by building a public client application
            PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .WithUseCorporateNetwork(false)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                 .WithLogging((level, message, containsPii) =>
                 {
                     Debug.WriteLine($"MSAL: {level} {message} ");
                 }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
                .Build();

            // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.
            IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
            IAccount firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                  .ExecuteAsync()
                                                  .ConfigureAwait(false);

            }
            return authResult.AccessToken;
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する<a name="more-information"></a>

`AcquireTokenInteractive` メソッドによって、ユーザーにサインインを求めるウィンドウが表示されます。 通常、アプリケーションのユーザーは、保護されたリソースに初めてアクセスするときに、対話形式でサインインを求められます。 また、自動でのトークンの取得に失敗した場合にも、ユーザーはサインインする必要があります。 たとえば、ユーザーのパスワードが期限切れになっている場合などです。

#### <a name="get-a-user-token-silently"></a>ユーザー トークンを自動で取得する

`AcquireTokenSilent` メソッドは、ユーザーの操作なしでトークンの取得と更新を処理します。 `AcquireTokenInteractive` が初めて実行され、ユーザーに資格情報の入力を求めるメッセージが表示されたら、`AcquireTokenSilent` メソッドを使って後続の呼び出しのためにトークンを要求します。 このメソッドでは、トークンが自動的に取得されます。 トークンのキャッシュと更新は、Microsoft Authentication Library で処理されます。

最終的に、`AcquireTokenSilent` メソッドは失敗します。 この失敗は、ユーザーがサインアウトしたか、別のデバイスでパスワードを変更したことが原因と考えられます。 対話操作を必要とする問題が Microsoft Authentication Library によって検出された場合、`MsalUiRequiredException` 例外がスローされます。 アプリケーションでは、この例外を 2 つの方法で処理できます。

* アプリケーションによって `AcquireTokenInteractive` が直ちに呼び出されます。 この呼び出しにより、ユーザーにサインインを求めます。 通常、ユーザーが使用できるオフライン コンテンツがないオンライン アプリケーションでは、このアプローチを使用します。 このガイドの設定で生成されるサンプルは、このパターンに従っています。 サンプルを初めて実行したときの動作で、それがわかります。

   アプリケーションはユーザーによって使用されたことがないため、`accounts.FirstOrDefault()` には null 値が含まれ、`MsalUiRequiredException` 例外がスローされます。

   その後、サンプルのコードは、`AcquireTokenInteractive` を呼び出すことによって例外を処理します。 この呼び出しにより、ユーザーにサインインを求めます。

* アプリケーションによって、サインインする必要があることがユーザーに視覚的に示されます。 その場合、ユーザーは適切な時を選んでサインインすることができます。 アプリケーションは後で `AcquireTokenSilent` を再試行できます。 このアプローチは、ユーザーが中断することなく他のアプリケーション機能を使用できる場合に使用されます。 たとえば、アプリケーションでオフラインのコンテンツを使用可能な場合です。 この場合、ユーザーはサインインするタイミングを決めることができます。 アプリケーションは、ネットワークが一時的に使用できなくなった後に `AcquireTokenSilent` を再試行できます。

### <a name="instantiate-the-microsoft-graph-service-client-by-obtaining-the-token-from-the-signinuserandgettokenusingmsal-method"></a>SignInUserAndGetTokenUsingMSAL メソッドからトークンを取得して Microsoft Graph サービス クライアントをインスタンス化する

次の新しいメソッドを *MainPage.xaml.cs* に追加します。

```csharp
      /// <summary>
     /// Sign in user using MSAL and obtain a token for Microsoft Graph
     /// </summary>
     /// <returns>GraphServiceClient</returns>
     private async static Task<GraphServiceClient> SignInAndInitializeGraphServiceClient(string[] scopes)
     {
         GraphServiceClient graphClient = new GraphServiceClient(MSGraphURL,
             new DelegateAuthenticationProvider(async (requestMessage) =>
             {
                 requestMessage.Headers.Authorization = new AuthenticationHeaderValue("bearer", await SignInUserAndGetTokenUsingMSAL(scopes));
             }));

         return await Task.FromResult(graphClient);
     }
```

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>保護された API に対する REST 呼び出しの実行についての詳細

このサンプル アプリケーションでは、`GetGraphServiceClient` メソッドからアクセス トークンを使用して `GraphServiceClient` をインスタンス化します。 次に、`GraphServiceClient` を使用して、**me** エンドポイントからユーザーのプロファイル情報を取得します。

### <a name="add-a-method-to-sign-out-the-user"></a>ユーザーをサインアウトするメソッドの追加

ユーザーをサインアウトするには、次のメソッドを *MainPage.xaml.cs* に追加します。

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
    IAccount firstAccount = accounts.FirstOrDefault();

    try
    {
        await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            ResultText.Text = "User has signed out";
            this.CallGraphButton.Visibility = Visibility.Visible;
                this.SignOutButton.Visibility = Visibility.Collapsed;
            });
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing out user: {ex.Message}";
        }
    }
```

MSAL.NET では、トークンの取得やアカウントの操作に非同期メソッドを使用しています。 したがって、UI スレッドでの UI 操作をサポートします。 そのため、`Dispatcher.RunAsync` 呼び出しと、予防的な `ConfigureAwait(false)` 呼び出しが使用されています。

#### <a name="more-information-about-signing-out"></a>サインアウトに関する詳細情報<a name="more-information-on-sign-out"></a>

`SignOutButton_Click` メソッドは、Microsoft Authentication Library ユーザー キャッシュからユーザーを削除します。 このメソッドは、実際には現在のユーザーを忘れるように Microsoft Authentication Library に指示します。 トークンを取得するための以降の要求は、対話型の場合にのみ成功します。

このサンプルのアプリケーションは、単一のユーザーをサポートしています。 Microsoft Authentication Library では、ユーザーが複数のアカウントでサインインできるシナリオがサポートされています。 例として、電子メール アプリケーションで 1 人のユーザーがいくつかのアカウントを持っている場合が挙げられます。

### <a name="display-basic-token-information"></a>基本的なトークン情報を表示する

トークンについての基本的な情報を表示するには、次のメソッドを *MainPage.xaml.cs* ファイルに追加します。

```csharp
/// <summary>
/// Display basic information contained in the token. Needs to be called from the UI thread.
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

#### <a name="more-information"></a>詳細情報<a name="more-information-1"></a>

**OpenID 接続** を使用して取得した ID トークンにも、ユーザー関連情報の少量のサブセットが含まれています。 `DisplayBasicTokenInfo` は、トークンに含まれている基本的な情報を表示します。 この情報には、ユーザーの表示名や ID が含まれます。 また、トークンの有効期限やアクセス トークン自体を表す文字列も含まれます。 **[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)** ボタンを数回選択すると、その後の要求で同じトークンが再利用されていることが確認できます。 また、Microsoft Authentication Library がトークンの更新時期だと判断したときに、有効期限が延長されることも確認できます。

### <a name="display-message"></a>メッセージの表示

次の新しいメソッドを *MainPage.xaml.cs* に追加します。

```csharp
/// <summary>
/// Displays a message in the ResultText. Can be called from any thread.
/// </summary>
private async Task DisplayMessageAsync(string message)
{
     await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
         () =>
         {
             ResultText.Text = message;
         });
     }
```

## <a name="register-your-application"></a>アプリケーションの登録

次に、アプリケーションを登録します。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
1. アプリケーションの **名前** を入力します (例: `UWP-App-calling-MSGraph`)。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory (Any Azure AD directory - Multitenant) and personal Microsoft accounts (e.g. Skype, Xbox)]\(任意の組織ディレクトリ内のアカウント (任意の Azure AD ディレクトリ - マルチテナント) と、個人用の Microsoft アカウント (Skype、Xbox など)\)** を選択します。 
1. **[登録]** を選択します。
1. 概要ページで、 **[アプリケーション (クライアント) ID]** の値を見つけてコピーします。 Visual Studio に戻って *MainPage.xaml.cs* を開き、`ClientId` の値を、この値に置き換えます。

アプリケーションの認証を構成します。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> に戻り、 **[管理]** で **[認証]**  >  **[プラットフォームを追加]** の順に選択し、 **[モバイル アプリケーションとデスクトップ アプリケーション]** を選択します。
1. **[リダイレクト URI]** セクションで、「`https://login.microsoftonline.com/common/oauth2/nativeclient`」と入力します。
1. **[構成]** をクリックします。

アプリケーション用に API アクセス許可を構成します。

1. **[管理]** で、 **[API のアクセス許可]**  >  **[アクセス許可の追加]** の順に選択します。
1. **[Microsoft Graph]** を選択します。
1. **[委任されたアクセス許可]** を選択し、*User.Read* を探して、 **[User.Read]** が選択されていることを確認します。
1. 変更を行った場合は、 **[アクセス許可の追加]** を選択して保存します。

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>フェデレーション ドメインでの統合認証の有効化 (省略可能)

統合 Windows 認証を、フェデレーション Azure AD ドメインと共に使用する場合に有効にするには、アプリケーション マニフェストで追加機能を有効にする必要があります。 Visual Studio で、アプリケーションに戻ります。

1. *Package.appxmanifest* を開きます。
1. **[機能]** を選択し、次の設定を有効にします。

   * **エンタープライズ認証**
   * **プライベート ネットワーク (クライアントとサーバー)**
   * **共有ユーザー証明書**

> [!IMPORTANT]
> このサンプルの既定では、[統合 Windows 認証](https://aka.ms/msal-net-iwa)は構成されていません。 `Enterprise Authentication` または `Shared User Certificates` 機能を要求するアプリケーションでは、Windows ストアによるより高いレベルの検証が必要です。 また、すべての開発者がより高いレベルの検証を望むとは限りません。 この設定は、統合 Windows 認証とフェデレーション Azure AD ドメインが必要な場合にのみ有効にしてください。

## <a name="alternate-approach-to-using-withdefaultredirecturi"></a>WithDefaultRedirectURI() を使用した別のアプローチ

現在のサンプルでは、`WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")` メソッドが使用されています。 `WithDefaultRedirectURI()` を使用するには、次の手順に従います。

1. *MainPage.XAML.cs* で、`WithRedirectUri` を `WithDefaultRedirectUri` に置き換えます。

   **現在のコード**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority(Authority)
       .WithUseCorporateNetwork(false)
       .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();

   ```

   **更新後のコード**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority("https://login.microsoftonline.com/common")
       .WithUseCorporateNetwork(false)
       .WithDefaultRedirectUri()
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();
   ```

2.  *MainPage.xaml.cs* に `redirectURI` フィールドを追加してそこにブレークポイントを設定することにより、アプリのコールバック URI を調べます。

    ```csharp

    public sealed partial class MainPage : Page
    {
            ...

            string redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker
                                .GetCurrentApplicationCallbackUri().ToString();
            public MainPage()
            {
                ...
            }
           ...
    }

    ```

    アプリを実行し、ブレークポイントに到達したら `redirectUri` の値をコピーします。 この値は `ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/` のようになります。

    このコード行は、値を取得するために 1 回だけ実行すればよく、その後は削除してかまいません。

3. 返された値を、アプリの登録ポータルの **[認証]** ペインの **[RedirectUri]** に追加します。

## <a name="test-your-code"></a>コードのテスト

アプリケーションをテストするには、Visual Studio で **F5** キーを押してプロジェクトを実行します。 メイン ウィンドウが表示されます。

![アプリケーションのユーザー インターフェイス](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

テストの準備ができたら、 **[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)** を選択します。 次に、Azure AD 組織アカウントまたは Microsoft アカウント (live.com、outlook.com など) を使用してサインインします。 ユーザーが初めてこのテストを実行すると、ユーザーにサインインを求めるウィンドウが表示されます。

### <a name="consent"></a>同意

アプリケーションに初めてサインインすると、次の画像のような同意画面が表示されます。 アクセスするには、 **[はい]** を選択して、明示的に同意します。

![アクセス同意画面](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>予想される結果

**[API Call Results]\(API 呼び出しの結果\)** 画面に、Microsoft Graph API の呼び出しによって返されたユーザー プロファイル情報が表示されます。

![API 呼び出しの結果画面](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

`AcquireTokenInteractive` または `AcquireTokenSilent` によって取得したトークンに関する以下の基本的な情報も、 **[Token Info]\(トークン情報\)** ボックスに表示されます。

|プロパティ  |Format  |説明 |
|---------|---------|---------|
|`Username` |`user@domain.com` |ユーザーを識別するユーザー名。|
|`Token Expires` |`DateTime` |トークンの有効期限が切れる日時。 Microsoft Authentication Library では、必要に応じてトークンを更新することで、有効期限日を延長します。|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API は、ユーザーのプロファイルを読み込むためにスコープ `user.read` を必要とします。 このスコープは、アプリケーション登録ポータルで登録されたすべてのアプリケーションで、既定で追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 たとえば、Microsoft Graph API には、ユーザーの予定表を表示するための `Calendars.Read` スコープが必要です。

アプリケーションのコンテキストでユーザーの予定表にアクセスするには、`Calendars.Read` の委任されたアクセス許可をアプリケーション登録情報に追加します。 次に、`acquireTokenSilent` の呼び出しに `Calendars.Read` スコープを追加します。

スコープの数を増やすと、ユーザーは追加の同意を求められることがあります。

## <a name="known-issues"></a>既知の問題

### <a name="issue-1"></a>問題 1

フェデレーション Azure AD ドメイン上のアプリケーションにサインインするときに、次のいずれかのエラー メッセージが表示されます。

* "要求に有効なクライアント証明書が見つかりませんでした。"
* "ユーザーの証明書ストアに有効な証明書が見つかりませんでした。"
* "別の認証方法を選択してやり直してください。"

**原因:** エンタープライズ機能および証明書機能が有効になっていません。

**解決方法:** 「[フェデレーション ドメインでの統合認証の有効化 (省略可能)](#enable-integrated-authentication-on-federated-domains-optional)」の手順に従います。

### <a name="issue-2"></a>問題 2

[フェデレーション ドメインの統合認証](#enable-integrated-authentication-on-federated-domains-optional)を有効にし、Windows 10 コンピューターで Windows Hello を使用して、多要素認証が構成されている環境でサインインしようとします。 証明書の一覧が表示されます。 PIN の使用を選択しても、PIN ウィンドウが表示されません。

**原因:** この問題は、Windows 10 デスクトップで実行される UWP アプリケーションの Web 認証ブローカーに関する既知の制限です。 Windows 10 Mobile では正常に動作します。

**対処法:** **[Sign in with other options]\(他のオプションでサインイン\)** を選択します。 次に、 **[Sign in with a username and password]\(ユーザー名とパスワードでサインイン\)** を選択します。 **[Provide your password]\(パスワードを指定\)** を選択します。 電話認証プロセスに進みます。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次の手順

.NET アプリケーションでの認可と認証に Microsoft Authentication Library (MSAL) を使用する方法を参照してください:

> [!div class="nextstepaction"]
> [Microsoft Authentication Library (MSAL) の概要](msal-overview.md)
