---
title: 仮想ネットワーク
titleSuffix: Azure Cognitive Services
description: Cognitive Services リソースの多層型ネットワーク セキュリティを構成します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: eaffa535b51b786a53f1e6cc35233c55dd837233
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99989075"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Azure Cognitive Services 仮想ネットワークを構成する

Azure Cognitive Services は、多層型のセキュリティ モデルを採用しています。 このモデルでは、ネットワークの特定のサブセットに、Cognitive Services アカウントを固定することができます。 ネットワーク ルールを構成すると、指定したネットワークのセットを経由してデータを要求しているアプリケーションのみが、アカウントにアクセスできます。 要求フィルターにより、リソースへのアクセスを制限することができます。 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) 内の指定した IP アドレス、IP 範囲、またはサブネットのリストから発信された要求に制限します。

ネットワーク ルールが有効なときに Cognitive Services リソースにアクセスするアプリケーションでは、認可が必要です。 承認は、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 資格情報、または有効な API キーで行うことができます。

> [!IMPORTANT]
> Cognitive Services アカウントのファイアウォール規則をオンにすると、既定ではデータの受信要求がブロックされます。 要求を通すよう許可するには、次のいずれかの条件を満たしている必要があります。

> * 要求は、ターゲットの Cognitive Services アカウントの許可されたサブネットの一覧にある Azure Virtual Network (VNet) 内で動作するサービスから発信されたものである必要があります。 VNet を発信元とする要求のエンドポイントは、Cognitive Services アカウントの[カスタム サブドメイン](cognitive-services-custom-subdomains.md)として設定する必要があります。
> * または、許可された IP アドレスのリストからの要求である必要があります。
>
> ブロックされる要求には、他の Azure サービスからの要求、Azure portal からの要求、ログおよびメトリック サービスからの要求などが含まれます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>シナリオ

Cognitive Services リソースをセキュリティで保護するには、最初に、(インターネット トラフィックを含む) すべてのネットワークからのトラフィックに対して既定でアクセスを拒否するように、ルールを構成する必要があります。 次に、特定の VNet からのトラフィックにアクセスを許可するルールを構成する必要があります。 この構成では、アプリケーションに対してセキュリティで保護されたネットワーク境界を構築することができます。 また、選択したパブリック インターネット IP アドレス範囲からのトラフィックにアクセスを許可して、インターネットやオンプレミスの特定のクライアントからの接続を有効にすることもできます。

Azure Cognitive Services に対して、REST や WebSocket などのすべてのネットワーク プロトコルにネットワーク ルールが適用されます。 Azure のテスト コンソールのようなツールを使用してデータにアクセスするには、明示的なネットワーク ルールを構成する必要があります。 既存の Cognitive Services リソース、または新しい Cognitive Services リソースを作成するときに、ネットワークルールを適用できます。 適用したネットワーク ルールは、すべての要求に対して適用されます。

## <a name="supported-regions-and-service-offerings"></a>サポートされているリージョンとサービス内容

