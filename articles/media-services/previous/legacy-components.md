---
title: Azure Media Services のレガシ コンポーネント | Microsoft Docs
description: このトピックでは、Azure Media Services のレガシ コンポーネントについて説明します。
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
ms.date: 09/26/2019
ms.author: juliako
ms.openlocfilehash: e4ff157f58a68c68f8610c6c473f5d69897650ad
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338410"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services のレガシ コンポーネント

Media Services のコンポーネントは、時間の経過と共に着実に改善され機能が強化されてきました。 その結果、いくつかのレガシ コンポーネントは廃止されています。 次の記事では、レガシ コンポーネントから最新のコンポーネントにアプリケーションを移行する手順を確認できます。

## <a name="legacy-components-and-migration-guidance"></a>レガシ コンポーネントと移行のガイダンス

お知らせしているように *Windows Azure Media Encoder* (WAME) と *Azure Media Encoder* (AME) のメディア プロセッサは非推奨となっており、 2019 年 11 月 30 日には廃止される予定です。

* [Windows Azure Media Encoder から Media Encoder Standard に移行する](migrate-windows-azure-media-encoder.md)
* [Azure Media Encoder から Media Encoder Standard に移行する](migrate-azure-media-encoder.md)

また、*Azure Media Indexer v1* および "*Azure Media Indexer v2 プレビュー*" についても廃止を発表します。 [Azure Media Indexer v1](media-services-index-content.md) メディア プロセッサは、2020 年 10 月 1 日に廃止されます。 [Azure Media Indexer v2 プレビュー](media-services-process-content-with-indexer2.md) メディア プロセッサは、2019 年 1 月 1 日に廃止されます。  [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) が、これらの従来のメディア プロセッサに取って代わります。

* [Azure Media Indexer v1 と Azure Media Indexer v2 から Azure Media Services Video Indexer に移行する](migrate-indexer-v1-v2.md)。

## <a name="next-steps"></a>次の手順

[Media Services v2 から v3 への移行のガイダンス](../latest/migrate-from-v2-to-v3.md)
