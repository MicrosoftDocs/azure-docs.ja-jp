---
title: Node.js を使用して発話をインポートする - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS Authoring API を使用して、CSV 形式の既存のデータからプログラムで LUIS アプリを作成する方法を説明します。
services: cognitive-services
manager: nitinme
ms.custom: seodec18, devx-track-js
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/05/2019
ms.openlocfilehash: 58eb92f4d0bc3de4671ca2ece14a178a876e4a6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91541045"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Node.js を使用したプログラムによる LUIS アプリの作成

LUIS は、[LUIS](luis-reference-regions.md) Web サイトによって実行されるすべてのことを実行するプログラム API を提供します。 これにより、既存のデータがあり、情報を手動で入力するよりも、LUIS アプリをプログラムで作成する方が速い場合に時間を節約できます。

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>前提条件

* [LUIS](luis-reference-regions.md) Web サイトにサインインし、[アカウント設定] で[オーサリング キー](luis-how-to-azure-subscription.md#authoring-key)を見つけます。 このキーを使用して、Authoring API を呼び出します。
* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。
* この記事では、架空の会社のユーザー要求のログが含まれた CSV ファイルをまず使用します。 [こちら](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv)でダウンロードできます。
* NPM を使用して最新の Node.js をインストールします。 これは、[こちら](https://nodejs.org/en/download/)からダウンロードできます。
* **[推奨]** IntelliSense およびデバッグ用の Visual Studio Code。[こちら](https://code.visualstudio.com/)から無料でダウンロードできます。

この記事のコードはすべて、[Azure-Samples Language Understanding GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv)で入手できます。

## <a name="map-preexisting-data-to-intents-and-entities"></a>既存のデータを意図とエンティティにマップする
LUIS を念頭に置いて作成されていないシステムがあっても、ユーザーが実行したいさまざまなことにマップされるテキスト データが含まれていれば、ユーザー入力の既存のカテゴリから LUIS の意図へのマッピングを考えることができます。 ユーザーの発話内の重要な単語やフレーズを特定できる場合、これらの単語はエンティティにマップできます。

[`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) ファイルを開きます。 このファイルには、架空のホーム オートメーション サービスに対するユーザー クエリのログが含まれています。ログには、クエリの分類方法、ユーザーの発話、クエリから引き出された有用な情報を含む列が含まれています。

![既存のデータの CSV ファイル](./media/luis-tutorial-node-import-utterances-csv/csv.png)

**RequestType** 列は意図として使用でき、**Request** 列は発話の例を示していることがわかります。 その他のフィールドは、発話に出現した場合にエンティティとして使用できます。 意図、エンティティ、発話の例があるため、単純なサンプル アプリの要件が揃っています。

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>LUIS 以外のデータから LUIS アプリを作成する手順
CSV ファイルから新しい LUIS アプリを生成するには、次のようにします。

* CSV ファイルのデータを解析します。
    * オーサリング API を使用して、LUIS にアップロードできる形式に変換します。
    * 解析されたデータから、意図およびエンティティに関する情報を収集します。
* オーサリング API を呼び出して、次のことを行います。
    * アプリを作成します。
    * 解析済みのデータから収集された意図およびエンティティを追加します。
    * LUIS アプリを作成したら、解析されたデータから発話の例を追加できます。

このプログラム フローは、`index.js` ファイルの最後の部分で確認できます。 このコードをコピーまたは[ダウンロード](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js)し、`index.js` に保存します。

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>CSV を解析する

CSV の発話を含む列エントリは、LUIS が理解できる JSON 形式に解析する必要があります。 この JSON 形式には、発話の意図を識別する `intentName` フィールドが含まれている必要があります。 また、発話にエンティティがない場合に空にすることができる、`entityLabels` フィールドも含まれている必要があります。

たとえば、"Turn on the lights" のエントリは、次の JSON にマップされます。

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

この例では、`intentName` は CSV ファイルの **Request** 列見出しの下のユーザー要求から取得され、`entityName` は重要な情報が含まれたその他の列から取得されています。 たとえば、**Operation** または **Device** のエントリがあり、その文字列が実際の要求にも含まれている場合は、エンティティとしてラベル付けできます。 次のコードは、この解析プロセスを示しています。 このコードをコピーまたは[ダウンロード](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js)し、`_parse.js` に保存できます。

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>LUIS アプリを作成する
データが JSON に解析されたら、LUIS アプリに追加します。 次のコードでは LUIS アプリを作成します。 このコードをコピーまたは[ダウンロード](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js)し、`_create.js` に保存します。

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>意図の追加
アプリを作成したら、意図を追加する必要があります。 次のコードでは LUIS アプリを作成します。 このコードをコピーまたは[ダウンロード](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js)し、`_intents.js` に保存します。

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>複数エンティティの追加
次のコードでは、LUIS アプリにエンティティを追加します。 このコードをコピーまたは[ダウンロード](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js)し、`_entities.js` に保存します。

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]



## <a name="add-utterances"></a>発話の追加
LUIS アプリでエンティティと意図が定義されたら、発話を追加できます。 次のコードでは、[Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API を使用しています。この API により、一度に最大 100 個の発話を追加できます。  このコードをコピーまたは[ダウンロード](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js)し、`_upload.js` に保存します。

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>コードの実行


### <a name="install-nodejs-dependencies"></a>Node.js の依存関係をインストールする
ターミナル/コマンド ラインで、NPM から Node.js の依存関係をインストールします。

```console
> npm install
```

### <a name="change-configuration-settings"></a>構成設定を変更する
このアプリケーションを使用するには、index.js ファイル内の値を独自のエンドポイント キーに変更し、アプリに付ける名前を指定する必要があります。 また、アプリのカルチャを設定したり、バージョン番号を変更したりすることもできます。

index.js ファイルを開き、ファイルの先頭にあるこれらの値を変更します。


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us";
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>スクリプトを実行する
Node.js を使用して、ターミナル/コマンド ラインからスクリプトを実行します。

```console
> node index.js
```

or

```console
> npm start
```

### <a name="application-progress"></a>アプリケーションの進行状況
アプリケーションの実行中は、コマンド ラインに進行状況が表示されます。 コマンド ライン出力には、LUIS からの応答の形式が含まれています。

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>LUIS アプリを開く
スクリプトが完了したら、[LUIS](luis-reference-regions.md) にサインインし、作成した LUIS アプリを **[マイ アプリ]** で確認できます。 **TurnOn**、**TurnOff**、**None** の各意図で、追加した発話を確認できます。

![意図 TurnOn](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [LUIS Web サイトでアプリをテストおよびトレーニングする](luis-interactive-test.md)

## <a name="additional-resources"></a>その他のリソース

このサンプル アプリケーションでは、次の LUIS API を使用しています。
- [アプリの作成](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [意図の追加](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [エンティティの追加](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e)
- [発話の追加](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
