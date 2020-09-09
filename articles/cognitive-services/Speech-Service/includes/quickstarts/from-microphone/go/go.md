---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 99540e8518aa31a2221844aa954ff665609d8217
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374819"
---
## <a name="prerequisites"></a>前提条件

開始する前に、以下の操作を行います。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [ご自分の開発環境を設定し、空のプロジェクトを作成する](../../../../quickstarts/setup-platform.md)
> * オーディオ キャプチャ用のマイクにアクセスできることを確認する

## <a name="setup-your-environment"></a>環境を設定する

次の行を追加して、go.mod ファイルを最新の SDK バージョンで更新します。
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.13.0
)
```

## <a name="start-with-some-boilerplate-code"></a>定型コードを使用して開始する
1. ご使用のソース ファイル (例: `sr-quickstart.go`) の内容を次に置き換えます。これには次のものが含まれます。

- "main" パッケージの定義
- Speech SDK からの必要なモジュールのインポート
- このクイック スタートの後半で置き換えられるサブスクリプション情報を格納するための変数
- オーディオ入力用のマイクを使用したシンプルな実装
- 音声認識中に発生するさまざまなイベントのイベント ハンドラー

```sh
package recognizer

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

`YOUR_SUBSCRIPTION_KEY` と `YOUR_SUBSCRIPTIONKEY_REGION` の値は、Speech リソースの実際の値に置き換えてください。

- Azure portal に移動し、Speech リソースを開きます
- 左側の **[キー]** に、利用可能なサブスクリプション キーが 2 つあります
    - そのどちらか一方で `YOUR_SUBSCRIPTION_KEY` の値を置き換えます
- 左側の **[概要]** で、リージョンをメモし、それをリージョン識別子にマッピングします
- `YOUR_SUBSCRIPTIONKEY_REGION` の置換値としてリージョン識別子を使用します (**米国西部**の場合は `"westus"` など)

## <a name="code-explanation"></a>コードの説明
音声構成オブジェクトを作成するには、Speech のサブスクリプション キーとリージョンが必要です。 音声認識エンジン オブジェクトをインスタンス化するには、この構成オブジェクトが必要です。

認識エンジン インスタンスは、複数の音声認識方法を公開します。 この例では、音声は継続的に認識されます。 この機能によって、認識のために多くの語句が送信されていることと、音声の認識を停止するためにプログラムを終了するタイミングを Speech サービスに知らせることができます。 結果が生成されると、コードによってコンソールに出力されます。

## <a name="build-and-run"></a>ビルドおよび実行
これで、プロジェクトをビルドし、Speech サービスを使用して音声認識をテストするように設定できました。
1. プロジェクト (例: **"go build"** ) をビルドします。
2. モジュールを実行して、デバイスのマイクに向かってフレーズや文章を話します。 音声が Speech サービスに送信され、テキストに変換されます。これは出力に表示されます。


> [!NOTE]
> Speech SDK では、既定で認識される言語が en-us です。ソース言語の選択については、「[音声テキスト変換のソース言語を指定する](../../../../how-to-specify-source-language.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

[!INCLUDE [footer](./footer.md)]
