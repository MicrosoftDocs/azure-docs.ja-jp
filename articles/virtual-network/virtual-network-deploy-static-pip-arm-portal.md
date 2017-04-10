---
title: "静的パブリック IP アドレスを持つ VM を作成する - Azure Portal | Microsoft Docs"
description: "Azure Portal を使用して、静的パブリック IP アドレスを持つ VM を作成する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
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
ms.openlocfilehash: 7740861cccfe0ba4e8ae0694f6d5c5fec3df47ef
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Azure Portal を使用して静的パブリック IP アドレスを持つ VM を作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [テンプレート](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (クラシック)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../resource-manager-deployment-model.md)&2; 種類のデプロイメント モデルがあります。 この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、クラシック デプロイメント モデルではなくこのモデルをお勧めします。

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>静的 IP を持つ VM を作成する

Azure Portal で静的パブリック IP アドレスを持つ VM を作成するには、次の手順に従います。

1. ブラウザーから [Azure Portal](https://portal.azure.com) に移動します。必要であれば Azure アカウントでサインインします。
2. ポータルの左上で、**[新規]**>>**[Compute]**>**[Windows Server 2012 R2 Datacenter]** の順にクリックします。
3. **[デプロイ モデルの選択]** の一覧から **[リソース マネージャー]** を選択し、**[作成]** をクリックします。
4. **[基本]** ブレードで、次に示す VM 情報を入力し、**[OK]** をクリックします。
   
    ![Azure ポータル - 基本](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. **[サイズの選択]** ブレードで、次に示す **[A1 標準]** をクリックした後、**[選択]** をクリックします。
   
    ![Azure ポータル - サイズの選択](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. **[設定]** ブレードで、**[パブリック IP アドレス]** をクリックします。次に、**[パブリック IP アドレスの作成]** ブレードで、次に示す **[割り当て]** の下の **[静的]** をクリックします。 **[OK]** をクリックします
   
    ![Azure ポータル - パブリック IP アドレスの作成](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. **[設定]** ブレードで **[OK]** をクリックします。
8. 次に示す **[概要]** ブレードを見直し、**[OK]** をクリックします。
   
    ![Azure ポータル - パブリック IP アドレスの作成](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. ダッシュボードの新しいタイルに注意してください。
   
    ![Azure ポータル - パブリック IP アドレスの作成](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. VM が作成されると、次に示す **[設定]** ブレードが表示されます。
    
    ![Azure ポータル - パブリック IP アドレスの作成](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)


