---
title: Azure Media Services のエンコード エラー コード | Microsoft Docs
description: このトピックでは、エンコード タスクの実行中にエラーが発生した場合に返される可能性があるエラー コードの一覧を示しています。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 7e6848fb49dd63fa67a639d09754a28dd5953a32
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013484"
---
# <a name="encoding-error-codes"></a>エラー コードのエンコード

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

次の表は、エンコード タスクの実行中にエラーが発生した場合に返される可能性があるエラー コードの一覧です。  .NET コードのエラーの詳細を取得するには、 [ErrorDetails](/previous-versions/azure/jj126075(v=azure.100)) クラスを使用します。 REST コードのエラーの詳細を取得するには、 [ErrorDetails](/rest/api/media/operations/errordetail) REST API を使用します。

| ErrorDetail.Code | 考えられる原因 |
| --- | --- |
| Unknown |タスクの実行中に発生する不明なエラー |
| ErrorDownloadingInputAssetMalformedContent |無効なファイル名、サイズ 0 のファイル、不正なファイル形式など、入力資産のダウンロード中に発生するエラーのカテゴリ |
| ErrorDownloadingInputAssetServiceFailure |ダウンロード中のネットワーク エラーやストレージ エラーなど、サービス側での問題に関するエラーのカテゴリ |
| ErrorParsingConfiguration |構成が有効なシステム プリセットではない、構成に無効な XML が含まれているなど、タスク \<see cref="MediaTask.PrivateData"/> (構成) が有効でない場合に発生するエラーのカテゴリ。 |
| ErrorExecutingTaskMalformedContent |入力メディア ファイル内の問題が障害の原因となり、タスクの実行中に発生するエラーのカテゴリ |
| ErrorExecutingTaskUnsupportedFormat |メディア形式がサポートされていない、または構成と一致しないなど、提供されたファイルをメディア プロセッサが処理できない場合に発生するエラーのカテゴリ (映像のみの資産から音声のみの出力を生成しようとした場合など) |
| ErrorProcessingTask |コンテンツとは無関係に、メディア プロセッサでタスクの処理中に発生するその他のエラーのカテゴリ |
| ErrorUploadingOutputAsset |出力資産のアップロード中に発生するエラーのカテゴリ |
| ErrorCancelingTask |タスクをキャンセルしようとして失敗したときに発生するエラーのカテゴリ |
| TransientError |一時的な問題に関するエラーのカテゴリ (例: Azure Storage の一時的なネットワークの問題) |

**Media Services** チームのサポートを受けるには、 [サポート チケット](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を開いてください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>関連記事
* [Media Encoder Standard のプリセットをカスタマイズし、高度なエンコード タスクを実行する](media-services-custom-mes-presets-with-dotnet.md)
* [クォータと制限](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
