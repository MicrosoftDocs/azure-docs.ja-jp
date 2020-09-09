---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 7572e5c5621b514c375e44ca44ddfc4102f5d714
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87298821"
---
## <a name="prerequisites"></a>前提条件

開始する前に、以下の操作を行います。

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?pivots=programming-language-python" target="_blank">実際の開発環境に対応した Speech SDK をインストールし、空のサンプル プロジェクトを作成します<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

## <a name="create-a-luis-app-for-intent-recognition"></a>意図認識用の LUIS アプリを作成する

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>プロジェクトを開く

1. 好みの IDE を開きます。
2. 新しいプロジェクトを作成し、`quickstart.py` という名前のファイルを作成して開きます。

## <a name="start-with-some-boilerplate-code"></a>定型コードを使用して開始する

このプロジェクトのスケルトンとして機能するコードを追加しましょう。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Speech 構成を作成する

`IntentRecognizer` オブジェクトを初期化する前に、LUIS 予測リソース用のキーとリージョンを使用する構成を作成する必要があります。

このコードを `quickstart.py` に挿入します。 次の値を必ず更新してください。

* `"YourLanguageUnderstandingSubscriptionKey"` を LUIS 予測キーで置き換えます。
* `"YourLanguageUnderstandingServiceRegion"` を LUIS の場所で置き換えます。 [リージョン](https://aka.ms/speech/sdkregion)の**リージョン識別子**を使用してください。

>[!TIP]
> これらの値を見つける方法については、「[意図認識用の LUIS アプリを作成する](#create-a-luis-app-for-intent-recognition)」を参照してください。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

このサンプルでは、LUIS キーとリージョンを使用して `SpeechConfig` オブジェクトを構築します。 使用可能なメソッドの完全な一覧については、[SpeechConfig クラス](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)に関する記事を参照してください。

Speech SDK では、既定で認識される言語が en-us です。ソース言語の選択については、「[音声テキスト変換のソース言語を指定する](../../../../how-to-specify-source-language.md)」を参照してください。

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer を初期化する

ここで、`IntentRecognizer` を作成しましょう。 Speech 構成のすぐ下にこのコードを挿入します。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel と意図を追加する

`LanguageUnderstandingModel` と意図認識エンジンを関連付け、認識させる意図を追加する必要があります。 ホーム オートメーション用のあらかじめ構築されたドメインの意図を使用します。

次のコードを `IntentRecognizer` の下に挿入します。 `"YourLanguageUnderstandingAppId"` は必ずお客様の LUIS app ID で置き換えてください。 

>[!TIP]
> この値を見つける方法については、「[意図認識用の LUIS アプリを作成する](#create-a-luis-app-for-intent-recognition)」を参照してください。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

この例では `add_intents()` 関数を使用して、明示的に定義された意図の一覧を追加します。 モデルのすべての意図を追加する場合は、`add_all_intents(model)` を使用し、モデルを渡します。

## <a name="recognize-an-intent"></a>意図を認識する

`IntentRecognizer` オブジェクトから、`recognize_once()` メソッドを呼び出します。 認識の対象として 1 つの語句を送信しようとしていること、また、その語句が識別された後で、音声認識を停止しようとしていることが、このメソッドを通じて Speech サービスに伝えられます。

このコードをモデルの下に挿入します。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>認識結果 (またはエラー) を表示する

音声サービスによって認識結果が返されたら、それを使用して何らかの操作を行います。 シンプルに保ち、結果をコンソールに出力します。

`recognize_once()` の呼び出しの下にこのコードを追加します。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>コードを確認する

この時点で、コードは次のようになります。

> [!NOTE]
> このバージョンにはいくつかのコメントを追加してあります。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>アプリをビルドして実行する

コンソールまたは IDE からサンプルを実行します。

```
python quickstart.py
```

その後 15 秒間、マイクからの音声入力が認識され、コンソール ウィンドウにログが記録されます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [footer](./footer.md)]
