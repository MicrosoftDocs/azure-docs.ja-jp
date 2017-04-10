---
title: "Azure Notification Hubs と Bing の空間データを使用したジオフェンス型プッシュ通知 | Microsoft Docs"
description: "このチュートリアルでは、Azure Notification Hubs と Bing の空間データを使用して所在に応じたプッシュ通知を行う方法について説明します。"
services: notification-hubs
documentationcenter: windows
keywords: "プッシュ通知,プッシュ通知"
author: dend
manager: yuaxu
editor: dend
ms.assetid: f41beea1-0d62-4418-9ffc-c9d70607a1b7
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/31/2016
ms.author: dendeli
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b2a84e0479aac9ded08bb64e1ea20ddee6636cce


---
# <a name="geo-fenced-push-notifications-with-azure-notification-hubs-and-bing-spatial-data"></a>Azure Notification Hubs と Bing の空間データを使用したジオフェンス型プッシュ通知
> [!NOTE]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02)を参照してください。
> 
> 

このチュートリアルでは、ユニバーサル Windows プラットフォーム アプリケーション内からの利用を前提に、Azure Notification Hubs と Bing の空間データを使用して、所在に応じたプッシュ通知を行う方法について説明します。

## <a name="prerequisites"></a>前提条件
まず、前提条件として以下のソフトウェアとサービスがすべて揃っていることを確認してください。

