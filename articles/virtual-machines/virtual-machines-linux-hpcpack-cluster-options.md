---
title: "クラウドでの Linux HPC Pack クラスター オプション | Microsoft Docs"
description: "Microsoft HPC Pack を使用して Azure クラウドで Linux ハイ パフォーマンス コンピューティング (HPC) クラスターを作成および管理するためのオプションについて学習します。"
services: virtual-machines-linux,cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 02/06/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: a0d2b29bd452228fbbb7d344b89ac84f17238e0c
ms.lasthandoff: 03/27/2017


---
# <a name="options-with-hpc-pack-to-create-and-manage-an-hpc-cluster-in-azure-for-linux-workloads"></a>Azure で Linux ワークロード用の HPC クラスターを作成して管理するために使用する HPC Pack のオプション
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

この記事では、HPC Pack を使用して Linux ワークロードを実行するオプションについて説明します。 [HPC Pack を使用して Windows HPC ワークロードを実行する](virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)オプションもあります。

## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Azure VM での HPC Pack クラスターの実行
### <a name="azure-templates"></a>Azure テンプレート
* (GitHub) [HPC Pack 2016 クラスター テンプレート](https://github.com/MsHpcPack/HPCPack2016)
* (Marketplace) [HPC Pack cluster for Linux workloads (Linux ワークロード用の HPC Pack クラスター)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)
* (クイックスタート) [Create an HPC cluster with Linux compute nodes (Linux コンピューティング ノードがある HPC クラスターを作成する)](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="powershell-deployment-script"></a>PowerShell デプロイメント スクリプト
* [Create an HPC cluster with the Linux HPC Pack IaaS deployment script (HPC Pack IaaS デプロイメント スクリプトを使用した Linux HPC クラスターの作成)](linux/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Tutorials (チュートリアル)
* [チュートリアル: Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](linux/classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [チュートリアル: Azure の Linux コンピューティング ノード上で Microsoft HPC Pack を使用して NAMD を実行する](linux/classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [チュートリアル: Azure の Linux RDMA クラスター上で Microsoft HPC Pack を使用して OpenFOAM を実行する](linux/classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Tutorial: Run STAR-CCM+ with Microsoft HPC Pack on a Linux RDMA cluster in Azure (チュートリアル: Azure の Linux RDMA クラスターで Microsoft HPC Pack を使用して STAR-CCM+ を実行する)](linux/classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="cluster-management"></a>クラスターの管理
* [オンプレミス コンピューターから Azure にデプロイされた HPC Pack クラスターに HPC ジョブを送信する](virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [HPC Pack でのジョブ管理](https://technet.microsoft.com/library/jj899585.aspx)

## <a name="create-rdma-clusters-for-mpi-workloads"></a>MPI ワークロードのための RDMA のクラスターの作成
* [チュートリアル: Azure の Linux RDMA クラスター上で Microsoft HPC Pack を使用して OpenFOAM を実行する](linux/classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)


