---
title: チュートリアル:Language Understanding ボット Node.js v4
titleSuffix: Azure Cognitive Services
description: Node.js を使用して、Language Understanding (LUIS) と統合されたチャット ボットを作成します。 このチャット ボットは、人事アプリを使用して、ボット ソリューションをすばやく実装します。 このボットは、Bot Framework バージョン 4 と Azure Web アプリ ボットで作成します。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: diberry
ms.openlocfilehash: 8f0438ab015f9d16fd3776421b8d0032fc0a0639
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772902"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>チュートリアル:Node.js で Language Understanding に対応した Web アプリ ボットを使用する 

Node.js を使用して、Language Understanding (LUIS) と統合されたチャット ボットを作成します。 このボットは、Azure [Web アプリ ボット](https://docs.microsoft.com/azure/bot-service/) リソースと [Bot Framework バージョン](https://github.com/Microsoft/botbuilder-dotnet) V4 で作成します。

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * Web アプリ ボットを作成する。 このプロセスにより、新しい LUIS アプリが作成されます。
> * Web ボット サービスによって作成されたボット プロジェクトをダウンロードする
> * ボットとエミュレーターをコンピューター上でローカルに起動する
> * ボットの発話結果を表示する

## <a name="prerequisites"></a>前提条件

* [ボット エミュレーター](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Web アプリ ボット リソースを作成する

1. [Azure portal](https://portal.azure.com) で、 **[新しいリソースの作成]** を選択します。

1. 検索ボックスで、 **[Web アプリ ボット]** を検索して選択します。 **作成** を選択します。

1. **[ボット サービス]** で、必要な情報を指定します。

    |Setting|目的|推奨される設定|
    |--|--|--|
    |ボット名|リソース名|`luis-nodejs-bot-` + `<your-name>` (例: `luis-nodejs-bot-johnsmith`)|
    |Subscription|サブスクリプション ボットを作成するサブスクリプション。|プライマリ サブスクリプション。
    |Resource group|Azure リソースの論理グループ|このボットで使用するすべてのリソースを格納する新しいグループを作成し、グループに `luis-nodejs-bot-resource-group` という名前を付けます。|
    |Location|Azure リージョン - これは LUIS の作成または公開リージョンと同じである必要はありません。|`westus`|
    |Pricing tier|サービス要求の制限と課金に使用されます。|`F0` は無料レベルです。
    |アプリの名前|この名前は、ボットがクラウドにデプロイされるときに、サブドメインとして使用されます (humanresourcesbot.azurewebsites.net など)。|`luis-nodejs-bot-` + `<your-name>` (例: `luis-nodejs-bot-johnsmith`)|
    |ボット テンプレート|Bot Framework の設定 - 次の表を参照|
    |LUIS アプリの場所|LUIS リソース リージョンと同じである必要があります|`westus`|
    |App Service プラン/場所|指定されている既定値は変更しないでください。|
    |Application Insights|指定されている既定値は変更しないでください。|
    |Microsoft App ID and password (Microsoft アプリ ID とパスワード)|指定されている既定値は変更しないでください。|

1. **[ボット テンプレート]** で、次の項目を選択して、それらの設定の下にある **[選択]** ボタンを選択します。

    |Setting|目的|選択肢番号|
    |--|--|--|
    |SDK バージョン|Bot Framework のバージョン|**SDK v4**|
    |SDK 言語|ボットのプログラミング言語|**Node.JS**|
    |ボット|ボットの種類|**基本ボット**|
    
1. **作成** を選択します。 これによって、ボット サービスが作成され、Azure にデプロイされます。 このプロセスの一環として、`luis-nodejs-bot-XXXX` という LUIS アプリが作成されます。 この名前は、Azure Bot Service のアプリ名に基づきます。

    [![Web アプリ ボットの作成](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

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

1. ポップアップ ダイアログで、 **[Include app settings in the downloaded zip file?]\(ダウンロードする zip ファイルにアプリの設定を含めますか?\)** と尋ねられたら、 **[はい]** を選択します。 これで LUIS の設定が得られます。 

1. ソース コードが圧縮されている場合は、コードをダウンロードするリンクを示すメッセージが表示されます。 リンクを選択します。 

1. .zip ファイルをローカル コンピューターに保存し、ファイルを抽出します。 Visual Studio でプロジェクトを開きます。 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>LUIS に発話を送信し、応答を取得するコードを確認する

1. ユーザーの発話を LUIS の予測エンドポイントに送信するには、**dialogs の flightBookingRecognizer.js** ファイルを開きます。 これは、ボット内に入力されたユーザーの発話が LUIS に送信される場所です。 LUIS からの応答は、**executeLuisQuery** メソッドから返されます。  

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. **dialogs の mainDialog** は発話をキャプチャし、actStep メソッドで executeLuisQuery に送信します。


    ````javascript
    class MainDialog extends ComponentDialog {

        constructor(luisRecognizer, bookingDialog) {
            ...
            this.luisRecognizer = luisRecognizer;
            ...
        }


        ...

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {

            ...

            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);

            switch (LuisRecognizer.topIntent(luisResult)) {
                    case 'BookFlight':
                        // Extract the values for the composite entities from the LUIS result.
                        const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                        const toEntities = this.luisRecognizer.getToEntities(luisResult);
            
                        // Show a warning for Origin and Destination if we can't resolve them.
                        await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);
            
                        // Initialize BookingDetails with any entities we may have found in the response.
                        bookingDetails.destination = toEntities.airport;
                        bookingDetails.origin = fromEntities.airport;
                        bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                        console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));
            
                        // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                        return await stepContext.beginDialog('bookingDialog', bookingDetails);
            
                    case 'GetWeather':
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        const getWeatherMessageText = 'TODO: get weather flow here';
                        await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        break;
            
                    default:
                        // Catch all for unhandled intents
                        const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                        await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    }
            
                    return await stepContext.next();

        }

        ...

    }
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>ボット エミュレーターを使用してボットをテストする

航空便を予約する意図があることをボットに伝えます。

1. ボット エミュレーターを開始し、 **[Open Bot]\(ボットを開く\)** を選択します。
1. **[Open a bot]\(ボットを開く\)** ポップアップ ダイアログで、ボットの URL (`http://localhost:3978/api/messages` など) を入力します。 `/api/messages` ルートは、ボットの Web アドレスです。
1. **Microsoft アプリ ID** と **Microsoft アプリ パスワード**を入力します。これらは、ダウンロードしたボット コードのルートにある **.env** ファイル内にあります。

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