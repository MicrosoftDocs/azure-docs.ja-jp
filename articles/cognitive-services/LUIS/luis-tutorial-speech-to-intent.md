---
title: Speech C# SDK を使用する
titleSuffix: Azure Cognitive Services
description: 音声サービスを使用すると、1 回の要求を使用して音声を受け取り、LUIS 予測 JSON オブジェクトを返せます。 この記事では、C# プロジェクトをダウンロードして Visual Studio で使用し、マイクに向かって発話して LUIS 予測情報を受け取ります。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 04/08/2018
ms.author: diberry
ms.openlocfilehash: 9d6173ee25f28aa884513d126c06a8a7c722098d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273840"
---
# <a name="integrate-speech-service-with-your-language-understanding-app"></a>音声サービスを Language Understanding アプリと統合する
[音声サービス](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/)を使用すると、1 回の要求を使用して音声を受け取り、LUIS 予測 JSON オブジェクトを返せます。 この記事では、C# プロジェクトをダウンロードして Visual Studio で使用し、マイクに向かって発話して LUIS 予測情報を受け取ります。 プロジェクトでは、参照として既に含まれている Speech [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) パッケージを使用します。 

この記事では、アプリケーションをインポートするための、無料の [LUIS ][LUIS] Website アカウントが必要です。

## <a name="create-luis-endpoint-key"></a>LUIS エンドポイント キーの作成
Azure portal で、LUIS アプリ用の **Cognitive Service** (LUIS) キーを[作成](luis-how-to-azure-subscription.md)します。  

## <a name="import-human-resources-luis-app"></a>人事管理 LUIS アプリをインポートする
この記事の意図と発話は、[Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding) GitHub リポジトリにある人事管理 LUIS アプリからのものです。 [HumanResources.json](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources.json) ファイルをダウンロードし、`.json` 拡張子を付けて保存し、LUIS に[インポート](luis-how-to-start-new-app.md#import-new-app)します。 

このアプリには、人事分野に関連するインテント、エンティティ、および発話があります。 発話の例を次に示します。

|発話の例|
|--|
|John Smith の上司は誰ですか?|
|John Smith は誰を管理していますか?|
|フォーム 123456 はどこですか?|
|有給休暇はありますか?|


## <a name="add-keyphrase-prebuilt-entity"></a>KeyPhrase 事前構築済みエンティティを追加する
アプリをインポートしたら、**[エンティティ]** を選択し、**[Add prebuilt entity]\(作成済みエンティティの追加\)** を選択します。 **KeyPhrase** エンティティを追加します。 KeyPhrase エンティティは、発話からキーの主題を抽出します。

## <a name="train-and-publish-the-app"></a>アプリをトレーニングして公開する
1. 右上のナビゲーション バーで、**[トレーニングする]** ボタンをクリックして LUIS アプリをトレーニングします。

2. 右上のバーで **[管理]** を選択し、左側のナビゲーションで **[Keys and endpoints]\(キーとエンドポイント\)** を選択します。 

3. **[Keys and endpoints]\(キーとエンドポイント\)** ページで、「[LUIS エンドポイント キーの作成](#create-luis-endpoint-key)」セクションで作成した LUIS キーを割り当てます。

   このページで、アプリ ID、公開リージョン、および「[LUIS エンドポイント キーを作成する](#create-luis-endpoint-key)」セクションで作成した LUIS キーのサブスクリプション ID を収集します。 この記事の後半でこれらの値を使用するためにコードを変更する必要があります。 
  
   この演習では、無料のスターター キーを使用**しないで**ください。 この演習では、Azure Portal で作成された **Language Understanding** キーのみが使用できます。 

   https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**?subscription-key=**LUISKEY**&q=


4. 右上のバーにある **[公開]** ボタンを選択して LUIS アプリを公開します。 

## <a name="audio-device"></a>オーディオ デバイス
この記事では、お使いのコンピューターのオーディオ デバイスを使用します。 これには、マイク付きのヘッドセットや、コンピューターに内蔵されているオーディオ デバイスが含まれます。 オーディオ デバイスに音声を認識させるために、通常よりも大きな音量で話す必要があるかどうか、オーディオの入力レベルを確認してください。 

## <a name="download-the-luis-sample-project"></a>LUIS サンプル プロジェクトをダウンロードする
 [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding) リポジトリを複製またはダウンロードします。 Visual Studio で [Speech to intent project](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-speech-intent-recognition) を開き、NuGet パッケージを復元します。 VS ソリューション ファイルは documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln です。

Speech SDK が既に参照として含まれています。 

[![Microsoft.CognitiveServices.Speech NuGet パッケージが表示された Visual Studio 2017 のスクリーンショット](./media/luis-tutorial-speech-to-intent/nuget-package.png "Microsoft.CognitiveServices.Speech NuGet パッケージが表示された Visual Studio 2017 のスクリーンショット")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>C# コードを編集する
`Program.cs` ファイルを開き、次の変数を変更します。

|変数名|目的|
|--|--|
|LUIS_assigned_endpoint_key|[発行] ページからのエンドポイント URL の割り当てられたサブスクリプション キーの値に対応しています|
|LUIS_endpoint_key_region|エンドポイント URL の最初のサブドメインに対応しています (例: `westus`)|
|LUIS_app_ID|**apps/** の後に続く、エンドポイント URL のルートに対応します|

`Program.cs` ファイルには既に人事意図がマップされています。

アプリケーションをビルドし、実行します。 

## <a name="test-code-with-utterance"></a>発話を使ってコードをテストする
マイクに向かって "Who are the approved dentists in Redmond" と話します。

[!code-console[Command line response from spoken utterance](~/samples-luis/documentation-samples/tutorial-speech-intent-recognition/console-output.txt "Command line response from spoken utterance")]

正しい意図の **GetEmployeeBenefits** が 85% の信頼度で見つかりました。 keyPhrase エンティティが返されました。 

Speech SDK は、LUIS の応答全体を返します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になったら、LUIS HumanResources アプリを削除します。 これを行うには、アプリを選択し、一覧の上のコンテキスト ツール バーの **[削除]** を選択します。 **[Delete app?]\(アプリを削除しますか?\)** ポップアップ ダイアログで、**[OK]** をクリックします。

サンプル コードの使用が終わったら、このディレクトリを削除します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [LUIS をボットと統合する](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
