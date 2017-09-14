---
title: "Azure での Linux HPC Pack クラスター オプション | Microsoft Docs"
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
ms.date: 08/25/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 99aee6b73242d21842b02ded8c9e4e25bc5cedb4
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>Azure で Linux HPC ワークロード用のクラスターを作成して管理するための HPC Pack のオプション
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

この記事では、HPC Pack を使用して Linux ワークロードを実行するオプションについて説明します。 [HPC Pack を使用して Windows HPC ワークロードを実行する](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)オプションもあります。

## <a name="hpc-pack-cluster-in-azure-vms"></a>Azure VM の HPC Pack クラスター
### <a name="azure-templates"></a>Azure テンプレート
* (GitHub) [HPC Pack 2016 クラスター テンプレート](https://github.com/MsHpcPack/HPCPack2016)
* (Marketplace) [HPC Pack cluster for Linux workloads (Linux ワークロード用の HPC Pack クラスター)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)
* (クイックスタート) [Create an HPC cluster with Linux compute nodes (Linux コンピューティング ノードがある HPC クラスターを作成する)](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>HPC Pack 2012 R2 の PowerShell デプロイメント スクリプト
* [Create an HPC cluster with the Linux HPC Pack IaaS deployment script (HPC Pack IaaS デプロイメント スクリプトを使用した Linux HPC クラスターの作成)](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Tutorials (チュートリアル)
* [チュートリアル: Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [チュートリアル: Azure の Linux コンピューティング ノード上で Microsoft HPC Pack を使用して NAMD を実行する](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [チュートリアル: Azure の Linux RDMA クラスター上で Microsoft HPC Pack を使用して OpenFOAM を実行する](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Tutorial: Run STAR-CCM+ with Microsoft HPC Pack on a Linux RDMA cluster in Azure (チュートリアル: Azure の Linux RDMA クラスターで Microsoft HPC Pack を使用して STAR-CCM+ を実行する)](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="cluster-management"></a>クラスターの管理
* [オンプレミス コンピューターから Azure にデプロイされた HPC Pack クラスターに HPC ジョブを送信する](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [HPC Pack でのジョブ管理](https://technet.microsoft.com/library/jj899585.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>MPI ワークロードのための RDMA のクラスター
* [チュートリアル: Azure の Linux RDMA クラスター上で Microsoft HPC Pack を使用して OpenFOAM を実行する](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)


