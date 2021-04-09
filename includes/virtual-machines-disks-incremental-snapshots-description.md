---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 39750a86ccf781a10109e299e27a55a03173acb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98901024"
---
増分スナップショットは、マネージド ディスクの特定の時点のバックアップであり、取得時に、最後のスナップショット以降の変更のみで構成されます。 増分スナップショットからディスクを復元する場合、増分スナップショットの取得時に、システムによってディスクの特定の時点のバックアップを表す完全なディスクが再構築されます。 マネージド ディスク スナップショットに追加されたこの新しい機能は、コスト効率の向上に役立つ可能性があります。ユーザーが選択しない限り、スナップショットを取得するたびにディスク全体を保存する必要がなくなるからです。 増分スナップショットは、フル スナップショットと同様、完全なマネージド ディスクまたはフル スナップショットのいずれを作成する場合にも使用できます。

増分スナップショットとフル スナップショットには、いくつかの違いがあります。 増分スナップショットでは、ディスクのストレージの種類に関係なく、常に Standard HDD ストレージが使用されますが、フル スナップショットでは Premium SSD を使用できます。 VM のデプロイをスケールアップするために Premium Storage でフル スナップショットを使用している場合は、[Shared Image Gallery](../articles/virtual-machines/shared-image-galleries.md) の Standard Storage でカスタム イメージを使用することをお勧めします。 これは、より低コストでさらに大規模なスケールを実現するのに役立ちます。 さらに、増分スナップショットは、[ゾーン冗長ストレージ](../articles/storage/common/storage-redundancy.md) (ZRS) を使用すると、信頼性が向上する可能性があります。 選択したリージョンで ZRS が使用可能な場合、増分スナップショットは ZRS を自動的に使用します。 そのリージョンで ZRS が使用できない場合、スナップショットは既定で[ローカル冗長ストレージ](../articles/storage/common/storage-redundancy.md) (LRS) に設定されます。 この動作をオーバーライドして手動で選択することもできますが、これはお勧めしません。

増分スナップショットには、マネージド ディスクでのみ使用できる差分機能も用意されています。 これにより、同じマネージド ディスクの 2 つの増分スナップショット間の変更内容を、ブロック レベルまで取得できます。 スナップショットを複数のリージョンにわたってコピーするとき、この機能を使用してデータ フットプリントを削減できます。  たとえば、最初の増分スナップショットは、別のリージョンのベース BLOB としてダウンロードできます。 以降の増分スナップショットについては、前回のスナップショット以降の変更分のみをベース BLOB にコピーできます。 変更をコピーした後は、別のリージョンにあるディスクのポイントインタイム バックアップを表すスナップショットを、ベース BLOB で取得できます。 ディスクの復元は、ベース BLOB から行うこともでき、別のリージョンにあるベース BLOB のスナップショットから行うこともできます。

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="複数のリージョンにコピーされた増分スナップショットを示す図。スナップショットでは、各スナップショットに対応したページ BLOB が最終的に形成されるまで、さまざまな API 呼び出しが行われます。":::

増分スナップショットは、使用されたサイズに対してのみ課金されます。 スナップショットの使用サイズは、[Azure 利用状況レポート](../articles/cost-management-billing/understand/review-individual-bill.md)で見つけることができます。 たとえば、スナップショットの使用データ サイズが 10 GiB の場合、**毎日** の利用状況レポートには、使用量として 10 GiB/(31 日) = 0.3226 が表示されます。