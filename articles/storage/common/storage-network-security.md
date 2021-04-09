---
title: Azure Storage ファイアウォールおよび仮想ネットワークの構成 | Microsoft Docs
description: Azure Storage ファイアウォールと Azure Virtual Network を使用して、ストレージ アカウントの多層型ネットワーク セキュリティを構成します。
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 62f61549ffd6312b94589b9cabbc347edafd0ff2
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601969"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure Storage ファイアウォールおよび仮想ネットワークを構成する

Azure Storage では、多層型セキュリティ モデルが提供されています。 このモデルでは、使用されるネットワークまたはリソースの種類とサブネットに基づいて、アプリケーションやエンタープライズ環境で求められるストレージ アカウントへのアクセス レベルを確保して制御できます。 ネットワーク ルールを構成すると、指定したネットワークのセットまたは指定した Azure リソースのセットを経由してデータを要求しているアプリケーションのみが、ストレージ アカウントにアクセスできます。 ストレージ アカウントへのアクセスを、指定した IP アドレス、IP 範囲、Azure Virtual Network (VNet) 内のサブネット、または一部の Azure サービスのリソース インスタンスから発信された要求に制限できます。

ストレージ アカウントには、インターネットを介してアクセスできるパブリック エンドポイントがあります。 また、[自分のストレージ アカウント用にプライベート エンドポイント](storage-private-endpoints.md)を作成することもできます。自分の VNet からストレージ アカウントにプライベート IP アドレスを割り当てて、プライベート リンクを介して VNet とストレージ アカウントの間のトラフィックをすべて保護できます。 Azure Storage ファイアウォールを使用すると、自分のストレージ アカウントのパブリック エンドポイントへのアクセスを制御できます。 プライベート エンドポイントの使用時には、パブリック エンドポイント経由のすべてのアクセスをブロックするためにファイアウォールを使用することもできます。 また、ストレージ ファイアウォールは、信頼された一部の Azure プラットフォーム サービスがストレージ アカウントに安全にアクセスできるよう構成することもできます。

ネットワーク規則が有効なときにストレージ アカウントにアクセスするアプリケーションでも、要求に対する適切な承認が必要です。 認可は、BLOB とキューに対する Azure Active Directory (Azure AD) の資格情報、有効なアカウント アクセス キー、または SAS トークンでサポートされています。

> [!IMPORTANT]
> ストレージ アカウントのファイアウォール規則を有効にすると、Azure Virtual Network (VNet) 内で動作しているサービス、または許可されたパブリック IP アドレスから送信された要求でない限り、データに対して受信した要求は既定でブロックされます。 ブロックされる要求には、他の Azure サービスからの要求、Azure portal からの要求、ログおよびメトリック サービスからの要求などが含まれます。
>
> サービス インスタンスがホストされているサブネットからのトラフィックを許可することで、VNet 内で動作する Azure サービスにアクセス権を付与できます。 また、以下で説明する例外メカニズムによって、限られた数のシナリオを有効にすることもできます。 Azure portal を通してストレージ アカウントからデータにアクセスするには、設定済みの信頼できる境界 (IP または VNet) 内のコンピューター上にいる必要があります。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>シナリオ

ストレージ アカウントをセキュリティで保護するには、最初に、既定ではパブリック エンドポイントですべてのネットワークからのトラフィック (インターネット トラフィックなど) にアクセスを許可しないことを規則として構成する必要があります。 次に、特定の VNet からのトラフィックにアクセスを許可するルールを構成する必要があります。 また、ルールを構成して、選択したパブリック インターネット IP アドレス範囲からのトラフィックへのアクセスを許可できます。これにより、インターネットやオンプレミスの特定クライアントからの接続を有効にできます。 この構成では、アプリケーションに対してセキュリティで保護されたネットワーク境界を構築することができます。

同じストレージ アカウントでは、ファイアウォール規則を組み合わせて、特定の仮想ネットワークからとパブリック IP アドレス範囲からのアクセスを許可することができます。 ストレージ ファイアウォール規則は、既存のストレージ アカウントに適用できます。または、新しいストレージ アカウントの作成時に適用できます。

ストレージ ファイアウォール規則は、ストレージ アカウントのパブリック エンドポイントに適用されます。 ストレージ アカウントのプライベート エンドポイントへのトラフィックを許可するのにファイアウォール アクセス規則は必要ありません。 プライベート エンドポイントの作成を承認するプロセスで、プライベート エンドポイントをホストするサブネットからのトラフィックへのアクセスが暗黙的に許可されます。

