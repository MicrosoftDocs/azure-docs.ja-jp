---
title: 既存のプレーヤーを使用したコンテンツの再生 - Azure | Microsoft Docs
description: この記事では、コンテンツの再生に使用できる既存のプレーヤーを示します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: bd09b734f1ac5ac3c98b6c0c717a48de19b0106f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89261072"
---
# <a name="playing-your-content-with-existing-players"></a>既存のプレーヤーによるコンテンツの再生

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services は、スムーズ ストリーミング、HTTP ライブ ストリーミング、Mpeg-dash など、人気のある多くのストリーミング形式をサポートします。 このトピックでは、ストリームのテストに使用できる既存のプレーヤーを示します。

### <a name="the-azure-portal-media-services-content-player"></a>Azure Portal Media Services コンテンツ プレーヤー
**Azure** ポータルには、ビデオのテストに使用できるコンテンツ プレーヤーが用意されています。

目的のビデオをクリックし ( [発行済](media-services-portal-publish.md)であることを確認します)、ポータルの下部の **[再生]** ボタンをクリックします。

いくつかの考慮事項が適用されます。

* **Media Services コンテンツ プレーヤー**を既定のストリーミング エンドポイントから再生します。 既定以外のストリーミング エンドポイントから再生する場合は、別のプレーヤーを使用します たとえば、[Azure Media Player](https://aka.ms/azuremediaplayer) を使用します。

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player

[Azure Media Player](https://aka.ms/azuremediaplayer) を使用して、次のいずれかの形式でコンテンツ (平文のコンテンツまたは保護されたコンテンツ) を再生します。

* スムーズ ストリーミング
* MPEG DASH
* HLS
* Progressive MP4

### <a name="flash-player"></a>Flash Player

#### <a name="playready-with-token"></a>トークンを使用した PlayReady

[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>DASH Players

[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>その他
次を使用して HLS URL をテストすることもできます。

* **Safari** (iOS デバイス) または
* **3ivx HLS Player** (Windows)

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
