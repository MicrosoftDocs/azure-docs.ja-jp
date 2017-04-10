---
title: "Azure 仮想ネットワーク (クラシック) の作成 - クラシック ポータル | Microsoft Docs"
description: "Azure クラシック ポータルで netcfg ファイルを使用して仮想ネットワーク (クラシック) を作成する方法について説明します。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 69894a0b-8050-451e-8a25-c513e1bd271e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: d365f7137527d60eb509b4f431295de2218ea706
ms.openlocfilehash: 5ff91cccb711d61ed120e4a4e820d6a5dfc5e4a6


---

# <a name="create-a-virtual-network-classic-with-a-netcfg-file-using-the-azure-classic-portal"></a>Azure クラシック ポータルで netcfg ファイルを使用して仮想ネットワーク (クラシック) を作成する
[!INCLUDE [virtual-networks-create-vnet-selectors-classic-include](../../includes/virtual-networks-create-vnet-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、Azure クラシック ポータルを使用したクラシック デプロイメント モデルにより、netcfg ファイルを使用して仮想ネットワークを作成する方法について説明します。 [netcfg ファイルを使用しないでクラシック デプロイメント モデルで仮想ネットワークを作成](virtual-networks-create-vnet-classic-pportal.md)したり、[Azure Portal を使用して Azure Resource Manager デプロイメント モデルで仮想ネットワークを作成](virtual-networks-create-vnet-arm-pportal.md)したりすることもできます。

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="how-to-create-a-vnet-with-a-network-config-file-in-the-microsoft-azure-classic-portal"></a>Microsoft Azure クラシック ポータルのネットワーク構成ファイルで VNet を作成する方法
Azure では、xml ファイルを使用して、サブスクリプションに使用可能なすべての Vnet を定義します。 このファイルをダウンロードして編集して、クラシック デプロイメント モデルで VNet を作成したり、既存の VNet を変更または削除したりできます。 この記事では、ネットワーク構成 (または netcfg) ファイルと呼ばれるこのファイルをダウンロードする方法、これに VNet を追加する方法、ファイルをアップロードして VNet を作成する方法を説明します。 ネットワーク構成ファイルの詳細については、「[Azure Virtual Network Configuration Schema (Azure 仮想ネットワークの構成スキーマ)](https://msdn.microsoft.com/library/azure/jj157100.aspx)」を参照してください。

Azure クラシック ポータルで netcfg ファイルを使用して VNet を作成するには、次の手順を実行します。

1. ブラウザーから http://manage.windowsazure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. サービスの一覧で下にスクロールして、次の図のとおりに **[ネットワーク]** > **[エクスポート]** の順にクリックします。

    ![Azure 仮想ネットワーク](./media/virtual-networks-create-vnet-classic-portal/networks.png)
3. **[ネットワーク構成のエクスポート]** ダイアログ ボックスで、仮想ネットワーク構成のエクスポート元となるサブスクリプションを選択してから、ボックス右下隅のチェック マーク ボタンをクリックします。
4. ブラウザーの使用手順に従って、 **NetworkConfig.xml** ファイルを保存します。 ファイルの保存場所を覚えておいてください。
5. 任意の XML またはテキスト エディター アプリケーションを使用して、手順 4 で保存したファイルを開き、`<VirtualNetworkConfiguration>` の要素内の `<VirtualNetworkSites>` の要素を探します。 既存の VNet がある場合、それぞれの `<VirtualNetworkSite>` 要素内に各 VNet が一覧表示されています。 ファイルが `<VirtualNetworkConfiguration>` 要素内に `<VirtualNetworkSites>` 要素を含んでいない場合、新しく作成します。
6. 既存の NetworkConfig ファイルに VNet が含まれていない場合、NetworkConfig.xml ファイルにこのシナリオで記述されている VNet を追加すると、NetworkConfig.xml ファイルは次の例のようになります。

    ```xml
    <NetworkConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="TestVNet" Location="Central US">
            <AddressSpace>
              <AddressPrefix>192.168.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>192.168.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>192.168.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
    ```
7. ネットワーク構成ファイルを保存します。
8. Azure クラシック ポータルで、次の図のとおり **[新規]** > **[ネットワーク サービス]****[仮想ネットワーク]****[構成のインポート]** の順にクリックします。

    ![構成のインポート](./media/virtual-networks-create-vnet-classic-portal/import.png)
10. 次の図のとおり、**[ネットワーク構成ファイルをインポートする]** ダイアログ ボックスで **[ファイルの参照...]** をクリックし、ステップ 7 でファイルを保存したフォルダに移動し、**[開く]** をクリックします。

    ![[ネットワーク構成ファイルのインポート] ページ](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure4.png)

    ボックス右下隅の矢印をクリックし、次の手順に進みます。

9. 次の図のように、**[ネットワークを構築しています]** ダイアログ ボックスに新しい VNet のエントリが表示されます。

    ![[ネットワークを構築しています] ページ](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure5.png)
10. VNet を作成するには、直前の図の右下隅にあるチェック マーク ボタンをクリックします。 数秒経つと、作成した VNet が次の図のように利用可能な VNet の一覧に表示されます。

    ![新しい仮想ネットワーク](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure6.png)



<!--HONumber=Jan17_HO5-->


