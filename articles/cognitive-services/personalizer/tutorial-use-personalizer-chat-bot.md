---
title: チャット ボットで Personalizer を使用する - Personalizer
description: Personalizer ループを使用して C# .NET チャット ボットをカスタマイズし、アクション (特徴を伴う) およびコンテキストの特徴に基づいてユーザーに適切なコンテンツを提供します。
ms.topic: tutorial
ms.date: 07/17/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ee037632b068b0d8d2f3ed3b6ea7a83d157c5083
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935655"
---
# <a name="tutorial-use-personalizer-in-net-chat-bot"></a>チュートリアル:.NET チャット ボットで Personalizer を使用する

C# .NET チャット ボットを Personalizer ループとともに使用して、ユーザーに適切なコンテンツを提供します。 このチャット ボットは、ユーザーに特定のコーヒーやお茶を提案します。 ユーザーはその提案を受け入れるか拒否することができます。 これにより、次回の提案をさらに適切にするために役立つ情報が Personalizer に提供されます。

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * Azure リソースの設定
> * ボットの構成と実行
> * ボット エミュレーターを使用したボットとの対話
> * ボットが Personalizer を使用する場面と方法の理解


## <a name="how-does-the-chat-bot-work"></a>チャット ボットが機能するしくみ

チャット ボットは一般的に、ユーザーとの会話のやり取りです。 この特定のチャット ボットは、Personalizer を使用して、ユーザーに提供する最良のアクション (コーヒーまたはお茶) を選択します。 Personalizer は強化学習を使用して、この選択を行います。

