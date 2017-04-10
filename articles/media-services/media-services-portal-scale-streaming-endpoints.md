---
title: "Azure Portal を使用したストリーミング エンドポイントのスケール設定 | Microsoft Docs"
description: "このチュートリアルでは、Azure ポータルを使用したストリーミング エンドポイントのスケール設定の手順について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: 9bed9392502dae01724c4ca86b8c735ab60a2882
ms.lasthandoff: 01/11/2017


---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Azure ポータルを使用したストリーミング エンドポイントのスケール設定
## <a name="overview"></a>概要

> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 
> 
> 

このトピックは、**Premium** タイプの**ストリーミング エンドポイント**を使用するお客様を対象としています。 ストリーミング エンドポイントの種類と CDN 構成について詳しくは、「[ストリーミング エンドポイントの概要](media-services-portal-manage-streaming-endpoints.md)」トピックをご覧ください。
 
**Premium** タイプを使用する場合、既定では 1 つのストリーミング ユニット (SU) が提供されます。 ストリーミング エンドポイントを拡張する必要がある場合は、このトピックの手順に従ってください。

料金設定の詳細については、「 [Azure 料金早見表](http://go.microsoft.com/fwlink/?LinkId=275107)」を参照してください。

## <a name="scale-streaming-endpoints"></a>ストリーミング エンドポイントのスケール設定

ストリーミング ユニットを作成したり、数を変更したりするには、以下の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]** ウィンドウで **[ストリーミング エンドポイント]** を選択します。
3. 次に、スケールを設定するストリーミング エンドポイントをクリックします。 
4. スライダーを移動してストリーミング ユニットの数を指定します。

![ストリーミング エンドポイント](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


