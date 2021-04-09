---
title: 接続されていない Azure NIC の検索と削除
description: Azure CLI を使用して、VM に接続されていない Azure NIC を検索して削除する方法
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 5b28226cfe6bc51a2619c4dd63e666ddd51dad2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016201"
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

## <a name="next-steps"></a>次のステップ

Azure で仮想ネットワークを作成して管理する方法の詳細については、[VM ネットワークの作成と管理](tutorial-virtual-network.md)についてのトピックを参照してください。