チャット ボットは会話のターンを管理する必要があります。 チャット ボットは [Bot Framework](https://github.com/microsoft/botframework-sdk) を使用してボットのアーキテクチャと会話を管理し、Cognitive Service の [Language Understanding](../LUIS/index.yml) (LUIS) を使用して、ユーザーからの自然言語の意図を理解します。

チャット ボットは、要求に応答するために使用できる特定のルートを持つ Web サイト `http://localhost:3978/api/messages` です。 ボット エミュレーターを使用すると、ボットをローカルで開発するときに、実行中のチャット ボットと視覚的に対話できます。

### <a name="user-interactions-with-the-bot"></a>ボットとユーザーのやり取り

これはテキスト クエリを入力できる単純なチャット ボットです。

|ユーザーが入力するテキスト|ボットが応答するテキスト|応答テキストを決定するためにボットが行うアクションの説明|
|--|--|--|
|テキスト入力なし - ボットが会話を開始する。|`This is a simple chatbot example that illustrates how to use Personalizer. The bot learns what coffee or tea order is preferred by customers given some context information (such as weather, temperature, and day of the week) and information about the user.`<br>`To use the bot, just follow the prompts. To try out a new imaginary context, type “Reset” and a new one will be randomly generated.`<br>`Welcome to the coffee bot, please tell me if you want to see the menu or get a coffee or tea suggestion for today. Once I’ve given you a suggestion, you can reply with ‘like’ or ‘don’t like’. It’s Tuesday today and the weather is Snowy.`|ボットは指示テキストで会話を開始し、ユーザーにコンテキスト (`Tuesday`、`Snowy`) を知らせます。|
|`Show menu`|`Here is our menu: Coffee: Cappuccino Espresso Latte Macchiato Mocha Tea: GreenTea Rooibos`|LUIS を使用してクエリの意図を判断し、コーヒーおよびお茶の項目のメニュー選択を表示します。 アクションの特徴 |
|`What do you suggest`|`How about Latte?`|LUIS を使用してクエリの意図を判断し、**Rank API** を呼び出して、質問として最上位の選択肢 `How about {response.RewardActionId}?` を表示します。 また、説明のために JSON 呼び出しと応答も表示します。|
|`I like it`|`That’s great! I’ll keep learning your preferences over time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|LUIS を使用してクエリの意図を判断し、`1` の報酬で **Reward API** を呼び出し、説明のために JSON 呼び出しと応答を表示します。|
|`I don't like it`|`Oh well, maybe I’ll guess better next time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|LUIS を使用してクエリの意図を判断し、`0` の報酬で **Reward API** を呼び出し、説明のために JSON 呼び出しと応答を表示します。|
|`Reset`|指示テキストを返します。|LUIS を使用してクエリの意図を判断し、指示テキストを表示してコンテキストをリセットします。|


### <a name="personalizer-in-this-bot"></a>このボットの Personalizer

このチャット ボットは、Personalizer を使用して、"_アクション_" (ある種のコンテンツ) とコンテキストの特徴のリストに基づいて、最上位のアクション (特定のコーヒーまたはお茶) を選択します。

ボットはアクションのリストを、コンテキストの特徴とともに Personalizer ループに送信します。 Personalizer は最適な 1 つのアクションをボットに返し、ボットはそれを表示します。

このチュートリアルでは、**アクション**は次の種類のコーヒーとお茶です。

|コーヒー|お茶|
|--|--|
|カプチーノ<br>エスプレッソ<br>ラテ<br>モカ|緑茶<br>ルイボス|

**Rank API:** Personalizer がユーザーのアクションについて学習できるようにするために、ボットは Rank API 要求ごとに次のものを送信します。

* アクション "_と特徴_"
* コンテキストの特徴

モデルの**特徴**は、チャット ボットのユーザーベースのメンバー間で集約 (グループ化) できるアクションまたはコンテキストに関する情報です。 特徴は、個別に特定されたもの (ユーザー ID など) でも、詳細に特定されたもの (正確な時刻など) でも "_ありません_"。

特徴は、アクションをモデル内の現在のコンテキストに合わせるために使用されます。 モデルは、アクション、コンテキスト、それらの特徴に関する Personalizer の過去の知識を表現したものであり、知識や経験に基づいた決定を可能にします。

特徴を含むモデルは、Azure portal の **[モデルの更新頻度]** 設定に基づいたスケジュールで更新されます。

特徴は、お客様の技術アーキテクチャのスキーマまたはモデルに適用するのと同じ計画と設計で選択しなければなりません。 特徴の値は、ビジネス ロジックまたはサードパーティのシステムで設定できます。

> [!CAUTION]
> このアプリケーション内の特徴はデモンストレーション用であり、ユーザーのユース ケースの Web アプリで使用するのに最適な特徴であるとは限りません。

#### <a name="action-features"></a>アクションの特徴

各アクション (コンテンツ項目) には、コーヒーまたはお茶の項目を区別するのに役立つ特徴があります。

これらの特徴は、Azure portal のループ構成の一部として構成されません。 代わりに、各 Rank API 呼び出しを使用して JSON オブジェクトとして送信されます。 これにより、アクションとその特徴を時間の経過と共に拡張、変更、縮小できる柔軟性が得られ、Personalizer は傾向を追跡できるようになります。

コーヒーとお茶の特徴は次のとおりです。

* ケニアやブラジルなどのコーヒー豆の産地
* コーヒーまたはお茶がオーガニックかどうか
* コーヒーの焙煎度 (浅煎りまたは深煎り)

コーヒーには上記のリストの 3 つの特徴がありますが、お茶には 1 つしかありません。 Personalizer には、アクションに対して意味のある特徴のみを渡してください。 アクションに適用されない特徴に対して空の値を渡さないでください。

#### <a name="context-features"></a>コンテキストの特徴

コンテキストの特徴は、表示デバイス、ユーザー、場所、ユーザーのユース ケースに関連するその他の特徴など、環境のコンテキストを Personalizer が理解するのに役立ちます。

このチャット ボットのコンテキストは次のとおりです。

* 天気の種類 (雪、雨、晴)
* 曜日

このチャット ボットでは、特徴の選択がランダムに行われます。 実際のボットでは、コンテキストの特徴に実際のデータを使用します。

### <a name="design-considerations-for-this-bot"></a>このボットの設計に関する考慮事項

この会話に関するいくつかの注意事項があります。
* **ボットの対話**:この会話は、単純なユース ケースでの順位と報酬を示すものであるため、非常に単純です。 これは Bot Framework SDK やエミュレーターの完全な機能を示すわけではありません。
* **Personalizer**:特徴は、使用状況をシミュレートするためにランダムに選択されます。 運用環境の Personalizer シナリオでは、特徴をランダム化しないでください。
* **Language Understanding (LUIS)** :LUIS モデルの少数の発話例は、このサンプルのみを対象としています。 運用環境の LUIS アプリケーションでは、使用する発話の例が少なすぎないようにしてください。


## <a name="install-required-software"></a>必要なソフトウェアのインストール
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。 ダウンロード可能なサンプル リポジトリには、.NET Core CLI を使用する必要がある場合の手順が含まれています。
- [Microsoft Bot Framework Emulator](https://aka.ms/botframeworkemulator) は、ボット開発者がローカル ホストで、またはトンネルを介したリモート実行時にボットをテストおよびデバッグできるデスクトップ アプリケーションです。

## <a name="download-the-sample-code-of-the-chat-bot"></a>チャット ボットのサンプル コードのダウンロード

このチャットボットは Personalizer サンプル リポジトリから入手できます。 リポジトリを複製または[ダウンロード](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/archive/master.zip)し、Visual Studio 2019 を使用して `/samples/ChatbotExample` ディレクトリのサンプルを開きます。

リポジトリを複製するには、Bash シェル (ターミナル) で次の Git コマンドを使用します。

```bash
git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
```

## <a name="create-and-configure-personalizer-and-luis-resources"></a>Personalizer および LUIS リソースの作成と構成

### <a name="create-azure-resources"></a>Azure リソースを作成する

このチャット ボットを使用するには、Personalizer および Language Understanding (LUIS) 用の Azure リソースを作成する必要があります。

* [LUIS リソースを作成します](../luis/luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal)。 作成と予測の両方のリソースが必要であるため、作成手順で **[両方]** を選択します。
* [Personalizer リソースを作成](how-to-create-resource.md)し、Azure portal からキーとエンドポイントをコピーします。 これらの値は、.NET プロジェクトの `appsettings.json` ファイルで設定する必要があります。

### <a name="create-luis-app"></a>LUIS アプリを作成する

LUIS を初めて使用する場合は、[サインイン](https://www.luis.ai)してアカウントをすぐに移行する必要があります。 新しいリソースを作成する必要はありません。代わりに、このチュートリアルの前のセクションで作成したリソースを選択します。

1. 新しい LUIS アプリケーションを作成するには、[LUIS ポータル](https://www.luis.ai)で、サブスクリプションと作成リソースを選択します。
1. 次に、同じページで、 **[+ New app for conversation]\(+ 会話用の新しいアプリ\)** を選択し、 **[Import as JSON]\(JSON としてインポート\)** を選択します。
1. ポップアップ ダイアログで **[ファイルの選択]** を選択し、`/samples/ChatbotExample/CognitiveModels/coffeebot.json` ファイルを選択します。 名前 `Personalizer Coffee bot` を入力します。
1. LUIS ポータルの右上のナビゲーションで、 **[Train]\(トレーニング\)** ボタンを選択します。
1. **[Publish]\(発行\)** ボタンを選択して、予測ランタイムの**運用スロット**にアプリを発行します。
1. **[Manage]\(管理\)** 、 **[Settings]\(設定\)** の順に選択します。 **[App ID]** の値をコピーします。 この値は、.NET プロジェクトの `appsettings.json` ファイルで設定する必要があります。
1. 再度 **[Manage]\(管理\)** セクションで、 **[Azure resources]\(Azure リソース\)** を選択します。 これにより、アプリの関連リソースが表示されます。
1. **[Add prediction resource]\(予測リソースの追加\)** を選択します。 ポップアップ ダイアログで、サブスクリプションと、このチュートリアルの前のセクションで作成した予測リソースを選択し、 **[Done]\(完了\)** を選択します。
1. **主キー**と**エンドポイント URL** の値をコピーします。 これらの値は、.NET プロジェクトの `appsettings.json` ファイルで設定する必要があります。

### <a name="configure-bot-with-appsettingsjson-file"></a>appsettings.json ファイルを使用してボットを構成する

1. Visual Studio 2019 を使用して、チャット ボット ソリューション ファイル `ChatbotSamples.sln` を開きます。
1. プロジェクトのルート ディレクトリで `appsettings.json` を開きます。
1. このチュートリアルの前のセクションでコピーした 5 つの設定をすべて設定します。

    ```json
    {
      "PersonalizerChatbot": {
        "LuisAppId": "",
        "LuisAPIKey": "",
        "LuisServiceEndpoint": "",
        "PersonalizerServiceEndpoint": "",
        "PersonalizerAPIKey": ""
      }
    }
    ```

### <a name="build-and-run-the-bot"></a>ボットをビルドして実行する

`appsettings.json` を構成すると、チャット ボットをビルドして実行する準備が整います。 実行すると、ブラウザーが開いて実行中の Web サイト `http://localhost:3978` が表示されます。

:::image type="content" source="media/tutorial-chat-bot/chat-bot-web-site.png" alt-text="チャットボット Web サイトを表示しているブラウザーのスクリーンショット。":::

このチュートリアルにはボットの動作内容が説明されているため、この Web サイトを実行したままにし、ボットと対話できるようにしてください。


## <a name="set-up-the-bot-emulator"></a>ボット エミュレーターをセットアップする

1. ボット エミュレーターを開き、 **[Open Bot]\(ボットを開く\)** を選択します。

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-startup.png" alt-text="ボット エミュレーターの起動画面のスクリーンショット。":::


1. 次の **[Bot URL]\(ボット URL\)** を使用してボットを構成し、 **[Connect]\(接続\)** を選択します。

    `http://localhost:3978/api/messages`

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-open-bot-settings.png" alt-text="ボット エミュレーターのボットを開く設定のスクリーンショット。":::

    エミュレーターはチャット ボットに接続し、ローカル開発に役立つログやデバッグ情報とともに、説明テキストを表示します。

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-bot-conversation-first-turn.png" alt-text="会話の最初のターンにおけるボット エミュレーターのスクリーンショット。":::

## <a name="use-the-bot-in-the-bot-emulator"></a>ボット エミュレーターでボットを使用する

1. `I would like to see the menu` と入力して、メニューを表示するように要求します。 チャット ボットによって項目が表示されます。
1. `Please suggest a drink for me.` と入力することによって、ボットが項目を提案するようにします。エミュレーターではチャット ウィンドウに順位の要求と応答が表示されるため、完全な JSON を確認できます。 ボットは `How about Latte?` のような提案を行います。
1. それが気に入った (`I like it.`) と回答します。これは、Personalizer の上位に順位付けされた選択を受け入れることを意味します。エミュレーターでは、報酬スコア 1 の報酬要求と応答がチャット ウィンドウに表示されるため、完全な JSON を確認できます。 ボットは `That’s great! I’ll keep learning your preferences over time.` および `Would you like to get a new suggestion or reset the simulated context to a new day?` と応答します

    選択に対して `no` で応答すると、報酬スコア 0 が Personalizer に送信されます。


## <a name="understand-the-net-code-using-personalizer"></a>Personalizer を使用する .NET コードについて理解する

.NET ソリューションは、単純なボット フレームワークのチャット ボットです。 Personalizer に関連するコードは、次のフォルダーにあります。
* `/samples/ChatbotExample/Bots`
    * ボットと Personalizer の間の対話のための `PersonalizerChatbot.cs` ファイル
* `/samples/ChatbotExample/ReinforcementLearning` - Personalizer モデルのアクションと特徴を管理します
* `/samples/ChatbotExample/Model` - Personalizer アクションおよび特徴のためのファイル、および LUIS インテントのためのファイル

### <a name="personalizerchatbotcs---working-with-personalizer"></a>PersonalizerChatbot.cs - Personalizer との連携

`PersonalizerChatbot` クラスは `Microsoft.Bot.Builder.ActivityHandler` から派生しています。 これには、会話フローを管理する 3 つのプロパティと、メソッドがあります。

> [!CAUTION]
> このチュートリアルのコードはコピーしないでください。 [Personalizer サンプル リポジトリ](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)のサンプル コードを使用してください。

```csharp
public class PersonalizerChatbot : ActivityHandler
{

    private readonly LuisRecognizer _luisRecognizer;
    private readonly PersonalizerClient _personalizerClient;

    private readonly RLContextManager _rlFeaturesManager;

    public PersonalizerChatbot(LuisRecognizer luisRecognizer, RLContextManager rlContextManager, PersonalizerClient personalizerClient)
            {
                _luisRecognizer = luisRecognizer;
                _rlFeaturesManager = rlContextManager;
                _personalizerClient = personalizerClient;
            }
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            // Check LUIS model
            var recognizerResult = await _luisRecognizer.RecognizeAsync(turnContext, cancellationToken);
            var topIntent = recognizerResult?.GetTopScoringIntent();
            if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
            {
                Intents intent = (Intents)Enum.Parse(typeof(Intents), topIntent.Value.intent);
                switch (intent)
                {
                    case Intents.ShowMenu:
                        await turnContext.SendActivityAsync($"Here is our menu: \n Coffee: {CoffeesMethods.DisplayCoffees()}\n Tea: {TeaMethods.DisplayTeas()}", cancellationToken: cancellationToken);
                        break;
                    case Intents.ChooseRank:
                        // Here we generate the event ID for this Rank.
                        var response = await ChooseRankAsync(turnContext, _rlFeaturesManager.GenerateEventId(), cancellationToken);
                        _rlFeaturesManager.CurrentPreference = response.Ranking;
                        await turnContext.SendActivityAsync($"How about {response.RewardActionId}?", cancellationToken: cancellationToken);
                        break;
                    case Intents.RewardLike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 1, cancellationToken);
                            await turnContext.SendActivityAsync($"That's great! I'll keep learning your preferences over time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you like. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.RewardDislike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 0, cancellationToken);
                            await turnContext.SendActivityAsync($"Oh well, maybe I'll guess better next time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you dislike. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.Reset:
                        _rlFeaturesManager.GenerateRLFeatures();
                        await SendResetMessageAsync(turnContext, cancellationToken);
                        break;
                    default:
                        break;
                }
            }
            else
            {
                var msg = @"Could not match your message with any of the following LUIS intents:
                        'ShowMenu'
                        'ChooseRank'
                        'RewardLike'
                        'RewardDislike'.
                        Try typing 'Show me the menu','What do you suggest','I like it','I don't like it'.";
                await turnContext.SendActivityAsync(msg);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Generate a new weekday and weather condition
            // These will act as the context features when we call rank with Personalizer
            _rlFeaturesManager.GenerateRLFeatures();

            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
    }

    // code removed for brevity, full sample code available for download
    private async Task SendWelcomeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendResetMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendByebyeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
    private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
}
```

`Send` で始まるメソッドは、ボットと LUIS との会話を管理します。 `ChooseRankAsync` と `RewardAsync` のメソッドは、Personalizer と対話します。

#### <a name="calling-rank-api-and-display-results"></a>Rank API の呼び出しと結果の表示

メソッド `ChooseRankAsync` は、特徴を含むアクションとコンテキストの特徴を収集することによって、Personalizer の Rank API に送信する JSON データを構築します。

```csharp
private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
{
    IList<object> contextFeature = new List<object>
    {
        new { weather = _rlFeaturesManager.RLFeatures.Weather.ToString() },
        new { dayofweek = _rlFeaturesManager.RLFeatures.DayOfWeek.ToString() },
    };

    Random rand = new Random(DateTime.UtcNow.Millisecond);
    IList<RankableAction> actions = new List<RankableAction>();
    var coffees = Enum.GetValues(typeof(Coffees));
    var beansOrigin = Enum.GetValues(typeof(CoffeeBeansOrigin));
    var organic = Enum.GetValues(typeof(Organic));
    var roast = Enum.GetValues(typeof(CoffeeRoast));
    var teas = Enum.GetValues(typeof(Teas));

    foreach (var coffee in coffees)
    {
        actions.Add(new RankableAction
        {
            Id = coffee.ToString(),
            Features =
            new List<object>()
            {
                new { BeansOrigin = beansOrigin.GetValue(rand.Next(0, beansOrigin.Length)).ToString() },
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
                new { Roast = roast.GetValue(rand.Next(0, roast.Length)).ToString() },
            },
        });
    }

    foreach (var tea in teas)
    {
        actions.Add(new RankableAction
        {
            Id = tea.ToString(),
            Features =
            new List<object>()
            {
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
            },
        });
    }

    // Sending a rank request to Personalizer
    // Here we are asking Personalizer to decide which drink the user is most likely to want
    // based on the current context features (weather, day of the week generated in RLContextManager)
    // and the features of the drinks themselves
    var request = new RankRequest(actions, contextFeature, null, eventId);
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL TO RANK =====\n" +
        "This is what is getting sent to Rank:\n" +
        $"{JsonConvert.SerializeObject(request, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    var response = await _personalizerClient.RankAsync(request, cancellationToken);
    await turnContext.SendActivityAsync(
        $"===== DEBUG MESSAGE RETURN FROM RANK =====\n" +
        "This is what Rank returned:\n" +
        $"{JsonConvert.SerializeObject(response, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    return response;
}
```

#### <a name="calling-reward-api-and-display-results"></a>Reward API の呼び出しと結果の表示

メソッド `RewardAsync` は、スコアを決定することによって、Personalizer の Reward API に送信する JSON データを構築します。 スコアは、ユーザーテキストで識別され、`OnTurnAsync` メソッドから送信される LUIS インテントから決定されます。

```csharp
private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
{
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL REWARD =====\n" +
        "Calling Reward:\n" +
        $"eventId = {eventId}, reward = {reward}\n",
        cancellationToken: cancellationToken);

    // Sending a reward request to Personalizer
    // Here we are responding to the drink ranking Personalizer provided us
    // If the user liked the highest ranked drink, we give a high reward (1)
    // If they did not, we give a low reward (0)
    await _personalizerClient.RewardAsync(eventId, new RewardRequest(reward), cancellationToken);
}
```

## <a name="design-considerations-for-a-bot"></a>ボットの設計に関する考慮事項

このサンプルは、ボットにおける Personalizer の簡単なエンドツーエンド ソリューションを示すことを目的としています。 実際のユース ケースはより複雑になる可能性があります。

運用環境のボットで Personalizer を使用する場合は、次のことを計画してください。
* 順位付けされた選択が必要になる "_たびに_" Personalizer にリアルタイムでアクセスできること。 Rank API は、バッチ処理もキャッシュもできません。  報酬呼び出しは、遅延させたり、別のプロセスにオフロードさせたりすることができ、ユーザーが指定された時間内に報酬を返さない場合、既定の報酬値がイベントに設定されます。
* ユースケースに基づく報酬の計算:この例では、0 と 1 の 2 つの報酬を示しました。ここで、それらの間に範囲がなく、スコアに負の値がありません。 お使いのシステムでは、より詳細なスコアリングが必要になります。
* ボット チャネル:このサンプルでは 1 つのチャネルを使用していますが、複数のチャネルを使用したり、1 つのチャネルでさまざまなボットを使用したりする場合は、Personalizer モデルのコンテキストの特徴の一部として考慮する必要がある場合があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを完了したら、次のリソースをクリーンアップします。

* サンプル プロジェクト ディレクトリを削除します。
* Azure portal で Personalizer と LUIS リソースを削除します。

## <a name="next-steps"></a>次のステップ
* [Personalizer のしくみ](how-personalizer-works.md)
* [特徴](concepts-features.md): アクションおよびコンテキストで使用する特徴に関する概念を学習する
* [報酬](concept-rewards.md): 報酬の計算について学習する
