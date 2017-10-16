---
title: "Azure Storage ファイアウォールおよび仮想ネットワークの構成 (プレビュー) | Microsoft Docs"
description: "ストレージ アカウントの多層型ネットワーク セキュリティを構成します。"
services: storage
documentationcenter: 
author: cbrooksmsft
manager: cbrooks
editor: cbrooks
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 09/25/2017
ms.author: cbrooks
ms.openlocfilehash: de52e9cb32e28d2f40a56743ed759b5d5d0a63f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks-preview"></a>Azure Storage ファイアウォールおよび仮想ネットワークの構成 (プレビュー)
Azure Storage は多層型セキュリティ モデルを提供しているため、許可されたネットワークの特定のセットに対するストレージ アカウントをセキュリティで保護することができます。  ネットワーク ルールが構成されている場合、ストレージ アカウントにアクセスできるのは、許可されているネットワークからのアプリケーションのみです。  許可されているネットワークからの呼び出し時に、アプリケーションはストレージ アカウントにアクセスするための適切な承認 (有効なアクセス キーまたは SAS トークン) を要求します。

## <a name="preview-availability-and-support"></a>プレビューの可用性とサポート
Azure Storage ファイアウォールおよび仮想ネットワークはプレビュー段階です。  この機能は、現在のところ、次のリージョンの新規または既存のストレージ アカウントに対して提供されています。
- 米国東部
- 米国西部
- 米国西部 2
- 米国中西部
- オーストラリア東部
- オーストラリア南東部

> [!NOTE]
> プレビュー期間中は、運用環境のワークロードはサポートされていません。
>

## <a name="scenarios"></a>シナリオ
既定では、ストレージ アカウントを、(インターネット トラフィックを含む) すべてのネットワークからトラフィックへのアクセスを拒否するように構成できます。  特定の Azure 仮想ネットワークからのトラフィックにアクセスを許可できるため、アプリケーションに対してセキュリティで保護されたネットワーク境界を構築することができます。  また、パブリック インターネットの IP アドレスの範囲にもアクセスを許可できるため、インターネットやオンプレミスの特定のクライアントからの接続を実現できます。

Azure Storage に対して、REST や SMB などのすべてのネットワーク プロトコルにネットワーク ルールが適用されます。  Azure Portal、Storage Explorer、AZCopy などのツールからデータにアクセスするには、ネットワーク ルールが施行されているときにアクセスを許可する明示的なネットワーク ルールが必要です。

ネットワーク ルールは、既存のストレージ アカウントに適用できます。または、新しいストレージ アカウントの作成中に適用できます。

ネットワーク ルールが適用されると、そのルールはすべての要求に対して適用されます。  特定の IP アドレス サービスへのアクセスを許可する SAS トークンは、トークン所有者のアクセスを**制限**する働きをしますが、構成されているネットワーク ルール以上の新しいアクセスを許可しないでください。 

仮想マシン ディスクのトラフィック (マウントとマウント解除、およびディスク IO を含む) はネットワーク ルールによって影響を**受けません**。  管理対象外のディスクのバックアップは、プレビュー中は保護されているストレージ アカウントに対してはサポートされていません。  ページ BLOB への REST アクセス (仮想マシン ディスクの使用) はネットワーク ルールによって保護されています。

従来のストレージ アカウントは、ファイアウォールおよび仮想ネットワークをサポート**していません**。

## <a name="change-the-default-network-access-rule"></a>既定のネットワーク アクセス ルールの変更
既定では、ストレージ アカウントは、任意のネットワーク上のクライアントからの接続を受け入れます。  選択したネットワークへのアクセスを制限するには、まず既定のアクションを変更する必要があります。

> [!WARNING]
> ネットワーク ルールを変更すると、Azure Storage に接続するアプリケーションの機能に影響が及ぶことがあります。  既定のネットワーク ルールを**拒否**に設定すると、アクセスを*許可*する特定のネットワーク ルールも合わせて適用されていない限り、データへのアクセスがすべてブロックされます。  アクセスを拒否する既定のルールを変更する前に、ネットワーク ルールを使用して、許可されたネットワークへのアクセスを許可するようにしてください。
>