ネットワーク ルールは、Azure Storage のすべてのネットワーク プロトコル (REST と SMB を含む) に適用されます。 Azure portal、Storage Explorer、AZCopy などのツールを使用してデータにアクセスするには、明示的なネットワーク ルールを構成する必要があります。

適用したネットワーク ルールは、すべての要求に対して適用されます。 特定の IP アドレスへのアクセスを許可する SAS トークンは、トークン所有者のアクセスを制限する働きをしますが、構成されているネットワーク ルールを超えて新しいアクセスを許可することはありません。

仮想マシン ディスクのトラフィック (マウントとマウント解除、およびディスク IO を含む) は、ネットワーク ルールによる影響を受けません。 ページ BLOB への REST アクセスはネットワーク ルールによって保護されています。

従来のストレージ アカウントでは、ファイアウォールと仮想ネットワークはサポートされていません。

ネットワーク ルールが適用されたストレージ アカウントでアンマネージド ディスクを使用し、例外を作成することにより、VM をバックアップおよび復元できます。 このプロセスについては、この記事の「[例外を管理する](#manage-exceptions)」セクションをご覧ください。 ファイアウォールの例外は Azure によって既に管理されているので、マネージド ディスクには適用されません。

## <a name="change-the-default-network-access-rule"></a>既定のネットワーク アクセス ルールの変更

既定では、ストレージ アカウントは、任意のネットワーク上のクライアントからの接続を受け入れます。 選択したネットワークへのアクセスを制限するには、まず既定のアクションを変更する必要があります。

> [!WARNING]
> ネットワーク ルールを変更すると、Azure Storage に接続するアプリケーションの機能に影響が及ぶことがあります。 既定のネットワーク ルールを **拒否** に設定すると、アクセスを **許可** する特定のネットワーク ルールも合わせて適用されていない限り、データへのアクセスがすべてブロックされます。 アクセスを拒否する既定のルールを変更する前に、ネットワーク ルールを使用して、許可されたネットワークへのアクセスを許可するようにしてください。

### <a name="managing-default-network-access-rules"></a>既定のネットワーク アクセス ルールを管理する

Azure portal、PowerShell、または CLIv2 を使用して、ストレージ アカウントに対する既定のネットワーク アクセス ルールを管理できます。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. セキュリティで保護するストレージ アカウントに移動します。

2. **[ネットワーク]** という名前の設定メニューをクリックします。

3. 既定でアクセスを拒否するには、 **[選択されたネットワーク]** からのアクセスを許可するように選択します。 すべてのネットワークからのトラフィックを許可するには、 **[すべてのネットワーク]** からのアクセスを許可するように選択します。

4. **[保存]** を選択して変更を保存します。

<a id="powershell"></a>

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps) をインストールして[サインイン](/powershell/azure/authenticate-azureps)します。

2. ストレージ アカウントの既定のルールの状態を表示します。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. 既定でネットワーク アクセスを拒否する既定のルールを設定します。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. 既定でネットワーク アクセスを許可する既定のルールを設定します。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) をインストールして[サインイン](/cli/azure/authenticate-azure-cli)します。

