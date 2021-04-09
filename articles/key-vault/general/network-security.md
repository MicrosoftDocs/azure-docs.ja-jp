---
title: Azure Key Vault のファイアウォールと仮想ネットワークを構成する - Azure Key Vault
description: Key Vault のファイアウォールと仮想ネットワークを構成する手順
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 13ead70f278f12866dbe00b53c487aebcc43304f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742638"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault のファイアウォールと仮想ネットワークを構成する

この記事では、Azure Key Vault ファイアウォールを構成する方法に関するガイダンスを示します。 このドキュメントでは Key Vault ファイアウォールのさまざまな構成について詳しく説明すると共に、他のアプリケーションや Azure サービスと連携するように Azure Key Vault を構成する手順について説明します。

詳細については、「[Azure Key Vault の仮想ネットワーク サービス エンドポイント](overview-vnet-service-endpoints.md)」を参照してください。

## <a name="firewall-settings"></a>ファイアウォール設定

このセクションでは、Azure Key Vault ファイアウォールを構成するためのさまざまな方法について説明します。

### <a name="key-vault-firewall-disabled-default"></a>Key Vault ファイアウォールを無効にする (既定値)

既定では、新しいキー コンテナーを作成するとき、Azure Key Vault ファイアウォールは無効になります。 すべてのアプリケーションおよび Azure サービスから、キー コンテナーにアクセスし、キー コンテナーに要求を送信することができます。 なお、この構成は、お客様のキー コンテナー上で任意のユーザーが操作を行えるということを意味するものではありません。 キー コンテナーは、Azure Active Directory 認証およびアクセス ポリシーのアクセス許可を必要とすることで、キー コンテナーに格納されているシークレット、キー、および証明書に引き続き制限されます キー コンテナー認証の詳細については、[こちら](./authentication-fundamentals.md)のキー コンテナー認証の基礎に関するドキュメントを参照してください。

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Key Vault ファイアウォールを有効にする (信頼されたサービスのみ)

Key Vault ファイアウォールを有効にすると、[Allow trusted Microsoft services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\) に対するオプションが表示されます。 信頼されたサービスのリストには、単一の Azure サービスがすべてが含まれるわけではありません。 たとえば、Azure DevOps は信頼されたサービスのリストに掲載されていません。 **これは、信頼されたサービスのリストに表示されないサービスが信頼されていない、またはセキュリティで保護されていないということを意味するものではありません。** 信頼されたサービスのリストには、サービス上で実行されるすべてのコードを Microsoft が制御するサービスが含まれます。 Azure DevOps などの Azure サービス内ではユーザーがカスタム コードを記述できるため、Microsoft はサービスの包括的承認を作成するオプションを提供していません。 さらに、サービスが、信頼されたサービスのリストに掲載されていたからといって、それがすべてのシナリオに対して許可されるわけではありません。

