---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c0627dd0833e3b20468eb5f50fbeb9fd9d9ae2b3
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864649"
---
**送信データ転送**: [送信データ転送](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure データ センターから送信されるデータ) では、帯域幅の使用量に対して課金されます。

**トランザクション**:Standard マネージド ディスクで実行されるトランザクションの数に対して課金されます。 Standard SSD では、スループットが 256 KiB 以下の I/O 操作は、それぞれ単一の I/O 操作とみなされます。 スループットが 256 KiB を超える I/O 操作は、サイズが 256 KiB の複数の I/O とみなされます。 Standard HDD では、各 IO 操作は、I/O サイズに関係なく、単一のトランザクションとみなされます。

トランザクション コストを含め、Managed Disks の価格の詳細については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks)」をご覧ください。

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra Disk VM 予約料金

Azure VM には、Ultra Disk と互換性があるかどうかを示す機能があります。 Ultra ディスク互換 VM では、コンピューティング VM インスタンスとブロック ストレージ スケール ユニット間に専用の帯域幅キャパシティが割り当てられてパフォーマンスが最適化され、待機時間が減少します。 このキャパシティを VM に追加すると、Ultra ディスクがアタッチされていない VM 上で Ultra ディスク キャパシティを有効にした場合にのみ、予約料金がかかります。 Ultra ディスク互換の VM に Ultra ディスクがアタッチされている場合、この料金は適用されません。 この料金は、VM 上にプロビジョニングされている vCPU ごとにかかります。 

> [!Note]
> [制約付きコア VM サイズ](../articles/virtual-machines/constrained-vcpu.md)の場合、予約料金は、制限付きコアではなく実際の vCPU 数に基づいています。 Standard_E32-8s_v3 の場合、予約料金は 32 コアに基づきます。 

Ultra Disk の価格について詳しくは、[Azure ディスクの価格](https://azure.microsoft.com/pricing/details/managed-disks/)に関するページをご覧ください。

### <a name="azure-disk-reservation"></a>Azure ディスク予約

ディスク予約は、1 年分のディスク ストレージを割引価格で事前に購入して、総コストを削減するオプションです。 ディスク予約を購入する場合は、ターゲット リージョンの特定のディスク SKU を選択します。たとえば、1 年間の米国東部 2 リージョンの 10 P30 (1TiB) プレミアム SSD を選択します。 予約エクスペリエンスは、予約済みの仮想マシン (VM) インスタンスに似ています。 VM 予約とディスク予約をバンドルすると、最大限に節約できます。 現時点で、Azure ディスク予約には、すべての運用リージョンで P30 (1 TiB) から P80 (32 TiB) までの Premium SSD SKU の 1 年間のコミットメント プランが用意されています。 予約済みディスクの価格の詳細については、[Azure ディスクの価格のページ](https://azure.microsoft.com/pricing/details/managed-disks/)を参照してください。