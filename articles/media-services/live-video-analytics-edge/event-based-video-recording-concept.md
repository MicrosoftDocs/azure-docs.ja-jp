---
title: イベントベースのビデオ記録 - Azure
description: イベントベースのビデオ記録 (EVR) とは、イベントによってトリガーされるビデオ記録のプロセスのことです。 対象となるイベントは、ビデオ信号自体の処理が原因で発生したもの (動きの検出など) でも、独立したソースからのもの (ドアを開くなど) でもかまいません。  この記事では、イベントベースのビデオ記録に関連するいくつかのユース ケースについて説明します。
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 6a5f4873b2cfef8d9a6594916d82cd30a3bc1cc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97401605"
---
# <a name="event-based-video-recording"></a>イベントベースのビデオ記録  
 
## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事  

* [継続的なビデオ記録](continuous-video-recording-concept.md)
* [記録されたコンテンツの再生](video-playback-concept.md)
* [メディア グラフの概念](media-graph-concept.md)

## <a name="overview"></a>概要 

イベントベースのビデオ記録 (EVR) とは、イベントによってトリガーされるビデオ記録のプロセスのことです。 対象となるイベントは、ビデオ信号自体の処理が原因で発生したものでも (動きの検出など)、独立したソースからのものでも (ドアを開くなど) かまいません。 

