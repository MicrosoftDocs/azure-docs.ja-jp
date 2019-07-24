---
title: Microsoft Azure Media Services のシナリオとデータ センターにおける機能の可用性 | Microsoft Docs
description: このトピックでは、Microsoft Azure Media Services のシナリオの概要のほか、データ センターにおける機能とサービスの可用性について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 3576a50cd1510a4da562981ff9ae98e12a357fc9
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317010"
---
# <a name="scenarios-and-availability-of-media-services-features-across-datacenters"></a>Media Services のシナリオとデータ センターにおける機能の可用性

Microsoft Azure Media Services (AMS) を使用すると、各種クライアント (テレビ、PC、モバイル デバイスなど) へのオンデマンド ストリーミング配信とライブ ストリーミング配信の両方で、ビデオやオーディオのコンテンツを安全にアップロード、保存、エンコード、パッケージ化できます。

AMS は、世界中の複数のデータセンターで動作しています。 これらのデータセンターは、地理的なリージョンにグループ化されていて、アプリケーションの作成場所を選択するときの柔軟性を与えています。 [リージョンとその場所の一覧](https://azure.microsoft.com/regions/)をご確認ください。 

このトピックでは、コンテンツの[ライブ](#live_scenarios)配信またはオンデマンド配信の一般的なシナリオを紹介します。 また、データ センターにおけるメディア機能とサービスの可用性についても説明します。

## <a name="overview"></a>概要

### <a name="prerequisites"></a>前提条件

Azure Media Services を使用するには、次が必要です。

* Azure アカウント。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com)を参照してください。
* Azure Media Services アカウントを作成します。 詳細については、[アカウントの作成](media-services-portal-create-account.md)に関するページを参照してください。
* コンテンツのストリーミング元のストリーミング エンドポイントは、**実行中**状態である必要があります。

    AMS アカウントの作成時に、**既定**のストリーミング エンドポイントが自分のアカウントに追加され、**停止**状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、ストリーミング エンドポイントが**実行中**状態である必要があります。

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>AMS OData モデルに対する開発時によく使用されるオブジェクト

次の図は、Media Services OData モデルに対する開発時に最もよく使用されるオブジェクトの一部を示しています。

画像をクリックすると、フル サイズで表示されます。  

<a href="./media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a> 

モデル全体は、[こちら](https://media.windows.net/API/$metadata?api-version=2.15)で確認できます。  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>ストレージのコンテンツを保護し、ストリーミング メディアを平文 (暗号化されていない) で配信する

![VoD ワークフロー](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. 高品質なメディア ファイルを資産にアップロードします。

    ストレージ暗号化オプションを資産に適用し、アップロード中のコンテンツとストレージ内のコンテンツを保護することをお勧めします。
2. アダプティブ ビットレート MP4 ファイルのセットにエンコードする。

    ストレージ暗号化オプションを出力資産に適用し、保存されているコンテンツを保護することをお勧めします。
3. 資産配信ポリシーを構成します (動的パッケージで使用)。

    アセットがストレージで暗号化されている場合は、アセット配信ポリシーを構成する **必要があります** 。
4. OnDemand ロケーターを作成して資産を発行します。
5. 公開済みコンテンツをストリーミングします。

データ センターにおける可用性については、[可用性](#availability)に関するセクションを参照してください。

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>ストレージ内のコンテンツを保護し、動的に暗号化されたストリーミング メディアを配信する

![PlayReady による保護](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. 高品質なメディア ファイルを資産にアップロードします。 ストレージ暗号化オプションを資産に適用します。
2. アダプティブ ビットレート MP4 ファイルのセットにエンコードする。 ストレージ暗号化オプションを出力資産に適用します。
3. 再生中に動的に暗号化する資産の、暗号化コンテンツ キーを作成します。
4. コンテンツ キー承認ポリシーを構成します。
5. 資産配信ポリシーを構成します (動的パッケージと動的暗号化で使用)。
6. OnDemand ロケーターを作成して資産を発行します。
7. 公開済みコンテンツをストリーミングします。

データ センターにおける可用性については、[可用性](#availability)に関するセクションを参照してください。

## <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Media Analytics を使用して、ビデオから実用的な洞察を得る

Media Analytics は音声および視覚コンポーネントの集合体であり、組織や企業がこれを活用することで、ビデオ ファイルから実用的な洞察を簡単に引き出すことができます。 詳細については、「 [Azure Media Services Analytics の概要](media-services-analytics-overview.md)」を参照してください。

1. 高品質なメディア ファイルを資産にアップロードします。
2. [Media Analytics の概要](media-services-analytics-overview.md)に関するセクションで説明されている Media Analytics サービスのいずれかを使用して、ビデオを処理します。
3. Media Analytics のメディア プロセッサによって MP4 ファイルまたは JSON ファイルが生成されます。 メディア プロセッサによって MP4 ファイルが生成された場合は、そのファイルのプログレッシブ ダウンロードが可能です。 メディア プロセッサによって JSON ファイルが生成された場合は、そのファイルを Azure Blob Storage からダウンロードできます。

データ センターにおける可用性については、[可用性](#availability)に関するセクションを参照してください。

## <a name="deliver-progressive-download"></a>プログレッシブ ダウンロードの提供

1. 高品質なメディア ファイルを資産にアップロードします。
2. 単一 MP4 ファイルにエンコードする。
3. OnDemand または SAS ロケーターを作成して資産を発行します。

    SAS ロケーターを使用する場合、コンテンツは Azure BLOB ストレージからダウンロードされます。 この場合、開始状態のストリーミング エンドポイントは必要ありません。
4. コンテンツを徐々にダウンロードします。

## <a id="live_scenarios"></a>ライブ ストリーミング イベントの配信 

1. さまざまなライブ ストリーミング プロトコル (RTMP や Smooth Streaming など) を使用してライブ コンテンツを取り込みます。
2. (省略可) ストリームをアダプティブ ビットレート ストリームにエンコードします。
3. ライブ ストリームをプレビューします。
4. コンテンツを一般的なストリーミング プロトコル (MPEG DASH、Smooth、HLS など) を介して顧客に直接配信したり、再配布のための Content Delivery Network (CDN) に配信したりします。

    または

    後でストリーミングするために、取り込んだコンテンツを記録して保存します (ビデオ オン デマンド)。

ライブ ストリーミングを実行する際には、次のいずれかのルートを選択できます。

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>オンプレミスのエンコーダーからマルチビットレートのライブ ストリームを受信するチャネルを操作する (パススルー)

次の図は、 **パススルー** ワークフローに関連する AMS プラットフォームの主要な部分を示しています。

![ライブ ワークフロー](./media/scenarios-and-availability/media-services-live-streaming-current.png)

詳細については、 [オンプレミスのエンコーダーからマルチビットレートのライブ ストリームを受信するチャネルの操作](media-services-live-streaming-with-onprem-encoders.md)に関するページを参照してください。

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Azure Media Services による Live Encoding を実行できるチャネルを操作する

次の図は、Media Services による Live Encoding の実行が有効なチャネルのライブ ストリーミング ワークフローに関連する AMS プラットフォームの主要な部分を示しています。

![ライブ ワークフロー](./media/scenarios-and-availability/media-services-live-streaming-new.png)

詳細については、「 [Azure Media Services を使用してライブ エンコードの実行が有効なチャネルを操作する](media-services-manage-live-encoder-enabled-channels.md)」をご覧ください。

データ センターにおける可用性については、[可用性](#availability)に関するセクションを参照してください。

## <a name="consuming-content"></a>コンテンツの使用

Azure Media Services には、ほとんどのプラットフォーム (iOS デバイス、Android デバイス、Windows、Windows Phone、Xbox、セットトップ ボックスなど) 向けのリッチで動的なクライアント再生アプリケーションの作成に必要なツールが用意されています。 次のトピックには、Media Services からのストリーミング メディアを使用する独自のクライアント アプリケーションの開発に使用できる、SDK とプレーヤー フレームワークへのリンクがあります。 詳細については、「[ビデオ プレーヤー アプリケーションの開発](media-services-develop-video-players.md)」を参照してください。

## <a name="enabling-azure-cdn"></a>Azure CDN を有効にする

Media Services では、Azure CDN との統合をサポートしています。 Azure CDN を有効にする方法については、「 [Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-portal-manage-streaming-endpoints.md)」をご覧ください。

## <a id="scaling"></a>Media Services アカウントのスケーリング

AMS のお客様は、AMS アカウントのストリーミング エンドポイント、メディア処理、ストレージをスケーリングできます。

* Media Services のお客様は、**Standard** と **Premium** のいずれかのストリーミング エンドポイントを選択できます。 **Standard** ストリーミング エンドポイントは、ほとんどのストリーミング ワークロードに適しています。 **Premium** ストリーミング エンドポイントと同じ機能が備わっており、送信帯域幅が自動的にスケーリングされます。 

    **Premium** ストリーミング エンドポイントは専用のスケーラブルな帯域幅の容量を提供するため、高度なワークロードに適しています。 **Premium** ストリーミング エンドポイントを持つユーザーは、既定で 1 つのストリーミング ユニット (SU) を取得します。 ストリーミング エンドポイントは、SU を追加することで拡張できます。 各 SU は、アプリケーションに追加の帯域幅の容量を提供します。 **Premium** ストリーミング エンドポイントのスケーリングの詳細については、[ストリーミング エンドポイントのスケーリング](media-services-portal-scale-streaming-endpoints.md)に関するトピックを参照してください。

* Media Services アカウントは、メディア処理タスクを処理する速度を決定する予約ユニットの種類に関連付けられます。 予約ユニットの種類は、以下から選択できます:**S1**、**S2**、**S3**。 たとえば、同じエンコード ジョブの場合に、予約ユニットの種類として **S1** よりも **S2** を使用する方が、ジョブの実行が高速になります。

    予約ユニットの種類を指定するだけでなく、**予約ユニット** (RU) を使用したアカウントのプロビジョニングを指定することもできます。 プロビジョニングされた RU の数によって、所定のアカウントで並列処理できるメディア タスクの数が決まります。

    >[!NOTE]
    >RU は、Azure Media Indexer を使用するインデックス作成ジョブを含む、すべてのメディア処理を並列化するために動作します。 ただし、エンコードとは異なり、インデックス作成ジョブでは高速予約ユニットを使用した高速処理は行われません。

    詳細については、[メディア処理のスケーリング](media-services-portal-scale-media-processing.md)に関するページを参照してください。
* ストレージ アカウントを追加して、Media Services アカウントの規模を設定することもできます。 各ストレージ アカウントの上限は 500 TB (テラバイト) です。 既定の上限を超えるストレージ容量を設定するために、複数のストレージ アカウントを単一の Media Services アカウントにアタッチすることを選択できます。 詳細については、[ストレージ アカウントの管理](meda-services-managing-multiple-storage-accounts.md)に関するページを参照してください。

## <a id="availability"></a> データ センターにおける Media Services 機能の可用性

このセクションでは、データ センターにおける Media Services 機能の可用性について詳しく説明します。

### <a name="ams-accounts"></a>AMS アカウント

#### <a name="availability"></a>可用性

データ センターで Media Services が使用可能かどうかを確認するには、 https://azure.microsoft.com/status/ にアクセスして、「メディア」テーブルまでスクロールします。

### <a name="streaming-endpoints"></a>ストリーミング エンドポイント 

Media Services のお客様は、**Standard** と **Premium** のいずれかのストリーミング エンドポイントを選択できます。 詳細については、[スケーリング](#scaling)に関するセクションを参照してください。

#### <a name="availability"></a>可用性

|Name|Status|データ センター
|---|---|---|
|Standard|一般公開|All|
|Premium|一般公開|All|

### <a name="live-encoding"></a>ライブ エンコード

#### <a name="availability"></a>可用性

以下を除く、すべてのデータ センターで利用できます:ドイツ、ブラジル南部、インド西部、インド南部、インド中部。 

### <a name="encoding-media-processors"></a>Encoding メディア プロセッサ

AMS には、**Media Encoder Standard** と **Media Encoder Premium ワークフロー**という 2 つのオンデマンド エンコーダーが用意されています。 詳細については、「[Azure オンデマンド メディア エンコーダーの概要と比較](media-services-encode-asset.md)」を参照してください。 

#### <a name="availability"></a>可用性

|メディア プロセッサ名|Status|データ センター
|---|---|---|
|メディア エンコーダー スタンダード|一般公開|All|
|メディア エンコーダー Premium ワークフロー|一般公開|中国を除くすべて|

### <a name="analytics-media-processors"></a>Analytics メディア プロセッサ

Media Analytics は音声および視覚コンポーネントの集合体であり、組織や企業がこれを活用することで、ビデオ ファイルから実用的な洞察を簡単に引き出すことができます。 詳細については、「 [Azure Media Services Analytics の概要](media-services-analytics-overview.md)」を参照してください。

#### <a name="availability"></a>可用性

|メディア プロセッサ名|Status|データ センター
|---|---|---|
|Azure Media Face Detector|プレビュー|All|
|Azure Media Hyperlapse|プレビュー|All|
|Azure Media Indexer|一般公開|All|
|Azure Media Motion Detector|プレビュー|All|
|Azure Media OCR|プレビュー|All|
|Azure Media Redactor|プレビュー|All|
|Azure Media Stabilizer|プレビュー|All|
|Azure Media Video Thumbnails|プレビュー|All|
|Azure Media Indexer 2|プレビュー|中国および連邦政府リージョンを除くすべて|

### <a name="protection"></a>保護

Microsoft Azure Media Services を使用すると、メディアがコンピューターから離れてから、保存、処理、配信されるまでのセキュリティ保護が可能になります。 詳細については、[AMS コンテンツの保護](media-services-content-protection-overview.md)に関するページを参照してください。

#### <a name="availability"></a>可用性

|暗号化|Status|データ センター|
|---|---|---| 
|Storage|一般公開|All|
|AES-128 キー|一般公開|All|
|FairPlay|一般公開|All|
|PlayReady|一般公開|All|
|Widevine|一般公開|ドイツ、連邦政府、中国を除くすべて

### <a name="reserved-units-rus"></a>予約ユニット (RU)

プロビジョニングされた予約ユニットの数によって、所定のアカウントで並列処理できるメディア タスクの数が決まります。 

詳細については、[スケーリング](#scaling)に関するセクションを参照してください。

#### <a name="availability"></a>可用性

すべてのデータ センターで利用できます。

### <a name="reserved-unit-ru-type"></a>予約ユニット (RU) の種類

Media Services アカウントは、メディア処理タスクを処理する速度を決定する予約ユニットの種類に関連付けられます。 予約ユニットの種類は、以下から選択できます:S1、S2、S3。

詳細については、[スケーリング](#scaling)に関するセクションを参照してください。

#### <a name="availability"></a>可用性

|RU の種類の名前|Status|データ センター
|---|---|---|
|S1|一般公開|All|
|S2|一般公開|ブラジル南部とインド西部を除くすべて|
|S3|一般公開|インド西部を除くすべて|

## <a name="next-steps"></a>次の手順

Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

