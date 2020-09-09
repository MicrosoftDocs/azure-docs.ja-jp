---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: fc479522b3fd436ff02ff6b8985bdeddb66da90a
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448716"
---
### <a name="create-the-xamarinforms-solution"></a>Xamarin.Forms ソリューションを作成する

1. **Visual Studio** で、**空のフォーム アプリ**をテンプレートとして使用し、 **[プロジェクト名]** に「*PushDemo*」と入力して新しい **Xamarin. Forms** ソリューションを作成します。

    > [!NOTE]
    > **[Configure your Blank Forms App] (空のフォームアプリの構成)** ダイアログで、**組織 ID** が前に使用した値と一致していること、およびターゲットとして **Android** と **iOS** の両方のチェック ボックスがオンになっていることを確認します。

1. *PushDemo* ソリューションを **Control** + **クリック**し、 **[Update NuGet Packages] (NuGet パッケージの更新)** を選択します。
1. *PushDemo* ソリューションを **Control** + **クリック**し、 **[NuGet パッケージの管理...]** を選択します。
1. **Newtonsoft.Json** を探し、そのチェック ボックスがオンになっていることを確認します。
1. **[パッケージの追加]** をクリックし、ライセンス条項への同意を求めるメッセージが表示されたら、 **[同意する]** をクリックします。
1. 各ターゲット プラットフォームでアプリをビルドして実行し (**Command** + **Enter** キー)、テンプレート化されたアプリがデバイスで実行されることをテストします。

### <a name="implement-the-cross-platform-components"></a>クロスプラットフォーム コンポーネントを実装する

1. **PushDemo** プロジェクトを **Control** + **クリック**し、 **[追加]** メニューから **[新しいフォルダー]** を選択します。次に、 **[フォルダー名]** として「*Models*」を使用して **[追加]** をクリックします。

1. **Models** フォルダーを **Control** + **クリック**し、 **[追加]** メニューから **[新しいファイル...]** を選択します。

1. **[全般]**  >  **[空のクラス]** と選択し、「*DeviceInstallation.cs*」と入力してから、次の実装を追加します。

    ```csharp
    using System.Collections.Generic;
    using Newtonsoft.Json;

    namespace PushDemo.Models
    {
        public class DeviceInstallation
        {
            [JsonProperty("installationId")]
            public string InstallationId { get; set; }

            [JsonProperty("platform")]
            public string Platform { get; set; }

            [JsonProperty("pushChannel")]
            public string PushChannel { get; set; }

            [JsonProperty("tags")]
            public List<string> Tags { get; set; } = new List<string>();
        }
    }
    ```

1. 次の実装を使用して、*PushDemoAction.cs* という名前の**空の列挙型**を **Models** フォルダーに追加します。

    ```csharp
    namespace PushDemo.Models
    {
        public enum PushDemoAction
        {
            ActionA,
            ActionB
        }
    }
    ```

