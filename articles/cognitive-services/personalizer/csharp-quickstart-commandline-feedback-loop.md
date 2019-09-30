---
title: クイック スタート:.NET 用 Personalizer クライアント ライブラリ | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 学習ループを使用した .NET 用 Personalizer クライアント ライブラリの概要について説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 2fc9e91a34975642a666aa794694cfcc3321c93f
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962542"
---
# <a name="quickstart-personalize-client-library-for-net"></a>クイック スタート:.NET 用 Personalize クライアント ライブラリ

Personalizer サービスを使用するこの C# クイック スタートでパーソナライズしたコンテンツを表示します。

.NET 用 Personalizer クライアント ライブラリの概要 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。

 * パーソナル化のアクション一覧をランク付けする。
 * 上位にランクされたアクションの成功を示す報酬スコアをレポートする。

[リファレンスのドキュメント](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [サンプル](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* 最新バージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="setting-up"></a>設定

### <a name="create-a-personalizer-azure-resource"></a>Personalizer Azure リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) または [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、ローカル マシン上に Personalizer のリソースを作成します。 さらに、以下を実行できます。

* 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services)を無料で入手する。 これは、サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)で入手できます。  
* [Azure portal](https://portal.azure.com/) でご利用のリソースを表示する。

<!-- rename TBD_KEY to something meaningful for your service, like TEXT_ANALYTICS_KEY -->
試用版のサブスクリプションまたはリソースからキーを取得した後、[環境変数](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)を 2 つ作成します。

* `PERSONALIZER_RESOURCE_KEY` (リソース キー)。
* `PERSONALIZER_RESOURCE_ENDPOINT` (リソース エンドポイント)。

Azure portal では、キーとエンドポイントのどちらの値も **[クイック スタート]** ページで取得できます。

### <a name="change-the-model-update-frequency"></a>モデルの更新頻度を変更する

Azure portal の Personalizer リソースで、 **[モデルの更新頻度]** を 10 秒に変更します。 これでサービスが迅速にトレーニングされるようになり、繰り返しのたびに上位のアクションが変化する様子を確認できます。

![モデルの更新頻度を変更する](./media/settings/configure-model-update-frequency-settings.png)

Personalizer ループを初めてインスタンス化したときには、トレーニング元となる Reward API 呼び出しがないため、モデルは存在しません。 Rank 呼び出しは、各項目に対して等しい確率を返します。 それでも、アプリケーションは、RewardActionId の出力を使用して、常にコンテンツをランク付けする必要があります。

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

好みのエディターまたは IDE で、新しい .NET Core アプリケーションを作成します。 

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、dotnet `new` コマンドを使用し、`personalizer-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイル (`Program.cs`) を使用する単純な "Hello World" C# プロジェクトが作成されます。 

```console
dotnet new console -n personalizer-quickstart
```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドできます。

```console
dotnet build
```

ビルドの出力に警告やエラーが含まれないようにする必要があります。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-sdk"></a>SDK のインストール

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 Personalizer クライアント ライブラリをインストールします。

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0
```

Visual Studio IDE を使用している場合、クライアント ライブラリは、ダウンロード可能な NuGet パッケージとして入手できます。

## <a name="object-model"></a>オブジェクト モデル

Personalizer クライアントは、自分のキーが含まれている Microsoft.Rest.ServiceClientCredentials を使用して Azure に対する認証を行う [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) オブジェクトです。

コンテンツのランクを要求するには、[RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview) を作成し、それを [client.Rank](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) メソッドに渡します。 Rank メソッドから返される RankResponse に、ランク付けされたコンテンツが含まれています。 

Personalizer に報酬を送信するには、[RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview) を作成し、それを [client.Reward](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) メソッドに渡します。 

このクイックスタートでは、報酬を決定するにあたって大きな問題はありません。 実稼働システムでは、何がどの程度まで[報酬スコア](concept-rewards.md)に影響を及ぼすかを特定するのは複雑なプロセスとなる場合があり、そのプロセスはやがて変更することになる場合もあります。 実際の Personalizer アーキテクチャでは、その点を設計上の主要な意思決定に含めるようにしてください。 

## <a name="code-examples"></a>コード例

以下のコード スニペットは、.NET 用 Personalizer クライアント ライブラリを使用して次のことを実行する方法を示します。

* [Personalizer クライアントを作成する](#create-a-personalizer-client)
* [ランクを要求する](#request-a-rank)
* [報酬を送信する](#send-a-reward)

## <a name="add-the-dependencies"></a>依存関係を追加する

プロジェクト ディレクトリから、好みのエディターまたは IDE で **Program.cs** ファイルを開きます。 既存の `using` コードを次の `using` ディレクティブに置き換えます。

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Personalizer のリソース情報を追加する

**Program** クラスに、`PERSONALIZER_RESOURCE_KEY` および `PERSONALIZER_RESOURCE_ENDPOINT` という名前の環境変数から取得される、リソースの Azure キーとエンドポイントの変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、再読み込みしなければならない場合があります。 メソッドは、このクイックスタートの中で後から作成します。

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Personalizer クライアントを作成する

次に、Personalizer クライアントを返すメソッドを作成します。 このメソッドのパラメーターは `PERSONALIZER_RESOURCE_ENDPOINT` で、ApiKey は `PERSONALIZER_RESOURCE_KEY` です。

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-content-choices-represented-as-actions"></a>アクションとして表されるコンテンツの選択肢を取得する

アクションとは、Personalizer にランク付けさせたいコンテンツの選択肢を表します。 Program クラスに次のメソッドを追加します。時間帯と現在の食べ物の好みに関するユーザーの入力をコマンド ラインから取得するものです。

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

どちらのメソッドも、`GetKey` メソッドを使用して、ユーザーの選択内容をコマンド ラインから読み取ります。 

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>学習ループを作成する

Personalizer の学習ループとは、ランク呼び出しと報酬呼び出しのサイクルです。 このクイックスタートでは、コンテンツをパーソナライズするための各ランク呼び出しの後に、そのコンテンツへのランク付けにサービスがどの程度寄与したかを Personalizer に伝える報酬呼び出しを行います。 

次のコードは、プログラムの `main` メソッドです。コマンド ラインでユーザーに好みをたずね、その情報を Personalizer に送信してランク付けし、そのランク付けされた選択肢をリストから選択できるようユーザーに提示した後、選択のランク付けにサービスがどの程度寄与したかを報酬の形で Personalizer に伝達するサイクルをループで処理しています。

```csharp
static void Main(string[] args)
{
    int iteration = 1;
    bool runLoop = true;

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Initialize Personalizer client.
    PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

    do
    {
        Console.WriteLine("\nIteration: " + iteration++);

        // <rank>
        // Get context information from the user.
        string timeOfDayFeature = GetUsersTimeOfDay();
        string tasteFeature = GetUsersTastePreference();

        // Create current context from user specified data.
        IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature }
        };

        // Exclude an action for personalizer ranking. This action will be held at its current position.
        // This simulates a business rule to force the action "juice" to be ignored in the ranking.
        // As juice is excluded, the return of the API will always be with a probability of 0.
        IList<string> excludeActions = new List<string> { "juice" };

        // Generate an ID to associate with the request.
        string eventId = Guid.NewGuid().ToString();

        // Rank the actions
        var request = new RankRequest(actions, currentContext, excludeActions, eventId);
        RankResponse response = client.Rank(request);
        // </rank>

        Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

        // <reward>
        float reward = 0.0f;
        string answer = GetKey();

        if (answer == "Y")
        {
            reward = 1;
            Console.WriteLine("\nGreat! Enjoy your food.");
        }
        else if (answer == "N")
        {
            reward = 0;
            Console.WriteLine("\nYou didn't like the recommended food choice.");
        }
        else
        {
            Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
        }

        Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
        foreach (var rankedResponse in response.Ranking)
        {
            Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
        }

        // Send the reward for the action based on user response.
        client.Reward(response.EventId, new RewardRequest(reward));
        // </reward>

        Console.WriteLine("\nPress q to break, any other key to continue:");
        runLoop = !(GetKey() == "Q");

    } while (runLoop);
}
```

## <a name="request-a-rank"></a>ランクを要求する

ランク要求を実行するために、このプログラムは、ユーザーの好みをたずねてコンテンツの選択肢の `currentContent` を作成します。 このプロセスで、ランクから除外するコンテンツ (`excludeActions`) を作成することもできます。 ランク付けされた応答を取得するためには、ランク要求に actions、currentContext、excludeActions、ランクの一意の eventId (GUID) が必要です。 

このクイックスタートにおけるコンテキストのフィーチャーは、時間帯と食べ物に対するユーザーの好みという単純なものです。 実稼働システムでは、[アクションとフィーチャー](concepts-features.md)を決定し、[評価](concept-feature-evaluation.md)することが、決して簡単ではない場合もあります。  

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>報酬を送信する

報酬要求を完了するために、このプログラムは、ユーザーの選択をコマンド ラインから取得し、各選択に数値を割り当てた後、一意のランク イベント ID と数値を報酬メソッドに送信します。

このクイックスタートでは、0 または 1 という単純な数値を報酬として割り当てます。 実際のニーズにもよりますが、実稼働システムでは、いつ何を [Reward](concept-rewards.md) 呼び出しに送信するかが決して簡単な決定事項ではない場合もあります。 

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>プログラムの実行

アプリケーション ディレクトリから、dotnet `run` コマンドを使用してアプリケーションを実行します。

```console
dotnet run
```

![クイック スタート プログラムは、フィーチャーと呼ばれるユーザー設定を収集するためにいくつかの質問をしてから、最上位のアクションを提供します。](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[このクイックスタートのソース コード](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs)は、Personalizer のサンプル GitHub リポジトリから入手できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
>[Personalizer のしくみ](how-personalizer-works.md)

* [Personalizer とは](what-is-personalizer.md)
* [Personalizer を使用できる状況](where-can-you-use-personalizer.md)
* [トラブルシューティング](troubleshooting.md)