* [Visual Studio 2015 Update 1](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) 以降 ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409) も使用可)。 
* 最新バージョンの [Azure SDK](https://azure.microsoft.com/downloads/)。 
* [Bing マップ デベロッパー センター アカウント](https://www.bingmapsportal.com/)。アカウントの作成は無料です。作成したアカウントは Microsoft アカウントに関連付けることができます。 

## <a name="getting-started"></a>Getting Started (概要)
最初にプロジェクトを作成しましょう。 Visual Studio で、 **[空白のアプリ (ユニバーサル Windows)]**タイプの新しいプロジェクトを起動します。

![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

プロジェクトを作成したら、アプリ自体のテスト ハーネスが必要です。 ジオフェンス インフラストラクチャを構成するさまざまな要素を設定していきましょう。 ここでは Bing サービスを使用するので、そのパブリック REST API エンドポイントを介して、特定の位置の領域を照会することができます。

    http://spatial.virtualearth.net/REST/v1/data/

これを利用するには、次のパラメーターを指定する必要があります。

* **データ ソース ID** と**データ ソース名** – Bing マップ API では、さまざまな分類のメタデータ (位置情報、企業の営業時間など) がデータ ソースに格納されます。 詳細については、こちらを参照してください。 
* **エンティティ名** – 通知の基準点として使用するエンティティです。 
* **Bing マップ API キー** – これは、先ほど Bing デベロッパー センター アカウントを作成したときに取得したキーです。

では、上に挙げた各要素の設定について詳しく見ていきます。

## <a name="setting-up-the-data-source"></a>データ ソースの設定
データ ソースの設定は、Bing マップ デベロッパー センターで行うことができます。 最上部のナビゲーション バーにある **[Data sources (データ ソース)]** をクリックし、**[Manage Data Sources (データ ソースの管理)]** を選択します。

![](./media/notification-hubs-geofence/bing-maps-manage-data.png)

過去に Bing マップ API を使用したことがない場合、おそらくデータ ソースは存在しません。データ ソースへのデータのアップロード ボタンをクリックして新しく作成してください。 すべての必須フィールドに必要事項を入力します。

![](./media/notification-hubs-geofence/bing-maps-create-data.png)

データ ファイルとは何でしょうか。また、何をアップロードすればよいのでしょうか。その点が少しわかりにくいかもしれません。 このテストでは、サンフランシスコの埠頭エリアを囲むサンプルをパイプを使って指定します。

    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))

このコードは、次のエンティティを表しています。

![](./media/notification-hubs-geofence/bing-maps-geofence.png)

単に上の文字列をコピーして新しいファイルに貼り付け、 **NotificationHubsGeofence.pipe**という名前で保存して、Bing デベロッパー センターにアップロードしてください。

> [!NOTE]
> **[マスター キー]** に、**[クエリ キー]** とは異なる新しいキーを指定するよう求められる場合があります。 ダッシュボードで新しいキーを作成して、データ ソースのアップロード ページを更新してください。
> 
> 

データ ファイルをアップロードしたら、データ ソースを発行する必要があります。 

先ほどと同様、**[Manage Data Sources (データ ソースの管理)]** に移動し、該当するデータ ソースをリストから探して、**[Actions (アクション)]** 列の **[Publish (発行)]** をクリックします。 すぐに **[発行されたデータ ソース]** タブにデータ ソースが表示されます。

![](./media/notification-hubs-geofence/bing-maps-published-data.png)

**[編集]**をクリックすると、今取り込んだ場所がひとめで確認できます。

![](./media/notification-hubs-geofence/bing-maps-data-details.png)

この時点では、作成したジオフェンスの境界がポータルに表示されません。指定した場所が、適切に収まっていることを確認するだけでかまいません。

これで、データ ソースの要件がすべて揃いました。 API 呼び出しに使用する要求 URL について詳しい情報を確認するには、Bing マップ デベロッパー センターで **[Data sources (データ ソース)]** をクリックし、**[Data Source Information (データ ソース情報)]** を選択します。

![](./media/notification-hubs-geofence/bing-maps-data-info.png)

ここで必要になるのは、 **[クエリの URL]** です。 このエンドポイントに対しクエリを実行することで、デバイスが特定の位置の境界内に存在するかどうかをリアルタイムでチェックすることができます。 このチェックを実行するために必要な操作は、クエリの URL に次のパラメーターを追加して GET 呼び出しを実行するだけです。

    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY

たとえば、デバイスから取得した目標地点を指定すると、その地点がジオフェンスの範囲内にあるかどうかが、Bing Maps によって自動的に計算されます。 ブラウザー (または cURL) から要求を実行すると、標準の JSON 形式の応答が返されます。

![](./media/notification-hubs-geofence/bing-maps-json.png)

この応答が返されるのは、実際にその地点が、指定された境界の範囲内にあるときだけです。 境界の範囲内になければ、空の **結果** のバケットが返されます。

![](./media/notification-hubs-geofence/bing-maps-nores.png)

## <a name="setting-up-the-uwp-application"></a>UWP アプリケーションの設定
データ ソースの準備が整ったら、先ほど立ち上げた UWP アプリケーションの作業に進みます。

まず、アプリケーションに対して位置情報サービスを有効にする必要があります。 そのためには、**ソリューション エクスプローラー**で `Package.appxmanifest` ファイルをダブルクリックします。

![](./media/notification-hubs-geofence/vs-package-manifest.png)

パッケージのプロパティ タブが表示されたら、**[機能]** をクリックし、**[場所]** をオンにします。

![](./media/notification-hubs-geofence/vs-package-location.png)

位置情報の機能を宣言したので、`Core` という名前の新しいフォルダーをソリューションに作成し、そこに新しいファイル (`LocationHelper.cs`) を追加します。

![](./media/notification-hubs-geofence/vs-location-helper.png)

この時点では、 `LocationHelper` クラスそのものは、System API を介してユーザーの位置情報を取得するだけの、ごく基本的な処理内容となっています。

    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }

UWP アプリでユーザーの位置情報を取得する方法について詳しくは、公式の [MSDN ドキュメント](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx)をご覧ください。

位置情報の取得機能が正しく動作することを確認するために、メイン ページ (`MainPage.xaml.cs`) のコードを開いてください。 `MainPage` コンストラクターに、`Loaded` イベントの新しいイベント ハンドラーを作成します。

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

イベント ハンドラーには、次のコードを実装します。

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }

