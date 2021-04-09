---
title: Azure Media Services でエンコードの速度とコンカレンシーを管理する | Microsoft Docs
description: この記事では、Azure Media Services でエンコード ジョブやエンコード タスクの速度とコンカレンシーを管理する方法について簡単に説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: f01235e5b02bf2a61a359fa5571c6e6d575c1232
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014810"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>エンコードの速度とコンカレンシーの管理

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

この記事では、エンコード ジョブやエンコード タスクの速度とコンカレンシーを管理する方法について簡単に説明します。

## <a name="overview"></a>概要

Media Services では、メディア処理タスクの処理速度が **予約ユニットの種類** によって決まります。 予約ユニットの種類は、以下から選択できます:**S1**、**S2**、**S3**。 たとえば、同じエンコード ジョブの場合に、予約ユニットの種類として **S1** よりも **S2** を使用する方が、ジョブの実行が高速になります。 どのエンコード速度を使用するかを判断する際は、[エンコード ユニットのスケール設定](media-services-scale-media-processing-overview.md)のトピックに記載されている表が参考になります。

**予約ユニット** の種類を指定するだけでなく、予約ユニットを使用したアカウントのプロビジョニングを指定することもできます。 プロビジョニングされた予約ユニットの数によって、所定のアカウントで並列処理できるメディア タスクの数が決まります。 たとえば、アカウントの予約ユニットの数が 5 である場合、処理するタスクがある限り、5 個のメディア タスクが並列実行されます。 残りのタスクはキューで待機して、実行中のタスクが完了するとキューから取り出されて順番に処理されます。 アカウントに予約ユニットが用意されていない場合、タスクは逐次処理されます。 この場合、あるタスクが終了した後、次のタスクが開始するまでの待機時間は、システムのリソースが利用できるかどうかに左右されます。

エンコード ユニットのスケール方法についての詳しい情報や例については、[こちら](media-services-scale-media-processing-overview.md)のトピックを参照してください。

## <a name="next-step"></a>次のステップ

[エンコード ユニットのスケール設定](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

