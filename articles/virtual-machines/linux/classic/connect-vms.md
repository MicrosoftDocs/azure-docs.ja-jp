---
title: "クラウド サービスで Linux VM を接続する | Microsoft Docs"
description: "クラシック デプロイ モデルで作成された Linux 仮想マシンを、Azure クラウド サービスまたは仮想ネットワークに接続します。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 2fd23055-6b34-4ef0-88a8-fc19e32fb3c9
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 02b158735fe1cd17d712dc725f8cb3bc1d2a8f51
ms.lasthandoff: 03/27/2017


---
# <a name="connect-linux-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>仮想ネットワークまたはクラウド サービスで構成されるクラシック デプロイ モデルで作成された Linux 仮想マシンに接続します。
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

クラシック デプロイ モデルで作成された Linux 仮想マシンは、常にクラウド サービス内に配置されます。 クラウド サービスはコンテナーとして機能し、インターネット経由で仮想マシンにアクセスするために、一意のパブリック DNS 名、パブリック IP アドレス、一連のエンドポイントを提供します。 クラウド サービスは仮想ネットワークに配置できますが、必須ではありません。 [Windows 仮想マシンを仮想ネットワークまたはクラウド サービスと接続する](../../windows/classic/connect-vms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)こともできます。

クラウド サービスが仮想ネットワーク内に含まれていない場合は、 *スタンドアロン* クラウド サービスといいます。 スタンドアロン クラウド サービス内にある仮想マシンが他の仮想マシンと通信するには、他の仮想マシンのパブリック DNS 名を使用する必要があり、そのトラフィックはインターネットを介して送信されます。 クラウド サービスが仮想ネットワークにある場合、そのクラウド サービス内にある仮想マシンは、トラフィックをインターネットに送信することなく、仮想ネットワーク内の他のすべての仮想マシンと通信できます。

同じスタンドアロン クラウド サービスに仮想マシンを配置する場合は、負荷分散と可用性セットを引き続き使用できます。 詳細については、[仮想マシンの負荷分散](../../virtual-machines-linux-load-balance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページと、「[仮想マシンの可用性管理](../../virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。 ただし、仮想マシンをサブネットでまとめることや、スタンドアロン クラウド サービスをオンプレミスのネットワークに接続することはできません。 次に例を示します。

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>次のステップ
仮想マシンを作成したら、サービスやワークロードがデータを格納するための場所として、 [データ ディスクを追加する](attach-disk.md) ことをお勧めします。 


