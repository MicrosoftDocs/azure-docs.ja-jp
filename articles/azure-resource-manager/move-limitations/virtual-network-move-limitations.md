---
title: Azure Virtual Network リソースを新しいサブスクリプションまたはリソース グループに移動する | Microsoft Docs
description: Azure Resource Manager を使用して、新しいリソース グループまたはサブスクリプションに仮想ネットワークを移動します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 809d403a463048910a284e7f8fcdc18cf7c65b69
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723351"
---
# <a name="move-guidance-for-virtual-networks"></a>仮想ネットワークの移動に関するガイダンス

この記事では、特定のシナリオにおいて仮想ネットワークを移動する方法を示します。

## <a name="dependent-resources"></a>依存リソース

仮想ネットワークを移動するときは、その依存リソースも移動する必要があります。 VPN ゲートウェイでは、IP アドレス、仮想ネットワーク ゲートウェイ、および関連付けられているすべての接続リソースを移動する必要があります。 各ローカル ネットワーク ゲートウェイは、異なるリソース グループ内に配置することができます。

ネットワーク インターフェイス カードで仮想マシンを移動するには、すべての依存リソースを移動する必要があります。 ネットワーク インターフェイス カードに対応した仮想ネットワーク、仮想ネットワークに対応した他のすべてのネットワーク インターフェイス カード、および VPN ゲートウェイを移動します。

## <a name="peered-virtual-network"></a>ピアリングされた仮想ネットワーク

ピアリングされた仮想ネットワークを移動するには、最初に仮想ネットワークのピアリングを無効にする必要があります。 無効にすると、仮想ネットワークを移動できます。 移動後に、仮想ネットワークのピアリングを再度有効にします。

## <a name="subnet-links"></a>サブネット リンク

仮想ネットワークにリソース ナビゲーション リンクのあるサブネットが含まれる場合、仮想ネットワークを別のサブスクリプションに移動することはできません。 たとえば、Azure Cache for Redis リソースがサブネットにデプロイされている場合、そのサブネットにはリソース ナビゲーション リンクがあります。

## <a name="next-steps"></a>次の手順

リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../resource-group-move-resources.md)」を参照してください。
