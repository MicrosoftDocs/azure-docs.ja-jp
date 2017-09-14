---
title: "Batch でのコンピューティング集中型 Azure VM の使用 | Microsoft Docs"
description: "Azure Batch プールで RDMA 対応または GPU 対応の VM サイズを利用する方法"
services: batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: 
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: c52a054e4fc8f61f871acd9f35b9a3e6247e48ef
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>Batch プールでの RDMA 対応または GPU 対応インスタンスの使用

特定の Batch ジョブを実行するために、大規模な計算用に設計された Azure VM サイズを利用できます。 たとえば、マルチインスタンスの [MPI ワークロード](batch-mpi.md)を実行する場合は、リモート ダイレクト メモリ アクセス (RDMA) 用のネットワーク インターフェイスを備えた A8、A9、または H シリーズのサイズを選択できます。 これらのサイズでは、InfiniBand ネットワークに接続してノード間通信を行うため、MPI アプリケーションを高速化できます。 CUDA アプリケーションの場合は、NVIDIA Tesla グラフィックス プロセッシング ユニット (GPU) カードを備えた N シリーズのサイズを選択できます。

この記事では、Batch プールで Azure の特殊なサイズを使用するためのガイダンスと例を示します。 仕様と背景については、以下をご覧ください。

* ハイ パフォーマンス コンピューティング VM のサイズ ([Linux](../virtual-machines/linux/sizes-hpc.md)、[Windows](../virtual-machines/windows/sizes-hpc.md)) 

* GPU 対応 VM サイズ ([Linux](../virtual-machines/linux/sizes-gpu.md)、[Windows](../virtual-machines/windows/sizes-gpu.md)) 


## <a name="subscription-and-account-limits"></a>サブスクリプションとアカウントの制限

