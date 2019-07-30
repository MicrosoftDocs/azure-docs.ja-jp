---
title: Batch でのコンピューティング集中型 Azure VM の使用 | Microsoft Docs
description: Azure Batch プールで HPC および GPU の VM サイズを利用する方法
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2018
ms.author: lahugh
ms.openlocfilehash: 687783520b082cdfd1a6ffc91a8641ea35fafd68
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323346"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Batch プールで RDMA または GPU インスタンスを使用する

特定の Batch ジョブを実行するために、大規模な計算用に設計された Azure VM サイズを利用できます。 例:

* マルチインスタンスの [MPI ワークロード](batch-mpi.md)を実行するには、リモート ダイレクト メモリ アクセス (RDMA) 用のネットワーク インターフェイスを備えた H シリーズなどのサイズを選択できます。 これらのサイズでは、InfiniBand ネットワークに接続してノード間通信を行うため、MPI アプリケーションを高速化できます。 

* CUDA アプリケーションの場合は、NVIDIA Tesla グラフィックス処理装置 (GPU) カードを備えた N シリーズのサイズを選択します。

この記事では、Batch プールで Azure の特殊なサイズを使用するためのガイダンスと例を示します。 仕様と背景については、以下をご覧ください。

* ハイ パフォーマンス コンピューティング VM のサイズ ([Linux](../virtual-machines/linux/sizes-hpc.md)、[Windows](../virtual-machines/windows/sizes-hpc.md)) 

