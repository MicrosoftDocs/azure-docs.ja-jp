---
title: クイック スタート:カスタムの音声優先仮想アシスタント (プレビュー)、C# (UWP) - Speech Service
titleSuffix: Azure Cognitive Services
description: この記事では、Cognitive Services Speech ソフトウェア開発キット (SDK) を使用して C# のユニバーサル Windows プラットフォーム (UWP) アプリケーションを作成します。 クライアント アプリケーションを、Direct Line Speech チャネルを使用するように構成された、以前に作成済みの Bot Framework ボットに接続します。 このアプリケーションは、Speech SDK NuGet パッケージと Microsoft Visual Studio 2019 を使用して作成します。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: travisw
ms.openlocfilehash: c676e98eb812a31d6fb8d7cc0f58929f803c868e
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382003"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-uwp"></a>クイック スタート:Speech SDK、UWP を使用して音声優先仮想アシスタントを作成する

[音声認識](quickstart-csharp-uwp.md)、[音声合成](quickstart-text-to-speech-csharp-uwp.md)、[音声翻訳](quickstart-translate-speech-uwp.md)のクイックスタートも利用できます。

この記事では、[Speech SDK](speech-sdk.md) を使用して C# のユニバーサル Windows プラットフォーム (UWP) アプリケーションを開発します。 このプログラムは、以前に作成および構成されたボットに接続して、クライアント アプリケーションからの音声優先仮想アシスタント エクスペリエンスを可能にします。 このアプリケーションは、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2019 (任意のエディション) を使用して作成します。

