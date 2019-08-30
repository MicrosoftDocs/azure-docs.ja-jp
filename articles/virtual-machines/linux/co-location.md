---
title: Linux Azure VM を併置する | Microsoft Docs
description: Azure VM リソースを併置することで待機時間を短縮する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: cynthn
ms.openlocfilehash: 81b9dc5059a6ab3e8245acd9c7e7ef8be5abdafd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083867"
---
# <a name="co-locate-resources-for-improved-latency"></a>リソースの併置による待機時間の短縮

Azure にアプリケーションをデプロイするときに、複数のリージョンまたは可用性ゾーンにインスタンスを分散すると、ネットワーク待機時間が発生し、アプリケーションの全体的なパフォーマンスに影響を及ぼす可能性があります。 

## <a name="preview-proximity-placement-groups"></a>更新:近接通信配置グループ

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>次の手順

Azure CLI を使用して[近接通信配置グループ](proximity-placement-groups.md)に VM をデプロイします。

