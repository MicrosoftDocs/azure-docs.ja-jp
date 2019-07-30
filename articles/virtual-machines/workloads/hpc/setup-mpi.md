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
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 541e42a72ea604c4d71dc546b14dea2f0857bcc1
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797505"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>HPC の Message Passing Interface を設定する

Message Passing Interface (MPI) ワークロードは、従来の HPC ワークロードの重要な部分です。 Azure 上の SR-IOV に対応した VM のサイズでは、ほぼすべての種類の MPI を使用できます。 

VM 上で MPI ジョブを実行するには、テナント全体のパーティション キー (p キー) を設定する必要があります。 p キー値の決定の詳細については、[パーティション キーの検出](#discover-partition-keys)のセクションの手順に従ってください。

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx) は IB で最適なパフォーマンスを提供し、MPICH および OpenMPI と連動します。

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

前述のように、UCX をインストールします。

```bash
sudo yum install –y openmpi
```

OpenMPI をビルドします。

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

OpenMPI を実行します。

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

前述のように、パーティション キーを確認します。

## <a name="mpich"></a>MPICH

前述のように、UCX をインストールします。

MPICH をビルドします。

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

MPICH を実行します。

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

前述のように、パーティション キーを確認します。

## <a name="mvapich2"></a>MVAPICH2

MVAPICH2 をビルドします。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

MVAPICH2 を実行します。

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
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

## <a name="intel-mpi"></a>Intel MPI

[Intel MPI をダウンロード](https://software.intel.com/mpi-library/choose-download)します。

バージョンに応じて I_MPI_FABRICS 環境変数を変更します。 Intel MPI 2018 には `I_MPI_FABRICS=shm:ofa` を使用し、2019 には `I_MPI_FABRICS=shm:ofi` を使用します。

プロセス固定は、既定で 15、30、60 PPN で正しく機能します。

## <a name="osu-mpi-benchmarks"></a>OSU MPI ベンチマーク

[OSU MPI ベンチマークをダウンロードし](http://mvapich.cse.ohio-state.edu/benchmarks/)、解凍します。

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

同じテナント (可用性セットまたは VM スケール セット) 内の他の VM と通信するために、パーティション キー (p キー) を検出します。

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

テナント (AVSet または VMSS) が存在する限り、p キーは同じままであることにも注意してください。 これは、ノードが追加または削除された場合でも当てはまります。 新しいテナントには、別の p キーが割り当てられます。


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
chmod 644 /home/$USER/.ssh/config
```

上記の構文は共有ホーム ディレクトリを想定しており、それ以外の場合は .ssh ディレクトリを各ノードにコピーする必要があります。

## <a name="next-steps"></a>次の手順

Azure での [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) の詳細を確認する。
