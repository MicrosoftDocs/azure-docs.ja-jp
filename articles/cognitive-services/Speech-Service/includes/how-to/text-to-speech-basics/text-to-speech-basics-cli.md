---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: bee28c946242ecf227287fb36b5b03aa6defb1c2
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88170139"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>スピーカーに音声を合成する

Speech CLI を実行して、音声をテキストに合成する準備ができました。 コマンド ラインから、Speech CLI バイナリ ファイルが含まれるディレクトリに変更します。 次に、次のコマンドを実行します。

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

Speech CLI では、コンピューターのスピーカーを通じて、英語の自然言語を生成します。

## <a name="synthesize-speech-to-a-file"></a>音声をファイルに合成する

次のコマンドを実行して、スピーカーからの出力を `.wav` ファイルに変更します。

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Speech CLI では、`greetings.wav` オーディオ ファイルに英語の自然言語を生成します。
Windows では、`start greetings.wav` と入力すると、オーディオファイルを再生できます。