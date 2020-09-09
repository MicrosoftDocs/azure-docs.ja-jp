---
title: 記録を行わないライブ ビデオの分析 - Azure
description: メディア グラフは、ライブ ビデオ ストリームから分析を単に抽出するために使用できます。これをエッジやクラウドに記録する必要はありません。 この記事ではこの概念について説明します。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 29e00b9c04a652771ca150e2a45e980d20f8bc1f
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84260185"
---
# <a name="analyzing-live-video-without-any-recording"></a>記録を行わないライブ ビデオの分析

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事 

* [メディア グラフの概念](media-graph-concept.md)
* [イベントベースのビデオ記録](event-based-video-recording-concept.md)

## <a name="overview"></a>概要  

メディア グラフを使用して、ビデオの一部をファイルや資産に記録することなく、ライブ ビデオを分析することができます。 以下に示すメディア グラフは、「[イベントベースのビデオ記録](event-based-video-recording-concept.md)」の記事のものと似ていますが、資産シンク ノードやファイル シンク ノードはありません。

### <a name="motion-detection"></a>モーション検出

以下に示すメディア グラフは、[RTSP ソース](media-graph-concept.md#rtsp-source) ノード、[モーション検出プロセッサ](media-graph-concept.md#motion-detection-processor) ノード、および [IoT Hub メッセージ シンク](media-graph-concept.md#iot-hub-message-sink) ノードで構成されています。 このようなメディア グラフのグラフ トポロジの JSON 表現については、[こちら](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json)を参照してください。 このグラフを使用すると、受信ライブ ビデオ ストリームのモーションを検出し、IoT Hub メッセージ シンク ノードを介して他のアプリやサービスにモーション イベントを中継できます。 外部のアプリまたはサービスではアラートをトリガーしたり、適切な担当者に通知を送信したりすることができます。

![モーション検出に基づく Live Video Analytics](./media/analyze-live-video/motion-detection.png)

### <a name="analyzing-video-using-a-custom-vision-model"></a>カスタム ビジョン モデルを使用するビデオの分析 

以下に示すメディア グラフでは、別のモジュールにパッケージ化されたカスタム ビジョン モデルを使用してライブ ビデオ ストリームを分析できます。 このようなメディア グラフのグラフ トポロジの JSON 表現については、[こちら](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)を参照してください。 推論サービスとして実行される IoT Edge モジュールにモデルをラップするいくつかの例については、[こちら](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis)を参照してください。

![外部の推論モジュールに基づく Live Video Analytics](./media/analyze-live-video/external-inferencing-module.png)

このメディア グラフでは、フレーム レート フィルター プロセッサ ノードで受信ライブ ビデオ ストリームのフレーム レートが下げられ、その後、[HTTP 拡張プロセッサ](media-graph-concept.md#http-extension-processor) ノードに送信されます。このノードでは、(JPEG、BMP、または PNG 形式の) イメージ フレームを REST 経由で外部の推論サービスに送信します。 外部の推論サービスからの結果は、HTTP 拡張ノードによって取得され、IoT Hub メッセージ シンク ノードを介して IoT Edge ハブに中継されます。 この種のメディア グラフは、たとえば、交差点での車両の時系列分布の理解や、小売店でのコンシューマー トラフィック パターンの把握など、さまざまなシナリオ向けのソリューションを構築するために使用できます。

この例を拡張し、フレーム レート フィルター プロセッサ ノードの前の段階でモーション検出プロセッサを使用します。 これにより、ビデオにモーション アクティビティがある場合にのみ使用されるため、推論サービスの負荷が軽減されます。

![外部推論モジュールによるモーション検出フレームに基づく Live Video Analytics](./media/analyze-live-video/motion-detected-frames.png)

## <a name="next-steps"></a>次のステップ

[継続的なビデオ記録](continuous-video-recording-concept.md)
