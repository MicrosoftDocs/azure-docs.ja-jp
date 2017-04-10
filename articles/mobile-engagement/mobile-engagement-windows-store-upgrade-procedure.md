---
title: "Windows ユニバーサル アプリ SDK のアップグレード手順"
description: "Azure Mobile Engagement 向け Windows ユニバーサル アプリ SDK のアップグレード手順"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 4c898175-2cd6-43db-b350-bb408332f24d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c7b3441892484cf54e2055e9153858171d738e85
ms.lasthandoff: 11/17/2016


---
# <a name="windows-universal-apps-sdk-upgrade-procedures"></a>Windows ユニバーサル アプリ SDK のアップグレード手順
既にアプリケーションに以前のバージョンの Mobile Engagement を統合してある場合は、SDK をアップグレードするときに、次の点を考慮する必要があります。

SDK の一部のバージョンが不足している場合、いくつかの手順に従う必要があることがあります。 たとえば、0.10.1 から 0.11.0 に移行する場合、まず「0.9.0から 0.10.1」への手順を実行してから「0.10.1 から 0.11.0」への手順を実行する必要があります。

## <a name="from-330-to-340"></a>3.3.0 から 3.4.0 に移行
### <a name="test-logs"></a>テスト ログ
SDK によって生成されるコンソール ログを有効化/無効化/フィルター処理できるようになりました。 これをカスタマイズするには、次の例のように `EngagementAgent.Instance.TestLogEnabled` プロパティを `EngagementTestLogLevel` 列挙型の使用可能な値の 1 つに更新します。

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>リソース
Reach オーバーレイの機能を強化しました。 これは SDK NuGet パッケージのリソースの一部です。

新しいバージョンの SDK にアップグレードする際、リソースのオーバーレイ フォルダーにある既存ファイルを保持するかどうかを選択できます。

* 以前のオーバーレイが機能している、または `WebView` 要素を手動で統合している場合、既存ファイルを保持することで、引き続き使用することができます。 
* 新しいオーバーレイに更新する場合、リソースの `overlay` フォルダー全体を SDK パッケージの新しいフォルダーに置き換えます (UWP アプリ: アップグレード後に %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources から新しいオーバーレイ フォルダーを取得できます)。

> [!WARNING]
> 新しいオーバーレイを使用すると、以前のバージョンに対して行ったすべてのカスタマイズが上書きされます。
> 
> 

## <a name="from-320-to-330"></a>3.2.0 から 3.3.0 に移行
### <a name="resources"></a>リソース
この手順では、カスタマイズされたリソースが問題になります。 SDK 提供のリソース (html、画像、オーバーレイ) をカスタマイズしている場合、アップグレード前にそのリソースをバックアップし、アップグレードしたリソースにカスタマイズを再適用する必要があります。

## <a name="from-310-to-320"></a>3.1.0 から 3.2.0 に移行
### <a name="resources"></a>リソース
この手順では、カスタマイズされたリソースが問題になります。 SDK 提供のリソース (html、画像、オーバーレイ) をカスタマイズしている場合、アップグレード前にそのリソースをバックアップし、アップグレードしたリソースにカスタマイズを再適用する必要があります。

### <a name="webview-integration"></a>Web ビュー統合 を使用しないでください。
今回のバージョンで、さまざまなデバイス フォーム ファクターに合わせた改善が導入されました。 Web ビューの統合が次のようになっていることを確認してください。

XAML の page ():

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

関連付けられた .cs ファイル:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }

              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif

              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }

              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

## <a name="from-200-to-300"></a>2.0.0 から 3.0.0 に移行
### <a name="resources"></a>リソース
この手順では、カスタマイズされたリソースが問題になります。 SDK 提供のリソース (html、画像、オーバーレイ) をカスタマイズしている場合、アップグレード前にそのリソースをバックアップし、アップグレードしたリソースにカスタマイズを再適用する必要があります。

## <a name="from-111-to-200"></a>1.1.1 から 2.0.0 に移行
Azure Mobile Engagement を使用するアプリに Capptain SAS によって提供される Capptain サービスから SDK の統合を移行する方法を次に示します。 

> [!IMPORTANT]
> Capptain と Mobile Engagement は、同じサービスではありません。次の手順では、クライアント アプリケーションを移行する方法についてのみ詳しく説明します。 アプリで SDK を移行しても、データは Capptain サーバーから Mobile Engagement のサーバーに移行されません。
> 
> 

