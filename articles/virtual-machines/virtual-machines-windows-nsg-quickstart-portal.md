---
title: "Azure Portal を使用した VM へのポートの開放 | Microsoft Docs"
description: "Azure Portal で Resource Manager デプロイメント モデルを使用して、Windows VM へのポートを開き、エンドポイントを作成する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 979260cdb1ce7f3eaa4b7e05be4c34fce06d0399


---
# <a name="opening-ports-to-a-vm-in-azure-using-the-azure-portal"></a>Azure Portal を使用した Azure の VM へのポートの開放
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>クイック コマンド
[これらの手順は、Azure PowerShell を使用して実行](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)することもできます。

最初に、ネットワーク セキュリティ グループを作成します。 ポータルでリソース グループを選択し、**[追加]** をクリックして、[ネットワーク セキュリティ グループ] を探して選択します。

![ネットワーク セキュリティ グループの追加](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

ネットワーク セキュリティ グループの名前を入力し、リソース グループを選択または作成してから場所を選択します。 入力し終えたら **[作成]** をクリックします。

![ネットワーク セキュリティ グループの作成](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

新しいネットワーク セキュリティ グループを選択します。 [受信セキュリティ規則] を選択し、**[追加]** をクリックして規則を作成します。

![受信規則の追加](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

新しい規則の名前を指定します。 ポート 80 が既定で入力されています。 他の規則をネットワーク セキュリティ グループに追加する場合は、このブレードでソース、プロトコル、宛先を変更します。 **[OK]** をクリックして規則を作成します。

![受信規則の作成](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

最後に、ネットワーク セキュリティ グループを、サブネットまたは特定のネットワーク インターフェイスに関連付けます。 ネットワーク セキュリティ グループをサブネットに関連付けてみましょう。 [サブネット] を選択し、**[関連付け]** をクリックします。

![ネットワーク セキュリティ グループとサブネットの関連付け](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

仮想ネットワークを選択し、適切なサブネットを選択します。

![ネットワーク セキュリティ グループと仮想ネットワークの関連付け](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

これで、ネットワーク セキュリティ グループの作成、ポート 80 のトラフィックを許可する受信規則の作成、ネットワーク セキュリティ グループとサブネットの関連付けが完了しました。 そのサブネットに接続するすべての VM に、ポート 80 で到達できます。

## <a name="more-information-on-network-security-groups"></a>ネットワーク セキュリティ グループの詳細
このページのクイック コマンドでは、VM にフローするトラフィックの開始と実行を行うことができます。 ネットワーク セキュリティ グループには優れた機能が多数用意されており、リソースへのアクセスをきめ細かく制御できます。 詳細については、 [ネットワーク セキュリティ グループと ACL 規則の作成](../virtual-network/virtual-networks-create-nsg-arm-ps.md)に関するページをご覧ください。

ネットワーク セキュリティ グループと ACL 規則は、Azure Resource Manager のテンプレートの一部として定義できます。 詳細については、 [テンプレートを使用したネットワーク セキュリティ グループの作成](../virtual-network/virtual-networks-create-nsg-arm-template.md)に関するページをご覧ください。

ポート フォワーディングを使用して、一意の外部ポートを VM 上の内部ポートにマップする必要がある場合は、ロード バランサーとネットワーク アドレス変換 (NAT) 規則を使用します。 たとえば、TCP ポート 8080 を外部に公開し、VM 上の TCP ポート 80 にトラフィックを送ることができます。 詳細については、 [インターネットに接続するロード バランサーの作成](../load-balancer/load-balancer-get-started-internet-arm-ps.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
この例では、HTTP トラフィックを許可する単純な規則を作成します。 より精密な環境の作成については、次の記事で確認できます。

* [Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md)
* [ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)
* [ロード バランサー用の Azure Resource Manager の概要](../load-balancer/load-balancer-arm.md)




<!--HONumber=Nov16_HO3-->