#### <a name="azure-portal"></a>Azure ポータル
1. セキュリティで保護するストレージ アカウントを表示します。  
> [!NOTE]
> ストレージ アカウントが、パブリック プレビューでサポートされるリージョンのいずれかにあることを確認します。
>

2. **[Firewalls and virtual networks] (ファイアウォールおよび仮想ネットワーク)** という設定メニューをクリックします。
3. 既定でアクセスを拒否するには、"選択したネットワーク" からのアクセスを許可するように選択します。  すべてのネットワークからのトラフィックを許可するには、"すべてのネットワーク" からのアクセスを許可するように選択します。
4. *[保存]* をクリックして変更を保存します。

#### <a name="powershell"></a>PowerShell
1. 最新の [Azure PowerShell](/powershell/azure/install-azurerm-ps) をインストールして[ログイン](/powershell/azure/authenticate-azureps)します。

2. ストレージ アカウントの既定のルールの状態を表示します。
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. 既定でネットワーク アクセスを拒否する既定のルールを設定します。  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. 既定でネットワーク アクセスを許可するする既定のルールを設定します。
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLIv2
1. [Azure CLI 2.0](/cli/azure/install-azure-cli) をインストールして[ログイン](/cli/azure/authenticate-azure-cli)します。
2. ストレージ アカウントの既定のルールの状態を表示します。
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkAcls.defaultAction
```

3. 既定でネットワーク アクセスを拒否する既定のルールを設定します。  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. 既定でネットワーク アクセスを許可するする既定のルールを設定します。
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>仮想ネットワークからアクセスの許可
特定の Azure 仮想ネットワークからのみアクセスを許可するようにストレージ アカウントを構成できます。 

仮想ネットワーク内で Azure Storage の[サービス エンドポイント](/azure/virtual-network/virtual-network-service-endpoints-overview)を有効にすると、トラフィックでは Azure Storage サービスへの最適なルートが確保されます。 仮想ネットワークとサブネットの ID も、各要求と一緒に転送されます。  管理者は、仮想ネットワーク内の特定のサブネットから受信する要求を許可するストレージ アカウントのネットワーク ルールを後で構成できます。  これらのネットワーク ルールによってアクセスを許可されたクライアントがデータにアクセスするには、ストレージ アカウントの承認要件を満たす必要があります。

各ストレージ アカウントは、[IP ネットワーク ルール](#grant-access-from-an-internet-ip-range)と合わせて最大 100 個の仮想ネットワーク ルールをサポートできます。

### <a name="available-virtual-network-regions"></a>使用可能な仮想ネットワークのリージョン
一般に、サービス エンドポイントは、同じ Azure リージョンの仮想ネットワークとサービス インスタンス間で機能します。  サービス エンドポイントが Azure Storage で使用されている場合、このスコープは[ペアのリージョン](/azure/best-practices-availability-paired-regions)を含めるように拡張されます。  これにより、リージョンのフェールオーバー時の継続性とともに、読み取り専用の geo 冗長ストレージ (RA-GRS) インスタンスへのシームレスなアクセスを実現します。  仮想ネットワークからストレージ アカウントへのアクセスを許可するネットワー ルールでは、任意の RA-GRS インスタンスへのアクセスも許可します。

リージョンの障害時にディザスター リカバリーを計画する場合、ペアのリージョンに仮想ネットワークを事前にプロビジョニングしておく必要があります。 Azure Storage のサービス エンドポイントを有効にして、geo 冗長ストレージ アカウントに対して、これらの代替の仮想ネットワークからのアクセスを許可するネットワーク ルールを適用する必要があります。

> [!NOTE]
> サービス エンドポイントは、仮想ネットワークのリージョンと指定されたリージョンのペア以外のトラフィックには適用されません。  仮想ネットワークからストレージ アカウントへのアクセスを許可するネットワーク ルールは、ストレージ アカウントのプライマリ リージョンまたは指定されたペアのリージョン内の仮想ネットワークにのみ適用できます。
>

### <a name="required-permissions"></a>必要なアクセス許可
ストレージ アカウントに仮想ネットワーク ルールを適用するには、追加するサブネットに対して*サブネットにサービスを参加させる*ことのできるユーザー権限が必要です。  この権限は、*ストレージ アカウントの共同作業者*組み込みロールに含まれており、カスタム ロールの定義に追加できます。

ストレージ アカウントとアクセスが許可されている仮想ネットワークで、サブスクリプションが異なる**可能性があります**が、これらのサブスクリプションは、同じ Azure Active Directory テナントの一部である必要があります。

### <a name="managing-virtual-network-rules"></a>仮想ネットワーク ルールの管理
ストレージ アカウント用の仮想ネットワーク ルールは、Azure Portal、PowerShell、または CLIv2 を通じて管理できます。

#### <a name="azure-portal"></a>Azure ポータル
1. セキュリティで保護するストレージ アカウントを表示します。  
2. **[Firewalls and virtual networks] (ファイアウォールおよび仮想ネットワーク)** という設定メニューをクリックします。
3. "選択したネットワーク" からのアクセスを許可するように選択していることを確認します。
4. 新しいネットワーク ルールで仮想ネットワークへのアクセスを許可するには、[仮想ネットワーク] で、[既存を追加] をクリックして既存の仮想ネットワークとサブネットを選択し、*[追加]*をクリックします。  新しい仮想ネットワークを作成してアクセスを許可するには、*[新規追加]* をクリックして、新しい仮想ネットワークを作成するために必要な情報を入力し、*[作成]* をクリックします。

> [!NOTE]
> Azure Storage のサービス エンドポイントが、選択した仮想ネットワークとサブネットに対してまだ構成されていない場合は、この操作の中で構成することができます。
>

5. 仮想ネットワークまたはサブネットのルールを削除するには、[...] をクリックして仮想ネットワークまたはサブネットのコンテキスト メニューを開き、[削除] をクリックします。
6. *[保存]* をクリックして変更を保存します。

#### <a name="powershell"></a>PowerShell
1. 最新の [Azure PowerShell](/powershell/azure/install-azurerm-ps) をインストールして[ログイン](/powershell/azure/authenticate-azureps)します。
2. 仮想ネットワーク ルールを一覧表示します。
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. 既存の仮想ネットワークとサブネット上の Azure Storage のサービス エンドポイントを有効にします。
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. 仮想ネットワークとサブネットのネットワーク ルールを追加します。  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. 仮想ネットワークとサブネットのネットワーク ルールを削除します。  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> 拒否するように[既定のルールを設定](#change-the-default-network-access-rule)していることを確認します。そうしないと、ネットワーク ルールは効力を発揮しません。
>

#### <a name="cliv2"></a>CLIv2
1. [Azure CLI 2.0](/cli/azure/install-azure-cli) をインストールして[ログイン](/cli/azure/authenticate-azure-cli)します。
2. 仮想ネットワーク ルールを一覧表示します。
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. 既存の仮想ネットワークとサブネット上の Azure Storage のサービス エンドポイントを有効にします。
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. 仮想ネットワークとサブネットのネットワーク ルールを追加します。  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "TestVNET" --name "default" --query id --output tsv)
az storage account network-rule add --resource-group myresourcegroup --account-name mystorageaccount --subnet $subnetid
```

