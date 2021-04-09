---
title: Smooth Streaming 用の Windows ストア アプリケーション チュートリアル | Microsoft Docs
description: Azure Media Services を使用して、スムーズ ストリーム コンテンツを再生するための XML MediaElement コントロールを備えた Windows ストア アプリケーションを C# で作成する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 20c7068fa0cb56699fdbc6d75b279abadd583832
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017122"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>スムーズ ストリーミング用の Windows ストア アプリケーションを作成する方法

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Windows 8 用 Smooth Streaming Client SDK を使用すると、オンデマンドおよびライブ スムーズ ストリーミング コンテンツの再生が可能な Windows ストア アプリケーションを作成できます。 スムーズ ストリーミング コンテンツの基本再生機能に加えて、SDK では、Microsoft PlayReady Protection、品質レベル制限、ライブ DVR、オーディオ ストリーム切り替え、ステータス更新 (品質レベルの変化など) のリスニング、エラー イベントなどの豊富な機能が提供されます。 サポートされている機能の詳細については、 [リリース ノート](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes)を参照してください。 詳細については、 [Windows 8 用プレーヤー フレームワークに関する記事](https://playerframework.codeplex.com/)を参照してください。 

このチュートリアルには、次の 4 つのレッスンが含まれています。

1. 基本的なスムーズ ストリーミング ストア アプリケーションの作成
2. スライダーの追加によるメディア進行の制御
3. スムーズ ストリーミング ストリームの選択
4. スムーズ ストリーミング トラックの選択

## <a name="prerequisites"></a>前提条件
> [!NOTE]
> Visual Studio 2017 では、Windows ストア プロジェクト バージョン 8.1 以前のプロジェクトはサポートされていません。  詳細については、「[Visual Studio 2017 の対象プラットフォームと互換性](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs)」を参照してください。

* Windows 8 32 ビットまたは 64 ビット。
* Visual Studio バージョン 2012 から 2015。
* [Windows 8 用 Microsoft Smooth Streaming Client SDK](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home)。

各レッスンの完成したソリューションは、MSDN デベロッパー サンプル コード集 (コード ギャラリー) からダウンロードできます: 

* [レッスン 1](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - シンプルな Windows 8 スムーズ ストリーミング用のメディア プレーヤー 
* [レッスン 2](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - シンプルな Windows 8 スムーズ ストリーミング用のメディア プレーヤーとスライダー バー制御 
* [レッスン 3](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - Windows 8 スムーズ ストリーミング用のメディア プレーヤーとストリーム選択  
* [レッスン 4](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) - Windows 8 スムーズ ストリーミング用のメディア プレーヤーとトラック選択

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>レッスン 1:基本的なスムーズ ストリーミング ストア アプリケーションの作成

このレッスンでは、スムーズ ストリーミング コンテンツを再生するための MediaElement コントロールを備えた Windows ストア アプリケーションを作成します。  実行中のアプリケーションは次のような外観になります。

![スムーズ ストリーミング用の Windows ストア アプリケーションの例][PlayerApplication]

Windows ストア アプリケーションの開発の詳細については、「 [Windows 8 用の優れたアプリケーションの開発](https://developer.microsoft.com/en-us/windows/)」をご覧ください。 このレッスンは、次の工程で構成されています。

1. Windows ストア プロジェクトの作成
2. ユーザー インターフェイスの設計 (XAML)
3. コード ビハインド ファイルの変更
4. アプリケーションのコンパイルとテスト

### <a name="to-create-a-windows-store-project"></a>Windows ストア プロジェクトを作成するには

1. Visual Studio を実行します。バージョン 2012 から 2015 がサポートされます。
1. **[ファイル]** メニューの **[新規作成]** をクリックし、 **[プロジェクト]** をクリックします。
1. [新しいプロジェクト] ダイアログ ボックスで、次の値を入力または選択します。

    | 名前 | 値 |
    | --- | --- |
    | テンプレート グループ |インストール済み/テンプレート/Visual C#/Windows ストア |
    | Template |新しいアプリケーション (XAML) |
    | 名前 |SSPlayer |
    | 場所 |C:\SSTutorials |
    | [ソリューション名] |SSPlayer |
    | ソリューションのディレクトリを作成 |(オン) |

1. **[OK]** をクリックします。

### <a name="to-add-a-reference-to-the-smooth-streaming-client-sdk"></a>スムーズ ストリーミング クライアント SDK への参照を追加するには

1. ソリューション エクスプローラーで **[SSPlayer]** を右クリックし、 **[参照の追加]** をクリックします。
1. 次の値を入力または選択します。

    | 名前 | 値 |
    | --- | --- |
    | 参照グループ |Windows/拡張 |
    | リファレンス |Windows 8 用 Microsoft Smooth Streaming Client SDK と Microsoft Visual C++ ランタイム パッケージを選択 |

1. **[OK]** をクリックします。 

参照を追加した後、対象プラットフォーム (x64 または x86) を選択します。[任意の CPU] プラットフォーム構成では参照の追加が機能しません。  この場合は、ソリューション エクスプローラーで、追加した参照に黄色の警告マークが表示されます。

### <a name="to-design-the-player-user-interface"></a>プレーヤー ユーザー インターフェイスを設計するには

1. ソリューション エクスプローラーで、 **MainPage.xaml** をダブルクリックしてデザイン ビューを開きます。
2. XAML ファイル内で **&lt;Grid&gt;** タグと **&lt;/Grid&gt;** タグを探し、2 つのタグの間に次のコードを貼り付けます。

   ```xml
         <Grid.RowDefinitions>

            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
         </Grid.RowDefinitions>

         <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="https://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
         </StackPanel>

         <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
         </StackPanel>

         <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
         </StackPanel>
   ```
   MediaElement コントロールは、メディアの再生に使用します。 次のレッスンでは、sliderProgress という名前のスライダー コントロールを使用して、メディアの進行を制御します。
3. **Ctrl + S** キーを押して、ファイルを保存します。

MediaElement コントロールは、そのままではスムーズ ストリーミング コンテンツをサポートしていません。 スムーズ ストリーミング サポートを有効にするには、スムーズ ストリーミング バイトストリーム ハンドラーをファイル名拡張子と MIME タイプで登録する必要があります。  登録するには、Windows.Media 名前空間の MediaExtensionManager.RegisterByteStreamHandler メソッドを使用します。

この XAML ファイル内で、いくつかのイベント ハンドラーがコントロールに関連付けられています。  これらのイベント ハンドラーを定義する必要があります。

### <a name="to-modify-the-code-behind-file"></a>分離コード ファイルを変更するには

1. ソリューション エクスプローラーで **MainPage.xaml** を右クリックし、 **[コードの表示]** をクリックします。
2. ファイルの先頭に次の using ステートメントを追加します。

    ```csharp
        using Windows.Media;
    ```

3. **MainPage** クラスの先頭に、次のデータ メンバーを追加します。

    ```csharp
        private MediaExtensionManager extensions = new MediaExtensionManager();
    ```

4. **MainPage** コンストラクターの末尾に、次の 2 行を追加します。

    ```csharp
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
    ```

5. **MainPage** クラスの末尾に、次のコードを貼り付けます。
   ```csharp
         # region UI Button Click Events
         private void btnPlay_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Play();
         txtStatus.Text = "MediaElement is playing ...";
         }
         private void btnPause_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Pause();
         txtStatus.Text = "MediaElement is paused";
         }
         private void btnSetSource_Click(object sender, RoutedEventArgs e)
         {

         sliderProgress.Value = 0;
         mediaElement.Source = new Uri(txtMediaSource.Text);

         if (chkAutoPlay.IsChecked == true)
         {
             txtStatus.Text = "MediaElement is playing ...";
         }
         else
         {
             txtStatus.Text = "Click the Play button to play the media source.";
         }
         }
         private void btnStop_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Stop();
         txtStatus.Text = "MediaElement is stopped";
         }
         private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
         {

         txtStatus.Text = "Seek to position " + sliderProgress.Value;
         mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
         }
         # endregion
   ```
   ここでは sliderProgress_PointerPressed イベント ハンドラーが定義されています。  正しく機能させるには他の作業も必要ですが、これらについては次のレッスンで説明します。
6. **Ctrl + S** キーを押して、ファイルを保存します。

完成したコード ビハインド ファイルは次のようになります。

![Visual Studio のコード ビューに表示されたスムーズ ストリーミング用の Windows ストア アプリケーション][CodeViewPic]

### <a name="to-compile-and-test-the-application"></a>アプリケーションのコンパイルとテストを行うには

1. **[ビルド]** メニューの **[構成マネージャー]** をクリックします。
2. **[アクティブ ソリューション プラットフォーム]** を、開発プラットフォームに一致するように変更します。
3. **F6** キーを押して、プロジェクトをコンパイルします。 
4. **F5** キーを押してアプリケーションを実行します。
5. アプリケーションの先頭部分で、既定のスムーズ ストリーミング URL を使用するか、別の URL を入力します。 
6. **[ソースの設定]** をクリックします。 **[自動的に再生する]** が既定で有効になっているため、メディアは自動的に再生されます。  **[再生]** ボタン、 **[一時停止]** ボタン、および **[停止]** ボタンを使用して、メディアを制御できます。  垂直スライダーを使用して、音量を制御できます。  ただし、メディアの進行を制御するための水平スライダーは、まだ完全に実装できていません。 

これでレッスン 1 が完了しました。  このレッスンでは、MediaElement コントロールを使用して、スムーズ ストリーミング コンテンツを再生しました。  次のレッスンでは、スライダーを追加して、スムーズ ストリーミング コンテンツの進行を制御します。

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>レッスン 2: スライダーの追加によるメディア進行の制御

レッスン 1 では、スムーズ ストリーミング メディア コンテンツを再生するための MediaElement XAML コントロールを備えた Windows ストア アプリケーションを作成しました。  これには、開始、停止、一時停止などの基本的なメディア機能が備わっています。  このレッスンでは、アプリケーションにスライダー コントロールを追加します。

このチュートリアルでは、タイマーを使用し、MediaElement コントロールの現在の位置に基づいて、スライダーの位置を更新します。  ライブ コンテンツの場合は、スライダーの開始時間と終了時間も更新する必要があります。  この処理は、アダプティブ ソース更新イベント内で行う方が適しています。

メディア ソースとは、メディア データを生成するオブジェクトです。  ソース リゾルバーは URL またはバイト ストリームを受け取って、そのコンテンツに対応する適切なメディア ソースを作成します。  ソース リゾルバーの使用は、アプリケーションがメディア ソースを作成するための標準的な手段です。 

このレッスンは、次の工程で構成されています。

1. スムーズ ストリーミング ハンドラーの登録 
2. アダプティブ ソース マネージャー レベルのイベント ハンドラーの追加
3. アダプティブ ソース レベルのイベント ハンドラーの追加
4. MediaElement イベント ハンドラーの追加
5. スライダー関連コードの追加
6. アプリケーションのコンパイルとテスト

### <a name="to-register-the-smooth-streaming-byte-stream-handler-and-pass-the-propertyset"></a>スムーズ ストリーミング バイトストリーム ハンドラーを登録して propertyset を渡すには

1. ソリューション エクスプローラーで **MainPage.xaml** を右クリックし、 **[コードの表示]** をクリックします。
2. ファイルの先頭に次の using ステートメントを追加します。

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. MainPage クラスの先頭に、次のデータ メンバーを追加します。

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. **MainPage** コンストラクター内で、**this.Initialize Components();** という行と前のレッスンで記述した登録コード行の後に、次のコードを追加します。

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. **MainPage** コンストラクター内で、2 つの RegisterByteStreamHandler メソッドを変更して、4 つ目のパラメーターを追加します。

   ```csharp
         // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
         // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
         // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
         propertySet);
   ```
6. **Ctrl + S** キーを押して、ファイルを保存します。

### <a name="to-add-the-adaptive-source-manager-level-event-handler"></a>アダプティブ ソース マネージャー レベルのイベント ハンドラーを追加するには

1. ソリューション エクスプローラーで **MainPage.xaml** を右クリックし、 **[コードの表示]** をクリックします。
2. **MainPage** クラス内で、次のデータ メンバーを追加します。

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. **MainPage** クラスの末尾に、次のイベント ハンドラーを追加します。

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. AdaptiveSourceOpenedEvent をサブスクライブするために、 **MainPage** コンストラクターの末尾に次の行を追加します。

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. **Ctrl + S** キーを押して、ファイルを保存します。

### <a name="to-add-adaptive-source-level-event-handlers"></a>アダプティブ ソース レベルのイベント ハンドラーを追加するには

1. ソリューション エクスプローラーで **MainPage.xaml** を右クリックし、 **[コードの表示]** をクリックします。
2. **MainPage** クラス内で、次のデータ メンバーを追加します。

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. **MainPage** クラスの末尾に、次のイベント ハンドラーを追加します。

   ```csharp
         # region Adaptive Source Level Events
         private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
         }

         private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
         {

            adaptiveSourceStatusUpdate = args;
         }

         private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
         {

            txtStatus.Text = "Error: " + args.HttpResponse;
         }

         # endregion Adaptive Source Level Events
   ```
4. 各イベントをサブスクライブするために、 **mediaElement AdaptiveSourceOpened** メソッドの末尾に次のコードを追加します。

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. **Ctrl + S** キーを押して、ファイルを保存します。

アダプティブ ソース マネージャー レベルにも同じイベントがあり、これらは、アプリケーション内ですべてのメディア要素に共通した機能の処理に使用できます。 各 AdaptiveSource には独自のイベントがあり、すべての AdaptiveSource イベントは AdaptiveSourceManager からカスケード処理されます。

### <a name="to-add-media-element-event-handlers"></a>MediaElement イベント ハンドラーを追加するには

1. ソリューション エクスプローラーで **MainPage.xaml** を右クリックし、 **[コードの表示]** をクリックします。
2. **MainPage** クラスの末尾に、次のイベント ハンドラーを追加します。

   ```csharp
         # region Media Element Event Handlers
         private void MediaOpened(object sender, RoutedEventArgs e)
         {

            txtStatus.Text = "MediaElement opened";
         }

         private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
         {

            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
         }

         private void MediaEnded(object sender, RoutedEventArgs e)
         {

            txtStatus.Text ="MediaElement ended.";
         }

         # endregion Media Element Event Handlers
   ```
3. 各イベントをサブスクライブするために、 **MainPage** コンストラクターの末尾に次のコードを追加します。

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. **Ctrl + S** キーを押して、ファイルを保存します。

### <a name="to-add-slider-bar-related-code"></a>スライダー バー関連コードを追加するには

1. ソリューション エクスプローラーで **MainPage.xaml** を右クリックし、 **[コードの表示]** をクリックします。
2. ファイルの先頭に次の using ステートメントを追加します。

   ```csharp
        using Windows.UI.Core;
   ```
3. **MainPage** クラス内で、次のデータ メンバーを追加します。

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. **MainPage** コンストラクターの末尾に、次のコードを追加します。

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. **MainPage** クラスの末尾に、次のコードを追加します。

   ```csharp
         # region sliderMediaPlayer
         private double SliderFrequency(TimeSpan timevalue)
         {

            long absvalue = 0;
            double stepfrequency = -1;

            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }

            TimeSpan totalDVRDuration = new TimeSpan(absvalue);

            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }

            return stepfrequency;
         }

         void updateSliderPositionoNTicks(object sender, object e)
         {

            sliderProgress.Value = mediaElement.Position.TotalSeconds;
         }

         public void setupTimer()
         {

            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
         }

         public void startTimer()
         {

            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderStartTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderEndTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
         }

         # endregion sliderMediaPlayer
   ```

   > [!NOTE]
   > CoreDispatcher は、非 UI スレッドから UI スレッドへの変更を行うために使用します。 開発者は、ディスパッチャー スレッドでボトルネックが発生した場合に備えて、更新する UI 要素によって提供されるディスパッチャーの使用を選択できます。  次に例を示します。

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. **mediaElement_AdaptiveSourceStatusUpdated** メソッドの末尾に、次のコードを追加します。

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. **MediaOpened** メソッドの末尾に、次のコードを追加します。

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. **Ctrl + S** キーを押して、ファイルを保存します。

### <a name="to-compile-and-test-the-application"></a>アプリケーションのコンパイルとテストを行うには

1. **F6** キーを押して、プロジェクトをコンパイルします。 
2. **F5** キーを押してアプリケーションを実行します。
3. アプリケーションの先頭部分で、既定のスムーズ ストリーミング URL を使用するか、別の URL を入力します。 
4. **[ソースの設定]** をクリックします。 
5. スライダーをテストします。

これでレッスン 2 が完了しました。  このレッスンでは、アプリケーションにスライダー コントロールを追加しました。 

## <a name="lesson-3-select-smooth-streaming-streams"></a>レッスン 3:スムーズ ストリーミング ストリームの選択
スムーズ ストリーミングでは、複数の言語オーディオ トラックを使用したコンテンツのストリーミングが可能あり、ユーザーがストリームを選択することができます。  このレッスンでは、ユーザーによるストリーム選択を有効にします。 このレッスンは、次の工程で構成されています。

1. XAML ファイルの変更
2. コード ビハインド ファイルの変更
3. アプリケーションのコンパイルとテスト

### <a name="to-modify-the-xaml-file"></a>XAML ファイルを変更するには

1. ソリューション エクスプローラーで **MainPage.xaml** を右クリックし、 **[デザイナーの表示]** をクリックします。
2. &lt;Grid.RowDefinitions&gt; を探し、次のように各 RowDefinition を変更します。

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. &lt;Grid&gt;&lt;/Grid&gt; タグ内に次のコードを追加して、リストボックス コントロールを定義します。これによりユーザーは、使用できるストリームの一覧を確認し、ストリームを選択できます。

   ```xml
         <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
         </Grid>
   ```
4. **Ctrl + S** キーを押して、変更を保存します。

### <a name="to-modify-the-code-behind-file"></a>分離コード ファイルを変更するには

1. ソリューション エクスプローラーで **MainPage.xaml** を右クリックし、 **[コードの表示]** をクリックします。
2. SSPlayer 名前空間内に、新しいクラスを追加します。

   ```csharp
        #region class Stream
   
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
   
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
   
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
   
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream
   ```
3. MainPage クラスの先頭に、次の変数定義を追加します。

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. MainPage クラス内に、次の #region ブロックを追加します。
   ```csharp
        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
   
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
   
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
   
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
   
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
   
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
   
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
   
            // Select the first video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
   
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
   
            // Select the first audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
   
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
   
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection
   ```
5. mediaElement_ManifestReady メソッドを探して、関数の末尾に次のコードを追加します。
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    これにより、MediaElement マニフェストの準備が完了すると、コードは使用可能なストリームのリストを取得し、このリストを UI リスト ボックスに設定します。
6. MainPage クラス内で "UI buttons click events" という #region ブロックを探し、次の関数定義を追加します。
   ```csharp
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }
   ```

### <a name="to-compile-and-test-the-application"></a>アプリケーションのコンパイルとテストを行うには

1. **F6** キーを押して、プロジェクトをコンパイルします。 
2. **F5** キーを押してアプリケーションを実行します。
3. アプリケーションの先頭部分で、既定のスムーズ ストリーミング URL を使用するか、別の URL を入力します。 
4. **[ソースの設定]** をクリックします。 
5. 既定の言語は audio_eng です。 audio_eng と audio_es の間で切り替えます。 新しいストリームを選択するたびに、[送信] ボタンをクリックする必要があります。

これでレッスン 3 が完了しました。  このレッスンでは、ストリームを選択する機能を追加しました。

## <a name="lesson-4-select-smooth-streaming-tracks"></a>レッスン 4:スムーズ ストリーミング トラックの選択

スムーズ ストリーミング プレゼンテーションには、別々の品質レベル (ビット レート) と解像度でエンコードされた複数のビデオ ファイルが含まれていることがあります。 このレッスンでは、ユーザーによるトラック選択を有効にします。 このレッスンは、次の工程で構成されています。

1. XAML ファイルの変更
2. コード ビハインド ファイルの変更
3. アプリケーションのコンパイルとテスト

### <a name="to-modify-the-xaml-file"></a>XAML ファイルを変更するには

1. ソリューション エクスプローラーで **MainPage.xaml** を右クリックし、 **[デザイナーの表示]** をクリックします。
2. Name 属性が **gridStreamAndBitrateSelection** である &lt;Grid&gt; タグを探し、タグの末尾に次のコードを追加します。
   ```xml
         <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
         </StackPanel>
   ```
3. **Ctrl + S** キーを押して、変更を保存します。

### <a name="to-modify-the-code-behind-file"></a>分離コード ファイルを変更するには

1. ソリューション エクスプローラーで **MainPage.xaml** を右クリックし、 **[コードの表示]** をクリックします。
2. SSPlayer 名前空間内に、新しいクラスを追加します。
   ```csharp
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
   
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
   
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
   
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track
   ```
3. MainPage クラスの先頭に、次の変数定義を追加します。
   ```csharp
        private List<Track> availableTracks;
   ```
4. MainPage クラス内に、次の #region ブロックを追加します。
   ```csharp
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>
   
        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();
   
            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;
   
            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;
   
                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }
   
        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }
   
        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }
   
        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }
   
        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection
   ```
5. mediaElement_ManifestReady メソッドを探して、関数の末尾に次のコードを追加します。
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. MainPage クラス内で "UI buttons click events" という #region ブロックを探し、次の関数定義を追加します。
   ```csharp
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }
   ```
   
### <a name="to-compile-and-test-the-application"></a>アプリケーションのコンパイルとテストを行うには

1. **F6** キーを押して、プロジェクトをコンパイルします。 
2. **F5** キーを押してアプリケーションを実行します。
3. アプリケーションの先頭部分で、既定のスムーズ ストリーミング URL を使用するか、別の URL を入力します。 
4. **[ソースの設定]** をクリックします。 
5. 既定では、ビデオ ストリームのすべてのトラックが選択されます。 ビット レートの変化を試すには、使用可能な最低ビット レートを選択してから、使用可能な最高ビット レートを選択します。 変更した後には、そのたびに [送信] をクリックする必要があります。  ビデオ品質の変化を確認できます。

これでレッスン 4 が完了しました。  このレッスンでは、トラックを選択する機能を追加しました。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>その他のリソース:
* [How to build a Smooth Streaming Windows 8 JavaScript application with advanced features (高度な機能を備えたスムーズ ストリーミング用の Windows 8 JavaScript アプリケーションを作成する方法)](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Smooth Streaming Technical Overview (スムーズ ストリーミングの技術概要)](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
