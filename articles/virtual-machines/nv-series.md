---
title: NV シリーズ - Azure Virtual Machines
description: NV シリーズ VM の仕様。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 6dd912313176eecce1973849875234149700c5e6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284780"
---
# <a name="nv-series"></a>NV シリーズ

NV シリーズの仮想マシンは、[NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU およびデスクトップ アクセラレータ アプリケーションや仮想デスクトップ向けの NVIDIA GRID テクノロジを備えていて、お客様は、データやシミュレーションを視覚化することができます。 NV インスタンスでは、グラフィックス処理を要するワークフローを視覚化して優れたグラフィックス機能を活用し、さらにエンコードやレンダリングなどの単精度のワークロードを実行することもできます。 NV シリーズ VM は、Intel Xeon E5-2690 v3 (Haswell) CPU も搭載しています。

NV インスタンスの GPU ごとに GRID ライセンスが付属します。 このライセンスでは柔軟性が確保され、NV インスタンスを仮想ワークステーションとして 1 人のユーザーに対して使用したり、仮想アプリケーションのシナリオで 25 人のユーザーが同時に VM に接続したりできます。

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

ライブ マイグレーション:サポートされていません

メモリ保持更新: サポートされていません

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | 最大 NIC 数 | 仮想ワークステーション | 仮想アプリケーション |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = M60 カードの 2 分の 1 相当。

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
