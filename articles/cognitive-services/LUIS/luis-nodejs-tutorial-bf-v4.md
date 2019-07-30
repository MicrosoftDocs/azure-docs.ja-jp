---
title: Language Understanding ボット Node.js v4
titleSuffix: Azure Cognitive Services
description: Node.js を使用して、Language Understanding (LUIS) と統合されたチャット ボットを作成します。 このチャット ボットは、人事アプリを使用して、ボット ソリューションをすばやく実装します。 このボットは、Bot Framework バージョン 4 と Azure Web アプリ ボットで作成します。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: a06bd5a1a061de82230e93b867ea88e333b3cc93
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442551"
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
    |サブスクリプション|サブスクリプション ボットを作成するサブスクリプション。|プライマリ サブスクリプション。
    |リソース グループ|Azure リソースの論理グループ|このボットで使用するすべてのリソースを格納する新しいグループを作成し、グループに `luis-nodejs-bot-resource-group` という名前を付けます。|
    |Location|Azure リージョン - これは LUIS の作成または公開リージョンと同じである必要はありません。|`westus`|
    |価格レベル|サービス要求の制限と課金に使用されます。|`F0` は無料レベルです。
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
|なし|アプリのドメインの外側にあるものすべて。|

## <a name="test-the-bot-in-web-chat"></a>Web チャットでのボットのテスト

1. 引き続き Azure portal で、新しいボットに対して **[Test in Web Chat]\(Web チャットでのテスト\)** を選択します。 
1. **[Type your message]\(メッセージを入力\)** ボックスに、テキスト `hello` を入力します。 ボットは、パリへの航空便の予約など、特定の LUIS モデルのクエリの例に加えて、ボット フレームワークに関する情報で応答します。 

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

1. **ダイアログ -> luisHelper.js** ファイルを開きます。 これは、ボット内に入力されたユーザーの発話が LUIS に送信される場所です。 LUIS からの応答は、**bookDetails** JSON オブジェクトとしてメソッドから返されます。 独自のボットを作成する場合、LUIS から詳細を返すための独自のオブジェクトも作成する必要があります。 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

