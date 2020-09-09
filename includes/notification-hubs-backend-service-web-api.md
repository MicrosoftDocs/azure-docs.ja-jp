---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: f0e83548d3ba3b353b471e2e3429a23421aec7b2
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095487"
---
### <a name="create-a-web-project"></a>Web プロジェクトの作成

1. **Visual Studio** で、 **[ファイル]**  >  **[新しいソリューション]** を選択します。

1. **[.NET Core]**  >  **[アプリ]**  >  **[ASP.NET Core]**  >  **[API]**  >  **[次へ]** を選択します。
  
1. **[Configure your new ASP.NET Core Web API] (新しい ASP.NET Core Web API の構成)** ダイアログで、 **.NET Core 3.1** の **[ターゲット フレームワーク]** を選択します。

1. **[プロジェクト名]** に「*PushDemoApi*」と入力して、 **[作成]** を選択します。

1. デバッグを開始して ( **[Command]**  +  **[Enter]** )、テンプレート アプリをテストします。

    > [!NOTE]
    > テンプレート アプリは、*launchUrl* として **WeatherForecastController** を使用するように構成されています。 これは **[プロパティ]**  >  **[launchSettings.json]** で設定されます。  
    >
    > **[Invalid development certificate found] (無効な開発証明書が見つかりました)** というメッセージが表示された場合は、以下を実行します。
    >
    > 1. 'dotnet dev-certs https' ツールを実行してこの問題を解決することに同意するには、 **[はい]** をクリックします。 証明書のパスワードと、キーチェーンのパスワードを入力するように求められます。
    >
    > 1. **[Install and trust the new certificate] (新しい証明書をインストールして信頼する)** の確認を求められたら、 **[はい]** をクリックします。次に、キーチェーンのパスワードを入力します。

1. **Controllers** フォルダーを展開して、**WeatherForecastController.cs** を削除します。

1. **WeatherForecast.cs** を削除します。

1. **PushDemoApi** プロジェクトを **Control** + **クリック**し、 **[追加]** メニューから **[新しいファイル...]** を選択します。

