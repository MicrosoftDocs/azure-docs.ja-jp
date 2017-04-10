---
title: "Azure CLI 1.0 を使用して複数の IP アドレスを持つ VM を作成する | Microsoft Docs"
description: "Azure CLI 1.0 を使用して仮想マシンに複数の IP アドレスを割り当てる方法 | Resource Manager"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 9f085dfa1fe4db36d58cb976bb550a46bf241ac7
ms.lasthandoff: 03/28/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli-10"></a>Azure CLI 1.0 を使用して仮想マシンに複数の IP アドレスを割り当てる

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

この記事では、Azure CLI 1.0 を使用して、Azure Resource Manager デプロイメント モデルで仮想マシン (VM) を作成する方法について説明します。 クラシック デプロイ モデルで作成されたリソースには、複数の IP アドレスを割り当てることはできません。 Azure のデプロイ モデルの詳細については、[デプロイ モデルの概要](../resource-manager-deployment-model.md)に関する記事をご覧ください。

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>複数の IP アドレスを持つ VM を作成する

このタスクは、Azure CLI 1.0 (この記事) または [Azure CLI 2.0](virtual-network-multiple-ip-addresses-cli.md) を使用して行うことができます。 以下の手順は、シナリオの説明に従って複数の IP アドレスを持つ VM を作成する方法を示しています。 変数名と IP アドレスの種類は、実際の実装に合わせて変更してください。

