---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 07/30/2020
ms.openlocfilehash: fdae79912e6fe3bf2f7d55b7405cb7883e484c47
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602257"
---
[リファレンスのドキュメント](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* 最新バージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="using-this-quickstart"></a>このクイックスタートの使用法

このクイックスタートを使用するには、次のようないくつかの手順があります。

* Azure portal で、Personalizer リソースを作成する
* Azure portal の Personalizer リソースの **[構成]** ページで、モデルの更新頻度を非常に短い間隔に変更する
* コード エディターで、コード ファイルを作成し、コード ファイルを編集する
* コマンド ラインまたはターミナルで、コマンド ラインから SDK をインストールする
* コマンド ラインまたはターミナルで、コード ファイルを実行する

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

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

## <a name="install-the-sdk"></a>SDK のインストール

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 Personalizer クライアント ライブラリをインストールします。

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Visual Studio IDE を使用している場合、クライアント ライブラリは、ダウンロード可能な NuGet パッケージとして入手できます。

## <a name="object-model"></a>オブジェクト モデル

Personalizer クライアントは、自分のキーが含まれている Microsoft.Rest.ServiceClientCredentials を使用して Azure に対する認証を行う [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) オブジェクトです。

最適なコンテンツ項目を 1 つだけ要求するには、[RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview) を作成し、それを [client.Rank](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) メソッドに渡します。 Rank メソッドにより、RankResponse が返されます。

Personalizer に報酬スコアを送信するには、[RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview) を作成し、それを [client.Reward](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) メソッドに渡します。

このクイックスタートでは、報酬スコアの決定は重要ではありません。 実稼働システムでは、何がどの程度まで[報酬スコア](../concept-rewards.md)に影響を及ぼすかを特定するのは複雑なプロセスとなる場合があり、そのプロセスはやがて変更することになる場合もあります。 実際の Personalizer アーキテクチャでは、この設計上の意思決定を主要な意思決定に含めるようにしてください。

## <a name="code-examples"></a>コード例

以下のコード スニペットは、.NET 用 Personalizer クライアント ライブラリを使用して次のタスクを実行する方法を示します。

* [Personalizer クライアントを作成する](#create-a-personalizer-client)
* [Rank API](#request-the-best-action)
* [Reward API](#send-a-reward)

## <a name="add-the-dependencies"></a>依存関係を追加する

プロジェクト ディレクトリから、好みのエディターまたは IDE で **Program.cs** ファイルを開きます。 既存の `using` コードを次の `using` ディレクティブに置き換えます。

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Personalizer のリソース情報を追加する

**Program** クラスで、コード ファイルの先頭近くにあるキーとエンドポイントの変数を、リソースの Azure キーとエンドポイントに編集します。 

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Personalizer クライアントを作成する

次に、Personalizer クライアントを返すメソッドを作成します。 このメソッドのパラメーターは `PERSONALIZER_RESOURCE_ENDPOINT` で、ApiKey は `PERSONALIZER_RESOURCE_KEY` です。

[!code-csharp[Create the Personalizer client](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>食品をランク付け可能なアクションとして取得する

アクションはコンテンツの選択肢を表します。Personalizer を使用して、この中から最適なコンテンツ項目を選択します。 一連のアクションとそのフィーチャーを表す次のメソッドを Program クラスに追加します。 

[!code-csharp[Food items as actions](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>コンテキストに対するユーザーの好みを取得する

Program クラスに次のメソッドを追加します。時間帯と現在の食べ物の好みに関するユーザーの入力をコマンド ラインから取得するものです。 これらはコンテキストのフィーチャーとして使用されます。

[!code-csharp[Present time out day preference to the user](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=createUserFeatureTastePreference)]

どちらのメソッドも、`GetKey` メソッドを使用して、ユーザーの選択内容をコマンド ラインから読み取ります。

[!code-csharp[Read user's choice from the command line](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>学習ループを作成する

Personalizer の学習ループとは、[Rank](#request-the-best-action) 呼び出しと [Reward](#send-a-reward) 呼び出しのサイクルです。 このクイックスタートでは、コンテンツをパーソナライズするための各 Rank 呼び出しの後に Reward 呼び出しを行って、サービスがどの程度適切に実行されたかを Personalizer に伝えます。

次のコードでは、コマンド ラインでユーザーに好みをたずね、その情報を Personalizer に送信して最適なアクションを選択し、その選択をリストから選択できるようユーザーに提示した後、その選択にサービスがどの程度寄与したかを示す報酬スコアを Personalizer に送る形でサイクルをループで処理しています。

[!code-csharp[Learning loop](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=mainLoop)]

コード ファイルを実行する前に、[コンテンツの選択肢](#get-food-items-as-rankable-actions)を取得する次のメソッドを追加します。

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>最適なアクションを要求する

Rank 要求を実行するために、このプログラムは、ユーザーの好みをたずねてコンテンツの選択肢の `currentContext` を作成します。 このプロセスで、アクションから除外するコンテンツ (`excludeActions`) を作成することもできます。 応答を取得するためには、Rank 要求にアクションとそのフィーチャー、currentContext フィーチャー、excludeActions、一意のイベント ID が必要です。

このクイックスタートにおけるコンテキストのフィーチャーは、時間帯と食べ物に対するユーザーの好みという単純なものです。 実稼働システムでは、[アクションとフィーチャー](../concepts-features.md)を決定し、[評価](../concept-feature-evaluation.md)することが、決して簡単ではない場合もあります。

[!code-csharp[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=rank)]

## <a name="send-a-reward"></a>報酬を送信する

Reward 要求で送信する報酬スコアを取得するめに、このプログラムは、ユーザーの選択をコマンド ラインから取得し、選択に数値を割り当てた後、一意のイベント ID と報酬スコア (数値) を Reward API に送信します。

このクイックスタートでは、0 または 1 という単純な数値を報酬スコアとして割り当てます。 実際のニーズにもよりますが、実稼働システムでは、いつ何を [Reward](../concept-rewards.md) 呼び出しに送信するかが決して簡単な決定事項ではない場合もあります。

[!code-csharp[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=reward)]

## <a name="run-the-program"></a>プログラムの実行

アプリケーション ディレクトリから、dotnet `run` コマンドを使用してアプリケーションを実行します。

```console
dotnet run
```

![クイック スタート プログラムは、フィーチャーと呼ばれるユーザー設定を収集するためにいくつかの質問をしてから、最上位のアクションを提供します。](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[こちらでこのクイックスタート用のソース コード](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)を入手できます。