* GPU 対応 VM サイズ ([Linux](../virtual-machines/linux/sizes-gpu.md)、[Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> 特定の VM サイズは、Batch アカウントを作成したリージョンで使用できない場合があります。 特定のサイズが使用可能かどうかを確認するには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」および [Batch プールの VM サイズの選択](batch-pool-vm-sizes.md)に関するページをご覧ください。

## <a name="dependencies"></a>依存関係

Batch のコンピューティング集中型サイズの RDMA または GPU の機能は、特定のオペレーティング システムでのみサポートされます。 (サポートされるオペレーティング システムの一覧は、これらのサイズで作成された仮想マシンでサポートされているオペレーティング システムの一部です。)Batch プールの作成方法によっては、追加のドライバーや他のソフトウェアをノードにインストールまたは構成する必要があります。 以下の表に、これらの依存関係を示します。 詳細については、リンク先の記事をご覧ください。 Batch プールの構成オプションについては、この記事で後述します。

### <a name="linux-pools---virtual-machine-configuration"></a>Linux プール - 仮想マシン構成

| Size | 機能 | オペレーティング システム | 必要なソフトウェア | プールの設定 |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r、NC24rs_v2、NC24rs_v3、ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS、または<br/>CentOS-based HPC<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Linux RDMA ドライバー | ノード間通信を有効にし、同時実行タスクの実行を無効にする |
| [NC、NCv2、NCv3、NDv2 シリーズ](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (シリーズによって異なります) | Ubuntu 16.04 LTS、または<br/>CentOS 7.3 または 7.4<br/>(Azure Marketplace) | NVIDIA CUDA または CUDA Toolkit ドライバー | 該当なし | 
| [NV、NVv2 シリーズ](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS、または<br/>CentOS 7.3<br/>(Azure Marketplace) | NVIDIA GRID ドライバー | 該当なし |

<sup>*</sup>RDMA 対応 N シリーズのサイズには NVIDIA Tesla GPU も含まれます

### <a name="windows-pools---virtual-machine-configuration"></a>Windows プール - 仮想マシン構成

| Size | 機能 | オペレーティング システム | 必要なソフトウェア | プールの設定 |
| -------- | ------ | -------- | -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r、NC24rs_v2、NC24rs_v3、ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016、2012 R2、または<br/>2012 (Azure Marketplace) | Microsoft MPI 2012 R2 以降または<br/> Intel MPI 5<br/><br/>Windows RDMA ドライバー | ノード間通信を有効にし、同時実行タスクの実行を無効にする |
| [NC、NCv2、NCv3、ND、NDv2 シリーズ](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (シリーズによって異なります) | Windows Server 2016 または <br/>2012 R2 (Azure Marketplace) | NVIDIA CUDA または CUDA Toolkit ドライバー| 該当なし | 
| [NV、NVv2 シリーズ](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 または<br/>2012 R2 (Azure Marketplace) | NVIDIA GRID ドライバー | 該当なし |

<sup>*</sup>RDMA 対応 N シリーズのサイズには NVIDIA Tesla GPU も含まれます

### <a name="windows-pools---cloud-services-configuration"></a>Windows プール - クラウド サービス構成

> [!NOTE]
> N シリーズのサイズは、クラウド サービス構成の Batch プールではサポートされていません。
>

| Size | 機能 | オペレーティング システム | 必要なソフトウェア | プールの設定 |
| -------- | ------- | -------- | -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016、2012 R2、2012、または<br/>2008 R2 (ゲスト OS ファミリ) | Microsoft MPI 2012 R2 以降または<br/>Intel MPI 5<br/><br/>Windows RDMA ドライバー | ノード間通信を有効にし、<br/> 同時実行タスクの実行を無効にする |

## <a name="pool-configuration-options"></a>プール構成オプション

Batch プール用の特殊な VM サイズを構成するために、必要なソフトウェアやドライバーをインストールするためのいくつかのオプションがあります。

* 仮想マシン構成のプールの場合、ドライバーとソフトウェアがプレインストールされている構成済みの [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) VM イメージを選択します。 次に例を示します。 

  * [CentOS-based 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) - RDMA ドライバーと Intel MPI 5.1 が含まれています

  * [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md) for Linux または Windows - NVIDIA CUDA ドライバーが含まれています

  * GPU および RDMA ドライバーも含まれている Batch コンテナー ワークロードの Linux イメージ:

    * [Azure Batch コンテナー プール用の CentOS (GPU および RDMA ドライバー付き)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Azure Batch コンテナー プール用の Ubuntu Server (GPU および RDMA ドライバー付き)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* インストールされているドライバー、ソフトウェア、またはその VM サイズに必要な他の設定を含む、[カスタムの Windows または Linux VM イメージ](batch-custom-images.md)を作成します。 

* zip 形式のドライバーまたはアプリケーションのインストーラーから Batch の[アプリケーション パッケージ](batch-application-packages.md)を作成し、パッケージをプール ノードにデプロイするように Batch を構成し、各ノードが作成されたときに 1 回インストールします。 たとえば、アプリケーション パッケージがインストーラーの場合は、プールのすべてのノードにアプリケーションをサイレント インストールする[開始タスク](batch-api-basics.md#start-task) コマンド ラインを作成します。 ワークロードが特定のドライバー バージョンに依存する場合は、アプリケーション パッケージとプールの開始タスクの使用を検討してください。

  > [!NOTE] 
  > 開始タスクは管理者特権で実行する必要があり、成功するまで待機する必要があります。 実行時間の長いタスクは、Batch プールをプロビジョニングする時間が長くなります。
  >

* [Batch Shipyard](https://github.com/Azure/batch-shipyard) は、Azure Batch のコンテナー化ワークロードで透過的に機能するように GPU ドライバーと RDMA ドライバーを自動的に構成します。 Batch Shipyard は、完全に構成ファイルで駆動されます。 N シリーズ VM で GPU ドライバーを事前に構成し、Microsoft Cognitive Toolkit ソフトウェアを Docker イメージとして読み込む [CNTK GPU レシピ](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI)など、GPU および RDMA ワークロードに対応する多数のサンプル レシピ構成が提供されています。


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>例:Windows NC VM プールの NVIDIA GPU ドライバー

Windows NC ノードのプールで CUDA アプリケーションを実行するには、NVDIA GPU ドライバーをインストールする必要があります。 次の手順の例では、アプリケーション パッケージを使用して NVIDIA GPU ドライバーをインストールします。 ワークロードが特定の GPU ドライバー バージョンに依存する場合は、このオプションを選択できます。

1. [NVIDIA の Web サイト](https://www.nvidia.com/Download/index.aspx)から Windows Server 2016 上の GPU ドライバーのセットアップ パッケージをダウンロードします (たとえば、[バージョン 411.82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe))。 *GPUDriverSetup.exe* のような短い名前を使用して、ファイルをローカルで保存します。
2. パッケージの zip ファイルを作成します。
3. パッケージを Batch アカウントにアップロードします。 手順については、[アプリケーション パッケージ](batch-application-packages.md)のガイダンスを参照してください。 アプリケーション ID (*GPUDriver* など) とバージョン (*411.82* など) を指定します。
1. Batch API または Azure portal で、必要な数のノードとスケールを指定して、仮想マシン構成でプールを作成します。 次の表に、開始タスクを使用して NVIDIA GPU ドライバーを自動的にインストールする際の設定の例を示します。

| Setting | 値 |
| ---- | ----- | 
| **イメージの種類** | Marketplace (Linux/Windows) |
| **発行元** | MicrosoftWindowsServer |
| **プラン** | WindowsServer |
| **SKU** | 2016-Datacenter |
| **ノード サイズ** | NC6 Standard |
| **アプリケーション パッケージの参照** | GPUDriver、バージョン 411.82 |
| **開始タスクが有効** | True<br>**[コマンド ライン]**  - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**ユーザー ID** - Pool autouser、admin<br/>**成功を待機** - True

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>例:Linux NC VM プールの NVIDIA GPU ドライバー

Linux NC ノードのプールで CUDA アプリケーションを実行するには、CUDA Toolkit から必要な NVIDIA Tesla GPU ドライバーをインストールする必要があります。 次の手順の例では、GPU ドライバーがインストールされたカスタム Ubuntu 16.04 LTS イメージを作成およびデプロイします。

1. Ubuntu 16.04 LTS を実行する Azure NC シリーズ VM をデプロイします。 たとえば、米国中南部リージョンに VM を作成します。 
2. Azure portal、Azure サブスクリプションに接続するクライアント コンピューター、または Azure Cloud Shell を使用して、[NVIDIA GPU ドライバー拡張機能](../virtual-machines/extensions/hpccompute-gpu-linux.md
)を VM に追加します。 または、手順に従って VM に接続し、手動で [CUDA ドライバーをインストール](../virtual-machines/linux/n-series-driver-setup.md)します。
3. 手順に従って、Batch 用の[スナップショットとカスタム Linux VM イメージ](batch-custom-images.md)を作成します。
4. NC VM をサポートするリージョンに Batch アカウントを作成します。
5. Batch API または Azure Portal で、[カスタム イメージを使い](batch-custom-images.md)、必要な数のノードとスケールを指定して、プールを作成します。 次の表に、イメージのプール設定の例を示します。

| Setting | 値 |
| ---- | ---- |
| **イメージの種類** | カスタム イメージ |
| **カスタム イメージ** | *イメージの名前* |
| **ノード エージェント SKU** | batch.node.ubuntu 16.04 |
| **ノード サイズ** | NC6 Standard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>例:Windows H16r VM プールの Microsoft MPI

Azure H16r VM ノードのプールで Windows MPI アプリケーションを実行するには、HpcVmDrivers 拡張機能を構成し、[Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) をインストールする必要があります。 必要なドライバーとソフトウェアがインストールされたカスタムの Windows Server 2016 イメージをデプロイする手順の例を次に示します。

1. Windows Server 2016 を実行する Azure H16r VM をデプロイします。 たとえば、米国西部リージョンに VM を作成します。 
2. Azure サブスクリプションに接続するクライアント コンピューターから [Azure PowerShell コマンドを実行して](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances
)、または Azure Cloud Shell を使用して、HpcVmDrivers 拡張機能を VM に追加します。 
1. VM へのリモート デスクトップ接続を作成します。
1. 最新バージョンの Microsoft MPI の[セットアップ パッケージ](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) をダウンロードし、Microsoft MPI をインストールします。
1. 手順に従って、Batch 用の[スナップショットとカスタム Windows VM イメージ](batch-custom-images.md)を作成します。
1. Batch API または Azure Portal で、[カスタム イメージを使い](batch-custom-images.md)、必要な数のノードとスケールを指定して、プールを作成します。 次の表に、イメージのプール設定の例を示します。

| Setting | 値 |
| ---- | ---- |
| **イメージの種類** | カスタム イメージ |
| **カスタム イメージ** | *イメージの名前* |
| **ノード エージェント SKU** | batch.node.windows amd64 |
| **ノード サイズ** | H16r Standard |
| **ノード間通信が有効** | True |
| **ノードごとの最大タスク数** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>例:Linux H16r VM プール上の Intel MPI

Linux H シリーズのノードのプールで MPI アプリケーションを実行するには、1 つのオプションとして、Azure Marketplace から入手した [CentOS ベースの 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) イメージを使用します。 Linux RDMA ドライバーと Intel MPI はプレインストールされています。 このイメージは、Docker コンテナーのワークロードもサポートしています。

Batch API または Azure portal で、このイメージを使い、必要な数のノードとスケールを指定して、プールを作成します。 次の表に、プール設定の例を示します。

| Setting | 値 |
| ---- | ---- |
| **イメージの種類** | Marketplace (Linux/Windows) |
| **発行元** | OpenLogic |
| **プラン** | CentOS-HPC |
| **SKU** | 7.4 |
| **ノード サイズ** | H16r Standard |
| **ノード間通信が有効** | True |
| **ノードごとの最大タスク数** | 1 |

## <a name="next-steps"></a>次の手順

* Azure Batch プールで MPI ジョブを実行する方法については、[Windows](batch-mpi.md) または [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) の例を参照してください。

* Batch の GPU ワークロードの例については、[Batch Shipyard](https://github.com/Azure/batch-shipyard/) レシピを参照してください。