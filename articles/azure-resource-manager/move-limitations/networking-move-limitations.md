---
title: Azure ネットワーク リソースを新しいサブスクリプションまたはリソース グループに移動する | Microsoft Docs
description: Azure Resource Manager を使用して、仮想ネットワークおよびその他のネットワーク リソースを、新しいリソース グループまたはサブスクリプションに移動します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 6ad7f32704d8cb73999a6e3cf60cb2a238268242
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034545"
---
# <a name="move-guidance-for-networking-resources"></a>ネットワーク リソースの移動ガイダンス

この記事では、特定のシナリオで仮想ネットワークとその他のネットワーク リソースを移動する方法について説明します。

## <a name="dependent-resources"></a>依存リソース

仮想ネットワークを移動するときは、その依存リソースも移動する必要があります。 VPN ゲートウェイでは、IP アドレス、仮想ネットワーク ゲートウェイ、および関連付けられているすべての接続リソースを移動する必要があります。 各ローカル ネットワーク ゲートウェイは、異なるリソース グループ内に配置することができます。

ネットワーク インターフェイス カードで仮想マシンを移動するには、すべての依存リソースを移動する必要があります。 ネットワーク インターフェイス カードに対応した仮想ネットワーク、仮想ネットワークに対応した他のすべてのネットワーク インターフェイス カード、および VPN ゲートウェイを移動します。

## <a name="peered-virtual-network"></a>ピアリングされた仮想ネットワーク

ピアリングされた仮想ネットワークを移動するには、最初に仮想ネットワークのピアリングを無効にする必要があります。 無効にすると、仮想ネットワークを移動できます。 移動後に、仮想ネットワークのピアリングを再度有効にします。

## <a name="subnet-links"></a>サブネット リンク

仮想ネットワークにリソース ナビゲーション リンクのあるサブネットが含まれる場合、仮想ネットワークを別のサブスクリプションに移動することはできません。 たとえば、Azure Cache for Redis リソースがサブネットにデプロイされている場合、そのサブネットにはリソース ナビゲーション リンクがあります。

## <a name="next-steps"></a>次の手順

リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../resource-group-move-resources.md)」を参照してください。