1. [Azure CLI のインストールと構成](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順に従って、Azure CLI 1.0 をインストールして構成し、`azure-login` コマンドを使用して Azure アカウントにログインします。

2. リソース グループを作成します。
    
    ```azurecli
    RgName=myResourceGroup
    Location=westcentralus
    azure group create --name $RgName --location $Location
    ```
3. 仮想ネットワークを作成します。

    ```azurecli
    azure network vnet create --resource-group $RgName --location $Location --name myVNet \
    --address-prefixes 10.0.0.0/16
    ```
4. 仮想ネットワークのサブネットを作成します。

    ```azurecli
    azure network vnet subnet create --name mySubnet --resource-group $RgName --vnet-name myVNet \
    --address-prefix 10.0.0.0/24
    ```
    
5. VM のストレージ アカウントを作成します。 次のコマンドを実行する前に、*mystorageaccount* を一意の名前で置き換えます。 この名前は Azure 全体で一意である必要があります。

    ```azurecli
    az storage account create --resource-group $RgName --location $Location --name mystorageaccount \
    --kind Storage --sku Standard_LRS
    ```

6. IP 構成、NIC を作成し、IP 構成を NIC に割り当てます。 構成は、必要に応じて追加、削除、または変更できます。 このシナリオで説明する構成は次のとおりです。

    **IPConfig 1**

    次のコマンドを入力して、以下を作成します。

    - 静的パブリック IP アドレスが割り当てられたパブリック IP アドレス リソース
    - パブリック IP アドレスと静的プライベート IP アドレスを割り当てる NIC
    
    *mypublicdns* は、Azure の場所内で一意の名前を使って置き換えます。

      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP1 \
      --domain-name-label mypublicdns --allocation-method Static
        
      azure network nic create --resource-group $RgName --location $Location --name myNic1 \
      --private-ip-address 10.0.0.4 --subnet-name mySubnet --subnet-vnet-name myVNet \
      --subnet-name mySubnet --public-ip-name myPublicIP1
      ```

      > [!NOTE]
      > パブリック IP アドレスには、わずかな費用がかかります。 IP アドレスの料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 制限の詳細については、[Azure の制限](../azure-subscription-service-limits.md#networking-limits)に関する記事をご覧ください。

    **IPConfig-2**

     次のコマンドを入力して、新しいパブリック IP アドレス リソース、および静的パブリック IP アドレスと静的プライベート IP アドレスが割り当てられた新しい IP 構成を作成します。
    
      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP2
      --domain-name-label mypublicdns2 --allocation-method Static

      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --name IPConfig-2
      --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
      ```

    **IPConfig-3**

    次のコマンドを入力して、静的プライベート IP アドレスが割り当てられ、パブリック IP アドレスは割り当てられていない IP 構成を作成します。

      ```azurecli
      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --private-ip-address 10.0.0.6 \
      --name IPConfig-3
      ```

    >[!NOTE] 
    >この記事では、すべての IP 構成を 1 つの NIC に割り当てていますが、複数の IP 構成を VM の任意の NIC に割り当てることもできます。 複数の NIC が接続された VM の作成方法については、複数の NIC が接続された VM の作成に関する記事をご覧ください。

7. Linux VM の作成 

    ```azurecli
    az vm create --resource-group $RgName --name myVM1 --location $Location --nics myNic1 \
    --image UbuntuLTS --ssh-key-value ~/.ssh/id_rsa.pub --admin-username azureuser
    ```

    たとえば、VM サイズを Standard DS2 v2 に変更するには、手順 6 の `azure vm create` コマンドに `--vm-size Standard_DS3_v2` プロパティを追加します。

8. 次のコマンドを入力して、NIC および関連付けられた IP 構成を表示します。

    ```azurecli
    azure network nic show --resource-group $RgName    --name myNic1
    ```
9. この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」に記載されたご使用のオペレーティング システム用の手順に従って、プライベート IP アドレスを VM オペレーティング システムに追加します。

## <a name="add"></a>VM に IP アドレスを追加する

プライベートおよびパブリック IP アドレスを既存の NIC に追加するには、次の手順を実行します。 各例はこの記事で説明する[シナリオ](#Scenario)に基づいています。

1. Azure CLI を開き、1 つの CLI セッションでこのセクションの残りの手順を実行します。 Azure CLI のインストールと構成をまだ行っていない場合は、[Azure CLI のインストールと構成](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順を完了し、Azure アカウントにログインします。

2. 要件に基づいて、以下のいずれかのセクションの手順を実行します。

    - **プライベート IP アドレスを追加する**
    
        NIC にプライベート IP アドレスを追加するには、次のコマンドを使用して IP 構成を作成する必要があります。 静的アドレスは、サブネットの未使用のアドレスである必要があります。

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 \
        --private-ip-address 10.0.0.7 --name IPConfig-4
        ```
        一意の構成名とプライベート IP アドレス (静的 IP アドレスを使用する構成の場合) を使用して、必要な数だけ構成を作成します。

    - **パブリック IP アドレスを追加する**
    
        パブリック IP アドレスを追加するには、新しい IP 構成または既存の IP 構成にパブリック IP アドレスを関連付けます。 必要に応じて、以下のいずれかのセクションの手順を実行します。

        > [!NOTE]
        > パブリック IP アドレスには、わずかな費用がかかります。 IP アドレスの料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 制限の詳細については、[Azure の制限](../azure-subscription-service-limits.md#networking-limits)に関する記事をご覧ください。
        >

        **リソースを新しい IP 構成に関連付ける**
    
        すべての IP 構成にプライベート IP アドレスが必要であるため、パブリック IP アドレスを新しい IP 構成に追加するときは、必ずプライベート IP アドレスも追加する必要があります。 既存のパブリック IP アドレス リソースを追加することも、新しいリソースを作成することもできます。 新しいパブリック IP アドレス リソースを作成するには、次のコマンドを入力します。

        ```azurecli
        azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 \
        --domain-name-label mypublicdns3
        ```

         静的プライベート IP アドレスと、関連付けられた *myPublicIP3* パブリック IP アドレス リソースで新しい IP 構成を作成するには、次のコマンドを入力します。

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 \
        --private-ip-address 10.0.0.8 --public-ip-name myPublicIP3
        ```

        **リソースを既存の IP 構成に関連付ける**

        パブリック IP アドレス リソースは、このリソースがまだ関連付けられていない IP 構成にのみ関連付けることができます。 IP 構成にパブリック IP アドレスが関連付けられているかどうかを確認するには、次のコマンドを入力します。

        ```azurecli
        azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
        ```

        返された出力で IPConfig-3 に関する次のような行を探します。

        ```            
        Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet
        ```
          
        *IpConfig-3* の **Public IP** 列が空白であるため、現在、この構成にはパブリック IP アドレス リソースは関連付けられていません。 IpConfig-3 に既存のパブリック IP アドレス リソースを追加することも、次のコマンドを入力して新しいリソースを作成することもできます。

        ```azurecli
        azure network public-ip create --resource-group  myResourceGroup --location westcentralus \
        --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
        ```

        パブリック IP アドレス リソースを、*IPConfig-3* という名前の既存の IP 構成に関連付けるには、次のコマンドを入力します。
        ```azurecli
        azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 \
        --public-ip-name myPublicIP3
        ```

3. 次のコマンドを入力して、NIC に割り当てられたプライベート IP アドレスとパブリック IP アドレス リソースを表示します。

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

      次のような出力が返されます。
      ```
      Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        
      default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
      IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
      IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet  myPublicIP3
      ```
4. この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」の手順に従って、NIC に追加したプライベート IP アドレスを、VM オペレーティング システムに追加します。 オペレーティング システムにパブリック IP アドレスは追加しないでください。

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

