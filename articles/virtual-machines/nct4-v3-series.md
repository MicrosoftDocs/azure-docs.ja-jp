---
title: NCas T4 v3 シリーズ
description: NCas T4 v3 シリーズ VM の仕様。
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: vikancha
ms.openlocfilehash: d07da12ecef7dfc6cf1a6df67f6beae01c4573d9
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168118"
---
# <a name="ncast4_v3-series"></a>NCasT4_v3 シリーズ 

NCasT4_v3 シリーズ仮想マシンは、[Nvidia Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU と AMD EPYC 7V12(Rome) CPU を搭載しています。 この VM には、最大 4 個の NVIDIA T4 GPU (それぞれに 16 GB のメモリを装備)、最大 64 個の非マルチスレッド AMD EPYC 7V12(Rome) プロセッサ コア、440 GiB のシステム メモリが搭載されています。 これらの仮想マシンは、CUDA、TensorFlow、Pytorch、Caffe などのフレームワークを使用した ML と AI のワークロード、または NVIDIA GRID テクノロジを使用したグラフィックス ワークロードを実行するのに最適です。 NCasT4_v3 シリーズは、推論ワークロードの実行に最適です。

<br>

ACU: 230-260

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

ライブ マイグレーション: サポートされていません

メモリ保持更新: サポートされていません

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 |
| Standard_NV8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4  |
| Standard_NV16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8  |
| Standard_NV64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

Windows または Linux を実行している Azure NCasT4_v3 シリーズの GPU 機能を利用するには、Nvidia GPU ドライバーがインストールされている必要があります。

Nvidia GPU ドライバーを手動でインストールする場合、サポートされるオペレーティング システム、ドライバー、インストール、および検証手順について、[Windows 用 N シリーズ GPU ドライバーのセットアップ](./windows/n-series-driver-setup.md)に関する記事を参照してください。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
