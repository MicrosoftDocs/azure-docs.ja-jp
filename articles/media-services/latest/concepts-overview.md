---
title: Azure Media Services の概念 - Azure | Microsoft Docs
description: このトピックでは、Azure Media Services の概念を簡単に説明し、詳細がわかるリンクを提供します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8f21374ae5da76c9954acc5227f593ab4be19ce9
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630548"
---
# <a name="media-services-concepts"></a>Media Services の概念

このトピックでは、Azure Media Services の概念を簡単に説明し、Media Services v3 の概念と機能について詳しく説明しているページのリンクを提供します。 開発を始める前に、これらのトピックで説明されている基本的な概念を確認する必要があります。

> [!NOTE]
> 現時点では、Azure portal を使用して v3 リソースを管理することはできません。 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref)、サポートされている [SDK](developers-guide.md) のいずれかを使用します。

## <a name="cloud-upload-and-storage"></a>クラウドのアップロードとストレージ

Azure でメディア コンテンツの管理、暗号化、エンコード、分析、およびストリーミングを開始するには、Media Services アカウントを作成し、デジタル ファイルを**アセット**にアップロードする必要があります。

- [クラウドのアップロードとストレージ](storage-account-concept.md)
- [アセットの概念](assets-concept.md)

## <a name="encoding"></a>エンコード

高品質なデジタル メディア ファイルをアセットにアップロードすると、さまざまなブラウザーやデバイスで再生できる形式にエンコードできます。 

Media Services v3 でエンコードするには、**変換**と**ジョブ**を作成する必要があります。

![変換](./media/encoding/transforms-jobs.png)

- [Transform と Job](transforms-jobs-concept.md)
- [Media Services でのエンコード](encoding-concept.md)

## <a name="media-analytics"></a>メディア分析

ビデオとオーディオ ファイルを分析するには、**変換**と**ジョブ**を作成する必要もあります。

