---
title: Azure Key Vault のファイアウォールと仮想ネットワークを構成する - Azure Key Vault
description: Key Vault のファイアウォールと仮想ネットワークを構成する手順
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: sudbalas
ms.custom: devx-track-azurecli
ms.openlocfilehash: d416c6fdef85b83d91ca0e341659f87618d268e0
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504519"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault のファイアウォールと仮想ネットワークを構成する

この記事では、キー コンテナーへのアクセスを制限するために、Azure Key Vault のファイアウォールと仮想ネットワークを構成する手順について説明します。 [Key Vault の仮想ネットワーク サービス エンドポイント](overview-vnet-service-endpoints.md)を使用すると、指定した仮想ネットワークや、IPv4 (インターネット プロトコル バージョン 4) アドレス範囲のリストに対するアクセスを制限することができます。

> [!IMPORTANT]
> ファイアウォール ルールを有効にした後は、要求が許可された仮想ネットワークまたは IPv4 アドレス範囲から送信された場合にのみ、ユーザーは Key Vault [データ プレーン](secure-your-key-vault.md#data-plane-access-control)の操作を実行できます。 これは、Azure portal から Key Vault にアクセスする場合にも適用されます。 ユーザーは Azure portal からキー コンテナーを参照できますが、クライアント マシンが許可リストに登録されていない場合、キー/シークレット/証明書を一覧表示できない場合があります。 これは、他の Azure サービスによる Key Vault 選択機能にも影響します。 ファイアウォール ルールでクライアント マシンが許可されていない場合、ユーザーはキー コンテナーを一覧表示できても、キーは一覧表示できないことがあります。

> [!NOTE]
> 構成に関する次の制限事項に注意してください。
> * 最大で 127 個の仮想ネットワーク規則と 127 個の IPv4 ルールを指定できます。 
> * "/31" や "/32" のプレフィックス サイズを使用する小さいアドレス範囲はサポートされていません。 これらの範囲を構成するには、個別の IP アドレス ルールを使用します。
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
7. 信頼された Microsoft サービスが Key Vault ファイアウォールをバイパスすることを許可する場合には、[はい] を選択します。 Key Vault で現在信頼されているサービスの完全な一覧については、次のリンクを参照してください。 [Azure Key Vault の信頼済みサービス](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
7. **[保存]** を選択します。

**[+ 新しい仮想ネットワークを追加]** を選択し、新しい仮想ネットワークとサブネットを追加して、新しく作成した仮想ネットワークとサブネットのサービス エンドポイントを有効にすることもできます。 その後、プロンプトに従います。

## <a name="use-the-azure-cli"></a>Azure CLI の使用 

Azure CLI を使用して Key Vault ファイアウォールと仮想ネットワークを構成する方法を次に示します

1. [Azure CLI をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)して[サインイン](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)します。

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

1. 最新の [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) をインストールして[サインイン](https://docs.microsoft.com/powershell/azure/authenticate-azureps)します。

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
* ARM テンプレート リファレンス: [Azure Key Vault ARM テンプレート リファレンス](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI コマンド: [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell コマンドレット:[Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault)、[Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule)、[Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule)、[Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>次のステップ

* [Key Vault の仮想ネットワーク サービス エンドポイント](overview-vnet-service-endpoints.md)
* [キー コンテナーをセキュリティで保護する](secure-your-key-vault.md)