2. ストレージ アカウントの既定のルールの状態を表示します。

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. 既定でネットワーク アクセスを拒否する既定のルールを設定します。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. 既定でネットワーク アクセスを許可する既定のルールを設定します。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```
---

## <a name="grant-access-from-a-virtual-network"></a>仮想ネットワークからアクセスの許可

特定のサブネットからのアクセスのみを許可するように、ストレージ アカウントを構成できます。 許可するサブネットは、同じサブスクリプション内の VNet に属していても、または異なる Azure Active Directory テナントに属するサブスクリプションなど、異なるサブスクリプション内のものであってもかまいません。

VNet 内の Azure Storage に対する[サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)を有効にします。 サービス エンドポイントでは、VNet からのトラフィックが、最適なパスを経由して、Azure Storage サービスにルーティングされます。 サブネットと仮想ネットワークの ID も、各要求と一緒に転送されます。 管理者は、その後、VNet 内の特定のサブネットからの要求の受信を許可するネットワーク ルールを、ストレージ アカウントに対して構成できます。 これらのネットワーク ルールによってアクセスを許可されたクライアントがデータにアクセスするには、ストレージ アカウントの認可要件を引き続き満たす必要があります。

各ストレージ アカウントでは最大 200 個の仮想ネットワーク規則がサポートされ、それを [IP ネットワーク ルール](#grant-access-from-an-internet-ip-range)と組み合わせることができます。

### <a name="available-virtual-network-regions"></a>使用可能な仮想ネットワークのリージョン

一般に、サービス エンドポイントは、同じ Azure リージョン内の仮想ネットワークとサービス インスタンスの間で機能します。 サービス エンドポイントを Azure Storage と共に使用すると、この範囲は[ペアのリージョン](../../best-practices-availability-paired-regions.md)を含むように拡張されます。 サービス エンドポイントにより、リージョンのフェールオーバー時の継続性と、読み取り専用の geo 冗長ストレージ (RA-GRS) インスタンスへのアクセスが実現されます。 仮想ネットワークからストレージ アカウントへのアクセスを許可するネットワー ルールでは、任意の RA-GRS インスタンスへのアクセスも許可します。

リージョンが停止したときのディザスター リカバリーを計画するときは、ペアのリージョン内にあらかじめ VNet を作成しておく必要があります。 これらの代替仮想ネットワークからのアクセスを許可するネットワーク ルールで、Azure Storage 用のサービス エンドポイントを有効にします。 その後、これらのルールを geo 冗長ストレージ アカウントに適用します。

> [!NOTE]
> サービス エンドポイントは、仮想ネットワークのリージョンと指定されたリージョン ペアの外部のトラフィックには適用されません。 仮想ネットワークからストレージ アカウントへのアクセスを許可するネットワーク ルールは、ストレージ アカウントのプライマリ リージョン内または指定したペアのリージョン内でのみ適用できます。

### <a name="required-permissions"></a>必要なアクセス許可

ストレージ アカウントに仮想ネットワーク規則を適用するには、追加されるサブネットに対する適切なアクセス許可を持っている必要があります。 規則の適用は、[Storage Account Contributor](../../role-based-access-control/built-in-roles.md#storage-account-contributor)か、カスタム Azure ロール経由で `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` [Azure リソース プロバイダー操作](../../role-based-access-control/resource-provider-operations.md#microsoftnetwork)にアクセスする権利が与えられているユーザーが実行できます。

ストレージ アカウントとアクセスを許可される仮想ネットワークは、異なる Azure AD テナントの一部であるサブスクリプションなど、異なるサブスクリプションに含まれていてもかまいません。

> [!NOTE]
> 異なる Azure Active Directory テナントの一部である仮想ネットワーク内のサブネットへのアクセスを許可するルールの構成は、現在、Powershell、CLI、および REST API でのみサポートされています。 このようなルールを Azure portal を使用して構成することはできませんが、ポータルで表示することはできます。

### <a name="managing-virtual-network-rules"></a>仮想ネットワーク規則の管理

ストレージ アカウントの仮想ネットワーク規則は、Azure portal、PowerShell、または CLIv2 で管理できます。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. セキュリティで保護するストレージ アカウントに移動します。

2. **[ネットワーク]** という名前の設定メニューをクリックします。

3. **[選択されたネットワーク]** からのアクセスを許可するように選択していることを確認します。

4. 新しいネットワーク ルールで仮想ネットワークへのアクセスを許可するには、 **[仮想ネットワーク]** で、 **[既存の仮想ネットワークを追加]** を選択し、 **[仮想ネットワーク]** 、 **[サブネット]** オプションの順に選択して、 **[追加]** を選択します。 新しい仮想ネットワークを作成してアクセスを許可するには、 **[Add new virtual network]\(新しい仮想ネットワークを追加\)** をクリックします。 新しい仮想ネットワークの作成に必要な情報を指定して、 **[作成]** を選択します。

    > [!NOTE]
    > Azure Storage 用のサービス エンドポイントが、選択した仮想ネットワークとサブネットに対してまだ構成されていない場合は、この操作の中で構成することができます。
    >
    > 現在、ルールの作成時に選択できるのは、同じ Azure Active Directory テナントに属する仮想ネットワークのみです。 別のテナントに属する仮想ネットワーク内のサブネットにアクセスを許可するには、Powershell、CLI、または REST API を使用してください。

5. 仮想ネットワークまたはサブネットのルールを削除するには、 **[...]** を選択して仮想ネットワークまたはサブネットのコンテキスト メニューを開き、 **[削除]** を選択します。

6. **[保存]** を選択して変更を保存します。

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps) をインストールして[サインイン](/powershell/azure/authenticate-azureps)します。

2. 仮想ネットワーク規則を一覧表示します。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. 既存の仮想ネットワークとサブネットで、Azure Storage 用のサービス エンドポイントを有効にします。

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. 仮想ネットワークとサブネットに対するネットワーク ルールを追加します。

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > 別の Azure AD テナントに属する VNet 内のサブネットに対するネットワーク ルールを追加するには、"/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" という形式の完全修飾 **VirtualNetworkResourceId** パラメーターを使用します。

5. 仮想ネットワークとサブネットのネットワーク ルールを削除します。

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> **拒否** するように [既定のルールを設定](#change-the-default-network-access-rule)します。そうしないと、ネットワーク ルールは効力を発揮しません。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) をインストールして[サインイン](/cli/azure/authenticate-azure-cli)します。

2. 仮想ネットワーク規則を一覧表示します。

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. 既存の仮想ネットワークとサブネットで、Azure Storage 用のサービス エンドポイントを有効にします。

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. 仮想ネットワークとサブネットに対するネットワーク ルールを追加します。

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > 別の Azure AD テナントに属する VNet 内のサブネット用にルールを追加するには、"/subscriptions/\<subscription-ID\>/resourceGroups/\<resourceGroup-Name\>/providers/Microsoft.Network/virtualNetworks/\<vNet-name\>/subnets/\<subnet-name\>" という形式で完全修飾サブネット ID を使用します。
    >
    > **subscription** パラメーターを使用して、別の Azure AD テナントに属する VNet のサブネット ID を取得できます。

5. 仮想ネットワークとサブネットのネットワーク ルールを削除します。

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> **拒否** するように [既定のルールを設定](#change-the-default-network-access-rule)します。そうしないと、ネットワーク ルールは効力を発揮しません。

---

## <a name="grant-access-from-an-internet-ip-range"></a>インターネットの IP 範囲からのアクセスを許可する

パブリック インターネットの特定の IP アドレス範囲からのアクセスを許可するように、ストレージ アカウントを構成できます。 この構成では、特定のインターネット ベースのサービスとオンプレミスのネットワークにアクセスを許可し、一般的なインターネット トラフィックをブロックします。

許可するインターネット アドレスの範囲は、[CIDR 表記法](https://tools.ietf.org/html/rfc4632)を使って *16.17.18.0/24* の形式で、または *16.17.18.19* のように個々の IP アドレスとして、指定できます。

   > [!NOTE]
   > 「/31」や「/32」のプレフィックス サイズを使用した小さなアドレス範囲はサポートされていません。 これらの範囲は、個々の IP アドレス ルールを使用して構成する必要があります。

IP ネットワーク ルールは、**パブリック インターネット** の IP アドレスに対してのみ許可されます。 プライベート ネットワーク用に予約されている IP アドレス範囲 ([RFC 1918](https://tools.ietf.org/html/rfc1918#section-3) で定義) は、IP ルールでは許可されません。 プライベート ネットワークには、_10.*_ 、_172.16.*_  - _172.31.*_ 、_192.168.*_ で始まるアドレスが含まれます。

   > [!NOTE]
   > IP ネットワーク ルールは、ストレージ アカウントと同じ Azure リージョンから送信された要求には影響ありません。 同じリージョンの要求を許可するには、[仮想ネットワーク規則](#grant-access-from-a-virtual-network)を使用します。

  > [!NOTE]
  > ストレージ アカウントと同じリージョンでデプロイされたサービスでは、プライベート Azure IP アドレスが通信に使用されます。 そのため、特定の Azure サービスへのアクセスを、そのパブリック送信 IP アドレスの範囲に基づいて制限することはできません。

ストレージ ファイアウォール規則の構成では、IPv4 アドレスのみがサポートされています。

各ストレージ アカウントでは、最大 200 個の IP ネットワーク規則を設定できます。

### <a name="configuring-access-from-on-premises-networks"></a>オンプレミスのネットワークからのアクセスの構成

IP ネットワーク ルールでオンプレミスのネットワークからストレージ アカウントへのアクセスを許可するには、ネットワークで使用するインターネット接続 IP アドレスを特定する必要があります。 サポートが必要な場合は、ネットワーク管理者にお問い合わせください。

パブリック ピアリングまたは Microsoft ピアリングのためにオンプレミスから [ExpressRoute](../../expressroute/expressroute-introduction.md) を使用している場合、使用されている NAT の IP アドレスを識別する必要があります。 パブリック ピアリングの場合、既定で、Azure サービスのトラフィックが Microsoft Azure のネットワーク バックボーンに入ったときに適用される 2 つの NAT IP アドレスが各 ExpressRoute 回線に使用されます。 Microsoft ピアリングの場合、使用される NAT の IP アドレスは、ユーザーが指定するか、サービス プロバイダーが指定します。 サービス リソースへのアクセスを許可するには、リソースの IP ファイアウォール設定でこれらのパブリック IP アドレスを許可する必要があります。 パブリック ピアリングの ExpressRoute 回線の IP アドレスを確認するには、Azure Portal から [ExpressRoute のサポート チケットを開いて](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)ください。 詳細については、[ExpressRoute のパブリック ピアリングと Microsoft ピアリングの NAT](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering) に関するセクションを参照してください。

### <a name="managing-ip-network-rules"></a>IP ネットワーク ルールの管理

ストレージ アカウントの IP ネットワーク ルールは、Azure portal、PowerShell、または CLIv2 で管理できます。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. セキュリティで保護するストレージ アカウントに移動します。

2. **[ネットワーク]** という名前の設定メニューをクリックします。

3. **[選択されたネットワーク]** からのアクセスを許可するように選択していることを確認します。

4. インターネット IP 範囲へのアクセスを許可するには、 **[ファイアウォール]**  >  **[アドレス範囲]** で IP アドレスまたはアドレス範囲 (CIDR 形式) を入力します。

5. IP ネットワーク ルールを削除するには、アドレス範囲の横にあるごみ箱アイコンを選択します。

6. **[保存]** を選択して変更を保存します。

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps) をインストールして[サインイン](/powershell/azure/authenticate-azureps)します。

2. IP ネットワーク ルールを一覧表示します。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. 個々 の IP アドレスに対するネットワーク ルールを追加します。

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. IP アドレス範囲に対するネットワーク ルールを追加します。

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. 個々 の IP アドレスに対するネットワーク ルールを削除します。

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. IP アドレス範囲に対するネットワーク ルールを削除します。

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> **拒否** するように [既定のルールを設定](#change-the-default-network-access-rule)します。そうしないと、ネットワーク ルールは効力を発揮しません。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) をインストールして[サインイン](/cli/azure/authenticate-azure-cli)します。

1. IP ネットワーク ルールを一覧表示します。

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. 個々 の IP アドレスに対するネットワーク ルールを追加します。

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. IP アドレス範囲に対するネットワーク ルールを追加します。

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. 個々 の IP アドレスに対するネットワーク ルールを削除します。

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. IP アドレス範囲に対するネットワーク ルールを削除します。

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> **拒否** するように [既定のルールを設定](#change-the-default-network-access-rule)します。そうしないと、ネットワーク ルールは効力を発揮しません。

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>Azure リソース インスタンスからのアクセスを許可する (プレビュー)

場合によっては、アプリケーションが、仮想ネットワークまたは IP アドレス ルールによって分離できない Azure リソースに依存していることがあります。 しかし、その場合でも、ストレージ アカウントのアクセスをセキュリティで保護してアプリケーションの Azure リソースのみに制限したいことがあります。 リソース インスタンス ルールを作成することによって、一部の Azure サービスの特定のリソース インスタンスにアクセスを許可するよう、ストレージ アカウントを構成できます。 

リソース インスタンスがストレージ アカウントのデータに対して実行できる操作の種類は、リソース インスタンスの [Azure ロールの割り当て](storage-auth-aad.md#assign-azure-roles-for-access-rights)によって決まります。 リソース インスタンスは、ストレージ アカウントと同じテナントからのものである必要がありますが、テナント内のどのサブスクリプションに属していてもかまいません。

> [!NOTE]
> この機能はパブリック プレビュー段階であり、すべてのパブリック クラウド リージョンで利用できます。

> [!NOTE]
> 現在、リソース インスタンス ルールは Azure Synapse でのみサポートされています。 この記事の「[システム割り当てマネージド ID に基づく信頼されたアクセス](#trusted-access-system-assigned-managed-identity)」セクションに掲載されている他の Azure サービスは、今後数週間以内にサポートされる予定です。


### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal で、リソース ネットワーク ルールを追加または削除できます。

1. [Azure ポータル](https://portal.azure.com/)にサインインして、作業を開始します。

2. ストレージ アカウントを見つけて、アカウントの概要を表示します。

3. **[ネットワーク]** を選択して、ネットワークの構成ページを表示します。

4. **[リソースの種類]** ドロップダウン リストで、リソース インスタンスのリソースの種類を選択します。 

5. **[インスタンス名]** ドロップダウン リストで、リソース インスタンスを選択します。 また、アクティブなテナント、サブスクリプション、またはリソース グループ内のすべてのリソース インスタンスを含めるように選択することもできます。

6. **[保存]** を選択して変更を適用します。 リソース インスタンスが、ネットワーク設定ページの **[リソース インスタンス]** セクションに表示されます。 

リソース インスタンスを削除するには、リソース インスタンスの横にある削除アイコン (:::image type="icon" source="media/storage-network-security/delete-icon.png":::) を選択します。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell コマンドを使用して、リソース ネットワーク ルールを追加または削除できます。

> [!IMPORTANT]
> **拒否** するように [既定のルールを設定](#change-the-default-network-access-rule)します。そうしないと、ネットワーク ルールは効力を発揮しません。

#### <a name="install-the-preview-module"></a>プレビュー モジュールをインストールする

PowerShellGet モジュールの最新バージョンをインストールします。 次に、PowerShell コンソールを閉じてから再度開きます。

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

**Az.Storage** プレビュー モジュールをインストールします。

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

PowerShell モジュールのインストール方法の詳細については、「[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)」を参照してください。

#### <a name="grant-access"></a>アクセス権の付与

リソース インスタンスからのアクセスを許可するネットワーク ルールを追加します。

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

ネットワーク ルール セットを変更することにより、一度に複数のリソース インスタンスを指定します。

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>アクセス権を削除する

リソース インスタンスからのアクセスを許可するネットワーク ルールを削除します。

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

リソース インスタンスからのアクセスを許可するすべてのネットワーク ルールを削除します。

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>許可されているリソース インスタンスの一覧を表示する

ストレージ アカウントへのアクセスを許可されているリソース インスタンスの完全な一覧を表示します。

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI コマンドを使用して、リソース ネットワーク ルールを追加または削除できます。

#### <a name="install-the-preview-extension"></a>プレビュー拡張機能をインストールする

1. [Azure Cloud Shell](../../cloud-shell/overview.md) を開きます。または、Azure CLI をローカルに[インストール](/cli/azure/install-azure-cli)した場合は、Windows PowerShell などのコマンド コンソール アプリケーションを開きます。

2. 次に、次のコマンドを使用して、インストールされている Azure CLI のバージョンが `2.13.0` 以上であることを確認します。

   ```azurecli
   az --version
   ```

   Azure CLI のバージョンが `2.13.0` より低い場合は、新しいバージョンをインストールします。 「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

3. 次のコマンドを入力して、プレビュー拡張機能をインストールします。

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>アクセス権の付与

リソース インスタンスからのアクセスを許可するネットワーク ルールを追加します。

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>アクセス権を削除する

リソース インスタンスからのアクセスを許可するネットワーク ルールを削除します。

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>許可されているリソース インスタンスの一覧を表示する

ストレージ アカウントへのアクセスを許可されているリソース インスタンスの完全な一覧を表示します。

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-trusted-azure-services"></a>信頼された Azure サービスにアクセスを許可する 

一部の Azure サービスは、ネットワーク ルールに含めることのできないネットワークから動作します。 他のアプリに対するネットワーク ルールを維持しながら、このような信頼された Azure サービスのサブセットにストレージ アカウントへのアクセスを許可できます。 これらの信頼されるサービスでは、強力な認証を使用して、ストレージ アカウントに安全に接続します。

ネットワーク ルールの例外を作成することによって、信頼された Azure サービスにアクセスを許可できます。 詳細な手順については、この記事の「[例外を管理する](#manage-exceptions)」セクションを参照してください。

信頼された Azure サービスにアクセスを許可する場合は、次の種類のアクセスを許可します。

- サブスクリプションに登録されているリソースに対する選択された操作のための信頼されたアクセス。
- システム割り当てマネージド ID に基づくリソースへの信頼されたアクセス。

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>サブスクリプションに登録されているリソースへの信頼されたアクセス

一部のサービスのリソースは、**お客様のサブスクリプションで登録されている場合に**、特定の操作 (ログの書き込みやバックアップなど) のために **同じサブスクリプション内の** ストレージ アカウントにアクセスできます。  次の表では、各サービスと許可される操作について説明します。 

| サービス                  | リソース プロバイダー名     | 許可される操作                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | IAAS 仮想マシンの管理対象外のディスクのバックアップとリストアを実行します。 (マネージド ディスクの場合は必須ではありません)。 [詳細については、こちらを参照してください](../../backup/backup-overview.md)。 |
| Azure Data Box           | Microsoft.DataBox          | Data Box を使用して Azure にデータをインポートできるようにします。 [詳細については、こちらを参照してください](../../databox/data-box-overview.md)。 |
| Azure DevTest Labs       | Microsoft.DevTestLab       | カスタム イメージの作成とアーティファクトのインストール [詳細については、こちらを参照してください](../../devtest-labs/devtest-lab-overview.md)。 |
| Azure Event Grid         | Microsoft.EventGrid        | Blob Storage のイベント発行を有効にし、ストレージ キューへの発行を Event Grid に許可します。 [Blob Storage イベント](../../event-grid/overview.md#event-sources)と[キューへの発行](../../event-grid/event-handlers.md)について確認してください。 |
| Azure Event Hubs         | Microsoft.EventHub         | Event Hubs Capture を使用したアーカイブ データのキャプチャ [詳細については、こちらを参照してください](../../event-hubs/event-hubs-capture-overview.md)。 |
| Azure File Sync          | Microsoft.StorageSync      | オンプレミスのファイル サーバーを Azure ファイル共有のキャッシュに変換できます。 マルチサイト同期、迅速なディザスターリカバリー、クラウド側バックアップが可能となります。 [詳細情報](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | 新しい HDInsight クラスターのための既定のファイル システムの初期コンテンツをプロビジョニングします。 [詳細については、こちらを参照してください](../../hdinsight/hdinsight-hadoop-use-blob-storage.md)。 |
| Azure Import Export      | Microsoft.ImportExport     | Azure Storage Import/Export サービスを使用すると、Azure Storage にデータをインポートしたり、Azure Storage からデータをエクスポートしたりできます。 [詳細については、こちらを参照してください](../../import-export/storage-import-export-service.md)。  |
| Azure Monitor            | Microsoft.Insights         | リソース ログ、Azure Active Directory サインインと監査ログ、Microsoft Intune ログなど、セキュリティで保護されたストレージ アカウントへの監視データの書き込みを許可します。 [詳細については、こちらを参照してください](../../azure-monitor/roles-permissions-security.md)。 |
| Azure のネットワーク         | Microsoft.Network          | Network Watcher および Traffic Analytics サービスを含め、ネットワーク トラフィック ログを格納し、分析します。 [詳細については、こちらを参照してください](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)。 |
| Azure Site Recovery      | Microsoft.SiteRecovery     | ファイアウォールが有効なキャッシュ、ソース、またはターゲット ストレージ アカウントを使用している場合、Azure IaaS 仮想マシンのディザスター リカバリーのレプリケーションを有効にします。  [詳細については、こちらを参照してください](../../site-recovery/azure-to-azure-tutorial-enable-replication.md)。 |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>システム割り当てマネージド ID に基づく信頼されたアクセス

次の表の一覧で示されているサービスは、それらのサービスのリソース インスタンスに適切なアクセス許可が付与されている場合、ストレージ アカウントのデータにアクセスできます。 アクセス許可を付与するには、各リソース インスタンスの[システム割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) への [Azure ロールの割り当て](storage-auth-aad.md#assign-azure-roles-for-access-rights)を明示的に行う必要があります。 この場合、インスタンスのアクセス範囲は、マネージド ID に割り当てられた Azure ロールに対応します。 

> [!TIP]
> 特定のリソースにアクセスを許可する推奨される方法は、リソース インスタンス ルールを使用することです。 特定のリソース インスタンスにアクセスを許可するには、この記事の「[Azure リソース インスタンスからのアクセスを許可する (プレビュー)](#grant-access-specific-instances)」を参照してください。


| サービス                        | リソース プロバイダー名                 | 目的            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API Management           | Microsoft.ApiManagement/service        | ポリシーを使用して、API Management サービスが、ファイアウォールの背後にあるストレージ アカウントにアクセスできるようにします。 [詳細については、こちらを参照してください](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy)。 |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | インデックス作成、処理、およびクエリのために、Cognitive Search サービスがストレージ アカウントにアクセスできるようになります。 |
| Azure Cognitive Services       | Microsoft.CognitiveService/accounts    | Cognitive Services がストレージ アカウントにアクセスできるようにします。 |
| Azure Container Registry タスク | Microsoft.ContainerRegistry/registries | ACR タスクは、コンテナー イメージを作成するときにストレージアカウントにアクセスできます。 |
| Azure Data Factory             | Microsoft.DataFactory/factories        | ADF ランタイムを使用してストレージ アカウントへのアクセスを許可します。 |
| Azure Data Share               | Microsoft.DataShare/accounts           | Data Share を使用してストレージ アカウントにアクセスできるようになります。 |
| Azure DevTest Labs             | Microsoft.DevTestLab/labs              | DevTest Labs からのストレージ アカウントへのアクセスを許可します。 |
| Azure IoT Hub                  | Microsoft.Devices/IotHubs              | IoT ハブからのデータを BLOB ストレージに書き込むことができます。 [詳細情報](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft.Logic/workflows              | ロジック アプリがストレージ アカウントにアクセスできるようにします。 [詳細については、こちらを参照してください](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)。 |
| Azure Machine Learning サービス | Microsoft.MachineLearningServices      | 承認された Azure Machine Learning ワークスペースでは、BLOB ストレージに実験の出力、モデル、およびログを書き込み、データを読み取ります。 [詳細については、こちらを参照してください](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources)。 |
| Azure Media Services           | Microsoft.Media/mediaservices          | Media Services からのストレージ アカウントへのアクセスを許可します。 |
| Azure Migrate                  | Microsoft.Migrate/migrateprojects      | Azure Migrate からのストレージ アカウントへのアクセスを許可します。 |
| Azure Purview                  | Microsoft.Purview/accounts             | Purview にストレージ アカウントへのアクセスを許可します。 |
| Azure Remote Rendering         | Microsoft.MixedReality/remoteRenderingAccounts | Remote Rendering からのストレージ アカウントへのアクセスを許可します。 |
| Azure Site Recovery            | Microsoft.RecoveryServices/vaults      | Site Recovery からのストレージ アカウントへのアクセスを許可します。 |
| Azure SQL データベース             | Microsoft.Sql                          | ファイアウォールの内側にあるストレージ アカウントへの監査データの[書き込み](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md)を許可します。 |
| Azure Synapse Analytics        | Microsoft.Sql                          | COPY ステートメントまたは PolyBase を使用して (専用プール)、またはサーバーレス プールで `openrowset` 関数と外部テーブルを使用して、特定の SQL データベースのデータのインポートとエクスポートを行うことを許可します。 [詳細については、こちらを参照してください](../../azure-sql/database/vnet-service-endpoint-rule-overview.md)。 |
| Azure Stream Analytics         | Microsoft.StreamAnalytics              | ストリーミング ジョブからのデータを Blob Storage に書き込むことができます。 [詳細については、こちらを参照してください](../../stream-analytics/blob-output-managed-identity.md)。 |
| Azure Synapse Analytics        | Microsoft.Synapse ワークスペース           | Azure Synapse Analytics から Azure Storage のデータにアクセスできるようにします。 |

## <a name="grant-access-to-storage-analytics"></a>Storage Analytics へのアクセスを許可する

場合によっては、ネットワーク境界の外側からリソース ログとメトリックを読み取るためにアクセスする必要があります。 信頼されたサービスによるストレージ アカウントへのアクセスを構成している場合、ネットワーク ルールの例外を作成することにより、ログ ファイルとメトリック テーブルの一方またはその両方に対する読み取りアクセスを許可できます。 詳細な手順については、後の「**例外を管理する**」セクションを参照してください。 Storage Analytics の操作の詳細については、[Azure Storage Analytics を使用したログとメトリック データの収集](./storage-analytics.md)に関するページを参照してください。 

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>例外を管理する

ネットワーク ルールの例外は、Azure portal、PowerShell、または Azure CLI v2 を通じて管理できます。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. セキュリティで保護するストレージ アカウントに移動します。

2. **[ネットワーク]** という名前の設定メニューをクリックします。

3. **[選択されたネットワーク]** からのアクセスを許可するように選択していることを確認します。

4. **[例外]** で、許可する例外を選択します。

5. **[保存]** を選択して変更を保存します。

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps) をインストールして[サインイン](/powershell/azure/authenticate-azureps)します。

2. ストレージ アカウントのネットワーク ルールの例外を表示します。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. ストレージ アカウントのネットワーク ルールの例外を設定します。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. ストレージ アカウントのネットワーク ルールの例外を削除します。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> **拒否** するように [既定のルールを設定](#change-the-default-network-access-rule)していることを確認します。そうしないと、例外の削除は効力を発揮しません。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) をインストールして[サインイン](/cli/azure/authenticate-azure-cli)します。

2. ストレージ アカウントのネットワーク ルールの例外を表示します。

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. ストレージ アカウントのネットワーク ルールの例外を設定します。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. ストレージ アカウントのネットワーク ルールの例外を削除します。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> **拒否** するように [既定のルールを設定](#change-the-default-network-access-rule)していることを確認します。そうしないと、例外の削除は効力を発揮しません。

---

## <a name="next-steps"></a>次のステップ

[サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)で Azure ネットワークのサービス エンドポイントについて確認してください。

[Azure Storage セキュリティ ガイド](../blobs/security-recommendations.md)で Azure Storage のセキュリティを詳しく調べてください。