- [ビデオおよびオーディオ ファイルの分析](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>パッケージ化、デリバリー、保護

コンテンツがエンコードされたら、**ダイナミック パッケージ**を活用できます。 **ストリーミング エンドポイント**は、メディア コンテンツをクライアント プレーヤーに届ける Media Services のダイナミック パッケージ サービスです。 出力アセット内のビデオをクライアントが再生できるようにするには、**ストリーミング ロケーター**を作成し、ストリーミング URL をビルドする必要があります。 

**ストリーミング ロケーター**を作成するときに、アセット名に加えて、**ストリーミング ポリシー**を指定する必要があります。 **ストリーミング ポリシー**を使用して、**ストリーミング ロケーター**のためのストリーミング プロトコルと暗号化オプション (該当する場合) を定義できます。

ダイナミック パッケージは、コンテンツのストリーミングがライブの場合でもオンデマンドの場合でも使用されます。 次の図は、ダイナミック パッケージのワークフローを使用したオンデマンド ストリーミングを示しています。

![ダイナミック パッケージ](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Media Services では、Advanced Encryption Standard (AES-128) または主要な 3 つのデジタル著作権管理 (DRM) システム (Microsoft PlayReady、Google Widevine、Apple FairPlay) によって動的に暗号化されたライブまたはオンデマンドのコンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM (PlayReady、Widevine、FairPlay) ライセンスを配信するためのサービスも提供しています。

ストリームで暗号化オプションを指定するには、**コンテンツ キー ポリシー**を作成し、それを**ストリーミング ロケーター**に関連付けます。 **クライアント キー ポリシー**では、エンド クライアントにコンテンツ キーを配信する方法を構成できます。

次の図は、Media Services でのコンテンツ保護のワークフローを示しています。 

![コンテンツの保護](./media/content-protection/content-protection.svg)

&#42; 動的暗号化は、AES-128 "クリア キー"、CBCS、CENC をサポートします。 

Media Services **動的マニフェスト**を使用し、動画を特定の方法でストリーミングしたり、サブクリップのみをストリーミングしたりできます。 次の例では、エンコーダーを使用して中間ファイル アセットを 7 つの ISO MP4 ビデオ演奏にエンコードしました (180p ～ 1080p)。 エンコード済みのアセットは、HLS、MPEG DASH、Smooth のいずれかのストリーミング プロトコルに動的にパッケージ化できます。  図の上部には、フィルターを使用しないアセット用の HLS マニフェストが表示されています (これには全 7 演奏が含まれています)。  左下には、"ott" という名前のフィルターが適用された HLS マニフェストが表示されています。 "ott" フィルターは、1 Mbps より低いビットレートをすべて削除するように指定されているので、下位 2 つの品質レベルが応答で除去されています。 右下には、"mobile" という名前のフィルターが適用された HLS マニフェストが表示されています。 "mobile" フィルターでは、2 つの 1080 p の演奏が除去されてしまうため、解像度が 720 p を超える演奏を削除するように指定します。

![演奏フィルター処理](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [ダイナミック パッケージ](dynamic-packaging-overview.md)
- [ストリーミング エンドポイント](streaming-endpoint-concept.md)
- [ストリーミング ロケーター](streaming-locators-concept.md)
- [ストリーミング ポリシー](streaming-policy-concept.md)
- [コンテンツ キー ポリシー](content-key-policy-concept.md)
- [コンテンツ保護](content-protection-overview.md)
- [動的マニフェスト](filters-dynamic-manifest-overview.md)
- [フィルター](filters-concept.md)

## <a name="live-streaming"></a>ライブ ストリーミング

Azure Media Services では、Azure クラウドで顧客にライブ イベントを配信することができます。 **ライブ イベント**は、ライブ ビデオ フィードの取り込みと処理を担当します。 **ライブ イベント**を作成するときに、リモートのエンコーダーからライブ信号を送信するために使用できる入力エンドポイントが作成されます。 ストリームが**ライブ イベント**に流れ始めると、**アセット**、**ライブ出力**、**ストリーミング ロケーター**を作成することにより、ストリーミング イベントを開始できます。 **ライブ出力**により、ストリームが**アセット**にアーカイブされ、**ストリーミング エンドポイント**を介して視聴者がストリームを使用できるようになります。 **ライブ イベント**には、**パススルー**と**ライブ エンコード**の 2 種類があります。

次の図は、パススルーのワークフローを示しています。

![パススルー](./media/live-streaming/pass-through.svg)

- [ライブ ストリーミングの概要](live-streaming-overview.md)
- [ライブ イベントとライブ出力](live-events-outputs-concept.md)

## <a name="monitoring"></a>監視

### <a name="event-grid"></a>Event Grid

ジョブの進行状況を確認するには、**Event Grid** を使用してください。 Media Services では、ライブ イベントも出力されます。 Event Grid では、アプリはほぼすべての Azure サービスやカスタム ソースのイベントをリッスンし、対応できます。 

- [Event Grid イベントの処理](reacting-to-media-services-events.md)
- [スキーマ](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Azure Monitor を使用して、アプリケーションの実行状況を理解する上で役立つメトリックと診断ログを監視します。

- [メトリックと診断ログ](media-services-metrics-diagnostic-logs.md)
- [診断ログのスキーマ](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Player クライアント

Azure Media Player を使用すると、Media Services によってストリーミングされたメディア コンテンツをさまざまなブラウザーやデバイス上で再生できます。 Azure Media Player では、HTML5、Media Source Extensions (MSE)、Encrypted Media Extensions (EME) といった業界標準を使用して、強化されたアダプティブ ストリーミングを提供します。 

- [Azure Media Player の概要](use-azure-media-player.md)

## <a name="next-steps"></a>次の手順

* [リモート ファイルのエンコードとビデオのストリーミング – REST](stream-files-tutorial-with-rest.md)
* [アップロードされたファイルのエンコードとビデオのストリーム配信 - .NET](stream-files-tutorial-with-api.md)
* [ライブ ストリーム配信 - .NET](stream-live-tutorial-with-api.md)
* [ビデオの分析 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 動的暗号化 - .NET](protect-with-aes128.md)
* [マルチ DRM を使用した動的な暗号化 - .NET](protect-with-drm.md) 
