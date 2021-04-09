---
title: Microsoft Azure Media Services の一般的なシナリオ | Microsoft Docs
description: この記事では、Microsoft Azure Media Services シナリオの概要を説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: cb7500489cc4516b8cf44dd029c0831103dc53a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007936"
---
# <a name="microsoft-azure-media-services-common-scenarios"></a>Microsoft Azure Media Services の一般的なシナリオ

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 には新機能は追加されません。 最新のバージョンである [Media Services v3](../latest/media-services-overview.md) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

Microsoft Azure Media Services (AMS) を使用すると、各種クライアント (テレビ、PC、モバイル デバイスなど) へのオンデマンド ストリーミング配信とライブ ストリーミング配信の両方で、ビデオやオーディオのコンテンツを安全にアップロード、保存、エンコード、パッケージ化できます。

この記事では、コンテンツのライブ配信またはオンデマンド配信の一般的なシナリオを紹介します。

## <a name="overview"></a>概要

### <a name="prerequisites"></a>前提条件

* Azure アカウント。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com)」を参照してください。
* Azure Media Services アカウントを作成します。 詳細については、[アカウントの作成](media-services-portal-create-account.md)に関するページを参照してください。
* コンテンツのストリーミング元のストリーミング エンドポイントは、**実行中** 状態である必要があります。

    AMS アカウントの作成時に、**既定** のストリーミング エンドポイントが自分のアカウントに追加され、**停止** 状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、ストリーミング エンドポイントが **実行中** 状態である必要があります。

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>AMS OData モデルに対する開発時によく使用されるオブジェクト

次の図は、Media Services OData モデルに対する開発時に最もよく使用されるオブジェクトの一部を示しています。

画像をクリックすると、フル サイズで表示されます。  

