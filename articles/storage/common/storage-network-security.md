---
title: Azure Storage ファイアウォールおよび仮想ネットワークの構成 | Microsoft Docs
description: Azure Storage ファイアウォールと Azure Virtual Network を使用して、ストレージ アカウントの多層型ネットワーク セキュリティを構成します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 3ec4e6ee49052657210fffa8976b661c1a9e5948
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827462"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure Storage ファイアウォールおよび仮想ネットワークを構成する

Azure Storage では、多層型セキュリティ モデルが提供されています。 このモデルでは、使用されるネットワークの種類とサブネットに基づいて、アプリケーションやエンタープライズ環境で求められるストレージ アカウントへのアクセス レベルを確保して制御できます。 ネットワーク ルールを構成すると、指定したネットワークのセットを経由してデータを要求しているアプリケーションのみが、ストレージ アカウントにアクセスできます。 ストレージ アカウントへのアクセスを、Azure Virtual Network (VNet) 内の指定した IP アドレス、IP 範囲、またはサブネットのリストから発信された要求に制限できます。

ストレージ アカウントには、インターネットを介してアクセスできるパブリック エンドポイントがあります。 また、[自分のストレージ アカウント用にプライベート エンドポイント](storage-private-endpoints.md)を作成することもできます。自分の VNet からストレージ アカウントにプライベート IP アドレスを割り当てて、プライベート リンクを介して VNet とストレージ アカウントの間のトラフィックをすべて保護できます。 Azure ストレージ ファイアウォールを使用すると、自分のストレージ アカウントのパブリック エンドポイントへのアクセスを制御できます。 プライベート エンドポイントの使用時には、パブリック エンドポイント経由のすべてのアクセスをブロックするためにファイアウォールを使用することもできます。 また、ストレージ ファイアウォールは、信頼された一部の Azure プラットフォーム サービスがストレージ アカウントに安全にアクセスできるよう構成することもできます。

ネットワーク規則が有効なときにストレージ アカウントにアクセスするアプリケーションでも、要求に対する適切な承認が必要です。 認可は、BLOB とキューに対する Azure Active Directory (Azure AD) の資格情報、有効なアカウント アクセス キー、または SAS トークンでサポートされています。

