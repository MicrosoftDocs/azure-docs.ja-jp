---
title: NC シリーズ - Azure Virtual Machines
description: NC シリーズ VM の仕様。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: bd62d7f7c04896a8a62ab1290ddbc9370934d333
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286242"
---
# <a name="nc-series"></a>NC シリーズ

NC シリーズ VM は、[NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) カードおよび Intel Xeon E5-2690 v3 (Haswell) プロセッサを搭載しています。 エネルギー調査アプリケーション向け CUDA やクラッシュ シミュレーション、レイ トレーシング レンダリング、ディープ ラーニングなどを活用することで、ユーザーはデータをさらに高速に処理することができます。 NC24r 構成には、密結合並列コンピューティングのワークロード向けに最適化された、低待機時間かつ高スループットのネットワーク インターフェイスが搭載されています。

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

ライブ マイグレーション:サポートされていません

メモリ保持更新: サポートされていません

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | 最大 NIC 数 |
|---|---|---|---|---|---|---|---|
| Standard_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standard_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standard_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = K80 カードの 2 分の 1 相当。

*RDMA 対応

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

Azure N シリーズ VM の GPU 機能を利用するには、NVIDIA GPU ドライバーをインストールする必要があります。

[NVIDIA GPU ドライバー拡張機能](./extensions/hpccompute-gpu-windows.md)は、N シリーズ VM 上に適切な NVIDIA CUDA または GRID ドライバーをインストールします。 この拡張機能は、Azure Portal または Azure PowerShell や Azure Resource Manager テンプレートなどのツールを使用してインストールまたは管理します。 サポートされるオペレーティング システムおよびデプロイ手順については、[NVIDIA GPU ドライバー拡張機能のドキュメント](./extensions/hpccompute-gpu-windows.md)を参照してください。 VM 拡張機能の一般情報については、「[Azure 仮想マシンの拡張機能と機能](./extensions/overview.md)」をご覧ください。

NVIDIA GPU ドライバーを手動でインストールすることを選択した場合、サポートされるオペレーティング システム、ドライバー、インストール、および検証の手順については、[Windows 用 N シリーズ GPU ドライバーのセットアップ](./windows/n-series-driver-setup.md)または [Linux 用 N シリーズ GPU ドライバーのセットアップ](./linux/n-series-driver-setup.md)に関する記事を参照してください。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