[![Azure Media Services オブジェクト データ モデルに対する開発時に最もよく使用されるオブジェクトの一部を示す図](./media/media-services-overview/media-services-overview-object-model-small.png)](./media/media-services-overview/media-services-overview-object-model.png#lightbox)

モデル全体は、[こちら](https://media.windows.net/API/$metadata?api-version=2.15)で確認できます。  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>ストレージのコンテンツを保護し、ストリーミング メディアを平文 (暗号化されていない) で配信する

![VoD ワークフロー](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. 高品質なメディア ファイルを資産にアップロードします。

    ストレージ暗号化オプションを資産に適用し、アップロード中のコンテンツとストレージ内のコンテンツを保護することをお勧めします。

1. アダプティブ ビットレート MP4 ファイルのセットにエンコードする。

    ストレージ暗号化オプションを出力資産に適用し、保存されているコンテンツを保護することをお勧めします。

1. 資産配信ポリシーを構成します (動的パッケージで使用)。

    アセットがストレージで暗号化されている場合は、アセット配信ポリシーを構成する **必要があります** 。
1. OnDemand ロケーターを作成して資産を発行します。
1. 公開済みコンテンツをストリーミングします。

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>ストレージ内のコンテンツを保護し、動的に暗号化されたストリーミング メディアを配信する

![PlayReady による保護](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. 高品質なメディア ファイルを資産にアップロードします。 ストレージ暗号化オプションを資産に適用します。
1. アダプティブ ビットレート MP4 ファイルのセットにエンコードする。 ストレージ暗号化オプションを出力資産に適用します。
1. 再生中に動的に暗号化する資産の、暗号化コンテンツ キーを作成します。
1. コンテンツ キー承認ポリシーを構成します。
1. 資産配信ポリシーを構成します (動的パッケージと動的暗号化で使用)。
1. OnDemand ロケーターを作成して資産を発行します。
1. 公開済みコンテンツをストリーミングします。

## <a name="deliver-progressive-download"></a>プログレッシブ ダウンロードの提供

1. 高品質なメディア ファイルを資産にアップロードします。
1. 単一 MP4 ファイルにエンコードする。
1. OnDemand または SAS ロケーターを作成して資産を発行します。 SAS ロケーターを使用する場合、コンテンツは Azure BLOB ストレージからダウンロードされます。 開始状態のストリーミング エンドポイントは必要ありません。
1. コンテンツを徐々にダウンロードします。

## <a name="delivering-live-streaming-events"></a>ライブ ストリーミング イベントの配信

1. さまざまなライブ ストリーミング プロトコル (RTMP や Smooth Streaming など) を使用してライブ コンテンツを取り込みます。
1. (省略可) ストリームをアダプティブ ビットレート ストリームにエンコードします。
1. ライブ ストリームをプレビューします。
1. 次を使用して配信します。
    1. 一般的なストリーミング プロトコル (MPEG DASH、Smooth、HLS など) を使用して顧客に直接
    1. Content Delivery Network (CDN) に配信し、そこからさらに配信する
    1. 後でストリーミングするために、取り込んだコンテンツを記録して保存する (ビデオ オン デマンド)。

ライブ ストリーミングを実行する際には、次のいずれかのルートを選択できます。

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>オンプレミスのエンコーダーからマルチビットレートのライブ ストリームを受信するチャネルを操作する (パススルー)

次の図は、 **パススルー** ワークフローに関連する AMS プラットフォームの主要な部分を示しています。

!["パススルー" ワークフローに関連する AMS プラットフォームの主要な部分を示す図。](./media/scenarios-and-availability/media-services-live-streaming-current.png)

詳細については、 [オンプレミスのエンコーダーからマルチビットレートのライブ ストリームを受信するチャネルの操作](media-services-live-streaming-with-onprem-encoders.md)に関するページを参照してください。

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Azure Media Services による Live Encoding を実行できるチャネルを操作する

次の図は、Media Services による Live Encoding の実行が有効なチャネルのライブ ストリーミング ワークフローに関連する AMS プラットフォームの主要な部分を示しています。

![ライブ ワークフロー](./media/scenarios-and-availability/media-services-live-streaming-new.png)

詳細については、「 [Azure Media Services を使用してライブ エンコードの実行が有効なチャネルを操作する](media-services-manage-live-encoder-enabled-channels.md)」をご覧ください。

## <a name="consuming-content"></a>コンテンツの使用

Azure Media Services には、ほとんどのプラットフォーム (iOS デバイス、Android デバイス、Windows、Windows Phone、Xbox、セットトップ ボックスなど) 向けのリッチで動的なクライアント再生アプリケーションの作成に必要なツールが用意されています。

## <a name="enabling-azure-cdn"></a>Azure CDN を有効にする

Media Services では、Azure CDN との統合をサポートしています。 Azure CDN を有効にする方法については、「 [Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-portal-manage-streaming-endpoints.md)」をご覧ください。

## <a name="scaling-a-media-services-account"></a>Media Services アカウントのスケーリング

AMS のお客様は、AMS アカウントのストリーミング エンドポイント、メディア処理、ストレージをスケーリングできます。

* Media Services のお客様は、**Standard** と **Premium** のいずれかのストリーミング エンドポイントを選択できます。 **Standard** ストリーミング エンドポイントは、ほとんどのストリーミング ワークロードに適しています。 **Premium** ストリーミング エンドポイントと同じ機能が備わっており、送信帯域幅が自動的にスケーリングされます。

    **Premium** ストリーミング エンドポイントは専用のスケーラブルな帯域幅の容量を提供するため、高度なワークロードに適しています。 **Premium** ストリーミング エンドポイントを持つユーザーは、既定で 1 つのストリーミング ユニット (SU) を取得します。 ストリーミング エンドポイントは、SU を追加することで拡張できます。 各 SU は、アプリケーションに追加の帯域幅の容量を提供します。 **Premium** ストリーミング エンドポイントのスケーリングの詳細については、[ストリーミング エンドポイントのスケーリング](media-services-portal-scale-streaming-endpoints.md)に関するトピックを参照してください。

* Media Services アカウントは、メディア処理タスクを処理する速度を決定する予約ユニットの種類に関連付けられます。 予約ユニットの種類は、以下から選択できます:**S1**、**S2**、**S3**。 たとえば、同じエンコード ジョブの場合に、予約ユニットの種類として **S1** よりも **S2** を使用する方が、ジョブの実行が高速になります。

    予約ユニットの種類を指定するだけでなく、**予約ユニット** (RU) を使用したアカウントのプロビジョニングを指定することもできます。 プロビジョニングされた RU の数によって、所定のアカウントで並列処理できるメディア タスクの数が決まります。

    > [!NOTE]
    > RU は、Azure Media Indexer を使用するインデックス作成ジョブを含む、すべてのメディア処理を並列化するために動作します。 ただし、エンコードとは異なり、インデックス作成ジョブでは高速予約ユニットを使用した高速処理は行われません。

    詳細については、[メディア処理のスケーリング](media-services-portal-scale-media-processing.md)に関するページを参照してください。

* ストレージ アカウントを追加して、Media Services アカウントの規模を設定することもできます。 各ストレージ アカウントの上限は 500 TB (テラバイト) です。 詳細については、[ストレージ アカウントの管理](./media-services-managing-multiple-storage-accounts.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[Media Services v3 への移行](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]