1. **ダイアログ -> bookingDialog.js** を開いて、会話フローを管理するために BookingDetails オブジェクトがどのように使用されているか把握します。 手順では、旅行の詳細が尋ねられ、予約全体が確認され、最後にユーザーに対して内容が再び繰り返されます。 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { ConfirmPrompt, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');
    const { CancelAndHelpDialog } = require('./cancelAndHelpDialog');
    const { DateResolverDialog } = require('./dateResolverDialog');
    
    const CONFIRM_PROMPT = 'confirmPrompt';
    const DATE_RESOLVER_DIALOG = 'dateResolverDialog';
    const TEXT_PROMPT = 'textPrompt';
    const WATERFALL_DIALOG = 'waterfallDialog';
    
    class BookingDialog extends CancelAndHelpDialog {
        constructor(id) {
            super(id || 'bookingDialog');
    
            this.addDialog(new TextPrompt(TEXT_PROMPT))
                .addDialog(new ConfirmPrompt(CONFIRM_PROMPT))
                .addDialog(new DateResolverDialog(DATE_RESOLVER_DIALOG))
                .addDialog(new WaterfallDialog(WATERFALL_DIALOG, [
                    this.destinationStep.bind(this),
                    this.originStep.bind(this),
                    this.travelDateStep.bind(this),
                    this.confirmStep.bind(this),
                    this.finalStep.bind(this)
                ]));
    
            this.initialDialogId = WATERFALL_DIALOG;
        }
    
        /**
         * If a destination city has not been provided, prompt for one.
         */
        async destinationStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            if (!bookingDetails.destination) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'To what city would you like to travel?' });
            } else {
                return await stepContext.next(bookingDetails.destination);
            }
        }
    
        /**
         * If an origin city has not been provided, prompt for one.
         */
        async originStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the response to the previous step's prompt
            bookingDetails.destination = stepContext.result;
            if (!bookingDetails.origin) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'From what city will you be travelling?' });
            } else {
                return await stepContext.next(bookingDetails.origin);
            }
        }
    
        /**
         * If a travel date has not been provided, prompt for one.
         * This will use the DATE_RESOLVER_DIALOG.
         */
        async travelDateStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.origin = stepContext.result;
            if (!bookingDetails.travelDate || this.isAmbiguous(bookingDetails.travelDate)) {
                return await stepContext.beginDialog(DATE_RESOLVER_DIALOG, { date: bookingDetails.travelDate });
            } else {
                return await stepContext.next(bookingDetails.travelDate);
            }
        }
    
        /**
         * Confirm the information the user has provided.
         */
        async confirmStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.travelDate = stepContext.result;
            const msg = `Please confirm, I have you traveling to: ${ bookingDetails.destination } from: ${ bookingDetails.origin } on: ${ bookingDetails.travelDate }.`;
    
            // Offer a YES/NO prompt.
            return await stepContext.prompt(CONFIRM_PROMPT, { prompt: msg });
        }
    
        /**
         * Complete the interaction and end the dialog.
         */
        async finalStep(stepContext) {
            if (stepContext.result === true) {
                const bookingDetails = stepContext.options;
    
                return await stepContext.endDialog(bookingDetails);
            } else {
                return await stepContext.endDialog();
            }
        }
    
        isAmbiguous(timex) {
            const timexPropery = new TimexProperty(timex);
            return !timexPropery.types.has('definite');
        }
    }
    
    module.exports.BookingDialog = BookingDialog;
    ```


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>依存関係をインストールして Visual Studio でボット コードを起動する

1. VSCode では、統合ターミナルからコマンド `npm install` を使用して依存関係をインストールします。
1. また、統合ターミナルからコマンド `npm start` を使用してボットを起動します。 


## <a name="use-the-bot-emulator-to-test-the-bot"></a>ボット エミュレーターを使用してボットをテストする

1. ボット エミュレーターを開始し、 **[Open Bot]\(ボットを開く\)** を選択します。
1. **[Open a bot]\(ボットを開く\)** ポップアップ ダイアログで、ボットの URL (`http://localhost:3978/api/messages` など) を入力します。 `/api/messages` ルートは、ボットの Web アドレスです。
1. **Microsoft アプリ ID** と **Microsoft アプリ パスワード**を入力します。これらは、ダウンロードしたボット コードのルートにある **.env** ファイル内にあります。

    オプションで、新しいボット構成を作成し、ボットの Visual Studio プロジェクトの **.env** ファイルから `MicrosoftAppId` と `MicrosoftAppPassword` をコピーできます。 ボット構成ファイルの名前は、ボット名と同じにする必要があります。 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from .env>",
                "appPassword": "<appPassword from .env>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. ボット エミュレーターで、「`Hello`」と入力します。これにより、 **[Test in Web Chat]\(Web チャットでのテスト\)** で受け取った応答と同じ基本ボットの応答を受け取ります。

    [![エミュレーターでの基本ボットの返答](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>ボットに航空便を予約する意図があることを伝える

1. ボット エミュレーターで、次の発話を入力して航空便を予約します。 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    ボット エミュレーターにより確認が求められます。 

1. **[はい]** を選択します。 ボットは、その操作の要約で応答します。 
1. ボット エミュレーターのログから、`Luis Trace` を含む行を選択します。 これにより、発話の意図とエンティティについて LUIS からの JSON 応答が表示されます。

    [![エミュレーターでの基本ボットの返答](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>次の手順

会話型ボットのある他の[サンプル](https://github.com/microsoft/botframework-solutions)を確認してください。 

> [!div class="nextstepaction"]
> [カスタムの主題の領域を使用して Language Understanding アプリを作成する](luis-quickstart-intents-only.md)