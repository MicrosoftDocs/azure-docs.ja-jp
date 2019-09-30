---
title: Azure Blockchain の制限事項
description: Azure Blockchain Service でのサービスと機能の制限に関する概要です
services: azure-blockchain
keywords: ブロックチェーン
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: aeed84f19da3843d043eafef9d7444661901c53e
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147139"
---
# <a name="limits-in-azure-blockchain-service"></a>Azure Blockchain Service での制限事項

Azure Blockchain Service のサービスと機能には、メンバーが持つことのできるノードの数、コンソーシアムの制限、ストレージの量などの制限事項があります。

## <a name="pricing-tier"></a>Pricing tier

トランザクションと検証ノードの数の上限は、Azure Blockchain Service を Basic または Standard のどちらの価格レベルでプロビジョニングするかによって異なります。

| Pricing tier | トランザクション ノードの最大数 | 検証ノードの最大数 |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

メンバーの作成後に価格レベルを Basic と Standard の間で変更することはできません。

## <a name="storage-capacity"></a>ストレージの容量

台帳データおよびログ用にノードごとに使用できるストレージの最大量は、1.8 テラバイトです。

台帳およびログのストレージ サイズを減らすことはできません。

## <a name="consortium-limits"></a>コンソーシアムの制限

* Azure Blockchain Service では**コンソーシアム名とメンバー名は一意でなければなりません**。

* **メンバー名とコンソーシアム名は変更できません**

* **1 つのコンソーシアム内のすべてのメンバーは、同じ価格レベルである必要があります**

* **1 つのコンソーシアムに参加するすべてのメンバーは、同じリージョン内に存在する必要があります**

    コンソーシアムで最初のメンバーを作成するときに、リージョンを指定します。 コンソーシアムに招待されるメンバーは、最初のメンバーと同じリージョン内に存在する必要があります。 すべてのメンバーを同じリージョンに制限することにより、ネットワーク コンセンサスへの悪影響がありません。

* **コンソーシアムには少なくとも 1 人の管理者が必要です**

    コンソーシアムの管理者が 1 人だけの場合、別の管理者が追加されるか、コンソーシアムでレベル上げされるまで、管理者は自分自身やメンバーをコンソーシアムから削除することはできません。

* **コンソーシアムから削除されたメンバーを再び追加することはできません**

    代わりに、コンソーシアムへの参加を再度招待し、新しいメンバーを作成する必要があります。 トランザクションの履歴を保持するため、既存メンバーのリソースは削除されません。

* **1 つのコンソーシアム内のすべてのメンバーは、同じ台帳バージョンを使用する必要があります**

    Azure Blockchain Service で使用できる修正プログラムの適用、更新、および台帳バージョンについて詳しくは、「[Patching, updates, and versions (修正プログラムの適用、更新、およびバージョン)](ledger-versions.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

* [修正プログラムの適用、更新、およびバージョン](ledger-versions.md)