以下のユース ケースで説明されているように、[RTSP ソース](media-graph-concept.md#rtsp-source) ノード、[資産シンク](media-graph-concept.md#asset-sink) ノード、その他のノードで構成されるメディア グラフを使用して、CCTV カメラから Media Services 資産に (イベントによってトリガーされた) ビデオを記録することができます。 イベントが発生するたびに新しい資産が生成されるように[資産シンク](media-graph-concept.md#asset-sink) ノードを構成し、各イベントに対応するビデオが個別の資産になるようにすることができます。 また、1 つの資産を使用して、すべてのイベントのビデオを保持することもできます。 

資産シンク ノードの代わりに、[ファイル シンク](media-graph-concept.md#file-sink) ノードを使用して、(関心のあるイベントに関連する) ビデオの短いスニペットを、エッジ デバイス上のローカル ファイル システムの指定した場所に、キャプチャすることができます。 

この記事では、イベントベースのビデオ記録に関連するいくつかのユース ケースについて説明します。

### <a name="video-recording-based-on-motion-detection"></a>モーション検出に基づくビデオ記録  

このユース ケースでは、ビデオ内でモーションが検出さた場合にのみビデオ クリップを記録でき、そのようなビデオ クリップが生成されたらアラートを受け取ることができます。 これは、重要なインフラストラクチャの保護を含む商用のセキュリティ シナリオに関連する場合があります。 予期しないモーションが検出されたときにアラートを生成してビデオを記録することで、対応する必要があるのはアラートが生成されたときだけでよいので、人間のオペレーターの効率を向上させることができます。

次の図は、このユース ケースに対応するメディア グラフをグラフィカルに表したものです。 このようなメディア グラフのグラフ トポロジの JSON 表現については、[こちら](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json)を参照してください。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/motion-detection.svg" alt-text="モーション検出に基づくビデオ記録":::

この図では、カメラからのライブ ビデオ フィードが RTSP ソース ノードによってキャプチャされ、それが[モーション検出プロセッサ](media-graph-concept.md#motion-detection-processor) ノードに配信されます。 ライブ ビデオでモーションが検出されると、モーション検出プロセッサ ノードによってイベントが生成され、それが[シグナル ゲート プロセッサ](media-graph-concept.md#signal-gate-processor) ノードと IoT Hub メッセージ シンク ノードに送信されます。 後者のノードによってイベントが IoT Edge ハブに送信され、イベントはそこから他の送信先にルーティングされて、アラートがトリガーされます。 

モーション検出ノードからのイベントによってシグナル ゲート プロセッサ ノードがトリガーされ、RTSP ソース ノードからのライブ ビデオ フィードが資産シンク ノードに送信されます。 このような後続のモーション検出イベントがない場合、ゲートは構成された時間が経過すると閉じられます。 これにより、記録されるビデオの期間が決まります。

### <a name="video-recording-based-on-events-from-other-sources"></a>他のソースからのイベントに基づくビデオ記録  

このユース ケースでは、別の IoT センサーからのシグナルを使用して、ビデオの記録をトリガーできます。 次の図は、このユース ケースに対応するメディア グラフをグラフィカルに表したものです。 このようなメディア グラフのグラフ トポロジの JSON 表現については、[こちら](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json)を参照してください。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/other-sources.svg" alt-text="他のソースからのイベントに基づくビデオ記録":::

この図では、外部センサーによって IoT Edge ハブにイベントが送信されます。 その後イベントは、[IoT Hub メッセージ ソース](media-graph-concept.md#iot-hub-message-source) ノード経由でシグナル ゲート プロセッサ ノードにルーティングされます。 シグナル ゲート プロセッサ ノードの動作は、前のユース ケースと同じです。ノードは、外部イベントによってトリガーされると開き、RTSP ソース ノードからファイル シンク ノード (または資産シンク ノード) へのライブ ビデオ フィードがフローできるようになります。 

ファイル シンク ノードを使用すると、ビデオはエッジ デバイス上のローカル ファイル システムに記録されます。 そうではなく、資産シンク ノードを使用した場合は、ビデオは資産に記録されます。

### <a name="video-recording-based-on-an-external-inferencing-module"></a>外部推論モジュールに基づくビデオ記録 

このユース ケースでは、外部のロジック システムからのシグナルに基づいてビデオ クリップを記録できます。 このようなユース ケースの例は、高速道路の交通状況のビデオ フィードでトラックが検出されたときにだけビデオ クリップを記録する場合です。 次の図は、このユース ケースに対応するメディア グラフをグラフィカルに表したものです。 このようなメディア グラフのグラフ トポロジの JSON 表現については、[こちら](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)を参照してください。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/external-inferencing-module.svg" alt-text="外部推論モジュールに基づくビデオ記録":::

この図では、RTSP ソース ノードによってカメラからのライブ ビデオ フィードがキャプチャされ、2 つのブランチに配信されています。1 つには[シグナル ゲート プロセッサ](media-graph-concept.md#signal-gate-processor) ノードがあり、もう 1 つでは [HTTP 拡張](media-graph-concept.md)ノードを使用して外部のロジック モジュールにデータが送信されています。 HTTP 拡張ノードを使用すると、メディア グラフで REST を使用して外部の推論サービスに画像フレーム (JPEG、BMP、または PNG 形式) を送信できます。 このシグナル パスでは、通常、低フレーム レート (5 fps 未満) のみがサポートされます。 HTTP 拡張機能プロセッサ ノードを使用すると、外部推論モジュールに送信されるビデオのフレーム レートを下げることができます。

外部推論サービスからの結果は、HTTP 拡張ノードによって取得され、IoT Hub メッセージ シンク ノードを介して IoT Edge ハブに中継されます。そこでは、外部ロジック モジュールによって結果をさらに処理できます。 たとえば、推論サービスで車両を検出できる場合、ロジック モジュールではバスやトラックなどの特定の車両を探すことができます。 ロジック モジュールでは、目的のものが検出されると、IoT Edge ハブ経由でグラフの IoT Hub メッセージ ソース ノードにイベントを送信することにより、シグナル ゲート プロセッサ ノードをトリガーできます。 シグナル ゲートからの出力では、ファイル シンク ノードまたは資産シンク ノードのいずれかに送信することが示されています。 前者では、ビデオはエッジ デバイス上のローカル ファイル システムに記録されます。 後者の場合は、ビデオは資産に記録されます。

この例を拡張し、HTTP 拡張機能プロセッサ ノードの前の段階でモーション検出プロセッサを使用します。 これにより、高速道路に長時間車両が存在しない場合がある夜間など、推論サービスの負荷が軽減されます。 

## <a name="next-steps"></a>次のステップ

[チュートリアル: イベントベースのビデオ記録](event-based-video-recording-tutorial.md)