以前のバージョンから移行する場合は、Capptain web サイトをご覧のうえ、まず 1.1.1 に移行し、次の手順を適用してください。

### <a name="nuget-package"></a>NuGet パッケージ
**Capptain.WindowsPhone** を **MicrosoftAzure.MobileEngagement** Nuget パッケージに置き換えます。

### <a name="applying-mobile-engagement"></a>Mobile Engagement の適用
SDK は `Engagement`という用語を使用します。 この変更を一致させるためにプロジェクトをアップグレードする必要があります。

現在の Capptain NuGet パッケージをアンインストールする必要があります。 [Capptain Resources] フォルダー内のすべての変更が削除されることを検討します。 これらのファイルを保持する場合は、コピーを作成します。

そのあと、新しい Microsoft Azure エンゲージメント NuGet パッケージをプロジェクトにインストールします。 [NuGetの Web サイト] または ここのインデックスで直接検索できます。 この操作は、エンゲージメントによって使用されるすべてのリソース ファイルを置換し、プロジェクトの参照に新しいエンゲージメントの DLL を追加します。

Capptain DLL の参照を削除して、プロジェクトの参照をクリーンアップする必要があります。 これを実行しない場合は、Capptain のバージョンが競合してエラーが発生します。

Capptain リソースをカスタマイズした場合、古いファイルの内容をコピーし、新しい Engagement ファイルに貼り付けます。 xaml と cs ファイルの両方を更新する必要があることにご注意ください。

これらの手順が完了したら、新しい Engagement の参照で古い Capptain の参照を置き換える必要があります。

1. すべての Capptain 名前空間の更新が必要です。
   
    移行前:
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    移行後:
   
        using Microsoft.Azure.Engagement;
2. "Capptain" が含まれているすべての Capptain クラスには、"Engagement" が含まれている必要があります。
   
    移行前:
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    移行後:
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. xaml ファイルの Capptain 名前空間と属性も変更します。
   
    移行前:
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    移行後:
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. オーバーレイ ページの変更
   
   > [!IMPORTANT]
   > オーバーレイも変更します。 その新しい名前空間は `Microsoft.Azure.Engagement.Overlay`です。 xaml と cs ファイルの両方で使用する必要があります。 さらに、`CapptainGrid` は `EngagementGrid` と名前が付けられ、`capptain_notification_content` と `capptain_announcement_content` は `engagement_notification_content` と `engagement_announcement_content` と名前が付けられます。
   > 
   > 
   
    オーバーレイについて:
   
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
   
    次のようになります:
   
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>
5. Capptain 画像や HTML ファイルなどその他のリソースについては、"Engagement" を使用して名前が変更されていることにもご注意ください。

### <a name="project-declaration"></a>プロジェクトの宣言
Package.appxmanifest で `File Type Associations` が次から更新されています。

* capptain\_reach\_content から engagement\_reach\_content へ
* capptain\_log\_file から engagement\_log\_file へ

### <a name="application-id--sdk-key"></a>アプリケーション ID / SDK キー
Engagement は、接続文字列を使用します。 Mobile Engagement でアプリケーション ID と SDK キーを指定する必要はありません。指定する必要があるのは接続文字列のみです。 接続文字列は、EngagementConfiguration ファイルで設定できます。

Engagement の構成は、プロジェクトの `Resources\EngagementConfiguration.xml` ファイルで設定できます。

このファイルを編集して、次の内容を指定します。

* `<connectionString>` タグと `<\connectionString>` タグの間のアプリケーション接続文字列。

代わりに指定を実行時に行う場合は、 Engagement エージェントを初期化する前に、次のメソッドを呼び出すことができます。

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

アプリケーションの接続文字列が Azure クラシック ポータルに表示されます。

### <a name="items-name-change"></a>項目名の変更
*capptain* という名前の項目はすべて *engagement* という名前に変更されています。 同様に、*Capptain* は *Engagement* に変更されています。

一般的に使用される Capptain 項目の例:

* CapptainConfiguration は EngagementConfiguration という名前になりました
* CapptainAgent は EngagementAgent という名前になりました
* CapptainReach は EngagementReach という名前になりました
* CapptainHttpConfig は EngagementHttpConfig という名前になりました
* GetCapptainPageName は GetEngagementPageName という名前になりました

名前の変更はオーバーライドされたメソッドにも影響することにご注意ください。