1. [シークレット マネージャー ツール](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=linux#secret-manager)を使用して、ローカル構成値を設定します。 ソリューションからシークレットを分離することによって、ソース管理で終了することがないようにできます。 **ターミナル**を開き、プロジェクト ファイルのディレクトリに移動して、次のコマンドを実行します。

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set "NotificationHub:Name" <value>
    dotnet user-secrets set "NotificationHub:ConnectionString" <value>
    ```

    プレースホルダーの値は、実際の通知ハブの名前と接続文字列の値に置き換えてください。 それらは「[通知ハブの作成](#create-a-notification-hub)」セクションでメモしました。 それ以外の場合は、[Azure](https://portal.azure.com) で確認できます。

    **NotificationsHub:Name**:  
    **[概要]** の上部にある **[要点]** サマリーの *[名前]* を参照してください。  

    **NotificationHub:ConnectionString**:  
    **アクセス ポリシー**の *DefaultFullSharedAccessSignature* を参照してください。

    > [!NOTE]
    > 運用環境のシナリオでは、[Azure KeyVault](https://azure.microsoft.com/services/key-vault) などのオプションを参照して、接続文字列を安全に格納することができます。 わかりやすくするために、シークレットは [Azure App Service](https://azure.microsoft.com/services/app-service/) のアプリケーション設定に追加されます。

### <a name="authenticate-clients-using-an-api-key-optional"></a>API キーを使用してクライアントを認証する (オプション)

API キーはトークンほど安全ではありませんが、このチュートリアルの目的には十分です。 API キーは、[ASP.NET ミドルウェア](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1)を介して簡単に構成できます。

1. **API キー**をローカル構成値に追加します。

    ```bash
    dotnet user-secrets set "Authentication:ApiKey" <value>
    ```

    > [!NOTE]
    > プレースホルダーの値を実際の値に置き換え、メモする必要があります。

1. **PushDemoApi** プロジェクトを **Control** + **クリック**し、 **[追加]** メニューから **[新しいフォルダー]** を選択します。次に、 **[フォルダー名]** として「*Authentication*」を使用して **[追加]** をクリックします。

1. **Authentication** フォルダーを **Control** + **クリック**し、 **[追加]** メニューから **[新しいファイル...]** を選択します。

1. **[全般]**  >  **[空のクラス]** を選択し、 **[名前]** に「*ApiKeyAuthOptions.cs*」と入力して **[新規]** をクリックし、次の実装を追加します。

    ```csharp
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthOptions : AuthenticationSchemeOptions
        {
            public const string DefaultScheme = "ApiKey";
            public string Scheme => DefaultScheme;
            public string ApiKey { get; set; }
        }
    }
    ```

1. *ApiKeyAuthHandler.cs* という別の **[空のクラス]** を **Authentication** フォルダーに追加し、次の実装を追加します。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Security.Claims;
    using System.Text.Encodings.Web;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthHandler : AuthenticationHandler<ApiKeyAuthOptions>
        {
            const string ApiKeyIdentifier = "apikey";

            public ApiKeyAuthHandler(
                IOptionsMonitor<ApiKeyAuthOptions> options,
                ILoggerFactory logger,
                UrlEncoder encoder,
                ISystemClock clock)
                : base(options, logger, encoder, clock) {}

            protected override Task<AuthenticateResult> HandleAuthenticateAsync()
            {
                string key = string.Empty;

                if (Request.Headers[ApiKeyIdentifier].Any())
                {
                    key = Request.Headers[ApiKeyIdentifier].FirstOrDefault();
                }
                else if (Request.Query.ContainsKey(ApiKeyIdentifier))
                {
                    if (Request.Query.TryGetValue(ApiKeyIdentifier, out var queryKey))
                        key = queryKey;
                }

                if (string.IsNullOrWhiteSpace(key))
                    return Task.FromResult(AuthenticateResult.Fail("No api key provided"));

                if (!string.Equals(key, Options.ApiKey, StringComparison.Ordinal))
                    return Task.FromResult(AuthenticateResult.Fail("Invalid api key."));

                var identities = new List<ClaimsIdentity> {
                    new ClaimsIdentity("ApiKeyIdentity")
                };

                var ticket = new AuthenticationTicket(
                    new ClaimsPrincipal(identities), Options.Scheme);

                return Task.FromResult(AuthenticateResult.Success(ticket));
            }
        }
    }
    ```

    > [!NOTE]
    > [認証ハンドラー](https://docs.microsoft.com/aspnet/core/security/authentication/?view=aspnetcore-3.1#authentication-handler)は、スキームの動作 (この場合はカスタム API キー スキーム) を実装する型です。

1. *ApiKeyAuthenticationBuilderExtensions.cs* という別の **[空のクラス]** を **Authentication** フォルダーに追加し、次の実装を追加します。

    ```csharp
    using System;
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public static class AuthenticationBuilderExtensions
        {
            public static AuthenticationBuilder AddApiKeyAuth(
                this AuthenticationBuilder builder,
                Action<ApiKeyAuthOptions> configureOptions)
            {
                return builder
                    .AddScheme<ApiKeyAuthOptions, ApiKeyAuthHandler>(
                        ApiKeyAuthOptions.DefaultScheme,
                        configureOptions);
            }
        }
    }
    ```

    > [!NOTE]
    > この拡張メソッドを利用すると **Startup.cs** のミドルウェア構成コードが簡素化され、より読みやすくなります。

1. **Startup.cs** で **ConfigureServices** メソッドを更新して、**services.AddControllers** メソッドの呼び出しの下で API キー認証を構成します。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();

        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = ApiKeyAuthOptions.DefaultScheme;
            options.DefaultChallengeScheme = ApiKeyAuthOptions.DefaultScheme;
        }).AddApiKeyAuth(Configuration.GetSection("Authentication").Bind);
    }
    ```

1. さらに **Startup.cs** で **Configure** メソッドを更新して、アプリの **IApplicationBuilder** で **UseAuthentication** および **UseAuthorization** 拡張メソッドを呼び出します。 これらのメソッドが **UseRouting** の後、**app.UseEndpoints** の前に呼び出されていることを確認します。

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseHttpsRedirection();

        app.UseRouting();

        app.UseAuthentication();

        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
    ```

    > [!NOTE]
    > **UseAuthentication** を呼び出すと、(**ConfigureServices** から) 以前に登録した認証方式を使用するミドルウェアが登録されます。 これは、認証されているユーザーに依存するミドルウェアの前に呼び出す必要があります。

### <a name="add-dependencies-and-configure-services"></a>依存関係を追加してサービスを構成する

ASP.NET Core では、[依存関係の挿入 (DI)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1) ソフトウェア デザイン パターンがサポートされています。これは、クラスと依存関係の間で[制御の反転 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) を実現するための技術です。  