ハンドラーを async として宣言していることに注目してください。`GetCurrentLocation` は待機可能であるため、非同期コンテキストで実行する必要があります。 また、位置情報が null の状況も考えられるため (位置情報サービスが無効になっている、位置情報へのアクセスをアプリケーションが拒否されたなど)、そのような状況を null チェックで適切に処理する必要があります。

アプリケーションを実行します。 位置情報へのアクセスを許可してください。

![](./media/notification-hubs-geofence/notification-hubs-location-access.png)

アプリケーションの起動後、 **出力** ウィンドウに座標が表示されます。

![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

これで、位置情報取得が正しく機能していることを確認できたので、Loaded のテスト イベント ハンドラーは不要です。削除してかまいません。

次に、位置情報の変化をキャプチャします。 そこで、`LocationHelper` クラスに戻って、`PositionChanged` のイベント ハンドラーを追加しましょう。

    geolocator.PositionChanged += Geolocator_PositionChanged;

実装したコードによって、該当する位置の座標が **出力** ウィンドウに表示されます。

    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }

## <a name="setting-up-the-backend"></a>バックエンドの設定
[GitHub から .NET バックエンド サンプル](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers)をダウンロードします。 ダウンロードが完了したら、`NotifyUsers` フォルダーを開き、`NotifyUsers.sln` ファイルを開きます。

`AppBackend` プロジェクトを **スタートアップ プロジェクト** として設定し、起動します。

![](./media/notification-hubs-geofence/vs-startup-project.png)

このプロジェクトは、ターゲット デバイスにプッシュ通知を送信するように、あらかじめ構成されています。必要な設定は 2 つだけです。通知ハブの接続文字列を適切な文字列に変更することと、ユーザーがジオフェンス内に入ったときにだけ通知を送信するための境界 ID を追加することです。

接続文字列を構成するには、`Models` フォルダーの `Notifications.cs` を開きます。 `NotificationHubClient.CreateClientFromConnectionString` 関数には、通知ハブの情報を含める必要があります。この情報は、[Azure Portal](https://portal.azure.com) の **[設定]** の **[アクセス ポリシー]** ブレードで確認できます。 更新した構成ファイルを保存します。

次に、Bing マップ API から返される結果のモデルを作成する必要があります。 これを簡単に行う方法があります。まず、`Models` フォルダーを右クリックし、**[追加]** > **[クラス]** の順にクリックしてください。 これに `GeofenceBoundary.cs` という名前を付けます。 その後、最初のセクションで取り上げた API の応答から JSON をコピーし、Visual Studio で **[編集]** > **[形式を選択して貼り付け]** > **[JSON をクラスとして貼り付ける]** の順に選択します。 

こうすることでオブジェクトが、その意図に従って適切に逆シリアル化されます。 これで次のようなクラスが完成しました。

    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }

次に、 `Controllers` > `NotificationsController.cs`を開きます。 ターゲットの緯度と経度に対応するため、Post の呼び出しを微調整する必要があります。 そこで、関数のシグネチャに `latitude` と `longitude` という 2 つの文字列を追加します。

    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)

プロジェクト内に、`ApiHelper.cs` という新しいクラスを作成します。Bing に接続して特定の地点と境界の交わりをチェックする際に、このクラスを使用します。 以下に示したのは、`IsPointWithinBounds` 関数の実装例です。

    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

> [!NOTE]
> API エンドポイントは必ず、先ほど Bing デベロッパー センターから取得したクエリ URL に置き換えてください (API キーも同様)。 
> 
> 

クエリに対する結果が存在する場合、指定した地点がジオフェンスの境界範囲内に存在することを意味します。つまり、戻り値は `true` となります。 結果が存在しない場合、その地点は、照会した枠の外にあるということです。この場合、戻り値は `false` となります。

`NotificationsController.cs`に戻り、枠の内側にあるかどうかの条件判定を switch ステートメントの直前に追加します。

    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }

