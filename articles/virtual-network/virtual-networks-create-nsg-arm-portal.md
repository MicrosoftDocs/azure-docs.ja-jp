---
title: "ネットワーク セキュリティ グループの管理 - Azure Portal | Microsoft Docs"
description: "Azure Portal を使用してネットワーク セキュリティ グループを管理する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: faee5ac8-f4c4-4f97-ade5-197a37aad496
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: ecb4fb4608628f5a1bd54fac6af19fecfa4508f2
ms.lasthandoff: 02/28/2017


---
# <a name="manage-network-security-groups-using-the-azure-portal"></a>Azure Portal を使用したネットワーク セキュリティ グループの管理

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、リソース マネージャーのデプロイ モデルについて説明します。 [クラシック デプロイ モデルで NSG を作成](virtual-networks-create-nsg-classic-ps.md)することもできます。

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

以下の PowerShell のサンプル コマンドでは、上記シナリオに基づいて単純な環境が既に作成されていると想定します。 このドキュメントに表示されているコマンドを実行するには、まず [このテンプレート](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd)をデプロイしてテスト環境を構築してから **[Azure にデプロイ]**をクリックし、必要に応じて既定のパラメーター値を置き換えてから、ポータルの指示に従います。 次の手順では、テンプレートのデプロイ先のリソース グループの名前として **RG-NSG** を使用します。

## <a name="create-the-nsg-frontend-nsg"></a>NSG-FrontEnd NSG を作成する
上記のシナリオのように **NSG-FrontEnd** という名前の NSG を作成するには、次の手順を実行します。

1. ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. **[参照] > ** > **[ネットワーク セキュリティ グループ]** をクリックします。
   
    ![Azure ポータル - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. **[ネットワーク セキュリティ グループ]** ブレードで、**[追加]** をクリックします。
   
    ![Azure ポータル - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. **[ネットワーク セキュリティ グループの作成]** ブレードで、*RG-NSG* リソース グループに *NSG FrontEnd* という名前の NSG を作成し、**[作成]** をクリックします。
   
    ![Azure ポータル - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>既存の NSG に規則を作成する
Azure ポータルから既存の NSG に規則を作成するには、次の手順を実行します。

1. **[参照] > ** > **[ネットワーク セキュリティ グループ]** をクリックします。
2. NSG の一覧で、 **NSG-FrontEnd** > **[受信セキュリティ規則]**
   
    ![Azure ポータル - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. **[受信セキュリティ規則]**をデプロイしてテスト環境を構築してから **[追加]**することもできます。
   
    ![Azure ポータル - 規則の追加](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. **[受信セキュリティ規則の追加]** ブレードで、ポート *80* の *TCP* 経由で任意のソースから任意の VM にアクセスできるようにする *web-rule* という名前の規則を *200* の優先度で作成して、**[OK]** をクリックします。 これらの設定のほとんどは、既定値が設定されています。
   
    ![Azure ポータル - 規則の設定](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. 数秒後に、NSG の新しい規則が表示されます。
   
    ![Azure ポータル - 新しい規則](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. 手順 6 までを繰り返し、ポート *3389* の *TCP* 経由で任意のソースから任意の VM にアクセスできるようにする *rdp-rule* という名前の受信規則を *250* の優先度で作成します。

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>NSG を FrontEnd サブネットに関連付ける
1. **[参照]** > **[リソース グループ]** > **[RG-NSG]** をクリックします。
2. **[RG NSG]** ブレードで、**[...]** > **[TestVNet]** をクリックします。
   
    ![Azure ポータル - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. **[設定]** ブレードで、**[サブネット]** > **[FrontEnd]** > **[ネットワーク セキュリティ グループ]** > **[NSG-FrontEnd]** をクリックします。
   
    ![Azure ポータル - サブネット設定](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. **[FrontEnd]** ブレードで、**[保存]** をクリックします。
   
    ![Azure ポータル - サブネット設定](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>NSG-BackEnd NSG を作成する
**NSG-BackEnd** NSG を作成し、**BackEnd** サブネットに関連付けるには、次の手順を実行します。

1. 「 [NSG-FrontEnd NSG を作成する](#Create-the-NSG-FrontEnd-NSG) 」の手順を繰り返し、 *NSG-BackEnd*
2. 「 [既存の NSG に規則を作成する](#Create-rules-in-an-existing-NSG) 」の手順を繰り返し、次の表のように **受信** 規則を作成します。
   
   | 受信規則 | 送信規則 |
   | --- | --- |
   | ![Azure ポータル - 受信規則](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Azure ポータル - 送信規則](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. 「[NSG を FrontEnd サブネットに関連付ける](#Associate-the-NSG-to-the-FrontEnd-subnet)」の手順を繰り返し、**NSG-Backend** NSG を **BackEnd** サブネットに関連付けます。

## <a name="next-steps"></a>次のステップ
* [既存の NSG の管理](virtual-network-manage-nsg-arm-portal.md)
* [ログを有効にします](virtual-network-nsg-manage-log.md) 。