仮想ネットワーク (Vnet) は、[Cognitive Services が使用可能なリージョン](https://azure.microsoft.com/global-infrastructure/services/)でサポートされます。 Cognitive Services は、ネットワーク ルールの構成用のサービス タグをサポートしています。 次に示すサービスは、**CognitiveServicesManagement** サービス タグに含まれています。

> [!div class="checklist"]
> * Anomaly Detector
> * Computer Vision
> * Content Moderator
> * Custom Vision
> * Face
> * Form Recognizer
> * イマーシブ リーダー
> * Language Understanding (LUIS)
> * Personalizer
> * Speech Services
> * Text Analytics
> * QnA Maker
> * Translator Text


> [!NOTE]
> LUIS または Speech Service を使用している場合、**CognitiveServicesManagement** タグを指定しても、SDK または REST API を使用してサービスを使用できるようになるだけです。 仮想ネットワークから LUIS ポータルまたは Speech Studio、またはその両方にアクセスして使用するには、次のタグを使用する必要があります。  
> * **AzureActiveDirectory**
> * **AzureFrontDoor.Frontend**
> * **AzureResourceManager** 
> * **CognitiveServicesManagement**



## <a name="change-the-default-network-access-rule"></a>既定のネットワーク アクセス ルールの変更

既定では、Cognitive Services リソースは任意のネットワーク上のクライアントからの接続を受け入れます。 選択したネットワークへのアクセスを制限するには、まず既定のアクションを変更する必要があります。

> [!WARNING]
> ネットワーク ルールを変更すると、Azure Cognitive Services に接続するアプリケーションの機能に影響が及ぶことがあります。 既定のネットワーク ルールを **拒否** に設定すると、アクセスを **許可** する特定のネットワーク ルールも合わせて適用されていない限り、データへのアクセスがすべてブロックされます。 アクセスを拒否する既定のルールを変更する前に、ネットワーク ルールを使用して、許可されたネットワークへのアクセスを許可するようにしてください。 オンプレミス ネットワークの IP アドレスを一覧表示することを許可する場合は、オンプレミス ネットワークから発信することができるすべてのパブリック IP アドレスを追加してください。

### <a name="managing-default-network-access-rules"></a>既定のネットワーク アクセス ルールを管理する

Cognitive Services リソースの規定のネットワーク アクセス ルールは、Azure portal、PowerShell、または Azure CLI で管理できます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. セキュリティで保護する Cognitive Services リソースにアクセスします。

1. **[仮想ネットワーク]** という名前の **[リソース管理]** メニューを選択します。

   ![仮想ネットワークのオプション](media/vnet/virtual-network-blade.png)

1. 既定でアクセスを拒否するには、 **[選択されたネットワーク]** からのアクセスを許可するように選択します。 **[選択されたネットワーク]** のみを設定した状態で、 **[仮想ネットワーク]** または **[アドレス範囲]** を構成していない場合 - すべてのアクセスが効率的に拒否されます。 すべてのアクセスが拒否された場合、Cognitive Services リソースを使用しようとしている要求は許可されません。 これまで通り、Azure portal、Azure PowerShell または Azure CLI を使用して Cognitive Services リソースを構成することもできます。
1. すべてのネットワークからのトラフィックを許可するには、 **[すべてのネットワーク]** からのアクセスを許可するように選択します。

   ![仮想ネットワークの拒否](media/vnet/virtual-network-deny.png)

1. **[保存]** を選択して変更を保存します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. [Azure PowerShell](/powershell/azure/install-az-ps) をインストールして [サインイン](/powershell/azure/authenticate-azureps)するか、 **[使ってみる]** を選択します。

1. Cognitive Services リソースの既定のルールの状態を表示します。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. 既定でネットワーク アクセスを拒否する既定のルールを設定します。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. 既定でネットワーク アクセスを許可する既定のルールを設定します。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) をインストールして [サインイン](/cli/azure/authenticate-azure-cli)するか、 **[使ってみる]** を選択します。

1. Cognitive Services リソースの既定のルールの状態を表示します。

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. 既定でネットワーク アクセスを拒否する既定のルールを設定します。

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. 既定でネットワーク アクセスを許可する既定のルールを設定します。

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>仮想ネットワークからアクセスの許可

パブリック インターネットの特定の サブネットからのアクセスのみを許可するように、Cognitive Services リソースを構成できます。 許可するサブネットは、同じサブスクリプション内の VNet に属していても、または異なる Azure Active Directory テナントに属するサブスクリプションなど、異なるサブスクリプション内のものであってもかまいません。

