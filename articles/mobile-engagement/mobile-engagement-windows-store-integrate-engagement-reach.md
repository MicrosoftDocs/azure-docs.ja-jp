---
title: "Windows ユニバーサル アプリ Reach SDK 統合"
description: "Windows ユニバーサル アプリと Azure Mobile Engagement Reach を統合する方法"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a31ca1d6-856f-4aec-898a-07969ae5f7ec
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8fc1faac88cab0e3d3755fdf16fe3d7188169961
ms.lasthandoff: 11/17/2016


---
# <a name="windows-universal-apps-reach-sdk-integration"></a>Windows ユニバーサル アプリ Reach SDK 統合
このガイドの手順を実行する前に、「 [Windows Phone ユニバーサル エンゲージメント SDK 統合](mobile-engagement-windows-store-integrate-engagement.md) 」の統合手順を実行する必要があります。

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>エンゲージメント Reach SDK を Windows ユニバーサル プロジェクトに組み込む
追加するものは何もありません。 `EngagementReach` のリファレンスとリソースは、既にプロジェクト内に存在します。

> [!TIP]
> プロジェクトの `Resources` フォルダーにあるイメージをカスタマイズできます (特に、既定のエンゲージメント アイコンであるブランド アイコン)。 ユニバーサル アプリでは、共有プロジェクトの `Resources` フォルダーを移動させ、そのコンテンツをアプリ間で共有できますが、`Resources\EngagementConfiguration.xml` ファイルはプラットフォーム依存のため、その既定の場所に維持する必要があります。
> 
> 

## <a name="enable-the-windows-notification-service"></a>Windows 通知サービスを有効にする
### <a name="windows-8x-and-windows-phone-81-only"></a>Windows 8.x および Windows Phone 8.1 のみ
`Package.appxmanifest` ファイルで **Windows 通知サービス** (WNS) を使用するために、`Application UI` で左下のボックスから `All Image Assets` をクリックします。 `Notifications` のボックスの右で、`toast capable` を `(not set)` から `(Yes)` に変更します。

### <a name="all-platforms"></a>すべてのプラットフォーム
アプリを Microsoft アカウントとエンゲージメント プラットフォームと同期させる必要があります。 これには、アカウントを作成するか、 [Windows デベロッパー センター](https://dev.windows.com)にログインする必要があります。 その後に、新しいアプリケーションを作成して SID と秘密キーを検索します。 エンゲージメントのフロントエンドで、[ `native push` ] のアプリ設定に移動し、資格情報を貼り付けます。 その後、プロジェクトを右クリックし、[`store`] と [`Associate App with the Store...`] を選択します。 同期するには、前に作成したアプリケーションを選択する必要があります。

## <a name="initialize-the-engagement-reach-sdk"></a>Engagement Reach SDK を初期化する
`App.xaml.cs` を変更します。

* `InitEngagement` メソッドの `EngagementAgent.Instance.Init` の直後に `EngagementReach.Instance.Init` を挿入します。
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
        EngagementReach.Instance.Init(e);
      }
  
  `EngagementReach.Instance.Init` は専用のスレッドで稼働します。 自分で実行する必要はありません。

