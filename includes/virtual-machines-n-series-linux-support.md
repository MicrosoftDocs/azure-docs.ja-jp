---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 99c8ee79c51205c33d14328530f527ad8e3ff7b4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87085464"
---
## <a name="supported-distributions-and-drivers"></a>サポートされているディストリビューションとドライバー

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA ドライバー

NC、NCv2、NCv3、ND、および NDv2 シリーズ VM (NV シリーズではオプション) の NVIDIA CUDA ドライバーは、次の表に示されている Linux ディストリビューションでのみサポートされます。 CUDA ドライバーの情報は、公開された時点のものです。 最新の CUDA ドライバーとサポートされているオペレーティング システムについては、[NVIDIA](https://developer.nvidia.com/cuda-zone) の Web サイトを参照してください。 ディストリビューションには最新の CUDA ドライバーをインストールまたはアップグレードしてください。 

> [!TIP]
> CUDA ドライバーを手動で Linux VM にインストールする代わりに、Azure [データ サイエンス仮想マシン](../articles/machine-learning/data-science-virtual-machine/overview.md) イメージをデプロイすることもできます。 Ubuntu 16.04 LTS または CentOS 7.4 用の DSVM エディションでは、NVIDIA CUDA ドライバーや CUDA Deep Neural Network ライブラリなどのツールが事前にインストールされています。


### <a name="nvidia-grid-drivers"></a>NVIDIA GRID ドライバー

Microsoft では、仮想ワークステーションまたは仮想アプリケーションとして使用される NV および NVv3 シリーズ VM 用の NVIDIA GRID ドライバーのインストーラーを再分配します。 次の表に記載されているオペレーティング システム上でのみ、これらの GRID ドライバーを Azure NV VM にインストールします。 これらのドライバーには、Azure での GRID 仮想 GPU ソフトウェアのライセンスが含まれています。 NVIDIA vGPU ソフトウェア ライセンス サーバーを設定する必要はありません。

Azure によって再頒布された GRID ドライバーは、NV シリーズ以外の VM (NC、NCv2、NCv3、ND、NDv2 シリーズの VM など) では機能しません。

| Distribution | Driver |
| --- | -- |
|Ubuntu 18.04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.6 から 7.8、8.0、8.1<br/><br/>CentOS-based 7.6、7.7、8.0、8 (1911)<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA GRID 11、ドライバー ブランチ R450|

> [!WARNING] 
> サード パーティ製ソフトウェアを Red Hat 製品にインストールすると、Red Hat サポート条件に影響を与えることがあります。 [Red Hat のサポート技術情報記事](https://access.redhat.com/articles/1067)を参照してください。
>
