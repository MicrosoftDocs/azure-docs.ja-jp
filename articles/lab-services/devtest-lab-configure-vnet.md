---
title: Azure DevTest Labs で仮想ネットワークを構成する | Microsoft Docs
description: 既存の仮想ネットワークとサブネットを構成し、それらを VM で Azure DevTest Labs と共に使用する方法について説明します
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 6cf3d2f82c98a3caab47ff48a600316747932b72
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390046"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Azure DevTest Labs で仮想ネットワークを構成する
[ラボへの VM の追加](devtest-lab-add-vm.md)に関する記事で説明しているように、ラボで VM を作成する際に、構成されている仮想ネットワークを指定できます。 たとえば、ExpressRoute またはサイト間 VPN で構成されている仮想ネットワークを使用して、VM から企業ネットワーク リソースにアクセスしなければならない場合があります。

この記事では、VM の作成時に選択できるように、ラボの Virtual Network の設定に既存の仮想ネットワークを追加する方法を説明します。

> [!NOTE]
> Azure Virtual Network サービスに関連するコストの詳細については、[Azure Virtual Network の価格](../virtual-network/virtual-networks-overview.md#pricing)に関するページを参照してください。

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Azure ポータルを使用してラボ用の仮想ネットワークを構成する
次に、ラボに既存の仮想ネットワーク (およびサブネット) を追加し、同じラボで VM を作成するときにそれを使用できるようにするための手順を説明します。 

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で目的のラボを選択します。 
1. ラボのメイン ウィンドウで、 **[構成とポリシー]** を選択します。

    ![ラボの構成とポリシーにアクセスする](./media/devtest-lab-configure-vnet/policies-menu.png)
1. **[外部リソース]** セクションで **[仮想ネットワーク]** を選択します。 現在のラボに構成した仮想ネットワークのほか、ラボ用に作成される既定の仮想ネットワークの一覧が表示されます。 
1. **[+ 追加]** を選択します。
   
    ![既存の仮想ネットワークをラボに追加する](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. **[仮想ネットワーク]** ウィンドウで、 **[仮想ネットワークの選択]** を選択します。
   
    ![既存の仮想ネットワークを選択する](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. **[仮想ネットワークの選択]** ウィンドウで、目的の仮想ネットワークを選択します。 サブスクリプションでラボと同じリージョンの下にある仮想ネットワークの一覧が表示されます。
1. 仮想ネットワークを選択すると、 **[仮想ネットワーク]** ウィンドウに戻ります。 下部の一覧からサブネットを選択します。

    ![サブネットの一覧](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    [ラボ サブネット] ウィンドウが表示されます。

    ![[ラボ サブネット] ウィンドウ](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - **ラボ サブネット名**を指定します。
   - ラボ VM の作成にサブネットを使用できるようにするには、 **[仮想マシンの作成に使用]** を選択します。
   - [共有パブリック IP アドレス](devtest-lab-shared-ip.md)を有効にするには、 **[Enable shared public IP (共有パブリック IP を有効にする)]** を選択します。
   - サブネットでパブリック IP アドレスを許可するには、 **[Allow public IP creation (パブリック IP の作成を許可する)]** を選択します。
   - **[ユーザーあたりの最大仮想マシン数]** フィールドに、サブネットごとのユーザーあたりの最大 VM 数を指定します。 無制限の VM 数が必要な場合は、このフィールドを空白のままにします。
1. **[OK]** を選択して、[ラボ サブネット] ウィンドウを閉じます。
1. **[保存]** を選択して、[仮想ネットワーク] ウィンドウを閉じます。

仮想ネットワークが構成されたので、VM の作成時にそれを選択できます。 VM の作成方法および仮想ネットワークの指定方法については、[ラボへの VM の追加](devtest-lab-add-vm.md)に関する記事を参照してください。 

Azure の [Virtual Network ドキュメント](https://docs.microsoft.com/azure/virtual-network)では、VNet を設定および管理して、オンプレミスの ネットワークに接続する方法など、VNet を使用する方法について詳しく説明しています。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次の手順
ラボに目的の仮想ネットワークを追加したら、次は [ラボに VM を追加](devtest-lab-add-vm.md)します。

