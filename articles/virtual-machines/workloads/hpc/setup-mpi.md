---
title: HPC の Message Passing Interface の設定 - Azure Virtual Machines | Microsoft Docs
description: Azure で HPC の MPI を設定する方法について説明します。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 210b2935cd2df81b0ff079c9a1c945fe770933f9
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926520"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>HPC の Message Passing Interface を設定する

[メッセージ パッシング インターフェイス (MPI)](https://en.wikipedia.org/wiki/Message_Passing_Interface) は、分散型メモリ並列化についてのオープン ライブラリであり、事実上の標準です。 多くの HPC ワークロードにわたってよく使用されています。 [RDMA 対応](../../sizes-hpc.md#rdma-capable-instances)の [H シリーズ](../../sizes-hpc.md)および [N シリーズ](../../sizes-gpu.md) VM 上の HPC ワークロードは、MPI を使用し、低待機時間で高帯域幅の InfiniBand ネットワークを介して通信することができます。

Azure の SR-IOV 対応 VM サイズ (HBv2、HB、HC、NCv3、NDv2) では、ほぼすべてのフレーバーの MPI を Mellanox OFED と一緒に使用できます。 SR-IOV に対応していない VM の場合、サポートされている MPI 実装では、VM 間の通信に Microsoft Network Direct (ND) インターフェイスが使用されます。 そのため、Microsoft MPI (MS MPI) 2012 R2 以降と Intel MPI 5.x バージョンのみがサポートされています。 Intel MPI ランタイム ライブラリの以降のバージョン (2017、2018) は、Azure RDMA ドライバーと互換性がある場合とない場合があります。

SR-IOV が有効な [RDMA 対応 VM](../../sizes-hpc.md#rdma-capable-instances) については、Marketplace の [CentOS-HPC バージョン 7.6 以降](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)のバージョンの VM イメージは最適化されていて、RDMA 用の OFED ドライバー、一般的に使用されているさまざまな MPI ライブラリ、科学コンピューティング パッケージが事前に読み込まれているため、最も簡単に利用を開始できる方法となっています。

これらの例は RHEL/CentOS に向けたものですが、手順は一般的であり、Ubuntu (16.04、18.04、19.04、20.04) や SLES (12 SP4、15) などの互換性のあるすべての Linux オペレーティング システムで使用できます。 その他の MPI 実装を別のディストリビューションで設定するための例については、[azhpc-images リポジトリ](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)により多く用意されています。

> [!NOTE]
> SR-IOV 対応の VM で MPI ジョブを実行するには、分離とセキュリティのために、テナント全体のパーティション キー (p キー) を設定する必要があります。 p キーの値を決定し、その値を MPI ジョブのために正しく設定することの詳細については、「[パーティション キーを検出する](#discover-partition-keys)」セクションの手順に従ってください。

## <a name="ucx"></a>UCX

[Unified Communication X (UCX)](https://github.com/openucx/ucx) は、HPC 用の通信 API フレームワークです。 InfiniBand 経由の MPI 通信のために最適化されており、OpenMPI や MPICH などの多くの MPI 実装で動作します。

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="hpc-x"></a>HPC-X

[HPC-X ソフトウェア ツールキット](https://www.mellanox.com/products/hpc-x-toolkit)には、UCX と HCOLL が含まれています。

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

HPC-X を実行します

```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

## <a name="openmpi"></a>OpenMPI

前述したように UCX をインストールします。 HCOLL は [HPC-X ソフトウェア ツールキット](https://www.mellanox.com/products/hpc-x-toolkit)の一部であり、特別なインストールは不要です。

リポジトリで入手可能なパッケージから OpenMPI をインストールします。

```bash
sudo yum install –y openmpi
```

OpenMPI をビルドします。

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

OpenMPI を実行します。

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

前述のように、パーティション キーを確認します。

## <a name="intel-mpi"></a>Intel MPI

選択したバージョンの [Intel MPI](https://software.intel.com/mpi-library/choose-download) をダウンロードします。 バージョンに応じて I_MPI_FABRICS 環境変数を変更します。 Intel MPI 2018 には `I_MPI_FABRICS=shm:ofa` を使用し、2019 には `I_MPI_FABRICS=shm:ofi` を使用します。

### <a name="non-sr-iov-vms"></a>SR-IOV 非対応 VM
SR-IOV 非対応 VM の場合、5.x ランタイムの[無料評価版](https://registrationcenter.intel.com/en/forms/?productid=1740)をダウンロードする例は次のとおりです。
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
インストール手順については、[Intel MPI ライブラリのインストール ガイド](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)をご覧ください。
必要に応じて (最新バージョンの Intel MPI に必要な) non-root non-debugger プロセスに対して ptrace を有効にすることをお勧めします。
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
SUSE Linux Enterprise Server の VM イメージ バージョンが SLES 12 SP3 for HPC、SLES 12 SP3 for HPC (Premium)、SLES 12 SP1 for HPC、SLES 12 SP1 for HPC (Premium)、SLES 12 SP4、SLES 15 の場合、RDMA ドライバーがインストールされ、 VM には Intel MPI パッケージが配布されます。 次のコマンドを実行して Intel MPI をインストールします。
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mpich"></a>MPICH

前述したように UCX をインストールします。 MPICH をビルドします。

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

MPICH を実行します。

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

前述のように、パーティション キーを確認します。

## <a name="mvapich2"></a>MVAPICH2

MVAPICH2 をビルドします。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

MVAPICH2 を実行します。

```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>プラットフォーム MPI コミュニティ エディション

Platform MPI に必要なパッケージをインストールします。

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

インストール プロセスに従います。

## <a name="osu-mpi-benchmarks"></a>OSU MPI ベンチマーク

[OSU MPI ベンチマーク](http://mvapich.cse.ohio-state.edu/benchmarks/)をダウンロードし、解凍します。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

特定の MPI ライブラリを使用してベンチマークをビルドします:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI ベンチマークは `mpi/` フォルダー下にあります。


## <a name="discover-partition-keys"></a>パーティション キーを検出する

同じテナント (可用性セットまたは仮想マシン スケール セット) 内の他の VM と通信するために、パーティション キー (p キー) を検出します。

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

2 つのうち大きいほうは、MPI で使用する必要があるテナント キーです。 例:p キーが次の場合、MPI では 0x800b を使用する必要があります。

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

既定 (0x7fff) のパーティション キー以外のパーティションを使用します。 UCX では、p キーの MSB をクリアする必要があります。 たとえば、0x800b の場合、UCX_IB_PKEY を 0x000b と設定します。

テナント (可用性セットまたは仮想マシン スケール セット) が存在する限り、p キーは同じままであることにも注意してください。 これは、ノードが追加または削除された場合でも当てはまります。 新しいテナントには、別の p キーが割り当てられます。


## <a name="set-up-user-limits-for-mpi"></a>MPI のユーザー制限を設定する

MPI のユーザー制限を設定します。

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>MPI の SSH キーを設定する

SSH キーが必要な MPI タイプの場合、設定します。

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

上記の構文は共有ホーム ディレクトリを想定しており、それ以外の場合は .ssh ディレクトリを各ノードにコピーする必要があります。

## <a name="next-steps"></a>次の手順

- [InfiniBand 対応](../../sizes-hpc.md#rdma-capable-instances)の [H シリーズ](../../sizes-hpc.md)および [N シリーズ](../../sizes-gpu.md)の VM について学習します
- [HB シリーズの概要](hb-series-overview.md)と [HC シリーズの概要](hc-series-overview.md)に関するページを参照して、パフォーマンスとスケーラビリティのためにワークロードを最適に構成する方法を学習します。
- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表および HPC の例と結果について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。