これで、指定した地点が境界の範囲内に存在するときにだけ通知が送信されます。

## <a name="testing-push-notifications-in-the-uwp-app"></a>UWP アプリでのプッシュ通知のテスト
いよいよ、UWP アプリに戻って通知のテストを実行します。 `LocationHelper` クラスに、`SendLocationToBackend` という新しい関数を作成します。

    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }

> [!NOTE]
> `POST_URL` は、前のセクションで作成したデプロイ済みの Web アプリケーションのアドレスに置き換えてください。 差し当たりローカルで実行してもかまいませんが、パブリック バージョンをデプロイするときには、外部のプロバイダーでホストする必要があります。
> 
> 

ここで、プッシュ通知に使用する UWP アプリを登録しましょう。 Visual Studio で、**[プロジェクト]** > **[ストア]** > **[アプリケーションをストアと関連付ける]** の順にクリックします。

![](./media/notification-hubs-geofence/vs-associate-with-store.png)

開発者アカウントにサインインしたら、既存のアプリを選択するか、または新しくアプリを作成してそこにパッケージを関連付けてください。 

デベロッパー センターにアクセスして、先ほど作成したアプリを開きます。 **[Services (サービス)]** > **[Push Notifications (プッシュ通知)]** > **[Live Services site (Live サービス サイト)]** の順にクリックします。

![](./media/notification-hubs-geofence/ms-live-services.png)

このサイトで、**アプリケーションのシークレット**と**パッケージ SID** をメモします。 どちらも Azure Portal で必要となります。通知ハブを開いて **[設定]** > **[Notification Services]** > **[Windows (WNS)]** の順にクリックし、必要なフィールドにその情報を入力してください。

![](./media/notification-hubs-geofence/notification-hubs-wns.png)

**[Save]**をクリックします。

**ソリューション エクスプローラー**で **[参照設定]** を右クリックし、**[NuGet パッケージの管理]** を選択します。 **Microsoft Azure Service Bus マネージ ライブラリ**への参照を追加する必要があります。`WindowsAzure.Messaging.Managed` を探してプロジェクトに追加してください。

![](./media/notification-hubs-geofence/vs-nuget.png)

テストを行うために、もう一度 `MainPage_Loaded` イベント ハンドラーを作成し、次のコード スニペットを追加してください。

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }

このコードによってアプリが通知ハブに登録されます。 これで準備が整いました。 

`LocationHelper` の `Geolocator_PositionChanged` ハンドラーに、位置情報が常にジオフェンス内となるようなテスト コードを追加してみましょう。

    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");

実際の座標 (開発時点で境界の範囲内にいることはまずありません) を渡しているわけではなく、あらかじめ定義したテスト値を使用しているため、更新時に通知が表示されます。

![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

## <a name="whats-next"></a>次の手順
通常、このソリューションを本番環境で使用するためには、以上の手順に加え、2 つの作業が必要となります。

第一に、ジオフェンスを動的に変更するためのしくみが必要です。 既存のデータ ソース内に新しい境界をアップロードできるように、Bing API に関して追加作業が必要となります。 この点について詳しくは、 [Bing 空間データ サービス API のドキュメント](https://msdn.microsoft.com/library/ff701734.aspx) をご覧ください。

次に、適切な参加者に対して確実に通知を配信するために、 [タグ付け](notification-hubs-tags-segment-push-message.md)によってターゲットを指定する場合があります。

ここで紹介したソリューションのシナリオは多様なターゲット プラットフォームを想定しており、システム固有の機能にジオフェンスを限定することはしていません。 しかし、ユニバーサル Windows プラットフォームには、 [細かい設定なしにジオフェンスを検出](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence)する機能が備わっています。

Notification Hubs の機能について詳しくは、 [ドキュメント ポータル](https://azure.microsoft.com/documentation/services/notification-hubs/)をご覧ください。




<!--HONumber=Dec16_HO2-->


