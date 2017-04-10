---
title: "クラウドでの Windows HPC Pack クラスター オプション | Microsoft Docs"
description: "Microsoft HPC Pack を使用して Azure クラウドで Windows ハイ パフォーマンス コンピューティング (HPC) クラスターを作成および管理するためのオプションについて学習します。"
services: virtual-machines-windows,cloud-services,batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 02/06/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: b008f11070077372487f8aede604017033ae21e4
ms.lasthandoff: 03/25/2017


---
# <a name="options-with-hpc-pack-to-create-and-manage-a-windows-hpc-cluster-in-azure"></a>Azure で Windows HPC クラスターを作成して管理するために使用する HPC Pack のオプション
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

この記事では、Windows ワークロードの実行に使用する HPC Pack クラスターを作成するためのオプションについて説明します。 また、[Linux HPC ワークロード](virtual-machines-linux-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を実行するのに使用する HPC Pack クラスターを作成するためのオプションもあります。


## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Azure VM での HPC Pack クラスターの実行
### <a name="azure-templates"></a>Azure テンプレート
* (GitHub) [HPC Pack 2016 クラスター テンプレート](https://github.com/MsHpcPack/HPCPack2016)
* (Marketplace) [HPC Pack cluster for Windows workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)
* (Marketplace) [HPC Pack cluster for Excel workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)
* (クイックスタート) [HPC クラスターを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (クイックスタート) [Create an HPC cluster with custom compute node image (カスタム コンピューティング ノード イメージを使用した HPC クラスターの作成)](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Azure VM イメージ
* [Windows Server 2016 上の HPC Pack 2016 ヘッド ノード](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2016?tab=Overview)
* [Windows Server 2016 上の HPC Pack 2016 コンピューティング ノード](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2016?tab=Overview)
* [Windows Server 2012 R2 上の HPC Pack 2016 ヘッド ノード](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2012R2?tab=Overview)
* [Windows Server 2012 R2 上の HPC Pack 2016 コンピューティング ノード](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2012R2?tab=Overview)
* [Windows Server 2012 R2 上の HPC Pack 2012 R2 ヘッド ノード](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)
* [Windows Server 2012 R2 上の HPC Pack 2012 R2 コンピューティング ノード](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)
* [Windows Server 2012 R2 上の HPC Pack コンピューティング ノードと Excel](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### <a name="powershell-deployment-script"></a>PowerShell デプロイメント スクリプト
* [HPC Pack IaaS デプロイ スクリプトを使用し、Windows VM でハイ パフォーマンス コンピューティング (HPC) クラスターを作成する](windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Tutorials (チュートリアル)
* [チュートリアル: Azure に HPC Pack 2016 クラスターをデプロイする](virtual-machines-windows-hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [チュートリアル: Azure の HPC Pack クラスターで Excel と SOA ワークロードを実行する](virtual-machines-windows-excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="manual-deployment-with-the-azure-portal"></a>Azure ポータルを使用した手動デプロイメント
* [Marketplace イメージを利用し、Azure VM で HPC Pack クラスターのヘッド ノードを作成する](virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="cluster-management"></a>クラスターの管理
* [Azure の HPC Pack クラスターのコンピューティング ノードの数と可用性を管理する](windows/classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [クラスターのワークロードに合わせて Azure の HPC Pack クラスター リソースを自動的に拡大縮小する](windows/classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [オンプレミス コンピューターから Azure にデプロイされた HPC Pack クラスターに HPC ジョブを送信する](virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [HPC Pack でのジョブ管理](https://technet.microsoft.com/library/jj899585.aspx)
* [Azure Active Directory を使用して Azure の HPC Pack クラスターを管理する](virtual-machines-windows-hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="add-worker-role-nodes-to-an-hpc-pack-cluster"></a>HPC Pack クラスターへの worker ロールの追加
* [HPC Pack を使用した Azure ワーカー インスタンスへのバースト](https://technet.microsoft.com/library/gg481749.aspx)
* [Azure で HPC Pack を使用してハイブリッド クラスターをセットアップする](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)
* [Azure での HPC Pack クラスターへのオンデマンドの "バースト" ノードの追加](windows/classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="integrate-with-azure-batch"></a>Azure Batch との統合
* [HPC Pack を使用した Azure Batch へのバースト](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="create-rdma-clusters-for-mpi-workloads"></a>MPI ワークロードのための RDMA のクラスターの作成
* [HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する](windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


