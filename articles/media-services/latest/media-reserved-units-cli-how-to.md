---
title: CLI を使用してメディア占有ユニットをスケーリングする - Azure | Microsoft Docs
description: このトピックでは、CLI を使用して、Azure Media Services でのメディア処理をスケーリングする方法について説明します。
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
ms.custom: seodec18
ms.openlocfilehash: 13fa733417558ab8be9ff1e5a9f1e484fb40f445
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102945"
---
# <a name="scaling-media-processing"></a>メディア処理のスケーリング

Azure Media Services を使用してメディア占有ユニット (MRU) を管理することで、お使いのアカウントで実行されるメディア処理をスケーリングできます。 MRU によって、メディア処理タスクが処理される速度が決定されます。 予約ユニットの種類は、以下から選択できます:**S1**、**S2**、**S3**。 たとえば、同じエンコード ジョブの場合に、予約ユニットの種類として **S1** よりも **S2** を使用する方が、ジョブの実行が高速になります。 

予約ユニットの種類を指定するだけでなく、予約ユニットを使用したアカウントのプロビジョニングを指定することもできます。 プロビジョニングされた予約ユニットの数によって、所定のアカウントで並列処理できるメディア タスクの数が決まります。 たとえば、アカウントの予約ユニットの数が 5 である場合、処理するタスクがある限り、5 個のメディア タスクが並列実行されます。 残りのタスクはキューで待機して、実行中のタスクが完了するとキューから取り出されて順番に処理されます。 アカウントに予約ユニットが用意されていない場合、タスクは逐次処理されます。 この場合、あるタスクが終了した後、次のタスクが開始するまでの待機時間は、システムのリソースが利用できるかどうかに左右されます。

## <a name="choosing-between-different-reserved-unit-types"></a>さまざまな予約ユニットの種類の選択

次の表は、さまざまなエンコーディングの速度を選択して決定するときに役立ちます。 いくつかのベンチマーク ケースも用意されています[ダウンロード可能なビデオ](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)独自のテストを実行します。

|RU の種類|シナリオ|[7 分間の 1080p ビデオ](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)に関するサンプル結果|
|---|---|---|
| **S1**|シングル ビットレート エンコード。 <br/>解像度が SD 以下であり、時間的制約がなく、低コストなファイル。|"H264 Single Bitrate SD 16x9" を使用したシングル ビットレート SD 解像度 MP4 ファイルへのエンコードには約 7 分かかります。|
| **S2**|シングル ビットレート エンコードと複数ビットレート エンコード。<br/>通常は SD と HD 両方のエンコードに使用されます。|"H264 Single Bitrate 720p" プリセットでのエンコードには約 6 分かかります。<br/><br/>"H264 Single Bitrate 720p" プリセットでのエンコードには約 12 分かかります。|
| **S3**|シングル ビットレート エンコードと複数ビットレート エンコード。<br/>解像度がフル HD および 4K であるビデオ。 時間に依存しない高速ターンアラウンド エンコード。|"H264 Single Bitrate 1080p" プリセットでのエンコードは約 3 分かかります。<br/><br/>"H264 Multiple Bitrate 1080p" プリセットでのエンコードは約 8 分かかります。|

## <a name="considerations"></a>考慮事項

* Media Services v3 または Video Indexer によってトリガーされるオーディオ分析およびビデオ分析ジョブでは、S3 のユニットの種類を強くお勧めします。
* 共有プールを使用する (すなわち、予約ユニットを使用しない) 場合のエンコード タスクのパフォーマンスは S1 予約ユニットを使用したときと同等になります。 ただし、タスクがキューに登録された状態である時間の上限はなく、同時に実行されるタスクの数は最大で 1 つのみです。

この記事の残りでは、[Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) を使用して MRU をスケーリングする方法を示します。

> [!NOTE]
> Media Services v3 または Video Indexer によってトリガーされる音声分析と動画分析ジョブでは、お使いのアカウントを 10 個の S3 の MRU でプロビジョニングすることを強くお勧めします。 10 個を超える S3 の MRU が必要な場合は、[Azure portal](https://portal.azure.com/) を使用してサポート チケットを開いてください。
>
> 現在、Azure Portal を使用して他の v3 リソースを管理することはできません。 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref)、またはサポートされている [SDK](media-services-apis-overview.md#sdks) のいずれかを使用してください。

## <a name="prerequisites"></a>前提条件 

[Media Services アカウントを作成する](create-account-cli-how-to.md)

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>CLI を使用してメディア占有ユニットをスケーリングする

`mru` コマンドを実行します。

次の [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) コマンドでは、"amsaccount" アカウントのメディア占有ユニットを、**count** パラメーターと **type** パラメーターを使用して設定しています。

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>課金

アカウントにメディア占有ユニットがプロビジョニングされた時間 (分) に基づいて課金されます。 課金は、アカウントで実行中のジョブがあるかどうかとは無関係に発生します。 詳しくは、[Media Services の価格詳細](https://azure.microsoft.com/pricing/details/media-services/)ページの FAQ のセクションをご覧ください。   

## <a name="next-step"></a>次のステップ

[ビデオを分析する](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>関連項目

* [クォータと制限](limits-quotas-constraints.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
