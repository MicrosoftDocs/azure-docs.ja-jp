---
ms.openlocfilehash: df6137e4e00ebd089ef7fb5efa163a513fb30728
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98061146"
---
このクイックスタートの Azure リソースを設定する際に、駐車場の短いビデオが、IoT Edge デバイスとして使用される Azure の Linux VM にコピーされます。 このビデオ ファイルは、このチュートリアルのライブ ストリームをシミュレートするために使用されます。

[VLC メディア プレーヤー](https://www.videolan.org/vlc/)などのアプリケーションを起動し、Ctrl キーを押しながら N キーを押して、[このリンク](https://lvamedia.blob.core.windows.net/public/lots_015.mkv)を駐車場ビデオに貼り付けて再生を開始します。 だいたい 5 秒の地点で、白の車が駐車場を通って移動します。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

次の手順を完了すると、Live Video Analytics on IoT Edge を使用してその車の動きが検出され、5 秒のマークあたりから始まるビデオ クリップが録画されます。
