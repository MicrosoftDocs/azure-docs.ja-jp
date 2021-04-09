---
title: 継続的なビデオ記録 - Azure
description: 継続的なビデオ記録 (CVR) とは、ビデオ ソースからの継続的なビデオ記録プロセスのことを指します。 このトピックでは、CVR について説明します。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: f8b25d74db279f8cfff68f08efeab2975484a0c5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453752"
---
# <a name="continuous-video-recording"></a>継続的なビデオ記録  

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事  

* [メディア グラフの概念](media-graph-concept.md)
* [ビデオ記録の概念](video-recording-concept.md)

## <a name="overview"></a>概要

継続的なビデオ記録 (CVR) とは、ビデオ ソースからの継続的なビデオ記録プロセスのことを指します。 IoT Edge のライブ ビデオ分析は、RTSP ソース ノードと資産シンク ノードで構成される[メディア グラフ](media-graph-concept.md)を使用して、CCTV カメラから 24 時間 365 日ベースで継続的なビデオ記録をサポートします。 そのようなメディア グラフをグラフィカルに表したものが次の図です。 このようなメディア グラフの[グラフ トポロジ](media-graph-concept.md#media-graph-topologies-and-instances)の JSON 表現については、[こちら](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset)を参照してください。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="継続的なビデオ記録":::

上に示したメディア グラフは任意のエッジ デバイス上で実行でき、資産シンクがビデオを Azure Media Services [資産](terminology.md#asset)に記録します。 メディア グラフがアクティブ状態のままである限り、ビデオは記録されます。 ビデオは資産として記録されるため、Media Services の既存のストリーミング機能を使用して再生できます。 詳細については、「[記録されたコンテンツの再生](video-playback-concept.md)」を参照してください。

## <a name="resilient-recording"></a>回復性がある記録

Live Video Analytics on IoT Edge は、エッジ デバイスがときどきクラウドとの接続を失ったり、使用可能な帯域幅が低下したりする可能性がある、完璧ではないネットワーク状態での運用をサポートします。 これを考慮して、ソースからのビデオはローカルでキャッシュに記録され、定期的に資産と自動同期されます。 [グラフ トポロジ JSON](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json) を調べると、次のプロパティが定義されていることがわかります。

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```

後者 2 つのプロパティは、回復性がある記録に関連しています (両方とも資産シンク ノードの必須プロパティです)。 LocalMediaCachePath プロパティは、資産シンクに、そのフォルダー パスを使用して、資産にアップロードする前にメディア データをキャッシュするように指示します。 [この](../../iot-edge/how-to-access-host-storage-from-module.md)記事を読むと、エッジ モジュールがデバイスのローカル ストレージをどのように利用できるかを理解することができます。 LocalMediaCacheMaximumSizeMiB プロパティは、資産シンクがキャッシュとして使用できるディスク領域を定義します (1 MiB = 1024 * 1024 バイト)。 

エッジ モジュールが非常に長い時間接続を失い、キャッシュフォルダーに格納されているコンテンツが localMediaCacheMaximumSizeMiB 値に達すると、資産シンクは、最も古いデータから、キャッシュからのデータの破棄を開始します。 たとえば、デバイスの接続が午前 10 時に失われ、キャッシュが午後 6 時に上限に達した場合、資産シンクは午前 10 時に記録されたデータの削除を開始します。 

ネットワーク接続が復元されると、資産シンクはキャッシュからのアップロードを、やはり最も古いデータから開始します。 上の例では、接続が復元された時刻 (たとえば午後 6:02) までにキャッシュから 5 分間のビデオを破棄する必要があったとすると、資産シンクは午前 10: 05 のマークからアップロードを開始します。

後で[これらの](playback-recordings-how-to.md) API を使用して資産を調べた場合、午前 10 時から午前 10:05 頃までの資産にギャップがあることがわかります。

## <a name="segmented-recording"></a>セグメント化された記録  

上記で説明したように、資産シンク ノードはビデオをローカル キャッシュに記録し、ビデオをクラウドに定期的にアップロードします。 segmentLength プロパティ (上のセクションを参照) は、資産が記録されているストレージ アカウントへのデータの書き込みに関連する書き込みトランザクション コストを制御するのに役立ちます。 たとえば、アップロード期間を 30 秒から 5 分に増やした場合、ストレージ トランザクションの数は 10 分の 1 (5*60/30) に減少します。

segmentLength プロパティにより、segmentLength 秒あたり 1 回は、エッジ モジュールがビデオをアップロードするようになります。 このプロパティの最小値は 30 秒 (既定値) で、30 秒刻みで最大 5 分まで増やすことができます。

> [!NOTE]
> segmentLength が再生に与える影響については、[レコーディングの再生](playback-recordings-how-to.md)に関する記事を参照してください。

## <a name="see-also"></a>関連項目

* [イベントベースのビデオ記録](event-based-video-recording-concept.md)
* [記録されたコンテンツの再生](video-playback-concept.md)

## <a name="next-steps"></a>次のステップ

[チュートリアル: 継続的なビデオ記録](continuous-video-recording-tutorial.md)
