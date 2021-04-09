---
ms.openlocfilehash: 3f92bae608284c8b619be34a0e08f15e831bf88e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750822"
---
「[IoT Edge の配置マニフェストを生成してデプロイする](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)」の手順で、Visual Studio Code で **AZURE IOT HUB** の下にある **lva-sample-device** ノード (左下のセクション内) を展開します。 次のモジュールがデプロイされていることを確認できます。

* `lvaEdge` という名前の Live Video Analytics モジュール
* `rtspsim` モジュール。ライブ ビデオ フィードのソースとして機能する RTSP サーバーをシミュレートします

  ![モジュール](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> 上記の手順は、セットアップ スクリプトによって作成された仮想マシンの使用を前提としています。 独自のエッジ デバイスを使用している場合は、エッジ デバイスにアクセスし、**管理者権限** で次のコマンドを実行して、このクイックスタートで使用するサンプル ビデオ ファイルをプルして保存します。  

```
mkdir /home/lvaedgeuser/samples
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```
