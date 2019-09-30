---
title: 完全なテキスト一致のエンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: リスト エンティティを追加して、単語や語句の変化形に LUIS でラベルを付けやすくする方法について説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: a722ce39a679fa13e1fe849c46b44f786ea5ee42
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390267"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>リスト エンティティを使用してエンティティの検出を向上する 
この記事では、[リスト エンティティ](luis-concept-entity-types.md)を使用してエンティティの検出を向上させる方法について説明します。 リスト エンティティは、用語の完全一致なので、ラベルを付ける必要はありません。  

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * リスト エンティティを作成する 
> * 正規化された値とシノニムを追加する
> * 改善されたエンティティ特定を検証する

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * 最新の [Node.js](https://nodejs.org)
> * [HomeAutomation LUIS アプリ](luis-get-started-create-app.md)。 Home Automation アプリを作成していない場合は、新しいアプリを作成し、事前構築済みのドメイン **HomeAutomation** を追加します。 アプリをトレーニングして公開します。 
> * LUIS アプリの [AuthoringKey](luis-concept-keys.md#authoring-key)、[EndpointKey](luis-concept-keys.md#endpoint-key) (複数回クエリを実行する場合)、アプリ ID、バージョン ID、[リージョン](luis-reference-regions.md)。

> [!Tip]
> サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)に登録できます。

この記事のコードは、すべて [Azure-Samples GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity)で入手できます。 

## <a name="use-homeautomation-app"></a>HomeAutomation アプリを使用する
HomeAutomation アプリを使用すると、照明、エンターテイメント システム、暖房や冷房などの環境制御などのデバイスを制御できます。 このようなシステムには、製造元の名前、ニックネーム、頭字語、スラングなどのさまざまな名前があります。 

文化や購買層によってさまざまな名前があるシステムの 1 つとしてサーモスタットがあります。 サーモスタットは、住宅または建物の冷却システムと暖房システムの両方を制御できます。

理想的には、次の発話は事前構築済みエンティティ **HomeAutomation.Device** に解決される必要があります。

|#|発話|特定されたエンティティ|スコア|
|--|--|--|--|
|1|turn on the ac (ac の電源を入れて)|HomeAutomation.Device - "ac"|0.8748562|
|2|turn up the heat (暖房の電源を入れて)|HomeAutomation.Device - "heat"|0.784990132|
|3|make it colder (寒くして)|||

最初の 2 つの発話は、異なるデバイスにマップされます。 3 つ目の発話の "make it colder" (温度を下げて) はデバイスにマップされず、代わりに結果を要求します。 LUIS は、用語 "colder" (寒く) が、サーモスタットが要求対象のデバイスを意味していることを認識していません。 理想的には、LUIS はこれらの発話のすべてを同じデバイスに解決する必要があります。 

## <a name="use-a-list-entity"></a>リスト エンティティを使用する
HomeAutomation.Device エンティティは、少数のデバイスや名前の変化形が少ない場合に最適です。 オフィス ビルやキャンパスでは、HomeAutomation.Device エンティティの有用性を超えるほどデバイス名の数が多くなります。 

このシナリオには、**リスト エンティティ**が適しています。建物やキャンパス内のデバイスの用語セットは、膨大であっても既知のセットだからです。 リスト エンティティを使用すると、LUIS はサーモスタットのセット内の考えられる値をすべて受け取り、それを "サーモスタット" という単一のデバイスのみに解決することができます。 

この記事では、サーモスタットを含むエンティティ リストを作成します。 この記事のサーモスタットの代替名は次のとおりです。 

|サーモスタットの代替名|
|--|
| ac |
| a/c|
| a-c|
|heater (ヒーター)|
|hot (暖かい)|
|hotter (暖かく)|
|cold (寒い)|
|colder (寒く)|

LUIS が新しい代替名を頻繁に判断する必要がある場合は、[語句リスト](luis-concept-feature.md#how-to-use-phrase-lists)の方が適しています。

## <a name="create-a-list-entity"></a>リスト エンティティを作成する
Node.js ファイルを作成し、次のコードをそのファイルにコピーします。 authoringKey、appId、versionId、および region の値を変更します。

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

次のコマンドを使用して NPM の依存関係をインストールし、コードを実行してリスト エンティティを作成します。

```console
npm install && node add-entity-list.js
```

実行の出力は、リスト エンティティの ID です。

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>モデルをトレーニングする
新しいリストがクエリ結果に反映されるように LUIS をトレーニングします。 トレーニングは、トレーニングと、トレーニング完了後の状態の確認という 2 つのプロセスから構成されます。 多くのモデルを持つアプリでは、トレーニングに数分かかる場合があります。 次のコードでは、トレーニングが成功するまでアプリを訓練します。 このコードでは、429 "Too many requests" (要求が多すぎます) エラーを回避するために、待機して再試行する戦略を使用しています。 

Node.js ファイルを作成し、次のコードをそのファイルにコピーします。 authoringKey、appId、versionId、および region の値を変更します。

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

次のコマンドを使用して、アプリをトレーニングするコードを実行します。

```console
node train.js
```

実行の出力は、LUIS モデルのトレーニングの各繰り返しの状態です。 次の実行では、トレーニングの確認が 1 回のみ必要でした。

```console
1 trained = true
[ { modelId: '2c549f95-867a-4189-9c35-44b95c78b70f',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '5530e900-571d-40ec-9c78-63e66b50c7d4',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '519faa39-ae1a-4d98-965c-abff6f743fe6',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9671a485-36a9-46d5-aacd-b16d05115415',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9ef7d891-54ab-48bf-8112-c34dcd75d5e2',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '8e16a660-8781-4abf-bf3d-f296ebe1bf2d',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } } ]

```
## <a name="publish-the-model"></a>モデルを発行する
エンドポイントからリスト エンティティを使用できるように発行します。

Node.js ファイルを作成し、次のコードをそのファイルにコピーします。 endpointKey、appId、および region の値を変更します。 クォータ制限を超えてこのファイルを呼び出す予定がない場合は、authoringKey を使用できます。

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

次のコマンドを使用して、アプリのクエリを実行するコードを実行します。

```console
node publish.js
```

次の出力には、すべてのクエリのエンドポイント URL が含まれています。 実際の JSON 結果には、実際の appID が含まれます。 

```json
{ 
  versionId: null,
  isStaging: false,
  endpointUrl: 'https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>',
  region: null,
  assignedEndpointKey: null,
  endpointRegion: 'westus',
  publishedDateTime: '2018-01-29T22:17:38Z' }
}
```

## <a name="query-the-app"></a>アプリのクエリを実行する 
エンドポイントからアプリのクエリを実行し、リスト エンティティによって LUIS がデバイスの種類を判断するために役立っていることを証明します。

Node.js ファイルを作成し、次のコードをそのファイルにコピーします。 endpointKey、appId、および region の値を変更します。 クォータ制限を超えてこのファイルを呼び出す予定がない場合は、authoringKey を使用できます。

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

次のコマンドを使用してコードを実行し、アプリのクエリを実行します。

```console
node train.js
```

出力はクエリ結果です。 コードによって**詳細な**名前/値のペアがクエリ文字列に追加されたため、出力にはすべての意図とそのスコアが含まれます。

```json
{
  "query": "turn up the heat",
  "topScoringIntent": {
    "intent": "HomeAutomation.TurnOn",
    "score": 0.139018849
  },
  "intents": [
    {
      "intent": "HomeAutomation.TurnOn",
      "score": 0.139018849
    },
    {
      "intent": "None",
      "score": 0.120624863
    },
    {
      "intent": "HomeAutomation.TurnOff",
      "score": 0.06746891
    }
  ],
  "entities": [
    {
      "entity": "heat",
      "type": "HomeAutomation.Device",
      "startIndex": 12,
      "endIndex": 15,
      "score": 0.784990132
    },
    {
      "entity": "heat",
      "type": "DevicesList",
      "startIndex": 12,
      "endIndex": 15,
      "resolution": {
        "values": [
          "Thermostat"
        ]
      }
    }
  ]
}
```

**Thermostat** (サーモスタット) の特定のデバイスは、"turn up the heat" (暖房の電源を入れて) という結果指向のクエリで識別されます。 元の HomeAutomation.Device エンティティはまだアプリ内にあるので、その結果も表示できます。 

他の 2 つの発話を試して、サーモスタットとして返されることを確認してください。 

|#|発話|エンティティ|type|value|
|--|--|--|--|--|
|1|turn on the ac (ac の電源を入れて)| ac | DevicesList | Thermostat|
|2|turn up the heat (暖房の電源を入れて)|heat (暖房)| DevicesList |Thermostat|
|3|make it colder (寒くして)|colder (寒く)|DevicesList|Thermostat|

## <a name="next-steps"></a>次の手順

別のリスト エンティティを作成して、デバイスの場所を部屋、階、または建物に拡張することができます。 
