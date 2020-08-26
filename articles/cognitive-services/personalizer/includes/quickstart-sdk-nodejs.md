---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file, devx-track-javascript
ms.date: 07/30/2020
ms.openlocfilehash: ba7885859adc1d9899c66917204306c8a0d0092f
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246106"
---
[リファレンスのドキュメント](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [パッケージ (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* 最新バージョンの [Node.js](https://nodejs.org) および NPM。

## <a name="using-this-quickstart"></a>このクイックスタートの使用法


このクイックスタートを使用するには、次のようないくつかの手順があります。

* Azure portal で、Personalizer リソースを作成する
* Azure portal の Personalizer リソースの **[構成]** ページで、モデルの更新頻度を非常に短い間隔に変更する
* コード エディターで、コード ファイルを作成し、コード ファイルを編集する
* コマンド ラインまたはターミナルで、コマンド ラインから SDK をインストールする
* コマンド ラインまたはターミナルで、コード ファイルを実行する

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。

```console
mkdir myapp && cd myapp
```

`npm init -y` コマンドを実行して、`package.json` ファイルを作成します。

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Personalizer 用 Node.js ライブラリをインストールする

次のコマンドを使用して、Node.js 用 Personalizer クライアント ライブラリをインストールします。

```console
npm install @azure/cognitiveservices-personalizer --save
```

このクイックスタートの残りの NPM パッケージをインストールします。

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>オブジェクト モデル

Personalizer クライアントは、自分のキーが含まれている Microsoft.Rest.ServiceClientCredentials を使用して Azure に対する認証を行う [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) オブジェクトです。

最適なコンテンツ項目を 1 つだけ要求するには、[RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest) を作成し、それを [client.Rank](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) メソッドに渡します。 Rank メソッドにより、RankResponse が返されます。

Personalizer に報酬を送信するには、[RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest) を作成し、それを Events クラスの [Reward](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) メソッドに渡します。

このクイックスタートでは、報酬を決定するにあたって大きな問題はありません。 実稼働システムでは、何がどの程度まで[報酬スコア](../concept-rewards.md)に影響を及ぼすかを特定するのは複雑なプロセスとなる場合があり、そのプロセスはやがて変更することになる場合もあります。 実際の Personalizer アーキテクチャでは、その点を設計上の主要な意思決定に含めるようにしてください。

## <a name="code-examples"></a>コード例

以下のコード スニペットは、Node.js 用 Personalizer クライアント ライブラリを使用して次のことを実行する方法を示します。

* [Personalizer クライアントを作成する](#create-a-personalizer-client)
* [Rank API](#request-the-best-action)
* [Reward API](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

お気に入りのエディターまたは IDE で、`sample.js` という名前の新しい Node.js アプリケーションを作成します。

## <a name="add-the-dependencies"></a>依存関係を追加する

好みのエディターまたは IDE で、**sample.js** ファイルを開きます。 次の `requires` を追加して、NPM パッケージを追加します。

[!code-javascript[Add module dependencies](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Personalizer のリソース情報を追加する

コード ファイルの先頭近くにあるキーとエンドポイントの変数を、リソースの Azure キーとエンドポイントに編集します。 

[!code-javascript[Add Personalizer resource information](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Personalizer クライアントを作成する

次に、Personalizer クライアントを返すメソッドを作成します。 このメソッドのパラメーターは `PERSONALIZER_RESOURCE_ENDPOINT` で、ApiKey は `PERSONALIZER_RESOURCE_KEY` です。

[!code-javascript[Create a Personalizer client](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>アクションとして表されるコンテンツの選択肢を取得する

アクションはコンテンツの選択肢を表します。Personalizer を使用して、この中から最適なコンテンツ項目を選択します。 一連のアクションとそのフィーチャーを表す次のメソッドを Program クラスに追加します。

[!code-javascript[Create user features](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>学習ループを作成する

Personalizer の学習ループとは、[Rank](#request-the-best-action) 呼び出しと [Reward](#send-a-reward) 呼び出しのサイクルです。 このクイックスタートでは、コンテンツをパーソナライズするための各 Rank 呼び出しの後に Reward 呼び出しを行って、サービスがどの程度適切に実行されたかを Personalizer に伝えます。

次のコードでは、コマンド ラインでユーザーに好みをたずね、その情報を Personalizer に送信して最適なアクションを選択し、その選択をリストから選択できるようユーザーに提示した後、その選択にサービスがどの程度寄与したかを示す報酬を Personalizer に送る形でサイクルをループで処理しています。

[!code-javascript[Create the learning loop](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=mainLoop)]

rank 呼び出しと reward 呼び出しについて、以降の各セクションでさらに詳しく見ていきましょう。

コード ファイルを実行する前に、[コンテンツの選択肢](#get-content-choices-represented-as-actions)を取得する次のメソッドを追加します。

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>最適なアクションを要求する

Rank 要求を実行するために、このプログラムは、ユーザーの好みをたずねてコンテンツの選択肢を作成します。 このプロセスで、アクションから除外するコンテンツ (`excludeActions`) を作成することもできます。 優先度付けされた応答を取得するためには、Rank 要求に[アクション](../concepts-features.md#actions-represent-a-list-of-options)とそのフィーチャー、currentContext フィーチャー、excludeActions、一意の Rank イベント ID が必要です。

このクイックスタートにおけるコンテキストのフィーチャーは、時間帯と食べ物に対するユーザーの好みという単純なものです。 実稼働システムでは、[アクションとフィーチャー](../concepts-features.md)を決定し、[評価](../concept-feature-evaluation.md)することが、決して簡単ではない場合もあります。

[!code-javascript[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=rank)]

## <a name="send-a-reward"></a>報酬を送信する


Reward 要求で送信する報酬スコアを取得するめに、このプログラムは、ユーザーの選択をコマンド ラインから取得し、選択に数値を割り当てた後、一意のイベント ID と報酬スコア (数値) を Reward API に送信します。

このクイックスタートでは、0 または 1 という単純な数値を報酬スコアとして割り当てます。 実際のニーズにもよりますが、実稼働システムでは、いつ何を [Reward](../concept-rewards.md) 呼び出しに送信するかが決して簡単な決定事項ではない場合もあります。

[!code-javascript[The Personalizer learning loop sends a reward.](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=reward)]

## <a name="run-the-program"></a>プログラムの実行

アプリケーション ディレクトリから Node.js を使用して、アプリケーションを実行します。

```console
node sample.js
```

![クイック スタート プログラムは、フィーチャーと呼ばれるユーザー設定を収集するためにいくつかの質問をしてから、最上位のアクションを提供します。](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
