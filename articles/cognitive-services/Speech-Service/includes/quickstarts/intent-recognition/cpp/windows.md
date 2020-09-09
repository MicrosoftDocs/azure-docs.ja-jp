---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 9c0c2673acf12ddf80b77be57acc5af55e7f160c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87298801"
---
## <a name="prerequisites"></a>前提条件

開始する前に、以下の操作を行います。

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp" target="_blank">実際の開発環境に対応した Speech SDK をインストールし、空のサンプル プロジェクトを作成します<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

## <a name="create-a-luis-app-for-intent-recognition"></a>意図認識用の LUIS アプリを作成する

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Visual Studio でプロジェクトを開きます。

次に、Visual Studio でプロジェクトを開きます。

1. Visual Studio 2019 を起動します。
2. プロジェクトを読み込んで `helloworld.cpp` を開きます。

## <a name="start-with-some-boilerplate-code"></a>定型コードを使用して開始する

このプロジェクトのスケルトンとして機能するコードを追加しましょう。 `recognizeIntent()` という非同期メソッドを作成済みであることを確認します。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,72-80)]

## <a name="create-a-speech-configuration"></a>Speech 構成を作成する

`IntentRecognizer` オブジェクトを初期化する前に、LUIS 予測リソース用のキーとリージョンを使用する構成を作成する必要があります。

> [!IMPORTANT]
> スターター キーとオーサリング キーは機能しません。 前の手順で作成した予測キーと場所を使用する必要があります。 詳細については、「[意図認識用の LUIS アプリを作成する](#create-a-luis-app-for-intent-recognition)」を参照してください。

このコードを `recognizeIntent()` メソッドに挿入します。 次の値を必ず更新してください。

* `"YourLanguageUnderstandingSubscriptionKey"` を LUIS 予測キーで置き換えます。
* `"YourLanguageUnderstandingServiceRegion"` を LUIS の場所で置き換えます。  [リージョン](https://aka.ms/speech/sdkregion)の**リージョン識別子**を使用してください。

>[!TIP]
> これらの値を見つける方法については、「[意図認識用の LUIS アプリを作成する](#create-a-luis-app-for-intent-recognition)」を参照してください。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

このサンプルでは、`FromSubscription()` メソッドを使用して `SpeechConfig` をビルドします。 使用可能なメソッドの完全な一覧については、[SpeechConfig クラス](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)に関する記事を参照してください。

Speech SDK では、既定で認識される言語が en-us です。ソース言語の選択については、「[音声テキスト変換のソース言語を指定する](../../../../how-to-specify-source-language.md)」を参照してください。

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer を初期化する

ここで、`IntentRecognizer` を作成しましょう。 このコードを Speech 構成のすぐ下にある `recognizeIntent()` メソッドに挿入します。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel と意図を追加する

`LanguageUnderstandingModel` と意図認識エンジンを関連付け、認識させる意図を追加する必要があります。 ホーム オートメーション用のあらかじめ構築されたドメインの意図を使用します。

次のコードを `IntentRecognizer` の下に挿入します。 `"YourLanguageUnderstandingAppId"` は必ずお客様の LUIS app ID で置き換えてください。

>[!TIP]
> この値を見つける方法については、「[意図認識用の LUIS アプリを作成する](#create-a-luis-app-for-intent-recognition)」を参照してください。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-33)]

この例では、`AddIntent()` 関数を使用して、個別に意図を追加します。 モデルのすべての意図を追加する場合は、`AddAllIntents(model)` を使用し、モデルを渡します。

## <a name="recognize-an-intent"></a>意図を認識する

`IntentRecognizer` オブジェクトから、`RecognizeOnceAsync()` メソッドを呼び出します。 認識の対象として 1 つの語句を送信しようとしていること、また、その語句が識別された後で、音声認識を停止しようとしていることが、このメソッドを通じて Speech サービスに伝えられます。 簡素化のため、結果が返されて完了するまで待機します。

次のコードをモデルの下に挿入します。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=43)]

## <a name="display-the-recognition-results-or-errors"></a>認識結果 (またはエラー) を表示する

音声サービスによって認識結果が返されたら、それを使用して何らかの操作を行います。 シンプルに保ち、結果をコンソールに出力します。

次のコードを `auto result = recognizer->RecognizeOnceAsync().get();` の下に挿入します。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=46-71)]

## <a name="check-your-code"></a>コードを確認する

この時点で、コードは次のようになります。

> [!NOTE]
> このバージョンにはいくつかのコメントを追加してあります。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-79)]

## <a name="build-and-run-your-app"></a>アプリをビルドして実行する

これで、アプリをビルドし、Speech サービスを使用して音声認識をテストする準備ができました。

1. **コードをコンパイルする** - Visual Studio のメニュー バーで、 **[ビルド]**  >  **[ソリューションのビルド]** の順に選択します。
2. **アプリを起動する** - メニュー バーから **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、<kbd>F5</kbd> キーを押します。
3. **認識を開始する** - 英語で語句を読み上げるように求められます。 音声が Speech Service に送信され、テキストとして文字起こしされて、コンソールに表示されます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [footer](./footer.md)]
