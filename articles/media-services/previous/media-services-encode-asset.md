---
title: Azure オンデマンド メディア エンコーダーの概要 | Microsoft Docs
description: Azure Media Services には、クラウド内のメディア エンコーディングに使用できる複数のオプションが用意されています。 この記事では、Azure オンデマンド メディア エンコーダーの概要を説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: d5d5b8a7328ee82e94d494795617832cb0258667
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901508"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Azure オンデマンド メディア エンコーダーの概要 

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-from-v2-to-v3.md)を参照してください。

Azure Media Services には、クラウド内のメディア エンコーディングに使用できる複数のオプションが用意されています。

Media Services を使い始める場合、コーデックとファイル形式の違いを理解することが重要です。
コーデックは圧縮/展開アルゴリズムを実装するソフトウェアで、ファイル形式は圧縮されたビデオを保持するコンテナーです。

Media Services には動的パッケージ化機能があり、アダプティブ ビットレート MP4 またはSmooth Streamingでエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG DASH、HLS、Smooth Streaming) でそのまま配信できます。つまり、これらのストリーミング形式に再度パッケージ化する必要がありません。

Media Services アカウントの作成時に、**既定**のストリーミング エンドポイントが**停止**状態でアカウントに追加されます。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態である必要があります。 ストリーミング エンドポイントの課金は、エンドポイントが**実行中**状態のときに発生します。

Media Services は次のオンデマンド エンコーダーをサポートしてます。これらについてはこの記事で説明します。

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [メディア エンコーダー Premium ワークフロー](media-services-encode-asset.md#media-encoder-premium-workflow)

この記事には、オンデマンド メディア エンコーダーの簡単な説明と、詳しい情報を提供する記事のリンクが含まれています。 また、このトピックではエンコーダーを比較します。

既定では、1 つの Media Services アカウントにつき、同時に 1 つのアクティブなエンコーディング タスクを実行できます。 エンコード ユニットを予約して、複数のエンコード タスク (購入したエンコード予約ユニットごとに 1 つ) を同時に実行できます。 詳細については、「 [エンコード ユニットの拡大/縮小](media-services-scale-media-processing-overview.md)」を参照してください。

## <a name="media-encoder-standard"></a>メディア エンコーダー スタンダード

### <a name="how-to-use"></a>使用方法
[メディア エンコーダー スタンダードを使用したエンコード方法](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>形式
[形式とコーデック](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>プリセット
Media Encoder Standard は、 [ここ](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)で説明されているエンコーダーのプリセット文字列のいずれかを使用して構成されます。

### <a name="input-and-output-metadata"></a>入力メタデータと出力メタデータ
エンコーダーの入力メタデータの説明は [ここ](media-services-input-metadata-schema.md)にあります。

エンコーダーの出力メタデータの説明は [ここ](media-services-output-metadata-schema.md)にあります。

### <a name="generate-thumbnails"></a>サムネイルを生成する
詳細については、「 [サムネイルを生成する](media-services-advanced-encoding-with-mes.md#thumbnails)」をご覧ください。

### <a name="trim-videos-clipping"></a>動画をトリミングする (クリッピング)
詳細については、「 [動画をトリミングする (クリッピング)](media-services-advanced-encoding-with-mes.md#trim_video)」をご覧ください。

### <a name="create-overlays"></a>オーバーレイを作成する
詳細については、「 [オーバーレイを作成する](media-services-advanced-encoding-with-mes.md#overlay)」をご覧ください。

### <a name="see-also"></a>関連項目
[Media Services ブログ](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>メディア エンコーダー Premium ワークフロー
### <a name="overview"></a>概要
[Azure Media Services の Premium Encoding の紹介 (ブログの投稿)](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>使用方法
メディア エンコーダー プレミアム ワークフロー は複雑なワークフローを使用して構成されています。 Workflow ファイルは、 [Workflow Designer](media-services-workflow-designer.md) ツールを使用して作成して更新できます。

[Azure Media Services で Premium Encoding を使用する方法 (ブログの投稿)](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>既知の問題
入力ビデオにクローズド キャプションが含まれない場合でも、出力アセットには空の TTML ファイルが含まれます。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>関連記事
* [Media Encoder Standard のプリセットをカスタマイズし、高度なエンコード タスクを実行する](media-services-custom-mes-presets-with-dotnet.md)
* [クォータと制限](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
