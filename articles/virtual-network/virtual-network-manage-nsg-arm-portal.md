---
title: "Azure Portal を使用して NSG を管理する | Microsoft Docs"
description: "Azure Portal を使用して既存の NSG を管理する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5d55679d-57da-457c-97dc-1e1973909ee5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 1fe845d442c7010580d4592f205e92e8ef70e34a
ms.openlocfilehash: e9bcf8a893ff209337f6a5763b631a22f8514e20


---
# <a name="manage-nsgs-using-the-portal"></a>ポータルを使用して NSG を管理する

> [!div class="op_single_selector"]
> * [ポータル](virtual-network-manage-nsg-arm-portal.md)
> * [PowerShell](virtual-network-manage-nsg-arm-ps.md)
> * [Azure CLI](virtual-network-manage-nsg-arm-cli.md)
>

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../resource-manager-deployment-model.md) 2 種類のデプロイメント モデルがあります。 この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、クラシック デプロイメント モデルではなくこのモデルをお勧めします。
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>情報を取得する
既存の NSG を表示し、既存の NSG の規則を取得し、NSG が関連付けられているリソースを検索することができます。

### <a name="view-existing-nsgs"></a>既存の NSG を表示する

サブスクリプションの既存の NSG をすべて表示するには、次の手順を実行します。

1. ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。

2. **[参照] > ** > **[ネットワーク セキュリティ グループ]** をクリックします。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. **[ネットワーク セキュリティ グループ]** ブレードで NSG の一覧を確認します。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

### <a name="view-nsgs-in-a-resource-group"></a>リソース グループ内で NSG を表示する

**RG-NSG** リソース グループの NSG の一覧を表示するには、次の手順を実行します。

1. **[リソース グループ]** > **[RG-NSG]** > **[...]** をクリックします。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. リソースの一覧で、以下の **[リソース]** ブレードのように NSG アイコンが表示されている項目を探します。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure4.png)

### <a name="list-all-rules-for-an-nsg"></a>NSG のすべての規則を一覧表示する

**NSG-FrontEnd**という NSG の規則を表示するには、次の手順を実行します。

1. **[ネットワーク セキュリティ グループ]** ブレードまたは上の **[リソース]** ブレードの **[NSG-FrontEnd]** をクリックします。

2. **[設定]** タブの **[受信セキュリティ規則]** をクリックします。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. 次のように **[受信セキュリティ規則]** ブレードが表示されます。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. **[設定]** タブの **[送信セキュリティ規則]** をクリックして、送信規則を表示します。

    > [!NOTE]
    > 既定の規則を表示するには、規則が表示されたブレードの上部にある **[既定の規則]** アイコンをクリックします。
    >

### <a name="view-nsgs-associations"></a>NSG の関連付けを表示する

**NSG-FrontEnd** NSG が関連付けられているリソースを表示するには、次の手順を実行します。

1. **[ネットワーク セキュリティ グループ]** ブレードまたは上の **[リソース]** ブレードの **[NSG-FrontEnd]** をクリックします。

2. **[設定]** タブの **[サブネット]** をクリックして、NSG に関連付けられているサブネットを表示します。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. **[設定]** タブの **[ネットワーク インターフェイス]** をクリックして、NSG に関連付けられている NIC を表示します。

## <a name="manage-rules"></a>規則を管理する
既存の NSG に規則を追加し、既存の規則を編集し、規則を削除することができます。

### <a name="add-a-rule"></a>規則を追加する
任意のコンピューターから **NSG-FrontEnd** NSG へのポート **443** に対する**受信**トラフィックを許可する規則を追加するには、次の手順を実行します。

1. **[ネットワーク セキュリティ グループ]** ブレードまたは上の **[リソース]** ブレードの **[NSG-FrontEnd]** をクリックします。
2. **[設定]** タブの **[受信セキュリティ規則]** をクリックします。
3. **[受信セキュリティ規則]** ブレードで **[追加]** をクリックします。 **[受信セキュリティ規則の追加]** ブレードに次のように値を入力し、**[OK]** をクリックします。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

    数秒後に、 **[受信セキュリティ規則]** ブレードに新しい規則が表示されます。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>規則を変更する
上の手順で作成した規則を、 **インターネット** からの受信トラフィックのみを許可する規則に変更するには、次の手順を実行します。

