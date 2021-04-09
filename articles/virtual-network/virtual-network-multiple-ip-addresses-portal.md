---
title: Azure 仮想マシンの複数の IP アドレス - Portal | Microsoft Docs
description: Azure Portal を使用して、仮想マシンに複数の IP アドレスを割り当てる方法 | Resource Manager
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: allensu
ms.openlocfilehash: a0dab64aac8bc5fa68a53fad6cd8e6f6bb4bac03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217143"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Azure Portal を使用して仮想マシンに複数の IP アドレスを割り当てる

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> この記事では、Azure ポータルを使用して Azure Resource Manager デプロイ モデルで仮想マシン (VM) を作成する方法について説明します。 クラシック デプロイ モデルで作成されたリソースには、複数の IP アドレスを割り当てることはできません。 Azure のデプロイ モデルの詳細については、[デプロイ モデルの概要](../azure-resource-manager/management/deployment-models.md)に関する記事をご覧ください。

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>複数の IP アドレスを持つ VM を作成する

複数の IP アドレスまたは静的プライベート IP アドレスを持つ VM を作成する場合は、PowerShell または Azure CLI を使用して VM を作成する必要があります。 その方法については、この記事の上部にある PowerShell または CLI オプションをクリックしてください。 単一の動的プライベート IP アドレスと (必要に応じて) 単一のパブリック IP アドレスを持つ VM を作成できます。 [Windows VM の作成](../virtual-machines/windows/quick-create-portal.md)または [Linux VM の作成](../virtual-machines/linux/quick-create-portal.md)に関する記事の手順に従ってポータルを使用してください。 VM を作成したら、この記事の「[VM に IP アドレスを追加する](#add)」セクションの手順に従ってポータルを使用することにより、IP アドレス タイプを動的から静的に変更して、別の IP アドレスを追加できます。

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>VM に IP アドレスを追加する

プライベート IP アドレスとパブリック IP アドレスを Azure ネットワーク インターフェイスに追加するには、次の手順を実行します。 次のセクションの例では、[シナリオ](#scenario)で説明している 3 つの IP 構成を使用した VM を既に所有していることを前提としていますが、必須ではありません。

### <a name="core-steps"></a><a name="coreadd"></a>主な手順

1. 必要に応じて、 https://portal.azure.com で Azure Portal を開き、サインインします。
2. そのポータルで、 **[その他のサービス]** をクリックし、フィルター ボックスに「*virtual machines*」と入力して、 **[Virtual Machines]** をクリックします。
3. **[Virtual Machines]** ウィンドウで、IP アドレスを追加する VM をクリックします。 **[ネットワーク]** タブに移動します。このページで **[ネットワーク インターフェイス]** をクリックします。 次の図に示すようになります。 


    ![VM に対するパブリック IP アドレスの追加](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. **[ネットワーク インターフェイス]** ウィンドウで、 **[IP 構成]** をクリックします。

5. 選択した NIC について表示されるウィンドウで、 **[IP 構成]** をクリックします。 **[追加]** をクリックし、追加する IP アドレスのタイプに基づいて、以下のセクションのいずれかの手順を完了してから、 **[OK]** をクリックします。 

### <a name="add-a-private-ip-address"></a>プライベート IP アドレスを追加する

新しいプライベート IP アドレスを追加するには、次の手順を完了します。

1. この記事の「[コア ステップ](#coreadd)」セクションの手順を完了し、VM ネットワーク インターフェイスの **[IP 構成]** セクションにいることを確認します。  既定として表示されているサブネット (10.0.0.0/24 など) を確認します。
2. **[追加]** をクリックします。 表示された **[IP 構成の追加]** ウィンドウで、「*IPConfig-4*」という名前の IP 構成を作成し、最終オクテットに新しい数値を選ぶことによって新しい *静的* プライベート IP アドレスを指定し、 **[OK]** をクリックします。  (10.0.0.0/24 のサブネットの場合、IP の例は *10.0.0.7* です。)

    > [!NOTE]
    > 静的 IP アドレスを追加するときは、NIC が接続されているサブネット上に未使用の有効なアドレスを指定する必要があります。 選択したアドレスが利用できない場合はポータルで IP アドレスに X が表示され、別のアドレスを選択する必要があります。

3. [OK] をクリックするとウィンドウが閉じ、新しい IP 構成が表示されます。 **[OK]** をクリックして、 **[IP 構成の追加]** ウィンドウを閉じます。
4. **[追加]** をクリックすると、別の IP 構成を追加できます。または、開いているすべてのブレードを閉じて IP アドレスの追加を完了できます。
5. この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」に記載された手順に従って、プライベート IP アドレスを VM オペレーティング システムに追加します。

### <a name="add-a-public-ip-address"></a>パブリック IP アドレスを追加する

パブリック IP アドレスを追加するには、新しい IP 構成または既存の IP 構成にパブリック IP アドレス リソースを関連付けます。

> [!NOTE]
> パブリック IP アドレスには、わずかな費用がかかります。 IP アドレスの料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 制限の詳細については、[Azure の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)に関する記事をご覧ください。
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>パブリック IP アドレス リソースの作成

パブリック IP アドレスは、パブリック IP アドレス リソースの設定の 1 つです。 IP 構成に関連付ける予定で、まだ IP 構成に関連付けられていないパブリック IP アドレス リソースがある場合は、次の手順を省略して、必要に応じてそれに続く手順の 1 つを実行します。 利用できるパブリック IP アドレス リソースがない場合は、次の手順を実行して 1 つ作成します。

1. 必要に応じて、 https://portal.azure.com で Azure Portal を開き、サインインします。
3. ポータルで、 **[リソースの作成]**  >  **[ネットワーク]**  >  **[パブリック IP アドレス]** の順に選択します。
4. 次の図に示すように、表示される **[パブリック IP アドレスの作成]** ウィンドウで **[名前]** を入力し、 **[IP アドレスの割り当て]** のタイプ、 **[サブスクリプション]** 、 **[リソース グループ]** 、 **[場所]** を選択してから、 **[作成]** をクリックします。

    ![パブリック IP アドレス リソースの作成](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. パブリック IP アドレス リソースを、IP 構成に関連付けるには、次のいずれかの手順を実行します。

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>パブリック IP アドレス リソースを新しい IP 構成に関連付ける

1. この記事の「[主な手順](#coreadd)」で述べた手順を完了します。
2. **[追加]** をクリックします。 表示される **[IP 構成の追加]** ウィンドウで、「*IPConfig-4*」という名前の IP 構成を作成します。 **[パブリック IP アドレス]** を有効にして、表示された **[パブリック IP アドレスの選択]** ウィンドウから利用できる既存のパブリック IP アドレス リソースを選択します。

    パブリック IP アドレス リソースを選択したら、 **[OK]** をクリックするとウィンドウが閉じます。 既存のパブリック IP アドレスがない場合は、この記事の「[パブリック IP アドレス リソースの作成](#create-public-ip)」セクションの手順を実行して、パブリック IP アドレスを作成できます。 

3. 新しい IP 構成を確認します。 プライベート IP アドレスが明示的に割り当てられていないとしても、すべての IP 設定にはプライベート IP アドレスが必要であるため、IP 設定にはプライベート IP アドレスが自動的に割り当てられています。
4. **[追加]** をクリックすると、別の IP 構成を追加できます。または、開いているすべてのブレードを閉じて IP アドレスの追加を完了できます。
5. この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」に記載された、お使いのオペレーティング システム用の手順に従って、プライベート IP アドレスを VM オペレーティング システムに追加します。 オペレーティング システムにパブリック IP アドレスは追加しないでください。

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>パブリック IP アドレス リソースを既存の IP 構成に関連付ける

1. この記事の「[主な手順](#coreadd)」で述べた手順を完了します。
2. パブリック IP アドレス リソースを追加する IP 構成をクリックします。
3. 表示される [IP 構成] ウィンドウで、 **[IP アドレス]** をクリックします。
4. **[パブリック IP アドレスの選択]** ウィンドウで、パブリック IP アドレスを選択します。
5. **[保存]** をクリックしてウィンドウを閉じます。 既存のパブリック IP アドレスがない場合は、この記事の「[パブリック IP アドレス リソースの作成](#create-public-ip)」セクションの手順を実行して、パブリック IP アドレスを作成できます。
3. 新しい IP 構成を確認します。
4. **[追加]** をクリックすると、別の IP 構成を追加できます。または、開いているすべてのブレードを閉じて IP アドレスの追加を完了できます。 オペレーティング システムにパブリック IP アドレスは追加しないでください。


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]