通知ハブおよび [Notification Hubs SDK のバックエンド操作](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)への使用は、サービス内にカプセル化されます。 サービスが登録され、適切な抽象化によって使用できるようになります。

1. **Dependencies** フォルダーを **Control** + **クリック**し、 **[NuGet パッケージの管理...]** を選択します。

1. **Microsoft.Azure.NotificationHubs** を検索し、オンになっていることを確認します。

1. **[パッケージの追加]** をクリックし、ライセンス条項への同意を求めるメッセージが表示されたら、 **[同意する]** をクリックします。

1. **PushDemoApi** プロジェクトを **Control** + **クリック**し、 **[追加]** メニューから **[新しいフォルダー]** を選択します。次に、 **[フォルダー名]** として「*Models*」を使用して **[追加]** をクリックします。 

1. **Models** フォルダーを **Control** + **クリック**し、 **[追加]** メニューから **[新しいファイル...]** を選択します。

1. **[全般]**  >  **[空のクラス]** を選択し、 **[名前]** に「*PushTemplates.cs*」と入力して **[新規]** をクリックし、次の実装を追加します。

    ```csharp
    namespace PushDemoApi.Models
    {
        public class PushTemplates
        {
            public class Generic
            {
                public const string Android = "{ \"notification\": { \"title\" : \"PushDemo\", \"body\" : \"$(alertMessage)\"}, \"data\" : { \"action\" : \"$(alertAction)\" } }";
                public const string iOS = "{ \"aps\" : {\"alert\" : \"$(alertMessage)\"}, \"action\" : \"$(alertAction)\" }";
            }

            public class Silent
            {
                public const string Android = "{ \"data\" : {\"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\"} }";
                public const string iOS = "{ \"aps\" : {\"content-available\" : 1, \"apns-priority\": 5, \"sound\" : \"\", \"badge\" : 0}, \"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\" }";
            }
        }
    }
    ```

    > [!NOTE]
    > このクラスには、このシナリオで必要とされる汎用およびサイレント通知用のトークン化された通知ペイロードが含まれています。 ペイロードは[インストール](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation?view=azure-dotnet)の外部で定義されます。これにより、サービスを介して既存のインストールを更新しなくても実験を行うことができます。 この方法でのインストールに対する変更の処理については、このチュートリアルでは扱いません。 運用環境では、[カスタム テンプレート](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)を検討してください。

1. **[全般]**  >  **[空のクラス]** を選択し、 **[名前]** に「*DeviceInstallation.cs*」と入力して **[新規]** をクリックし、次の実装を追加します。

    ```csharp
    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class DeviceInstallation
        {
            [Required]
            public string InstallationId { get; set; }

            [Required]
            public string Platform { get; set; }

            [Required]
            public string PushChannel { get; set; }

            public IList<string> Tags { get; set; } = Array.Empty<string>();
        }
    }
    ```

1. *NotificationRequest.cs* という別の **[空のクラス]** を **Models** フォルダーに追加し、次の実装を追加します。

    ```csharp
    using System;

    namespace PushDemoApi.Models
    {
        public class NotificationRequest
        {
            public string Text { get; set; }
            public string Action { get; set; }
            public string[] Tags { get; set; } = Array.Empty<string>();
            public bool Silent { get; set; }
        }
    }
    ```

1. *NotificationHubOptions.cs* という別の **[空のクラス]** を **Models** フォルダーに追加し、次の実装を追加します。

    ```csharp
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class NotificationHubOptions
        {
            [Required]
            public string Name { get; set; }

            [Required]
            public string ConnectionString { get; set; }
        }
    }
    ```

1. **PushDemoApi** プロジェクトに *Services* という名前の新しいフォルダーを追加します。

1. *INotificationService.cs* という **[空のインターフェイス]** を **Services** フォルダーに追加し、次の実装を追加します。

    ```csharp
    using System.Threading.Tasks;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public interface INotificationService
        {
            Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token);
            Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token);
            Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token);
        }
    }
    ```