* **クォータ** - 1 つ以上の Azure クォータによって、Batch プールに追加できるノードの数または種類が制限される場合があります。 RDMA 対応、GPU 対応、または他のマルチコアの VM サイズを選択すると、制限される可能性が高くなります。 作成した Batch アカウントの種類によっては、アカウント自体またはサブスクリプションにクォータが適用される可能性があります。

    * **Batch サービス**構成で Batch アカウントを作成した場合、[Batch アカウントあたりの専用コア クォータ](batch-quota-limit.md#resource-quotas)によって制限されます。 既定では、このクォータは 20 コアです。 [優先順位の低い VM](batch-low-pri-vms.md) を使用している場合、これらの VM には個別のクォータが適用されます。 

    * **ユーザー サブスクリプション**構成でアカウントを作成した場合、サブスクリプションによってリージョンあたりの VM コアの数が制限されます。 「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」をご覧ください。 サブスクリプションによって、特定の VM サイズ (HPC インスタンスや GPU インスタンスなど) にリージョンのクォータも適用されます。 ユーザー サブスクリプション構成では、Batch アカウントに追加のクォータは適用されません。 

  Batch で特殊な VM サイズを使用するときには、1 つ以上のクォータを増やすことが必要な場合があります。 クォータを増やすためのリクエストは、[オンライン カスタマー サポートに申請](../azure-supportability/how-to-create-azure-support-request.md) (無料) してください。

* **リージョンの可用性** - コンピューティング集中型 VM は、Batch アカウントを作成したリージョンで使用できない場合があります。 特定のサイズが使用可能かどうかを確認するには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」をご覧ください。


## <a name="dependencies"></a>依存関係

コンピューティング集中型サイズの RDMA と GPU の機能は、特定のオペレーティング システムでのみサポートされます。 オペレーティング システムによっては、追加のドライバーや他のソフトウェアをインストールまたは構成する必要があります。 以下の表に、これらの依存関係を示します。 詳細については、リンク先の記事をご覧ください。 Batch プールの構成オプションについては、この記事で後述します。


### <a name="linux-pools---virtual-machine-configuration"></a>Linux プール - 仮想マシン構成

| サイズ | 機能 | オペレーティング システム | 必要なソフトウェア | プールの設定 |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | SUSE Linux Enterprise Server 12 HPC または<br/>CentOS ベースの HPC<br/>(Azure Marketplace) | Intel MPI 5 | ノード間通信を有効にし、同時実行タスクの実行を無効にする |
| [NC シリーズ*](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-vms) | NVIDIA Tesla K80 GPU | Ubuntu 16.04 LTS。<br/>Red Hat Enterprise Linux 7.3 または<br/>CentOS-based 7.3<br/>(Azure Marketplace) | NVIDIA CUDA Toolkit 8.0 ドライバー | 該当なし | 
| [NV シリーズ](../virtual-machines/linux/n-series-driver-setup.md#install-grid-drivers-for-nv-vms) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS<br/>Red Hat Enterprise Linux 7.3<br/>CentOS-based 7.3<br/>(Azure Marketplace) | NVIDIA GRID 4.3 ドライバー | 該当なし |

*NC24r VM の RDMA 接続は、Intel MPI がインストールされた CentOS ベース 7.3 HPC でサポートされます。



### <a name="windows-pools---virtual-machine-configuration"></a>Windows プール - 仮想マシン構成

| サイズ | 機能 | オペレーティング システム | 必要なソフトウェア | プールの設定 |
| -------- | ------ | -------- | -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2 または<br/>Windows Server 2012 (Azure Marketplace) | Microsoft MPI 2012 R2 以降または<br/> Intel MPI 5<br/><br/>HpcVMDrivers Azure VM 拡張機能 | ノード間通信を有効にし、同時実行タスクの実行を無効にする |
| [NC シリーズ*](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla K80 GPU | Windows Server 2016 または <br/>Windows Server 2012 R2 (Azure Marketplace) | NVIDIA Tesla ドライバーまたは CUDA Toolkit 8.0 ドライバー| 該当なし | 
| [NV シリーズ](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 または<br/>Windows Server 2012 R2 (Azure Marketplace) | NVIDIA GRID 4.3 ドライバー | 該当なし |

*NC24r VM の RDMA 接続は、HpcVMDrivers 拡張機能と Microsoft MPI または Intel MPI がインストールされた Windows Server 2012 R2 でサポートされます。

### <a name="windows-pools---cloud-services-configuration"></a>Windows プール - クラウド サービス構成

> [!NOTE]
> N シリーズのサイズは、クラウド サービス構成の Batch プールではサポートされていません。
>

| サイズ | 機能 | オペレーティング システム | 必要なソフトウェア | プールの設定 |
| -------- | ------- | -------- | -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2、<br/>Windows Server 2012、または<br/>Windows Server 2008 R2 (ゲスト OS ファミリ) | Microsoft MPI 2012 R2 以降または<br/>Intel MPI 5<br/><br/>HpcVMDrivers Azure VM 拡張機能 | ノード間通信を有効にし、<br/> 同時実行タスクの実行を無効にする |





## <a name="pool-configuration-options"></a>プール構成オプション

Batch プール用の特殊な VM サイズを構成するために、Batch API とツールには、必要なソフトウェアやドライバーをインストールするための次のようなオプションが用意されています。

* [開始タスク](batch-api-basics.md#start-task) - Batch アカウントと同じリージョンにある Azure ストレージ アカウントに、リソース ファイルとしてインストール パッケージをアップロードします。 プールの起動時にリソース ファイルをサイレント インストールする開始タスク コマンド ラインを作成します。 詳細については、[REST API のドキュメント](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask)をご覧ください。

  > [!NOTE] 
  > 開始タスクは管理者特権で実行する必要があり、成功するまで待機する必要があります。
  >

* [アプリケーション パッケージ](batch-application-packages.md) - 圧縮されたインストール パッケージを Batch アカウントに追加し、プールでパッケージ参照を構成します。 この設定では、プールのすべてのノードでパッケージがアップロードされ、解凍されます。 パッケージがインストーラーの場合は、プールのすべてのノードにアプリケーションをサイレント インストールする開始タスク コマンド ラインを作成します。 必要に応じて、ノードでタスクの実行がスケジュールされているときにパッケージをインストールすることもできます。

* [プールのカスタム イメージ](batch-api-basics.md#pool) - ドライバー、ソフトウェア、またはその VM サイズに必要な他の設定を含む、カスタムの Windows または Linux VM イメージを作成します。 ユーザー サブスクリプション構成で Batch アカウントを作成した場合は、Batch プールのカスタム イメージを指定します  (カスタム イメージは、Batch サービス構成のアカウントではサポートされていません)。カスタム イメージは、仮想マシン構成のプールでのみ使用できます。

  > [!IMPORTANT]
  > Batch プールでは、管理ディスクまたは Premium Storage を使用して作成されたカスタム イメージは現在使用できません。
  >



* [Batch Shipyard](https://github.com/Azure/batch-shipyard) - Azure Batch のコンテナー化ワークロードで透過的に機能するように GPU と RDMA を自動的に構成します。 Batch Shipyard は、完全に構成ファイルで駆動されます。 N シリーズ VM で GPU ドライバーを事前に構成し、Microsoft Cognitive Toolkit ソフトウェアを Docker イメージとして読み込む [CNTK GPU レシピ](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI)など、GPU および RDMA ワークロードに対応する多数のサンプル レシピ構成が提供されています。


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>例: A8 VM プールの Microsoft MPI

Azure A8 ノードのプールで Windows MPI アプリケーションを実行するには、サポートされている MPI 実装をインストールする必要があります。 Batch アプリケーション パッケージを使用して、Windows プールに [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) をインストールする手順の例を次に示します。

1. 最新バージョンの Microsoft MPI の[セットアップ パッケージ](http://go.microsoft.com/FWLink/p/?LinkID=389556) (MSMpiSetup.exe) をダウンロードします。
2. パッケージの zip ファイルを作成します。
3. パッケージを Batch アカウントにアップロードします。 手順については、[アプリケーション パッケージ](batch-application-packages.md)のガイダンスを参照してください。 アプリケーション ID (*MSMPI* など) とバージョン (*8.1* など) を指定します。 
4. Batch API または Azure Portal で、必要な数のノードとスケールを指定して、クラウド サービス構成でプールを作成します。 次の表に、開始タスクを使用して無人モードで MPI を設定する際の設定の例を示します。

| 設定 | 値 |
| ---- | ----- | 
| **イメージの種類** | Cloud Services |
| **OS ファミリ** | Windows Server 2012 R2 (OS ファミリ 4) |
| **ノード サイズ** | A8 Standard |
| **ノード間通信が有効** | True |
| **ノードごとの最大タスク数** | 1 |
| **アプリケーション パッケージの参照** | MSMPI |
| **Start task enabled\(開始タスクが有効\)** | True<br>**[コマンド ライン]** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**ユーザー ID** - Pool autouser、admin<br/>**成功を待機** - True

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>例: NC VM プールの NVIDIA Tesla ドライバー

Linux NC ノードのプールで CUDA アプリケーションを実行するには、CUDA Toolkit 8.0 をノードにインストールする必要があります。 この Toolkit により、必要な NVIDIA Tesla GPU ドライバーがインストールされます。 GPU ドライバーがインストールされたカスタム Ubuntu 16.04 LTS イメージをデプロイする手順の例を次に示します。

1. Ubuntu 16.04 LTS を実行する Azure NC6 VM をデプロイします。 たとえば、米国中南部リージョンに VM を作成します。 VM は、Standard Storage を使用し、管理ディスクを使用*せずに*作成する必要があります。
2. VM に接続し、[CUDA ドライバーをインストール](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-vms)する手順に従います。
3. Linux エージェントをプロビジョニング解除し、Azure CLI 1.0 コマンドを使用して Linux VM イメージをキャプチャします。 手順については、「[Azure で実行されている Linux 仮想マシンをキャプチャする](../virtual-machines/linux/capture-image-nodejs.md)」をご覧ください。 イメージ URI を書き留めておきます。
  > [!IMPORTANT]
  > Azure Batch のイメージをキャプチャするときは、Azure CLI 2.0 コマンドを使用しないでください。 現在、CLI 2.0 コマンドは、管理ディスクを使用して作成された VM のみをキャプチャします。
  >
4. NC VM をサポートするリージョンに、ユーザー サブスクリプション構成で Batch アカウントを作成します。
5. Batch API または Azure Portal で、カスタム イメージを使用し、必要な数のノードとスケールを指定してプールを作成します。 次の表に、イメージのプール設定の例を示します。

| 設定 | 値 |
| ---- | ---- |
| **イメージの種類** | カスタム イメージ |
| **カスタム イメージ** | `https://yourstorageaccountdisks.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd` 形式のイメージ URI |
| **ノード エージェント SKU** | batch.node.ubuntu 16.04 |
| **ノード サイズ** | NC6 Standard |



## <a name="next-steps"></a>次のステップ

* Azure Batch プールで MPI ジョブを実行する方法については、[Windows](batch-mpi.md) または [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) の例を参照してください。

* Batch の GPU ワークロードの例については、[Batch Shipyard](https://github.com/Azure/batch-shipyard/) レシピを参照してください。