4. 仮想ネットワークとサブネットのネットワーク ルールを削除します。 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "TestVNET" --name "default" --query id --output tsv)
az storage account network-rule remove --resource-group myresourcegroup --account-name mystorageaccount --subnet $subnetid
```

> [!IMPORTANT]
> 拒否するように[既定のルールを設定](#change-the-default-network-access-rule)していることを確認します。そうしないと、ネットワーク ルールは効力を発揮しません。
>

## <a name="grant-access-from-an-internet-ip-range"></a>インターネットの IP 範囲からのアクセスを許可する
ストレージ アカウントは、特定のパブリック インターネットの IP アドレス範囲からアクセスを許可するように構成できます。  この構成により、特定のインターネット ベースのサービスとオンプレミスのネットワークにアクセスを許可しながら、一般的なインターネット トラフィックはブロックできます。

許可するインターネットのアドレス範囲は、[CIDR 表記法](https://tools.ietf.org/html/rfc4632)で*16.17.18.0/24*などの形式、あるいは*16.17.18.19*といった個々の IP アドレスを使用して指定できます。

> [!NOTE]
> 「/31」や「/32」のプレフィックス サイズを使用した小さなアドレス範囲はサポートされていません。  これらの範囲は、個々の IP アドレス ルールを使用して構成する必要があります。
>

IP ネットワーク ルールは、**パブリック インターネット**の IP アドレスに対してのみ許可されます。  プライベート ネットワーク用に予約されている IP アドレス範囲 (RFC 1918 で定義) は、IP ルールでは許可されません。  プライベート ネットワークには、*10.\**、*172.16.\**、および *192.168.\** で始まるアドレスが含まれます。

現時点でサポートされているのは、IPv4 アドレスのみです。

各ストレージ アカウントは、[仮想ネットワーク ルール](#grant-access-from-a-virtual-network)と合わせて最大 100 個の IP ネットワーク ルールをサポートできます。

### <a name="configuring-access-from-on-premises-networks"></a>オンプレミスのネットワークからのアクセスの構成
IP ネットワーク ルールでオンプレミスのネットワークからストレージ アカウントへのアクセスを許可するには、ネットワークで使用するインターネット接続 IP アドレスを特定する必要があります。  サポートが必要な場合は、ネットワーク管理者にお問い合わせください。

ネットワークが [ExpressRoute](/azure/expressroute/expressroute-introduction) を使用して Azure ネットワークに接続されている場合、各回線は、[Azure パブリック ピアリング](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains)を使用して Azure Storage などの Microsoft サービスへ接続するために使用している 2 つのパブリック IP アドレスを使って Microsoft Edge で構成されます。  回線から Azure Storage への通信を許可するには、回線のパブリック IP アドレスに対する IP ネットワーク ルールを作成する必要があります。  ExpressRoute 回線のパブリック IP アドレスを確認するには、Azure Portal から [ExpressRoute のサポート チケットを開いて](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)ください。


### <a name="managing-ip-network-rules"></a>IP ネットワーク ルールの管理
ストレージ アカウント用の IP ネットワーク ルールは、Azure Portal、PowerShell、または CLIv2 を通じて管理できます。

#### <a name="azure-portal"></a>Azure ポータル
1. セキュリティで保護するストレージ アカウントを表示します。  
2. **[Firewalls and virtual networks] (ファイアウォールおよび仮想ネットワーク)** という設定メニューをクリックします。
3. "選択したネットワーク" からのアクセスを許可するように選択していることを確認します。
4. インターネット IP 範囲へのアクセスを許可するには、[ファイアウォール] の [アドレス範囲] に IP アドレスまたはアドレス範囲 (CIDR 形式) を入力します。
5. IP ネットワーク ルールを削除するには、[...] をクリックしてルールのコンテキスト メニューを開き、[削除] をクリックします。
6. *[保存]* をクリックして変更を保存します。

#### <a name="powershell"></a>PowerShell
1. 最新の [Azure PowerShell](/powershell/azure/install-azurerm-ps) をインストールして[ログイン](/powershell/azure/authenticate-azureps)します。
2. IP ネットワーク ルールを一覧表示します。
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. 個々 の IP アドレスに対するネットワーク ルールを追加します。  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. IP アドレス範囲に対するネットワーク ルールを追加します。  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. 個々 の IP アドレスに対するネットワーク ルールを削除します。 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. IP アドレス範囲に対するネットワーク ルールを削除します。  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> 拒否するように[既定のルールを設定](#change-the-default-network-access-rule)していることを確認します。そうしないと、ネットワーク ルールは効力を発揮しません。
>

#### <a name="cliv2"></a>CLIv2
1. [Azure CLI 2.0](/cli/azure/install-azure-cli) をインストールして[ログイン](/cli/azure/authenticate-azure-cli)します。
2. IP ネットワーク ルールを一覧表示します。
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. 個々 の IP アドレスに対するネットワーク ルールを追加します。
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. IP アドレス範囲に対するネットワーク ルールを追加します。  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. 個々 の IP アドレスに対するネットワーク ルールを削除します。  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. IP アドレス範囲に対するネットワーク ルールを削除します。  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> 拒否するように[既定のルールを設定](#change-the-default-network-access-rule)していることを確認します。そうしないと、ネットワーク ルールは効力を発揮しません。
>

## <a name="exceptions"></a>例外
ネットワーク ルールでは、ほとんどのシナリオに対応する安全なネットワーク構成を実現できますが、場合によっては、完全な機能を有効にするために例外を許可する必要があります。  ストレージ アカウントは、信頼できる Microsoft サービスに対する例外と、ストレージ分析データにアクセスするための例外で構成できます。

### <a name="trusted-microsoft-services"></a>信頼できる Microsoft サービス
ストレージ アカウントとやり取りする一部の Microsoft サービスは、ネットワーク ルールでアクセスを許可できないネットワークから実行されます。 

この種類のサービスを意図したとおりに動作させるために、一連の信頼できる Microsoft サービスがネットワーク ルールをバイパスできるように許可することができます。 そうすると、これらのサービスはストレージ アカウントにアクセスするために強力な認証を使用します。

「信頼できる Microsoft サービス」の例外を有効にすると、(サブスクリプションに登録されている場合は) 次のサービスにストレージ アカウントへのアクセスが許可されます。

|サービス|リソース プロバイダー名|目的|
|:------|:---------------------|:------|
|Azure DevTest Labs|Microsoft.DevTestLab|カスタム イメージの作成とアーティファクトのインストール  [詳細情報](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-overview)|
|Azure Event Grid|Microsoft.EventGrid|Blob Storage イベントの発行を有効にする  [詳細情報](https://docs.microsoft.com/en-us/azure/event-grid/overview)|
|Azure Event Hubs|Microsoft.EventHub|Event Hubs Capture を使用したアーカイブ データのキャプチャ  [詳細情報](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-capture-overview)|
|Azure HDInsight|Microsoft.HDInsight|クラスターのプロビジョニングおよびインストール  [詳細情報](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-use-blob-storage)|
|Azure のネットワーク|Microsoft.Networking|ネットワーク トラフィック ログの保存および分析  [詳細情報](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-packet-capture-overview)|
|Azure SQL Data Warehouse|Microsoft.Sql|データのインポートとエクスポート  [詳細情報](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-overview-load#load-from-azure-blob-storage)|
||||

### <a name="storage-analytics-data-access"></a>ストレージ分析データ アクセス
場合によっては、ネットワーク境界の外側から診断ログとメトリックを読み取るためにアクセスする必要があります。  ネットワーク ルールの例外では、ストレージ アカウントのログ ファイル、メトリック テーブル、またはその両方への読み取りアクセスを許可できます。 [ストレージ分析の使用に関する説明](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>例外の管理
ネットワーク ルールの例外は、Azure Portal、PowerShell、または Azure CLI v2 を通じて管理できます。

#### <a name="azure-portal"></a>Azure ポータル
1. セキュリティで保護するストレージ アカウントを表示します。  
2. **[Firewalls and virtual networks] (ファイアウォールおよび仮想ネットワーク)** という設定メニューをクリックします。
3. "選択したネットワーク" からのアクセスを許可するように選択していることを確認します。
4. [例外] で、許可する例外を選択します。
5. *[保存]* をクリックして変更を保存します。

#### <a name="powershell"></a>PowerShell
1. 最新の [Azure PowerShell](/powershell/azure/install-azurerm-ps) をインストールして[ログイン](/powershell/azure/authenticate-azureps)します。
2. ストレージ アカウントのネットワーク ルールの例外を表示します。
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. ストレージ アカウントのネットワーク ルールの例外を設定します。
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. ストレージ アカウントのネットワーク ルールの例外を削除します。
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> 拒否するように[既定のルールを設定](#change-the-default-network-access-rule)していることを確認します。そうしないと、例外の削除は効力を発揮しません。
>

#### <a name="cliv2"></a>CLIv2
1. [Azure CLI 2.0](/cli/azure/install-azure-cli) をインストールして[ログイン](/cli/azure/authenticate-azure-cli)します。
2. ストレージ アカウントのネットワーク ルールの例外を表示します。
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkAcls.bypass
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
> 拒否するように[既定のルールを設定](#change-the-default-network-access-rule)していることを確認します。そうしないと、例外の削除は効力を発揮しません。
>

## <a name="next-steps"></a>次のステップ
[サービス エンドポイント](/azure/virtual-network/virtual-network-service-endpoints-overview)で Azure ネットワークのサービス エンドポイントについて確認してください。

[Azure Storage セキュリティ ガイド](storage-security-guide.md)で Azure Storage のセキュリティを詳しく調べてください。
