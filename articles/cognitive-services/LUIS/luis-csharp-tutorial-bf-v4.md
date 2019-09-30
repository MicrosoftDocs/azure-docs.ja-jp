---
title: チュートリアル:Language Understanding ボット C# v4
titleSuffix: Azure Cognitive Services
description: C# を使用して、Language Understanding (LUIS) と統合されたチャット ボットを作成します。 このボットは、Bot Framework バージョン 4 と Azure Web アプリ ボット サービスで作成します。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: diberry
ms.openlocfilehash: 0911747da38ed736a79e692fd511e5bfbfaf7439
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772911"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>チュートリアル:C# で Language Understanding に対応した Web アプリ ボットを使用する

C# を使用して、Language Understanding (LUIS) と統合されたチャット ボットを作成します。 このボットは、Azure [Web アプリ ボット](https://docs.microsoft.com/azure/bot-service/) リソースと [Bot Framework バージョン](https://github.com/Microsoft/botbuilder-dotnet) V4 で作成します。

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * Web アプリ ボットを作成する。 このプロセスにより、新しい LUIS アプリが作成されます。
> * Web ボット サービスによって作成されたボット プロジェクトをダウンロードする
> * ボットとエミュレーターをコンピューター上でローカルに起動する
> * ボットの発話結果を表示する

## <a name="prerequisites"></a>前提条件

