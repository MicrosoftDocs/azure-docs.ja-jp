---
title: Video Indexer と Azure Media Services v3 プリセットの比較 | Microsoft Docs
description: このトピックでは、Video Indexer と Azure Media Services v3 プリセットを比較します。
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
ms.date: 04/07/2019
ms.author: juliako
ms.openlocfilehash: 2c98f6d12f4868e5f90874fe3210fe5368f7ca2d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270338"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Azure Media Services v3 プリセットと Video Indexer の比較 

この記事では、**Video Indexer API** と **Media Services v3 API** の機能を比較します。 

現在、[Video Indexer API](https://api-portal.videoindexer.ai/) と [Media Services v3 API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json) によって提供される機能には重複があります。 次の表に、違う点と似ている点を理解するための最新ガイドラインを示します。 

## <a name="compare"></a>比較

|機能|Video Indexer API |ビデオ アナライザーとオーディオ アナライザーのプリセット<br/>(Media Services v3 API)|
|---|---|---|
|メディア分析情報|[拡張](video-indexer-output-json-v2.md) |[基礎](../latest/intelligence-concept.md)|
|エクスペリエンス|サポートされている機能の完全な一覧を参照: <br/> [概要](video-indexer-overview.md)|ビデオの分析情報のみを返す|
|課金|[Media Services の価格](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Media Services の価格](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|コンプライアンス|[ISO 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001)、[ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018)、[SOC 1,2,3](https://www.microsoft.com/TrustCenter/Compliance/SOC)、[HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa)、[FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp)、[PCI](https://www.microsoft.com/trustcenter/compliance/pci)、[HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) 認定済み。 最新の更新プログラムについては、[現在の Video Indexer の認定資格の状態](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)に関するページにアクセスしてください。|Media Services は多くの認証に準拠しています。 [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) を入手し、"Media Services" を検索して、関心がある認証に準拠しているかどうかを確認します。|
|無料試用版|米国東部|使用できません。|
|利用可能なリージョン|米国東部 2、米国中南部、米国西部 2、北ヨーロッパ、西ヨーロッパ、東南アジア、東アジア、オーストラリア東部。  最新の更新プログラムについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)」を参照してください。|[Azure の状態](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)を参照。|

## <a name="next-steps"></a>次の手順

[Video Indexer の概要](video-indexer-overview.md)

[Media Services v3 の概要](../latest/media-services-overview.md)
