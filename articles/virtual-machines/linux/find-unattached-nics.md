---
title: 接続されていない Azure NIC の検索と削除 | Microsoft Docs
description: Azure CLI を使用して、VM に接続されていない Azure NIC を検索して削除する方法
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 1665b5fa8d2bfd63982bcffd1d5251214ff3586b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083321"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Azure VM の接続されていないネットワーク インターフェイス カード (NIC) を検索して削除する方法
Azure 内の仮想マシン (VM) を削除しても、ネットワーク インターフェイス カード (NIC) は既定では削除されません。 複数の VM を作成して削除すると、未使用の NIC は内部 IP アドレスのリースを引き続き使用します。 他の VM NIC を作成するときに、サブネットのアドレス空間で IP リースを取得できない可能性があります。 この記事では、接続されていない NIC を検索して削除する方法を示します。

## <a name="find-and-delete-unattached-nics"></a>接続されていない NIC の検索と削除

NIC の *VirtualMachine* プロパティは、NIC の接続先 VM の ID とリソース グループを格納しています。 次のスクリプトは、サブスクリプション内のすべての NIC をループ処理し、*virtualMachine* プロパティが null かどうかをチェックします。 このプロパティが null の場合、その NIC は VM に接続されていません。

接続されていないすべての NIC を表示するため、まず、*deleteUnattachedNics* 変数を *0* に設定してこのスクリプトを実行することを強くお勧めします。 一覧の出力の確認後に、接続されていない NIC をすべて削除するには、*deleteUnattachedNics* を *1* に設定してスクリプトを実行します。

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>次の手順

Azure で仮想ネットワークを作成して管理する方法の詳細については、[VM ネットワークの作成と管理](tutorial-virtual-network.md)についてのトピックを参照してください。