> [!IMPORTANT]
> ストレージ アカウントのファイアウォール規則を有効にすると、Azure Virtual Network (VNet) 内で動作しているサービス、または許可されたパブリック IP アドレスから送信された要求でない限り、データに対して受信した要求は既定でブロックされます。 ブロックされる要求には、他の Azure サービスからの要求、Azure portal からの要求、ログおよびメトリック サービスからの要求などが含まれます。
>
> サービス インスタンスがホストされているサブネットからのトラフィックを許可することで、VNet 内で動作する Azure サービスにアクセス権を付与できます。 また、以下で説明する[例外](#exceptions)メカニズムによって、限られた数のシナリオを有効にすることもできます。 Azure portal を通してストレージ アカウントからデータにアクセスするには、設定済みの信頼できる境界 (IP または VNet) 内のコンピューター上にいる必要があります。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>シナリオ

ストレージ アカウントをセキュリティで保護するには、最初に、既定ではパブリック エンドポイントですべてのネットワークからのトラフィック (インターネット トラフィックなど) にアクセスを許可しないことを規則として構成する必要があります。 次に、特定の VNet からのトラフィックにアクセスを許可するルールを構成する必要があります。 また、選択したパブリック インターネット IP アドレス範囲からのトラフィックにアクセスを許可して、インターネットやオンプレミスの特定のクライアントからの接続を有効にすることもできます。 この構成では、アプリケーションに対してセキュリティで保護されたネットワーク境界を構築することができます。

同じストレージ アカウントでは、ファイアウォール規則を組み合わせて、特定の仮想ネットワークからとパブリック IP アドレス範囲からのアクセスを許可することができます。 ストレージ ファイアウォール規則は、既存のストレージ アカウントに適用できます。または、新しいストレージ アカウントの作成時に適用できます。

ストレージ ファイアウォール規則は、ストレージ アカウントのパブリック エンドポイントに適用されます。 ストレージ アカウントのプライベート エンドポイントへのトラフィックを許可するのにファイアウォール アクセス規則は必要ありません。 プライベート エンドポイントの作成を承認するプロセスで、プライベート エンドポイントをホストするサブネットからのトラフィックへのアクセスが暗黙的に許可されます。

Azure Storage に対して、REST や SMB などのすべてのネットワーク プロトコルにネットワーク ルールが適用されます。 Azure portal、Storage Explorer、AZCopy などのツールを使用してデータにアクセスするには、明示的なネットワーク ルールを構成する必要があります。

適用したネットワーク ルールは、すべての要求に対して適用されます。 特定の IP アドレスへのアクセスを許可する SAS トークンは、トークン所有者のアクセスを制限する働きをしますが、構成されているネットワーク ルールを超えて新しいアクセスを許可することはありません。

仮想マシン ディスクのトラフィック (マウントとマウント解除、およびディスク IO を含む) は、ネットワーク ルールによる影響を受けません。 ページ BLOB への REST アクセスはネットワーク ルールによって保護されています。

従来のストレージ アカウントでは、ファイアウォールと仮想ネットワークはサポートされていません。

ネットワーク ルールが適用されたストレージ アカウントでアンマネージド ディスクを使用し、例外を作成することにより、VM をバックアップおよび復元できます。 このプロセスについては、この記事の「[例外](#exceptions)」セクションをご覧ください。 ファイアウォールの例外は Azure によって既に管理されているので、マネージド ディスクには適用されません。

## <a name="change-the-default-network-access-rule"></a>既定のネットワーク アクセス ルールの変更

既定では、ストレージ アカウントは、任意のネットワーク上のクライアントからの接続を受け入れます。 選択したネットワークへのアクセスを制限するには、まず既定のアクションを変更する必要があります。

> [!WARNING]
> ネットワーク ルールを変更すると、Azure Storage に接続するアプリケーションの機能に影響が及ぶことがあります。 既定のネットワーク ルールを**拒否**に設定すると、アクセスを**許可**する特定のネットワーク ルールも合わせて適用されていない限り、データへのアクセスがすべてブロックされます。 アクセスを拒否する既定のルールを変更する前に、ネットワーク ルールを使用して、許可されたネットワークへのアクセスを許可するようにしてください。

### <a name="managing-default-network-access-rules"></a>既定のネットワーク アクセス ルールを管理する

Azure portal、PowerShell、または CLIv2 を使用して、ストレージ アカウントに対する既定のネットワーク アクセス ルールを管理できます。

#### <a name="azure-portal"></a>Azure portal

1. セキュリティで保護するストレージ アカウントに移動します。

1. **[Firewalls and virtual networks] \(ファイアウォールおよび仮想ネットワーク)** という設定メニューをクリックします。

1. 既定でアクセスを拒否するには、 **[選択されたネットワーク]** からのアクセスを許可するように選択します。 すべてのネットワークからのトラフィックを許可するには、 **[すべてのネットワーク]** からのアクセスを許可するように選択します。

1. **[保存]** をクリックして変更を保存します。

#### <a name="powershell"></a>PowerShell

1. [Azure PowerShell](/powershell/azure/install-Az-ps) をインストールして[サインイン](/powershell/azure/authenticate-azureps)します。

1. ストレージ アカウントの既定のルールの状態を表示します。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. 既定でネットワーク アクセスを拒否する既定のルールを設定します。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. 既定でネットワーク アクセスを許可する既定のルールを設定します。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. [Azure CLI](/cli/azure/install-azure-cli) をインストールして[サインイン](/cli/azure/authenticate-azure-cli)します。

1. ストレージ アカウントの既定のルールの状態を表示します。

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. 既定でネットワーク アクセスを拒否する既定のルールを設定します。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. 既定でネットワーク アクセスを許可する既定のルールを設定します。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>仮想ネットワークからアクセスの許可

特定のサブネットからのアクセスのみを許可するように、ストレージ アカウントを構成できます。 許可するサブネットは、同じサブスクリプション内の VNet に属していても、または異なる Azure Active Directory テナントに属するサブスクリプションなど、異なるサブスクリプション内のものであってもかまいません。

VNet 内の Azure Storage に対する[サービス エンドポイント](/azure/virtual-network/virtual-network-service-endpoints-overview)を有効にします。 サービス エンドポイントでは、VNet からのトラフィックが、最適なパスを経由して、Azure Storage サービスにルーティングされます。 サブネットと仮想ネットワークの ID も、各要求と一緒に転送されます。 管理者は、その後、VNet 内の特定のサブネットからの要求の受信を許可するネットワーク ルールを、ストレージ アカウントに対して構成できます。 これらのネットワーク ルールによってアクセスを許可されたクライアントがデータにアクセスするには、ストレージ アカウントの認可要件を引き続き満たす必要があります。

各ストレージ アカウントでは最大 200 個の仮想ネットワーク規則がサポートされ、それを [IP ネットワーク ルール](#grant-access-from-an-internet-ip-range)と組み合わせることができます。

### <a name="available-virtual-network-regions"></a>使用可能な仮想ネットワークのリージョン

一般に、サービス エンドポイントは、同じ Azure リージョン内の仮想ネットワークとサービス インスタンスの間で機能します。 サービス エンドポイントを Azure Storage と共に使用すると、この範囲は[ペアのリージョン](/azure/best-practices-availability-paired-regions)を含むように拡張されます。 サービス エンドポイントにより、リージョンのフェールオーバー時の継続性と、読み取り専用の geo 冗長ストレージ (RA-GRS) インスタンスへのアクセスが実現されます。 仮想ネットワークからストレージ アカウントへのアクセスを許可するネットワー ルールでは、任意の RA-GRS インスタンスへのアクセスも許可します。

リージョンが停止したときのディザスター リカバリーを計画するときは、ペアのリージョン内にあらかじめ VNet を作成しておく必要があります。 これらの代替仮想ネットワークからのアクセスを許可するネットワーク ルールで、Azure Storage 用のサービス エンドポイントを有効にします。 その後、これらのルールを geo 冗長ストレージ アカウントに適用します。

> [!NOTE]
> サービス エンドポイントは、仮想ネットワークのリージョンと指定されたリージョン ペアの外部のトラフィックには適用されません。 仮想ネットワークからストレージ アカウントへのアクセスを許可するネットワーク ルールは、ストレージ アカウントのプライマリ リージョン内または指定したペアのリージョン内でのみ適用できます。

### <a name="required-permissions"></a>必要なアクセス許可

ストレージ アカウントに仮想ネットワーク規則を適用するには、追加されるサブネットに対する適切なアクセス許可を持っている必要があります。 必要なアクセス許可は "*サブネットにサービスを参加させる*" であり、"*ストレージ アカウント共同作成者*" 組み込みロールに含まれます。 カスタム ロール定義に追加することもできます。

ストレージ アカウントとアクセスを許可される仮想ネットワークは、異なる Azure AD テナントの一部であるサブスクリプションなど、異なるサブスクリプションに含まれていてもかまいません。

> [!NOTE]
> 異なる Azure Active Directory テナントの一部である仮想ネットワーク内のサブネットへのアクセスを許可するルールの構成は、現在、Powershell、CLI、および REST API でのみサポートされています。 このようなルールを Azure portal を使用して構成することはできませんが、ポータルで表示することはできます。

### <a name="managing-virtual-network-rules"></a>仮想ネットワーク規則の管理

ストレージ アカウントの仮想ネットワーク規則は、Azure portal、PowerShell、または CLIv2 で管理できます。

#### <a name="azure-portal"></a>Azure portal

1. セキュリティで保護するストレージ アカウントに移動します。

1. **[Firewalls and virtual networks] \(ファイアウォールおよび仮想ネットワーク)** という設定メニューをクリックします。

1. **[選択されたネットワーク]** からのアクセスを許可するように選択していることを確認します。

1. 新しいネットワーク ルールで仮想ネットワークへのアクセスを許可するには、 **[仮想ネットワーク]** で、 **[既存の仮想ネットワークを追加]** をクリックし、 **[仮想ネットワーク]** 、 **[サブネット]** オプションの順に選択して、 **[追加]** をクリックします。 新しい仮想ネットワークを作成してアクセスを許可するには、 **[新しい仮想ネットワークを追加]** をクリックします。 新しい仮想ネットワークの作成に必要な情報を指定して、 **[作成]** をクリックします。

    > [!NOTE]
    > Azure Storage 用のサービス エンドポイントが、選択した仮想ネットワークとサブネットに対してまだ構成されていない場合は、この操作の中で構成することができます。
    >
    > 現在、ルールの作成時に選択できるのは、同じ Azure Active Directory テナントに属する仮想ネットワークのみです。 別のテナントに属する仮想ネットワーク内のサブネットにアクセスを許可するには、Powershell、CLI、または REST API を使用してください。

1. 仮想ネットワークまたはサブネットのルールを削除するには、 **[...]** をクリックして仮想ネットワークまたはサブネットのコンテキスト メニューを開き、 **[削除]** をクリックします。

1. **[保存]** をクリックして変更を保存します。

#### <a name="powershell"></a>PowerShell

1. [Azure PowerShell](/powershell/azure/install-Az-ps) をインストールして[サインイン](/powershell/azure/authenticate-azureps)します。

1. 仮想ネットワーク規則を一覧表示します。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. 既存の仮想ネットワークとサブネットで、Azure Storage 用のサービス エンドポイントを有効にします。

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. 仮想ネットワークとサブネットに対するネットワーク ルールを追加します。

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > 別の Azure AD テナントに属する VNet 内のサブネットに対するネットワーク ルールを追加するには、"/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" という形式の完全修飾 **VirtualNetworkResourceId** パラメーターを使用します。

1. 仮想ネットワークとサブネットのネットワーク ルールを削除します。

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> **拒否**するように[既定のルールを設定](#change-the-default-network-access-rule)します。そうしないと、ネットワーク ルールは効力を発揮しません。

#### <a name="cliv2"></a>CLIv2

1. [Azure CLI](/cli/azure/install-azure-cli) をインストールして[サインイン](/cli/azure/authenticate-azure-cli)します。

1. 仮想ネットワーク規則を一覧表示します。

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. 既存の仮想ネットワークとサブネットで、Azure Storage 用のサービス エンドポイントを有効にします。

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. 仮想ネットワークとサブネットに対するネットワーク ルールを追加します。

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > 別の Azure AD テナントに属する VNet 内のサブネット用にルールを追加するには、"/subscriptions/\<subscription-ID\>/resourceGroups/\<resourceGroup-Name\>/providers/Microsoft.Network/virtualNetworks/\<vNet-name\>/subnets/\<subnet-name\>" という形式で完全修飾サブネット ID を使用します。
    >
    > **subscription** パラメーターを使用して、別の Azure AD テナントに属する VNet のサブネット ID を取得できます。

1. 仮想ネットワークとサブネットのネットワーク ルールを削除します。

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> **拒否**するように[既定のルールを設定](#change-the-default-network-access-rule)します。そうしないと、ネットワーク ルールは効力を発揮しません。

## <a name="grant-access-from-an-internet-ip-range"></a>インターネットの IP 範囲からのアクセスを許可する

パブリック インターネットの特定の IP アドレス範囲からのアクセスを許可するように、ストレージ アカウントを構成できます。 この構成では、特定のインターネット ベースのサービスとオンプレミスのネットワークにアクセスを許可し、一般的なインターネット トラフィックをブロックします。

許可するインターネット アドレスの範囲は、[CIDR 表記法](https://tools.ietf.org/html/rfc4632)を使って *16.17.18.0/24* の形式で、または *16.17.18.19* のように個々の IP アドレスとして、指定できます。

   > [!NOTE]
   > 「/31」や「/32」のプレフィックス サイズを使用した小さなアドレス範囲はサポートされていません。 これらの範囲は、個々の IP アドレス ルールを使用して構成する必要があります。

IP ネットワーク ルールは、**パブリック インターネット**の IP アドレスに対してのみ許可されます。 プライベート ネットワーク用に予約されている IP アドレス範囲 ([RFC 1918](https://tools.ietf.org/html/rfc1918#section-3) で定義) は、IP ルールでは許可されません。 プライベート ネットワークには、_10.*_ 、_172.16.*_  - _172.31.*_ 、_192.168.*_ で始まるアドレスが含まれます。

   > [!NOTE]
   > IP ネットワーク ルールは、ストレージ アカウントと同じ Azure リージョンから送信された要求には影響ありません。 同じリージョンの要求を許可するには、[仮想ネットワーク規則](#grant-access-from-a-virtual-network)を使用します。

  > [!NOTE]
  > ストレージ アカウントと同じリージョンでデプロイされたサービスでは、プライベート Azure IP アドレスが通信に使用されます。 そのため、特定の Azure サービスへのアクセスを、そのパブリック送信 IP アドレスの範囲に基づいて制限することはできません。

ストレージ ファイアウォール規則の構成では、IPv4 アドレスのみがサポートされています。

各ストレージ アカウントでは、最大 100 個の IP ネットワーク規則を設定できます。

### <a name="configuring-access-from-on-premises-networks"></a>オンプレミスのネットワークからのアクセスの構成

IP ネットワーク ルールでオンプレミスのネットワークからストレージ アカウントへのアクセスを許可するには、ネットワークで使用するインターネット接続 IP アドレスを特定する必要があります。 サポートが必要な場合は、ネットワーク管理者にお問い合わせください。

パブリック ピアリングまたは Microsoft ピアリングのためにオンプレミスから [ExpressRoute](/azure/expressroute/expressroute-introduction) を使用している場合、使用されている NAT の IP アドレスを識別する必要があります。 パブリック ピアリングの場合、既定で、Azure サービスのトラフィックが Microsoft Azure のネットワーク バックボーンに入ったときに適用される 2 つの NAT IP アドレスが各 ExpressRoute 回線に使用されます。 Microsoft ピアリングの場合、使用される NAT の IP アドレスは、ユーザーが指定するか、サービス プロバイダーが指定します。 サービス リソースへのアクセスを許可するには、リソースの IP ファイアウォール設定でこれらのパブリック IP アドレスを許可する必要があります。 パブリック ピアリングの ExpressRoute 回線の IP アドレスを確認するには、Azure Portal から [ExpressRoute のサポート チケットを開いて](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)ください。 詳細については、[ExpressRoute のパブリック ピアリングと Microsoft ピアリングの NAT](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering) に関するセクションを参照してください。

### <a name="managing-ip-network-rules"></a>IP ネットワーク ルールの管理

ストレージ アカウントの IP ネットワーク ルールは、Azure portal、PowerShell、または CLIv2 で管理できます。

#### <a name="azure-portal"></a>Azure portal

1. セキュリティで保護するストレージ アカウントに移動します。

1. **[Firewalls and virtual networks] \(ファイアウォールおよび仮想ネットワーク)** という設定メニューをクリックします。

1. **[選択されたネットワーク]** からのアクセスを許可するように選択していることを確認します。

1. インターネット IP 範囲へのアクセスを許可するには、 **[ファイアウォール]**  >  **[アドレス範囲]** で IP アドレスまたはアドレス範囲 (CIDR 形式) を入力します。

1. IP ネットワーク ルールを削除するには、アドレス範囲の横にあるごみ箱のアイコンをクリックします。

1. **[保存]** をクリックして変更を保存します。

#### <a name="powershell"></a>PowerShell

1. [Azure PowerShell](/powershell/azure/install-Az-ps) をインストールして[サインイン](/powershell/azure/authenticate-azureps)します。

1. IP ネットワーク ルールを一覧表示します。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. 個々 の IP アドレスに対するネットワーク ルールを追加します。

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. IP アドレス範囲に対するネットワーク ルールを追加します。

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. 個々 の IP アドレスに対するネットワーク ルールを削除します。

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. IP アドレス範囲に対するネットワーク ルールを削除します。

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> **拒否**するように[既定のルールを設定](#change-the-default-network-access-rule)します。そうしないと、ネットワーク ルールは効力を発揮しません。

#### <a name="cliv2"></a>CLIv2

1. [Azure CLI](/cli/azure/install-azure-cli) をインストールして[サインイン](/cli/azure/authenticate-azure-cli)します。

1. IP ネットワーク ルールを一覧表示します。

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. 個々 の IP アドレスに対するネットワーク ルールを追加します。

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. IP アドレス範囲に対するネットワーク ルールを追加します。

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. 個々 の IP アドレスに対するネットワーク ルールを削除します。

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. IP アドレス範囲に対するネットワーク ルールを削除します。

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> **拒否**するように[既定のルールを設定](#change-the-default-network-access-rule)します。そうしないと、ネットワーク ルールは効力を発揮しません。

## <a name="exceptions"></a>例外

ネットワーク規則は、ほとんどのシナリオで、アプリケーションとデータとを接続する安全な環境の構築に役立ちます。 ただし、一部のアプリケーションは、仮想ネットワークまたは IP アドレスの規則を使用して一意に分離できない Azure サービスに依存しています。 しかし、アプリケーションが機能を完全に発揮するには、そのようなサービスにストレージへのアクセスが許可される必要があります。 このような場合、***[信頼された Microsoft サービスを許可]*** 設定を使用して、このようなサービスで、自分のデータ、ログ、分析にアクセスできるように設定できます。

### <a name="trusted-microsoft-services"></a>信頼できる Microsoft サービス

一部の Microsoft サービスは、お客様のネットワーク規則の対象にできないネットワークで稼働しています。 他のアプリに対するネットワーク規則を維持しながら、このような信頼された Microsoft サービスの一部にストレージ アカウントへのアクセスを許可できます。 これらの信頼されるサービスでは、強力な認証を使用して、お客様のストレージ アカウントに安全に接続します。 Microsoft サービスには、2 種類の信頼されるアクセスが用意されています。

- 一部のサービスのリソースは、**お客様のサブスクリプションで登録されている場合に**、特定の操作 (ログの書き込みやバックアップなど) のために**同じサブスクリプション内の**ストレージ アカウントにアクセスできます。
- 一部のサービスのリソースでは、そのシステム割り当てマネージド ID に **Azure ロールを割り当てる**ことで、ストレージ アカウントへのアクセスを明示的に許可できます。


**[信頼された Microsoft サービスを許可]** 設定を有効にすると、ストレージ アカウントと同じサブスクリプションに登録された次のサービスのリソースに対し、記載された一部の操作へのアクセスが許可されます。

| サービス                  | リソース プロバイダー名     | 許可される操作                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | IAAS 仮想マシンの管理対象外のディスクのバックアップとリストアを実行します。 (マネージド ディスクの場合は必須ではありません)。 [詳細については、こちらを参照してください](/azure/backup/backup-introduction-to-azure-backup)。 |
| Azure Data Box           | Microsoft.DataBox          | Data Box を使用して Azure にデータをインポートできるようにします。 [詳細については、こちらを参照してください](/azure/databox/data-box-overview)。 |
| Azure DevTest Labs       | Microsoft.DevTestLab       | カスタム イメージの作成とアーティファクトのインストール [詳細については、こちらを参照してください](../../devtest-labs/devtest-lab-overview.md)。 |
| Azure Event Grid         | Microsoft.EventGrid        | Blob Storage のイベント発行を有効にし、ストレージ キューへの発行を Event Grid に許可します。 [Blob Storage イベント](/azure/event-grid/event-sources)と[キューへの発行](/azure/event-grid/event-handlers)について確認してください。 |
| Azure Event Hubs         | Microsoft.EventHub         | Event Hubs Capture を使用したアーカイブ データのキャプチャ [詳細については、こちらを参照してください](/azure/event-hubs/event-hubs-capture-overview)。 |
| Azure File Sync          | Microsoft.StorageSync      | オンプレミスのファイル サーバーを Azure ファイル共有のキャッシュに変換できます。 マルチサイト同期、迅速なディザスターリカバリー、クラウド側バックアップが可能となります。 [詳細情報](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | 新しい HDInsight クラスターのための既定のファイル システムの初期コンテンツをプロビジョニングします。 [詳細については、こちらを参照してください](/azure/hdinsight/hdinsight-hadoop-use-blob-storage)。 |
| Azure Import Export      | Microsoft.ImportExport     | Import/Export サービスを使用して、Azure のデータのインポートと Azure からのデータのエクスポートを行えるようにします。 [詳細については、こちらを参照してください](/azure/storage/common/storage-import-export-service)。  |
| Azure Monitor            | Microsoft.Insights         | リソース ログ、Azure Active Directory サインインと監査ログ、Microsoft Intune ログなど、セキュリティで保護されたストレージ アカウントへの監視データの書き込みを許可します。 [詳細については、こちらを参照してください](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security)。 |
| Azure のネットワーク         | Microsoft.Network          | Network Watcher および Traffic Analytics サービスを含め、ネットワーク トラフィック ログを格納し、分析します。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)。 |
| Azure Site Recovery      | Microsoft.SiteRecovery     | ファイアウォールが有効なキャッシュ、ソース、またはターゲット ストレージ アカウントを使用している場合、Azure IaaS 仮想マシンのディザスター リカバリーのレプリケーションを有効にします。  [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)。 |

また、 **[信頼された Microsoft サービスを許可]** の設定を有効にすると、以下に示すサービスの特定のインスタンスからストレージ アカウントにアクセスできます (そのリソース インスタンスの[システム割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) に明示的に [Azure ロールを割り当てている](storage-auth-aad.md#assign-azure-roles-for-access-rights)場合)。 この場合、インスタンスのアクセス範囲は、マネージド ID に割り当てられた Azure ロールに対応します。

| サービス                        | リソース プロバイダー名                 | 目的            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API Management           | Microsoft.ApiManagement/service        | ポリシーを使用して、API Management サービスが、ファイアウォールの背後にあるストレージ アカウントにアクセスできるようにします。 [詳細については、こちらを参照してください](/azure/api-management/api-management-authentication-policies#use-managed-identity-in-send-request-policy)。 |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | インデックス作成、処理、およびクエリのために、Cognitive Search サービスがストレージ アカウントにアクセスできるようになります。 |
| Azure Container Registry タスク | Microsoft.ContainerRegistry/registries | ACR タスクは、コンテナー イメージを作成するときにストレージアカウントにアクセスできます。 |
| Azure Data Factory             | Microsoft.DataFactory/factories        | ADF ランタイムを使用してストレージ アカウントへのアクセスを許可します。 |
| Azure Data Share               | Microsoft.DataShare/accounts           | Data Share を使用してストレージ アカウントにアクセスできるようになります。 |
| Azure IoT Hub                  | Microsoft.Devices/IotHubs              | IoT ハブからのデータを BLOB ストレージに書き込むことができます。 [詳細情報](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft.Logic/workflows              | ロジック アプリがストレージ アカウントにアクセスできるようにします。 [詳細については、こちらを参照してください](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity)。 |
| Azure Machine Learning サービス | Microsoft.MachineLearningServices      | 承認された Azure Machine Learning ワークスペースでは、BLOB ストレージに実験の出力、モデル、およびログを書き込み、データを読み取ります。 [詳細については、こちらを参照してください](/azure/machine-learning/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace)。 | 
| Azure Synapse Analytics (旧称 SQL Data Warehouse)       | Microsoft.Sql                          | COPY ステートメントまたは PolyBase を使用して、特定の SQL データベースからのデータのインポートとエクスポートを許可します。 [詳細については、こちらを参照してください](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)。 |
| Azure SQL データベース       | Microsoft.Sql                          | ストレージ アカウントからデータの[インポート](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql?view=sql-server-ver15#f-importing-data-from-a-file-in-azure-blob-storage)したり、ファイアウォールの背後にあるストレージ アカウントに監査データを[書き込んだり](https://docs.microsoft.com/azure/azure-sql/database/audit-write-storage-account-behind-vnet-firewall)することができます。 |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | ストリーミング ジョブからのデータを Blob Storage に書き込むことができます。 現在、この機能はプレビュー段階にあります。 [詳細については、こちらを参照してください](/azure/stream-analytics/blob-output-managed-identity)。 |
| Azure Synapse Analytics        | Microsoft.Synapse ワークスペース          | Synapse Analytics から Azure Storage のデータにアクセスできるようにします。 |


### <a name="storage-analytics-data-access"></a>ストレージ分析データ アクセス

場合によっては、ネットワーク境界の外側からリソース ログとメトリックを読み取るためにアクセスする必要があります。 信頼されたサービスによるストレージ アカウントへのアクセスを構成している場合、ログ ファイル、メトリック テーブル、またはその両方に対する読み取りアクセスを許可できます。 [ストレージ分析の使用に関する説明](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>例外の管理

ネットワーク ルールの例外は、Azure portal、PowerShell、または Azure CLI v2 を通じて管理できます。

#### <a name="azure-portal"></a>Azure portal

1. セキュリティで保護するストレージ アカウントに移動します。

1. **[Firewalls and virtual networks] \(ファイアウォールおよび仮想ネットワーク)** という設定メニューをクリックします。

1. **[選択されたネットワーク]** からのアクセスを許可するように選択していることを確認します。

1. **[例外]** で、許可する例外を選択します。

1. **[保存]** をクリックして変更を保存します。

#### <a name="powershell"></a>PowerShell

1. [Azure PowerShell](/powershell/azure/install-Az-ps) をインストールして[サインイン](/powershell/azure/authenticate-azureps)します。

1. ストレージ アカウントのネットワーク ルールの例外を表示します。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. ストレージ アカウントのネットワーク ルールの例外を設定します。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. ストレージ アカウントのネットワーク ルールの例外を削除します。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> **拒否**するように[既定のルールを設定](#change-the-default-network-access-rule)していることを確認します。そうしないと、例外の削除は効力を発揮しません。

#### <a name="cliv2"></a>CLIv2

1. [Azure CLI](/cli/azure/install-azure-cli) をインストールして[サインイン](/cli/azure/authenticate-azure-cli)します。

1. ストレージ アカウントのネットワーク ルールの例外を表示します。

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. ストレージ アカウントのネットワーク ルールの例外を設定します。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. ストレージ アカウントのネットワーク ルールの例外を削除します。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> **拒否**するように[既定のルールを設定](#change-the-default-network-access-rule)していることを確認します。そうしないと、例外の削除は効力を発揮しません。

## <a name="next-steps"></a>次のステップ

[サービス エンドポイント](/azure/virtual-network/virtual-network-service-endpoints-overview)で Azure ネットワークのサービス エンドポイントについて確認してください。

[Azure Storage セキュリティ ガイド](../blobs/security-recommendations.md)で Azure Storage のセキュリティを詳しく調べてください。
