---
title: Azure portal を利用して Azure マネージド ディスクのパフォーマンスを変更する
description: Azure portal を利用し、新規と既存のマネージド ディスクのパフォーマンス レベルを変更する方法について説明します。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/05/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 625fb1e3dd0b433da6b60f995aa6b380c23ec9ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97901035"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Azure portal を利用してパフォーマンス レベルを変更する

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>制限

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>作業の開始

### <a name="new-disks"></a>新しいディスク

次の手順は、ディスクを最初に作成するとき、ディスクのパフォーマンス レベルを変更する方法を示しています。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. 新しいディスクを作成する VM に移動します。
1. 新しいディスクを選択するとき、まず、必要なディスクのサイズを選択します。
1. サイズを選択したら、別のパフォーマンス レベルを選択し、そのパフォーマンスを変更します。
1. **[OK]** を選択してディスクを作成します。

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="ディスク作成ブレードのスクリーンショット。1 つのディスクが強調表示されています。パフォーマンス レベルのドロップダウンが強調表示されています。" lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>既存のディスク

次の手順では、既存のディスクのパフォーマンス レベルを変更する方法を説明しています。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. 変更するディスクが含まれる VM に移動します。
1. VM の割り当てを解除するか、ディスクをデタッチします。
1. ディスクを選択します
1. **[Size + Performance]\(サイズおよびパフォーマンス\)** を選択します。
1. **[パフォーマンス レベル]** ドロップダウンで、ディスクの現在のパフォーマンス レベル以外のレベルを選択します。
1. **[サイズ変更]** を選択します。

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="[Size + Performance]\(サイズおよびパフォーマンス\) ブレードのスクリーンショット。パフォーマンス レベルが強調表示されています。" lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>次の手順

より高いパフォーマンス レベルを利用できるようにディスクのサイズを変更する必要がある場合は、次の記事を参照してください。

- [Azure CLI を使用して Linux VM の仮想ハード ディスクを拡張する](linux/expand-disks.md)
- [Windows 仮想マシンにアタッチされたマネージド ディスクを拡張する](windows/expand-os-disk.md)
