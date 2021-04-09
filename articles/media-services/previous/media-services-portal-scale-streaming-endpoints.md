---
title: Azure Portal を使用したストリーミング エンドポイントのスケール設定 | Microsoft Docs
description: このチュートリアルでは、Azure ポータルを使用したストリーミング エンドポイントのスケール設定の手順について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 4db53d64131e6fb43ce425cf579d79e62775754a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009755"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Azure ポータルを使用したストリーミング エンドポイントのスケール設定

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>概要

> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)」を参照してください。 
> 
> 

**Premium** ストリーミング エンドポイントは専用のスケーラブルな帯域幅の容量を提供するため、高度なワークロードに適しています。 **Premium** ストリーミング エンドポイントを持つユーザーは、既定で 1 つのストリーミング ユニット (SU) を取得します。 ストリーミング エンドポイントは、SU を追加することで拡張できます。 各 SU は、アプリケーションに追加の帯域幅の容量を提供します。 ストリーミング エンドポイントの種類と CDN 構成について詳しくは、「[ストリーミング エンドポイントの概要](media-services-streaming-endpoints-overview.md)」トピックをご覧ください。
 
このトピックでは、ストリーミング エンドポイントを拡大縮小する方法を示します。

料金設定の詳細については、「 [Azure 料金早見表](https://azure.microsoft.com/pricing/details/media-services/)」を参照してください。

## <a name="scale-streaming-endpoints"></a>ストリーミング エンドポイントのスケール設定

ストリーミング ユニットの数を変更するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]** ウィンドウで **[ストリーミング エンドポイント]** を選択します。
3. 次に、スケールを設定するストリーミング エンドポイントをクリックします。 

    > [!NOTE] 
    > スケールできるのは **Premium** ストリーミング エンドポイントだけです。

4. スライダーを移動してストリーミング ユニットの数を指定します。

    ![ストリーミング エンドポイント](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

