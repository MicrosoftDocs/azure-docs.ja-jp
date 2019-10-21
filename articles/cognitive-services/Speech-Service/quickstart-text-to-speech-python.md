---
title: クイック スタート:音声を合成する、Python - Speech Service
titleSuffix: Azure Cognitive Services
description: Python で Speech SDK を使用して音声を合成する方法について説明します
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/14/2019
ms.author: yulili
ms.openlocfilehash: fed0dea1a562f5ddea05516125a38c690069f446
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803675"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-python"></a>クイック スタート:Python 用 Speech SDK を使用して音声を合成する

[音声認識](quickstart-python.md)のクイックスタートも利用できます。

この記事では、Python 用 Speech SDK を通じて Speech Services を使用する方法について説明します。 テキストから音声を合成し、既定のオーディオ出力で再生する方法を示します。

## <a name="prerequisites"></a>前提条件

* Speech Services 用の Azure サブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。
* [Python 3.5 以降](https://www.python.org/downloads/)。
* 以下のオペレーティング システム用の Python Speech SDK パッケージを入手できます。
    * Windows 10: x64 および x86。
    * Mac: macOS X バージョン 10.12 以降。
    * Linux:x64 上の Ubuntu 16.04、Ubuntu 18.04、Debian 9。
* Linux では、以下のコマンドを実行して、必要なパッケージをインストールします。

  * Ubuntu の場合:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  * Debian 9 の場合:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

* Windows では、お使いのプラットフォームに対応した [Microsoft Visual Studio 2019 の Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)が必要です。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

このチュートリアルは 1.7.0 より前のバージョンの SDK では動作しないことに注意してください。

次のコマンドは、[PyPI](https://pypi.org/) から Speech SDK 用の Python パッケージをインストールします。

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>サポートと更新プログラム

Speech SDK Python パッケージの更新プログラムは、PyPI を通じて配布され、[リリース ノート](./releasenotes.md)で発表されます。
新しいバージョンが利用可能な場合は、コマンド `pip install --upgrade azure-cognitiveservices-speech` を使用してそれに更新できます。
`azure.cognitiveservices.speech.__version__` 変数を調べて、現在インストールされているバージョンを確認します。

問題があるか、機能が欠落している場合は、「[サポート オプションとヘルプ オプション](./support.md)」を参照してください。

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Speech SDK を使用する Python アプリケーションの作成

### <a name="run-the-sample"></a>サンプルを実行する

このクイック スタートからソース ファイル `quickstart.py` に[サンプル コード](#sample-code)をコピーして、お使いの IDE またはコンソールで実行することができます。

```sh
python quickstart.py
```

また、このクイック スタート チュートリアルを [Speech SDK のサンプル リポジトリ](https://aka.ms/csspeech/samples)から [Jupyter](https://jupyter.org) ノートブックとしてダウンロードして、ノートブックとして実行することもできます。

### <a name="sample-code"></a>サンプル コード

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Speech SDK と Visual Studio Code をインストールして使用する

1. [Python](https://www.python.org/downloads/) の 64 ビット バージョン (3.5 以降) をお使いのコンピューターにダウンロードして、インストールします。
1. [Visual Studio Code](https://code.visualstudio.com/Download) をダウンロードして、インストールします。
1. Visual Studio Code を開いて、Python 拡張機能をインストールします。 メニューで **[ファイル]**  >  **[基本設定]**  >  **[拡張機能]** の順に選択します。 **Python** を検索します。

   ![Python 拡張機能のインストール](media/sdk/qs-python-vscode-python-extension.png)

1. プロジェクトの保存先となるフォルダーを作成します。 たとえば、エクスプローラーを使用して行います。
1. Visual Studio Code の **[ファイル]** アイコンを選択します。 次に、作成したフォルダーを開きます。

   ![フォルダーを開く](media/sdk/qs-python-vscode-python-open-folder.png)

1. 新しいファイル アイコンを選択して、新しい Python ソース ファイル `speechsdk.py` を作成します。

   ![ファイルを作成する](media/sdk/qs-python-vscode-python-newfile.png)

1. 新しく作成されたファイルに [Python コード](#sample-code)をコピーして貼り付け、保存します。
1. お使いの Speech Services のサブスクリプション情報を挿入します。
1. Python インタープリターが選択されている場合は、ウィンドウの下部にあるステータス バーの左側にそれが表示されます。
   そうでない場合は、使用可能な Python インタープリターの一覧が表示されます。 コマンド パレットを開いて (Ctrl + Shift + P)、「**Python: Select Interpreter**」と入力します。 適切なものを選択します。
1. Visual Studio Code 内から Speech SDK Python パッケージをインストールできます。 選択した Python インタープリター用にまだインストールされていない場合は、インストールします。
   Speech SDK パッケージをインストールするには、ターミナルを開きます。 コマンド パレットをもう一度開いて (Ctrl + Shift + P)、「**Terminal: Create New Integrated Terminal**」と入力してターミナルを開きます。
   開いたターミナルに、コマンド `python -m pip install azure-cognitiveservices-speech` か、システムに応じた適切なコマンドを入力します。
1. サンプル コードを実行するには、エディター内のどこかを右クリックします。 **[Run Python File in Terminal]\(ターミナル内の Python ファイルを実行する\)** を選択します。
   プロンプトが表示されたら、テキストを入力します。 合成された音声がすぐに再生されます。

   ![サンプルを実行する](media/sdk/qs-python-vscode-python-run-tts.png)

これらの手順で問題が発生した場合は、より詳しい [Visual Studio Code Python チュートリアル](https://code.visualstudio.com/docs/python/python-tutorial)を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で Python のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [音声フォントをカスタマイズする](how-to-customize-voice-font.md)
- [音声サンプルを録音する](record-custom-voice-samples.md)
