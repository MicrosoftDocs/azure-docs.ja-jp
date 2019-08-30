---
title: クイック スタート:Node.js 用 Personalizer クライアント ライブラリ | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 学習ループを使用した Node.js 用 Personalizer クライアント ライブラリの概要について説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 08/13/2019
ms.author: diberry
ms.openlocfilehash: fbd86698438e09a0c94c06e0e79800b8d38b57c1
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69544799"
---
# <a name="quickstart-personalize-client-library-for-nodejs"></a>クイック スタート:Node.js 用 Personalize クライアント ライブラリ

Personalizer サービスを使用するこの Node.js クイックスタートでパーソナライズしたコンテンツを表示します。

Node.js 用 Personalizer クライアント ライブラリの概要 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。

 * パーソナル化のアクション一覧をランク付けする。
 * 上位にランクされたアクションの成功を示す報酬スコアをレポートする。

[ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [パッケージ (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [サンプル](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* 最新バージョンの [Node.js](https://nodejs.org)。

## <a name="setting-up"></a>設定

### <a name="create-a-personalizer-azure-resource"></a>Personalizer Azure リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) または [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、ローカル マシン上に Personalizer のリソースを作成します。 さらに、以下を実行できます。

* 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services)を無料で入手する。 これは、サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)で入手できます。  
* [Azure portal](https://portal.azure.com/) でご利用のリソースを表示する。

試用版のサブスクリプションまたはリソースからキーを取得した後、[環境変数](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)を 2 つ作成します。

* `PERSONALIZER_KEY` (リソース キー)。
* `PERSONALIZER_ENDPOINT` (リソース エンドポイント)。

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

`npm init -y` コマンドを実行して、`package.json` ファイルを作成します。 

```console
npm init -y
```

### <a name="install-the-nodejs-library-for-personalizer"></a>Personalizer 用 Node.js ライブラリをインストールする

次のコマンドを使用して、Node.js 用 Personalizer クライアント ライブラリをインストールします。

```console
npm install @azure/cognitiveservices-personalizer --save
```

このクイックスタートの残りの NPM パッケージをインストールします。

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

### <a name="change-the-model-update-frequency"></a>モデルの更新頻度を変更する

Azure portal の Personalizer リソースで、 **[モデルの更新頻度]** を 10 秒に変更します。 これでサービスが迅速にトレーニングされるようになり、繰り返しのたびに上位のアクションが変化する様子を確認できます。

![モデルの更新頻度を変更する](./media/settings/configure-model-update-frequency-settings.png)

Personalizer ループを初めてインスタンス化したときには、トレーニング元となる Reward API 呼び出しがないため、モデルは存在しません。 Rank 呼び出しは、各項目に対して等しい確率を返します。 それでも、アプリケーションは、RewardActionId の出力を使用して、常にコンテンツをランク付けする必要があります。

## <a name="object-model"></a>オブジェクト モデル

Personalizer クライアントは、自分のキーが含まれている Microsoft.Rest.ServiceClientCredentials を使用して Azure に対する認証を行う PersonalizerClient オブジェクトです。

コンテンツのランクを要求するには、RankRequest を作成し、それを client.Rank メソッドに渡します。 Rank メソッドから返される RankResponse に、ランク付けされたコンテンツが含まれています。 

Personalizer に報酬を送信するには、RewardRequest を作成し、それを client.Reward メソッドに渡します。 

このクイックスタートでは、報酬を決定するにあたって大きな問題はありません。 実稼働システムでは、何がどの程度まで[報酬スコア](concept-rewards.md)に影響を及ぼすかを特定するのは複雑なプロセスとなる場合があり、そのプロセスはやがて変更することになる場合もあります。 実際の Personalizer アーキテクチャでは、その点を設計上の主要な意思決定に含めるようにしてください。 

## <a name="code-examples"></a>コード例

以下のコード スニペットは、Node.js 用 Personalizer クライアント ライブラリを使用して次のことを実行する方法を示します。

* [Personalizer クライアントを作成する](#create-a-personalizer-client)
* [ランクを要求する](#request-a-rank)
* [報酬を送信する](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

お気に入りのエディターまたは IDE で、`sample.js` という名前の新しい Node.js アプリケーションを作成します。 

## <a name="add-the-dependencies"></a>依存関係を追加する

好みのエディターまたは IDE で、**sample.js** ファイルを開きます。 次の `requires` を追加して、NPM パッケージを追加します。

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Personalizer のリソース情報を追加する

`PERSONALIZER_KEY` および `PERSONALIZER_ENDPOINT` という名前の環境変数から取得される、リソースの Azure キーとエンドポイントの変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、再読み込みしなければならない場合があります。 メソッドは、このクイックスタートの中で後から作成します。

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Personalizer クライアントを作成する

次に、Personalizer クライアントを返すメソッドを作成します。 このメソッドのパラメーターは `PERSONALIZER_RESOURCE_ENDPOINT` で、ApiKey は `PERSONALIZER_RESOURCE_KEY` です。

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>アクションとして表されるコンテンツの選択肢を取得する

アクションとは、Personalizer にランク付けさせたいコンテンツの選択肢を表します。 Program クラスに次のメソッドを追加します。時間帯と現在の食べ物の好みに関するユーザーの入力をコマンド ラインから取得するものです。

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>学習ループを作成する

Personalizer の学習ループとは、[rank](#request-a-rank) 呼び出しと [reward](#send-a-reward) 呼び出しのサイクルです。 このクイックスタートでは、コンテンツをパーソナライズするための各ランク呼び出しの後に、そのコンテンツへのランク付けにサービスがどの程度寄与したかを Personalizer に伝える報酬呼び出しを行います。 

次のループ コードは、コマンド ラインでユーザーに好みをたずね、その情報を Personalizer に送信してランク付けし、そのランク付けされた選択肢をリストから選択できるようユーザーに提示した後、選択のランク付けにサービスがどの程度寄与したかを報酬の形で Personalizer に伝達するサイクルをループで処理しています。

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

rank 呼び出しと reward 呼び出しについて、以降の各セクションでさらに詳しく見ていきましょう。

## <a name="request-a-rank"></a>ランクを要求する

ランク要求を実行するために、このプログラムは、ユーザーの好みをたずねてコンテンツの選択肢を作成します。 このプロセスで、ランクから除外するコンテンツ (`excludeActions`) を作成することもできます。 ランク付けされた応答を取得するためには、ランク要求に [actions](concepts-features.md#actions-represent-a-list-of-options)、currentContext、excludeActions、ランクの一意の eventId (GUID) が必要です。 

このクイックスタートにおけるコンテキストのフィーチャーは、時間帯と食べ物に対するユーザーの好みという単純なものです。 実稼働システムでは、[アクションとフィーチャー](concepts-features.md)を決定し、[評価](concept-feature-evaluation.md)することが、決して簡単ではない場合もあります。  

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>報酬を送信する

報酬要求を完了するために、このプログラムは、ユーザーの選択をコマンド ラインから取得し、各選択に数値を割り当てた後、一意のランク イベント ID と数値を報酬メソッドに送信します。

このクイックスタートでは、0 または 1 という単純な数値を報酬として割り当てます。 実際のニーズにもよりますが、実稼働システムでは、いつ何を [Reward](concept-rewards.md) 呼び出しに送信するかが決して簡単な決定事項ではない場合もあります。 

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>プログラムの実行

アプリケーション ディレクトリから Node.js を使用して、アプリケーションを実行します。

```console
node sample.js
```

![クイック スタート プログラムは、フィーチャーと呼ばれるユーザー設定を収集するためにいくつかの質問をしてから、最上位のアクションを提供します。](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

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
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js) にあります。
