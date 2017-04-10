---
title: "エンコード ユニットの追加によるメディア処理のスケール調整 - Azure | Microsoft Docs"
description: ".NET を使用して、エンコード ユニットを追加する方法を説明します。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: juliako;milangada;
translationtype: Human Translation
ms.sourcegitcommit: bdf41edfa6260749a91bc52ec0a2b62fcae99fb0
ms.openlocfilehash: ee40b92c48d91a391fe72582c7ea5e244907747f


---
# <a name="how-to-scale-encoding-with-net-sdk"></a>.NET SDK を使用してエンコードを拡張する方法
> [!div class="op_single_selector"]
> * [ポータル](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
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

.NET SDK を使用して予約ユニットの種類とエンコード予約ユニットの数を変更するには、以下の手順に従います。

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>サポート チケットを開く
既定で、各 Media Services アカウントは最大 25 個のエンコードと 5 個のオンデマンド ストリーミング予約ユニットを設定できます。 サポート チケットを開くと、上限の拡大を要求できます。

### <a name="open-a-support-ticket"></a>サポート チケットを開く
サポート チケットを開くには、以下の手順を実行します。

1. [[サポートの要求]](https://manage.windowsazure.com/?getsupport=true)をクリックします。 ログインしていない場合は、資格情報を入力するように求められます。
2. サブスクリプションを選択します。
3. サポートの種類として [技術] を選択します。
4. [チケットの作成] をクリックします。
5. 次のページに示される製品一覧で [Azure Media Services] を選択します。
6. 問題に適した「問題の種類」を選択します。
7. [続行] をクリックして続行します。
8. 次のページの指示に従って、問題に関する詳細を入力します。
9. [送信] をクリックして、チケットを開きます。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO4-->


