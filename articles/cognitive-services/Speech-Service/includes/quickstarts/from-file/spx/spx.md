---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 398dfe36314f86614515168986023146d695e69b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806420"
---
## <a name="find-a-file-that-contains-speech"></a>音声が含まれるファイルを検索する

Speech CLI では、多くのファイル形式と自然言語で音声認識を行うことができます。 このクイックスタートでは、英語の音声が含まれる WAV ファイル (16 kHz、8 kHz、16 ビット、および mono PCM) を使用できます。

1. <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav<span class="docon docon-download x-hidden-focus"></span></a> をダウンロードします。
2. `whatstheweatherlike.wav` ファイルを Speech CLI バイナリ ファイルと同じディレクトリにコピーします。

## <a name="run-the-speech-cli"></a>Speech CLI を実行する

Speech CLI を実行して、音声ファイルで検出された音声を認識する準備ができました。

コマンド ラインから、Speech CLI バイナリ ファイルが含まれるディレクトリに変更して、次のように入力します。

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> Speech CLI の既定値は英語です。 [「音声テキスト変換」表から](../../../../language-support.md)別の言語を選択できます。
> たとえば、ドイツ語の音声を認識するには、`--source de-DE` を追加します。

Speech CLI では、音声がテキストに文字起こしされて、画面に表示されます。 その後、Speech CLI は終了します。
