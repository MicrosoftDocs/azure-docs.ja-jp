---
title: Azure 仮想ネットワーク ピアリングの作成 - 異なるデプロイメント モデル - 異なるサブスクリプション
titlesuffix: Azure Virtual Network
description: 異なる Azure サブスクリプションに存在する、異なる Azure デプロイメント モデルで作成された仮想ネットワーク間に、仮想ネットワーク ピアリングを作成する方法について説明します。
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: fa647da6764ca61679aade2acc2849b474912278
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871982"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>仮想ネットワーク ピアリングの作成 - 異なるデプロイメント モデルとサブスクリプション

このチュートリアルでは、異なるデプロイメント モデルで作成された仮想ネットワーク間に仮想ネットワーク ピアリングを作成する方法について説明します。 仮想ネットワークは、異なるサブスクリプションに存在します。 2 つの仮想ネットワークをピアリングすると、別々の仮想ネットワークに存在するリソースが、あたかも同じ仮想ネットワーク内に存在するかのような帯域幅と待ち時間で相互に通信を行うことができます。 詳しくは、「[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)」をご覧ください。

仮想ネットワーク ピアリングを作成する手順は、サブスクリプションが同じか異なるか、また、どの[Azure デプロイメント モデル](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を使用して仮想ネットワークが作成されているかによって異なります。 他のシナリオで仮想ネットワークを作成する方法については、次の表で目的のシナリオをクリックしてください。

|Azure デプロイメント モデル  | Azure サブスクリプション  |
|--------- |---------|
|[両方とも Resource Manager](tutorial-connect-virtual-networks-portal.md) |同じ|
|[両方が Resource Manager](create-peering-different-subscriptions.md) |異なる|
|[一方が Resource Manager、もう一方がクラシック](create-peering-different-deployment-models.md) |同じ|

クラシック デプロイ モデルでデプロイされた 2 つの仮想ネットワークの間に、仮想ネットワーク ピアリングを作成することはできません。 このチュートリアルでは、同じリージョンに存在する仮想ネットワークを使用します。 このチュートリアルでは同じリージョンで複数の仮想ネットワークをピアリングします。 異なる[サポート対象リージョン](virtual-network-manage-peering.md#cross-region)間での仮想ネットワーク ピアリングも可能です。 仮想ネットワークのピアリングの前に、[ピアリングの要件と制約](virtual-network-manage-peering.md#requirements-and-constraints)をよく理解しておくことをお勧めします。

異なるサブスクリプションの仮想ネットワークの間で仮想ネットワーク ピアリングを作成する場合、両方のサブスクリプションが同じ Azure Active Directory テナントに関連付けられている必要があります。 Azure Active Directory テナントがまだない場合は、簡単に[作成](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant)できます。 異なるサブスクリプションや異なる Azure Active Directory テナント内の仮想ネットワークは、Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) を使って接続することができます。

仮想ネットワーク ピアリングは、[Azure Portal](#portal)、Azure [コマンド ライン インターフェイス](#cli) (CLI)、Azure [PowerShell](#powershell) のいずれかを使って作成できます。 いずれかのリンクをクリックすると、そのツールを使って仮想ネットワーク ピアリングを作成するための手順に直接移動します。

## <a name="portal"></a>ピアリングの作成 - Azure Portal

このチュートリアルでは、サブスクリプションごとに異なるアカウントを使用します。 両方のサブスクリプションへのアクセス許可を持つアカウントを使用している場合は、すべての手順で同じアカウントを使用し、ポータルからログアウトする手順と、他のユーザーのアクセス許可を仮想ネットワークに割り当てる手順はスキップできます。

1. [Azure Portal](https://portal.azure.com) に UserA としてログインします。 ログインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 アクセス許可の一覧については、[仮想ネットワークのピアリングのアクセス許可](virtual-network-manage-peering.md#permissions)に関するページをご覧ください。
2. **[+ 新規]** 、 **[ネットワーキング]** 、 **[仮想ネットワーク]** の順にクリックします。
3. **[仮想ネットワークの作成]** ブレードで、次の設定の値を入力するか選択し、 **[作成]** をクリックします。
    - **[名前]** : *myVnetA*
    - **[アドレス空間]** : *10.0.0.0/16*
    - **[サブネット名]** : "*既定値*"
    - **[サブネットのアドレス範囲]** : *10.0.0.0/24*
    - **サブスクリプション**:サブスクリプション A を選択します。
    - **[リソース グループ]** : **[新規作成]** を選択し、「*myResourceGroupA*」と入力します
    - **場所**: *米国東部*
4. ポータル上部の **[リソースの検索]** ボックスに「*myVnetA*」と入力します。 検索結果に **myVnetA** が表示されたら、それをクリックします。 **myVnetA** 仮想ネットワークのブレードが表示されます。
5. 表示された **[myVnetA]** ブレードの左側にある縦長のオプション一覧で **[アクセス制御 (IAM)]** をクリックします。
6. **[myVnetA - アクセス制御 (IAM)]** ブレードが表示されたら、 **[+ ロールの割り当ての追加]** をクリックします。
7. **[[ロールの割り当ての追加] ウィンドウ]** ブレードが表示されたら、 **[ロール]** ボックスの **[ネットワーク共同作成者]** を選択します。
8. **[選択]** ボックスで、UserB を選択するか、UserB のメール アドレスを入力して検索します。 一覧表示されるのは、ピアリングの設定対象の仮想ネットワークと同じ Azure Active Directory テナントからのユーザーです。 UserB が一覧表示されたら、クリックします。
9. **[Save]** をクリックします。
10. UserA としてポータルからログアウトし、UserB としてログインします。
11. **[+ 新規]** をクリックし、 *[Marketplace を検索]* ボックスに「**仮想ネットワーク**」と入力して、検索結果で **[仮想ネットワーク]** をクリックします。
12. 表示された **[仮想ネットワーク]** ブレードの **[デプロイ モデルの選択]** ボックスで、 **[クラシック]** を選択し、 **[作成]** をクリックします。
13. 表示された [仮想ネットワーク (クラシック) の作成] ボックスに、次の値を入力します。

    - **[名前]** : *myVnetB*
    - **[アドレス空間]** : *10.1.0.0/16*
    - **[サブネット名]** : "*既定値*"
    - **[サブネットのアドレス範囲]** : *10.1.0.0/24*
    - **サブスクリプション**:サブスクリプション B を選択します。
    - **[リソース グループ]** : **[新規作成]** を選択し、「*myResourceGroupB*」と入力します
    - **場所**: *米国東部*

14. ポータル上部の **[リソースの検索]** ボックスに「*myVnetB*」と入力します。 検索結果に **[myVnetB]** が表示されたら、それをクリックします。 **myVnetB** 仮想ネットワークのブレードが表示されます。
15. 表示された **[myVnetB]** ブレードの左側にある縦長のオプション一覧で **[プロパティ]** をクリックします。 **リソース ID** をコピーします。これは後の手順で使用されます。 リソース ID は次の例に似たものになります。`/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
16. myVnetB に対して手順 5. ～ 9. を実行します。手順 8. では **UserA** を入力します。
17. UserB としてポータルからログアウトし、UserA としてログインします。
18. ポータル上部の **[リソースの検索]** ボックスに「*myVnetA*」と入力します。 検索結果に **myVnetA** が表示されたら、それをクリックします。 **myVnet** 仮想ネットワークのブレードが表示されます。
19. **[myVnetA]** をクリックします。
20. 表示された **[myVnetA]** ブレードの左側にある縦長のオプション一覧で **[ピアリング]** をクリックします。
21. 表示された **[myVnetA - ピアリング]** ブレードで **[+ 追加]** をクリックします
22. 表示された **[ピアリングの追加]** ブレードで次のオプションを入力または選択し、 **[OK]** をクリックします。
     - **[名前]** : *myVnetAToMyVnetB*
     - **仮想ネットワークのデプロイ モデル**: **[クラシック]** を選択します。
     - **[リソース ID を知っている]** : このボックスをオンにします。
     - **[リソース ID]** : 手順 15 の myVnetB のリソース ID を入力します。
     - **[仮想ネットワーク アクセスを許可する]** : **[有効]** が選択されていることを確認します。
    その他の設定は、このチュートリアルでは使用しません。 すべてのピアリング設定については、[仮想ネットワーク ピアリングの管理](virtual-network-manage-peering.md#create-a-peering)に関するページを参照してください。
23. 前の手順で **[OK]** をクリックすると、 **[ピアリングの追加]** ブレードが閉じて、 **[myVnetA - ピアリング]** ブレードが再度表示されます。 数秒後、作成したピアリングがブレードに表示されます。 作成した **myVnetAToMyVnetB** ピアリングの **[ピアリング状態]** 列に **[接続済み]** と表示されます。 これでピアリングが確立されました。 仮想ネットワーク (クラシック) を仮想ネットワーク (Resource Manager) にピアリングする必要はありません。

    2 つの仮想ネットワークに作成した Azure リソースが、その IP アドレスを使用して相互に通信できるようになりました。 仮想ネットワークに Azure の既定の名前解決を使用する場合、そのネットワーク内のリソースは、通信相手の仮想ネットワークに対して名前を解決することができません。 ピアリングされた仮想ネットワークの間で名前を解決する必要がある場合は、独自の DNS サーバーを作成する必要があります。 その方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」を参照してください。

24. **省略可能**: このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
25. **省略可能**: このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-portal)」セクションの手順を実行してください。

## <a name="cli"></a>ピアリングの作成 - Azure CLI

このチュートリアルでは、サブスクリプションごとに異なるアカウントを使用します。 両方のサブスクリプションへのアクセス許可を持つアカウントを使用している場合は、すべての手順で同じアカウントを使用し、Azure からログアウトする手順をスキップして、ユーザー ロールの割り当てを作成するスクリプト行を削除できます。 次のすべてのスクリプトの UserA@azure.com と UserB@azure.com を、UserA と UserB で使用しているユーザー名に置き換えます。 Azure クラシック CLI と Azure CLI を使用して、次の手順を完了します。 次の手順のいずれかで **[試してみる]** ボタンを選択するだけで、または[クラシック CLI](/cli/azure/install-classic-cli) と [CLI](/cli/azure/install-azure-cli) をインストールし、ローカル コンピューター上のコマンドを実行すると、Azure Cloud Shell からの手順を行うことができます。

1. Cloud Shell を使用する場合は、Cloud Shell が Azure に自動的にサインインするため、手順 2 に進みます。 コマンド セッションを開き、`azure login` コマンドを使用して Azure にサインインします。
2. `azure config mode asm` コマンドを入力して、サービス管理モードでクラシック CLI を実行します。
3. 次のクラシック CLI コマンドを入力して、仮想ネットワーク (クラシック) を作成します。

    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
4. 残りの手順は、bash シェルと (クラシック CLI ではなく) Azure CLI を使用して、行う必要があります。
5. PC で次のスクリプトをテキスト エディターにコピーします。 `<SubscriptionB-Id>` は、サブスクリプション ID で置き換えてください。 サブスクリプション ID がわからない場合は、`az account show` コマンドを入力します。 出力された **id** の値がサブスクリプション ID です。変更後のスクリプトをコピーし、CLI セッションに貼り付けて、`Enter` キーを押します。

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    手順 4. で仮想ネットワーク (クラシック) を作成したとき、その仮想ネットワークは、*Default-Networking* リソース グループに作成されました。
6. UserB として Azure からログアウトし、CLI で UserA としてログインします。
7. リソース グループと仮想ネットワーク (Resource Manager) を作成します。 次のスクリプトをコピーし、CLI セッションに貼り付けて、`Enter` を押します。

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

8. 異なるデプロイメント モデルで作成された 2 つの仮想ネットワークの間で仮想ネットワーク ピアリングを作成します。 PC で次のスクリプトをテキスト エディターにコピーします。 `<SubscriptionB-id>` は、サブスクリプション ID で置き換えてください。サブスクリプション ID がわからない場合は、`az account show` コマンドを入力します。 出力された **id** の値がサブスクリプション ID です。手順 4. で作成した仮想ネットワーク (クラシック) は、*Default-Networking* という名前のリソース グループに作成されました。 変更後のスクリプトを CLI セッションに貼り付けて、`Enter` キーを押します。

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. スクリプトの実行後、仮想ネットワーク (Resource Manager) のピアリングを確認します。 次のスクリプトをコピーし、CLI セッションに貼り付けます。

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    出力の **PeeringState** 列に **Connected** と表示されます。

    2 つの仮想ネットワークに作成した Azure リソースが、その IP アドレスを使用して相互に通信できるようになりました。 仮想ネットワークに Azure の既定の名前解決を使用する場合、そのネットワーク内のリソースは、通信相手の仮想ネットワークに対して名前を解決することができません。 ピアリングされた仮想ネットワークの間で名前を解決する必要がある場合は、独自の DNS サーバーを作成する必要があります。 その方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」を参照してください。

10. **省略可能**: このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
11. **省略可能**: このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-cli)」の手順を実行してください。

## <a name="powershell"></a>ピアリングの作成 - PowerShell

このチュートリアルでは、サブスクリプションごとに異なるアカウントを使用します。 両方のサブスクリプションへのアクセス許可を持つアカウントを使用している場合は、すべての手順で同じアカウントを使用し、Azure からログアウトする手順をスキップして、ユーザー ロールの割り当てを作成するスクリプト行を削除できます。 次のすべてのスクリプトの UserA@azure.com と UserB@azure.com を、UserA と UserB で使用しているユーザー名に置き換えます。 

1. PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) および [Az](https://www.powershellgallery.com/packages/Az) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。
2. PowerShell セッションを開始します。
3. PowerShell で、`Add-AzureAccount` コマンドを入力して、UserB のサブスクリプションに UserB としてログインします。 ログインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 アクセス許可の一覧については、[仮想ネットワークのピアリングのアクセス許可](virtual-network-manage-peering.md#permissions)に関するページをご覧ください。
4. PowerShell で仮想ネットワーク (クラシック) を作成するには、ネットワーク構成ファイルを新しく作成するか、既存のものを変更する必要があります。 [ネットワーク構成ファイルをエクスポート、更新、およびインポートする](virtual-networks-using-network-configuration-file.md)方法を確認してください。 ファイルには、このチュートリアルで使用されている仮想ネットワークの次の **VirtualNetworkSite** 要素を含める必要があります。

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > 変更したネットワーク構成ファイルをインポートすると、サブスクリプションの既存の仮想ネットワーク (クラシック) が変更される可能性があります。 前の仮想ネットワークのみを追加し、サブスクリプションの既存の仮想ネットワークは変更または削除しないようにしてください。 

5. Resource Manager コマンドを使用するために、`Connect-AzAccount` コマンドを入力して、UserB のサブスクリプションに UserB としてログインします。
6. UserA のアクセス許可を仮想ネットワーク B に割り当てます。PC で次のスクリプトをテキスト エディターにコピーして、`<SubscriptionB-id>` をサブスクリプション B の ID で置き換えます。サブスクリプション ID がわからない場合は、`Get-AzSubscription` コマンドを入力して確認します。 返された出力の **id** の値がサブスクリプション ID です。 手順 4. で作成した仮想ネットワーク (クラシック) は、*Default-Networking* という名前のリソース グループに作成されました。 スクリプトを実行するには、変更後のスクリプトをコピーし、PowerShell に貼り付けて、`Enter` キーを押します。
    
    ```powershell 
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. UserB として Azure からログアウトし、`Connect-AzAccount` コマンドを入力して、UserA のサブスクリプションに UserA としてログインします。 ログインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 アクセス許可の一覧については、[仮想ネットワークのピアリングのアクセス許可](virtual-network-manage-peering.md#permissions)に関するページをご覧ください。
8. 仮想ネットワーク (Resource Manager) を作成します。これを行うには、次のスクリプトをコピーし、PowerShell に貼り付けて、`Enter` キーを押します。

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. UserB のアクセス許可を myVnetA に割り当てます。 PC で次のスクリプトをテキスト エディターにコピーして、`<SubscriptionA-Id>` をサブスクリプション A の ID で置き換えます。サブスクリプション ID がわからない場合は、`Get-AzSubscription` コマンドを入力して確認します。 返された出力の **id** の値がサブスクリプション ID です。 変更後のスクリプトを PowerShell に貼り付けてから、`Enter` キーを押して実行します。

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. PC で次のスクリプトをテキスト エディターにコピーして、`<SubscriptionB-id>` をサブスクリプション B の ID で置き換えます。myVnetA を myVNetB にピアリングするには、変更後のスクリプトをコピーし、PowerShell に貼り付けて、`Enter` キーを押します。

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. myVnetA のピアリング状態を確認します。これを行うには、次のスクリプトをコピーし、PowerShell に貼り付けて、`Enter` キーを押します。

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    状態は**接続済み**です。 myVnetB から myVnetA へのピアリングの設定が完了した時点で、**接続済み**に変わります。

    2 つの仮想ネットワークに作成した Azure リソースが、その IP アドレスを使用して相互に通信できるようになりました。 仮想ネットワークに Azure の既定の名前解決を使用する場合、そのネットワーク内のリソースは、通信相手の仮想ネットワークに対して名前を解決することができません。 ピアリングされた仮想ネットワークの間で名前を解決する必要がある場合は、独自の DNS サーバーを作成する必要があります。 その方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」を参照してください。

12. **省略可能**: このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
13. **省略可能**: このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-powershell)」の手順を実行してください。

## <a name="delete"></a>リソースの削除
このチュートリアルが完了したら、使用料がかからないように、チュートリアルで作成したリソースを削除することをお勧めします。 リソース グループを削除すると、リソース グループ内にあるリソースもすべて削除されます。

### <a name="delete-portal"></a>Azure Portal

1. ポータルの検索ボックスに、「**myResourceGroupA**」と入力します。 検索結果で、**myResourceGroupA** をクリックします。
2. **myResourceGroupA** のブレードで、 **[削除]** アイコンをクリックします。
3. 削除を確定するには、 **[リソース グループ名を入力してください]** ボックスに「**myResourceGroupA**」と入力し、 **[削除]** をクリックします。
4. ポータル上部の **[リソースの検索]** ボックスに「*myVnetB*」と入力します。 検索結果に **[myVnetB]** が表示されたら、それをクリックします。 **myVnetB** 仮想ネットワークのブレードが表示されます。
5. **[myVnetB]** ブレードで、 **[削除]** をクリックします。
6. 削除を確定するには、 **[仮想ネットワークの削除]** ボックスで **[はい]** をクリックします。

### <a name="delete-cli"></a>Azure CLI

1. CLI を使用して Azure にログインし、次のコマンドで仮想ネットワーク (Resource Manager) を削除します。

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. クラシック CLI を使用して Azure にサインインし、次のコマンドで仮想ネットワーク (クラシック) を削除します。

   ```azurecli-interactive
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="delete-powershell"></a>PowerShell

1. PowerShell コマンド プロンプトで、次のコマンドを入力して、仮想ネットワーク (Resource Manager) を削除します。

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. PowerShell で仮想ネットワーク (クラシック) を削除するには、既存のネットワーク構成ファイルを変更する必要があります。 [ネットワーク構成ファイルをエクスポート、更新、およびインポートする](virtual-networks-using-network-configuration-file.md)方法を確認してください。 このチュートリアルで使用されている仮想ネットワークの次の VirtualNetworkSite 要素を削除します。

   ```xml
   <VirtualNetworkSite name="myVnetB" Location="East US">
     <AddressSpace>
       <AddressPrefix>10.1.0.0/16</AddressPrefix>
     </AddressSpace>
     <Subnets>
       <Subnet name="default">
         <AddressPrefix>10.1.0.0/24</AddressPrefix>
       </Subnet>
     </Subnets>
   </VirtualNetworkSite>
   ```

   > [!WARNING]
   > 変更したネットワーク構成ファイルをインポートすると、ご使用のサブスクリプションの既存の仮想ネットワーク (クラシック) が変更される可能性があります。 前の仮想ネットワークのみを削除し、サブスクリプションの他の既存の仮想ネットワークは変更または削除しないようにしてください。 

## <a name="next-steps"></a>次の手順

- 運用環境で使用する仮想ネットワーク ピアリングを作成する前に、[仮想ネットワーク ピアリングの制約と動作](virtual-network-manage-peering.md#requirements-and-constraints)の要点をしっかりと理解します。
- [仮想ネットワーク ピアリングのさまざまな設定](virtual-network-manage-peering.md#create-a-peering)について理解を深めます。
- 仮想ネットワーク ピアリングで[ハブとスポークのネットワーク トポロジを作成](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)する方法を学習します。