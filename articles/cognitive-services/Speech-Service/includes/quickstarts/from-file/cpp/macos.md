---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: fb0174f831d99d3088171448c7b23163eb1d9fc8
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226326"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [開発環境を設定し、空のプロジェクトを作成する](../../../../quickstarts/setup-platform.md?tabs=macos&pivots=programming-language-cpp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. `helloworld.cpp` という名前で C++ ソース ファイルを作成し、その中に次のコードを貼り付けます。

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. この新しいファイルで、文字列 `YourSubscriptionKey` を、音声サービスのサブスクリプション キーで置き換えます。

1. `YourServiceRegion` という文字列を、自分のサブスクリプションに関連付けられている[リージョン](https://aka.ms/speech/sdkregion)の**リージョン識別子**に置き換えます。

1. 文字列 `whatstheweatherlike.wav` を実際のファイル名に置き換えます。

> [!NOTE]
> Speech SDK では、既定で認識される言語が en-us です。ソース言語の選択については、「[音声テキスト変換のソース言語を指定する](../../../../how-to-specify-source-language.md)」を参照してください。

## <a name="build-the-app"></a>アプリのビルド

> [!NOTE]
> 次のコマンドを必ず _1 つのコマンド ライン_ として入力してください。 各コマンドの横にある **[Copy]\(コピー)** ボタンを使用してコマンドをコピーし、シェル プロンプトでそれを貼り付けるのが、それを行う最も簡単な方法です。

* 次のコマンドを実行して、アプリケーションをビルドします。

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>アプリを実行する

1. Speech SDK ライブラリを指すようにローダーのライブラリ パスを構成します。

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. アプリケーションを実行します。

   ```sh
   ./helloworld
   ```

1. オーディオ ファイルが Speech サービスに送信され、ファイルの最初の発話がテキストに文字起こしされて、同じウィンドウに表示されます。

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
