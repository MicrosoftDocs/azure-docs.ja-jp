---
title: メディア占有ユニット - Azure
description: メディア占有ユニットを使用すると、メディア プロセスをスケーリングし、メディア処理タスクの速度を決定することができます。
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
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: e0cfc47b546c31e6b93d224e64de20b79baacfd9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98891298"
---
# <a name="media-reserved-units"></a>メディア占有ユニット

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services を使用してメディア占有ユニット (MRU) を管理することで、実行されるメディア処理をスケーリングできます。 MRU には、メディアのエンコードに必要な追加のコンピューティング容量が用意されています。 MRU の数によって、メディア タスクの処理速度と、アカウントで同時に処理できるメディア タスク数が決まります。 たとえば、アカウントに 5 つの MRU があり、処理するタスクがある場合、5 つのメディア タスクを同時に実行できます。 残りのタスクはすべてキューに格納され、実行中のタスクが完了したときに、順番に処理する対象として選択できます。 プロビジョニングした MRU ごとに容量は予約されますが、専用のリソースは提供されません。 需要が非常に高い時間帯には、すべての MRU の処理がすぐに開始されない場合があります。

タスクとは、アダプティブ ストリーミング エンコードなど、資産に対する作業の個別の操作です。 ジョブを送信すると、Media Services によってジョブは個々の操作 (つまり、タスク) に分割され、個別の MRU に関連付けられます。

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

実行中のジョブがあるかどうかに関係なく、アカウントでメディア占有ユニットがプロビジョニングされた時間 (分単位) に基づいて課金されます。 詳しくは、[Media Services の価格詳細](https://azure.microsoft.com/pricing/details/media-services/)ページの FAQ のセクションをご覧ください。

## <a name="next-step"></a>次のステップ
[CLI を使用してメディア占有ユニットをスケーリングする](media-reserved-units-cli-how-to.md)
[ビデオを分析する](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>関連項目

* [クォータと制限](limits-quotas-constraints.md)
