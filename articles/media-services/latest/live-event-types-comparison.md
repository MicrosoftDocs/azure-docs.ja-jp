---
title: Azure Media Services LiveEvent の種類 |Microsoft Docs
description: このアーティクルでは、LiveEvent の種類を比較する詳細な表を示します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: 9952a7bbac1eb79de0d3425f839e3bd30196844e
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243892"
---
# <a name="live-event-types-comparison"></a>ライブ イベントの種類の比較

Azure Media Services の[ライブ イベント](https://docs.microsoft.com/rest/api/media/liveevents)には、ライブ エンコードとパススルーの 2 種類があります。 

## <a name="types-comparison"></a>種類の比較 

次の表は、2 種類のライブ イベントの機能を比較したものです。

| 機能 | パススルー ライブ イベント | 標準のライブ イベント |
| --- | --- | --- |
| シングル ビットレートの入力がクラウド内でマルチビットレートにエンコードされる |いいえ  |はい |
| コントリビューション フィードの最大ビデオ解像度 |4K (60 フレーム/秒で 4096x2160) |1080p (30 フレーム/秒 で 1920 x 1088)|
| コントリビューション フィードの推奨最大レイヤー|最大 12 個|1 つのオーディオ|
| 出力時の最大レイヤー| 入力と同じ|最大 6 (後述の「システム プリセット」参照)|
| コントリビューション フィードの最大集約帯域幅|60 Mbps|該当なし|
| コントリビューション内の各レイヤーにおける最大ビットレート |20 Mbps|20 Mbps|
| 複数言語オーディオ トラックのサポート|はい|いいえ |
| サポートされている入力ビデオ コーデック |H.264/AVC と H.265/HEVC|H.264/AVC|
| サポートされている出力ビデオ コーデック|入力と同じ|H.264/AVC|
| サポートされているビデオ ビット深度、入力、出力|HDR 10/HLG を含む最大 10 ビット|8 ビット|
| サポートされている入力オーディオ コーデック|AAC-LC、HE-AAC v1、HE-AAC v2|AAC-LC、HE-AAC v1、HE-AAC v2|
| サポートされている出力オーディオ コーデック|入力と同じ|AAC-LC|
| 出力ビデオの最大ビデオ解像度|入力と同じ|720p (30 フレーム/秒)|
| 入力プロトコル|RTMP、フラグメント化された MP4 (スムーズ ストリーミング)|RTMP、フラグメント化された MP4 (スムーズ ストリーミング)|
| 料金|[価格に関するページ](https://azure.microsoft.com/pricing/details/media-services/) を参照し、[ライブ ビデオ] タブをクリックしてください。|[価格に関するページ](https://azure.microsoft.com/pricing/details/media-services/) を参照し、[ライブ ビデオ] タブをクリックしてください。|
| 最長実行時間| 24 時間 365 日、ライブ リニア | 最大 24 時間|
| キャプション データから埋め込まれた CEA 608/708 をパス スルーする機能|はい|はい|
| スレートの挿入のサポート|いいえ |いいえ |
| API による広告信号のサポート| いいえ |いいえ |
| インバンド SCTE-35 メッセージによる広告信号のサポート|はい|はい|
| 投稿フィードでの短時間の停止から回復する機能|はい|なし (6 秒経過しても入力データがない場合、ライブ イベントはスレートを開始します)|
| 均一でない入力 GOP のサポート|はい|いいえ。入力は固定 GOP 時間を持つ必要があります|
| 可変フレーム レートの入力のサポート|はい|なし。入力は固定フレーム レートにする必要があります。 たとえば、動きの大きなシーンでは、多少の変化は許容されます。 しかしコントリビューション フィードはフレームレートを (たとえば、15 フレーム/秒などに)ドロップすることはできません。|
| 入力フィードがなくなった場合のライブ イベントの自動停止|いいえ |12 時間後 (実行中の LiveOutput がない場合)|

## <a name="system-presets"></a>システム プリセット

ライブ エンコード (**Standard** に設定されたライブ イベント) を使用した場合、エンコード プリセットでは、受信ストリームを複数のビットレートまたはレイヤーにエンコードする方法が定義されます。 現在、唯一の許容されている値は *Default720p* (既定値) です。

**Default720p** では、次の 6 つのレイヤーにビデオがエンコードされます。

### <a name="output-video-stream"></a>出力ビデオ ストリーム

| ビットレート | 幅 | 高さ | 最大 FPS | プロファイル | 出力ストリーム名 |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |高 |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |高 |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |高 |Video_704x396_1350kbps |
| 850 |512 |288 |30 |高 |Video_512x288_850kbps |
| 550 |384 |216 |30 |高 |Video_384x216_550kbps |
| 200 |340 |192 |30 |高 |Video_340x192_200kbps |

> [!NOTE]
> カスタムのライブ エンコード プリセットを使用する必要がある場合は、amshelp@microsoft.com までお問い合わせください。 目的の解像度とビットレートを指定する必要があります。 720p のレイヤーが 1 つだけあり、最大で 6 つのレイヤーがあることを確認してください。

### <a name="output-audio-stream"></a>出力オーディオ ストリーム

オーディオは、ステレオの 128 kbps で AAC-LC に、サンプリング レート 48.1 kHz でエンコードされます。

## <a name="next-steps"></a>次の手順

[ライブ ストリーミングの概要](live-streaming-overview.md)
