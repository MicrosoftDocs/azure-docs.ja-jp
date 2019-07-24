---
title: Bot - Node.js - v4
titleSuffix: Azure Cognitive Services
description: Node.js を使用して、Language Understanding (LUIS) と統合されたチャット ボットを作成します。 このチャット ボットは、人事アプリを使用して、ボット ソリューションをすばやく実装します。 このボットは、Bot Framework バージョン 4 と Azure Web アプリ ボットで作成します。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: diberry
ms.openlocfilehash: 54bae5548764ed1f89a2ffb7992eb222a058c706
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57403653"
---
# <a name="tutorial-luis-bot-in-nodejs-with-the-bot-framework-4x-and-the-azure-web-app-bot"></a>チュートリアル: Bot Framework 4.x と Azure Web アプリ ボットを備えた Node.js の LUIS ボット
Node.js を使用すると、Language Understanding (LUIS) と統合されたチャット ボットを作成することができます。 このボットは、HomeAutomation アプリを使用してボット ソリューションを実装します。 このボットは、[Bot Framework バージョン](https://github.com/Microsoft/botbuilder-js) v4 と Azure [Web アプリ ボット](https://docs.microsoft.com/azure/bot-service/)で作成します。

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * Web アプリ ボットを作成する。 このプロセスにより、新しい LUIS アプリが作成されます。
> * 事前構築済みのドメインを新しい LUIS モデルに追加する
> * Web ボット サービスによって作成されたプロジェクトをダウンロードする
> * ボットとエミュレーターをコンピューター上でローカルに起動する
> * 新しい LUIS 意図に合わせてボット コードを変更する
> * ボットの発話結果を表示する

## <a name="prerequisites"></a>前提条件

* [ボット エミュレーター](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-web-app-bot"></a>Web アプリ ボットを作成する

1. [Azure portal](https://portal.azure.com) で、**[新しいリソースの作成]** を選択します。

2. 検索ボックスで、**[Web アプリ ボット]** を検索して選択します。 **作成**を選択します。

3. **[ボット サービス]** で、必要な情報を指定します。

    |Setting|目的|推奨される設定|
    |--|--|--|
    |ボット名|リソース名|`luis-nodejs-bot-` + `<your-name>` (例: `luis-nodejs-bot-johnsmith`)|
    |サブスクリプション|サブスクリプション ボットを作成するサブスクリプション。|プライマリ サブスクリプション。
    |リソース グループ|Azure リソースの論理グループ|このボットで使用するすべてのリソースを格納する新しいグループを作成し、グループに `luis-nodejs-bot-resource-group` という名前を付けます。|
    |Location|Azure リージョン - これは LUIS の作成または公開リージョンと同じである必要はありません。|`westus`|
    |価格レベル |サービス要求の制限と課金に使用されます。|`F0` は無料レベルです。
    |アプリの名前|この名前は、ボットがクラウドにデプロイされるときに、サブドメインとして使用されます (humanresourcesbot.azurewebsites.net など)。|`luis-nodejs-bot-` + `<your-name>` (例: `luis-nodejs-bot-johnsmith`)|
    |ボット テンプレート|Bot Framework の設定 - 次の表を参照|
    |LUIS アプリの場所|LUIS リソース リージョンと同じである必要があります|`westus`|

4. **[Bot template settings]\(ボット テンプレートの設定)** で、次の項目を選択して、それらの設定の下にある **[選択]** ボタンを選択します。

    |Setting|目的|選択肢番号|
    |--|--|--|
    |SDK バージョン|Bot Framework のバージョン|**SDK v4**|
    |SDK 言語|ボットのプログラミング言語|**Node.js**|
    |エコー/基本ボット|ボットの種類|**基本ボット**|
    
5. **作成**を選択します。 これによって、ボット サービスが作成され、Azure にデプロイされます。 このプロセスの一環として、`luis-nodejs-bot-XXXX` という LUIS アプリが作成されます。 この名前は、前のセクションのボットとアプリの名前に基づいています。

    [![Web アプリ ボットの作成](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

6. このブラウザー タブは開いたままにしておきます。 LUIS ポータルを使用したどの手順でも、新しいブラウザー タブを開きます。新しいボット サービスがデプロイされたら、次のセクションに進みます。

## <a name="add-prebuilt-domain-to-model"></a>事前構築済みのドメインをモデルに追加する
ボット サービスのデプロイの一部では、意図と発話の例を含む新しい LUIS アプリを作成します。 このボットは、以下の意図のために新しい LUIS アプリへの意図のマッピングを行います。 

|基本的なボット LUIS の意図|発話の例|
|--|--|
|Cancel|`stop`|
|Greeting|`hello`|
|[Help]|`help`|
|なし|アプリのドメインの外側にあるものすべて。|

事前構築済みの HomeAutomation アプリをモデルに追加して、`Turn off the living room lights` のような発話を処理します。

1. [LUIS](https://www.luis.ai) ポータルに移動し、サインインします。
2. **[マイ アプリ]** ページで **[作成日]** 列を選択して、アプリが作成された日付順に並べ替えます。 Azure Bot Service では、前のセクションで新しいアプリを作成しました。 その名前は、`luis-nodejs-bot-` + `<your-name>` + 4 つのランダムな文字です。
3. アプリを開き、上部のナビゲーションの **ビルド** セクションを選択します。
4. 左側のナビゲーションから **[Prebuilt Domains]\(事前構築済みドメイン)** を選択します。
5. そのカード上の **[ドメインの追加]** を選択して、**HomeAutomation** ドメインを選択します。
6. 右上のメニューの **[トレーニング]** を選択します。
7. 右上のメニューの **[発行]** を選択します。 

    これで、Azure Bot Service によって作成されたアプリに新しい意図が組み込まれました。

    |基本ボットの新しい意図|発話の例|
    |--|--|
    |HomeAutomation.TurnOn|`turn the fan to high`
    |HomeAutomation.TurnOff|`turn off ac please`|

## <a name="download-the-web-app-bot"></a>Web アプリ ボットをダウンロードする 
Web アプリ ボットのコードを開発するためには、コードをダウンロードし、ローカル コンピューターで使用します。 

1. Azure portal で、引き続き Web アプリ ボットのリソースに対して、**[アプリケーションの設定]** を選択し、**botFilePath** および **botFileSecret** の値をコピーします。 これらは後で環境ファイルに追加する必要があります。 

2. Azure portal の **[ボットの管理]** セクションで **[ビルド]** を選択します。 

3. **[ボットのソース コードをダウンロードする]** を選択します。 

    [![基本ボットの Web アプリ ボットのソース コードのダウンロード](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

4. ソース コードが圧縮されている場合は、コードをダウンロードするリンクを示すメッセージが表示されます。 リンクを選択します。 

5. .zip ファイルをローカル コンピューターに保存し、ファイルを抽出します。 プロジェクトを開きます。 

6. bot.js ファイルを開き、`const results = await this.luisRecognizer.recognize(context);` を検索します。 これは、ボット内に入力されたユーザーの発話が LUIS に送信される場所です。

   ```javascript
    /**
     * Driver code that does one of the following:
     * 1. Display a welcome card upon startup
     * 2. Use LUIS to recognize intents
     * 3. Start a greeting dialog
     * 4. Optionally handle Cancel or Help interruptions
     *
     * @param {Context} context turn context from the adapter
     */
    async onTurn(context) {
        // Create a dialog context
        const dc = await this.dialogs.createContext(context);

        if(context.activity.type === ActivityTypes.Message) {
            // Perform a call to LUIS to retrieve results for the current activity message.
            const results = await this.luisRecognizer.recognize(context);
            
            const topIntent = LuisRecognizer.topIntent(results);

            // handle conversation interrupts first
            const interrupted = await this.isTurnInterrupted(dc, results);
            if(interrupted) {
                return;
            }

            // Continue the current dialog
            const dialogResult = await dc.continue();

            switch(dialogResult.status) {
                case DialogTurnStatus.empty:
                    switch (topIntent) {
                        case GREETING_INTENT:
                            await dc.begin(GREETING_DIALOG);
                            break;

                        case NONE_INTENT:
                        default:
                            // help or no intent identified, either way, let's provide some help
                            // to the user
                            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
                            break;
                    }

                case DialogTurnStatus.waiting:
                    // The active dialog is waiting for a response from the user, so do nothing
                break;

                case DialogTurnStatus.complete:
                    await dc.end();
                    break;

                default:
                    await dc.cancelAll();
                    break;

            }

        } else if (context.activity.type === 'conversationUpdate' && context.activity.membersAdded[0].name === 'Bot') {
            // When activity type is "conversationUpdate" and the member joining the conversation is the bot
            // we will send our Welcome Adaptive Card.  This will only be sent once, when the Bot joins conversation
            // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
            const welcomeCard = CardFactory.adaptiveCard(WelcomeCard);
            await context.sendActivity({ attachments: [welcomeCard] });
        }
    }
    ```

    ボットがユーザーの発話を LUIS に送信し、結果を取得します。 最上位の意図が、会話フローを決定します。 


## <a name="start-the-bot"></a>ボットを起動する
コードや設定を変更する前に、ボットの動作を確認します。 

1. Visual Studio Code で、ターミナル ウィンドウを開きます。 

2. このボットの npm 依存関係をインストールします。 

    ```bash
    npm install
    ```
3. ボットのコードが探す環境変数を保持するためのファイルを作成します。 このファイルには `.env` という名前を付けます。 以下の環境変数を追加します。

    <!--there is no code language that represents an .env file correctly-->
    ```env
    botFilePath=
    botFileSecret=
    ```

    環境変数の値を、「**[Web アプリ ボットをダウンロードする](#download-the-web-app-bot)**」セクションの手順 1 で Azure Bot Service の [アプリケーションの設定] からコピーした値に設定します。

4. ボットをウォッチ モードで起動します。 この起動の後でコードに変更を加えると、アプリが自動的に再起動されます。

    ```bash
    npm run watch
    ```

5. ボットが起動すると、ターミナル ウィンドウに、ボットが実行されているローカル ポートが表示されます。

    ```console
    > basic-bot@0.1.0 start C:\Users\pattiowens\repos\BFv4\luis-nodejs-bot-src
    > node ./index.js NODE_ENV=development

    restify listening to http://[::]:3978
    
    Get the Emulator: https://aka.ms/botframework-emulator
    
    To talk to your bot, open the luis-nodejs-bot-pattiowens.bot file in the Emulator
    ```

## <a name="start-the-emulator"></a>エミュレーターを起動する

1. ボット エミュレーターを開始します。 

2. ボット エミュレーターで、プロジェクトのルートにある *.bot ファイルを選択します。 この `.bot` ファイルには、メッセージに対するボットの URL エンドポイントが含まれています。

    [![Bot emulator v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png)](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. 「**[Web アプリ ボットをダウンロードする](#download-the-web-app-bot)**」セクションの手順 1 で Azure Bot Service の [アプリケーションの設定] からコピーした、ボット シークレットを入力します。 これにより、エミュレーターが .bot ファイル内の暗号化されたフィールドにアクセスできるようになります。

    ![Bot emulator secret v4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)


4. ボット エミュレーターで、`Hello` と入力し、基本ボットの適切な返答を取得します。

    [![エミュレーターでの基本ボットの返答](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png)](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>ボットのコードを変更する 

`bot.js` ファイルで、新しい意図を処理するコードを追加します。 

1. ファイルの先頭で **[Supported LUIS Intents]\(サポートされている LUIS の意図)** セクションを見つけて、HomeAutomation の意図の定数を追加します。

   ```javascript
    // Supported LUIS Intents
    const GREETING_INTENT = 'Greeting';
    const CANCEL_INTENT = 'Cancel';
    const HELP_INTENT = 'Help';
    const NONE_INTENT = 'None';
    const TURNON_INTENT = 'HomeAutomation_TurnOn'; // new intent
    const TURNOFF_INTENT = 'HomeAutomation_TurnOff'; // new intent
    ```

    LUIS ポータルのアプリからのドメインと意図の間のピリオド (`.`) が、アンダースコア (`_`) に置き換えられていることに注意してください。 

2. 発話の LUIS の予測を受信する **isTurnInterrupted** を見つけて、結果をコンソールに出力するための行を追加します。

   ```javascript
    /**
     * Look at the LUIS results and determine if we need to handle
     * an interruptions due to a Help or Cancel intent
     *
     * @param {DialogContext} dc - dialog context
     * @param {LuisResults} luisResults - LUIS recognizer results
     */
    async isTurnInterrupted(dc, luisResults) {
        console.log(JSON.stringify(luisResults));
    ...
    ```

    このボットには LUIS REST API 要求とまったく同じ返答はないため、応答 JSON を調べて違いを学習することが重要です。 テキストと意図のプロパティは同じですが、エンティティのプロパティ値は変更されています。 

    ```json
    {
        "$instance": {
            "HomeAutomation_Device": [
                {
                    "startIndex": 23,
                    "endIndex": 29,
                    "score": 0.9776345,
                    "text": "lights",
                    "type": "HomeAutomation.Device"
                }
            ],
            "HomeAutomation_Room": [
                {
                    "startIndex": 12,
                    "endIndex": 22,
                    "score": 0.9079433,
                    "text": "livingroom",
                    "type": "HomeAutomation.Room"
                }
            ]
        },
        "HomeAutomation_Device": [
            "lights"
        ],
        "HomeAutomation_Room": [
            "livingroom"
        ]
    }
    ```

3. `DialogTurnStatus.empty` の場合の onTurn メソッドの switch ステートメントに、意図を追加します。

   ```javascript
    switch (topIntent) {
        case GREETING_INTENT:
            await dc.begin(GREETING_DIALOG);
            break;

        // New HomeAutomation.TurnOn intent
        case TURNON_INTENT: 

            await dc.context.sendActivity(`TurnOn intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        // New HomeAutomation.TurnOff intent
        case TURNOFF_INTENT: 

            await dc.context.sendActivity(`TurnOff intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        case NONE_INTENT:
        default:
            // help or no intent identified, either way, let's provide some help
            // to the user
            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
            break;
    }
    ```

## <a name="view-results-in-bot"></a>ボットで結果を表示する

1. ボット エミュレーターで、発話 `Turn on the livingroom lights to 50%` を入力します。

2. ボットは次のように返答します。

    ```json
    TurnOn intent found, entities included: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```

## <a name="learn-more-about-bot-framework"></a>Bot Framework の詳細
Azure Bot Service は、Bot Framework SDK を使用します。 SDK と Bot Framework の詳細を確認してください。

* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4 のドキュメント
* [Bot Builder のサンプル](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder SDK](https://docs.microsoft.com/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Bot Builder ツール](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>次の手順

Azure Bot Service を作成し、ボット シークレットと .bot ファイル パスをコピーし、コードの zip ファイルをダウンロードしました。 事前構築済みの HomeAutomation ドメイン LUIS アプリを、新しい Azure Bot Service の一部として作成した LUIS アプリに追加してから、アプリを再度トレーニングして発行しました。 コード プロジェクトを抽出し、環境ファイル (`.env`) を作成して、ボット シークレットと .bot ファイル パスを設定しました。 bot.js ファイルで、2 つの新しい意図を処理するコードを追加しました。 その後、ボット エミュレーターでボットをテストし、新しい意図の 1 つの発話に対する LUIS の返答を設定しました。 


> [!div class="nextstepaction"]
> [LUIS でカスタム ドメインをビルドする](luis-quickstart-intents-only.md)
