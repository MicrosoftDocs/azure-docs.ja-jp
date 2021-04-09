---
title: Media Services の用語と概念
description: Azure Media Services の用語と概念について説明します。
services: media-servicesgit
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 309ef8baada22f59b1395164626dd664366cb4d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581135"
---
# <a name="media-services-terminology-and-concepts"></a>Media Services の用語と概念

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

このトピックでは、Azure Media Services の用語と概念について簡単に説明します。 また、この記事では、Media Services v3 の概念と機能を詳細に説明する記事へのリンクを提供します。

開発を始める前に、これらのトピックで説明されている基本的な概念を確認する必要があります。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Media Services v3 の用語

|期間|説明|
|---|---|
|ライブ イベント|**ライブ イベント** は、動画、音声、およびリアルタイム メタデータのライブ ストリームの取り込み、コード変換 (省略可能)、およびパッケージ化を表します。<br/><br/>Media Services v2 API から移行するお客様の場合、**ライブ イベント** は v2 の **Channel** エンティティに置き換わるものです。 詳細については、[v2 から v3 への移行](migrate-v-2-v-3-migration-introduction.md)に関する記事を参照してください。|
|ストリーミング エンドポイント/梱包/Origin|**ストリーミング エンドポイント** は、ダイナミック (Just-In-Time) パッケージおよび配信元サービスを表します。これは、ライブのオンデマンド コンテンツをクライアント プレーヤー アプリケーションに直接配信できます。 一般的なストリーミング メディア プロトコル (HLS または DASH) のいずれかを使用します。 さらに、**ストリーミング エンドポイント** は、業界有数のデジタル著作権管理 (DRM) に動的 (Just-In-Time) 暗号化を提供します。<br/><br/>メディア ストリーミング業界では、このサービスは、一般的に **パッケージャー** または **配信元** と呼ばれます。  この機能に関するその他の一般的な業界用語には、JITP (Just-in-time-packager) や JITE (Just-in-time-encryption) があります。

## <a name="media-services-v3-concepts"></a>Azure Media Services v3 の概念

