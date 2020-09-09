---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: c73ee93d89a350763e8ced490187e1c47f8918de
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806324"
---
## <a name="run-the-speech-cli"></a>Speech CLI を実行する

Speech CLI を実行して、音声を 2 つの異なる言語のテキストに翻訳する準備ができました。

コマンド ラインから、Speech CLI バイナリ ファイルが含まれるディレクトリに変更して、次のように入力します。

```bash
spx translate --microphone --target de-DE --target es-MX
```

Speech CLI では、英語で話された自然言語をドイツ語とスペイン語 (メキシコ) に翻訳し、テキストに印刷します。
ツールを停止するには、Enter キーを押します。

> [!NOTE]
> Speech CLI の既定値は英語です。 [「音声テキスト変換」表から](../../../../language-support.md)別の言語を選択できます。
> たとえば、日本語の音声を認識するには、`--source ja-JP` を追加します。
