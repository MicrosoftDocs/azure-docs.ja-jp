---
ms.openlocfilehash: 97c21ca300ee070b2cebaa01a585c1618899b1eb
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687249"
---

![概要](../../../media/quickstarts/overview-grpc.png)

この図は、このクイックスタートでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](../../../media-graph-concept.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、[モーション検出プロセッサ](../../../media-graph-concept.md#motion-detection-processor) ノードにビデオ フレームを送信します。 このプロセッサはモーションを検出し、検出時にビデオ フレームを [gRPC 拡張プロセッサ](../../../media-graph-concept.md#grpc-extension-processor) ノードにプッシュします。

gRPC 拡張ノードはプロキシの役割を果たします。 ビデオ フレームを、指定した画像の種類に変換します。 次に、[共有メモリ](https://en.wikipedia.org/wiki/Shared_memory)を介して gRPC エンドポイントの背後で AI モデルを実行する別のエッジ モジュールに、画像を gRPC 経由で転送します。 この例では、そのエッジ モジュールは、さまざまな種類のオブジェクトを検出できる [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) モデルを使用して構築されています。 gRPC 拡張プロセッサ ノードは検出結果を収集し、イベントを [IoT Hub シンク](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/media-graph-concept#iot-hub-message-sink) ノードに発行します。 次に、このノードはこれらのイベントを [IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub) に送信します。

このクイックスタートでは次の作業を行います。

1. メディア グラフを作成してデプロイする。
1. 結果を解釈する。
1. リソースをクリーンアップする。
