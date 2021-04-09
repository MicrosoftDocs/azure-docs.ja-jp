---
title: DC シリーズ - Azure Virtual Machines
description: DC シリーズ VM の仕様。
author: susaxen
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: 677f4df0873f8b72d40dd373035111e2e0002491
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549232"
---
# <a name="dcsv2-series"></a>DCsv2 シリーズ


DCsv2 シリーズは、パブリック クラウドで処理中のデータおよびコードの機密性と整合性を保護するために役立ちます。 これらのマシンは、最新世代の Intel XEON E-2288G プロセッサと SGX テクノロジによって支援されています。 Intel Turbo Boost Technology により、これらのマシンは最大 5.0 GHz まで高速化できます。 DCsv2 シリーズのインスタンスを使用すると、安全なエンクレーブ ベースのアプリケーションを構築して、コードやデータを使用中に保護することができます。

使用例としては、機密のマルチパーティ データ共有、不正行為の検出、マネー ロンダリング対策、ブロックチェーン、機密の利用状況分析、インテリジェンス分析、機密の機械学習などがあります。

[Premium Storage](premium-storage-performance.md): サポートされています *<br>
[Premium Storage Caching](premium-storage-performance.md):サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md):サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md):サポートされていません<br>
[VM 世代サポート](generation-2.md):世代 2<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています ("* 4 vCPU 以上が必要*") <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>

*Standard_DC8_v2 を除く <br>

| サイズ             | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps (キャッシュ サイズは GiB 単位) | 最大 NIC 数/想定ネットワーク帯域幅 (MBps) | EPC メモリ (MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2,000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4,000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8,000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16,000/128                                                                                         | 2   | 168                                         |

- DCsv2 シリーズの VM は、[第 2 世代の VM](./generation-2.md#creating-a-generation-2-vm) であり、`Gen2` イメージのみをサポートしています。
- 現在は、[こちら](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)の一覧にあるリージョンで提供されています。
- 前世代の機密コンピューティング VM:[DC シリーズ](sizes-previous-gen.md#preview-dc-series)
- [Azure portal](./linux/quick-create-portal.md) または [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) を使用して DCsv2 VM を作成します



## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

料金計算ツール: [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

ディスクの種類の詳細情報: [ディスクの種類](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
