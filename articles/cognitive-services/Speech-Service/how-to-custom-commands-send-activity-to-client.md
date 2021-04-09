---
title: クライアント アプリケーションへのカスタム コマンド アクティビティの送信
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム コマンド アプリケーションから Speech SDK を実行しているクライアント アプリケーションにアクティビティを送信する方法について説明します。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52a4dbc4ff01515af8cd7d2503877184a09f7e64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94566097"
---
# <a name="send-custom-commands-activity-to-client-application"></a>クライアント アプリケーションへのカスタム コマンド アクティビティの送信

この記事では、カスタム コマンド アプリケーションから Speech SDK を実行しているクライアント アプリケーションにアクティビティを送信する方法について説明します。

次のタスクを実行します。

- カスタム コマンド アプリケーションからカスタム JSON ペイロードを定義して送信する
- C# UWP Speech SDK クライアント アプリケーションからカスタム JSON ペイロード コンテンツを受信して視覚化する

## <a name="prerequisites"></a>前提条件
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) またはそれ以降。 このガイドでは Visual Studio 2019 を使用します
> * 音声サービス用の Azure サブスクリプション キー: [無料で入手する](overview.md#try-the-speech-service-for-free)か、[Azure portal](https://portal.azure.com) で作成します
> * 以前に[作成したカスタム コマンド アプリ](quickstart-custom-commands-application.md)
> * Speech SDK が有効なクライアント アプリ: [Speech SDK を使用してクライアント アプリケーションと統合する](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>クライアントへのアクティビティの送信を設定する 
1. 前に作成したカスタム コマンド アプリケーションを開きます。
1. **TurnOnOff** コマンドを選択し、完了ルールの **[ConfirmationResponse]** を選択してから、 **[アクションの追加]** を選択します。
1. **[新しいアクションの種類]** で、 **[クライアントへのアクティビティの送信]** を選択します。
1. 以下の JSON を **アクティビティ コンテンツ** にコピーしてします
   ```json
   {
      "type": "event",
      "name": "UpdateDeviceState",
      "value": {
        "state": "{OnOff}",
        "device": "{SubjectDevice}"
      }
    }
   ```
1. **[保存]** をクリックして、アクティビティの送信アクションを含む新しいルールを作成し、変更内容を **トレーニング** して **公開** します

   > [!div class="mx-imgBorder"]
   > ![アクティビティの送信の完了規則](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>クライアント アプリケーションと統合する

[方法: Speech SDK (プレビュー) を使用してカスタム コマンド アプリケーションを設定する](./how-to-custom-commands-setup-speech-sdk.md) では、`turn on the tv`、`turn off the fan` などのコマンドを処理する Speech SDK を使用して UWP クライアント アプリケーションを作成しました。 いくつかのビジュアルを追加して、それらのコマンドの結果を確認できます。

**オン** または **オフ** を示すテキストを含むラベル付きボックスを追加するには、以下の StackPanel の XML ブロックを `MainPage.xaml` に追加します。

```xml
<StackPanel Orientation="Vertical" H......>
......
</StackPanel>
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
<MediaElement ....../>
```

### <a name="add-reference-libraries"></a>参照ライブラリを追加する

JSON ペイロードを作成したので、[JSON.NET](https://www.newtonsoft.com/json) ライブラリへの参照を追加して、逆シリアル化を処理する必要があります。

1. ソリューションを右クリックします。
1. **[ソリューションの NuGet パッケージの管理]** を選択し、 **[Browse]\(参照\)** を選択します。 
1. **Newtonsoft. json** を既にインストールしている場合は、そのバージョンが 12.0.3 以降であることを確認してください。 そうでない場合は、 **[ソリューションの NuGet パッケージの管理] - [Updates]\(更新プログラム\)** にアクセスし、**Newtonsoft. json** を検索して更新します。 このガイドでは、バージョン 12.0.3 を使用しています。

    > [!div class="mx-imgBorder"]
    > ![アクティビティの送信のペイロード](media/custom-commands/send-activity-to-client-json-nuget.png)

1. また、NuGet パッケージ **Microsoft.NETCore.UniversalWindowsPlatform** が 6.2.10 以降になっていることを確認します。 このガイドでは、バージョン 6.2.10 を使用しています。

"MainPage.xaml.cs" に以下を追加します

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>受信したペイロードを処理する

`InitializeDialogServiceConnector` で、`ActivityReceived` イベント ハンドラーを次のコードに置き換えます。 変更した `ActivityReceived` イベント ハンドラーでは、アクティビティからペイロードが抽出され、テレビまたはファンの表示状態がそれぞれ変更されます。

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.value?.state != null ? activity.value.state.ToString() : string.Empty;
        var device = activity?.value?.device != null ? activity.value.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>試してみる

1. アプリケーションの起動
1. [Enable Microphone]\(マイクを有効にする\) を選択します
1. [Talk]/(トーク/) ボタンを選択します
1. `turn on the tv` と言います
1. テレビの表示状態が "オン" に変わるはずです。
   > [!div class="mx-imgBorder"]
   > ![テレビの表示状態が現在オンになっていることを示すスクリーンショット。](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: Web エンドポイントを設定する (プレビュー)](./how-to-custom-commands-setup-web-endpoints.md)
