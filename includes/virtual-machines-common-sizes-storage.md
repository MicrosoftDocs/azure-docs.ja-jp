---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/17/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: b98aebfd7bef3edff8e046d7ef1c388ea57afa04
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67501249"
---
ストレージ最適化済み VM サイズは高いディスク スループットと IO を実現し、ビッグ データ、SQL、NoSQL データベース、データ ウェアハウス、および大規模なトランザクション データベースに最適です。  例としては、Cassandra、MongoDB、Cloudera、および Redis があります。 この記事では、最適化された各サイズのローカル ストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を提供します。

Lsv2 シリーズは、2.55 GHz の全コア ブーストと 3.0 GHz の最大ブーストが可能な [AMD EPYC&trade; 7551 プロセッサ](https://www.amd.com/en/products/epyc-7000-series)上で実行され、高スループット、低待ち時間、直接マッピングされたローカル NVMe ストレージを特長としています。 Lsv2 シリーズの VM には、同時マルチスレッド構成で 8 から 80 vCPU のサイズが用意されています。  vCPU あたり 8 GiB のメモリ、8 vCPU あたり 1 つの 1.92 TB NVMe SSD M.2 デバイスが用意され、L80s v2 では最大 19.2 TB (10 x 1.92 TB) を使用できます。

> [!NOTE]
> Lsv2 シリーズの VM は、持続性のあるデータ ディスクを使用する代わりに、VM に直接接続されているノード上のローカル ディスクを使用するように最適化されています。 これにより、ユーザーのワークロードに対応するより優れた IOPS/スループットが得られます。 Lsv2 および Ls シリーズでは、持続性のあるデータ ディスクで実現できる IOPS を向上させるためのローカル キャッシュの作成はサポートされません。
>
> Lsv2 および Ls シリーズの VM は、ローカル ディスクの高いスループットと IOPS によって、1 つの VM で障害が発生した場合に複数の VM にデータをレプリケートして永続性を実現する Apache Cassandra や MongoDB などの NoSQL ストアにとって理想的なものになっています。
>
> 詳細については、[Lsv2 シリーズの仮想マシンでパフォーマンスを最適化する](../articles/virtual-machines/linux/storage-performance.md)方法に関するページを参照してください。  


## <a name="lsv2-series"></a>Lsv2 シリーズ

ACU: 150 から 175

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされていません

| Size          | vCPU | メモリ (GiB) | 一時ディスク<sup>1</sup> (GiB) | NVMe ディスク<sup>2</sup> | NVMe ディスク スループット<sup>3</sup> (読み取り IOPS / MBps) | キャッシュ不使用時の最大データ ディスク スループット (IOPs/MBps)<sup>4</sup> | 最大データ ディスク数 | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---------------|-----------|-------------|--------------------------|----------------|---------------------------------------------------|-------------------------------------------|------------------------------|------------------------------| 
| Standard_L8s_v2   |  8 |  64 |  80 |  1 x 1.92 TB  | 400000/2000  | 8000/160   | 16 | 2/3200  |
| Standard_L16s_v2  | 16 | 128 | 160 |  2 x 1.92 TB  | 800000/4000  | 16000/320  | 32 | 4/6400  |
| Standard_L32s_v2  | 32 | 256 | 320 |  4 x 1.92 TB  | 1.5M/8000    | 32000/640  | 32 | 8/12800 |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x1.92 TB  | 2.2M/14000   | 48000/960  | 32 | 8/16000+ |
| Standard_L64s_v2  | 64 | 512 | 640 |  8 x 1.92 TB  | 2.9M/16000   | 64000/1280 | 32 | 8/16000+ |
| Standard_L80s_v2<sup>5</sup> | 80 | 640 | 800 | 10 x 1.92 TB   | 3.8M/20000   | 80000/1400 | 32 | 8/16000+ |

<sup>1</sup> Lsv2 シリーズの VM には、OS ページング/スワップ ファイル用の標準 SCSI ベースの一時リソース ディスクがあります (Windows の場合は D:、Linux の場合は /dev/sdb)。 このディスクは、8 vCPU ごとに 80 GiB のストレージ、4,000 IOPS、および 80 MBps の転送速度を提供します (たとえば、Standard_L80s_v2 は、40,000 IOPS および 800 MBPS で 800 GiB を提供します)。 これにより、NVMe ドライブを確実にアプリケーション専用にすることができます。 このディスクはエフェメラルであり、すべてのデータは停止/割り当て解除時に失われます。

<sup>2</sup> ローカル NVMe ディスクはエフェメラルであり、VM を停止/割り当て解除した場合これらのディスク上のデータは失われます。

<sup>3</sup> Hyper-V NVMe Direct テクノロジにより、ゲスト VM スペースに安全にマッピングされたローカル NVMe ドライブへの無制限のアクセスが提供されます。  最大のパフォーマンスを実現するには、Azure Marketplace から最新の WS2019 ビルドまたは Ubuntu 18.04 または 16.04 のいずれかを使用する必要があります。  書き込みのパフォーマンスは、IO サイズ、ドライブの負荷、および容量使用率によって異なります。

<sup>4</sup> Lsv2 ワークロードに役立たないため、Lsv2 シリーズの VM ではデータ ディスク用のホスト キャッシュを提供しません。  ただし、Lsv2 VM は、Azure のエフェメラル VM OS ディスク オプション (最大 30 GiB) に対応できます。

<sup>5</sup> vCPU が 64 個を超える VM には、次のサポートされているゲスト オペレーティング システムのいずれかが必要です。
- Windows Server 2016 以降
- Azure 用にチューニングされたカーネル (4.15 カーネル以降) を含む Ubuntu 16.04 LTS 以降
- SLES 12 SP2 以降
- Microsoft が提供する LIS パッケージ 4.3.1 (以降) がインストールされた RHEL または CentOS バージョン 6.7 ～ 6.10
- Microsoft が提供する LIS パッケージ 4.2.1 (以降) がインストールされた RHEL または CentOS バージョン 7.3
- RHEL または CentOS バージョン 7.6 以降
- UEK4 以降を含む Oracle Linux
- バックポート カーネルを含む Debian 9、Debian 10 以降
- 4\.14 カーネル以降を含む CoreOS


## <a name="size-table-definitions"></a>サイズ表の定義

- ストレージ容量は GiB (1024^3 バイト) 単位で示されています。 GB (1000^3 バイト) 単位のディスクと GiB (1024^3 バイト) 単位のディスクを比較する場合は、GiB 単位の方が容量の数値が小さく見えることに注意してください。 たとえば、1023 GiB = 1098.4 GB です。
- ディスク スループットの測定単位は、1 秒あたりの入力/出力操作数 (IOPS) および MBps です (MBps = 10^6 バイト/秒)。
- VM のパフォーマンスを最適にするには、データ ディスクの数を vCPU あたり 2 ディスクに制限する必要があります。
- **想定ネットワーク帯域幅**は、すべての宛先について、すべての NIC で [VM の種類ごとに割り当てられた最大集約帯域幅](../articles/virtual-network/virtual-machine-network-throughput.md)です。 上限は保証されませんが、目的のアプリケーションに適した VM の種類を選択するためのガイダンスを示しています。 実際のネットワークのパフォーマンスは、ネットワークの輻輳、アプリケーションの負荷、ネットワーク設定など、さまざまな要因に左右されます。 ネットワーク スループットの最適化については、[Windows および Linux のネットワーク スループットの最適化](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)に関する記事を参照してください。 Linux または Windows で想定ネットワーク パフォーマンスを実現するには、特定のバージョンを選択するか、VM を最適化することが必要になることがあります。 詳細については、[仮想マシンのスループットを確実にテストする方法](../articles/virtual-network/virtual-network-bandwidth-testing.md)に関する記事を参照してください。