|概念|説明|リンク|
|---|---|---|
|アセットとコンテンツのアップロード|Azure でメディア コンテンツの管理、暗号化、エンコード、分析、およびストリーミングを開始するには、Media Services アカウントを作成し、デジタル ファイルを **アセット** にアップロードする必要があります。|[クラウドのアップロードとストレージ](storage-account-concept.md)<br/><br/>[アセットの概念](assets-concept.md)|
|コンテンツのエンコード|高品質なデジタル メディア ファイルをアセットにアップロードすると、さまざまなブラウザーやデバイスで再生できる形式にエンコードできます。 <br/><br/>Media Services v3 でエンコードするには、**変換** と **ジョブ** を作成する必要があります。|[Transform と Job](transforms-jobs-concept.md)<br/><br/>[Media Services でのエンコード](encoding-concept.md)|
|コンテンツの分析 (Video Indexer)|Media Services v3 では Media Services v3 プリセットを使用して、ビデオ ファイルとオーディオ ファイルから分析情報を抽出できます。 Media Services v3 プリセットを使用してコンテンツを分析するには **変換** と **ジョブ** を作成する必要があります。<br/><br/>より詳細な分析情報が必要な場合は [Video Indexer](../video-indexer/index.yml) を直接使用します。|[ビデオおよびオーディオ ファイルの分析](analyzing-video-audio-files-concept.md)|
|パッケージと配信|コンテンツがエンコードされたら、**ダイナミック パッケージ** を活用できます。 Media Services では、 **ストリーミング エンドポイント** は、メディア コンテンツをクライアント プレーヤーに配信するために使用するダイナミック パッケージ サービスです。 出力アセット内のビデオをクライアントが再生できるようにするには、**ストリーミング ロケーター** を作成し、ストリーミング URL をビルドする必要があります。 <br/><br/>**ストリーミング ロケーター** を作成するときに、アセット名に加えて、**ストリーミング ポリシー** を指定する必要があります。 **ストリーミング ポリシー** を使用して、**ストリーミング ロケーター** のためのストリーミング プロトコルと暗号化オプション (該当する場合) を定義できます。 ダイナミック パッケージは、コンテンツのストリーミングがライブの場合でもオンデマンドの場合でも使用されます。 <br/><br/>Media Services **動的マニフェスト** を使用し、動画を特定の方法でストリーミングしたり、サブクリップのみをストリーミングしたりできます。|[ダイナミック パッケージ](dynamic-packaging-overview.md)<br/><br/>[ストリーミング エンドポイント](streaming-endpoint-concept.md)<br/><br/>[ストリーミング ロケーター](streaming-locators-concept.md)<br/><br/>[ストリーミング ポリシー](streaming-policy-concept.md)<br/><br/>[動的マニフェスト](filters-dynamic-manifest-overview.md)<br/><br/>[フィルター](filters-concept.md)|
|コンテンツの保護|Media Services では、Advanced Encryption Standard (AES-128) または主要な 3 つの DRM システム (Microsoft PlayReady、Google Widevine、Apple FairPlay) によって動的に暗号化されたライブまたはオンデマンドのコンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM (PlayReady、Widevine、FairPlay) ライセンスを配信するためのサービスも提供しています。 <br/><br/>ストリームで暗号化オプションを指定するには、**コンテンツ キー ポリシー** を作成し、それを **ストリーミング ロケーター** に関連付けます。 **クライアント キー ポリシー** では、エンド クライアントにコンテンツ キーを配信する方法を構成できます。<br/><br/> 同じオプションが必要な場合は常に、既存のポリシーを再利用するようにしてください。| [コンテンツ キー ポリシー](content-key-policy-concept.md)<br/><br/>[コンテンツ保護](content-protection-overview.md)|
|ライブ ストリーミング|Media Services では、Azure クラウドで顧客にライブ イベントを配信することができます。 **ライブ イベント** は、ライブ ビデオ フィードの取り込みと処理を担当します。 **ライブ イベント** を作成するときに、リモートのエンコーダーからライブ信号を送信するために使用できる入力エンドポイントが作成されます。 ストリームが **ライブ イベント** に流れ始めると、**アセット**、**ライブ出力**、**ストリーミング ロケーター** を作成することにより、ストリーミング イベントを開始できます。 **ライブ出力** により、ストリームが **アセット** にアーカイブされ、**ストリーミング エンドポイント** を介して視聴者がストリームを使用できるようになります。 ライブ イベントは、"*パススルー*" (オンプレミスのライブ エンコーダーによって複数のビットレート ストリームが送信される) または "*ライブ エンコード*" (オンプレミスのライブ エンコーダーによってシングル ビットレート ストリームが送信される) のいずれかに設定できます。 |[ライブ ストリーミングの概要](live-streaming-overview.md)<br/><br/>[ライブ イベントとライブ出力](live-events-outputs-concept.md)|
|Event Grid を使用した監視|ジョブの進行状況を確認するには、**Event Grid** を使用します。 Media Services では、ライブ イベントも出力されます。 Event Grid では、アプリはほぼすべての Azure サービスやカスタム ソースのイベントをリッスンし、対応できます。 |[Event Grid イベントの処理](monitoring/reacting-to-media-services-events.md)<br/><br/>[スキーマ](monitoring/media-services-event-schemas.md)|
|Azure Monitor を使用した監視|Azure Monitor を使用して、アプリの実行状況を理解する上で役立つメトリックと診断ログを監視します。|[メトリックと診断ログ](monitoring/monitor-media-services-data-reference.md)<br/><br/>[診断ログのスキーマ](monitoring/monitor-media-services-data-reference.md)|
|Player クライアント|Azure Media Player を使用すると、Media Services によってストリーミングされたメディア コンテンツをさまざまなブラウザーやデバイス上で再生できます。 Azure Media Player では、HTML5、Media Source Extensions (MSE)、Encrypted Media Extensions (EME) といった業界標準を使用して、強化されたアダプティブ ストリーミングを提供します。 |[Azure Media Player の概要](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次のステップ

* [リモート ファイルのエンコードとビデオのストリーミング – REST](stream-files-tutorial-with-rest.md)
* [アップロードされたファイルのエンコードとビデオのストリーム配信 - .NET](stream-files-tutorial-with-api.md)
* [ライブ ストリーム配信 - .NET](stream-live-tutorial-with-api.md)
* [ビデオの分析 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 動的暗号化 - .NET](protect-with-aes128.md)
* [マルチ DRM を使用した動的な暗号化 - .NET](protect-with-drm.md)