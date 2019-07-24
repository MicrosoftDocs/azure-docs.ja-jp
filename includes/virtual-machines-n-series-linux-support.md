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
ms.openlocfilehash: 3d4c58cd9e7ee0674f42d776c8df6e96faba1ab1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799492"
---
## <a name="supported-distributions-and-drivers"></a>サポートされているディストリビューションとドライバー

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA ドライバー

NC、NCv2、NCv3、ND、および NDv2 シリーズ VM (NV シリーズではオプション) の NVIDIA CUDA ドライバーは、次の表に示されている Linux ディストリビューションでのみサポートされます。 CUDA ドライバーの情報は、公開された時点のものです。 最新の CUDA ドライバーについては、[NVIDIA](https://developer.nvidia.com/cuda-zone) の Web サイトを参照してください。 ディストリビューションには最新の CUDA ドライバーをインストールまたはアップグレードしてください。 

> [!TIP]
> CUDA ドライバーを手動で Linux VM にインストールする代わりに、Azure [データ サイエンス仮想マシン](../articles/machine-learning/data-science-virtual-machine/overview.md) イメージをデプロイすることもできます。 Ubuntu 16.04 LTS または CentOS 7.4 用の DSVM エディションでは、NVIDIA CUDA ドライバーや CUDA Deep Neural Network ライブラリなどのツールが事前にインストールされています。

| ディストリビューション | ドライバー |
| --- | -- | 
| Ubuntu 16.04 LTS、18.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3、7.4、7.5、7.6<br/><br/> CentOS-based 7.3、7.4、7.5、7.6、CentOS-based 7.4 HPC | NVIDIA CUDA 10.1、ドライバー ブランチ R418 |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID ドライバー

Microsoft では、仮想ワークステーションまたは仮想アプリケーションとして使用される NV および NVv2 シリーズ VM 用の NVIDIA GRID ドライバーのインストーラーを再分配します。 次の表に記載されているオペレーティング システム上でのみ、これらの GRID ドライバーを Azure NV VM にインストールします。 これらのドライバーには、Azure での GRID 仮想 GPU ソフトウェアのライセンスが含まれています。 NVIDIA vGPU ソフトウェア ライセンス サーバーを設定する必要はありません。

| ディストリビューション | ドライバー |
| --- | -- |
| Ubuntu 16.04 LTS、18.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3、7.4、7.5、7.6<br/><br/>CentOS-based 7.3、7.4、7.5、7.6 | NVIDIA GRID 7.1、ドライバー ブランチ R410|

> [!WARNING] 
> サード パーティ製ソフトウェアを Red Hat 製品にインストールすると、Red Hat サポート条件に影響を与えることがあります。 [Red Hat のサポート技術情報記事](https://access.redhat.com/articles/1067)を参照してください。
>
