---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a9beaea70dd3c8ce852344b11a50c5f20e5f8ca2
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806211"
---
## <a name="enable-microphone"></a>マイクを有効にする

PC マイクを電源に接続してオンにして、マイクを使用する可能性のあるすべてのアプリをオフにします。 コンピューターによっては、マイクが内蔵されているものや、Bluetooth デバイスの構成が必要なものもあります。

## <a name="run-the-speech-cli"></a>Speech CLI を実行する

Speech CLI を実行して、マイクからの音声を認識する準備ができました。

1. **アプリを開始する** - コマンド ラインから Speech CLI バイナリ ファイルが含まれるディレクトリに変更して、次のように入力します。
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > Speech CLI の既定値は英語です。 [「音声テキスト変換」表から](../../../../language-support.md)別の言語を選択できます。
    > たとえば、ドイツ語の音声を認識するには、`--source de-DE` を追加します。

2. **[認識を開始する]** - マイクに向かって話します。 自分が発した言葉がテキストに文字起こしされて、リアルタイムに表示されます。 Speech CLI は、無音の状態が一定の時間続いた後、または Ctrl + C キーを押したときに停止します。