> [!NOTE]
> アプリケーションの他の場所でプッシュ通知を使用している場合は、Engagement Reach と [プッシュ チャネルを共有](#push-channel-sharing) する必要があります。
> 
> 

## <a name="integration"></a>統合
Engagement には、アナウンスおよびポーリング用の Reach アプリ内バナーとスポット ビューをアプリケーションに追加するために、オーバーレイ統合と Web ビュー手動統合という 2 つの方法が用意されています。 ただし、2 つの方法を組み合わせて同じページに使用することはお勧めしません。

以下に要約した方法で、2 種類の統合のどちらを選択するか決めることができます。

* ページで既に `EngagementPage` エージェントから継承している場合はオーバーレイ統合を選択できます。この場合、ページ内で `EngagementPage` を `EngagementPageOverlay` に置き換え、`xmlns:engagement="using:Microsoft.Azure.Engagement"` を `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` に置き換えるだけです。
* Reach UI をページ内に正確に配置する必要があるか、別の継承レベルをページに追加することを避ける必要がある場合は、Web ビュー手動統合を選択できます。 

### <a name="overlay-integration"></a>オーバーレイ統合
Engagement オーバーレイは、Reach キャンペーンを表示するために使用される UI 要素をページ内に動的に追加します。 オーバーレイがページのレイアウトに合わない場合は、Web ビュー手動統合の使用を検討する必要があります。

.xaml ファイル内で、`EngagementPage` の参照を `EngagementPageOverlay` に変更します。

* 次の内容を名前空間宣言に追加します。
  
      xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"
* `engagement:EngagementPage` を `engagement:EngagementPageOverlay` に置き換えます。

**EngagementPage を使用する場合:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">

            <!-- Your layout -->
        </engagement:EngagementPage>

**EngagementPageOverlay を使用する場合:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">

            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

次に、.cs ファイル内で `EngagementPage` ではなく `EngagementPageOverlay` にページをタグ付けし、`Microsoft.Azure.Engagement.Overlay` をインポートします。

            using Microsoft.Azure.Engagement.Overlay;

* `EngagementPage` を `EngagementPageOverlay` に置き換えます。

**EngagementPage を使用する場合:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**EngagementPageOverlay を使用する場合:**

            using Microsoft.Azure.Engagement.Overlay;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


Engagement オーバーレイは、レイアウトと 2 つの `WebView` 要素 (バナー用とスポット ビュー用) で構成される `Grid` 要素をページ上部に追加します。

オーバーレイ要素は `EngagementPageOverlay.cs` ファイル内で直接カスタマイズできます。

### <a name="web-views-manual-integration"></a>Web ビュー手動統合
Reach は、バナーとスポット ビューを表示するための 2 つの `WebView` 要素をページから検索します。 必要な作業は、次の例のように、これら 2 つの `WebView` 要素をページ内のどこかに追加することだけです。

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


この例では、 `WebView` 要素がコンテナーのサイズに合うように拡大され、画面の回転やウィンドウ サイズの変更が行われた場合には自動的にサイズが変更されるようになっています。

> [!WARNING]
> `WebView` 要素には、`engagement_notification_content` および `engagement_announcement_content` と同じように名前を付けることが重要です。 これは、Reach が各要素の名前で識別を行うためです。 
> 
> 

## <a name="handle-datapush-optional"></a>データのプッシュを操作する (オプション)
アプリケーションが Reach データのプッシュを受信できるようにするには、EngagementReach クラスの 2 つのイベントを実装する必要があります。

App.xaml.cs 内で、App() コンストラクターに次のコードを追加します。

            EngagementReach.Instance.DataPushStringReceived += (body) =>
            {
              Debug.WriteLine("String data push message received: " + body);
              return true;
            };

            EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
            {
              Debug.WriteLine("Base64 data push message received: " + encodedBody);
              // Do something useful with decodedBody like updating an image view
              return true;
            };

各メソッドのコールバックがブール値を返すことを確認できます。 データのプッシュをディスパッチした後、Engagement はバックエンドにフィードバックを送信します。 コールバックが false を返した場合、 `exit` フィードバックが送信されます。 それ以外の場合は、 `action`になります。 イベントにコールバックが設定されていない場合は、 `drop` フィードバックがエンゲージメントに返されます。

> [!WARNING]
> Engagement は、データのプッシュのフィードバックを複数受信することができません。 複数のハンドラーをイベントに設定する計画がある場合は、最後に送信されたものがフィードバックに相当することに注意してください。 この場合、フロントエンドでフィードバックが混同されるのを避けるために、常に同じ値を返すことをお勧めします。
> 
> 

## <a name="customize-ui-optional"></a>UI をカスタマイズする (オプション)
### <a name="first-step"></a>最初の手順
Reach UI をカスタマイズできるようにします。

そのためには、 `EngagementReachHandler` クラスのサブクラスを作成する必要があります。

**サンプル コード:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

次に、`App()` メソッドに含まれる `App.xaml.cs` クラスのカスタム オブジェクトを使用して、`EngagementReach.Instance.Handler` フィールドの内容を設定します。

**サンプル コード:**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [!NOTE]
> 既定では、エンゲージメントは `EngagementReachHandler` の独自の実装を使用します。
> 自分用の実装を作成する必要はなく、そうする場合でも、すべてのメソッドをオーバーライドする必要はありません。 既定の動作では、Engagement の基本オブジェクトを選択します。
> 
> 

### <a name="web-view"></a>Web ビュー
既定では、Reach は DLL に組み込まれたリソースを使用して、通知とページを表示します。

完全なカスタマイズを可能にするには、Web ビューのみを使用します。 レイアウトをカスタマイズする場合は、リソース ファイルの `EngagementAnnouncement.html` と `EngagementNotification.html` を直接上書きしてください。 Engagement を正常に実行するには、すべてのコードを `<body></body>` の中に含める必要があります。 ただし、タグを `engagement_webview_area`の外部に追加ができます。

また、独自のリソースを使用することも決定できます。

サブクラスの `EngagementReachHandler` メソッドをオーバーライドして、独自のレイアウトを使用するようにエンゲージメントに指示ができますが、組み込まれたエンゲージメントのメカニズムに注意してください。

**サンプル コード:**

            // In your subclass of EngagementReachHandler

            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


既定では、AnnouncementHTML は `ms-appx-web:///Resources/EngagementAnnouncement.html`です。 これは、プッシュ メッセージの内容を設計する html ファイルを示します (Text アナウンス、Web アナウンス、Poll アナウンス)。 AnnouncementName は `engagement_announcement_content`です。 これは、xaml ページの Web ビュー設計の名前です。

NotfificationHTML は `ms-appx-web:///Resources/EngagementNotification.html`です。 これは、プッシュ メッセージの通知を設計する html ファイルを示します。 NotfificationName は `engagement_notification_content`です。 これは、xaml ページの Web ビュー設計の名前です。

### <a name="customization"></a>カスタマイズ
Engagement オブジェクトを保持する場合、希望する通知とアナウンスの Web ビューをカスタマイズできます。 Web ビュー オブジェクトは 3 回記述されていることに注意してください。1 回目は xaml ファイルで、2 回目は .cs ファイルの "setwebview()" メソッドで、3 回目は html ファイルで記述されます。

* xaml ファイルでは、現在のグラフィカル レイアウトの Web ビュー コンポーネントを記述します。
* .cs ファイルでは、"setwebview()" を定義して、2 つの Web ビュー (通知とアナウンス) のディメンションを設定できます。 この機能は、アプリケーションがサイズ変更する場合に非常に効果的です。
* Engagement の html ファイルで、Web ビューのコンテンツ、デザイン、互いの要素間の位置を記述します。

### <a name="launch-message"></a>メッセージを起動する
ユーザーがシステム通知 (トースト) をクリックすると、Engagement がアプリケーションを起動して、プッシュ メッセージの内容をロードし、対応するキャンペーンのページを表示します。

アプリケーションが起動されてからページが表示されるまでの間に、(ネットワークの速度に応じて) 遅延が発生します。

読み込み中であることをユーザーに示すには、進行状況バーや進行状況インジケーターなどの視覚的な情報を提供する必要があります。 Engagement 自身では、それに対応することはできませんが、いくつかのハンドラーを提供します。

コールバックを実装するには、App.xaml.cs の "Public App(){}" に次の内容を追加します。

            /* The application has launched and the content is loading.
             * You should display an indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

            /* The application has finished loading the content and the page
             * is about to be displayed.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

            /* The content has been loaded, but an error has occurred.
             * You can provide an information to the user.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

できれば、`EngagementReach.Instance.Init()` を呼び出す前に、`App.xaml.cs` ファイルの "Public App(){}" メソッドにコールバックを設定します。

> [!TIP]
> 各ハンドラーは、UI スレッドにより呼び出されます。 メッセージボックスや UI 関連のものは、安心して使用できます。
> 
> 

## <a name="a-idpush-channel-sharinga-push-channel-sharing"></a><a id="push-channel-sharing"></a> プッシュ チャネルの共有
アプリケーションで、他の目的でプッシュ通知を使用している場合は、Engagement SDK のプッシュ チャネル共有機能を使用する必要があります。 これはプッシュが実行されないことを防ぐためです。

* Engagement Reach の初期化に独自のプッシュ チャネルを提供できます。 SDK は新しいものを要求する代わりにこれを使用します。

`App.xaml.cs` ファイルの `InitEngagement` メソッドで、Engagement Reach の初期化を自分のプッシュ チャネルで更新します。

    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

* または、単に Reach の初期化後にプッシュ チャネルを使用する場合は、プッシュ チャネルが SDK によって作成された後に取得されるように、Engagement Reach にコールバックを設定することができます。

コールバックは Reach の初期化の **後に** 、任意の場所に設定します。

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>カスタム スキーマのヒント
カスタム スキーマを使用すると、 エンゲージメントのフロントエンドから、エンゲージメント アプリケーションで使用する別の種類の URI を送信できます。 `http, ftp, ...` などの既定のスキーマは Windows により管理されます。デバイスに既定のアプリケーションがインストールされていない場合は、プロンプト ウィンドウが表示されます。 アプリケーションに対してカスタム スキーマを作成することもできます。

カスタム スキーマをアプリケーションで設定する簡単な方法は、`Package.appxmanifest` を開いて、[`Declarations`] パネルに移動することです。 [使用可能な宣言] スクロール ボックスで、[ `Protocol` ] を選択して追加します。 新しいプロトコルの希望する名前を使用して、[ `Name` ] フィールドを編集します。

これらのプロトコルを使用するには、`OnActivated` メソッドを使用して `App.xaml.cs` を編集します。ここで、エンゲージメントの初期化も忘れないでください。

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);

              //TODO design action to do when app is launch

              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;

                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion


