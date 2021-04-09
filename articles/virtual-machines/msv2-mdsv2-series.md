---
title: Msv2 シリーズ (プレビュー) - Azure Virtual Machines
description: Msv2 シリーズ VM の仕様。
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: a7f4757467523837423d52998eb6b8204090e627
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562573"
---
# <a name="msv2-and-mdsv2-series-medium-memory-preview"></a>Msv2 および Mdsv2 シリーズのミディアム メモリ (プレビュー)


> [!IMPORTANT]
> プレビューに参加するには、 **https://aka.ms/Mv2MedMemoryPreview** でフォームに入力してください。  

Msv2 および Mdsv2 ミディアム メモリ VM シリーズには、全コア ベース周波数 2.7 GHz、シングル コア ターボ周波数 4.0 GHz の、Intel® Xeon® Platinum 8280 (Cascade Lake) プロセッサが搭載されています。 これらの VM により、ローカル ディスクありとディスクなしのオプションで柔軟性が向上します。 また、最大で 192 の vCPU、4 TiB のメモリにまで拡張できる、より多くの CPU とメモリを備えた、新しい一連の分離された VM サイズにアクセスできます。 


Msv2 および Mdsv2 シリーズの VM は第 2 世代のみであり、第 2 世代でサポートされているイメージのサブセットがサポートされます。 Msv2 および Mdsv2 シリーズでサポートされているイメージの完全な一覧については、以下を参照してください。  

- Windows Server 2019 以降
- SUSE Linux Enterprise Server 12 SP4 以降または SUSE Linux Enterprise Server 15 SP1 以降
- Red Hat Enterprise Linux 7.6、7.7、8.1 以降 
- Oracle Enterprise Linux 7.7 以降

第 2 世代仮想マシンの詳細については、「[Azure での第 2 世代 VM のサポート](./generation-2.md)」を参照してください。



[Premium Storage](premium-storage-performance.md):サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
[VM 世代サポート](generation-2.md): 第 2 世代<br>
[書き込みアクセラレータ](./how-to-enable-write-accelerator.md): サポートされています<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 ミディアム メモリ (ディスクなし) 

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数 | 必要なネットワーク帯域幅 (Mbps) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192is_v2 | 192 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192ims_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Mdsv2 ミディアム メモリ (ディスクあり)  

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループット: IOPS/MBps | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数 | 必要なネットワーク帯域幅 (Mbps) | 
|---|---|---|---|---|---|---|---|---|
| Standard_M32dms_v2 | 32 | 875 | 1024 | 32 | 40000/400 | 20000/500 | 8 | 8000 | 
| Standard_M64ds_v2 | 64 | 1024 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M64dms_v2 | 64 | 1792 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M128ds_v2 | 128 | 2048 | 4096 | 64 |160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M128dms_v2 | 128 | 3892 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192ids_v2 | 192 | 2048 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192idms_v2 | 192 | 4096 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

料金計算ツール: [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
