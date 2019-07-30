---
title: Azure Firewall サービス タグの概要
description: この記事は、Azure Firewall サービス タグの概要です。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 6/27/2019
ms.author: victorh
ms.openlocfilehash: d0ac36e415c056dffc9c75d00968ff74c2156e63
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450164"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall サービス タグ

サービス タグは IP アドレス プレフィックスのグループを表し、セキュリティ規則の作成の複雑さを最小限に抑えるのに役立ちます。 独自のサービス タグを作成したり、タグに含まれる IP アドレスを指定したりすることはできません。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

Azure Firewall サービス タグは、ネットワーク ルールの宛先フィールドで使用できます。 特定の IP アドレスの代わりにそれらを使用できます。

## <a name="supported-service-tags"></a>サポートされるサービス タグ

Azure ファイアウォール ネットワーク ルール内で使用できるサービス タグの一覧については、「[セキュリティ グループ](../virtual-network/security-overview.md#service-tags)」を参照してください。

## <a name="next-steps"></a>次の手順

Azure Firewall ルールの詳細については、「[Azure Firewall ルール処理ロジック](rule-processing.md)」を参照してください。