---
title: Azure NetApp Files のサービス レベル | Microsoft Docs
description: Azure NetApp Files のサービス レベルのスループット パフォーマンスについて説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: c2086eb0c5529d8e570a545e35fc716f70c7541f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668346"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp Files のサービス レベル
Azure NetApp Files では、次の 2 つのサービス レベルがサポートされています: Premium と Standard。 

## <a name="Premium"></a>Premium Storage

*Premium* Storage では、TiB あたり最大 64 MiB/秒のスループットが提供されます。 スループットのパフォーマンスは、ボリュームのクォータと連動します。 たとえば、Premium Storage のボリュームでプロビジョニング済みクォータが 2 TiB である場合 (実際の消費量に関係なく)、スループットは 128 MiB/秒になります。

## <a name="Standard"></a>Standard Storage

*Standard* Storage では、TiB あたり最大 16 MiB/秒のスループットが提供されます。 スループットのパフォーマンスは、ボリュームのクォータと連動します。 たとえば、Standard Storage のボリュームでプロビジョニング済みクォータが 2 TiB である場合 (実際の消費量に関係なく)、スループットは 32 MiB/秒になります。

## <a name="next-steps"></a>次の手順

- さまざまなサービス レベルの価格については、[Azure NetApp Files の価格ページ](https://azure.microsoft.com/pricing/details/storage/netapp/)を参照してください
- [容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)
