---
title: Azure Media Services LiveEvent とクラウド DVR | Microsoft Docs
description: この記事では、LiveOutput の概要とクラウド DVR の使用方法について説明します。
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
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 5de7496d73ebe1c89ce27ef27df73b197f34e7c7
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2018
ms.locfileid: "52636987"
---
# <a name="using-a-cloud-dvr"></a>クラウド DVR の使用

[LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) では、記録するストリームの量 (たとえば、クラウド DVR の容量) や視聴者がライブ ストリームの視聴を開始することができるかどうかなど、送信ライブ ストリームのプロパティを制御することができます。 **LiveEvent** とその **LiveOutput** との関係は、従来のテレビの放送と似ており、チャンネル (**LiveEvent**) はビデオの一定のストリームを表し、録画 (**LiveOutput**) は特定の時間セグメント (たとえば、午後 6 時 30 分から午後 7 時 00 分までの夕方のニュース) を対象としています。 テレビはデジタル ビデオ レコーダー (DVR) を使用して録画することができます。それと同様の LiveEvent の機能は ArchiveWindowLength プロパティを介して管理されます。 これは、ISO-8601 形式で表した期間 (PTHH:MM:SS など) であり、DVR の容量を指定し、最短で 3 分から最長で 25 時間まで設定できます。

## <a name="liveoutput"></a>LiveOutput

**ArchiveWindowLength** 値で、視聴者が現在のライブ位置からどのくらい戻ることができるかが決まります。  **ArchiveWindowLength** で、クライアント マニフェストが拡大できる期間も決まります。

たとえば、フットボールの試合をストリーミングしていて、**ArchiveWindowLength** がわずか 30 分だとします。 試合の開始から 45 分にイベントの視聴を開始した視聴者は、最高で 15 分の時点まで戻ることができます。 試合の **LiveOutput** は **LiveEvent** が停止するまで継続されますが、**ArchiveWindowLength** の範囲から外れるコンテンツは引き続きストレージから破棄され、回復できません。 この例では、イベントの開始から 15 分までのビデオは DVR と [Asset](https://docs.microsoft.com/rest/api/media/assets) の BLOB ストレージ内のコンテナーから消去されています。 このアーカイブは回復できず、Azure BLOB ストレージ内のコンテナーから削除されます。

各 **LiveOutput** は **Asset** に関連付けられており、ビデオを関連する Azure BLOB ストレージ コンテナーに記録するために使用されます。 LiveOutput を公開するには、その **Asset** 用の **StreamingLocator** を作成する必要があります。 [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) を作成すると、視聴者に提供できるストリーミング URL を構築できます。

**LiveEvent** は、**LiveOutput** の同時実行を最大 3 つまでサポートしているため、1 つのライブ ストリームから最大 3 つの記録/アーカイブを作成できます。 これにより、1 つのイベントのさまざまな部分を必要に応じて発行したりアーカイブしたりできます。 たとえば、24 時間 365 日のライブ リニア フィードを配信し、顧客が見逃したコンテンツをオンデマンド視聴できるよう、終日プログラムの "記録" を作成するとします。 このシナリオではまず、プライマリ LiveOutput を、1 時間以下の短いアーカイブ ウィンドウで作成します。これは、視聴者が受信するプライマリ ライブ ストリームです。 この **LiveOutput** の **StreamingLocator** を作成し、アプリケーションまたは Web サイトに "ライブ" フィードとして発行することになります。 **LiveEvent** の実行中、同時実行される 2 つ目の **LiveOutput** を、プログラムの開始 (または後でトリミングしやすいよう 5 分前) の時点でプログラムから作成できます。 この 2 つ目の **LiveOutput** は、プログラムが終了して 5 分後に削除できます。 この 2 つ目の **Asset** を使用すると、新しい **StreamingLocator** を作成して、アプリケーションのカタログでオンデマンド資産としてこのプログラムを公開することができます。 オンデマンド ビデオとして共有したい他のプログラム範囲やハイライトについても、このプロセスを繰り返します。このプロセスはすべて、1 つ目の **LiveOutput** の "ライブ" フィードがリニア フィードを配信し続ける最中に行うことができます。 

> [!NOTE]
> **LiveOutput** は作成すると開始され、削除されると停止します。 **LiveOutput** を削除しても、基になる**資産**と資産のコンテンツは削除されません。  

## <a name="next-steps"></a>次の手順

- [ライブ ストリーミングの概要](live-streaming-overview.md)
- [ライブ ストリーミングのチュートリアル](stream-live-tutorial-with-api.md)