1. **[ネットワーク セキュリティ グループ]** ブレードまたは上の **[リソース]** ブレードの **[NSG-FrontEnd]** をクリックします。
2. **[設定]** タブで、上で作成した規則をクリックします。
3. **[allow-https]** ブレードで、**[ソース]** プロパティを次のように変更し、**[保存]** をクリックします。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>規則を削除する

先ほど作成した規則を削除するには、次の手順を実行します。

1. **[ネットワーク セキュリティ グループ]** ブレードまたは上の **[リソース]** ブレードの **[NSG-FrontEnd]** をクリックします。
2. **[設定]** タブで、上で作成した規則をクリックします。
3. **[allow-https]** ブレードで、**[削除]** をクリックし、**[はい]** をクリックします。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>関連付けを管理する
NSG をサブネットと NIC に関連付けることができます。 また、NSG とリソースの関連付けを解除することもできます。

### <a name="associate-an-nsg-to-a-nic"></a>NSG を NIC に関連付ける
**NSG-FrontEnd** NSG を **TestNICWeb1** NIC に関連付けるには、次の手順を実行します。

1. **[ネットワーク セキュリティ グループ]** ブレードまたは上の **[リソース]** ブレードの **[NSG-FrontEnd]** をクリックします。
2. **[設定]** タブの **[ネットワーク インターフェイス]** > **[関連付け]** > **[TestNICWeb1]** をクリックします。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>NSG と NIC の関連付けを解除する

**NSG-FrontEnd** NSG と **TestNICWeb1** NIC の関連付けを解除するには、次の手順を実行します。

1. Azure Portal で **[リソース グループ]** > **[RG-NSG]** > **[...]** > **[TestNICWeb1]** をクリックします。

2. **[TestNICWeb1]** ブレードで **[セキュリティの変更]** > **[なし]** をクリックします。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

> [!NOTE]
> このブレードを使用して、NIC を既存の任意の NSG に関連付けることもできます。
>

### <a name="dissociate-an-nsg-from-a-subnet"></a>NSG とサブネットの関連付けを解除する

**NSG-FrontEnd** NSG と **FrontEnd** サブネットの関連付けを解除するには、次の手順を実行します。

1. Azure Portalで **[リソース グループ]** > **[RG-NSG]** > **[...]** > **[TestVNet]** をクリックします。

2. **[設定]** ブレードで、**[サブネット]** > **[FrontEnd]** > **[ネットワーク セキュリティ グループ]** > **[なし]** をクリックします。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. **[FrontEnd]** ブレードで、**[保存]** をクリックします。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>NSG サブネットへの関連付け

**NSG-FrontEnd** NSG を **FronEnd** サブネットにもう一度関連付けるには、次の手順を実行します。

1. Azure Portalで **[リソース グループ]** > **[RG-NSG]** > **[...]** > **[TestVNet]** をクリックします。
2. **[設定]** ブレードで、**[サブネット]** > **[FrontEnd]** > **[ネットワーク セキュリティ グループ]** > **[NSG-FrontEnd]** をクリックします。
3. **[FrontEnd]** ブレードで、**[保存]** をクリックします。

> [!NOTE]
> NSG の **[設定]** ブレードから、NSG をサブネットに関連付けることもできます。
>

## <a name="delete-an-nsg"></a>NSG の削除
NSG に関連付けられているリソースがない場合にのみ、NSG を削除できます。 NSG を削除するには、次の手順を実行します。

1. Azure Portal で **[リソース グループ]** > **[RG-NSG]** > **[...]** > **[NSG-FrontEnd]** をクリックします。
2. **[設定]** ブレードで、**[ネットワーク インターフェイス]** をクリックします。
3. NIC が表示される場合は、NIC をクリックし、「 [NSG と NIC の関連付けを解除する](#Dissociate-an-NSG-from-a-NIC)」の手順 2 を実行します。
4. 各 NIC について手順 3 を繰り返します。
5. **[設定]** ブレードで **[サブネット]** をクリックします。
6. サブネットが表示される場合は、サブネットをクリックし、「 [NSG とサブネットの関連付けを解除する](#Dissociate-an-NSG-from-a-subnet)」の手順 2 と 3 を実行します。
7. 左側で **[NSG-FrontEnd]** ブレードまでスクロールし、**[削除]** > **[はい]** をクリックします。

    ![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>次のステップ
* [ログを有効にします](virtual-network-nsg-manage-log.md) 。



<!--HONumber=Jan17_HO1-->


