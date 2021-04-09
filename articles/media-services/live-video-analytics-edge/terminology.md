---
title: IoT Edge の Live Video Analytics の用語 - Azure
description: この記事では、Live Video Analytics on IoT Edge の用語の概要について説明します。
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: e3a77b69adf2241a4af2652db4edb6673a63b4f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88690614"
---
# <a name="terminology"></a>用語

この記事では、[Live Video Analytics on IoT Edge](overview.md) に関連する用語の概要について説明します。

## <a name="azure-media-services"></a>Azure Media Services

Azure Media Services は、メディア ソリューションの構築を可能にするクラウド メディア プラットフォームです。 詳細については、[Azure Media Services](../latest/media-services-overview.md) のドキュメントを参照してください。

## <a name="asset"></a>Asset

[アセット](../latest/assets-concept.md)は Azure Media Services 内のエンティティであり、Media Services アカウントにアタッチされている Azure ストレージ アカウントの BLOB コンテナーにマップされます。 アセットに関連付けられているすべてのファイルは BLOB としてそのコンテナーに格納され、Media Services には、アセットに関連付けられたメタデータ (名前、説明、作成時刻など) が保持されます。

Live Video Analytics on IoT Edge では、アセットを作成したり、既存の資産にデータを追加したりすることができます。 これにより、継続的なイベントベースのビデオ記録と再生 (エッジ デバイスでのビデオ キャプチャ、Azure Media Services への記録、既存の Azure Media Services ストリーミング機能による再生) のシナリオが可能になります。

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) は言語に依存しない高性能なリモート プロシージャ コール (RPC) フレームワークです。 通信の基礎となるメッセージ交換形式として、[Protocol Buffers 3](https://developers.google.com/protocol-buffers/docs/proto3) を介したセッションベースの構造化スキーマを使用しています。

## <a name="media-graph"></a>メディア グラフ

[メディア グラフ](media-graph-concept.md)を使用すると、キャプチャ元のメディア、処理方法、および結果の配信先を定義できます。 これにより、ソース、プロセッサ、およびシンク ノードで構成されるグラフを定義できるため、ライブ ビデオ分析アプリケーションを作成することができます。 メディア グラフの詳細については、メディア グラフの概念のページをご覧ください。

## <a name="recording"></a>記録

セキュリティ カメラ用のビデオ管理システムのコンテキストでは、ビデオ記録とは、カメラからビデオをキャプチャし、その後モバイルおよびブラウザー アプリで表示するためにそれをファイルに格納するプロセスのことです。 ビデオ記録は、[連続ビデオ記録](continuous-video-recording-concept.md)と[イベントベースのビデオ記録](event-based-video-recording-concept.md)に分類できます。 これらの詳細については、[ビデオ記録](video-recording-concept.md)の概念のページを参照してください。

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) とは、リアルタイム ストリーミング プロトコルのことです。 これは、リアルタイム プロパティを使用してデータの配信を制御するためのアプリケーション レベルのプロトコルです。 RTSP により、オーディオやビデオなどのリアルタイム データのオンデマンド配信を制御できるようにする拡張可能なフレームワークが提供されます。 

## <a name="streaming"></a>ストリーミング

Netflix、YouTube などのサービスからモバイル デバイスでビデオを視聴したことがある場合、ストリーミング ビデオを経験済みです。 再生は、[再生] を押すとすぐに開始され (十分な帯域幅がある場合)、ビデオのタイムラインを前後にシークできます。 ストリーミングでは、ビデオの視聴されている部分だけを配信し、データがまだサーバーから再生クライアントに転送されている間に視聴者がビデオの再生を開始できるようにします。 Azure Media Services のコンテキストでは、[ストリーミング](https://en.wikipedia.org/wiki/Streaming_media)は、[Azure Media Services](../azure-media-player/azure-media-player-overview.md) からストリーミング クライアント (Azure Media Player など) にメディアを配信するプロセスを指します。 Azure Media Services を使用すると、[HTTP ライブ ストリーミング (HLS)](https://developer.apple.com/streaming/) や [MPEG-DASH](https://dashif.org/about/) などの業界標準の HTTP ベースのメディア ストリーミング プロトコルを使用して、ビデオをクライアントにストリームできます。 HLS は、Azure Media Player および [JW Player](https://www.jwplayer.com/)、[hls.js](https://github.com/video-dev/hls.js/)、[VideoJS](https://videojs.com/)、[Google’s Shaka Player](https://github.com/google/shaka-player) などの Web プレーヤーでサポートされており、あるいは Android の [Exoplayer](https://github.com/google/ExoPlayer) や iOS の [AV Foundation](https://developer.apple.com/av-foundation/) を使用してモバイル アプリでネイティブにレンダリングすることができます。 同様に、MPEG-DASH は Azure Media Player および[こちらのページにあるクライアントの一覧](https://dashif.org/clients/)でサポートされています。 

[メディア グラフ](#media-graph)を使用して Azure Media Services の資産にビデオを記録することにより、Media Services のストリーミング機能を使用して、HLS および DASH でビデオ ストリームを配信できます。 詳細については、[ビデオ再生](video-playback-concept.md)に関する記事をご覧ください。

## <a name="vms"></a>VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system) とは、ビデオ管理システムのことです。 このようなシステムは、CCTV カメラの構成と制御、それらからのビデオのキャプチャと記録に使用されます。 これらのシステムでは、記録されたビデオを再生するクライアント アプリケーションも提供されます。

## <a name="next-steps"></a>次のステップ

[メディア グラフ](media-graph-concept.md)
