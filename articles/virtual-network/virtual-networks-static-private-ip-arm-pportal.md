---
title: "VM のプライベート IP アドレスの構成 - Azure Portal | Microsoft Docs"
description: "Azure Portal を使用して仮想マシンのプライベート IP アドレスを構成する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 672462fad715758e50680fa5bade4b1f9d50e6e5
ms.lasthandoff: 03/22/2017


---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Azure Portal を使用して仮想マシンのプライベート IP アドレスを構成する

> [!div class="op_single_selector"]
> * [Azure ポータル](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
> * [Azure Portal (クラシック)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (クラシック)](virtual-networks-static-private-ip-classic-ps.md)
> * [Azure CLI (クラシック)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、リソース マネージャーのデプロイ モデルについて説明します。 [クラシック デプロイ モデルで静的プライベート IP アドレスを管理する](virtual-networks-static-private-ip-classic-pportal.md)こともできます。

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

次のサンプルの手順では、単純な環境が既に作成されていると想定します。 このドキュメントに表示されている手順を実行する場合は、まず、 [vnet の作成](virtual-networks-create-vnet-arm-pportal.md)に関する記事に示されているテスト環境を構築します。

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>静的プライベート IP アドレスをテストするために VM を作成する方法
Azure ポータルを使用して、リソース マネージャー デプロイ モードで VM を作成する際に、静的プライベート IP アドレスを設定することはできません。 まず、VM を作成してから、そのプライベート IP が静的になるように設定する必要があります。

*TestVNet* という名前の VNet の *FrontEnd* サブネットで *DNS01* という名前の VM を作成するには、以下の手順に従います。

1. ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. 次の図に示すように、**[新規]** > **[Compute]** > **[Windows Server 2012 R2 Datacenter]** の順にクリックし、**[デプロイ モデルの選択]** の一覧に既に **[Resource Manager]** と表示されているのを確認してから **[作成]** をクリックします。
   
    ![Azure ポータルでの VM の作成](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. 次の図に示すように、 **[基本]** ブレードで、作成する VM の名前 (ここでは*DNS01* )、ローカル管理者のアカウント、およびパスワードを入力します。
   
    ![[基本] ブレード](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. 選択されている **[場所]** が *[米国中部]* であることを確認してから、**[リソース グループ]** の下にある **[既存の選択]** をクリックし、**[リソース グループ]** を再度クリックして、*[TestRG]*、**[OK]** の順にクリックします。
   
    ![[基本] ブレード](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. **[サイズの選択]** ブレードで、**[A1 標準]** を選択してから **[選択]** をクリックします。
   
    ![[サイズの選択] ブレード](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. **[設定]** ブレードで、次のプロパティが以下の値に設定されていることを確認してから **[OK]** をクリックします。
   
    -**ストレージ アカウント**: *vnetstorage*
   
   * **ネットワーク**: *TestVNet*
   * **サブネット**: *FrontEnd*
     
     ![[サイズの選択] ブレード](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. **[概要]** ブレードで、**[OK]** をクリックします。 ダッシュ ボードに以下のタイルが表示されることを確認します。
   
    ![Azure ポータルでの VM の作成](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>VM 用の静的プライベート IP アドレス情報を取得する方法
上記の手順で作成された VM の静的プライベート IP アドレス情報を表示するには、次の手順を実行します。

1. Azure Portal で、**[すべて参照]** > **[仮想マシン]** > **[DNS01]** > **[すべての設定]** > **[ネットワーク インターフェイス]** の順にクリックし、表示されている唯一のネットワーク インターフェイスをクリックします。
   
    ![VM のデプロイ タイル](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. **[ネットワーク インターフェイス]** ブレードで、**[すべての設定]** > **[IP アドレス]** の順にクリックして、**[割り当て]** と **[IP アドレス]** の値を確認します。
   
    ![VM のデプロイ タイル](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>既存の VM に静的プライベート IP アドレスを追加する方法
上記の手順を使用して作成した VM に静的プライベート IP アドレスを追加するには、次の手順に従います。

1. 上記の **[IP アドレス]** ブレードで、**[割り当て]** の下にある **[静的]** をクリックします。
2. **[IP アドレス]** に「*192.168.1.101*」と入力してから、**[保存]** をクリックします。
   
    ![Azure ポータルでの VM の作成](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> **[保存]** をクリックした後、割り当てがまだ **[動的]** に設定されている場合は、入力した IP アドレスが既に使用されていることを意味します。 別の IP アドレスを試してください。
> 
> 

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>VM から静的プライベート IP アドレスを削除する方法
上記で作成した VM から静的プライベート IP アドレスを削除するには、以下の手順に従います。

上記の **[IP アドレス]** ブレードで、**[割り当て]** の下にある **[動的]** をクリックしてから、**[保存]** をクリックします。

## <a name="next-steps"></a>次のステップ
* [予約済みパブリック IP](virtual-networks-reserved-public-ip.md) アドレスについて理解する。
* [インスタンスレベル パブリック IP (ILPIP)](virtual-networks-instance-level-public-ip.md) アドレスについて理解する。
* [予約済み IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)を確認する。


