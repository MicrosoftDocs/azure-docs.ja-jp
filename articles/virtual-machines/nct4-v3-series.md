---
title: NCas T4 v3 シリーズ
description: NCas T4 v3 シリーズ VM の仕様。
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
author: vikancha-MSFT
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: vikancha
ms.openlocfilehash: d73bd81f15263c79e16b574eb961d4ae0ac61175
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417809"
---
# <a name="ncast4_v3-series"></a>NCasT4_v3 シリーズ 

NCasT4_v3 シリーズ仮想マシンは、[Nvidia Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU と AMD EPYC 7V12(Rome) CPU を搭載しています。 この VM には、最大 4 個の NVIDIA T4 GPU (それぞれに 16 GB のメモリを装備)、最大 64 個の非マルチスレッド AMD EPYC 7V12 (Rome) プロセッサ コア、440 GiB のシステム メモリが搭載されています。 これらの仮想マシンは、AI サービス (ユーザーが生成した要求のリアルタイム推論など) のデプロイや、NVIDIA の GRID ドライバーと仮想 GPU テクノロジを使用した対話型グラフィックスと視覚化ワークロードに最適です。 CUDA、TensorRT、Caffe、ONNX、およびその他のフレームワークをベースにした標準的な GPU コンピューティング ワークロードや、OpenGL と DirectX をベースにした GPU アクセラレーションを使用するグラフィック アプリケーションは、ユーザーのすぐ近くにある NCasT4_v3 シリーズに経済的にデプロイできます。

<br>

[ACU](acu.md):230-260<br>
[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
Nvidia NVLink Interconnect:サポートされています<br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 / 8000 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4/8,000  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8/8,000  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8 / 32000  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

Windows または Linux を実行している Azure NCasT4_v3 シリーズの GPU 機能を利用するには、Nvidia GPU ドライバーがインストールされている必要があります。

Nvidia GPU ドライバーを手動でインストールする場合、サポートされるオペレーティング システム、ドライバー、インストール、および検証手順について、[Windows 用 N シリーズ GPU ドライバーのセットアップ](./windows/n-series-driver-setup.md)に関する記事を参照してください。

Azure Nvidia GPU ドライバー拡張機能は、CUDA ドライバーを NCasT4_v3 シリーズ VM にデプロイします。 グラフィックスと視覚化のワークロードについては、Azure でサポートされている GRID ドライバーを手動でインストールしてください。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