* [ボット エミュレーター](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Web アプリ ボット リソースを作成する

1. [Azure portal](https://portal.azure.com) で、 **[新しいリソースの作成]** を選択します。

1. 検索ボックスで、 **[Web アプリ ボット]** を検索して選択します。 **作成** を選択します。

1. **[ボット サービス]** で、必要な情報を指定します。

    |Setting|目的|推奨される設定|
    |--|--|--|
    |ボット名|リソース名|`luis-csharp-bot-` + `<your-name>` (例: `luis-csharp-bot-johnsmith`)|
    |Subscription|サブスクリプション ボットを作成するサブスクリプション。|プライマリ サブスクリプション。
    |Resource group|Azure リソースの論理グループ|このボットで使用するすべてのリソースを格納する新しいグループを作成し、グループに `luis-csharp-bot-resource-group` という名前を付けます。|
    |Location|Azure リージョン - これは LUIS の作成または公開リージョンと同じである必要はありません。|`westus`|
    |Pricing tier|サービス要求の制限と課金に使用されます。|`F0` は無料レベルです。
    |アプリの名前|この名前は、ボットがクラウドにデプロイされるときに、サブドメインとして使用されます (humanresourcesbot.azurewebsites.net など)。|`luis-csharp-bot-` + `<your-name>` (例: `luis-csharp-bot-johnsmith`)|
    |ボット テンプレート|Bot Framework の設定 - 次の表を参照|
    |LUIS アプリの場所|LUIS リソース リージョンと同じである必要があります|`westus`|
    |App Service プラン/場所|指定されている既定値は変更しないでください。|
    |Application Insights|指定されている既定値は変更しないでください。|
    |Microsoft App ID and password (Microsoft アプリ ID とパスワード)|指定されている既定値は変更しないでください。|

1. **[ボット テンプレート]** で、次の項目を選択して、それらの設定の下にある **[選択]** ボタンを選択します。

    |Setting|目的|選択肢番号|
    |--|--|--|
    |SDK バージョン|Bot Framework のバージョン|**SDK v4**|
    |SDK 言語|ボットのプログラミング言語|**C#**|
    |ボット|ボットの種類|**基本ボット**|
    
1. **作成** を選択します。 これによって、ボット サービスが作成され、Azure にデプロイされます。 このプロセスの一環として、`luis-csharp-bot-XXXX` という LUIS アプリが作成されます。 この名前は、Azure Bot Service のアプリ名に基づきます。

    [![Web アプリ ボットの作成](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    ボット サービスが作成されるまで待ち、次に進みます。

## <a name="the-bot-has-a-language-understanding-model"></a>ボットの Language Understanding モデル

ボット サービスの作成プロセスにより、意図と発話の例を含む新しい LUIS アプリも作成されます。 このボットは、以下の意図のために新しい LUIS アプリへの意図のマッピングを行います。 

|基本的なボット LUIS の意図|発話の例|
|--|--|
|航空便の予約|`Travel to Paris`|
|Cancel|`bye`|
|GetWeather|`what's the weather like?`|
|なし|アプリのドメインの外側にあるものすべて。|

## <a name="test-the-bot-in-web-chat"></a>Web チャットでのボットのテスト

1. 引き続き Azure portal で、新しいボットに対して **[Test in Web Chat]\(Web チャットでのテスト\)** を選択します。 
1. **[Type your message]\(メッセージを入力\)** ボックスに、テキスト `Book a flight from Seattle to Berlin tomorrow` を入力します。 このボットは、フライトを予約したい旨の確認で応答します。 

    ![Azure portal のスクリーンショット。テキスト「hello」を入力しています。](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    ボットをすばやくテストするために、テスト機能を使用できます。 デバッグを含む詳細なテストを行う場合、ボット コードをダウンロードし、Visual Studio を使用します。 

## <a name="download-the-web-app-bot-source-code"></a>Web アプリ ボットのソース コードをダウンロードする
Web アプリ ボットのコードを開発するためには、コードをダウンロードし、ローカル コンピューターで使用します。 

1. Azure portal の **[ボットの管理]** セクションで **[ビルド]** を選択します。 

1. **[ボットのソース コードをダウンロードする]** を選択します。 

    [![基本ボットの Web アプリ ボットのソース コードのダウンロード](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. ポップアップ ダイアログで、 **[Include app settings in the downloaded zip file?]\(ダウンロードする zip ファイルにアプリの設定を含めますか?\)** と尋ねられたら、 **[はい]** を選択します。

1. ソース コードが圧縮されている場合は、コードをダウンロードするリンクを示すメッセージが表示されます。 リンクを選択します。 

1. .zip ファイルをローカル コンピューターに保存し、ファイルを抽出します。 Visual Studio でプロジェクトを開きます。 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>LUIS に発話を送信し、応答を取得するコードを確認する

1. ユーザーの発話を LUIS の予測エンドポイントに送信するには、**FlightBookingRecognizer.cs** ファイルを開きます。 これは、ボット内に入力されたユーザーの発話が LUIS に送信される場所です。 LUIS からの応答は、**RecognizeAsync** メソッドから返されます。  

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    
    namespace Microsoft.BotBuilderSamples
    {
        public class FlightBookingRecognizer : IRecognizer
        {
            private readonly LuisRecognizer _recognizer;
    
            public FlightBookingRecognizer(IConfiguration configuration)
            {
                var luisIsConfigured = !string.IsNullOrEmpty(configuration["LuisAppId"]) && !string.IsNullOrEmpty(configuration["LuisAPIKey"]) && !string.IsNullOrEmpty(configuration["LuisAPIHostName"]);
                if (luisIsConfigured)
                {
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]);
    
                    _recognizer = new LuisRecognizer(luisApplication);
                }
            }
    
            // Returns true if luis is configured in the appsettings.json and initialized.
            public virtual bool IsConfigured => _recognizer != null;
    
            public virtual async Task<RecognizerResult> RecognizeAsync(ITurnContext turnContext, CancellationToken cancellationToken)
                => await _recognizer.RecognizeAsync(turnContext, cancellationToken);
    
            public virtual async Task<T> RecognizeAsync<T>(ITurnContext turnContext, CancellationToken cancellationToken)
                where T : IRecognizerConvert, new()
                => await _recognizer.RecognizeAsync<T>(turnContext, cancellationToken);
        }
    }
    ````

1. **Dialogs の MainDialog.cs** を開きます。MainDialog.cs は発話をキャプチャし、actStep メソッドで executeLuisQuery に送信します。 

    ```csharp
    public class MainDialog : ComponentDialog
    {
        private readonly FlightBookingRecognizer _luisRecognizer;

        ...

        public MainDialog(FlightBookingRecognizer luisRecognizer, BookingDialog bookingDialog, ILogger<MainDialog> logger)
                    : base(nameof(MainDialog))
        {
            _luisRecognizer = luisRecognizer;
            ...
        }

        private async Task<DialogTurnResult> ActStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
        {
            if (!_luisRecognizer.IsConfigured)
            {
                // LUIS is not configured, we just run the BookingDialog path with an empty BookingDetailsInstance.
                return await stepContext.BeginDialogAsync(nameof(BookingDialog), new BookingDetails(), cancellationToken);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt.)
            var luisResult = await _luisRecognizer.RecognizeAsync<FlightBooking>(stepContext.Context, cancellationToken);
            switch (luisResult.TopIntent().intent)
            {
                case FlightBooking.Intent.BookFlight:
                    await ShowWarningForUnsupportedCities(stepContext.Context, luisResult, cancellationToken);

                    // Initialize BookingDetails with any entities we may have found in the response.
                    var bookingDetails = new BookingDetails()
                    {
                        // Get destination and origin from the composite entities arrays.
                        Destination = luisResult.ToEntities.Airport,
                        Origin = luisResult.FromEntities.Airport,
                        TravelDate = luisResult.TravelDate,
                    };

                    // Run the BookingDialog giving it whatever details we have from the LUIS call, it will fill out the remainder.
                    return await stepContext.BeginDialogAsync(nameof(BookingDialog), bookingDetails, cancellationToken);

                case FlightBooking.Intent.GetWeather:
                    // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                    var getWeatherMessageText = "TODO: get weather flow here";
                    var getWeatherMessage = MessageFactory.Text(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(getWeatherMessage, cancellationToken);
                    break;

                default:
                    // Catch all for unhandled intents
                    var didntUnderstandMessageText = $"Sorry, I didn't get that. Please try asking in a different way (intent was {luisResult.TopIntent().intent})";
                    var didntUnderstandMessage = MessageFactory.Text(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(didntUnderstandMessage, cancellationToken);
                    break;
            }

            return await stepContext.NextAsync(null, cancellationToken);
        }
        
        ...

    }
    ```

## <a name="start-the-bot-code-in-visual-studio"></a>Visual Studio でボット コードを起動する

Visual Studio 2019 でボットを起動します。 ブラウザー ウィンドウが開いて、Web アプリ ボットの Web サイト (`http://localhost:3978/`) が表示されます。 ホーム ページには、ボットに関する情報が表示されます。

![ホーム ページには、ボットに関する情報が表示されます。](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>ボット エミュレーターを使用してボットをテストする

1. ボット エミュレーターを開始し、 **[Open Bot]\(ボットを開く\)** を選択します。
1. **[Open a bot]\(ボットを開く\)** ポップアップ ダイアログで、ボットの URL (`http://localhost:3978/api/messages` など) を入力します。 `/api/messages` ルートは、ボットの Web アドレスです。
1. **Microsoft アプリ ID** と **Microsoft アプリ パスワード**を入力します。これらは、ダウンロードしたボット コードのルートにある **appsettings.json** ファイル内にあります。


1. ボット エミュレーターで、「`Book a flight from Seattle to Berlin tomorrow`」と入力します。これにより、 **[Test in Web Chat]\(Web チャットでのテスト\)** で受け取った応答と同じ基本ボットの応答を受け取ります。

    [![エミュレーターでの基本ボットの返答](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. **[はい]** を選択します。 ボットは、その操作の要約で応答します。 
1. ボット エミュレーターのログから、`Luis Trace` を含む行を選択します。 これにより、発話の意図とエンティティについて LUIS からの JSON 応答が表示されます。

    [![エミュレーターでの基本ボットの返答](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]


## <a name="next-steps"></a>次の手順

会話型ボットのある他の[サンプル](https://github.com/microsoft/botframework-solutions)を確認してください。 

> [!div class="nextstepaction"]
> [カスタムの主題の領域を使用して Language Understanding アプリを作成する](luis-quickstart-intents-only.md)
