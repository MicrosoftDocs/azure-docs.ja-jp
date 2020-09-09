---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f26f0ab6da398dcdee307f89b27cca780d08af85
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85838891"
---
## <a name="prerequisites"></a>前提条件

開始する前に、以下の操作を行います。

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech リソースを作成する <span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [開発環境を設定し、空のプロジェクトを作成する](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * オーディオ キャプチャ用のマイクにアクセスできることを確認する

## <a name="source-code"></a>ソース コード

*quickstart.py* という名前のファイルを作成し、次の Python コードを貼り付けます。

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>コードの説明

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>アプリをビルドして実行する

これで、Speech サービスを使用して音声認識をテストする準備ができました。 

これを macOS で実行しているとき、マイクを使用する Python アプリを作成したのが初めてであれば、おそらく、マイクにアクセスする許可をターミナルに与える必要があります。 **[システム設定]** を開き、 **[セキュリティとプライバシー]** を選択します。 次に、 **[プライバシー]** を選択し、一覧から **[マイク]** を見つけます。 最後に、 **[ターミナル]** を選択して保存します。 

1. **アプリを開始する** - コマンド ラインで、次のように入力します:
    ```bash
    python quickstart.py
    ```
2. **認識を開始する** - 英語で語句を読み上げるよう求めるメッセージが表示されます。 音声が Speech Service に送信され、テキストとして文字起こしされて、コンソールに表示されます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
