---
title: Live Video Analytics on IoT Edge に関する FAQ - Azure
description: このトピックでは、Live Video Analytics on IoT Edge の FAQ に対する回答を示します。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 705c74b1ba6d3a28911185b82a558099e87dc18d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011517"
---
# <a name="frequently-asked-questions-faqs"></a>よく寄せられる質問 (FAQ)

このトピックでは、Live Video Analytics on IoT Edge の FAQ に対する回答を示します。

## <a name="general"></a>全般

グラフ トポロジ定義で使用できるシステム変数は何ですか。

|変数   |説明|
|---|---|
|[System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|特定の時点を表します。通常、日時形式で表されます。|
|System.GraphTopologyName   |メディア グラフ トポロジを表します。グラフのブループリントを保持します。|
|System.GraphInstanceName|  メディア グラフ インスタンスを表します。パラメーター値を保持し、トポロジを参照します。|

## <a name="configuration-and-deployment"></a>構成とデプロイ

メディア Edge モジュールを Windows 10 デバイスに展開することはできますか。
    * はい。 [Windows 10 での Linux コンテナー](/virtualization/windowscontainers/deploy-containers/linux-containers)に関する記事を参照してください。

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>IP カメラと RTSP 設定からのキャプチャ

* ビデオ ストリームで送信するには、デバイスで特別な SDK を使用する必要がありますか。
    * いいえ。 Live Video Analytics on IoT Edge では、RTSP ビデオ ストリーミング プロトコル (ほとんどの IP カメラでサポートされています) を使用したメディアのキャプチャがサポートされています。
* RTMP または Smooth (Media Services ライブ イベントなど) を使用して Live Video Analytics on IoT Edge にメディアをプッシュできますか。
    * いいえ。 LVA では、IP カメラからのビデオのキャプチャにのみ RTSP がサポートされます。
    * TCP/HTTP 経由の RTSP ストリーミングをサポートするすべてのカメラが動作します。 
* グラフ インスタンスで RTSP ソース URL をリセットまたは更新できますか。
    * はい (グラフ インスタンスが非アクティブな状態の場合)。  
* テストおよび開発中に使用できる RTSP シミュレーターはありますか。
    * はい。 学習プロセスをサポートするためのクイック スタートとチュートリアルで使用できる [RTSP シミュレーター](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) Edge モジュールがあります。 このモジュールは、ベストエフォートとして提供されており、常に使用できるとは限りません。 これは、数時間以上使用しないことを強くお勧めします。 運用環境のデプロイの計画を作成する前に、実際の RTSP ソースを使用したテストに投資する必要があります。
* Edge での IP カメラの ONVIF 検出をサポートしていますか。
    * いいえ。Edge 上のデバイスの ONVIF 検出はサポートされていません。

## <a name="streaming-and-playback"></a>ストリーミングと再生

* Edge から AMS に記録されたアセットは、HLS や DASH などの Media Services ストリーミング テクノロジを使用して再生できますか。
    * はい。 記録されたアセットは、Azure Media Services の他のアセットと同様にストリーミングできます。 コンテンツをストリーミングするには、ストリーミング エンドポイントを作成し、実行状態にする必要があります。 標準ストリーミング ロケーターの作成プロセスを使用すると、任意の機能を持つプレーヤー フレームワークにストリーミングするための HLS または DASH のマニフェストにアクセスできます。 発行 HLS または DASH のマニフェストの作成の詳細については、「[ダイナミック パッケージ](../latest/dynamic-packaging-overview.md)」を参照してください。
* アーカイブされたアセットで Media Services の標準的なコンテンツ保護と DRM 機能を使用できますか。
    * はい。 標準の動的暗号化コンテンツ保護と DRM 機能はすべて、メディア グラフから記録されたアセットで使用できます。
* 記録されたアセットのコンテンツを表示するには、どのプレーヤーを使用できますか。
   * 準拠している Apple HTTP ライブ ストリーミング (HLS) バージョン 3 またはバージョン 4 をサポートするすべての標準プレーヤーがサポートされています。 また、準拠している MPEG-DASH の再生が可能なプレーヤーもサポートされています。
    テストに推奨されるプレーヤーは次のとおりです。

    * [Azure Media Player](../latest/use-azure-media-player.md)
    * [HLS.js](https://hls-js.netlify.app/demo/)
    * [Video.js](https://videojs.com/)
    * [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
    * [Shaka Player](https://github.com/google/shaka-player)
    * [ExoPlayer](https://github.com/google/ExoPlayer)
    * [Apple ネイティブ HTTP ライブ ストリーミング](https://developer.apple.com/streaming/)
    * Edge、Chrome、または Safari に組み込まれている HTML5 ビデオ プレーヤー
    * HLS または DASH の再生をサポートする商用プレーヤー
* メディア グラフ アセットのストリーミングにはどのような制限がありますか。
    * Media Services は、メディアと娯楽、OTT、およびブロードキャストの各顧客に対してオンデマンドおよびライブ ストリーミングをサポートします。メディア グラフからライブまたは記録されたアセットをストリーミングするには、同じ大規模なインフラストラクチャとストリーミング エンドポイントを使用します。 つまり、Azure CDN、Verizon、または Akamai をすばやく簡単に有効にして、シナリオに応じて数人の視聴者から最大で数百万の視聴者にコンテンツを配信することができます。

    コンテンツは、Apple HTTP ライブ ストリーミング (HLS) と MPEG-DASH の両方を使用して配信できます。

## <a name="monitoring-and-metrics"></a>監視とメトリック

* Event Grid を使用して、Edge のメディア グラフを監視できますか。
    * いいえ。 現在 Event Grid はサポートされていません。
* Azure Monitor を使用して、クラウドまたは Edge のメディア グラフの正常性、メトリック、およびパフォーマンスを表示できますか。
    * いいえ。
* Media Services IoT Edge モジュールを簡単に監視できるツールはありますか。
    * Visual Studio Code では、LVAEdge モジュール エンドポイントを簡単に監視できる "Azure IoT Tools" 拡張機能がサポートされています。 このツールを使用すると、"イベント" の IoT Hub 組み込みエンドポイントの監視をすばやく開始し、Edge デバイスからクラウドにルーティングされる推論メッセージを確認できます。 

    また、この拡張機能を使用して、LVAEdge モジュールのモジュール ツインを編集し、メディア グラフの設定を変更することもできます。

詳細については、[監視とログ記録](monitoring-logging.md)に関する記事をご覧ください。

## <a name="billing-and-availability"></a>課金と可用性

* Live Video Analytics on IoT Edge はどのように課金されますか。
    * 詳細については、[価格](https://azure.microsoft.com/pricing/details/media-services/)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

[クイック スタート: はじめに - Live Video Analytics on IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
