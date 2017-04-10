---
title: "クラウド サービスで Windows 仮想マシンを接続する | Microsoft Docs"
description: "クラシック デプロイ モデルで作成された Windows 仮想マシンを、Azure クラウド サービスまたは仮想ネットワークに接続します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: c1cbc802-4352-4d2e-9e49-4ccbd955324b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 49784e26a0110165616bcc1af7bbcfd24acfbd52
ms.lasthandoff: 03/27/2017


---
# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>仮想ネットワークまたはクラウド サービスで構成されるクラシック デプロイ モデルで作成された Windows 仮想マシンに接続する
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

クラシック デプロイ モデルで作成された Windows 仮想マシンは、常にクラウド サービス内に配置されます。 クラウド サービスはコンテナーとして機能し、インターネット経由で仮想マシンにアクセスするために、一意のパブリック DNS 名、パブリック IP アドレス、一連のエンドポイントを提供します。 クラウド サービスは仮想ネットワークに配置できますが、必須ではありません。 [Linux 仮想マシンを仮想ネットワークまたはクラウド サービスと接続する](../../linux/classic/connect-vms.md)こともできます。

クラウド サービスが仮想ネットワーク内に含まれていない場合は、 *スタンドアロン* クラウド サービスといいます。 スタンドアロン クラウド サービス内にある仮想マシンが他の仮想マシンと通信するには、他の仮想マシンのパブリック DNS 名を使用する必要があり、そのトラフィックはインターネットを介して送信されます。 クラウド サービスが仮想ネットワークにある場合、そのクラウド サービス内にある仮想マシンは、トラフィックをインターネットに送信することなく、仮想ネットワーク内の他のすべての仮想マシンと通信できます。

同じスタンドアロン クラウド サービスに仮想マシンを配置する場合は、負荷分散と可用性セットを引き続き使用できます。 詳細については、[仮想マシンの負荷分散](../../virtual-machines-windows-load-balance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページと、「[仮想マシンの可用性管理](../../virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 ただし、仮想マシンをサブネットでまとめることや、スタンドアロン クラウド サービスをオンプレミスのネットワークに接続することはできません。 次に例を示します。

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>次のステップ
仮想マシンを作成したら、サービスやワークロードがデータを格納するための場所として、 [データ ディスクを追加する](attach-disk.md) ことをお勧めします。 


