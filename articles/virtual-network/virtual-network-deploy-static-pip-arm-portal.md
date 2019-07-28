---
title: 静的パブリック IP アドレスを持つ VM を作成する - Azure Portal | Microsoft Docs
description: Azure Portal を使用して、静的パブリック IP アドレスを持つ VM を作成する方法について説明します。
services: virtual-network
documentationcenter: na
author: kumudd
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: f6914a9894db07a40b372a8c247a7623c3957d86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64692421"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Azure portal を使用して静的パブリック IP アドレスを持つ仮想マシンを作成する

静的パブリック IP アドレスを持つ仮想マシンを作成できます。 パブリック IP アドレスを使用して、インターネットから仮想マシンへの通信を行うことができます。 動的アドレスではなく静的パブリック IP アドレスを割り当てることで、アドレスの変更がないことが保証されます。 [静的パブリック IP アドレス](virtual-network-ip-addresses-overview-arm.md#allocation-method)の詳細を参照してください。 既存の仮想マシンに割り当てられたパブリック IP アドレスの動的から静的への変更、またはプライベート IP アドレスの操作については、[IP アドレスの追加、変更、または削除](virtual-network-network-interface-addresses.md)に関する記事を参照してください。 パブリック IP アドレスには[ごくわずかな料金](https://azure.microsoft.com/pricing/details/ip-addresses)がかかり、サブスクリプションごとに使用できるパブリック IP アドレスの数には[制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)があります。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[Compute]** を選択し、 **[Windows Server 2016 VM]** または別の任意のオペレーティング システムを選択します。
3. 次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[OK]** を選択します。

    |Setting|値|
    |---|---|
    |Name|myVM|
    |ユーザー名| 任意のユーザー名を入力します。|
    |パスワード| 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
    |サブスクリプション| サブスクリプションを選択します。|
    |リソース グループ| **[既存のものを使用]** を選択し、 **[myResourceGroup]** を選択します。|
    |Location| **[米国東部]** を選択します。|

4. VM のサイズを選択して、 **[選択]** を選択します。
5. **[設定]** で **[パブリック IP アドレス]** を選択します。
6. 次の図に示すように、「*myPublicIpAddress*」と入力し、 **[静的]** を選択してから **[OK]** を選択します。

   ![[静的] の選択](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   パブリック IP アドレスが Standard SKU である必要がある場合は、 **[SKU]** で **[Standard]** を選択します。 [パブリック IP アドレスの SKU](virtual-network-ip-addresses-overview-arm.md#sku) の詳細を確認してください。 パブリック Azure Load Balancer のバックエンド プールに仮想マシンを追加する場合は、仮想マシンのパブリック IP アドレスの SKU がロード バランサーのパブリック IP アドレスの SKU と一致する必要があります。 詳細については、[Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus) に関する記事を参照してください。

6. **[パブリック受信ポートを選択]** で、ポートを選択するか、ポートなしを選択します。 インターネットから Windows Server 仮想マシンへのリモート アクセスを有効にするために、ポータル 3389 が選択されています。 運用環境のワークロードでは、インターネットからポート 3389 を開くことはお勧めしません。

   ![ポートの選択](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. 残りの既定値を受け入れて、 **[OK]** を選択します。
8. **[概要]** ページで、 **[作成]** を選択します。 仮想マシンのデプロイには、数分かかります。
9. 仮想マシンをデプロイしたら、ポータルの上部にある検索ボックスに「*myPublicIpAddress*」と入力します。 検索結果に **myPublicIpAddress** が表示されたら、それを選びます。
10. 次の図に示すように、割り当てられているパブリック IP アドレスと、そのアドレスが **myVM** 仮想マシンに割り当てられていることが表示されます。

    ![パブリック IP アドレスの表示](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure では、仮想マシンを作成したリージョンで使用されるアドレスからパブリック IP アドレスが割り当てられます。 Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519)、[US Government](https://www.microsoft.com/download/details.aspx?id=57063)、[China](https://www.microsoft.com/download/details.aspx?id=57062)、および [Germany](https://www.microsoft.com/download/details.aspx?id=57064) クラウドの範囲 (プレフィックス) の一覧をダウンロードできます。

11. **[構成]** を選択して、割り当てが **[静的]** であることを確認します。

    ![パブリック IP アドレスの表示](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> 仮想マシンのオペレーティング システム内の IP アドレス設定は変更しないでください。 オペレーティング システムは、Azure のパブリック IP アドレスを認識しません。 プライベート IP アドレスの設定をオペレーティング システムに追加できますが、必要でない限り、この操作は行わないことをお勧めします。必要な場合は、[オペレーティング システムへのプライベート IP アドレスの追加](virtual-network-network-interface-addresses.md#private)に関する記事を読んだ後で行うことをお勧めします。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループとそれに含まれるすべてのリソースが不要になったら、それらを削除します。

1. ポータル上部の **[検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **[myResourceGroup]** が表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[TYPE THE RESOURCE GROUP NAME:]\(リソース グループ名を入力してください:\)** に「*myResourceGroup*」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

- Azure の[パブリック IP アドレス](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)の詳細を確認する
- [パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)の詳細を確認する
- [プライベート IP アドレス](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)と Azure 仮想マシンへの[静的プライベート IP アドレス](virtual-network-network-interface-addresses.md#add-ip-addresses)の割り当ての詳細を確認する
- [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の仮想マシンの作成の詳細を確認する