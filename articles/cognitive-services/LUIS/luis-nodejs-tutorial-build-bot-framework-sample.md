---
title: Bot - Node.js - v3
titleSuffix: Azure Cognitive Services
description: Bot Framework 3.x と Azure Web アプリ ボットを使用して、LUIS アプリケーションと統合されるボットを作成します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/30/2019
ms.author: diberry
ms.openlocfilehash: 27ebe827e97c8361c7f93a125f09aa9e339a6a14
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087110"
---
# <a name="luis-bot-in-nodejs-with-the-bot-framework-3x-and-the-azure-web-app-bot"></a>Bot Framework 3.x と Azure Web アプリ ボットを備えた Node.js の LUIS ボット

Node.js を使用して、Language Understanding (LUIS) と統合されたチャット ボットを作成します。 このチャット ボットでは、ボット ソリューションをすばやく実装するため、事前構築済みの HomeAutomation ドメインを使用します。 ボットは、Bot Framework 3.x と Azure Web アプリ ボットで構築します。

## <a name="prerequisite"></a>前提条件

Bot を作成する前に、[アプリの作成](./luis-get-started-create-app.md)に関するページの手順に従って、ボットで使用される LUIS アプリをビルドします。

ボットは、LUIS アプリにある HomeAutomation ドメインの意図に応答します。 これらの意図ごとに、LUIS アプリにマップされている意図が提供されます。 ボットが提供するダイアログでは、LUIS で検出された意図が処理されます。

| 意図 | 発話の例 | ボット機能 |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | 照明をつける。 | `HomeAutomation.TurnOn` が検出されたときに、ボットによって `TurnOnDialog` が呼びされます。 このダイアログで、IoT サービスを呼び出し、デバイスの電源を入れて、そのデバイスの電源が入ったことをユーザーに通知します。 |
| HomeAutomation.TurnOff | 寝室の照明を消す。 | `HomeAutomation.TurnOff` が検出されたときに、ボットによって `TurnOffDialog` が呼びされます。 このダイアログで、IoT サービスを呼び出し、デバイスの電源を切って、そのデバイスの電源が切れたことをユーザーに通知します。 |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>Bot Service での Language Understanding ボットの作成

