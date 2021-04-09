---
title: Azure Media Services を使用したライブ ストリーミングの概要 | Microsoft Docs
description: この記事では、Microsoft Azure Media Services を使用したライブ ストリーミングの概要を説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 195333f5f06145a1dfa84f0de2c5984190e3d7ca
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014827"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Media Services を使用したライブ ストリーミングの概要

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

## <a name="overview"></a>概要

Azure Media Services を使用してライブ ストリーミング イベントを配信する場合、通常、次のコンポーネントが必要になります。

* イベントのブロードキャストに使用されるカメラ。
* カメラからの信号をライブ ストリーミング サービスに送信されるストリームに変換するライブ ビデオ エンコーダー。

    (省略可) ライブ ストリーミングのタイミングを同期させた複数のエンコーダー。 非常に高い可用性と高品質が要求される重要なライブ イベントでは、タイミングを同期させたアクティブ/アクティブの冗長エンコーダーを使用して、データを失わないシームレスなフェールオーバーを実現することをお勧めします。
* 次の操作を実行できるライブ ストリーミング サービス。

  * さまざまなライブ ストリーミング プロトコル (RTMP、スムーズ ストリーミングなど) を使用してライブ コンテンツを取り込む。
  * (省略可) ストリームをアダプティブ ビットレート ストリームにエンコードする。
  * ライブ ストリームをプレビューする。
  * 後でストリーミングするために、取り込んだコンテンツを記録して保存する (ビデオ オン デマンド)。
  * コンテンツを一般的なストリーミング プロトコル (MPEG DASH、Smooth、HLS など) を使用して顧客に配信したり、再配布のための Content Delivery Network (CDN) に直接配信する。

**Microsoft Azure Media Services** (AMS) は、ライブ ストリーミング コンテンツの取り込み、エンコード、プレビュー、保存、配信を行う機能を提供します。

Media Services では、[ダイナミック パッケージ](media-services-dynamic-packaging-overview.md)を利用して、サービスに送信される投稿フィードからのライブ ストリームを MPEG DASH、HLS、および Smooth Streaming 形式でブロードキャストできます。 視聴者は、HLS、DASH、またはスムーズ ストリーミングと互換性のある任意のプレーヤーを使用して、ライブ ストリームを再生できます。 これらいずれかのプロトコルでストリームを配信するために、Web またはモバイル アプリケーションの Azure Media Player を使用することができます。

> [!NOTE]
> 2018 年 5 月 12日以降は、ライブ チャネルで RTP/MPEG-2 トランスポート ストリーム取り込みプロトコルがサポートされなくなります。 RTP/MPEG-2 から RTMP またはフラグメント化 MP4 (Smooth Streaming) 取り込みプロトコルに移行してください。

## <a name="streaming-endpoints-channels-programs"></a>ストリーミング エンドポイント、チャネル、プログラム

Azure Media Services では、**チャネル**、**プログラム**、**ストリーミング エンドポイント** で、取り込み、書式設定、DVR、セキュリティ、スケーラビリティ、冗長性などのすべてのライブ ストリーミングの機能を処理します。

**チャネル** は、ライブ ストリーミング コンテンツを処理するためのパイプラインを表します。 チャネルは次の方法でライブ入力ストリームを受信できます。

