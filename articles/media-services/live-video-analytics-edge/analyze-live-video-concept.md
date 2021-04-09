---
title: 記録を行わないライブ ビデオの分析 - Azure
description: メディア グラフは、ライブ ビデオ ストリームから分析を単に抽出するために使用できます。これをエッジやクラウドに記録する必要はありません。 この記事ではこの概念について説明します。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 25a7cadc47603b726542fa391d441e1fbca78908
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97398976"
---
# <a name="analyzing-live-video-without-any-recording"></a>記録を行わないライブ ビデオの分析

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事 

* [メディア グラフの概念](media-graph-concept.md)
* [イベントベースのビデオ記録](event-based-video-recording-concept.md)

## <a name="overview"></a>概要  

メディア グラフを使用して、ビデオの一部をファイルや資産に記録することなく、ライブ ビデオを分析することができます。 以下に示すメディア グラフは、「[イベントベースのビデオ記録](event-based-video-recording-concept.md)」の記事のものと似ていますが、資産シンク ノードやファイル シンク ノードはありません。

### <a name="motion-detection"></a>モーション検出

以下に示すメディア グラフは、[RTSP ソース](media-graph-concept.md#rtsp-source) ノード、[モーション検出プロセッサ](media-graph-concept.md#motion-detection-processor) ノード、および [IoT Hub メッセージ シンク](media-graph-concept.md#iot-hub-message-sink) ノードで構成されています。 このようなメディア グラフのグラフ トポロジの JSON 表現については、[こちら](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json)を参照してください。 このグラフを使用すると、受信ライブ ビデオ ストリームのモーションを検出し、IoT Hub メッセージ シンク ノードを介して他のアプリやサービスにモーション イベントを中継できます。 外部のアプリまたはサービスではアラートをトリガーしたり、適切な担当者に通知を送信したりすることができます。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="モーション検出に基づく Live Video Analytics":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>カスタム ビジョン モデルを使用するビデオの分析 

以下に示すメディア グラフでは、別のモジュールにパッケージ化されたカスタム ビジョン モデルを使用してライブ ビデオ ストリームを分析できます。 このようなメディア グラフのグラフ トポロジの JSON 表現については、[こちら](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)を参照してください。 推論サービスとして実行される IoT Edge モジュールにモデルをラップするいくつかの例については、[こちら](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis)を参照してください。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detected-frames.svg" alt-text="外部の推論モジュールに基づく Live Video Analytics":::

このメディア グラフでは、RTSP ソースからのビデオ入力が [HTTP 拡張プロセッサ](media-graph-concept.md#http-extension-processor) ノードに送信され、そこからイメージ フレーム (JPEG、BMP、または PNG 形式) が REST 経由で外部の推論サービスに送信されます。 外部の推論サービスからの結果は、HTTP 拡張ノードによって取得され、IoT Hub メッセージ シンク ノードを介して IoT Edge ハブに中継されます。 この種のメディア グラフは、たとえば、交差点での車両の時系列分布の理解や、小売店でのコンシューマー トラフィック パターンの把握など、さまざまなシナリオ向けのソリューションを構築するために使用できます。
>[!TIP]
> HTTP 拡張プロセッサ ノード内のフレーム レートをダウンストリームに送信する前に、`samplingOptions` フィールドを使用して管理できます。

この例を拡張し、HTTP 拡張プロセッサ ノードの前にモーション検出プロセッサを使用します。 これにより、ビデオにモーション アクティビティがある場合にのみ使用されるため、推論サービスの負荷が軽減されます。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/custom-model.svg" alt-text="外部推論モジュールによるモーション検出フレームに基づく Live Video Analytics":::

## <a name="next-steps"></a>次のステップ

[継続的なビデオ記録](continuous-video-recording-concept.md)
