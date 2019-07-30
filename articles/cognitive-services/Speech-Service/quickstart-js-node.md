---
title: クイック スタート:音声を認識する、Node.js - Speech Services
titleSuffix: Azure Cognitive Services
description: このガイドでは、Node.js 用 Speech SDK を使用して音声テキスト変換コンソール アプリケーションを作成します。 完了すると、お使いのコンピューターのマイクを使用して、リアルタイムに文字起こし (音声テキスト変換) することができます。
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 9d233de8a9cdd4b9a3637edcd1c6196b4ad16fd2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605123"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>クイック スタート:Node.js 用 Speech SDK を使用して音声を認識する

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Azure Cognitive Services 用 Speech SDK の JavaScript バインドを使用して、音声をテキストに変換する Node.js プロジェクトを作成する方法について説明します。
このアプリケーションは、[JavaScript 用 Speech SDK](https://aka.ms/csspeech/npmpackage) が基になっています。

## <a name="prerequisites"></a>前提条件

* Speech サービス用の Azure サブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。
* 最新バージョンの [Node.js](https://nodejs.org)。

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

新しいフォルダーを作成してプロジェクトを初期化します。

```sh
npm init -f
```

このコマンドは、**package.json** ファイルを既定値で初期化します。 後でこのファイルを編集する場合があります。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

Node.js プロジェクトに Speech SDK を追加します。

```
npm install microsoft-cognitiveservices-speech-sdk
```

このコマンドは、**npmjs** から Speech SDK の最新バージョンと必要な前提条件をダウンロードし、インストールします。 SDK は、プロジェクト フォルダー内の `node_modules` ディレクトリにインストールされます。

## <a name="use-the-speech-sdk"></a>Speech SDK を使用する

フォルダーに `index.js` という名前の新しいファイルを作成し、テキスト エディターでこのファイルを開きます。

> [!NOTE]
> Node.js では、Speech SDK はマイクまたは**ファイル**のデータの種類をサポートしていません。 どちらもブラウザーでのみサポートされています。 代わりに、`AudioInputStream.createPushStream()` または `AudioInputStream.createPullStream()` を通じて、Speech SDK への **Stream** インターフェイスを使用します。

この例では、`PushAudioInputStream` インターフェイスを使用します。

この JavaScript コードを追加します。

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>サンプルを実行する

アプリを開くには、`YourSubscriptionKey`、`YourServiceRegion`、および `YourAudioFile.wav` を構成に合わせて調整します。 次に、このコマンドを呼び出して実行します。

```sh
node index.js
```

指定したファイル名を使用して、認識がトリガーされます。 出力がコンソールに表示されます。

このサンプルは、サブスクリプション キーを更新した後で `index.js` を実行し、ファイル `whatstheweatherlike.wav` を使用した場合の出力です。

```json
SpeechRecognitionResult {
  "privResultId": "9E30EEBD41AC4571BB77CF9164441F46",
  "privReason": 3,
  "privText": "What's the weather like?",
  "privDuration": 15900000,
  "privOffset": 300000,
  "privErrorDetails": null,
  "privJson": {
    "RecognitionStatus": "Success",
    "DisplayText": "What's the weather like?",
    "Offset": 300000,
    "Duration": 15900000
  },
  "privProperties": null
}
```

## <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Speech SDK と Visual Studio Code をインストールして使用する

Visual Studio Code からサンプルを実行することもできます。 以下の手順に従って、クイック スタートをインストールし、開いて実行します。

1. Visual Studio Code を起動します。 **[フォルダーを開く]** を選択します。 クイック スタートのフォルダーに移動します。

   ![フォルダーを開く](media/sdk/qs-js-node-01-open_project.png)

1. Visual Studio Code でターミナルを開きます。

   ![ターミナル ウィンドウ](media/sdk/qs-js-node-02_open_terminal.png)

1. `npm` を実行して依存関係をインストールします。

   ![npm インストール](media/sdk/qs-js-node-03-npm_install.png)

1. これで、`index.js` を開き、ブレークポイントを設定する準備が整いました。

   ![16 行目にブレークポイントを設定した index.js](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. デバッグを開始するには、F5 キーを押すか、メニューで **[デバッグ]、[デバッグの開始]** の順に選択します。

   ![デバッグ メニュー](media/sdk/qs-js-node-05-start_debugging.png)

1. ブレークポイントがヒットしたら、呼び出し履歴と変数を検査することができます。

   ![デバッガー](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. 出力は、デバッグ コンソール ウィンドウに表示されます。

   ![デバッグ コンソール](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で Node.js のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
