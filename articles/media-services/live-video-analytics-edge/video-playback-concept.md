---
title: ビデオの再生 - Azure
description: プレースホルダー
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 17b1f93c18dfb013916c4c0d3756fb97a73e2675
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87042965"
---
# <a name="video-playback"></a>ビデオ再生 

## <a name="suggested-pre-reading"></a>推奨される事前読み取り 

* [IoT Edge の Live Video Analytics (プレビュー)](overview.md)
* [Live Video Analytics on IoT Edge の用語](terminology.md)
* [メディア グラフの概念](media-graph-concept.md)

## <a name="overview"></a>概要  

[メディア グラフ](media-graph-concept.md)を使用すると、ビデオを Azure Media Services [アセット](terminology.md#asset)に記録できます。 このドキュメントでは、Azure Media Services の既存のストリーミング機能を使用してアセットを再生するために必要な手順について説明します。

## <a name="streaming-endpoint"></a>ストリーミング エンドポイント 

Azure Media Services を使用すると、HTTP ライブ ストリーミング (HLS) や MPEG ダッシュなどの業界標準の HTTP ベースのメディア ストリーミング プロトコルを使用して、アセットをビデオ プレーヤーに[ストリーム](terminology.md#streaming)できます。 記録されたコンテンツからストリーミング形式へのメディアの変換は、[ストリーミング エンドポイント](../latest/streaming-endpoint-concept.md)によって処理されます。これは、Azure Media Service アカウントでプロビジョニングする必要があるリソースです。

## <a name="streaming-policy"></a>ストリーミング ポリシー 

Azure Media Services には、「[Media Services 動的暗号化を使用してコンテンツを保護する](../latest/content-protection-overview.md)」の記事で説明されているように、ビデオ ストリームをセキュリティで保護するためのさまざまな方法が用意されています。 コンテンツ保護のオプションは、大まかには次のとおりです。

* **暗号化なしのストリーミング** - ストリーミング中に暗号化が適用されません。
* **Advanced Encryption Standard (AES-128) の使用** – 認証された視聴者にのみビデオを復号化するためのキーを配信するメソッドを実装します。
* **デジタル著作権管理 (DRM) システムの使用** – これらのポリシーを適用するデバイスへのビデオの使用、変更、および配信を制御します。

コンテンツ保護を実現するには、Media Service アカウントで[ストリーミング ポリシー](../latest/streaming-policy-concept.md)を定義して作成し、すべてのアセットのストリーミングに使用します (すべてのストリームで同じセキュリティ要件が使用されていることを前提としています)。 また、定義済みのポリシー (Predefined_ClearStreamingOnly など) のいずれかを使用することもできます。

## <a name="streaming-locator"></a>ストリーミング ロケーター  

Media Services アカウントでストリーミング エンドポイントを開始し、ストリーミング ポリシーを定義したら、HLS または DASH プロトコルを使用してアセットから録画したメディアをストリーミングすることができます。 Web プレーヤーとモバイル アプリには、HLS または DASH ストリームを指す URL が必要です。 この URL を作成するには、[ストリーミング ロケーター](../latest/streaming-locators-concept.md)を使用します。 この記事で説明され、「[ストリーミング ロケーターの作成と URL の構築](../latest/create-streaming-locator-build-url.md)」で示されているとおり、ストリーミング URL はストリーミング エンドポイント、ストリーミング ポリシー、ストリーミング ロケーターから構成されます。

## <a name="content-recorded-using-file-sink"></a>ファイル シンクを使用して記録されたコンテンツ  

[メディア グラフのファイル シンク](media-graph-concept.md#file-sink)に関する記事で説明されているように、メディア グラフを使用して、メディア グラフ内のファイル シンクを使用して、エッジ デバイスのローカル ファイル システムにビデオを記録することができます。 ファイル シンクによって [MP4](https://developer.mozilla.org/docs/Web/Media/Formats/Containers#MP4) ファイルが生成されます。HTML5 [&lt;video&gt;](https://developer.mozilla.org/docs/Web/HTML/Element/video) 要素を使用して、このようなコンテンツを再生できます。 

## <a name="next-steps"></a>次のステップ

[Azure IoT Edge](../../iot-edge/index.yml)
<!--
## Next steps

[Playback recording](playback-recording-how-to.md)
-->
