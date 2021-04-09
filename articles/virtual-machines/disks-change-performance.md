---
title: Azure マネージド ディスクのパフォーマンス レベル
description: マネージド ディスクのパフォーマンス レベルについて説明します。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: af1509073248b46575881beef7b9800107e7fed7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677310"
---
# <a name="performance-tiers-for-managed-disks"></a>マネージド ディスクのパフォーマンス レベル

Azure マネージド ディスクのパフォーマンスは、ディスクの作成時にパフォーマンス レベルの形式で設定されます。 パフォーマンス レベルによって、マネージド ディスクの IOPS とスループットが決まります。 ディスクのプロビジョニング済みのサイズを設定すると、パフォーマンス レベルが自動的に選択されます。 パフォーマンス レベルは、デプロイ時またはその後に、ディスクのサイズを変更せずに変更することができます。

パフォーマンス レベルを変更すると、ディスクのバースト機能を使用せずに、より高い需要に備え、対応することができます。 追加のパフォーマンスが必要な期間の長さによっては、バーストに依存するのではなく、パフォーマンス レベルを変更する方がコスト効率が高い場合があります。 これは、休日のショッピング、パフォーマンス テスト、トレーニング環境の実行など、一貫して高いレベルのパフォーマンスを一時的に必要とするイベントに最適です。 これらのイベントを処理するために、必要に応じて、より高いパフォーマンス レベルを使用できます。 その後、より高いパフォーマンスが必要でなくなったときに、元のレベルに戻すことができます。

## <a name="how-it-works"></a>しくみ

初めてディスクを配置またはプロビジョニングすると、プロビジョニングされたディスク サイズに基づいてそのディスクのベースライン パフォーマンス レベルが設定されます。 元のベースラインより高いパフォーマンス レベルを使用して、より高い需要を満たすことができます。 そのパフォーマンス レベルが必要でなくなった場合は、最初のベースライン パフォーマンス レベルに戻すことができます。

パフォーマンス レベルの変化に応じて、課金が変動します。 たとえば、P10 ディスク (128 GiB) をプロビジョニングした場合、ベースライン パフォーマンス レベルは P10 (500 IOPS、100 MBps) と設定されます。 P10 の料金で課金されます。 ディスク サイズを増やすことなく、P50 のパフォーマンス (7,500 IOPS、250 MBps) に適合するようにレベルをアップグレードできます。 アップグレードされている期間は、P50 の料金で課金されます。 より高いパフォーマンスが必要でなくなった場合は、P10 レベルに戻すことができます。 ディスクは、再び P10 の料金で課金されます。

| ディスク サイズ | ベースライン パフォーマンス レベル | アップグレードの範囲 |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2、P3、P4、P6、P10、P15、P20、P30、P40、P50 |
| 8 GiB | P2 | P3、P4、P6、P10、P15、P20、P30、P40、P50 |
| 16 GiB | P3 | P4、P6、P10、P15、P20、P30、P40、P50 | 
| 32 GiB | P4 | P6、P10、P15、P20、P30、P40、P50 |
| 64 GiB | P6 | P10、P15、P20、P30、P40、P50 |
| 128 GiB | P10 | P15、P20、P30、P40、P50 |
| 256 GiB | P15 | P20、P30、P40、P50 |
| 512 GiB | P20 | P30、P40、P50 |
| 1 TiB | P30 | P40、P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | なし |
| 8 TiB | P60 |  P70、P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | なし |

課金情報については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)」を参照してください。

## <a name="restrictions"></a>制限

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>次のステップ

パフォーマンス レベルを変更する方法については、[portal](disks-performance-tiers-portal.md) または [PowerShell/CLI ](disks-performance-tiers.md)に関する記事を参照してください。