使おうとしているサービスが信頼されたサービスのリストにあるかどうかを判断するには、[こちら](./overview-vnet-service-endpoints.md#trusted-services)のドキュメントを参照してください。

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Key Vault ファイアウォールを有効にする (IPv4 アドレスおよび範囲 - 静的 IP)

Key Vault ファイアウォールを経由してキー コンテナーにアクセスする特定のサービスを承認したい場合は、その IP アドレスをキー コンテナー ファイアウォール許可リストに追加します。 この構成は、静的 IP アドレスまたは既知の範囲を使用するサービスに最適です。 この場合、CIDR 範囲については、1000 までという制限があります。

Web アプリやロジック アプリなどの Azure リソースの IP アドレスまたは範囲を許可するには、次の手順を行います。

1. Azure Portal にログインする
1. リソース (サービスの特定のインスタンス) を選択する
1. [設定] の下にある [プロパティ] ブレードをクリックする
1. [IP アドレス] フィールドを探す。
1. この値または範囲をコピーし、キー コンテナー ファイアウォール許可リストに入力する。

Key Vault ファイアウォールを介して Azure サービス全体を許可するには、[こちら](https://www.microsoft.com/download/details.aspx?id=41653)の Azure 用の公式に文書化されたデータセンター IP アドレスのリストを使用してください。 目的のリージョンで必要なサービスに関連付けられている IP アドレスを見つけ、それらの IP アドレスを上記の手順を使用してキー コンテナー ファイアウォールに追加します。

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Key Vault ファイアウォールを有効にする (仮想ネットワーク - 動的 IP)

キー コンテナーを介して仮想マシンなどの Azure リソースを許可しようとしている場合は、静的 IP アドレスを使用できない可能性があり、さらに Azure Virtual Machines の一部の IP アドレスにキー コンテナーへのアクセスを許可したくないこともあります。

この場合は、仮想ネットワーク内にリソースを作成し、特定の仮想ネットワークおよびサブネットからのトラフィックがご利用のキー コンテナーにアクセスするのを許可する必要があります。 このためには、次の手順を実行します。

1. Azure Portal にログインする
1. 構成するキー コンテナーを選択する
1. [ネットワーク] タブを選択する
1. [+ 既存の仮想ネットワークを追加] を選択する
1. キー コンテナー ファイアウォールを介して許可する仮想ネットワークおよびサブネットを選択する。

### <a name="key-vault-firewall-enabled-private-link"></a>Key Vault ファイアウォールを有効にする (プライベート リンク)

ご利用のキー コンテナー上でプライベート リンク接続を構成する方法については、[こちら](./private-link-service.md)のドキュメントを参照してください。

> [!IMPORTANT]
> ファイアウォール ルールを有効にした後は、要求が許可された仮想ネットワークまたは IPv4 アドレス範囲から送信された場合にのみ、ユーザーは Key Vault [データ プレーン](secure-your-key-vault.md#data-plane-access-control)の操作を実行できます。 これは、Azure portal から Key Vault にアクセスする場合にも適用されます。 ユーザーは Azure portal からキー コンテナーを参照できますが、クライアント マシンが許可リストに登録されていない場合、キー/シークレット/証明書を一覧表示できない場合があります。 これは、他の Azure サービスによる Key Vault 選択機能にも影響します。 ファイアウォール ルールでクライアント マシンが許可されていない場合、ユーザーはキー コンテナーを一覧表示できても、キーは一覧表示できないことがあります。

> [!NOTE]
> 構成に関する次の制限事項に注意してください。
> * 最大で 127 個の仮想ネットワーク規則と 127 個の IPv4 ルールを指定できます。 
> * IP ネットワーク ルールは、パブリック IP アドレスに対してのみ許可されます。 プライベート ネットワーク用に予約されている IP アドレス範囲 (RFC 1918 で定義) は、IP ルールでは許可されません。 プライベート ネットワークには、**10.** 、**172.16-31**、および **192.168.** で始まるアドレスが含まれます。 
> * 現時点でサポートされているのは、IPv4 アドレスのみです。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

Azure portal を使用して Key Vault ファイアウォールと仮想ネットワークを構成する方法を次に示します。

1. セキュリティで保護するキー コンテナーに移動します。
2. **[ネットワーク]** を選択してから、 **[ファイアウォールと仮想ネットワーク]** タブを選択します。
3. **[許可するアクセス元]** の **[選択されたネットワーク]** を選択します。
4. 既存の仮想ネットワークをファイアウォールと仮想ネットワークの規則に追加するには、 **[+ 既存の仮想ネットワークを追加]** を選択します。
5. 表示される新しいブレードで、このキー コンテナーへのアクセスを許可するサブスクリプション、仮想ネットワーク、サブネットを選択します。 選択する仮想ネットワークとサブネットでサービス エンドポイントが有効になっていない場合は、サービス エンドポイントを有効にする必要があることを確認して、 **[有効]** を選択します。 有効になるまでに最大 15 分かかることがあります。
6. **[IP ネットワーク]** では、[CIDR (Classless Inter-Domain Routing) 表記](https://tools.ietf.org/html/rfc4632)で IPv4 アドレスの範囲を入力して IPv4 アドレス範囲を追加するか、個々の IP アドレスを追加します。
7. 信頼された Microsoft サービスが Key Vault ファイアウォールをバイパスすることを許可する場合には、[はい] を選択します。 Key Vault で現在信頼されているサービスの完全な一覧については、次のリンクを参照してください。 [Azure Key Vault の信頼済みサービス](./overview-vnet-service-endpoints.md#trusted-services)
7. **[保存]** を選択します。

**[+ 新しい仮想ネットワークを追加]** を選択し、新しい仮想ネットワークとサブネットを追加して、新しく作成した仮想ネットワークとサブネットのサービス エンドポイントを有効にすることもできます。 その後、プロンプトに従います。

## <a name="use-the-azure-cli"></a>Azure CLI の使用 

Azure CLI を使用して Key Vault ファイアウォールと仮想ネットワークを構成する方法を次に示します

1. [Azure CLI をインストール](/cli/azure/install-azure-cli)して[サインイン](/cli/azure/authenticate-azure-cli)します。

2. 使用可能な仮想ネットワーク規則の一覧を表示します。 このキー コンテナーに対してルールを何も設定していない場合、一覧は空になります。
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. 既存の仮想ネットワークとサブネット上の Key Vault のサービス エンドポイントを有効にします。
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. 仮想ネットワークとサブネットに対するネットワーク ルールを追加します。
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. そこから送信されたトラフィックを許可する IP アドレス範囲を追加します。
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. すべての信頼されたサービスでこのキー コンテナーにアクセスできるようにする必要がある場合は、`bypass` を `AzureServices` に設定します。
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. 既定のアクションを `Deny` に設定することによって、ネットワーク ルールを有効にします。
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Azure PowerShell の使用

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell を使用して Key Vault ファイアウォールと仮想ネットワークを構成する方法を次に示します。

1. 最新の [Azure PowerShell](/powershell/azure/install-az-ps) をインストールして[サインイン](/powershell/azure/authenticate-azureps)します。

2. 使用可能な仮想ネットワーク規則の一覧を表示します。 このキー コンテナーに対してルールを何も設定していない場合、一覧は空になります。
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. 既存の仮想ネットワークとサブネット上の Key Vault のサービス エンドポイントを有効にします。
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. 仮想ネットワークとサブネットに対するネットワーク ルールを追加します。
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. そこから送信されたトラフィックを許可する IP アドレス範囲を追加します。
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. すべての信頼されたサービスでこのキー コンテナーにアクセスできるようにする必要がある場合は、`bypass` を `AzureServices` に設定します。
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. 既定のアクションを `Deny` に設定することによって、ネットワーク ルールを有効にします。
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>References
* ARM テンプレート リファレンス: [Azure Key Vault ARM テンプレート リファレンス](/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI コマンド: [az keyvault network-rule](/cli/azure/keyvault/network-rule)
* Azure PowerShell コマンドレット:[Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault)、[Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule)、[Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule)、[Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>次のステップ

* [Key Vault の仮想ネットワーク サービス エンドポイント](overview-vnet-service-endpoints.md)
* [Azure Key Vault のセキュリティの概要](security-overview.md)