1. *NotificationHubsService.cs* という **[空のクラス]** を **Services** フォルダーに追加し、次のコードを追加して **INotificationService** インターフェイスを実装します。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public class NotificationHubService : INotificationService
        {
            readonly NotificationHubClient _hub;
            readonly Dictionary<string, NotificationPlatform> _installationPlatform;
            readonly ILogger<NotificationHubService> _logger;

            public NotificationHubService(IOptions<NotificationHubOptions> options, ILogger<NotificationHubService> logger)
            {
                _logger = logger;
                _hub = NotificationHubClient.CreateClientFromConnectionString(
                    options.Value.ConnectionString,
                    options.Value.Name);

                _installationPlatform = new Dictionary<string, NotificationPlatform>
                {
                    { nameof(NotificationPlatform.Apns).ToLower(), NotificationPlatform.Apns },
                    { nameof(NotificationPlatform.Fcm).ToLower(), NotificationPlatform.Fcm }
                };
            }

            public async Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(deviceInstallation?.InstallationId) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.Platform) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.PushChannel))
                    return false;

                var installation = new Installation()
                {
                    InstallationId = deviceInstallation.InstallationId,
                    PushChannel = deviceInstallation.PushChannel,
                    Tags = deviceInstallation.Tags
                };

                if (_installationPlatform.TryGetValue(deviceInstallation.Platform, out var platform))
                    installation.Platform = platform;
                else
                    return false;

                try
                {
                    await _hub.CreateOrUpdateInstallationAsync(installation, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(installationId))
                    return false;

                try
                {
                    await _hub.DeleteInstallationAsync(installationId, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token)
            {
                if ((notificationRequest.Silent &&
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
                    (!notificationRequest.Silent &&
                    (string.IsNullOrWhiteSpace(notificationRequest?.Text)) ||
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)))
                    return false;

                var androidPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.Android :
                    PushTemplates.Generic.Android;

                var iOSPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.iOS :
                    PushTemplates.Generic.iOS;

                var androidPayload = PrepareNotificationPayload(
                    androidPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                var iOSPayload = PrepareNotificationPayload(
                    iOSPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                try
                {
                    if (notificationRequest.Tags.Length == 0)
                    {
                        // This will broadcast to all users registered in the notification hub
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, token);
                    }
                    else if (notificationRequest.Tags.Length <= 20)
                    {
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, notificationRequest.Tags, token);
                    }
                    else
                    {
                        var notificationTasks = notificationRequest.Tags
                            .Select((value, index) => (value, index))
                            .GroupBy(g => g.index / 20, i => i.value)
                            .Select(tags => SendPlatformNotificationsAsync(androidPayload, iOSPayload, tags, token));

                        await Task.WhenAll(notificationTasks);
                    }

                    return true;
                }
                catch (Exception e)
                {
                    _logger.LogError(e, "Unexpected error sending notification");
                    return false;
                }
            }

            string PrepareNotificationPayload(string template, string text, string action) => template
                .Replace("$(alertMessage)", text, StringComparison.InvariantCulture)
                .Replace("$(alertAction)", action, StringComparison.InvariantCulture);

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, token)
                };

                return Task.WhenAll(sendTasks);
            }

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, IEnumerable<string> tags, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, tags, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, tags, token)
                };

                return Task.WhenAll(sendTasks);
            }
        }
    }
    ```

    > [!NOTE]
    > **SendTemplateNotificationAsync** に指定するタグ式は、20 個のタグに制限されています。 ほとんどの演算子では 6 に制限されていますが、この場合の式には、OR (||) のみが含まれています。 要求に 20 個を超えるタグがある場合は、複数の要求に分割する必要があります。 詳細については、「[ルーティングとタグ式](https://msdn.microsoft.com/library/azure/Dn530749.aspx?f=255&MSPPError=-2147217396)」のドキュメントを参照してください。

1. **Startup.cs** で **ConfigureServices** メソッドを更新して、**NotificationHubsService** を **INotificationService** のシングルトン実装として追加します。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ...

        services.AddSingleton<INotificationService, NotificationHubService>();

        services.AddOptions<NotificationHubOptions>()
            .Configure(Configuration.GetSection("NotificationHub").Bind)
            .ValidateDataAnnotations();
    }
    ```

### <a name="create-the-notifications-api"></a>通知 API の作成

1. **Controllers** フォルダーを **Control** + **クリック**し、 **[追加]** メニューから **[新しいファイル...]** を選択します。

1. **[ASP.NET Core]**  >  **[Web API コントローラー クラス]** を選択し、 **[名前]** に「*NotificationsController*」と入力して **[新規]** をクリックします。

1. 次の名前空間をファイルの先頭に追加します。

    ```csharp
    using System.ComponentModel.DataAnnotations;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using PushDemoApi.Models;
    using PushDemoApi.Services;
    ```

