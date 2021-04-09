---
title: VNet ピアリングの作成 - 異なるサブスクリプション
titlesuffix: Azure Virtual Network
description: 同じまたは別の Azure Active Directory テナント内の異なる Azure サブスクリプションに存在する Resource Manager で作成された仮想ネットワーク間に仮想ネットワーク ピアリングを作成する方法を説明します。
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: kumud
ms.openlocfilehash: 1f8b7162787e870337918794f353ab8dc94c1965
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518247"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions-and-azure-active-directory-tenants"></a>仮想ネットワーク ピアリングを作成する - Resource Manager、異なるサブスクリプション、および Azure Active Directory テナント

このチュートリアルでは、Resource Manager で作成された仮想ネットワーク間に仮想ネットワーク ピアリングを作成する方法について説明します。 異なる Azure Active Directory (Azure AD) テナントに属している可能性のあるさまざまなサブスクリプションに仮想ネットワークが存在します。 2 つの仮想ネットワークをピアリングすると、別々の仮想ネットワークに存在するリソースが、あたかも同じ仮想ネットワーク内に存在するかのような帯域幅と待ち時間で相互に通信を行うことができます。 詳しくは、「[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)」をご覧ください。

仮想ネットワーク ピアリングを作成する手順は、サブスクリプションが同じか異なるか、また、どの[Azure デプロイメント モデル](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を使用して仮想ネットワークが作成されているかによって異なります。 他のシナリオで仮想ネットワークを作成する方法については、次の表で目的のシナリオを選択してください。

|Azure デプロイメント モデル  | Azure サブスクリプション  |
|--------- |---------|
|[両方とも Resource Manager](tutorial-connect-virtual-networks-portal.md) |同じ|
|[一方が Resource Manager、もう一方がクラシック](create-peering-different-deployment-models.md) |同じ|
|[一方が Resource Manager、もう一方がクラシック](create-peering-different-deployment-models-subscriptions.md) |異なる|

クラシック デプロイ モデルでデプロイされた 2 つの仮想ネットワークの間に、仮想ネットワーク ピアリングを作成することはできません。 どちらもクラシック デプロイ モデルで作成された仮想ネットワークを接続する必要がある場合は、Azure [VPN Gateway](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) を使ってそれらの仮想ネットワークを接続できます。

このチュートリアルでは同じリージョンで複数の仮想ネットワークをピアリングします。 異なる[サポート対象リージョン](virtual-network-manage-peering.md#cross-region)間での仮想ネットワーク ピアリングも可能です。 仮想ネットワークのピアリングの前に、[ピアリングの要件と制約](virtual-network-manage-peering.md#requirements-and-constraints)をよく理解しておくことをお勧めします。

仮想ネットワーク ピアリングは、[Azure Portal](#portal)、Azure [コマンド ライン インターフェイス](#cli) (CLI)、Azure [PowerShell](#powershell)、[Azure Resource Manager テンプレート](#template)のいずれかを使って作成できます。 いずれかのリンクを選択すると、そのツールを使って仮想ネットワーク ピアリングを作成するための手順に直接移動します。

仮想ネットワークが異なるサブスクリプションに含まれ、サブスクリプションが異なる Azure Active Directory テナントに関連付けられている場合は、続行する前に、次の手順を完了します。
1. 各 Active Directory テナントのユーザーを、反対側の Azure Active Directory テナントに[ゲスト ユーザー](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory)として追加します。
1. 各ユーザーは、反対側の Azure Active Directory テナントからのゲスト ユーザーの招待を受け入れる必要があります。

## <a name="create-peering---azure-portal"></a><a name="portal"></a>ピアリングの作成 - Azure Portal

以下の手順では、サブスクリプションごとに異なるアカウントを使用します。 両方のサブスクリプションへのアクセス許可を持つアカウントを使用している場合は、すべての手順で同じアカウントを使用し、ポータルからログアウトする手順と、他のユーザーのアクセス許可を仮想ネットワークに割り当てる手順はスキップできます。

1. [Azure Portal](https://portal.azure.com) に *UserA* としてログインします。 ログインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 アクセス許可の一覧については、[仮想ネットワークのピアリングのアクセス許可](virtual-network-manage-peering.md#permissions)に関するページをご覧ください。
2. **[+ リソースの作成]** を選択します。 **[ネットワーク]** を選択し、 **[仮想ネットワーク]** を選択します。
3. 次の設定に対して、次の値の例を選択するか入力し、 **[作成]** を選択します。
    - **[名前]** : *myVnetA*
    - **[アドレス空間]** : *10.0.0.0/16*
    - **[サブネット名]** : "*既定値*"
    - **[サブネットのアドレス範囲]** : *10.0.0.0/24*
    - **サブスクリプション**:サブスクリプション A を選択します。
    - **[リソース グループ]** : **[新規作成]** を選択し、「*myResourceGroupA*」と入力します
    - **[場所]** :*米国東部*
4. ポータル上部の **[リソースの検索]** ボックスに「*myVnetA*」と入力します。 検索結果に **[myVnetA]** が表示されたら、それを選択します。
5. 左側にある縦長のオプション一覧から **[アクセス制御 (IAM)]** を選択します。
6. **[myVnetA - アクセス制御 (IAM)]** で **[+ ロールの割り当ての追加]** を選択します。
7. **[ロール]** ボックスで **[ネットワーク共同作成者]** を選択します。
8. **[選択]** ボックスで、 *[UserB]* を選択するか、UserB のメール アドレスを入力して検索します。
9. **[保存]** を選択します。
10. **[myVnetA - アクセス制御 (IAM)]** で、左側にある縦長のオプション一覧から **[プロパティ]** を選択します。 **リソース ID** をコピーします。これは後の手順で使用されます。 リソース ID は次の例のようになります。`/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA`
11. UserA としてポータルからログアウトし、UserB としてログインします。
12. 手順 2 ～ 3 を繰り返し、手順 3 で次の値を入力または選択します。

    - **[名前]** : *myVnetB*
    - **[アドレス空間]** : *10.1.0.0/16*
    - **[サブネット名]** : "*既定値*"
    - **[サブネットのアドレス範囲]** : *10.1.0.0/24*
    - **サブスクリプション**:サブスクリプション B を選択します。
    - **[リソース グループ]** : **[新規作成]** を選択し、「*myResourceGroupB*」と入力します
    - **[場所]** :*米国東部*

13. ポータル上部の **[リソースの検索]** ボックスに「*myVnetB*」と入力します。 検索結果に **[myVnetB]** が表示されたら、それを選択します。
14. **[myVnetB]** で、左側にある縦長のオプション一覧から **[プロパティ]** を選択します。 **リソース ID** をコピーします。これは後の手順で使用されます。 リソース ID は次の例のようになります。`/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
15. **[myVnetB]** で **[アクセス制御 (IAM)]** を選択し、myVnetB に対して手順 5 ～ 10 を実行します。手順 8 では「**UserA**」と入力します。
16. UserB としてポータルからログアウトし、UserA としてログインします。
17. ポータル上部の **[リソースの検索]** ボックスに「*myVnetA*」と入力します。 検索結果に **[myVnetA]** が表示されたら、それを選択します。
18. **[myVnetA]** を選択します。
19. **[設定]** で **[ピアリング]** を選択します。
20. **[MyVnetA - ピアリング]** で、 **[+ 追加]** を選択します。
21. **[ピアリングの追加]** で、次のオプションを入力するか選択し、 **[OK]** を選択します。
     - **[名前]** : *myVnetAToMyVnetB*
     - **仮想ネットワークのデプロイ モデル**: **[リソース マネージャー]** を選択します。
     - **[リソース ID を知っている]** : このボックスをオンにします。
     - **[リソース ID]** : 手順 14 のリソース ID を入力します。
     - **[仮想ネットワーク アクセスを許可する]** : **[有効]** が選択されていることを確認します。
    その他の設定は、このチュートリアルでは使用しません。 すべてのピアリング設定については、[仮想ネットワーク ピアリングの管理](virtual-network-manage-peering.md#create-a-peering)に関するページを参照してください。
22. 前の手順で **[OK]** を選択してしばらく待つと、作成したピアリングが表示されます 作成した **myVnetAToMyVnetB** ピアリングの **[ピアリング状態]** 列に **[開始済み]** と表示されます。 myVnetA を myVnetB にピアリングできましたが、今度は myVnetB を myVnetA にピアリングする必要があります。 各仮想ネットワーク内のリソースが相互に通信するためには、ピアリングを双方向で作成する必要があります。
23. UserA としてポータルからログアウトし、UserB としてログインします。
24. myVnetB について、手順 17 ～ 21 を繰り返します。 手順 21 では、ピアリングに「*myVnetBToMyVnetA*」という名前を設定し、 **[仮想ネットワーク]** で *myVnetA* を選び、手順 10 の ID を **[リソース ID]** ボックスに入力します。
25. **[OK]** をクリックして myVnetB のピアリングを作成してから数秒経つと、作成したばかりの **myVnetBToMyVnetA** ピアリングが **[接続済み]** として **[ピアリング状態]** 列に表示されます。
26. UserB としてポータルからログアウトし、UserA としてログインします。
27. 手順 17 ～ 19 を繰り返します。 **myVnetAToVNetB** ピアリングの **[ピアリング状態]** も **接続済み** になります。 ピアリングにおける両方の仮想ネットワークの **[ピアリング状態]** 列に **接続済み** と表示されれば、ピアリングは正常に確立されています。 2 つの仮想ネットワークに作成した Azure リソースが、その IP アドレスを使用して相互に通信できるようになりました。 仮想ネットワークに Azure の既定の名前解決を使用する場合、そのネットワーク内のリソースは、通信相手の仮想ネットワークに対して名前を解決することができません。 ピアリングされた仮想ネットワークの間で名前を解決する必要がある場合は、独自の DNS サーバーを作成する必要があります。 その方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」を参照してください。
28. **省略可能**:このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
29. **省略可能**:このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-portal)」セクションの手順を実行してください。

## <a name="create-peering---azure-cli"></a><a name="cli"></a>ピアリングの作成 - Azure CLI

このチュートリアルでは、サブスクリプションごとに異なるアカウントを使用します。 両方のサブスクリプションへのアクセス許可を持つアカウントを使用している場合は、すべての手順で同じアカウントを使用し、Azure からログアウトする手順をスキップして、ユーザー ロールの割り当てを作成するスクリプト行を削除できます。 次のすべてのスクリプトの UserA@azure.com と UserB@azure.com を、UserA と UserB で使用しているユーザー名に置き換えます。

以下のスクリプトでは:

- Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 アップグレードする必要がある場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。
- Bash シェルで動作します。 Azure CLI スクリプトを Windows クライアントで実行する方法については、「[Windows での Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli-windows)」をご覧ください。

CLI とその依存関係をインストールする代わりに、Azure Cloud Shell を使うことができます。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 次のスクリプトの **[使ってみる]** ボタンを選択すると Cloud Shell が開き、Azure アカウントでログインできます。

1. CLI セッションを開き、`azure login` コマンドを使って UserA として Azure にログインします。 ログインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 アクセス許可の一覧については、[仮想ネットワークのピアリングのアクセス許可](virtual-network-manage-peering.md#permissions)に関するページをご覧ください。
2. PC で次のスクリプトをテキスト エディターにコピーし、`<SubscriptionA-Id>` を SubscriptionA の ID に置き換えた後、変更後のスクリプトをコピーし、CLI セッションに貼り付けて、`Enter` キーを押します。 サブスクリプション ID がわからない場合は、`az account show` コマンドを入力します。 出力された **id** の値がサブスクリプション ID です。

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

3. `az logout` コマンドを使って UserA として Azure からログアウトした後、UserB として Azure にログインします。 ログインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 アクセス許可の一覧については、[仮想ネットワークのピアリングのアクセス許可](virtual-network-manage-peering.md#permissions)に関するページをご覧ください。
4. myVnetB を作成します。 PC で、手順 2 のスクリプトの内容をテキスト エディターにコピーします。 `<SubscriptionA-Id>` を SubscriptionB の ID に置き換えます。 10.0.0.0/16 を 10.1.0.0/16 に、すべての A を B に、すべての B を A に変更します。変更後のスクリプトをコピーし、CLI セッションに貼り付けて、`Enter` キーを押します。
5. UserB として Azure をログアウトし、UserA として Azure にログインします。
6. myVnetA から myVnetB への仮想ネットワーク ピアリングを作成します。 PC で次のスクリプトの内容をテキスト エディターにコピーします。 `<SubscriptionB-Id>` を SubscriptionB の ID に置き換えます。 スクリプトを実行するには、変更後のスクリプトをコピーし、CLI セッションに貼り付けて、Enter キーを押します。

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. myVnetA のピアリングの状態を表示します。

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    状態は **開始済み** です。 myVnetB から myVnetA へのピアリングを作成すると、**接続済み** に変わります。

8. UserA として Azure をログアウトし、UserB として Azure にログインします。
9. myVnetB から myVnetA へのピアリングを作成します。 PC で、手順 6 のスクリプトの内容をテキスト エディターにコピーします。 `<SubscriptionB-Id>` を SubscriptionA の ID に置き換え、すべての A を B に、すべての B を A に変更します。変更したスクリプトをコピーし、CLI セッションに貼り付けて、`Enter` キーを押します。
10. myVnetB のピアリングの状態を表示します。 PC で、手順 7 のスクリプトの内容をテキスト エディターにコピーします。 リソース グループと仮想ネットワークの名前の A を B に変更したスクリプトをコピーし、CLI セッションに貼り付けて、`Enter` キーを押します。 ピアリングの状態は **接続済み** です。 myVnetB から myVnetA へのピアリングを作成した後、myVnetA のピアリングの状態は **接続済み** に変わります。 UserA を Azure に再びログインさせ、手順 7 を再度実行して、myVnetA のピアリング状態を確認します。

    > [!NOTE]
    > 両方の仮想ネットワークでピアリングの状態が **接続済み** になるまで、ピアリングは確立されません。

11. **省略可能**:このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
12. **省略可能**:このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-cli)」の手順を実行してください。

2 つの仮想ネットワークに作成した Azure リソースが、その IP アドレスを使用して相互に通信できるようになりました。 仮想ネットワークに Azure の既定の名前解決を使用する場合、そのネットワーク内のリソースは、通信相手の仮想ネットワークに対して名前を解決することができません。 ピアリングされた仮想ネットワークの間で名前を解決する必要がある場合は、独自の DNS サーバーを作成する必要があります。 その方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」を参照してください。

## <a name="create-peering---powershell"></a><a name="powershell"></a>ピアリングの作成 - PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このチュートリアルでは、サブスクリプションごとに異なるアカウントを使用します。 両方のサブスクリプションへのアクセス許可を持つアカウントを使用している場合は、すべての手順で同じアカウントを使用し、Azure からログアウトする手順をスキップして、ユーザー ロールの割り当てを作成するスクリプト行を削除できます。 次のすべてのスクリプトの UserA@azure.com と UserB@azure.com を、UserA と UserB で使用しているユーザー名に置き換えます。

1. Azure PowerShell バージョン 1.0.0 以降を使用していることを確認します。 これは、`Get-Module -Name Az` を実行することで行えます。最新バージョンの PowerShell の [Az モジュール](/powershell/azure/install-az-ps)をインストールすることをお勧めします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。
2. PowerShell セッションを開始します。
3. PowerShell で、`Connect-AzAccount` コマンドを入力して UserA として Azure にログインします。 ログインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 アクセス許可の一覧については、[仮想ネットワークのピアリングのアクセス許可](virtual-network-manage-peering.md#permissions)に関するページをご覧ください。
4. リソース グループと仮想ネットワーク A を作成します。PC で、次のスクリプトをテキスト エディターにコピーします。 `<SubscriptionA-Id>` を SubscriptionA の ID に置き換えます。 サブスクリプション ID がわからない場合は、`Get-AzSubscription` コマンドを入力して表示します。 返された出力の **id** の値がサブスクリプション ID です。 スクリプトを実行するには、変更後のスクリプトをコピーし、PowerShell に貼り付けて、`Enter` キーを押します。

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Azure から UserA をログアウトし、UserB でログインします。 ログインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 アクセス許可の一覧については、[仮想ネットワークのピアリングのアクセス許可](virtual-network-manage-peering.md#permissions)に関するページをご覧ください。
6. PC で、手順 4 のスクリプトの内容をテキスト エディターにコピーします。 `<SubscriptionA-Id>` を、サブスクリプション B の ID に置き換えます。10.0.0.0/16 を 10.1.0.0/16 に変更します。 すべての A を B に、すべての B を A に変更します。スクリプトを実行するには、変更後のスクリプトをコピーし、PowerShell に貼り付けて、`Enter` キーを押します。
7. Azure から UserB をログアウトし、UserA でログインします。
8. myVnetA から myVnetB へのピアリングを作成します。 PC で次のスクリプトをテキスト エディターにコピーします。 `<SubscriptionB-Id>` を、サブスクリプション B の ID に置き換えます。スクリプトを実行するには、変更後のスクリプトをコピーし、PowerShell に貼り付けて、`Enter` キーを押します。

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. myVnetA のピアリングの状態を表示します。

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    状態は **開始済み** です。 myVnetB から myVnetA へのピアリングの設定が完了した時点で、**接続済み** に変わります。

10. Azure から UserA をログアウトし、UserB でログインします。
11. myVnetB から myVnetA へのピアリングを作成します。 PC で、手順 8 のスクリプトの内容をテキスト エディターにコピーします。 `<SubscriptionB-Id>` をサブスクリプション A の ID に置き換え、すべての A を B に、すべての B を A に変更します。スクリプトを実行するには、変更後のスクリプトをコピーし、PowerShell に貼り付けて、`Enter` キーを押します。
12. myVnetB のピアリングの状態を表示します。 PC で、手順 9 のスクリプトの内容をテキスト エディターにコピーします。 リソース グループと仮想ネットワークの名前の A を B に変更します。 スクリプトを実行するには、変更後のスクリプトを PowerShell に貼り付けて、`Enter` キーを押します。 状態は **接続済み** です。 **myVnetB** から **myVnetA** へのピアリングを作成した後、**myVnetA** のピアリングの状態は **接続済み** に変わります。 UserA を Azure に再びログインさせ、手順 9 を再度実行して、myVnetA のピアリング状態を確認します。

    > [!NOTE]
    > 両方の仮想ネットワークでピアリングの状態が **接続済み** になるまで、ピアリングは確立されません。

    2 つの仮想ネットワークに作成した Azure リソースが、その IP アドレスを使用して相互に通信できるようになりました。 仮想ネットワークに Azure の既定の名前解決を使用する場合、そのネットワーク内のリソースは、通信相手の仮想ネットワークに対して名前を解決することができません。 ピアリングされた仮想ネットワークの間で名前を解決する必要がある場合は、独自の DNS サーバーを作成する必要があります。 その方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」を参照してください。

13. **省略可能**:このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
14. **省略可能**:このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-powershell)」の手順を実行してください。

## <a name="create-peering---resource-manager-template"></a><a name="template"></a>ピアリングの作成 - Resource Manager テンプレート

1. 仮想ネットワークを作成し、適切な[アクセス許可](virtual-network-manage-peering.md#permissions)を割り当てるには、この記事の[ポータル](#portal)、[Azure CLI](#cli)、または [PowerShell](#powershell) のセクションの手順を完了します。
2. 次のテキストをローカル コンピューター上のファイルに保存します。 `<subscription ID>` は、UserA のサブスクリプション ID で置き換えてください。 たとえば、このファイルを vnetpeeringA.json として保存します。

   ```json
   {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
   }
   ```

3. Azure に UserA としてログインし、[ポータル](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template)、[PowerShell](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template-or-bicep-file) または [Azure CLI](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template-or-bicep-file) を使用してテンプレートをデプロイします。 手順 2 で保存したサンプルの json テキストのファイル名を指定します。
4. 手順 2 のサンプル json をコンピューター上のファイルにコピーして、次から始まる行を変更します。
   - **name**:*myVnetA/myVnetAToMyVnetB* を *myVnetB/myVnetBToMyVnetA* に変更します。
   - **id**:`<subscription ID>` を UserB のサブスクリプション ID で置き換え、*myVnetB* を *myVnetA* に変更します。
5. 再び手順 3. を行います。ここでは Azure に UserB としてログインします。
6. **省略可能**:このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
7. **省略可能**:このチュートリアルで作成したリソースを削除するには、Azure portal、PowerShell、Azure CLI のいずれかを使って、この記事の「[リソースの削除](#delete)」セクションの手順を実行してください。

## <a name="delete-resources"></a><a name="delete"></a>リソースの削除
このチュートリアルが完了したら、使用料がかからないように、チュートリアルで作成したリソースを削除することをお勧めします。 リソース グループを削除すると、リソース グループ内にあるリソースもすべて削除されます。

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. Azure Portal に UserA としてログインします。
2. ポータルの検索ボックスに、「**myResourceGroupA**」と入力します。 検索結果で、 **[myResourceGroupA]** を選択します。
3. **[削除]** を選択します。
4. 削除を確定するには、 **[リソース グループ名を入力してください]** ボックスに「**myResourceGroupA**」と入力し、 **[削除]** を選択します。
5. UserA としてポータルからログアウトし、UserB としてログインします。
6. myResourceGroupB に対して手順 2 ～ 4 を実行します。

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. UserA として Azure にログインし、次のコマンドを実行します。

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. UserA として Azure からログアウトし、UserB としてログインします。
3. たとえば、次のコマンドを実行します。

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. UserA として Azure にログインし、次のコマンドを実行します。

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. UserA として Azure からログアウトし、UserB としてログインします。
3. たとえば、次のコマンドを実行します。

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>次のステップ

- 運用環境で使用する仮想ネットワーク ピアリングを作成する前に、[仮想ネットワーク ピアリングの制約と動作](virtual-network-manage-peering.md#requirements-and-constraints)の要点をしっかりと理解します。
- [仮想ネットワーク ピアリングのさまざまな設定](virtual-network-manage-peering.md#create-a-peering)について理解を深めます。
- 仮想ネットワーク ピアリングで[ハブとスポークのネットワーク トポロジを作成](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering)する方法を学習します。