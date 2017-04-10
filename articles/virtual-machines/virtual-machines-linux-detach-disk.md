---
title: "Linux VM からデータ ディスクを切断する - Azure | Microsoft Docs"
description: "CLI 2.0 または Azure Portal を使用して、Azure の仮想マシンからデータ ディスクをデタッチする方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 9a5eceff35420c1560ff1185a1e4fb1286d74250
ms.lasthandoff: 03/22/2017


---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>データ ディスクを Linux 仮想マシンから切断する方法

仮想マシンに接続されたデータ ディスクが不要になった場合、そのディスクは簡単に切断できます。 そうすれば、ディスクは仮想マシンから削除されますが、ストレージからは削除されません。 

> [!WARNING]
> ディスクを切断した場合、自動的には削除されません。 Premium Storage のサブスクリプションにお申込みいただいている場合は、ディスクのストレージ料金が引き続き発生します。 詳細については [Premium Storage 利用時の料金と課金](../storage/storage-premium-storage.md#pricing-and-billing)に関する記事を参照してください。 
> 
> 

再びディスク上の既存のデータを使用する場合は、同じ仮想マシンや別の仮想マシンに再接続できます。  

## <a name="detach-a-data-disk-using-cli-20"></a>CLI 2.0 を使用してデータ ディスクを切断する

```azurecli
az vm disk detach -g myResourceGroup --vm-name myVm -n myDataDisk
```

ディスクはストレージに残りますが、仮想マシンからは切断されています。


## <a name="detach-a-data-disk-using-the-portal"></a>ポータルを使用してデータ ディスクを切断する方法
1. ポータル ハブで **[仮想マシン]**を選択します。
2. 切断するデータ ディスクが接続されている仮想マシンを選択し、**[停止]** をクリックして VM の割り当てを解除します。
3. 仮想マシンのブレードで、**[ディスク]** を選択します。
4. **[ディスク]** ブレードの上部で、**[編集]** を選択します。
5. **[ディスク]** ブレードで、切断するデータ ディスクの右端にある ![切断ボタンのイメージ](./media/virtual-machines-common-detach-disk/detach.png) 切断ボタンをクリックします。
5. ディスクが削除されたら、ブレードの上部にある [保存] をクリックします。
6. 仮想マシン ブレードで **[概要]** をクリックし、ブレードの上部にある **[開始]** ボタンをクリックして VM を再起動します。

ディスクはストレージに残りますが、仮想マシンからは切断されています。








## <a name="next-steps"></a>次のステップ
データ ディスクを再利用する場合は、[別の VM にそのデータ ディスクをアタッチ](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)します。


