---
ms.openlocfilehash: 69fd32c1569dbd1f08815be156585b0cce9f10d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98061116"
---
Azure リソースを設定する際に、高速道路のトラフィックの短いビデオが、IoT Edge デバイスとして使用している、Azure の Linux VM にコピーされます。 このクイックスタートでは、このビデオ ファイルを使用してライブ ストリームをシミュレートします。

[VLC メディア プレーヤー](https://www.videolan.org/vlc/)などのアプリケーションを開きます。 `Ctrl+N` キーを押し、[ハイウェイの交差点のサンプル ビデオ](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv)へのリンクを貼り付けて、再生を開始します。 高速道路のトラフィック内を移動する多くの車両の映像が表示されます。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

このクイックスタートでは、Live Video Analytics on IoT Edge を使用して、車両や人物などのオブジェクトを検出します。 関連付けられている推論イベントを IoT Edge Hub に発行します。