* オンプレミスのライブ エンコーダーは、マルチビットレート **RTMP** または **スムーズ ストリーミング** (Fragmented MP4) を、**パススルー** 配信用に設定されたチャネルに送信します。 **パススルー** 配信とは、取り込んだストリームが、追加の処理なしで **チャネル** を通過することをいいます。 マルチビットレートのスムーズ ストリーミングが出力される次のライブ エンコーダーを使用できます:MediaExcel、Ateme、Imagine Communications、Envivio、Cisco、Elemental。 次のライブ エンコーダーでは RTMP が出力されます:Telestream Wirecast、Haivision、Teradek トランスコーダー。  ライブ エンコーダーは、ライブ エンコードが有効になっていないチャネルにシングル ビットレート ストリームも送信できますが、これはお勧めしません。 Media Services は、要求に応じて、ストリームを顧客に配信します。

  > [!NOTE]
  > 長期にわたって複数のイベントを配信する場合で、かつオンプレミスのエンコーダーを既に導入済みである場合、ライブ ストリーミングの手段としてはパススルー方式が最も低コストです。 詳しくは、 [価格情報](https://azure.microsoft.com/pricing/details/media-services/) ページを参照してください。
  > 
  > 
* オンプレミスのライブ エンコーダーでは、次のいずれかの形式で、Media Services によるライブ エンコードが有効なチャネルに、シングル ビットレート ストリームが送信されます:RTMP またはスムーズ ストリーミング (フラグメント化 MP4)。 RTMP 出力を伴う次のライブ エンコーダーは、この種類のチャンネルで機能することがわかっています。Telestream Wirecast。 次に、受信したシングル ビットレート ストリームのマルチ ビットレート (アダプティブ) ビデオ ストリームへのライブ エンコードがチャネルで実行されます。 Media Services は、要求に応じて、ストリームを顧客に配信します。

Media Services 2.10 リリース以降、チャネルを作成するときに、チャネルで入力ストリームを受信する方法、およびチャネルでストリームのライブ エンコードを実行するかどうかを指定できます。 2 つのオプションがあります。

* **なし** (パススルー) – オンプレミスのライブ エンコーダーを使用してマルチビットレート ストリーム (パススルー ストリーム) を出力する場合には、この値を指定します。 この場合は、受信ストリームはエンコードなしで出力に渡されます。 これは、2.10 リリースより前のチャネル動作です。  
* **Standard** – Media Services を使用して、シングル ビットレートのライブ ストリームをマルチ ビットレート ストリームにエンコードする場合に、この値を選択します。 この方式は、頻度の低いイベントですばやくスケールアップする場合に、より経済的です。 ライブ エンコードは課金に影響することと、ライブ エンコード チャネルを "実行中" 状態のままにしておくと請求料金が課されることに注意してください。  余分な時間料金を課されないようにするために、ライブ ストリーミング イベントが完了したら、チャネルの実行をすぐに停止することをお勧めします。

## <a name="comparison-of-channel-types"></a>チャネルの種類の比較

次の表では、Media Services でサポートされている 2 種類のチャネルを比較し、説明しています。

| 機能 | パススルー チャネル | 標準チャネル |
| --- | --- | --- |
| シングル ビットレートの入力がクラウド内でマルチビットレートにエンコードされる |いいえ |はい |
| 最大解像度、層の数 |1080p、8 層、60 fps 以上 |720p、6 層、30 fps |
| 入力プロトコル |RTMP、スムーズ ストリーミング |RTMP、スムーズ ストリーミング |
| Price |[価格に関するページ](https://azure.microsoft.com/pricing/details/media-services/) を参照し、[ライブ ビデオ] タブをクリックしてください。 |[価格に関するページ](https://azure.microsoft.com/pricing/details/media-services/) |
| 最長実行時間 |24 時間 365 日 |8 時間 |
| スレートの挿入のサポート |いいえ |はい |
| 広告信号のサポート |いいえ |はい |
| パススルー CEA 608/708 キャプション |はい |はい |
| 均一でない入力 GOP のサポート |はい |いいえ – 入力は固定の 2 秒の GOP である必要があります |
| 可変フレーム レートの入力のサポート |はい |なし。入力は固定フレーム レートにする必要があります。<br/>たとえば、動きの大きなシーンでは、多少の変化は許容されます。 しかし、エンコーダーは 10 フレーム/秒に下げることはできません。 |
| 入力フィードがなくなった場合のチャネルの自動停止 |いいえ |12 時間後 (実行中のプログラムがない場合) |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>オンプレミスのエンコーダーからマルチビットレートのライブ ストリームを受信するチャネルを操作する (パススルー)

次の図は、 **パススルー** ワークフローに関連する AMS プラットフォームの主要な部分を示しています。

!["パススルー" ワークフローのための AMS プラットフォームの主要な部分を示す図。](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

詳細については、 [オンプレミスのエンコーダーからマルチビットレートのライブ ストリームを受信するチャネルの操作](media-services-live-streaming-with-onprem-encoders.md)に関するページを参照してください。

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Azure Media Services を使用してライブ エンコードの実行が有効なチャネルを操作する

次の図は、Media Services による Live Encoding の実行が有効なチャネルのライブ ストリーミング ワークフローに関連する AMS プラットフォームの主要な部分を示しています。

![ライブ ワークフロー](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

詳細については、「 [Azure Media Services を使用してライブ エンコードの実行が有効なチャネルを操作する](media-services-manage-live-encoder-enabled-channels.md)」をご覧ください。

## <a name="description-of-a-channel-and-its-related-components"></a>チャネルとその関連コンポーネントの説明

### <a name="channel"></a>チャネル

Media Services においてライブ ストリーミング コンテンツの処理を担うのが [チャネル](/rest/api/media/operations/channel)です。 チャネルは入力エンドポイントであり、その取り込み URL をライブ トランスコーダーに対して指定します。 チャネルは、ライブ トランスコーダーからライブ入力ストリームを受け取り、1 つまたは複数の StreamingEndpoint を介してストリーミングできる状態にします。 また、ストリームはあらかじめプレビューし、確認したうえで処理、配信しますが、チャネルはその際に使用するプレビュー エンドポイント (プレビュー URL) も提供します

チャネル作成時に取り込み URL とプレビュー URL を取得できます。 これらの URL を取得するには、チャネルが開始済み状態である必要はありません。 ライブ トランスコーダーからチャネルへのデータのプッシュを開始する準備ができたら、チャネルを開始する必要があります。 ライブ トランスコーダーがデータの取り込みを開始した後、ストリームをプレビューできます。

各 Media Services アカウントには、複数のチャネル、複数のプログラム、複数 StreamingEndpoints を含めることができます。 帯域幅とセキュリティのニーズに応じて、StreamingEndpoint サービスを 1 つまたは複数のチャネル専用にすることができます。 StreamingEndpoint は、どのチャネルからでもプルできます。

チャンネルを作成する際は、許可された IP アドレスを次の形式のいずれかで指定できます: 4 つの数字を含む IpV4 アドレス、CIDR アドレス範囲。

### <a name="program"></a>プログラム
ライブ ストリームでのセグメントの公開と保存は、[プログラム](/rest/api/media/operations/program)を使用して制御します。 プログラムはチャネルによって管理されます。 チャネルとプログラムの関係は、従来のメディアとよく似ています。チャネルが絶えずコンテンツのストリームを配信するのに対し、プログラムは、そのチャネル上で決まった時間に生じるイベントです。
録画されたコンテンツの保持時間は、プログラムの **ArchiveWindowLength** プロパティで設定できます。 この値は、最小 5 分から最大 25 時間までの範囲で設定できます。

クライアントが現在のライブ位置からさかのぼってシークできる最長時間も ArchiveWindowLength によって決まります。 プログラムの放送は、指定された期間継続しますが、ArchiveWindowLength を過ぎたコンテンツは絶えず破棄されていきます。 さらに、このプロパティの値によって、クライアント マニフェストが肥大した場合の最大サイズも決まります。

各プログラムは資産に関連付けられています。 プログラムを公開するには、関連付けられた資産のロケーターを作成する必要があります。 このロケーターを作成すると、ストリーミング URL を構築してクライアントに提供できます。

チャネルは、最大 3 つの同時実行プログラムをサポートするため、同じ受信ストリームのアーカイブを複数作成できます。 これにより、1 つのイベントのさまざまな部分を必要に応じて発行したりアーカイブしたりできます。 たとえば、ビジネス要件によって 1 つのプログラムの 6 時間分をアーカイブする一方、最後の 10 分間のみをブロードキャストする場合があります。 これを実現するには、2 つの同時実行プログラムを作成する必要があります。 1 つのプログラムは 6 時間分のイベントをアーカイブするように設定しますが、プログラムは発行されません。 もう 1 つのプログラムは 10 分間のアーカイブを行うように設定します。このプログラムは発行されます。

## <a name="billing-implications"></a>課金への影響
チャネルでは、API を通じて状態が "実行中" に遷移するとすぐに、課金が開始されます。  

次の表は、API と Azure Portal でチャネルの状態がどのように課金の状態に対応しているかを示しています。 API とポータルのユーザー エクスペリエンスとでは状態が少し異なることに注意してください。 チャネルが API を通じて "実行中" 状態になるか、Azure ポータルで "準備完了" または "ストリーミング" 状態になるとすぐに、課金がアクティブになります。

チャネルへの課金を停止するには、API を通じて、または Azure Portal で、チャネルを停止する必要があります。
チャネルが終了したら、自分でチャネルを停止する必要があります。 チャネルの停止に失敗すると、課金が継続されます。

> [!NOTE]
> Standard チャネルを使用している場合、入力フィードがなくなり、実行中のプログラムがなくなってから 12 時間後に、まだ "実行中" 状態のチャネルは AMS により自動的に停止されます。 ただし、チャネルが "実行中" 状態だった期間については課金されます。
>
>

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>チャネルの状態と、どのように課金モードにマッピングされているか
現在のチャネルの状態。 指定できる値は、次のとおりです。

* **停止済み**。 これは、チャネル作成後の初期状態です (ポータルで自動開始が選択されなかった場合)。この状態では、課金は行われません。 この状態で、チャネルのプロパティを更新できますが、ストリーミングは許可されていません。
* **開始中**。 チャネルを開始しています。 この状態では、課金は行われません。 この状態の場合、更新やストリーミングはできません。 エラーが発生した場合は、チャネルは Stopped 状態に戻ります。
* **実行中**。 チャネルは、ライブ ストリームを処理できます。 課金が始まります。 課金されないようにするには、チャネルを停止する必要があります。
* **停止中**。 チャネルを停止しています。 この遷移状態では、課金は行われません。 この状態の場合、更新やストリーミングはできません。
* **削除中**。 チャネルが削除されました。 この遷移状態では、課金は行われません。 この状態の場合、更新やストリーミングはできません。

次の表は、チャネルの状態と課金モードとの対応を示しています。

| チャネルの状態 | ポータル UI インジケーター | 課金対象 |
| --- | --- | --- |
| 開始中 |開始中 |いいえ (遷移状態) |
| 実行中 |準備完了 (実行中プログラムなし)<br/>or<br/>ストリーミング (実行中プログラムが最低 1 つ存在) |YES |
| 停止中 |停止中 |いいえ (遷移状態) |
| 停止済み |停止済み |いいえ |

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>関連トピック
[Azure Media Services の Fragmented MP4 ライブ インジェスト仕様](../media-services-fmp4-live-ingest-overview.md)

[Azure Media Services を使用して Live Encoding の実行が有効なチャネルを操作する](media-services-manage-live-encoder-enabled-channels.md)

[オンプレミスのエンコーダーからマルチ ビットレートのライブ ストリームを受信するチャネルを操作する](media-services-live-streaming-with-onprem-encoders.md)

[クォータと制限](media-services-quotas-and-limitations.md)。  

[Media Services の概念](media-services-concepts.md)
