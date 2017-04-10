---
title: "パートナーを使用して Azure Media Services に Widevine ライセンスを配信する | Microsoft Docs"
description: "この記事では、PlayReady と Widevine DRM の両方を使用して AMS で動的に暗号化されたストリームを、Azure Media Services (AMS) を使用して配信する方法について説明します。 PlayReady ライセンスは Media Services PlayReady サーバーから取得し、Widevine ライセンスは castLabs ライセンス サーバーから取得します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 5bcad5a4-c0bb-4871-9cce-808a913c53e6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b4211cef3b4f3ffa2c0c97fd7650606f3eef7008


---
# <a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>パートナーを使用して Azure Media Services に Widevine ライセンスを配信する
## <a name="overview"></a>Overview
Microsoft Azure Media Services を使用すると、Widevine DRM で保護された MPEG-DASH を配信できます。Widevine DRM は、共通暗号化 (CENC) 仕様に従って暗号化されています。

Media Services .NET SDK バージョン 3.5.2 以降、Media Services を使用すると Widevine ライセンス テンプレートを構成し、Widevine ライセンスを取得できます。 [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)、[EZDRM](http://ezdrm.com/)、[castLabs](http://castlabs.com/company/partners/azure/) の各 AMS パートナーを使用して、Widevine ライセンスを提供することもできます。

## <a name="castlabs"></a>castLabs
[castLabs](http://castlabs.com/company/partners/azure/) を使用して Widevine ライセンスを配信できます。 詳細については、「 [castLabs を使用して Azure Media Services に DRM ライセンスを配信する](media-services-castlabs-integration.md)

## <a name="axinom"></a>Axinom
[Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) を使用して Widevine ライセンスを配信できます。 詳細については、「 [Axinom を使用して Azure Media Services に DRM ライセンスを配信する](media-services-axinom-integration.md)

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>関連項目
[PlayReady または Widevine の動的共通暗号化を使用する](media-services-protect-with-drm.md)

[Mingfei のブログ](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)




<!--HONumber=Nov16_HO3-->


