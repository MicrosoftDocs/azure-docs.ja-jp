---
title: "Azure DevTest Labs で仮想ネットワークを構成する | Microsoft Docs"
description: "既存の仮想ネットワークとサブネットを構成し、それらを VM で Azure DevTest Labs と共に使用する方法について説明します"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 8c5ce9570541def245ca33f5841a8392e73569b4
ms.lasthandoff: 03/10/2017


---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Azure DevTest Labs で仮想ネットワークを構成する
「 [アーティファクトを含む VM を Azure DevTest ラボに追加する](devtest-lab-add-vm-with-artifacts.md)」の記事で説明しているように、ラボで VM を作成する際に、構成されている仮想ネットワークを指定できます。 これを行う&1; つのシナリオは、ExpressRoute またはサイト間 VPN で構成されている仮想ネットワークを使用して、VM から企業ネットワーク リソースにアクセスする必要がある場合です。 以下のセクションでは、VM の作成時に選択できるように、ラボの Virtual Network の設定に既存の仮想ネットワークを追加する方法を説明します。

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Azure ポータルを使用してラボ用の仮想ネットワークを構成する
次に、ラボに既存の仮想ネットワーク (およびサブネット) を追加し、同じラボで VM を作成するときにそれを使用できるようにするための手順を説明します。 

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
2. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。 
4. ラボのブレードで、 **[構成]**を選択します。
5. ラボの **[構成]** ブレードで、**[仮想ネットワーク]** を選択します。
6. **[仮想ネットワーク]** ブレードに、現在のラボに構成した仮想ネットワークのほか、ラボ用に作成される既定の仮想ネットワークの一覧が表示されます。 
7. **[+ 追加]**を選択します。
   
    ![既存の仮想ネットワークをラボに追加する](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
8. **[仮想ネットワーク]** ブレードで、**[Select virtual network (仮想ネットワークの選択)]** を選択します。
   
    ![既存の仮想ネットワークを選択する](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
9. **[仮想ネットワークの選択]** ブレードで、目的の仮想ネットワークを選択します。 ブレードには、サブスクリプションのラボと同じリージョンの下にあるすべての仮想ネットワークが表示されます。  
10. 仮想ネットワークを選択すると、**[仮想ネットワーク]** ブレードに戻ります。ブレード下部に表示されている一覧でサブネットをクリックします。

    ![サブネットの一覧](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    [Lab Subnet (ラボ サブネット)] ブレードが表示されます。

    ![[Lab Subnet (ラボ サブネット)] ブレード](./media/devtest-lab-configure-vnet/lab-subnet.png)

11. **ラボ サブネット名**を指定します。
12. ラボ VM の作成にサブネットを使用できるようにするには、**[仮想マシンの作成に使用]** を選択します。
13. [共有パブリック IP アドレス](devtest-lab-shared-ip.md)を有効にするには、**[Enable shared public IP (共有パブリック IP を有効にする)]** を選択します。
14. サブネットでパブリック IP アドレスを許可するには、**[Allow public IP creation (パブリック IP の作成を許可する)]** を選択します。
15. **[ユーザーあたりの最大仮想マシン数]** フィールドに、サブネットごとのユーザーあたりの最大 VM 数を指定します。 無制限の VM 数が必要な場合は、このフィールドを空白のままにします。
16. **[OK]** を選択して、[Lab Sabunet (ラボ サブネット)] ブレードを閉じます。
17. **[保存]** を選択して、[仮想ネットワーク] ブレードを閉じます。
18. 仮想ネットワークが構成されたので、VM の作成時にそれを選択できます。 
    VM の作成方法および仮想ネットワークの指定方法については、「 [アーティファクトを含む VM をラボに追加する](devtest-lab-add-vm-with-artifacts.md)」を参照してください。 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次のステップ
ラボに目的の仮想ネットワークを追加したら、次は [ラボに VM を追加](devtest-lab-add-vm-with-artifacts.md)します。


