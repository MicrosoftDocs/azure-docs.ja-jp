---
title: "メディア処理のスケール設定の概要 | Microsoft Docs"
description: "このトピックでは、Azure Media Services を使用したメディア処理のスケール設定の概要を示します。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 780ef5c2-3bd6-4261-8540-6dee77041387
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 165e4394d21a0202a320a8697360b715517bc7a2
ms.lasthandoff: 03/15/2017


---
# <a name="scaling-media-processing-overview"></a>メディア処理のスケール設定の概要
このページでは、メディア処理のスケールを設定する方法と、スケール設定を行う理由の概要について説明します。 

## <a name="overview"></a>概要
Media Services アカウントは、メディア処理タスクを処理する速度を決定する予約ユニットの種類に関連付けられます。 予約ユニットの種類は、**S1**、**S2**、**S3** から選択できます。 たとえば、同じエンコード ジョブの場合に、予約ユニットの種類として **S1** よりも **S2** を使用する方が、ジョブの実行が高速になります。 詳細については、 [予約ユニットの種類](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/)に関するページを参照してください。

予約ユニットの種類を指定するだけでなく、予約ユニットを使用したアカウントのプロビジョニングを指定することもできます。 プロビジョニングされた予約ユニットの数によって、所定のアカウントで並列処理できるメディア タスクの数が決まります。 たとえば、アカウントの予約ユニットの数が 5 である場合、処理するタスクがある限り、5 個のメディア タスクが並列実行されます。 残りのタスクはキューで待機して、実行中のタスクが完了するとキューから取り出されて順番に処理されます。 アカウントに予約ユニットが用意されていない場合、タスクは逐次処理されます。 この場合、あるタスクが終了した後、次のタスクが開始するまでの待機時間は、システムのリソースが利用できるかどうかに左右されます。

## <a name="choosing-between-different-reserved-unit-types"></a>さまざまな予約ユニットの種類の選択
次の表は、さまざまなエンコーディングの速度を選択して決定するときに役立ちます。 ベンチマーク ケースや SAS URL も記載されています。これらの SAS URL を使用してビデオをダウンロードして、独自のテストを実行できます。

| シナリオ | **S1** | **S2** | **S3** |
| --- | --- | --- | --- |
| 目的のユース ケース |シングル ビットレート エンコード。 <br/>解像度が SD 以下であり、時間的制約がなく、低コストなファイル。 |シングル ビットレート エンコードと複数ビットレート エンコード。<br/>通常は SD と HD 両方のエンコードに使用されます。 |シングル ビットレート エンコードと複数ビットレート エンコード。<br/>解像度がフル HD および 4K であるビデオ。 時間に依存しない高速ターンアラウンド エンコード。 |
| ベンチマーク |[入力ファイル: 640x360p を 29.97 フレーム/秒で 5 分間](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)。<br/><br/>同じ解像度でシングル ビットレート MP4 ファイルにエンコードするには約 11 分かかります。 |[入力ファイル: 1280x720p を 29.97 フレーム/秒で 5 分間](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>"H264 Single Bitrate 720p" プリセットでのエンコードは約 5 分かかります。<br/><br/>"H264 Multiple Bitrate 720p" プリセットでのエンコードは約 11 分半かかります。 |[入力ファイル: 1920x1080p を 29.97 フレーム/秒で 5 分間](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)。 <br/><br/>"H264 Single Bitrate 1080p" プリセットでのエンコードは約 2.7 分かかります。<br/><br/>"H264 Multiple Bitrate 1080p" プリセットでのエンコードは約 5.7 分かかります。 |

## <a name="considerations"></a>考慮事項
> [!IMPORTANT]
> このセクションで説明する考慮事項を確認してください。  
> 
> 

* 予約ユニットは、Azure Media Indexer を使用するインデックス作成ジョブを含む、すべてのメディア処理を並列化するために動作します。  ただし、エンコードとは異なり、インデックス作成ジョブでは高速予約ユニットを使用した高速処理は行われません。
* 共有プールを使用する (すなわち、予約ユニットを使用しない) 場合のエンコード タスクのパフォーマンスは S1 予約ユニットを使用したときと同等になります。 ただし、タスクがキューに登録された状態である時間の上限はなく、同時に実行されるタスクの数は最大で 1 つのみです。
* ブラジル南部とインド西部のデータ センターでは、予約ユニットの種類 **S2** は提供されません。
* インド西部のデータ センターでは、予約ユニットの種類 **S3** は提供されません。

## <a name="billing"></a>課金

請求はメディア占有ユニットを実際に使用した時間 (分) に基づいて行われます。 詳しくは、[Media Services の価格詳細](https://azure.microsoft.com/pricing/details/media-services/)ページの FAQ のセクションをご覧ください。   

## <a name="quotas-and-limitations"></a>クォータと制限
クォータと制限の詳細、サポート チケットを開く方法については、「 [Quotas and limitations (クォータと制限)](media-services-quotas-and-limitations.md)」をご覧ください

## <a name="next-step"></a>次のステップ
以下のいずれかのテクノロジを利用して、メディア処理のスケール設定タスクを実現します。 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [ポータル](media-services-portal-scale-media-processing.md)
> * [REST ()](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


