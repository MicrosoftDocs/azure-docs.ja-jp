---
title: Bot - C# - v4
titleSuffix: Language Understanding - Azure Cognitive Services
description: C# を使用して、Language Understanding (LUIS) と統合されたチャット ボットを作成します。 このチャット ボットは、人事アプリを使用して、ボット ソリューションをすばやく実装します。 このボットは、Bot Framework バージョン 4 と Azure Web アプリ ボットで作成します。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 028c06924e41606ba1d4e0b15fe26f2b7270db3c
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960303"
---
# <a name="tutorial-luis-bot-in-c-with-the-bot-framework-4x-and-the-azure-web-app-bot"></a>チュートリアル: Bot Framework 4.x と Azure Web アプリ ボットを使用した C# の LUIS ボット
C# を使用して、Language Understanding (LUIS) と統合されたチャット ボットを作成することができます。 このボットは、HomeAutomation アプリを使用してボット ソリューションを実装します。 このボットは、[Bot Framework バージョン](https://github.com/Microsoft/botbuilder-js) v4 と Azure [Web アプリ ボット](https://docs.microsoft.com/azure/bot-service/)で作成します。

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
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-web-app-bot"></a>Web アプリ ボットを作成する

1. [Azure portal](https://portal.azure.com) で、**[新しいリソースの作成]** を選択します。

2. 検索ボックスで、**[Web アプリ ボット]** を検索して選択します。 **作成**を選択します。

3. **[ボット サービス]** で、必要な情報を指定します。

    |Setting|目的|推奨される設定|
    |--|--|--|
    |ボット名|リソース名|`luis-csharp-bot-` + `<your-name>` (例: `luis-csharp-bot-johnsmith`)|
    |サブスクリプション|サブスクリプション ボットを作成するサブスクリプション。|プライマリ サブスクリプション。
    |リソース グループ|Azure リソースの論理グループ|このボットで使用するすべてのリソースを格納する新しいグループを作成し、グループに `luis-csharp-bot-resource-group` という名前を付けます。|
    |Location|Azure リージョン - これは LUIS の作成または公開リージョンと同じである必要はありません。|`westus`|
    |価格レベル |サービス要求の制限と課金に使用されます。|`F0` は無料レベルです。
    |アプリの名前|この名前は、ボットがクラウドにデプロイされるときに、サブドメインとして使用されます (humanresourcesbot.azurewebsites.net など)。|`luis-csharp-bot-` + `<your-name>` (例: `luis-csharp-bot-johnsmith`)|
    |ボット テンプレート|Bot Framework の設定 - 次の表を参照|
    |LUIS アプリの場所|LUIS リソース リージョンと同じである必要があります|`westus`|

4. **[Bot template settings]\(ボット テンプレートの設定)** で、次の項目を選択して、それらの設定の下にある **[選択]** ボタンを選択します。

    |Setting|目的|選択肢番号|
    |--|--|--|
    |SDK バージョン|Bot Framework のバージョン|**SDK v4**|
    |SDK 言語|ボットのプログラミング言語|**C#**|
    |エコー/基本ボット|ボットの種類|**基本ボット**|
    
5. **作成**を選択します。 これによって、ボット サービスが作成され、Azure にデプロイされます。 このプロセスの一環として、`luis-csharp-bot-XXXX` という LUIS アプリが作成されます。 この名前は、前のセクションのボットとアプリの名前に基づいています。

    [![Web アプリ ボットの作成](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

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
2. **[マイ アプリ]** ページで **[作成日]** 列を選択して、アプリが作成された日付順に並べ替えます。 Azure Bot Service では、前のセクションで新しいアプリを作成しました。 その名前は、`luis-csharp-bot-` + `<your-name>` + 4 つのランダムな文字です。
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

6. bot.cs ファイルを開き、`_services.LuisServices` を検索します。 これは、ボット内に入力されたユーザーの発話が LUIS に送信される場所です。

    ```csharp
    /// <summary>
    /// Run every turn of the conversation. Handles orchestration of messages.
    /// </summary>
    /// <param name="turnContext">Bot Turn Context.</param>
    /// <param name="cancellationToken">Task CancellationToken.</param>
    /// <returns>A <see cref="Task"/> representing the asynchronous operation.</returns>
    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    {
        var activity = turnContext.Activity;

        if (activity.Type == ActivityTypes.Message)
        {
            // Perform a call to LUIS to retrieve results for the current activity message.
            var luisResults = await _services.LuisServices[LuisConfiguration].RecognizeAsync(turnContext, cancellationToken).ConfigureAwait(false);

            // If any entities were updated, treat as interruption.
            // For example, "no my name is tony" will manifest as an update of the name to be "tony".
            var topScoringIntent = luisResults?.GetTopScoringIntent();

            var topIntent = topScoringIntent.Value.intent;
            switch (topIntent)
            {
                case GreetingIntent:
                    await turnContext.SendActivityAsync("Hello.");
                    break;
                case HelpIntent:
                    await turnContext.SendActivityAsync("Let me try to provide some help.");
                    await turnContext.SendActivityAsync("I understand greetings, being asked for help, or being asked to cancel what I am doing.");
                    break;
                case CancelIntent:
                    await turnContext.SendActivityAsync("I have nothing to cancel.");
                    break;
                case NoneIntent:
                default:
                    // Help or no intent identified, either way, let's provide some help.
                    // to the user
                    await turnContext.SendActivityAsync("I didn't understand what you just said to me.");
                    break;
            }
        }
        else if (activity.Type == ActivityTypes.ConversationUpdate)
        {
            if (activity.MembersAdded.Any())
            {
                // Iterate over all new members added to the conversation.
                foreach (var member in activity.MembersAdded)
                {
                    // Greet anyone that was not the target (recipient) of this message.
                    // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
                    if (member.Id != activity.Recipient.Id)
                    {
                        var welcomeCard = CreateAdaptiveCardAttachment();
                        var response = CreateResponse(activity, welcomeCard);
                        await turnContext.SendActivityAsync(response).ConfigureAwait(false);
                    }
                }
            }
        }

    }
    ```

    ボットがユーザーの発話を LUIS に送信し、結果を取得します。 最上位の意図が、会話フローを決定します。 


## <a name="start-the-bot"></a>ボットを起動する
コードや設定を変更する前に、ボットの動作を確認します。 

1. Visual Studio でソリューション ファイルを開きます。 

2. ボットのコードが探すボット変数を保持するための `appsettings.json` ファイルを作成します。

    ```JSON
    {
    "botFileSecret": "",
    "botFilePath": ""

    }
    ```

    変数の値を、「**[Web アプリ ボットをダウンロードする](#download-the-web-app-bot)**」セクションの手順 1 で Azure Bot Service の [アプリケーションの設定] からコピーした値に設定します。

3. Visual Studio で、ボットを起動します。 ブラウザー ウィンドウが開いて、Web アプリ ボットの Web サイト (`http://localhost:3978/`) が表示されます。

## <a name="start-the-emulator"></a>エミュレーターを起動する

1. ボット エミュレーターを開始します。

2. ボット エミュレーターで、プロジェクトのルートにある *.bot ファイルを選択します。 この `.bot` ファイルには、メッセージに対するボットの URL エンドポイントが含まれています。

    [![Bot emulator v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png)](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. 「**[Web アプリ ボットをダウンロードする](#download-the-web-app-bot)**」セクションの手順 1 で Azure Bot Service の [アプリケーションの設定] からコピーした、ボット シークレットを入力します。 これにより、エミュレーターが `.bot` ファイル内の暗号化されたフィールドにアクセスできるようになります。

    ![Bot emulator secret v4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)

4. ボット エミュレーターで、`Hello` と入力し、基本ボットの適切な返答を取得します。

    [![エミュレーターでの基本ボットの返答](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png)](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>ボットのコードを変更する 

`BasicBot.cs` ファイルで、新しい意図を処理するコードを追加します。 

1. ファイルの先頭で **[Supported LUIS Intents]\(サポートされている LUIS の意図)** セクションを見つけて、HomeAutomation の意図の定数を追加します。

    ```csharp
    // Supported LUIS Intents
    public const string GreetingIntent = "Greeting";
    public const string CancelIntent = "Cancel";
    public const string HelpIntent = "Help";
    public const string NoneIntent = "None";
    public const string TurnOnIntent = "HomeAutomation_TurnOn"; // new intent
    public const string TurnOffIntent = "HomeAutomation_TurnOff"; // new intent
    ```

    LUIS ポータルのアプリからのドメインと意図の間のピリオド (`.`) が、アンダースコア (`_`) に置き換えられていることに注意してください。 

2. 発話の LUIS の予測を受信する **OnTurnAsync** メソッドを見つけます。 2 つの HomeAutomation の意図に対する LUIS の返答を返すため、switch ステートメントのコードを追加します。 

    ```csharp
    case TurnOnIntent:
        await turnContext.SendActivityAsync("TurnOn intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    case TurnOffIntent:
        await turnContext.SendActivityAsync("TurnOff intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    ```

    このボットには LUIS REST API 要求とまったく同じ返答はないため、応答 JSON を調べて違いを学習することが重要です。 テキストと意図のプロパティは同じですが、エンティティのプロパティ値は変更されています。 

    ```JSON
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



## <a name="view-results-in-bot"></a>ボットで結果を表示する

1. ボット エミュレーターで、発話 `Turn on the livingroom lights to 50%` を入力します。

2. ボットは次のように返答します。

    ```JSON
    TurnOn intent found, JSON response: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```    

## <a name="learn-more-about-bot-framework"></a>Bot Framework の詳細
Azure Bot Service は、Bot Framework SDK を使用します。 SDK と Bot Framework の詳細を確認してください。

* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4 のドキュメント
* [Bot Builder のサンプル](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder SDK](https://docs.microsoft.com/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Bot Builder ツール](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>次の手順

Azure ボット サービスを作成し、ボット シークレットと `.bot` ファイル パスをコピーし、コードの zip ファイルをダウンロードしました。 事前構築済みの HomeAutomation ドメイン LUIS アプリを、新しい Azure Bot Service の一部として作成した LUIS アプリに追加してから、アプリを再度トレーニングして発行しました。 コード プロジェクトを抽出し、環境ファイル (`.env`) を作成して、ボット シークレットと `.bot` ファイル パスを設定しました。 bot.js ファイルで、2 つの新しい意図を処理するコードを追加しました。 その後、ボット エミュレーターでボットをテストし、新しい意図の 1 つの発話に対する LUIS の返答を設定しました。 

会話型ボットのある他の[サンプル](https://github.com/Microsoft/AI)を確認してください。 

> [!div class="nextstepaction"]
> [LUIS でカスタム ドメインをビルドする](luis-quickstart-intents-only.md)