> [!NOTE]
> ユニバーサル Windows プラットフォームを使用すると、PC、Xbox、Surface Hub、その他のデバイスなど、Windows 10 をサポートする任意のデバイスで動作するアプリを開発できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* Speech Services 用の Azure サブスクリプション キー。 [無料で入手する](get-started.md)か、[Azure portal](https://portal.azure.com) 上に作成します。
* [Direct Line Speech チャネル](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)で構成された、以前に作成済みのボット。

  > [!NOTE]
  > Direct Line Speech (プレビュー) は、Speech Services のリージョンの一部でのみ現在利用できます。 [音声優先仮想アシスタントをサポートしているリージョンの一覧](regions.md#voice-first-virtual-assistants)を参照し、ご使用のリソースがそれらのリージョンのいずれかにデプロイされていることを確認します。

## <a name="optional-get-started-fast"></a>省略可能:すぐに開始

このクイックスタートでは、音声対応のボットに接続するクライアント アプリケーションの作成方法を順を追って説明します。 このクイック スタートで使用されている、すぐにコンパイルできる完全なソース コードは、[Speech SDK のサンプル](https://aka.ms/csspeech/samples)のページの `quickstart` フォルダーに含まれています。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

今度は、アプリケーションのユーザー インターフェイスを定義する XAML コードを追加し、C# のコードビハインド実装を追加します。

### <a name="xaml-code"></a>XAML コード

まず、次の XAML コードを追加してアプリケーションのユーザー インターフェイスを作成します。

1. **ソリューション エクスプローラー**で、`MainPage.xaml` を開きます。

1. デザイナーの XAML ビューで、内容全体を次のコード スニペットに置き換えます。

    ```xml
    <Page
        x:Class="helloworld.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:helloworld"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

        <Grid>
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  
                        Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  
                        Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" 
                        Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" 
                        Margin="0,10,10,0" Click="ListenButton_ButtonClicked" 
                        Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" 
                            RelativePanel.AlignBottomWithPanel="True" 
                            RelativePanel.AlignRightWithPanel="True" 
                            RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" 
                               TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  
                                      VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce 
                                 the status update. -->
                            <TextBlock 
                                x:Name="StatusBlock" FontWeight="Bold" 
                                AutomationProperties.LiveSetting="Assertive"
                                MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" 
                                Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

デザイン ビューが更新されてアプリケーションのユーザー インターフェイスが表示されます。

### <a name="c-code-behind-source"></a>C# のコードビハインド ソース

次に、アプリケーションが想定どおりに動作するよう、コードビハインド ソースを追加します。 コードビハインド ソースには、次の内容が含まれています。

- `Speech` 名前空間と `Speech.Dialog` 名前空間の `using` ステートメント
- ボタン ハンドラーに接続された、マイクへのアクセスを確実にするための簡単な実装
- アプリケーション内のメッセージとエラーを表示する基本的な UI ヘルパー
- 後で設定される初期化コード パスのランディング ポイント
- テキスト読み上げを再生するヘルパー (ストリーミングのサポートなし)
- 後で設定されるリスニングを開始するための空のボタン ハンドラー

コードビハインド ソースを追加するには、次の手順を実行します。

1. **ソリューション エクスプローラー**で、コードビハインドのソース ファイル `MainPage.xaml.cs` を開きます (`MainPage.xaml` にグループ化されています)。

1. ファイルの内容を次のコード スニペットに置き換えます。

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Dialog;
    using System;
    using System.Diagnostics;
    using System.IO;
    using System.Text;
    using Windows.Foundation;
    using Windows.Storage.Streams;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Media;

    namespace helloworld
    {
        public sealed partial class MainPage : Page
        {
            private DialogServiceConnector connector;

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = 
                        new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = 
                        Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(
                        new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(
                string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(
                        Windows.UI.Core.CoreDispatcherPriority.Normal, 
                        () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) 
                    ? strMessage : "\n" + strMessage;

                if (!string.IsNullOrEmpty(StatusBlock.Text))
                {
                    StatusBorder.Visibility = Visibility.Visible;
                    StatusPanel.Visibility = Visibility.Visible;
                }
                else
                {
                    StatusBorder.Visibility = Visibility.Collapsed;
                    StatusPanel.Visibility = Visibility.Collapsed;
                }
                // Raise an event if necessary to enable a screen reader 
                // to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(
                        Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for and accumulates all audio associated with a given 
            // PullAudioOutputStream and then plays it to the MediaElement. Long spoken 
            // audio will create extra latency and a streaming playback solution 
            // (that plays audio while it continues to be received) should be used -- 
            // see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(
                PullAudioOutputStream activityAudio)
            {
                var playbackStreamWithHeader = new MemoryStream();
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
                playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
                playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

                byte[] pullBuffer = new byte[2056];

                uint lastRead = 0;
                do
                {
                    lastRead = activityAudio.Read(pullBuffer);
                    playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
                }
                while (lastRead == pullBuffer.Length);

                var task = Dispatcher.RunAsync(
                    Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(
                        playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeDialogServiceConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. `InitializeDialogServiceConnector` のメソッド本体に、次のコード スニペットを追加します。 このコードにより、サブスクリプション情報を使用して `DialogServiceConnector` が作成されます。

    ```csharp
    // create a DialogServiceConfig by providing a bot secret key 
    // and Cognitive Services subscription key
    // the RecoLanguage property is optional (default en-US); 
    // note that only en-US is supported in Preview
    const string channelSecret = "YourChannelSecret"; // Your channel secret
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key

    // Your subscription service region. 
    // Note: only a subset of regions are currently supported
    const string region = "YourServiceRegion"; 

    var botConfig = DialogServiceConfig.FromBotSecret(
        channelSecret, speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

   > [!NOTE]
   > Direct Line Speech (プレビュー) は、Speech Services のリージョンの一部でのみ現在利用できます。 [音声優先仮想アシスタントをサポートしているリージョンの一覧](regions.md#voice-first-virtual-assistants)を参照し、ご使用のリソースがそれらのリージョンのいずれかにデプロイされていることを確認します。

   > [!NOTE]
   > ボットの構成とチャネル シークレットの取得については、[Direct Line Speech チャネル](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)に関する Bot Framework のドキュメントを参照してください。

1. `YourChannelSecret`、`YourSpeechSubscriptionKey`、`YourServiceRegion` の各文字列を、実際のボット、音声サブスクリプション、[リージョン](regions.md)の値に置き換えます。

1. `InitializeDialogServiceConnector` のメソッド本体の最後に、次のコード スニペットを追加します。 このコードは、ボットのアクティビティや音声認識の結果などの情報を伝達する `DialogServiceConnector` が依存するイベントのハンドラーを設定します。

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client 
    // and uses bot framework activities
    connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
    {
        NotifyUser(
            $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

        if (activityReceivedEventArgs.HasAudio)
        {
            SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
        }
    };

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.Canceled += (sender, canceledEventArgs) =>
    {
        NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
        if (canceledEventArgs.Reason == CancellationReason.Error)
        {
            NotifyUser(
                $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };

    // Recognizing (not 'Recognized') will provide the intermediate recognized text 
    // while an audio stream is being processed
    connector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };

    // Recognized (not 'Recognizing') will provide the final recognized text 
    // once audio capture is completed
    connector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };

    // SessionStopped will notify when a turn is complete and 
    // it's safe to begin listening again
    connector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. `MainPage` クラスの `ListenButton_ButtonClicked` メソッドの本体に次のコード スニペットを追加します。 既に構成は確立済みであり、イベント ハンドラーも登録してあるため、後は、リッスンする `DialogServiceConnector` をこのコードで設定します。

    ```csharp
    if (connector == null)
    {
        InitializeDialogServiceConnector();
        // Optional step to speed up first interaction: if not called, 
        // connection happens automatically on first use
        var connectTask = connector.ConnectAsync();
    }

    try
    {
        // Start sending audio to your speech-enabled bot
        var listenTask = connector.ListenOnceAsync();

        // You can also send activities to your bot as JSON strings -- 
        // Microsoft.Bot.Schema can simplify this
        string speakActivity = 
            @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
        await connector.SendActivityAsync(speakActivity);

    }
    catch (Exception ex)
    {
        NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
    }
    ```

1. メニュー バーから **[ファイル]**  >  **[すべて保存]** を選択して変更内容を保存します。

## <a name="build-and-run-the-application"></a>アプリケーションの構築と実行

アプリケーションをビルドしてテストする準備が整いました。

1. メニュー バーから **[ビルド]**  >  **[ソリューションのビルド]** を選択してアプリケーションをビルドします。 これで、コードは、エラーなしでコンパイルされます。

1. **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押して、アプリケーションを起動します。 **[helloworld]** ウィンドウが表示されます。

   ![サンプル UWP 仮想アシスタント アプリケーション (C#) - クイックスタート](media/sdk/qs-virtual-assistant-uwp-helloworld-window.png)

1. **[Enable Microphone]** を選択し、アクセス許可要求がポップアップ表示されたら、 **[はい]** を選択します。

   ![マイクへのアクセス許可要求](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. **[Talk to your bot]** を選択し、デバイスのマイクに向かって英語のフレーズや文章を話します。 音声が Direct Line Speech チャネルに送信されてテキストに変換され、ウィンドウに表示されます。
<!--
    ![Successful bot response](media/voice-first-virtual-assistants/quickstart-cs-uwp-bot-successful-turn.png)
-->
## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [基本ボットの作成とデプロイ](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>関連項目

- [音声優先仮想アシスタントの概要](voice-first-virtual-assistants.md)
- [Speech Services のサブスクリプション キーを無料で取得する](get-started.md)
- [カスタム ウェイク ワード](speech-devices-sdk-create-kws.md)
- [ボットを Direct Line Speech に接続する](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [GitHub で C# のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
