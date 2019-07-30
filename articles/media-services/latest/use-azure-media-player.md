---
title: Azure Media Player での再生 - Azure | Microsoft Docs
description: このトピックでは、Azure Media Player の概要を説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/10/2018
ms.author: juliako
ms.openlocfilehash: 2b828ee10580b5ec0415cd5f364b6250a6498093
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853663"
---
# <a name="azure-media-player-overview"></a>Azure Media Player の概要

Azure Media Player は、さまざまなブラウザーやデバイス上で Microsoft Azure Media Services からメディア コンテンツを再生できる組み込みの Web ビデオ プレーヤーです。 Azure Media Player では、HTML5、Media Source Extensions (MSE)、Encrypted Media Extensions (EME) といった業界標準を使用して、強化されたアダプティブ ストリーミングを提供します。 デバイスやブラウザーでこれらの標準を使用できない場合、Azure Media Player は Flash や Silverlight をフォールバック テクノロジとして使用します。 使用する再生テクノロジにかかわらず、開発者は統一された JavaScript インターフェイスを使用して API にアクセスできます。 これにより、Azure Media Services で提供されるコンテンツを、さまざまなデバイスやブラウザーで追加作業なしで再生できるようになります。

Microsoft Azure Media Services では、HLS、DASH、スムーズ ストリーミングのストリーミング形式でコンテンツを提供して再生できます。 Azure Media Player はこれらの多用な形式を考慮し、プラットフォームやブラウザーの性能に基づいて最適なリンクを自動的に再生します。 また、Media Services では PlayReady 暗号化や AES-128 ビットエンベロープ暗号化を使用した資産の動的暗号化も可能です。 Azure Media Player では、コンテンツが適切に構成されていれば、PlayReady 暗号化や AES-128 ビットエンベロープ暗号化されたコンテンツの暗号化を解除できます。 

[無料試用版の利用を開始](https://azure.microsoft.com/pricing/free-trial/)

## <a name="use-azure-media-player-demo-page"></a>Azure Media Player のデモページを使用する

### <a name="start-using"></a>使用を開始する

[Azure Media Player のデモ ページ](https://aka.ms/azuremediaplayer)を使用して、Azure Media Services のサンプルや独自のストリームを再生することができます。  

新しいビデオを再生するには、別の URL を貼り付けて **[Update]\(更新する)** を押します。

さまざまな再生オプション (テクノロジ、言語、暗号化など) を構成するには、 **[Advanced Options]\(詳細オプション)** を押します。

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>ビデオ ストリームの診断を監視する

[Azure Media Player のデモ ページ](https://aka.ms/azuremediaplayer)を使用して、ビデオ ストリームの診断を監視します。 

![Azure Media Player の診断](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Azure Media Player を HTML で設定する

Azure Media Player は簡単にセットアップできます。 Media Services アカウントからメディア コンテンツの基本再生を取得するのにいくらか時間がかかるだけです。 Azure Media Player を設定および構成する方法の詳細については、[Azure Media Player のドキュメント](https://aka.ms/ampdocs)を参照してください。 

## <a name="next-steps"></a>次の手順

- [Azure Media Player のドキュメント](https://aka.ms/ampdocs)
- [Azure Media Player のサンプル](https://aka.ms/ampsamples)
