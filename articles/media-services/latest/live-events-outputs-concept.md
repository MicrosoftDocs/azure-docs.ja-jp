---
title: Azure Media Services でのライブ ストリーミングの概念 - ライブ イベントとライブ出力 | Microsoft Docs
description: この記事では、Azure Media Services v3 でのライブ ストリーミングの概念の概要を説明します。
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
ms.date: 03/30/2019
ms.author: juliako
ms.openlocfilehash: 00dab8381c26a6331dd325eacd4a550892bd3411
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495175"
---
# <a name="live-events-and-live-outputs"></a>ライブ イベントとライブ出力

Azure Media Services では、Azure クラウドで顧客にライブ イベントを配信することができます。 Media Services v3 でライブ ストリーミング イベントを構成するには、この記事で説明されている概念を理解する必要があります。 <br/>ページの右側に、セクションの一覧が表示されます。

## <a name="live-events"></a>ライブ イベント

[ライブ イベント](https://docs.microsoft.com/rest/api/media/liveevents)は、ライブ ビデオ フィードの取り込みと処理を担当します。 ライブ イベントを作成するときに、リモートのエンコーダーからライブ信号を送信するために使用できる入力エンドポイントが作成されます。 リモート ライブ エンコーダーは、[RTMP](https://www.adobe.com/devnet/rtmp.html) または [スムーズ ストリーミング](https://msdn.microsoft.com/library/ff469518.aspx) (Fragmented MP4) プロトコルのいずれかを使用して、投稿フィードをその入力エンドポイントに送信します。 Smooth Streaming 取り込みプロトコルでサポートされる URL スキームは `http://` と `https://` です。 RTMP 取り込みプロトコルでサポートされる URL スキームは `rtmp://` と `rtmps://` です。 

## <a name="live-event-types"></a>ライブ イベントの種類

[ライブ イベント](https://docs.microsoft.com/rest/api/media/liveevents)には、パススルーとライブ エンコードの 2 種類があります。 

### <a name="pass-through"></a>パススルー

![パススルー](./media/live-streaming/pass-through.svg)

パススルー **ライブ イベント**を使用する場合は、オンプレミス ライブ エンコーダーを活用して、マルチ ビットレート ビデオ ストリームを生成し、(RTMP または Fragmented MP4 プロトコルを使用して) ライブ イベントへの投稿フィードとして送信します。 その後、ライブ イベントは追加の処理なしで受信ビデオ ストリームを通過します。 このようなパススルー LiveEvent は、長時間実行されるライブ ストリームや 24 時間 365 日のリニア ライブ エンコード向けに最適化されています。 この種類のライブ イベントを作成するときは、None (LiveEventEncodingType.None) を指定してください。

投稿フィードは最大 4K の解像度と 60 フレーム/秒のフレーム レートで、H.264/AVC または H.265/HEVC のいずれかのビデオ コーデックと AAC (AAC-LC、HE-AACv1、または HE-AACv2) オーディオ コーデックを使用して送信することができます。  詳細については、[ライブ イベントの種類の比較](live-event-types-comparison.md)についての記事を参照してください。

> [!NOTE]
> 長期にわたって複数のイベントを配信する場合で、かつオンプレミスのエンコーダーを既に導入済みである場合、ライブ ストリーミングの手段としてはパススルー方式が最も低コストです。 詳しくは、 [価格情報](https://azure.microsoft.com/pricing/details/media-services/) ページを参照してください。
> 

[MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126) の .NET のコード例を参照してください。

### <a name="live-encoding"></a>ライブ エンコード  

![ライブ エンコード](./media/live-streaming/live-encoding.svg)

Media Services によるライブ エンコードを使用する場合は、オンプレミス ライブ エンコーダーを、(RTMP または Fragmented MP4 プロトコルを使用して) ライブ イベントへの投稿フィードとしてシングル ビットレート ビデオを送信するように構成します。 ライブ イベントは、受信シングル ビットレート ストリームを[マルチ ビットレート ビデオ ストリーム](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)にエンコードし、そのストリームを、MPEG-DASH、HLS、Smooth Streaming などのプロトコルを介して再生デバイスに配信できるようにします。 この種類のライブ イベントを作成するときは、エンコードの種類に **Standard** (LiveEventEncodingType.Standard) を指定してください。

投稿フィードは最大 1080p の解像度と 30 フレーム/秒のフレーム レートで、H.264/AVC ビデオ コーデックと AAC (AAC-LC、HE-AACv1、または HE-AACv2) オーディオ コーデックを使用して送信することができます。 詳細については、[ライブ イベントの種類の比較](live-event-types-comparison.md)についての記事を参照してください。

ライブ エンコード (**Standard** に設定されたライブ イベント) を使用する際、エンコードのプリセットでは、受信ストリームを複数のビットレートまたはレイヤーにエンコードする方法を定義します。 詳細については、[システムのプリセット](live-event-types-comparison.md#system-presets)に関するページを参照してください。

> [!NOTE]
> 現在、Standard タイプのライブ イベントで使用できるプリセット値は、*Default720p* のみです。 カスタムのライブ エンコード プリセットを使用する必要がある場合は、amshelp@microsoft.com までお問い合わせください。 目的の解像度とビットレートを指定する必要があります。 720p のレイヤーが 1 つだけあり、最大で 6 つのレイヤーがあることを確認してください。

## <a name="live-event-creation-options"></a>ライブ イベントの作成のオプション

ライブ イベントの作成時には次のオプションを指定できます。

* ライブ イベントのストリーミング プロトコル (現時点では、RTMP プロトコルと Smooth Streaming プロトコルがサポートされます)<br/>ライブ イベントや、それに関連付けられたライブ出力の実行中は、プロトコル オプションを変更できません。 別のプロトコルが必要な場合は、ストリーミング プロトコルごとに別のライブ イベントを作成する必要があります。  
* 取り込みやプレビューの IP 制限。 このライブ イベントへのビデオの取り込みが許可される IP アドレスを定義できます。 許可される IP アドレスは、1 つの IP アドレス (例: '10.0.0.1')、IP アドレスと CIDR サブネット マスクを使用した IP 範囲 (例: '10.0.0.1/22')、IP アドレスとピリオドで区切られた 10 進数のサブネット マスクを使用した IP 範囲 (例: '10.0.0.1(255.255.252.0)') のいずれかの形で指定できます。<br/>IP アドレスが指定されておらず、規則の定義もない場合は、どの IP アドレスも許可されません。 すべての IP アドレスを許可するには、規則を作成し、0.0.0.0/0 に設定します。<br/>IP アドレスの形式は、4 つの数字を含む IpV4 アドレスまたは CIDR アドレス範囲のどちらかである必要があります。
* イベントの作成中に、そのイベントを自動開始するように設定できます。 <br/>自動開始が true に設定されている場合、ライブ イベントは作成された後に開始されます。 ライブ イベントの実行が開始されるとすぐに課金が開始されます。 それ以上の課金を停止するには、ライブ イベント リソースの Stop を明示的に呼び出す必要があります。 または、ストリーミングを開始する準備ができたら、イベントを開始できます。 

    詳細については、[ライブ イベントの状態と課金](live-event-states-billing.md)に関するページを参照してください。

## <a name="live-event-ingest-urls"></a>ライブ イベントの取り込み URL

ライブ イベントが作成されると、ライブ オンプレミス エンコーダーに提供する取り込み URL を取得できます。 ライブ エンコーダーは、これらの URL を使用して、ライブ ストリームを入力します。 詳細については、「[おすすめのライブ ストリーミング エンコーダー](recommended-on-premises-live-encoders.md)」を参照してください。 

非バニティ URL またはバニティ URL を使用できます。 

* 非バニティ URL

    非バニティ URL は AMS v3 の既定モードです。 ライブ イベントをすばやく取得できる可能性がありますが、取り込み URL がわかるのはライブ イベントの開始時のみです。 ライブ イベントを停止または開始すると、URL は変わります。 <br/>非バニティは、エンド ユーザーが可能な限り早くライブ イベントを取得するアプリを使用してストリームしたいシナリオで、動的取り込み URL を使用しても問題にならない場合に役立ちます。
* バニティ URL

    バニティ モードは、ハードウェア ブロードキャスト エンコーダーを使用していて、ライブ イベントの開始時にエンコーダーを再構成したくない大規模なメディア放送局に好まれています。 時間の経過と共に変化しない、予測的な取り込み URL が望まれています。

> [!NOTE] 
> 取り込み URL を予測的にするには、(URL 内のランダムなトークンを避けるため) "バニティ" モードを使用して独自のアクセス トークンを渡す必要があります。

### <a name="live-ingest-url-naming-rules"></a>ライブ取り込み URL の名前付け規則

以下の "*ランダム*" な文字列は 128 ビットの 16 進数です (32 文字の 0-9 a-f で構成されています)。<br/>
以下の "*アクセス トークン*" は、固定 URL のために指定する必要があるものです。 これも 128 ビットの 16 進数です。

#### <a name="non-vanity-url"></a>非バニティ URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/<access token>`
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>スムーズ ストリーミング

`http://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`
`https://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`

#### <a name="vanity-url"></a>バニティ URL

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/<access token>`
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>スムーズ ストリーミング

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`

## <a name="live-event-preview-url"></a>ライブ イベント プレビュー URL

**ライブ イベント**が投稿フィードの受信を開始したら、ライブ ストリームを公開する前に、そのプレビュー エンドポイントを使用して、ライブ ストリームが受信されていることをプレビューして検証できます。 プレビュー ストリームが良好であることを確認したら、その LiveEvent を使用して、ライブ ストリームを 1 つ以上の (事前に作成した) **ストリーミング エンドポイント**を通して配信できるようになります。 これを実現するために、**ライブ イベント**に新しい[ライブ出力](https://docs.microsoft.com/rest/api/media/liveoutputs)を作成します。 

> [!IMPORTANT]
> 続行する前に、ビデオがプレビュー URL に送信されていることを確認してください。

## <a name="live-event-long-running-operations"></a>ライブイベントの長時間にわたって実行される操作

詳細については、「[Long-running operations (長時間にわたって実行される操作)](media-services-apis-overview.md#long-running-operations)」を参照してください。

## <a name="live-outputs"></a>ライブ出力

ストリームがライブ イベントに流れ始めると、[資産](https://docs.microsoft.com/rest/api/media/assets)、[ライブ出力](https://docs.microsoft.com/rest/api/media/liveoutputs)、[ストリーミング ロケーター](https://docs.microsoft.com/rest/api/media/streaminglocators)を作成することにより、ストリーミング イベントを開始できます。 ライブ出力により、ストリームがアーカイブされ、[ストリーミング エンドポイント](https://docs.microsoft.com/rest/api/media/streamingendpoints)を介して視聴者がストリームを使用できるようになります。  

> [!NOTE]
> ライブ出力は作成すると開始され、削除されると停止します。 ライブ出力を削除しても、基になる資産と資産のコンテンツは削除されません。 

**ライブ イベント**とその**ライブ出力**との関係は、従来のテレビの放送と似ており、チャンネル (**ライブ イベント**) はビデオの一定のストリームを表し、録画 (**ライブ出力**) は特定の時間セグメント (たとえば、午後 6 時 30 分から午後 7 時 00 分までの夕方のニュース) を対象としています。 テレビはデジタル ビデオ レコーダー (DVR) を使用して録画することができます。それと同様のライブ イベントの機能は **ArchiveWindowLength** プロパティを介して管理されます。 これは、ISO-8601 形式で表した期間 (PTHH:MM:SS など) であり、DVR の容量を指定し、最短で 3 分から最長で 25 時間まで設定できます。

**ライブ出力**オブジェクトは、ライブ ストリームをキャッチして Media Services アカウントの資産に記録するテープ レコーダーのようなものです。 記録されたコンテンツは、アカウントに接続されている Azure Storage アカウントに保持され、資産リソースによって定義されたコンテナーに保持されます。 また、**ライブ出力**を使用すると、アーカイブの記録に残すストリームの量 (たとえば、クラウド DVR の容量) や視聴者がライブ ストリームの視聴を開始することができるかどうかなど、送信ライブ ストリームのいくつかのプロパティを制御することもできます。 ディスク上のアーカイブは、**ライブ出力**の **archiveWindowLength** プロパティで指定されているコンテンツ量のみを保持する循環アーカイブ "ウィンドウ" です。 このウィンドウの範囲外のコンテンツは、ストレージ コンテナーから自動的に破棄され、復旧できません。 1 つの**ライブ イベント**に、アーカイブの長さと設定の異なる複数の**ライブ出力** (最大 3 つ) を作成できます。  

**ストリーミング ロケーター**を使用して**ライブ出力**の**資産**を発行した場合、**ライブ イベント** (DVR ウィンドウの長さまで) は、ストリーミング ロケーターの有効期限まで、または削除するまで、どちらか早い方のタイミングまで引き続き表示できます。

詳細については、「[クラウド DVR の使用](live-event-cloud-dvr.md)」を参照してください。

## <a name="next-steps"></a>次の手順

[ライブ ストリーミングのチュートリアル](stream-live-tutorial-with-api.md)
