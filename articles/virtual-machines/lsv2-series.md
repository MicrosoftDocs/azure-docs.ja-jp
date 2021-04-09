---
title: Lsv2 シリーズ - Azure Virtual Machines
description: Lsv2 シリーズ VM の仕様。
author: sasha-melamed
ms.service: virtual-machines
ms.subservice: vm-sizes-storage
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 787f9ada921e1580a34431568e87c41496b37f6a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563202"
---
# <a name="lsv2-series"></a>Lsv2 シリーズ

Lsv2 シリーズは、2.55 GHz の全コア ブーストと 3.0 GHz の最大ブーストが可能な [AMD EPYC<sup>TM</sup> 7551 プロセッサ](https://www.amd.com/en/products/epyc-7000-series)上で実行され、高スループット、低待ち時間、直接マッピングされたローカル NVMe ストレージを特長としています。 Lsv2 シリーズの VM には、同時マルチスレッド構成で 8 から 80 vCPU のサイズが用意されています。  vCPU あたり 8 GiB のメモリ、8 vCPU あたり 1 つの 1.92 TB NVMe SSD M.2 デバイスが用意され、L80s v2 では最大 19.2 TB (10 x 1.92 TB) を使用できます。

> [!NOTE]
> Lsv2 シリーズの VM は、持続性のあるデータ ディスクを使用する代わりに、VM に直接接続されているノード上のローカル ディスクを使用するように最適化されています。 これにより、ユーザーのワークロードに対応するより優れた IOPS/スループットが得られます。 Lsv2 および Ls シリーズでは、持続性のあるデータ ディスクで実現できる IOPS を向上させるためのローカル キャッシュの作成はサポートされません。
>
> Lsv2 シリーズの VM は、ローカル ディスクの高スループットと IOPS によって、1 つの VM で障害が発生した場合に複数の VM にデータをレプリケートして永続性を実現する Apache Cassandra や MongoDB などの NoSQL ストアにとって最適なものになっています。
>
> 詳細については、[Windows](../virtual-machines/windows/storage-performance.md) または [Linux](../virtual-machines/linux/storage-performance.md) 用の「Lsv2 シリーズの仮想マシン上でパフォーマンスを最適化する」を参照してください。  

[ACU](acu.md):150 から 175<br>
[Premium Storage](premium-storage-performance.md):サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされていません<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
[VM 世代サポート](generation-2.md):第 1 世代と第 2 世代<br>
バースト:サポートされています<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
<br>

| サイズ | vCPU | メモリ (GiB) | 一時ディスク<sup>1</sup> (GiB) | NVMe ディスク<sup>2</sup> | NVMe ディスク スループット<sup>3</sup> (読み取り IOPS/MBps) | キャッシュ不使用時のデータ ディスク スループット (IOPs/MBps)<sup>4</sup> | キャッシュ不使用時の最大バースト データ ディスク スループット (IOPs/MBps)<sup>5</sup>| 最大データ ディスク数 | 最大 NIC 数 | 必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1 x 1.92 TB  | 400000/2000  | 8000/160   | 8000/1280 | 16 | 2 | 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2 x 1.92 TB  | 800000/4000  | 16000/320  | 16000/1280 | 32 | 4 | 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4 x 1.92 TB  | 1.5M/8000    | 32000/640  | 32000/1280 | 32 | 8 | 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x1.92 TB  | 2.2M/14000   | 48000/960  | 48000/2000 | 32 | 8 | 16000+ |
| Standard_L64s_v2  | 64 | 512 | 640 |  8 x 1.92 TB  | 2.9M/16000   | 64000/1280 | 64000/2000 | 32 | 8 | 16000+ |
| Standard_L80s_v2<sup>6</sup> | 80 | 640 | 800 | 10 x 1.92 TB | 3.8M/20000 | 80000/1400 | 80000/2000 | 32 | 8 | 16000+ |

<sup>1</sup> Lsv2 シリーズの VM には、OS ページング/スワップ ファイル用の標準 SCSI ベースの一時リソース ディスクがあります (Windows の場合は D:、Linux の場合は /dev/sdb)。 このディスクは、8 vCPU ごとに 80 GiB のストレージ、4,000 IOPS、および 80 MBps の転送速度を提供します (たとえば、Standard_L80s_v2 は、40,000 IOPS および 800 MBPS で 800 GiB を提供します)。 これにより、NVMe ドライブを確実にアプリケーション専用にすることができます。 このディスクはエフェメラルであり、すべてのデータは停止/割り当て解除時に失われます。

<sup>2</sup> ローカル NVMe ディスクはエフェメラルであり、VM を停止/割り当て解除した場合これらのディスク上のデータは失われます。

<sup>3</sup> Hyper-V NVMe Direct テクノロジにより、ゲスト VM スペースに安全にマッピングされたローカル NVMe ドライブへの無制限のアクセスが提供されます。  最大のパフォーマンスを実現するには、Azure Marketplace から最新の WS2019 ビルドまたは Ubuntu 18.04 または 16.04 のいずれかを使用する必要があります。  書き込みのパフォーマンスは、IO サイズ、ドライブの負荷、および容量使用率によって異なります。

<sup>4</sup> Lsv2 ワークロードに役立たないため、Lsv2 シリーズの VM ではデータ ディスク用のホスト キャッシュを提供しません。

<sup>5</sup> Lsv2 シリーズの VM では、一度に最大 30 分間、ディスク パフォーマンスを[バースト](./disk-bursting.md)できます。 

<sup>6</sup> vCPU が 64 個を超える VM には、次のサポートされているゲスト オペレーティング システムのいずれかが必要です。

- Windows Server 2016 以降
- Azure 用にチューニングされたカーネル (4.15 カーネル以降) を含む Ubuntu 16.04 LTS 以降
- SLES 12 SP2 以降
- Microsoft が提供する LIS パッケージ 4.3.1 (以降) がインストールされた RHEL または CentOS バージョン 6.7 から 6.10
- Microsoft が提供する LIS パッケージ 4.2.1 (以降) がインストールされた RHEL または CentOS バージョン 7.3
- RHEL または CentOS バージョン 7.6 以降
- UEK4 以降を含む Oracle Linux
- バックポート カーネルを含む Debian 9、Debian 10 以降
- 4\.14 カーネル以降を含む CoreOS

## <a name="size-table-definitions"></a>サイズ表の定義

- ストレージ容量は GiB (1024^3 バイト) 単位で示されています。 GB (1000^3 バイト) 単位のディスクと GiB (1024^3 バイト) 単位のディスクを比較する場合は、GiB 単位の方が容量の数値が小さく見えることに注意してください。 たとえば、1023 GiB = 1098.4 GB です。
- ディスク スループットの測定単位は、1 秒あたりの入力/出力操作数 (IOPS) および MBps です (MBps = 10^6 バイト/秒)。
- VM のパフォーマンスを最適にするには、データ ディスクの数を vCPU あたり 2 ディスクに制限する必要があります。
- **想定ネットワーク帯域幅** は、すべての宛先について、すべての NIC で [VM の種類ごとに割り当てられた最大集約帯域幅](../virtual-network/virtual-machine-network-throughput.md)です。 上限は保証されませんが、目的のアプリケーションに適した VM の種類を選択するためのガイダンスを示しています。 実際のネットワークのパフォーマンスは、ネットワークの輻輳、アプリケーションの負荷、ネットワーク設定など、さまざまな要因に左右されます。 ネットワーク スループットの最適化については、[Windows および Linux のネットワーク スループットの最適化](../virtual-network/virtual-network-optimize-network-bandwidth.md)に関する記事を参照してください。 Linux または Windows で想定ネットワーク パフォーマンスを実現するには、特定のバージョンを選択するか、VM を最適化することが必要になることがあります。 詳細については、[仮想マシンのスループットを確実にテストする方法](../virtual-network/virtual-network-bandwidth-testing.md)に関する記事を参照してください。


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