1. **PushDemo.iOS** プロジェクトに *Services* という名前の新しいフォルダーを追加してから、以下の実装を使用してそのフォルダーに *ServiceContainer.cs* という名前の**空のクラス**を追加します。

     ```csharp
    using System;
    using System.Collections.Generic;

    namespace PushDemo.Services
    {
        public static class ServiceContainer
        {
            static readonly Dictionary<Type, Lazy<object>> services
                = new Dictionary<Type, Lazy<object>>();

            public static void Register<T>(Func<T> function)
                => services[typeof(T)] = new Lazy<object>(() => function());

            public static T Resolve<T>()
                => (T)Resolve(typeof(T));

            public static object Resolve(Type type)
            {
                {
                    if (services.TryGetValue(type, out var service))
                        return service.Value;

                    throw new KeyNotFoundException($"Service not found for type '{type}'");
                }
            }
        }
    }
    ```

    > [!NOTE]
    > これは、[XamCAT](https://github.com/xamcat/mobcat-library) リポジトリにある [ServiceContainer](https://github.com/xamcat/mobcat-library/blob/master/MobCAT/ServiceContainer.cs) クラスを簡略化したバージョンです。 これは、軽量な IoC (制御の反転) コンテナーとして使用されます。

1. *IDeviceInstallationService.cs* という名前の**空のインターフェイス**を **Services** フォルダーに追加してから、次のコードを追加します。

    ```csharp
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IDeviceInstallationService
        {
            string Token { get; set; }
            bool NotificationsSupported { get; }
            string GetDeviceId();
            DeviceInstallation GetDeviceInstallation(params string[] tags);
        }
    }
    ```

    > [!NOTE]
    > このインターフェイスは、プラットフォーム固有の機能を提供し、バックエンド サービスが必要とする **DeviceInstallation** 情報を提供するため、後で各ターゲットによって実装され、ブートストラップされます。

1. *INotificationRegistrationService.cs* という名前の別の**空のインターフェイス**を **Services** フォルダーに追加してから、次のコードを追加します。  

    ```csharp
    using System.Threading.Tasks;

    namespace PushDemo.Services
    {
        public interface INotificationRegistrationService
        {
            Task DeregisterDeviceAsync();
            Task RegisterDeviceAsync(params string[] tags);
            Task RefreshRegistrationAsync();
        }
    }
    ```

    > [!NOTE]
    > これにより、クライアントとバックエンド サービス間の相互作用が処理されます。

1. *INotificationActionService.cs* という名前の別の**空のインターフェイス**を **Services** フォルダーに追加してから、次のコードを追加します。  

    ```csharp
    namespace PushDemo.Services
    {
        public interface INotificationActionService
        {
            void TriggerAction(string action);
        }
    }
    ```

    > [!NOTE]
    > これは、通知アクションの処理を一元化する簡単なメカニズムとして使用されます。

1. 次の実装を使用して、*INotificationActionService* から派生する *IPushDemoNotificationActionService.cs* という名前の**空のインターフェイス**を **Services** フォルダーに追加します。  

    ```csharp
    using System;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IPushDemoNotificationActionService : INotificationActionService
        {
            event EventHandler<PushDemoAction> ActionTriggered;
        }
    }
    ```

    > [!NOTE]
    > この型は **PushDemo** アプリケーションに固有のもので、**PushDemoAction** 列挙型を使用して、厳密に型指定された方法でトリガーされるアクションを識別します。

1. 以下のコードにより、*NotificationRegistrationService.cs* という名前の**空のクラス**を **Services** フォルダーに追加し、*INotificationRegistrationService* を実装します。

    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using PushDemo.Models;
    using Xamarin.Essentials;

    namespace PushDemo.Services
    {
        public class NotificationRegistrationService : INotificationRegistrationService
        {
            const string RequestUrl = "api/notifications/installations";
            const string CachedDeviceTokenKey = "cached_device_token";
            const string CachedTagsKey = "cached_tags";

            string _baseApiUrl;
            HttpClient _client;
            IDeviceInstallationService _deviceInstallationService;

            public NotificationRegistrationService(string baseApiUri, string apiKey)
            {
                _client = new HttpClient();
                _client.DefaultRequestHeaders.Add("Accept", "application/json");
                _client.DefaultRequestHeaders.Add("apikey", apiKey);

                _baseApiUrl = baseApiUri;
            }

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService = ServiceContainer.Resolve<IDeviceInstallationService>());

            public async Task DeregisterDeviceAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                if (cachedToken == null)
                    return;

                var deviceId = DeviceInstallationService?.GetDeviceId();

                if (string.IsNullOrWhiteSpace(deviceId))
                    throw new Exception("Unable to resolve an ID for the device.");

                await SendAsync(HttpMethod.Delete, $"{RequestUrl}/{deviceId}")
                    .ConfigureAwait(false);

                SecureStorage.Remove(CachedDeviceTokenKey);
                SecureStorage.Remove(CachedTagsKey);
            }

            public async Task RegisterDeviceAsync(params string[] tags)
            {
                var deviceInstallation = DeviceInstallationService?.GetDeviceInstallation(tags);

                await SendAsync<DeviceInstallation>(HttpMethod.Put, RequestUrl, deviceInstallation)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedDeviceTokenKey, deviceInstallation.PushChannel)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedTagsKey, JsonConvert.SerializeObject(tags));
            }

            public async Task RefreshRegistrationAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                var serializedTags = await SecureStorage.GetAsync(CachedTagsKey)
                    .ConfigureAwait(false);

                if (string.IsNullOrWhiteSpace(cachedToken) ||
                    string.IsNullOrWhiteSpace(serializedTags) ||
                    string.IsNullOrWhiteSpace(DeviceInstallationService.Token) ||
                    cachedToken == DeviceInstallationService.Token)
                    return;

                var tags = JsonConvert.DeserializeObject<string[]>(serializedTags);

                await RegisterDeviceAsync(tags);
            }

            async Task SendAsync<T>(HttpMethod requestType, string requestUri, T obj)
            {
                string serializedContent = null;

                await Task.Run(() => serializedContent = JsonConvert.SerializeObject(obj))
                    .ConfigureAwait(false);

                await SendAsync(requestType, requestUri, serializedContent);
            }

            async Task SendAsync(
                HttpMethod requestType,
                string requestUri,
                string jsonRequest = null)
            {
                var request = new HttpRequestMessage(requestType, new Uri($"{_baseApiUrl}{requestUri}"));

                if (jsonRequest != null)
                    request.Content = new StringContent(jsonRequest, Encoding.UTF8, "application/json");

                var response = await _client.SendAsync(request).ConfigureAwait(false);

                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

    > [!NOTE]
    > **apiKey** 引数が必要なのは、「[API キーを使用してクライアントを認証する](#authenticate-clients-using-an-api-key-optional)」セクションを完了した場合のみです。

1. 以下のコードにより、*PushDemoNotificationActionService.cs* という名前の**空のクラス**を **Services** フォルダーに追加し、*IPushDemoNotificationActionService* を実装します。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public class PushDemoNotificationActionService : IPushDemoNotificationActionService
        {
            readonly Dictionary<string, PushDemoAction> _actionMappings = new Dictionary<string, PushDemoAction>
            {
                { "action_a", PushDemoAction.ActionA },
                { "action_b", PushDemoAction.ActionB }
            };

            public event EventHandler<PushDemoAction> ActionTriggered = delegate { };

            public void TriggerAction(string action)
            {
                if (!_actionMappings.TryGetValue(action, out var pushDemoAction))
                    return;

                List<Exception> exceptions = new List<Exception>();

                foreach (var handler in ActionTriggered?.GetInvocationList())
                {
                    try
                    {
                        handler.DynamicInvoke(this, pushDemoAction);
                    }
                    catch (Exception ex)
                    {
                        exceptions.Add(ex);
                    }
                }

                if (exceptions.Any())
                    throw new AggregateException(exceptions);
            }
        }
    }
    ```

1. 次の実装で、*Config.cs* という名前の**空のクラス**を **PushDemo** プロジェクトに追加します。  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            public static string ApiKey = "API_KEY";
            public static string BackendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
        }
    }
    ```

    > [!NOTE]
    > これは、機密をソース管理の対象外にしておくための簡単な方法として利用されます。 これらの値は、自動化されたビルドの一部として置き換えることも、ローカルの部分クラスを使用してオーバーライドすることもできます。 これは次の手順で実行します。
    >
    > **ApiKey** フィールドが必要なのは、「[API キーを使用してクライアントを認証する](#authenticate-clients-using-an-api-key-optional)」セクションを完了した場合のみです。

1. 次の実装で、今回は *Config.local_secrets.cs* という名前の別の**空のクラス**を、**PushDemo** プロジェクトに追加します。  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            static Config()
            {
                ApiKey = "<your_api_key>";
                BackendServiceEndpoint = "<your_api_app_url>";
            }
        }
    }
    ```

    > [!NOTE]
    > プレースホルダーの値は、実際のものに置き換えます。 バックエンド サービスを構築したときに、これらについてのメモを作成したはずです。 **API アプリ**の URL は ``https://<api_app_name>.azurewebsites.net/`` である必要があります。 このファイルがコミットされないように、お使いの gitignore ファイルに必ず ``*.local_secrets.*`` を追加してください。
    >
    > **ApiKey** フィールドが必要なのは、「[API キーを使用してクライアントを認証する](#authenticate-clients-using-an-api-key-optional)」セクションを完了した場合のみです。

1. 次の実装で、*Bootstrap.cs* という名前の**空のクラス**を **PushDemo** プロジェクトに追加します。  

    ```csharp
    using System;
    using PushDemo.Services;

    namespace PushDemo
    {
        public static class Bootstrap
        {
            public static void Begin(Func<IDeviceInstallationService> deviceInstallationService)
            {
                ServiceContainer.Register(deviceInstallationService);

                ServiceContainer.Register<IPushDemoNotificationActionService>(()
                    => new PushDemoNotificationActionService());

                ServiceContainer.Register<INotificationRegistrationService>(()
                    => new NotificationRegistrationService(
                        Config.BackendServiceEndpoint,
                        Config.ApiKey));
            }
        }
    }
    ```

    > [!NOTE]
    > **Begin** メソッドは、アプリがプラットフォーム固有の **IDeviceInstallationService** の実装を渡し始めたときに、各プラットフォームによって呼び出されます。
    >
    > **NotificationRegistrationService** の **apiKey** コンストラクターが必要なのは、「[API キーを使用してクライアントを認証する](#authenticate-clients-using-an-api-key-optional)」セクションを完了した場合のみです。

### <a name="implement-the-cross-platform-ui"></a>クロスプラットフォーム UI を実装する

1. **PushDemo** プロジェクトで、**MainPage.xaml** を開き、次によって **StackLayout** コントロールを置き換えます。

    ```xml
    <StackLayout VerticalOptions="EndAndExpand"  
                 HorizontalOptions="FillAndExpand"
                 Padding="20,40">
        <Button x:Name="RegisterButton"
                Text="Register"
                Clicked="RegisterButtonClicked" />
        <Button x:Name="DeregisterButton"
                Text="Deregister"
                Clicked="DeregisterButtonClicked" />
    </StackLayout>
    ```

1. 今度は **MainPage.xaml.cs** で、**readonly** のバッキング フィールドを追加して、**INotificationRegistrationService** 実装への参照を格納します。

    ```csharp
    readonly INotificationRegistrationService _notificationRegistrationService;
    ```

1. **MainPage** コンストラクター内で、**ServiceContainer** を使用して **INotificationRegistrationService** 実装を解決し、それを *_notificationRegistrationService_* バッキング フィールドに代入します。

    ```csharp
    public MainPage()
    {
        InitializeComponent();

        _notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>();
    }
    ```

1. 対応する **Register**/**Deregister** メソッドを呼び出す、**RegisterButton** ボタンと **DeregisterButton** ボタンの **Clicked** イベントのイベント ハンドラーを実装します。

    ```csharp
    void RegisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.RegisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device registered"); });

    void DeregisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.DeregisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device deregistered"); });

    void ShowAlert(string message)
        => MainThread.BeginInvokeOnMainThread(()
            => DisplayAlert("PushDemo", message, "OK").ContinueWith((task)
                => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. 今度は **App.xaml.cs** で、次の名前空間が参照されていることを確認します。

    ```csharp
    using PushDemo.Models;
    using PushDemo.Services;
    using Xamarin.Essentials;
    using Xamarin.Forms;
    ```

1. **IPushDemoNotificationActionService** の **ActionTriggered** イベントのためのイベント ハンドラーを実装します。

    ```csharp
    void NotificationActionTriggered(object sender, PushDemoAction e)
        => ShowActionAlert(e);

    void ShowActionAlert(PushDemoAction action)
        => MainThread.BeginInvokeOnMainThread(()
            => MainPage?.DisplayAlert("PushDemo", $"{action} action received", "OK")
                .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. **App** コンストラクター内で、**ServiceContainer** を使用して **IPushNotificationActionService** 実装を解決し、**IPushDemoNotificationActionService** の **ActionTriggered** イベントにサブスクライブします。

    ```csharp
    public App()
    {
        InitializeComponent();

        ServiceContainer.Resolve<IPushDemoNotificationActionService>()
            .ActionTriggered += NotificationActionTriggered;

        MainPage = new MainPage();
    }
    ```

    > [!NOTE]
    > これは単に、プッシュ通知アクションの受信と伝達を示すためのものです。 通常、これらは暗黙のうちに処理されます。たとえば、特定のビューへの移動や、この場合の、ルートの **Page**、**MainPage** を介してアラートを表示するのではなく、一部のデータを更新する場合です。
