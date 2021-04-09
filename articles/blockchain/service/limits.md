---
title: Azure Blockchain Service の制限事項
description: Azure Blockchain Service でのサービスと機能の制限に関する概要です
ms.date: 04/02/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 71e1bebf10fa0142870d03977182472da1ad031f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80676529"
---
# <a name="limits-in-azure-blockchain-service"></a>Azure Blockchain Service での制限事項

Azure Blockchain Service のサービスと機能には、メンバーが持つことのできるノードの数、コンソーシアムの制限、ストレージの量などの制限事項があります。

## <a name="pricing-tier"></a>Pricing tier

トランザクションと検証ノードの数の上限は、Azure Blockchain Service を Basic または Standard のどちらの価格レベルでプロビジョニングするかによって異なります。

| Pricing tier | トランザクション ノードの最大数 | 検証ノードの最大数 |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

コンソーシアム ネットワークには、少なくとも 2 つの Azure Blockchain Service の Standard レベル ノードが必要です。 Standard レベル ノードには、2 つの検証ノードがあります。 [Istanbul Byzantine Fault Tolerance コンセンサス](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)を満たすには、4 つの検証ノードが必要です。

開発、テスト、概念実証には、Basic レベルを使用します。 運用グレードのデプロイには、Standard レベルを使用します。 また、Blockchain Data Manager を使用している場合や大量のプライベート トランザクションを送信する場合にも、*Standard* レベルを使用する必要があります。

メンバーの作成後に価格レベルを Basic と Standard の間で変更することはできません。

## <a name="storage-capacity"></a>ストレージの容量

台帳データおよびログ用にノードごとに使用できるストレージの最大量は、1.8 テラバイトです。

台帳およびログのストレージ サイズを減らすことはできません。
## <a name="consortium-limits"></a>コンソーシアムの制限

* Azure Blockchain Service では **コンソーシアム名とメンバー名は一意でなければなりません**。

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

## <a name="performance"></a>パフォーマンス

各トランザクションの送信には、*eth.estimate* gas 関数を使用しないでください。 *eth.estimate* 関数は大量のメモリを使用します。 この関数を複数回呼び出すと、1 秒あたりのトランザクションが大幅に削減されます。

可能であれば、トランザクションの送信には控えめな gas 値を使用し、*eth.estimate* の使用を最小限に抑えてください。

## <a name="next-steps"></a>次のステップ

システムのパッチ適用とアップグレードに関するポリシーについて学習する - [修正プログラムの適用、更新、およびバージョン](ledger-versions.md)。
