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
ms.date: 06/19/2019
ms.author: juliako
ms.openlocfilehash: a951ebd46335ad4639b8499283ddd30f13edd64e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605647"
---
# <a name="live-events-and-live-outputs"></a>ライブ イベントとライブ出力

Azure Media Services では、Azure クラウドで顧客にライブ イベントを配信することができます。 Media Services v3 でライブ ストリーミング イベントを構成するには、この記事で説明されている概念を理解する必要があります。

> [!TIP]
> Media Services v2 API から移行するお客様の場合、**ライブ イベント** エンティティは v2 にある **Channel** に置き換わり、**ライブ出力**は **Program** に置き換わります。

## <a name="live-events"></a>ライブ イベント

[ライブ イベント](https://docs.microsoft.com/rest/api/media/liveevents)は、ライブ ビデオ フィードの取り込みと処理を担当します。 ライブ イベントを作成すると、リモート エンコーダーからライブ シグナルを送信するために使用できるプライマリおよびセカンダリ入力エンドポイントが作成されます。 リモート ライブ エンコーダーは、[RTMP](https://www.adobe.com/devnet/rtmp.html) または [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (Fragmented MP4) 入力プロトコルを使用して、その入力エンドポイントに投稿フィードを送信します。 RTMP 取り込みプロトコルの場合は、コンテンツを平文で (`rtmp://`)、またはワイヤ上で安全に暗号化して (`rtmps://`) 送信できます。 Smooth Streaming 取り込みプロトコルでサポートされる URL スキームは `http://` と `https://` です。  

## <a name="live-event-types"></a>ライブ イベントの種類

[ライブ イベント](https://docs.microsoft.com/rest/api/media/liveevents)には、パススルーとライブ エンコードの 2 種類があります。 これらの種類は、[LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype) を使用して作成中に設定されます。

* **LiveEventEncodingType.None** - オンプレミス ライブ エンコーダーは、マルチ ビットレート ストリームを送信します。 取り込まれたストリームは、それ以上処理されずにライブ イベントを通過します。 
* **LiveEventEncodingType.Standard** - オンプレミス ライブ エンコーダーは、ライブ イベントにシングル ビットレート ストリームを送信し、Media Services がマルチ ビットレート ストリームを作成します。 投稿フィードの解像度が 720p 以上である場合、**Default720p** プリセットは 6 つの解像度とビットレートのペアのセットをエンコードします。
* **LiveEventEncodingType.Premium1080p** - オンプレミス ライブ エンコーダーは、ライブ イベントにシングル ビットレート ストリームを送信し、Media Services がマルチ ビットレート ストリームを作成します。 Default1080p プリセットは、解像度とビットレートのペアの出力セットを指定します。 

### <a name="pass-through"></a>パススルー

![パススルー](./media/live-streaming/pass-through.svg)

パススルー **ライブ イベント**を使用する場合は、オンプレミス ライブ エンコーダーを活用して、マルチ ビットレート ビデオ ストリームを生成し、(RTMP または Fragmented MP4 プロトコルを使用して) ライブ イベントへの投稿フィードとして送信します。 その後、ライブ イベントは追加の処理なしで受信ビデオ ストリームを通過します。 このようなパススルー ライブ イベントは、長時間実行されるライブ ストリームや 24 時間 365 日のリニア ライブ エンコード向けに最適化されています。 この種類のライブ イベントを作成するときは、None (LiveEventEncodingType.None) を指定してください。

投稿フィードは最大 4K の解像度と 60 フレーム/秒のフレーム レートで、H.264/AVC または H.265/HEVC のいずれかのビデオ コーデックと AAC (AAC-LC、HE-AACv1、または HE-AACv2) オーディオ コーデックを使用して送信することができます。  詳細については、[ライブ イベントの種類の比較](live-event-types-comparison.md)についての記事を参照してください。

> [!NOTE]
> 長期にわたって複数のイベントを配信する場合で、かつオンプレミスのエンコーダーを既に導入済みである場合、ライブ ストリーミングの手段としてはパススルー方式が最も低コストです。 詳しくは、 [価格情報](https://azure.microsoft.com/pricing/details/media-services/) ページを参照してください。
> 

[MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126) の .NET のコード例を参照してください。

### <a name="live-encoding"></a>ライブ エンコード  

![ライブ エンコード](./media/live-streaming/live-encoding.svg)

Media Services によるライブ エンコードを使用する場合は、オンプレミス ライブ エンコーダーを、(RTMP または Fragmented MP4 プロトコルを使用して) ライブ イベントへの投稿フィードとしてシングル ビットレート ビデオを送信するように構成します。 次に、その受信シングル ビットレート ストリームを[マルチ ビットレート ビデオ ストリーム](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)にエンコードするようにライブ イベントを設定し、その出力を MPEG-DASH、HLS、Smooth Streaming などのプロトコル経由で再生デバイスに配信できるようにします。

ライブ エンコードを使用すると、H.264/AVC ビデオ コーデックと AAC (AAC-LC、HE-AACv1、または HE-AACv2) オーディオ コーデックを使用して、30 フレーム/秒のフレーム レートで最大 1080p の解像度のみで投稿フィードを送信できます。 パススルー ライブ イベントは、60 フレーム/秒で最大 4K の解像度をサポートできることに注意してください。 詳細については、[ライブ イベントの種類の比較](live-event-types-comparison.md)についての記事を参照してください。

ライブ エンコーダーからの出力に含まれている解像度とビットレートは、プリセットによって決定されます。 **Standard** ライブ エンコーダー (LiveEventEncodingType.Standard) を使用している場合、*Default720p* プリセットは、3.5Mbps での 720p から 200 kbps での 192p までの 6 つの解像度とビットレートのペアのセットを指定します。 それ以外で **Premium1080p** ライブ エンコーダー (LiveEventEncodingType.Premium1080p) を使用している場合、*Default1080p* プリセットは、3.5Mbps での 1080p から 200 kbps での 180p までの 6 つの解像度とビットレートのペアのセットを指定します。 詳細については、[システムのプリセット](live-event-types-comparison.md#system-presets)に関するページを参照してください。

> [!NOTE]
> ライブ エンコード プリセットをカスタマイズする必要がある場合は、Azure portal 経由でサポート チケットを開いてください。 目的の解像度とビットレートを指定する必要があります。 720p (Standard ライブ エンコーダーのプリセットを要求する場合) または 1080p (Premium1080p ライブ エンコーダーのプリセットを要求する場合) に 1 つのレイヤーと、多くとも 6 つのレイヤーしか存在しないことを確認してください。

## <a name="live-event-creation-options"></a>ライブ イベントの作成のオプション

ライブ イベントの作成時には次のオプションを指定できます。

* ライブ イベントのストリーミング プロトコル (現時点では、RTMP プロトコルと Smooth Streaming プロトコルがサポートされます)<br/>ライブ イベントや、それに関連付けられたライブ出力の実行中は、プロトコル オプションを変更できません。 別のプロトコルが必要な場合は、ストリーミング プロトコルごとに別のライブ イベントを作成する必要があります。  
* 取り込みやプレビューの IP 制限。 このライブ イベントへのビデオの取り込みが許可される IP アドレスを定義できます。 許可される IP アドレスは、1 つの IP アドレス (例: '10.0.0.1')、IP アドレスと CIDR サブネット マスクを使用した IP 範囲 (例: '10.0.0.1/22')、IP アドレスとピリオドで区切られた 10 進数のサブネット マスクを使用した IP 範囲 (例: '10.0.0.1(255.255.252.0)') のいずれかの形で指定できます。<br/>IP アドレスが指定されておらず、規則の定義もない場合は、どの IP アドレスも許可されません。 すべての IP アドレスを許可するには、規則を作成し、0.0.0.0/0 に設定します。<br/>IP アドレスの形式は、4 つの数字を含む IpV4 アドレスまたは CIDR アドレス範囲のどちらかである必要があります。
* イベントの作成中に、そのイベントを自動開始するように設定できます。 <br/>自動開始が true に設定されている場合、ライブ イベントは作成された後に開始されます。 ライブ イベントの実行が開始されるとすぐに課金が開始されます。 それ以上の課金を停止するには、ライブ イベント リソースの Stop を明示的に呼び出す必要があります。 または、ストリーミングを開始する準備ができたら、イベントを開始できます。 

    詳細については、[ライブ イベントの状態と課金](live-event-states-billing.md)に関するページを参照してください。

## <a name="live-event-ingest-urls"></a>ライブ イベントの取り込み URL

ライブ イベントが作成されると、ライブ オンプレミス エンコーダーに提供する取り込み URL を取得できます。 ライブ エンコーダーは、これらの URL を使用して、ライブ ストリームを入力します。 詳細については、「[おすすめのライブ ストリーミング エンコーダー](recommended-on-premises-live-encoders.md)」を参照してください。 

非バニティ URL またはバニティ URL を使用できます。 

> [!NOTE] 
> 取り込み URL を予測的にするには、"バニティ" モードを設定します。

* 非バニティ URL

    非バニティ URL は、Media Services v3 での既定のモードです。 ライブ イベントをすばやく取得できる可能性がありますが、取り込み URL がわかるのはライブ イベントの開始時のみです。 ライブ イベントを停止または開始すると、URL は変わります。 <br/>非バニティは、エンド ユーザーが可能な限り早くライブ イベントを取得するアプリを使用してストリームしたいシナリオで、動的取り込み URL を使用しても問題にならない場合に役立ちます。
    
    ライブ イベントが作成される前にクライアント アプリケーションが取り込み URL を事前に生成する必要がない場合は、ライブ イベント用のアクセス トークンが Media Services で自動生成されるようにします。
* バニティ URL

    バニティ モードは、ハードウェア ブロードキャスト エンコーダーを使用していて、ライブ イベントの開始時にエンコーダーを再構成したくない大規模なメディア放送局に好まれています。 時間の経過と共に変化しない、予測的な取り込み URL が望まれています。
    
    このモードを指定するには、作成時に `vanityUrl` を `true` に設定します (既定値は `false`)。 また、作成時に、独自のアクセス トークン (`LiveEventInput.accessToken`) を渡す必要があります。 トークンの値を指定して、URL 内のランダムなトークンを回避します。 このアクセス トークンは、有効な GUID 文字列 (ハイフン付きまたはなし) である必要があります。 モードはいったん設定されると、更新できません。

    アクセス トークンは、データ センター内で一意である必要があります。 アプリケーションでバニティ URL を使用する必要がある場合は、(既存の GUID を再利用する代わりに) 常にアクセス トークン用の新しい GUID インスタンスを作成することをお勧めします。 

    バニティ URL を有効にしたり、アクセス トークンを有効な GUID (たとえば `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`) に設定したりするには、次の API を使用します。  
    
    |言語|バニティ URL を有効にする|アクセス トークンを設定する|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--vanity-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>ライブ取り込み URL の名前付け規則

* 以下の "*ランダム*" な文字列は 128 ビットの 16 進数です (32 文字の 0-9 a-f で構成されています)。
* *アクセス トークン* - バニティ モードを使用するときに設定する有効な GUID 文字列。 たとえば、「 `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` 」のように入力します。
* *ストリーム名* - 特定の接続のためのストリーム名を示します。 ストリーム名の値は通常、使用するライブ エンコーダーによって追加されます。 接続を説明するために任意の名前 ("video1_audio1"、"video2_audio1"、"stream" など) を使用するようにライブ エンコーダーを構成できます。

#### <a name="non-vanity-url"></a>非バニティ URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>スムーズ ストリーミング

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>バニティ URL

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>スムーズ ストリーミング

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>ライブ イベント プレビュー URL

ライブ イベントが投稿フィードの受信を開始したら、ライブ ストリームを公開する前に、そのプレビュー エンドポイントを使用して、ライブ ストリームが受信されていることをプレビューして検証できます。 プレビュー ストリームが良好であることを確認したら、ライブ イベントを使用して、そのライブ ストリームを 1 つ以上の (事前に作成された) ストリーミング エンドポイント経由で配信可能にすることができます。 これを実現するために、ライブ イベントに新しい[ライブ出力](https://docs.microsoft.com/rest/api/media/liveoutputs)を作成します。 

> [!IMPORTANT]
> 続行する前に、ビデオがプレビュー URL に送信されていることを確認してください。

## <a name="live-event-long-running-operations"></a>ライブイベントの長時間にわたって実行される操作

詳細については、「[Long-running operations (長時間にわたって実行される操作)](media-services-apis-overview.md#long-running-operations)」を参照してください。

## <a name="live-outputs"></a>ライブ出力

ストリームがライブ イベントに流れ始めると、[資産](https://docs.microsoft.com/rest/api/media/assets)、[ライブ出力](https://docs.microsoft.com/rest/api/media/liveoutputs)、[ストリーミング ロケーター](https://docs.microsoft.com/rest/api/media/streaminglocators)を作成することにより、ストリーミング イベントを開始できます。 ライブ出力により、ストリームがアーカイブされ、[ストリーミング エンドポイント](https://docs.microsoft.com/rest/api/media/streamingendpoints)を介して視聴者がストリームを使用できるようになります。  

> [!NOTE]
> ライブ出力は作成すると開始され、削除されると停止します。 ライブ出力を削除しても、基になる資産と資産のコンテンツは削除されません。 

**ライブ イベント**とその**ライブ出力**との関係は、従来のテレビの放送と似ており、チャンネル (ライブ イベント) はビデオの連続したストリームを表し、録画 (ライブ出力) は特定の時間セグメント (たとえば、午後 6 時 30 分から午後 7 時 00 分までの夕方のニュース) を対象としています。 テレビはデジタル ビデオ レコーダー (DVR) を使用して録画することができます。それと同様のライブ イベントの機能は **archiveWindowLength** プロパティを介して管理されます。 これは、ISO-8601 形式で表した期間 (PTHH:MM:SS など) であり、DVR の容量を指定し、最短で 3 分から最長で 25 時間まで設定できます。

ライブ出力オブジェクトは、ライブ ストリームをキャッチして Media Services アカウントの資産に記録するテープ レコーダーのようなものです。 記録されたコンテンツは、アカウントに接続されている Azure Storage アカウントに保持され、資産リソースによって定義されたコンテナーに保持されます。 また、ライブ出力を使用すると、アーカイブの記録に残すストリームの量 (たとえば、クラウド DVR の容量) や視聴者がライブ ストリームの視聴を開始することができるかどうかなど、送信ライブ ストリームのいくつかのプロパティを制御することもできます。 ディスク上のアーカイブは、ライブ出力の archiveWindowLength プロパティで指定されているコンテンツ量のみを保持する循環アーカイブ "ウィンドウ" です。 このウィンドウの範囲外のコンテンツは、ストレージ コンテナーから自動的に破棄され、復旧できません。 1 つのライブ イベントに、アーカイブの長さと設定の異なる複数のライブ出力 (最大 3 つ) を作成できます。  

**ストリーミング ロケーター**を使用してライブ出力の**資産**を発行した場合、ライブ イベント (DVR ウィンドウの長さまで) は、ストリーミング ロケーターの有効期限まで、または削除するまで、どちらか早い方のタイミングまで引き続き表示できます。

詳細については、「[クラウド DVR の使用](live-event-cloud-dvr.md)」を参照してください。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの提供、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次の手順

[ライブ ストリーミングのチュートリアル](stream-live-tutorial-with-api.md)
