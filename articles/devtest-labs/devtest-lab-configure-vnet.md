---
title: Azure DevTest Labs で仮想ネットワークを構成する | Microsoft Docs
description: 既存の仮想ネットワークとサブネットを構成し、それらを VM で Azure DevTest Labs と共に使用する方法について説明します
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 21fe23b6be6fdc924c5502ff97f9c03a3f3e759d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87283556"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Azure DevTest Labs で仮想ネットワークを構成する
[ラボへの VM の追加](devtest-lab-add-vm.md)に関する記事で説明しているように、ラボで VM を作成する際に、構成されている仮想ネットワークを指定できます。 たとえば、ExpressRoute またはサイト間 VPN で構成されている仮想ネットワークを使用して、VM から企業ネットワーク リソースにアクセスしなければならない場合があります。

この記事では、VM の作成時に選択できるように、ラボの Virtual Network の設定に既存の仮想ネットワークを追加する方法を説明します。

> [!NOTE]
> Azure Virtual Network サービスに関連するコストの詳細については、[Azure Virtual Network の価格](../virtual-network/virtual-networks-overview.md#pricing)に関するページを参照してください。

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Azure ポータルを使用してラボ用の仮想ネットワークを構成する
次に、ラボに既存の仮想ネットワーク (およびサブネット) を追加し、同じラボで VM を作成するときにそれを使用できるようにするための手順を説明します。 

1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で目的のラボを選択します。 
1. ラボのメイン ウィンドウで、**[構成とポリシー]** を選択します。

    ![ラボの [構成とポリシー] にアクセスする](./media/devtest-lab-configure-vnet/policies-menu.png)
1. **[外部リソース]** セクションで **[仮想ネットワーク]** を選択します。 現在のラボに構成した仮想ネットワークのほか、ラボ用に作成される既定の仮想ネットワークの一覧が表示されます。 
1. **[+ 追加]** を選択します。
   
    ![既存の仮想ネットワークをラボに追加する](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. **[仮想ネットワーク]** ウィンドウで、**[仮想ネットワークの選択]** を選択します。
   
    ![既存の仮想ネットワークを選択する](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. **[仮想ネットワークの選択]** ウィンドウで、目的の仮想ネットワークを選択します。 サブスクリプションでラボと同じリージョンの下にある仮想ネットワークの一覧が表示されます。
1. 仮想ネットワークを選択すると、**[仮想ネットワーク]** ウィンドウに戻ります。 下部の一覧からサブネットを選択します。

    ![サブネットの一覧](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    [ラボ サブネット] ウィンドウが表示されます。

    ![[ラボ サブネット] ウィンドウ](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - **ラボ サブネット名** を指定します。
   - ラボ VM の作成にサブネットを使用できるようにするには、 **[仮想マシンの作成に使用]** を選択します。
   - [共有パブリック IP アドレス](devtest-lab-shared-ip.md)を有効にするには、**[Enable shared public IP (共有パブリック IP を有効にする)]** を選択します。
   - サブネットでパブリック IP アドレスを許可するには、**[Allow public IP creation (パブリック IP の作成を許可する)]** を選択します。
   - **[ユーザーあたりの最大仮想マシン数]** フィールドに、サブネットごとのユーザーあたりの最大 VM 数を指定します。 無制限の VM 数が必要な場合は、このフィールドを空白のままにします。
1. **[OK]** を選択して、[ラボ サブネット] ウィンドウを閉じます。
1. **[保存]** を選択して、[仮想ネットワーク] ウィンドウを閉じます。

仮想ネットワークが構成されたので、VM の作成時にそれを選択できます。 VM の作成方法および仮想ネットワークの指定方法については、[ラボへの VM の追加](devtest-lab-add-vm.md)に関する記事を参照してください。 

Azure の [Virtual Network ドキュメント](../virtual-network/index.yml)では、VNet を設定および管理して、オンプレミスの ネットワークに接続する方法など、VNet を使用する方法について詳しく説明しています。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次のステップ
ラボに目的の仮想ネットワークを追加したら、次は [ラボに VM を追加](devtest-lab-add-vm.md)します。
