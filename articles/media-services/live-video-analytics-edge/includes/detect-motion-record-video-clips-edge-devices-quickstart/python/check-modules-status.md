---
ms.openlocfilehash: 53052097fa6616f889b710c58488a9f7a616168d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956308"
---
「[IoT Edge の配置マニフェストを生成してデプロイする](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)」の手順で、Visual Studio Code で **AZURE IOT HUB** の下にある **lva-sample-device** ノード (左下のセクション内) を展開します。 次のモジュールがデプロイされていることを確認できます。

* `lvaEdge` という名前の Live Video Analytics モジュール
* `rtspsim` モジュール。ライブ ビデオ フィードのソースとして機能する RTSP サーバーをシミュレートします

  ![モジュール](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> 上記の手順は、セットアップ スクリプトによって作成された仮想マシンの使用を前提としています。 独自のエッジ デバイスを使用している場合は、エッジ デバイスにアクセスし、**管理者権限** で次のコマンドを実行して、このクイックスタートで使用するサンプル ビデオ ファイルをプルして保存します。  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
