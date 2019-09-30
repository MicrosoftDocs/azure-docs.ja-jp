---
title: メディア処理のスケール設定の概要 | Microsoft Docs
description: このトピックでは、Azure Media Services を使用したメディア処理のスケール設定の概要を示します。
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
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: 780d3ab5047bff321d0c554880ba2995bcf25524
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102910"
---
# <a name="scaling-media-processing-overview"></a>メディア処理のスケール設定の概要 
このページでは、メディア処理のスケールを設定する方法と、スケール設定を行う理由の概要について説明します。 

## <a name="overview"></a>概要
Media Services アカウントは、メディア処理タスクを処理する速度を決定する予約ユニットの種類に関連付けられます。 予約ユニットの種類は、以下から選択できます:**S1**、**S2**、**S3**。 たとえば、同じエンコード ジョブの場合に、予約ユニットの種類として **S1** よりも **S2** を使用する方が、ジョブの実行が高速になります。 詳細については、 [予約ユニットの種類](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/)に関するページを参照してください。

予約ユニットの種類を指定するだけでなく、予約ユニットを使用したアカウントのプロビジョニングを指定することもできます。 プロビジョニングされた予約ユニットの数によって、所定のアカウントで並列処理できるメディア タスクの数が決まります。 たとえば、アカウントの予約ユニットの数が 5 である場合、処理するタスクがある限り、5 個のメディア タスクが並列実行されます。 残りのタスクはキューで待機して、実行中のタスクが完了するとキューから取り出されて順番に処理されます。 アカウントに予約ユニットが用意されていない場合、タスクは逐次処理されます。 この場合、あるタスクが終了した後、次のタスクが開始するまでの待機時間は、システムのリソースが利用できるかどうかに左右されます。

## <a name="choosing-between-different-reserved-unit-types"></a>さまざまな予約ユニットの種類の選択
次の表は、さまざまなエンコーディングの速度を選択して決定するときに役立ちます。 いくつかのベンチマーク ケースも用意されています[ダウンロード可能なビデオ](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)独自のテストを実行します。

|RU の種類|シナリオ|[7 分間の 1080p ビデオ](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)に関するサンプル結果|
|---|---|---|
| **S1**|シングル ビットレート エンコード。 <br/>解像度が SD 以下であり、時間的制約がなく、低コストなファイル。|"H264 Single Bitrate SD 16x9" を使用したシングル ビットレート SD 解像度 MP4 ファイルへのエンコードには約 7 分かかります。|
| **S2**|シングル ビットレート エンコードと複数ビットレート エンコード。<br/>通常は SD と HD 両方のエンコードに使用されます。|"H264 Single Bitrate 720p" プリセットでのエンコードには約 6 分かかります。<br/><br/>"H264 Single Bitrate 720p" プリセットでのエンコードには約 12 分かかります。|
| **S3**|シングル ビットレート エンコードと複数ビットレート エンコード。<br/>解像度がフル HD および 4K であるビデオ。 時間に依存しない高速ターンアラウンド エンコード。|"H264 Single Bitrate 1080p" プリセットでのエンコードは約 3 分かかります。<br/><br/>"H264 Multiple Bitrate 1080p" プリセットでのエンコードは約 8 分かかります。|

## <a name="considerations"></a>考慮事項
> [!IMPORTANT]
> このセクションで説明する考慮事項を確認してください。  
> 
> 

* Media Services v3 または Video Indexer によってトリガーされるオーディオ分析およびビデオ分析ジョブでは、S3 のユニットの種類を強くお勧めします。
* 共有プールを使用する (すなわち、予約ユニットを使用しない) 場合のエンコード タスクのパフォーマンスは S1 予約ユニットを使用したときと同等になります。 ただし、タスクがキューに登録された状態である時間の上限はなく、同時に実行されるタスクの数は最大で 1 つのみです。

## <a name="billing"></a>課金

アカウントにメディア占有ユニットがプロビジョニングされた時間 (分) に基づいて課金されます。 課金は、アカウントで実行中のジョブがあるかどうかとは無関係に発生します。 詳しくは、[Media Services の価格詳細](https://azure.microsoft.com/pricing/details/media-services/)ページの FAQ のセクションをご覧ください。   

## <a name="quotas-and-limitations"></a>クォータと制限
クォータと制限の詳細、サポート チケットを開く方法については、「 [Quotas and limitations (クォータと制限)](media-services-quotas-and-limitations.md)」をご覧ください

## <a name="next-step"></a>次のステップ
以下のいずれかのテクノロジを利用して、メディア処理のスケール設定タスクを実現します。 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [ポータル](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> 最新バージョンの Java SDK を入手し、Java での開発を始めるには、「[Azure Media Services 用 Java クライアント SDK の概要](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use)」を参照してください。 <br/>
> Media Services 用の最新の PHP SDK をダウンロードするには、[Packagist リポジトリ](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)でバージョン 0.5.7 の Microsoft/WindowAzure パッケージを検索してください。  

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

