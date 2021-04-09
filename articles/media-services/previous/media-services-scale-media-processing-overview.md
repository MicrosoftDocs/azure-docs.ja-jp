---
title: メディア占有ユニットの概要 | Microsoft Docs
description: この記事では、Azure Media Services を使用したメディア処理のスケール設定の概要を示します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: d0692996c27f969ffc90078db2ddcc849ee15ab1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012719"
---
# <a name="media-reserved-units"></a>メディア占有ユニット

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services を使用してメディア占有ユニット (MRU) を管理することで、実行されるメディア処理をスケーリングできます。 MRU には、メディアのエンコードに必要な追加のコンピューティング容量が用意されています。 MRU の数によって、メディア タスクの処理速度と、アカウントで同時に処理できるメディア タスク数が決まります。 たとえば、アカウントに 5 つの MRU があり、処理するタスクがある場合、5 つのメディア タスクを同時に実行できます。 残りのタスクはすべてキューに格納され、実行中のタスクが完了したときに、順番に処理する対象として選択できます。 プロビジョニングした MRU ごとに容量は予約されますが、専用のリソースは提供されません。 需要が非常に高い時間帯には、すべての MRU の処理がすぐに開始されない場合があります。

## <a name="choosing-between-different-reserved-unit-types"></a>さまざまな予約ユニットの種類の選択

次の表は、さまざまなエンコーディングの速度を選択して決定するときに役立ちます。  使用する MRU に応じて、7 分間、1080p のビデオのエンコード期間が表示されます。

|RU の種類|シナリオ|7 分間の 1080p ビデオに関するサンプル結果 |
|---|---|---|
| **S1**|シングル ビットレート エンコード。 <br/>解像度が SD 以下であり、時間的制約がなく、低コストなファイル。|"H264 Single Bitrate SD 16x9" を使用したシングル ビットレート SD 解像度 MP4 ファイルへのエンコードには約 7 分かかります。|
| **S2**|シングル ビットレート エンコードと複数ビットレート エンコード。<br/>通常は SD と HD 両方のエンコードに使用されます。|"H264 Single Bitrate 720p" プリセットでのエンコードには約 6 分かかります。<br/><br/>"H264 Single Bitrate 720p" プリセットでのエンコードには約 12 分かかります。|
| **S3**|シングル ビットレート エンコードと複数ビットレート エンコード。<br/>解像度がフル HD および 4K であるビデオ。 時間に依存しない高速ターンアラウンド エンコード。|"H264 Single Bitrate 1080p" プリセットでのエンコードは約 3 分かかります。<br/><br/>"H264 Multiple Bitrate 1080p" プリセットでのエンコードは約 8 分かかります。|

> [!NOTE]
> アカウントに対して MRU をプロビジョニングしない場合、メディア タスクは S1 MRU のパフォーマンスで処理され、タスクは順番に取得されます。 処理容量は予約されていないため、あるタスクが終了してから次のタスクが開始するまでの待機時間は、システム内のリソースの可用性によって変わります。

## <a name="considerations"></a>考慮事項

* Media Services v3 または Video Indexer によってトリガーされる音声分析とビデオ分析ジョブの場合は、アカウントに 10 個の S3 ユニットをプロビジョニングすることを強くお勧めします。 10 個を超える S3 の MRU が必要な場合は、[Azure portal](https://portal.azure.com/) を使用してサポート チケットを開いてください。
* MRU のないエンコード タスクの場合、タスクがキューに格納された状態で費やすことができる時間に上限はなく、一度に 1 つのタスクしか実行されません。

## <a name="billing"></a>課金

アカウントにメディア占有ユニットがプロビジョニングされた時間 (分) に基づいて課金されます。 課金は、アカウントで実行中のジョブがあるかどうかとは無関係に発生します。 詳しくは、[Media Services の価格詳細](https://azure.microsoft.com/pricing/details/media-services/)ページの FAQ のセクションをご覧ください。

## <a name="quotas-and-limitations"></a>クォータと制限

クォータと制限の詳細、サポート チケットを開く方法については、「 [Quotas and limitations (クォータと制限)](media-services-quotas-and-limitations.md)」をご覧ください

## <a name="next-steps"></a>次のステップ

以下のいずれかのテクノロジを利用して、メディア処理のスケール設定を試行します。

[.NET](media-services-dotnet-encoding-units.md)
[Portal](media-services-portal-scale-media-processing.md)
[REST](/rest/api/media/operations/encodingreservedunittype)
[Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
[PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)