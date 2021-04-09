---
title: Web エンドポイントを設定する (プレビュー)
titleSuffix: Azure Cognitive Services
description: カスタム コマンドの Web エンド ポイントを設定する
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 6f2dfdbb5833b34441b4abba7359ad70c4717d1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98602151"
---
# <a name="set-up-web-endpoints"></a>Web エンドポイントを設定する

この記事では、クライアント アプリケーションから HTTP 要求を行うために、カスタム コマンド アプリケーションで Web エンドポイントを設定する方法について説明します。 次のタスクを実行します。

- カスタム コマンド アプリケーションで Web エンドポイントを設定する
- カスタム コマンド アプリケーションで Web エンドポイントを呼び出す
- Web エンドポイントの応答を受信する
- Web エンドポイントの応答をカスタム JSON ペイロードに統合し、C# UWP Speech SDK クライアント アプリケーションからそれを送信して視覚化する

## <a name="prerequisites"></a>前提条件
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * 音声サービス用の Azure サブスクリプション キー: [無料で入手する](overview.md#try-the-speech-service-for-free)か、[Azure portal](https://portal.azure.com) で作成します
> * 以前に[作成したカスタム コマンド アプリ](quickstart-custom-commands-application.md)
> * Speech SDK が有効なクライアント アプリ: [クライアント アプリケーションへのアクティビティの送信方法](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Web エンドポイントを設定する

1. 前に作成したカスタム コマンド アプリケーションを開きます。
1. [Web エンドポイント] に移動し、[New web endpoint]\(新しい Web エンドポイント\) をクリックします。

   > [!div class="mx-imgBorder"]
   > ![新しい Web エンドポイント](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | 設定 | 推奨値 | 説明 |
   | ------- | --------------- | ----------- |
   | 名前 | UpdateDeviceState | Web エンドポイントの名前。 |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | カスタム コマンド アプリの通信相手とするエンドポイントの URL。 |
   | Method | POST | エンドポイントに対して許可されたやりとり (GET や POST など)。|
   | ヘッダー | キー: アプリ、値: applicationId の最初の 8 桁を取得します | 要求ヘッダーに含めるヘッダー パラメーター。|

    > [!NOTE]
    > - [Azure Functions](../../azure-functions/index.yml) を使用して作成された Web エンドポイントの例。テレビとファンのデバイス状態を保存するデータベースと結合されます
    > - 提案されたヘッダーは、エンドポイントの例にのみ必要です
    > - ヘッダーの値が例のエンドポイントで一意になるよう、applicationId の最初の 8 桁を取得します
    > - 現実には、Web エンドポイントを、ご利用のデバイスを管理する [IOT ハブ](../../iot-hub/about-iot-hub.md)のエンドポイントとすることができます。

1. **[保存]** をクリックします。

## <a name="call-web-endpoints"></a>Web エンドポイントを呼び出す

1. **TurnOnOff** コマンドにアクセスし、完了ルールの **[ConfirmationResponse]** を選択してから、 **[アクションの追加]** を選択します。
1. **[新しいアクションの種類]** で、 **[Call Web Endpoint]\(Web エンドポイントの呼び出し\)** を選択します。
1. **[アクションの編集] - [エンドポイント]** で、作成済みの Web エンドポイントである **[UpdateDeviceState]** を選択します。  
1. **[構成]** で、次の値を入力します。
   > [!div class="mx-imgBorder"]
   > ![Web エンドポイント アクション パラメーターの呼び出し](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | 設定 | 推奨値 | 説明 |
   | ------- | --------------- | ----------- |
   | エンドポイント | UpdateDeviceState | このアクションで呼び出したい Web エンドポイント。 |
   | クエリ パラメーター | item={SubjectDevice}&&value={OnOff} | Web エンドポイントの URL に追加するクエリ パラメーター。  |
   | 本文のコンテンツ | 該当なし | 要求の本文のコンテンツ。 |

    > [!NOTE]
    > - 提案されたクエリ パラメーターは、エンドポイントの例にのみ必要です

1. **[成功時] - [実行するアクション]** で、 **[Send speech response]\(音声応答の送信\)** を選択します。

    **[シンプルなエディター]** に「`{SubjectDevice} is {OnOff}`」と入力します。

   > [!div class="mx-imgBorder"]
   > ![[成功時] - [実行するアクション] 画面を示すスクリーンショット。](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | 設定 | 推奨値 | 説明 |
   | ------- | --------------- | ----------- |
   | 実行するアクション | Send speech response (音声応答の送信) | Web エンドポイントへの要求が成功した場合に実行するアクション |

   > [!NOTE]
   > - `{YourWebEndpointName.FieldName}` を使用すれば、http 応答内のフィールドに直接アクセスすることもできます。 例: `{UpdateDeviceState.TV}`

1. **[失敗時] - [実行するアクション]** で、 **[Send speech response]\(音声応答の送信\)** を選択します。

    **[シンプルなエディター]** に「`Sorry, {WebEndpointErrorMessage}`」と入力します。

   > [!div class="mx-imgBorder"]
   > ![失敗時の Web エンドポイント アクションの呼び出し](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | 設定 | 推奨値 | 説明 |
   | ------- | --------------- | ----------- |
   | 実行するアクション | Send speech response (音声応答の送信) | Web エンドポイントへの要求が失敗した場合に実行するアクション |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` はオプションです。 これは、エラー メッセージを公開したくない場合、自由に削除できます。
   > - このエンドポイントの例では、ヘッダー パラメーターの欠落などの一般的なエラーの場合、詳細なエラー メッセージと共に http 応答を返送します。

### <a name="try-it-out-in-test-portal"></a>テスト ポータルで試してみる
- 成功時の応答\
保存、トレーニング、テスト
   > [!div class="mx-imgBorder"]
   > ![成功時の応答を示すスクリーンショット。](media/custom-commands/setup-web-endpoint-on-success-response.png)
- 失敗時の応答\
クエリ パラメーターの 1 つを削除し、保存、再トレーニング、およびテストを行います。
   > [!div class="mx-imgBorder"]
   > ![成功時の Web エンドポイント アクションの呼び出し](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>クライアント アプリケーションと統合する

[方法: クライアント アプリケーションへのアクティビティの送信 (プレビュー)](./how-to-custom-commands-send-activity-to-client.md) に関するページで、 **[クライアントへのアクティビティの送信]** アクションを追加しました。 このアクティビティは、 **[Call Web Endpoint]\(Web エンドポイントの呼び出し\)** アクションが正常に実行されたかどうかにかかわらず、クライアント アプリケーションに送信されます。
ただし、ほとんどの場合、クライアント アプリケーションにアクティビティを送信する必要があるのは、Web エンドポイントへの呼び出しが成功したときのみです。 この例で、それはデバイスの状態が正常に更新されたときとなります。

1. 前に追加した **[クライアントへのアクティビティの送信]** アクションを削除します。
1. Web エンドポイントの呼び出しを次のように編集します。
    1. **[構成]** で、 **[クエリ パラメーター]** が `item={SubjectDevice}&&value={OnOff}` になっていることを確認する
    1. **[成功時]** の **[実行するアクション]** を **[クライアントへのアクティビティの送信]** に変更する
    1. 以下の JSON を **[Activity Content]\(アクティビティ コンテンツ\)** にコピーする
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
これで、Web エンドポイントへの要求が成功した場合にのみ、アクティビティをクライアントに送信することになります。

### <a name="create-visuals-for-syncing-device-state"></a>デバイスの状態を同期するためのビジュアルを作成する
`"EnableMicrophoneButton"` ブロックの上の `MainPage.xaml` に次の XML を追加します。

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>デバイスの状態の同期

`MainPage.xaml.cs` 内に、参照 `using Windows.Web.Http;` を追加します。 次のコードを `MainPage` クラスに追加します。 このメソッドでは、エンドポイントの例に GET 要求が送信され、ご利用のアプリに対する現在のデバイス状態が抽出されます。 `<your_app_name>` を必ず、カスタム コマンド Web エンドポイントの **ヘッダー** で使用したものに変更してください。

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request.
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>試してみる

1. アプリケーションの起動
1. [Sync Device State]\(デバイスの状態の同期\) をクリックします。\
前のセクションで `turn on tv` でアプリをテストしたのであれば、テレビ番組が "オン" と表示されます。
    > [!div class="mx-imgBorder"]
    > ![デバイスの状態の同期](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. [Enable Microphone]\(マイクを有効にする\) を選択します
1. [Talk]/(トーク/) ボタンを選択します
1. `turn on the fan` と言います
1. ファンの表示状態が "オン" に変わるはずです
    > [!div class="mx-imgBorder"]
    > ![ファンをオンにする](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Custom Commands アプリケーションをリモートのスキルとしてエクスポートする](./how-to-custom-commands-integrate-remote-skills.md)

