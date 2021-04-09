---
title: Azure Firewall サービス タグの概要
description: サービス タグは IP アドレス プレフィックスのグループを表し、セキュリティ規則の作成の複雑さを最小限に抑えるのに役立ちます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 83e9a96573bbc72e0afff61cc0f151f95b081e30
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97031581"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall サービス タグ

サービス タグは IP アドレス プレフィックスのグループを表し、セキュリティ規則の作成の複雑さを最小限に抑えるのに役立ちます。 独自のサービス タグを作成したり、タグに含まれる IP アドレスを指定したりすることはできません。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

Azure Firewall サービス タグは、ネットワーク ルールの宛先フィールドで使用できます。 特定の IP アドレスの代わりにそれらを使用できます。

## <a name="supported-service-tags"></a>サポートされるサービス タグ

Azure ファイアウォール ネットワーク ルール内で使用できるサービス タグの一覧については、「[仮想ネットワーク サービス タグ](../virtual-network/service-tags-overview.md#available-service-tags)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Firewall ルールの詳細については、「[Azure Firewall ルール処理ロジック](rule-processing.md)」を参照してください。
