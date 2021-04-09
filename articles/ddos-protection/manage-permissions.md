---
title: Azure DDoS Protection プランのアクセス許可
description: 保護計画のアクセス許可を管理する方法について説明します。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: df53062c7c897493a47d88ea2873f9710b9825bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806258"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>DDoS Protection プランの管理: アクセス許可と制限

DDoS Protection プランは、複数のリージョンおよびサブスクリプション間で動作します。 同じプランを、自分のテナントを越えて、異なるリージョンの他のサブスクリプションの仮想ネットワークにリンクできます。 プランが関連付けられているサブスクリプションでは、プランに対する月額料金が請求されるほか、保護されているパブリック IP アドレスの数が 100 を超える場合は超過料金が請求されます。 DDoS 価格について詳しくは、[価格の詳細](https://azure.microsoft.com/pricing/details/ddos-protection/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

- このチュートリアルの手順を実行する前に、まず [Azure の DDoS 標準保護プラン](manage-ddos-protection.md)を作成する必要があります。

## <a name="permissions"></a>アクセス許可

DDoS 保護プランに関する作業を行うには、使用するアカウントが[ネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または次の表の適切なアクションが割り当てられた[カスタム](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ロールに、割り当てられている必要があります。

| アクション                                            | 名前                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | DDoS 保護プランを読み取る              |
| Microsoft.Network/ddosProtectionPlans/write       | DDoS 保護プランを作成または更新する  |
| Microsoft.Network/ddosProtectionPlans/delete      | DDoS 保護プランを削除する            |
| Microsoft.Network/ddosProtectionPlans/join/action | DDoS 保護プランを結合する              |

仮想ネットワークに対する DDoS 保護を有効にするには、使うアカウントに[仮想ネットワークの適切なアクション](../virtual-network/manage-virtual-network.md#permissions)も割り当てられている必要があります。

## <a name="azure-policy"></a>Azure Policy

ほとんどの組織では、複数のプランを作成する必要はありません。 プランをサブスクリプション間で移行することはできません。 プランが関連付けられているサブスクリプションを変更する必要がある場合は、既存のプランを削除し、新しいプランを作成する必要があります。

さまざまなサブスクリプションをお持ちで、コスト管理のために単一プランがテナント全体にデプロイされるようにしたいお客様は、Azure Policy を使用して [Azure DDoS Protection Standard プランの作成を制限](https://aka.ms/ddosrestrictplan)することができます。 サブスクリプションが以前に例外としてマークされていない限り、このポリシーにより、あらゆる DDoS プランの作成がブロックされます。 DDoS プランがデプロイされているがデプロイすべきではなく、コンプライアンス違反としてマークされているすべてのサブスクリプションの一覧もこのポリシーによって表示されます。


## <a name="next-steps"></a>次のステップ

DDoS 保護プラン用にテレメトリを表示および構成する方法を学習するには、チュートリアルに進んでください。

> [!div class="nextstepaction"]
> [DDoS 保護テレメトリの表示と構成](telemetry.md)
