---
title: Linux VM を併置する
description: Azure VM リソースを併置することで待機時間を短縮する方法について説明します。
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 000c8c8955314060ae9bdedb96b086a509fb3e96
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87368644"
---
# <a name="co-locate-resources-for-improved-latency"></a>リソースの併置による待機時間の短縮

Azure にアプリケーションをデプロイするときに、複数のリージョンまたは可用性ゾーンにインスタンスを分散すると、ネットワーク待機時間が発生し、アプリケーションの全体的なパフォーマンスに影響を及ぼす可能性があります。 

## <a name="proximity-placement-groups"></a>近接通信配置グループ

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>次のステップ

Azure CLI を使用して[近接通信配置グループ](proximity-placement-groups.md)に VM をデプロイします。

[ネットワーク待機時間をテストする](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)方法について学びます。

[ネットワーク スループットを最適化する](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)方法について学びます。  

[SAP アプリケーションで近接通信配置グループを使用する](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)方法について学びます。
