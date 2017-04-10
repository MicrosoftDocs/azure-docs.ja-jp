---
title: " Azure Portal を使用したメディア処理のスケール設定 | Microsoft Docs"
description: "このチュートリアルでは、Azure ポータルを使用したメディア処理のスケール設定の手順について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: 3f9134fdff56e0ee5146cb7ccf08d168ebf84281


---
# <a name="change-the-reserved-unit-type"></a>予約ユニットの種類の変更
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [ポータル](media-services-portal-scale-media-processing.md)
> * [REST ()](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>概要
> [!IMPORTANT]
> メディア処理のスケール設定の詳細については、 [概要](media-services-scale-media-processing-overview.md) に関するトピックを必ず確認してください。
> 
> 

## <a name="scale-media-processing"></a>メディア処理のスケール設定
予約ユニットの種類と予約ユニットの数を変更するには、以下の手順に従います。

1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]** ウィンドウで **[メディア占有ユニット]** をクリックします。
   
    選択した予約ユニットの種類の予約ユニット数を変更するには、 **[Media Serverd Units (メディア予約ユニット)]** スライダーを使用します。
   
    **[占有ユニットの種類]**を変更するには、[S1]、[S2]、または [S3] をクリックします。
   
    ![[プロセッサ] ページ](./media/media-services-portal-scale-media-processing/media-services-scale-media-processing.png)
3. [保存] ボタンを押して、変更を保存します。
   
    [保存] をクリックすると、新しい予約ユニットが割り当てられます。

## <a name="next-steps"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