1. **ControllerBase** から派生し、**ApiController** 属性で修飾されるように、テンプレート コントローラーを更新します。

    ```cs
    [ApiController]
    [Route("api/[controller]")]
    public class NotificationsController : ControllerBase
    {
        // Templated methods here
    }
    ```

    > [!NOTE]
    > **Controller** 基底クラスではビューのサポートが提供されますが、この場合は必要ありません。そのため **ControllerBase** を代わりに使用することができます。

1. 「[API キーを使用してクライアントを認証する](#authenticate-clients-using-an-api-key-optional)」セクションを完了した場合は、**NotificationsController** を **Authorize** 属性でも装飾する必要があります。

    ```cs
    [Authorize]
    ```

1. コンストラクターを更新して、**INotificationService** の登録済みインスタンスを引数として受け取り、それを readonly メンバーに割り当てます。

    ```cs
    readonly INotificationService _notificationService;

    public NotificationsController(INotificationService notificationService)
    {
        _notificationService = notificationService;
    }
    ```

1. **launchSettings.json** (**Properties** フォルダー内) で、**RegistrationsController** **Route** 属性に指定されている URL と一致するように **launchUrl** を `weatherforecast` から *api/notifications* に変更します。

1. デバッグを開始して ( **[Command]**  +  **[Enter]** )、アプリが新しい **NotificationsController** で動作し、**401 Unauthorized** 状態を返すことを確認します。

    > [!NOTE]
    > Visual Studio では、ブラウザーでアプリが自動的に起動しない場合があります。 この時点から API をテストするには [Postman](https://www.postman.com/downloads) を使用します。

1. 新しい **[[Postman]](https://www.postman.com/downloads)** タブで、要求を **[GET]** に設定し、以下のアドレスを入力します。

    ```bash
    https://localhost:5001/api/notifications
    ```

    > [!NOTE]
    > localhost アドレスは、**Properties** > **launchSettings.json** の **applicationUrl** の値と一致している必要があります。 既定値は `https://localhost:5001;http://localhost:5000` ですが、これは 404 応答を受信するかどうかを確認するためのものです。

1. 「[API キーを使用してクライアントを認証する](#authenticate-clients-using-an-api-key-optional)」セクションを完了した場合は、**apikey** 値を含めるように要求ヘッダーを構成してください。

   | Key                            | 値                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. **[送信]** ボタンをクリックします。

    > [!NOTE]
    > **JSON** コンテンツを含む **200 OK** 状態が表示されます。
    >
    > **SSL 証明書の検証**の警告が表示された場合は、 **[設定]** で SSL 証明書の検証を要求する **[Postman](https://www.postman.com/downloads)** 設定をオフに切り替えることができます。

1. テンプレート クラスのメソッドを次のコードに置き換えます。

    ```csharp
    [HttpPut]
    [Route("installations")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> UpdateInstallation(
        [Required]DeviceInstallation deviceInstallation)
    {
        var success = await _notificationService
            .CreateOrUpdateInstallationAsync(deviceInstallation, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpDelete()]
    [Route("installations/{installationId}")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<ActionResult> DeleteInstallation(
        [Required][FromRoute]string installationId)
    {
        var success = await _notificationService
            .DeleteInstallationByIdAsync(installationId, CancellationToken.None);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpPost]
    [Route("requests")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> RequestPush(
        [Required]NotificationRequest notificationRequest)
    {
        if ((notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
            (!notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Text)))
            return new BadRequestResult();

        var success = await _notificationService
            .RequestNotificationAsync(notificationRequest, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }
    ```

### <a name="create-the-api-app"></a>API アプリの作成

ここでは、バックエンド サービスをホストするために [Azure App Service](https://docs.microsoft.com/azure/app-service/) で [API アプリ](https://azure.microsoft.com/services/app-service/api/)を作成します。  

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[リソースの作成]** をクリックし、**API アプリ**を検索して選択し、 **[作成]** をクリックします。

1. 次のフィールドを更新してから、 **[作成]** をクリックします。

    **[アプリ名]:**  
    **API アプリ**のグローバルに一意の名前を入力します。

    **サブスクリプション:**  
    通知ハブを作成したのと同じターゲット **[サブスクリプション]** を選択します。

    **[リソース グループ]:**  
    通知ハブを作成したのと同じ **[リソース グループ]** を選択します。

    **[App Service プラン/場所]:**  
    新しい **App Service プラン**を作成します。  

    > [!NOTE]
    > 既定のオプションから **SSL** サポートを含むプランに変更します。 そうでない場合は、**http** 要求がブロックされないように、モバイル アプリを操作するときに適切な手順を実行する必要があります。

    **Application Insights:**  
    提案されたオプションをそのまま使用するか (その名前を使用して新しいリソースが作成されます)、または既存のリソースを選択します。

1. **API アプリ**がプロビジョニングされたら、そのリソースに移動します。

1. **[概要]** の上部にある **[要点]** サマリーの **[URL]** プロパティをメモしておきます。 この URL は、後で使用する*バックエンド エンドポイント*です。

    > [!NOTE]
    > URL は、前に指定した API アプリ名を `https://<app_name>.azurewebsites.net` という形式で使用します。

1. ( **[設定]** の) リストから **[構成]** を選択します。  

1. 以下の各設定について、 **[新しいアプリケーション設定]** をクリックして、 **[名前]** と **[値]** を入力してから、 **[OK]** をクリックします。

   | 名前                               | 値                          |
   | ---------------------------------- | ------------------------------ |
   | `Authentication:ApiKey`            | <api_key_value>                |
   | `NotificationHub:Name`             | <hub_name_value>               |
   | `NotificationHub:ConnectionString` | <hub_connection_string_value>  |

   > [!NOTE]
   > これらは、以前にユーザー設定で定義したものと同じ設定です。 それをコピーすることができます。 **Authentication:ApiKey** 設定は、「[API キーを使用してクライアントを認証する](#authenticate-clients-using-an-api-key-optional)」セクションを完了した場合にのみ必要です。 運用環境のシナリオでは、[Azure KeyVault](https://azure.microsoft.com/services/key-vault) などのオプションを参照できます。 この場合、わかりやすくするためにアプリケーション設定として追加されています。

1. すべてのアプリケーション設定が追加されたら、 **[保存]** 、 **[続行]** の順にクリックします。

### <a name="publish-the-backend-service"></a>バックエンド サービスの発行

次に、このアプリを API アプリにデプロイして、すべてのデバイスからアクセスできるようにします。  

1. 構成を **[デバッグ]** から **[リリース]** に変更します (まだ行っていない場合)。

1. **PushDemoApi** プロジェクトを **Control** + **クリック**し、 **[発行]** メニューから **[Azure への発行...]** を選択します。

1. メッセージが表示されたら、認証フローに従います。 前の「[API アプリの作成](#create-the-api-app)」セクションで使用したアカウントを使用します。

1. 発行先としてリストから以前に作成した **Azure App Service API アプリ**を選択し、 **[発行]** をクリックします。

ウィザードの完了後に、Azure にアプリを発行してからアプリを開きます。 まだ行っていない場合は、**URL** をメモしておきます。 この URL は、後で使用する*バックエンド エンドポイント*です。

### <a name="validating-the-published-api"></a>発行された API の検証

1. **[[Postman]](https://www.postman.com/downloads)** で新しいタブを開いて要求を **[POST]** に設定し、以下のアドレスを入力します。 プレースホルダーを、前の「[バックエンド サービスの発行](#publish-the-backend-service)」セクションでメモしたベース アドレスに置き換えます。

    ```csharp
    https://<app_name>.azurewebsites.net/api/notifications/installations
    ```

    > [!NOTE]
    > ベース アドレスは、``https://<app_name>.azurewebsites.net/`` の形式にする必要があります。

1. 「[API キーを使用してクライアントを認証する](#authenticate-clients-using-an-api-key-optional)」セクションを完了した場合は、**apikey** 値を含めるように要求ヘッダーを構成してください。

   | Key                            | 値                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. **[Body]** に **[raw]** オプションを選択し、書式オプションの一覧から **[JSON]** を選択します。次に、いくつかのプレースホルダー **JSON** コンテンツを含めます。

    ```json
    {}
    ```

1. **[送信]** をクリックします。

    > [!NOTE]
    > サービスから **400 Bad Request** 状態が返されます。

1. もう一度手順 1 ～ 4 を実行しますが、今度は要求エンドポイントを指定して、同じ **400 Bad Request** 応答を受信することを確認します。

    ```bash
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

> [!NOTE]
> 有効な要求データを使用して API をテストすることはできません。これは、クライアント モバイル アプリからのプラットフォーム固有の情報が必要になるためです。