1. [Azure portal](https://portal.azure.com) のメニュー ブレードで、**[新しいリソースの作成]** を選択し、**[すべて表示]** を選択します。

    ![Azure portal ですべてのリソースを表示する](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. 検索ボックスで、**Web アプリ ボット**を検索します。 

    ![Web アプリ ボットを選択してリソース作成プロセスを開始する](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. **[ボット サービス]** ブレードで、必要な情報を指定し、**[作成]** を選択します。 これによって、ボット サービスと LUIS アプリが作成され、Azure にデプロイされます。 [音声認識の準備](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)を使用するには、ボットを作成する前に、[リージョンの要件](troubleshooting.md#what-luis-regions-support-bot-framework-speech-priming)を確認します。 
   * **[アプリ名]** にボットの名前を設定します。 この名前は、ボットがクラウドにデプロイされるときに、サブドメインとして使用されます (mynotesbot.azurewebsites.net など)。 <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
   * サブスクリプション、[リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)、App Service プラン、[場所](https://azure.microsoft.com/regions/)を選択します。
   * **Bot テンプレート**で、以下を選択します。
       * **SDK v3**
       * **Node.js**
       * **言語の理解**
   * **LUIS アプリの場所**を選択します。 これは、アプリが作成されるオーサリング [リージョン][LUIS]です。
   * 法的通知の確認チェック ボックスをオンにします。 法的通知の条項はチェック ボックスの下にあります。

     ![[ボット サービス] ブレード](./media/luis-tutorial-node-bot/bot-service-setting-callout-template.png)


4. ボット サービスがデプロイされたことを確認します。
    * [通知] (Azure portal の上端にあるベル アイコン) を選択します。 通知が、**[デプロイが開始されました]** から **[デプロイメントに成功しました]** に変わります。
    * 通知が **[デプロイメントに成功しました]** に変わったら、その通知で **[リソースに移動]** を選択します。

## <a name="try-the-default-bot"></a>既定のボットを試す

ボットがデプロイされたことを確認するには、**[通知]** をオンにします。 通知が、**[デプロイは進行中です...]** から **[デプロイメントに成功しました]** に変わります。 **[リソースに移動]** を選択して、ボットのリソース ブレードを開きます。

<!-- this step isn't supposed to be necessary -->
## <a name="install-npm-resources"></a>NPM リソースのインストール
次の手順で NPM パッケージをインストールします。

1. Web アプリ ボットの **[Bot Management]\(ボットの管理\)** セクションで **[Build]\(ビルド\)** を選択します。 

2. 2 つ目のブラウザー ウィンドウが新しく開きます。 **[Open online code editor]\(オンライン コード エディターを開く\)** を選択します。

3. 上部のナビゲーション バーで、Web アプリ ボットの名前 `homeautomationluisbot` を選択します。 

4. ドロップダウン リストで、**[Kudu コンソールを開く]** を選択します。

5. 新しいブラウザー ウィンドウが開きます。 コンソールで、次のコマンドを入力します。

    ```console
    cd site\wwwroot && npm install
    ```

    インストール プロセスが完了するまで待ちます。 最初のブラウザー ウィンドウに戻ります。 

## <a name="test-in-web-chat"></a>Web チャットでのテスト
ボットが登録されたら、**[Test in Web Chat]\(Web チャットでのテスト\)** を選択して、Web チャット ウィンドウを開きます。 Web チャットに「hello」と入力します。

  ![Web チャットでのボットのテスト](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

ボットが "あいさつにリーチしました。 hello と言いました" と言って応答します。 これにより、ご自身のメッセージはボットによって受信され、そのボットが作成した既定の LUIS アプリに渡されたことが確認されます。 この既定の LUIS アプリによって、あいさつの意図が検出されました。 次の手順では、既定の LUIS アプリではなく前に作成した LUIS アプリにボットを接続します。

## <a name="connect-your-luis-app-to-the-bot"></a>ボットへの LUIS アプリの接続

最初のブラウザー ウィンドウで **[アプリケーションの設定]** を開き、**[LuisAppId]** フィールドを編集して、ご自身の LUIS アプリのアプリケーション ID を追加します。

  ![Azure での LUIS アプリ ID の更新](./media/luis-tutorial-node-bot/bot-service-app-id.png)

LUIS アプリ ID がない場合は、Azure へのログインに使用するアカウントと同じものを使って、[LUIS](luis-reference-regions.md) Web サイトにログインします。 **[My Apps]\(マイ アプリ\)** を選択します。 

1. HomeAutomation ドメインの意図とエンティティを含む、前に作成した LUIS アプリを検索します。

2. LUIS アプリの **[設定]** ページで、アプリ ID を検索してコピーします。

3. アプリをトレーニングしていない場合は、右上にある **[トレーニング]** を選択して、アプリをトレーニングします。

4. アプリを公開していない場合は、上部のナビゲーション バーで **[公開]** を選択し、**[公開]** ページを開きます。 [Production]\(運用\) スロットを選択し、**[Publish]\(公開\)** ボタンを選択します。

## <a name="modify-the-bot-code"></a>ボット コードの変更

2 番目のブラウザー ウィンドウがまだ開いている場合は、そのウィンドウに移動するか、または最初のブラウザー ウィンドウで、**[Build]\(ビルド\)**、**[Open online code editor]\(オンライン コード エディターを開く\)** の順に選択します。

   ![オンライン コード エディターを開く](./media/luis-tutorial-node-bot/bot-service-build.png)

コード エディターで、`app.js` を開きます。 これには以下のコードが含まれます。

```javascript
/*-----------------------------------------------------------------------------
A simple Language Understanding (LUIS) bot for the Microsoft Bot Framework. 
-----------------------------------------------------------------------------*/

var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// Add a dialog for each intent that the LUIS app recognizes.
// See https://docs.microsoft.com/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
bot.dialog('GreetingDialog',
    (session) => {
        session.send('You reached the Greeting intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Greeting'
})

bot.dialog('HelpDialog',
    (session) => {
        session.send('You reached the Help intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Help'
})

bot.dialog('CancelDialog',
    (session) => {
        session.send('You reached the Cancel intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Cancel'
})
```

app.js の既存の意図は無視されます。 これは、そのままにしておくことができます。 

## <a name="add-a-dialog-that-matches-homeautomationturnon"></a>HomeAutomation.TurnOn に一致するダイアログの追加

次のコードをコピーし、`app.js` に追加します。

```javascript
bot.dialog('TurnOn',
    (session) => {
        session.send('You reached the TurnOn intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOn'
})
```

ダイアログにアタッチされている [triggerAction][triggerAction] の [matches][matches] オプションによって、意図の名前が指定されます。 認識エンジンは、ボットがユーザーから発話を受信するたびに実行されます。 検出された最高スコアの意図が、ダイアログにバインドされた `triggerAction` と一致する場合、ボットによってそのダイアログが呼び出されます。

## <a name="add-a-dialog-that-matches-homeautomationturnoff"></a>HomeAutomation.TurnOff に一致するダイアログの追加

次のコードをコピーし、`app.js` に追加します。

```javascript
bot.dialog('TurnOff',
    (session) => {
        session.send('You reached the TurnOff intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOff'
})
```
## <a name="test-the-bot"></a>ボットのテスト

Azure portal で、**[Test in Web Chat]\(Web チャットでのテスト\)** を選択してボットをテストします。 "照明をつける"、"暖房を消す" などのメッセージを入力してみることで、追加した意図を呼び出します。
   ![Web チャットでの HomeAutomation ボットのテスト](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> 意図またはエンティティがボットによって必ずしも正しく認識されない場合は、発話の例をさらに追加して、LUIS アプリをトレーニングすることで、そのパフォーマンスを向上させます。 お使いのボットのコードを変更せずに、ご自身の LUIS アプリを再トレーニングできます。 [発話の例の追加](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances)に関するページ、および[ご自身の LUIS アプリのトレーニングとテスト](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test)に関するページをご覧ください。

## <a name="learn-more-about-bot-framework"></a>Bot Framework の詳細
[Bot Framework](https://dev.botframework.com/)、[3.x](https://github.com/Microsoft/BotBuilder) SDK、[4.x](https://github.com/Microsoft/botbuilder-js) SDK の詳細を確認します。

## <a name="next-steps"></a>次の手順

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.-->
ヘルプ、キャンセル、あいさつなど、他の意図を LUIS アプリに追加してみることができます。 その後、新しい意図のダイアログを追加し、ボットを使用してテストします。 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [意図の追加](./luis-how-to-add-intents.md)
> [音声認識の準備](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches


[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