VNet 内の Azure Cognitive Services に対する[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を有効にします。 サービス エンドポイントでは、VNet からのトラフィックが、最適なパスを経由して、Azure Cognitive Services サービスに送信されます。 サブネットと仮想ネットワークの ID も、各要求と一緒に転送されます。 管理者は、その後、VNet 内の特定のサブネットからの要求の受信を許可するネットワーク ルールを、Cognitive Services リソースに対して構成できます。 これらのネットワーク ルールによってアクセスを許可されたクライアントがデータにアクセスするには、Cognitive Services リソースの認可要件を引き続き満たす必要があります。

各 Cognitive Services リソースでは最大 100 個の 仮想ネットワーク ルールがサポートされ、それを [IP ネットワーク規則](#grant-access-from-an-internet-ip-range)と組み合わせることができます。

### <a name="required-permissions"></a>必要なアクセス許可

Cognitive Services リソースに仮想ネットワーク規則を適用するには、追加されるサブネットに対する適切なアクセス許可を持っている必要があります。 必要なアクセス許可は、既定の *共同作成者* ロール、または *Cognitive Services 共同作成者* ロールです。 必要なアクセス許可をカスタム ロール定義に追加することもできます。

Cognitive Services リソースとアクセスを許可される仮想ネットワークは、異なる Azure AD テナントの一部であるサブスクリプションなど、異なるサブスクリプションに含まれていてもかまいません。

> [!NOTE]
> 異なる Azure Active Directory テナントの一部である仮想ネットワーク内のサブネットへのアクセスを許可するルールの構成は、現在、Powershell、CLI、および REST API でのみサポートされています。 このようなルールを Azure portal を使用して構成することはできませんが、ポータルで表示することはできます。

### <a name="managing-virtual-network-rules"></a>仮想ネットワーク規則の管理

Cognitive Services リソースの 仮想ネットワーク規則は、Azure portal、PowerShell、または Azure CLI で管理できます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. セキュリティで保護する Cognitive Services リソースにアクセスします。

1. **[仮想ネットワーク]** という名前の **[リソース管理]** メニューを選択します。

1. **[選択されたネットワーク]** からのアクセスを許可するように選択していることを確認します。

1. 既存のネットワーク ルールが設定されている仮想ネットワークへのアクセスを許可するには、 **[仮想ネットワーク]** で **[Add existing Azure virtual network]\(既存の Azure 仮想ネットワークを追加\)** を選択します。

   ![既存の VNet を追加する](media/vnet/virtual-network-add-existing.png)

1. **[仮想ネットワーク]** と **[サブネット]** オプションを選択して、 **[有効化]** を選択します。

   ![既存の VNet の詳細を追加する](media/vnet/virtual-network-add-existing-details.png)

1. 新しい仮想ネットワークを作成してアクセスを許可するには、 **[Add new virtual network]\(新しい仮想ネットワークを追加\)** をクリックします。

   ![新しい vNet を追加する](media/vnet/virtual-network-add-new.png)

1. 新しい仮想ネットワークの作成に必要な情報を指定して、 **[作成]** を選択します。

   ![vNet を作成する](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Azure Cognitive Services 用のサービス エンドポイントが、選択した仮想ネットワークとサブネットに対してまだ構成されていない場合は、この操作の中で構成することができます。
    >
    > 現在、ルールの作成時に選択できるのは、同じ Azure Active Directory テナントに属する仮想ネットワークのみです。 別のテナントに属する仮想ネットワーク内のサブネットにアクセスを許可するには、Powershell、CLI、または REST API を使用してください。

1. 仮想ネットワークまたはサブネットのルールを削除するには、 **[...]** を選択して仮想ネットワークまたはサブネットのコンテキスト メニューを開き、 **[削除]** を選択します。

   ![vNet の削除](media/vnet/virtual-network-remove.png)

1. **[保存]** を選択して変更を保存します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. [Azure PowerShell](/powershell/azure/install-az-ps) をインストールして [サインイン](/powershell/azure/authenticate-azureps)するか、 **[使ってみる]** を選択します。

1. 仮想ネットワーク規則を一覧表示します。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. 既存の仮想ネットワークとサブネットで、Azure Cognitive Services 用のサービス エンドポイントを有効にします。

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. 仮想ネットワークとサブネットに対するネットワーク ルールを追加します。

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > 別の Azure AD テナントに属する VNet 内のサブネットに対するネットワーク ルールを追加するには、"/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" という形式の完全修飾 **VirtualNetworkResourceId** パラメーターを使用します。

1. 仮想ネットワークとサブネットのネットワーク ルールを削除します。

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) をインストールして [サインイン](/cli/azure/authenticate-azure-cli)するか、 **[使ってみる]** を選択します。

1. 仮想ネットワーク規則を一覧表示します。

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. 既存の仮想ネットワークとサブネットで、Azure Cognitive Services 用のサービス エンドポイントを有効にします。

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. 仮想ネットワークとサブネットに対するネットワーク ルールを追加します。

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > 別の Azure AD テナントに属する VNet 内のサブネットに対するルールを追加するには、"/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" という形式の完全修飾サブネット ID を使用します。
    > 
    > **subscription** パラメーターを使用して、別の Azure AD テナントに属する VNet のサブネット ID を取得できます。

1. 仮想ネットワークとサブネットのネットワーク ルールを削除します。

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

***

> [!IMPORTANT]
> **拒否** するように [既定のルールを設定](#change-the-default-network-access-rule)します。そうしないと、ネットワーク ルールは効力を発揮しません。

## <a name="grant-access-from-an-internet-ip-range"></a>インターネットの IP 範囲からのアクセスを許可する

パブリック インターネットの特定の IP アドレス範囲からのアクセスを許可するように、Cognitive Services リソースを構成できます。 この構成では、特定のサービスとオンプレミスのネットワークにアクセスを許可し、一般的なインターネット トラフィックを効率的にブロックします。

許可するインターネット アドレスの範囲は、[CIDR 表記法](https://tools.ietf.org/html/rfc4632)を使って `16.17.18.0/24` の形式で、または `16.17.18.19` のように個々の IP アドレスとして、指定できます。

   > [!Tip]
   > 「/31」や「/32」のプレフィックス サイズを使用した小さなアドレス範囲はサポートされていません。 これらの範囲は、個々の IP アドレス ルールを使用して構成する必要があります。

IP ネットワーク ルールは、**パブリック インターネット** の IP アドレスに対してのみ許可されます。 プライベート ネットワーク用に予約されている IP アドレス範囲 ([RFC 1918](https://tools.ietf.org/html/rfc1918#section-3) で定義) は、IP ルールでは許可されません。 プライベート ネットワークには、`10.*`、`172.16.*` - `172.31.*`、および `192.168.*` で始まるアドレスが含まれます。

現時点でサポートされているのは、IPv4 アドレスのみです。 各 Cognitive Services リソースでは最大 100 個の IP ネットワーク ルールがサポートされ、それを[仮想ネットワーク規則](#grant-access-from-a-virtual-network)と組み合わせることができます。

### <a name="configuring-access-from-on-premises-networks"></a>オンプレミスのネットワークからのアクセスの構成

IP ネットワーク ルールでオンプレミスのネットワークから Cognitive Services リソースへのアクセスを許可するには、ネットワークで使用するインターネット接続 IP アドレスを特定する必要があります。 サポートが必要な場合は、ネットワーク管理者にお問い合わせください。

パブリック ピアリングまたは Microsoft ピアリングのためにオンプレミスから [ExpressRoute](../expressroute/expressroute-introduction.md) を使用している場合、NAT の IP アドレスを識別する必要があります。 パブリック ピアリングの場合、各 ExpressRoute 回線は既定で 2 つの NAT IP アドレスを使用します。 それぞれの NAT IP アドレスは、トラフィックが Microsoft Azure ネットワーク バックボーンに入ったときに Azure サービス トラフィックに適用されます。 Microsoft ピアリングの場合、使用される NAT の IP アドレスは、ユーザーが指定するか、サービス プロバイダーが指定します。 サービス リソースへのアクセスを許可するには、リソースの IP ファイアウォール設定でこれらのパブリック IP アドレスを許可する必要があります。 パブリック ピアリングの ExpressRoute 回線の IP アドレスを確認するには、Azure Portal から [ExpressRoute のサポート チケットを開いて](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)ください。 詳細については、[ExpressRoute のパブリック ピアリングと Microsoft ピアリングの NAT](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering) に関するセクションを参照してください。

### <a name="managing-ip-network-rules"></a>IP ネットワーク ルールの管理

Cognitive Services リソースの IP ネットワーク ルールは、Azure portal、PowerShell、または Azure CLI で管理できます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. セキュリティで保護する Cognitive Services リソースにアクセスします。

1. **[仮想ネットワーク]** という名前の **[リソース管理]** メニューを選択します。

1. **[選択されたネットワーク]** からのアクセスを許可するように選択していることを確認します。

1. インターネット IP 範囲へのアクセスを許可するには、 **[ファイアウォール]**  >  **[アドレス範囲]** で IP アドレスまたはアドレス範囲 ([CIDR 形式](https://tools.ietf.org/html/rfc4632)) を入力します。 有効なパブリック IP (予約されない) アドレスのみが受け入れられます。

   ![IP 範囲を追加する](media/vnet/virtual-network-add-ip-range.png)

1. IP ネットワーク ルールを削除するには、アドレス範囲の横にあるごみ箱 <span class="docon docon-delete x-hidden-focus"></span> のアイコンを選択します。

   ![IP 範囲を削除する](media/vnet/virtual-network-delete-ip-range.png)

1. **[保存]** を選択して変更を保存します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. [Azure PowerShell](/powershell/azure/install-az-ps) をインストールして [サインイン](/powershell/azure/authenticate-azureps)するか、 **[使ってみる]** を選択します。

1. IP ネットワーク ルールを一覧表示します。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. 個々 の IP アドレスに対するネットワーク ルールを追加します。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. IP アドレス範囲に対するネットワーク ルールを追加します。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. 個々 の IP アドレスに対するネットワーク ルールを削除します。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. IP アドレス範囲に対するネットワーク ルールを削除します。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) をインストールして [サインイン](/cli/azure/authenticate-azure-cli)するか、 **[使ってみる]** を選択します。

1. IP ネットワーク ルールを一覧表示します。

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. 個々 の IP アドレスに対するネットワーク ルールを追加します。

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. IP アドレス範囲に対するネットワーク ルールを追加します。

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. 個々 の IP アドレスに対するネットワーク ルールを削除します。

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. IP アドレス範囲に対するネットワーク ルールを削除します。

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> **拒否** するように [既定のルールを設定](#change-the-default-network-access-rule)します。そうしないと、ネットワーク ルールは効力を発揮しません。

## <a name="use-private-endpoints"></a>プライベート エンドポイントを使用する

お使いの Cognitive Services リソースの[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用すると、仮想ネットワーク (VNet) 上のクライアントは[プライベート リンク](../private-link/private-link-overview.md)を介してデータに安全にアクセスできるようになります。 プライベート エンドポイントは、Cognitive Services リソースの VNet アドレス空間からの IP アドレスを使用します。 VNet 上のクライアントとリソースの間のネットワーク トラフィックは、VNet と Microsoft バックボーン ネットワーク上のプライベート リンクを経由することで、パブリック インターネットからの露出を排除します。

Cognitive Services リソースのプライベート エンドポイントを使用すると、次のことができます。

* Cognitive Services サービスのパブリック エンドポイント上のすべての接続をブロックするようにファイアウォールを構成することにより、Cognitive Services リソースをセキュリティで保護します。
* VNet からのデータの流出をブロックできるようにすることで、VNet のセキュリティを強化します。
* [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) または [ExpressRoutes](../expressroute/expressroute-locations.md) とプライベートピアリングを使用して VNet に接続するオンプレミス ネットワークから Cognitive Services リソースに安全に接続します。

### <a name="conceptual-overview"></a>概念の概要

プライベート エンドポイントは、[VNet](../virtual-network/virtual-networks-overview.md) 内の Azure リソース用の特別なネットワーク インターフェイスです。 Cognitive Services リソースのプライベート エンドポイントを作成すると、対象の VNet 上のクライアントと対象のリソース間のセキュリティで保護された接続が提供されます。 プライベート エンドポイントには、VNet の IP アドレス範囲から IP アドレスが割り当てられます。 プライベート エンドポイントと Cognitive Services サービス間の接続には、セキュリティで保護されたプライベート リンクが使用されます。

VNet 内のアプリケーションは、プライベート エンドポイント経由でサービスにシームレスに接続できます。その際、使用される接続文字列と承認メカニズムは、それを経由しない場合と同じものになります。 例外は Speech Service で、これには別のエンドポイントが必要です。 「[プライベート エンドポイントと Speech Service](#private-endpoints-with-the-speech-services)」セクションを参照してください。 プライベート エンドポイントは、Cognitive Services リソースでサポートされているすべてのプロトコル (REST を含む) で使用できます。

プライベート エンドポイントは、[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を使用するサブネットに作成できます。 サブネット内のクライアントは、プライベート エンドポイントを使用して 1 つの Cognitive Services リソースに接続する一方で、サービス エンドポイントを使用して他のリソースにアクセスできます。

お使いの VNet で Cognitive Services リソース用プライベート エンドポイントを作成すると、承認を得るために同意要求が Cognitive Services リソースの所有者に送信されます。 プライベート エンドポイントの作成を要求しているユーザーがリソースの所有者でもある場合、この同意要求は自動的に承認されます。

Cognitive Services リソースの所有者は、[Azure portal](https://portal.azure.com) で Cognitive Services リソースの *[プライベート エンドポイント]* タブを使用して、同意要求とプライベート エンドポイントを管理できます。

### <a name="private-endpoints"></a>プライベート エンドポイント

プライベート エンドポイントを作成するときは、接続先の Cognitive Services リソースを指定する必要があります。 プライベート エンドポイントを作成する方法の詳細については、次の記事を参照してください。

* [Azure portal でプライベート リンク センターを使用してプライベート エンドポイントを作成する](../private-link/create-private-endpoint-portal.md)
* [Azure CLI を使用してプライベート エンドポイントを作成する](../private-link/create-private-endpoint-cli.md)
* [Azure PowerShell を使用してプライベート エンドポイントを作成する](../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>プライベート エンドポイントへの接続

プライベート エンドポイントを使用する VNet 上のクライアントは、パブリック エンドポイントに接続するクライアントと同じ接続文字列を Cognitive Services リソースに対して使用する必要があります。 例外は Speech Service で、これには別のエンドポイントが必要です。 「[プライベート エンドポイントと Speech Service](#private-endpoints-with-the-speech-services)」セクションを参照してください。 プライベート リンク経由の VNet から Cognitive Services リソースへの接続を自動的にルーティングするために、DNS 解決に依存しています。 

既定では、VNet に接続されている[プライベート DNS ゾーン](../dns/private-dns-overview.md)が作成され、プライベート エンドポイントに必要な更新も行われます。 ただし、独自の DNS サーバーを使用している場合は、DNS 構成に追加の変更が必要になることがあります。 以下の [DNS の変更](#dns-changes-for-private-endpoints)に関するセクションで、プライベート エンドポイントに必要な更新について説明しています。

### <a name="private-endpoints-with-the-speech-services"></a>プライベート エンドポイントと Speech Service

「[Azure Private Link によって提供されるプライベート エンドポイントでの Speech Service の使用](Speech-Service/speech-services-private-link.md)」を参照してください。

### <a name="dns-changes-for-private-endpoints"></a>プライベート エンドポイントの DNS の変更

プライベート エンドポイントを作成すると、Cognitive Services リソースの DNS CNAME リソース レコードは、プレフィックス "*privatelink*" を持つサブドメイン内のエイリアスに更新されます。 既定では、"*privatelink*" サブドメインに対応する [プライベート DNS ゾーン](../dns/private-dns-overview.md)も作成されます。これには、プライベート エンドポイントの DNS A リソース レコードが含まれます。

プライベート エンドポイントを備える VNet の外部からエンドポイント URL を解決すると、Cognitive Services リソースのパブリック エンドポイントに解決されます。 プライベート エンドポイントをホストしている VNet から解決されると、エンドポイント URL はプライベート エンドポイントの IP アドレスに解決されます。

この方法を使用すると、プライベート エンドポイントをホストしている VNet 上のクライアントと、VNet の外部のクライアントから同じ接続文字列を使用して Cognitive Services リソースにアクセスできます。

ネットワーク上でカスタム DNS サーバーを使用している場合、クライアントで、Cognitive Services リソースのエンドポイントの完全修飾ドメイン名 (FQDN) をプライベート エンドポイントの IP アドレスに解決できる必要があります。 プライベート リンク サブドメインを VNet のプライベート DNS ゾーンに委任するように、DNS サーバーを構成してください。

> [!TIP]
> カスタムまたはオンプレミスの DNS サーバーを使用している場合は、"privatelink" サブドメインの Cognitive Services リソース名をプライベート エンドポイントの IP アドレスに解決するように DNS サーバーを構成する必要があります。 これを行うには、VNet のプライベート DNS ゾーンに "privatelink" サブドメインを委任するか、DNS サーバーで DNS ゾーンを構成し、DNS A レコードを追加します。

プライベート エンドポイントをサポートするように独自の DNS サーバーを構成する方法の詳細については、次の記事を参照してください。

* [Azure 仮想ネットワーク内のリソースの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
* [プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-overview.md#dns-configuration)

### <a name="pricing"></a>価格

料金の詳細については、「[Azure Private Link の料金](https://azure.microsoft.com/pricing/details/private-link)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* さまざまな [AzureCognitive Services](./what-are-cognitive-services.md) をご覧ください
* [Azure Virtual Network サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)の詳細を確認する
