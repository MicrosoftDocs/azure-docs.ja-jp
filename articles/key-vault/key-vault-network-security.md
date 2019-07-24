---
ms.assetid: ''
title: Azure Key Vault のファイアウォールと仮想ネットワークを構成する - Azure Key Vault
description: Key Vault のファイアウォールと仮想ネットワークを構成する手順
services: key-vault
author: amitbapat
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 01/02/2019
ms.author: ambapat
ms.openlocfilehash: c54b78a24068758fabb0918cfeb7d6516fd1bce5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487245"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault のファイアウォールと仮想ネットワークを構成する

この記事では、キー コンテナーへのアクセスを制限するために、Azure Key Vault のファイアウォールと仮想ネットワークを構成する手順について説明します。 [Key Vault の仮想ネットワーク サービス エンドポイント](key-vault-overview-vnet-service-endpoints.md)を使用すると、指定した仮想ネットワークや、IPv4 (インターネット プロトコル バージョン 4) アドレス範囲のリストに対するアクセスを制限することができます。

> [!IMPORTANT]
> ファイアウォール ルールを有効にした後は、要求が許可された仮想ネットワークまたは IPv4 アドレス範囲から送信された場合にのみ、ユーザーは Key Vault [データ プレーン](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control)の操作を実行できます。 これは、Azure portal から Key Vault にアクセスする場合にも適用されます。 ユーザーは Azure portal からキー コンテナーを参照できますが、クライアント マシンが許可リストに登録されていない場合、キー/シークレット/証明書を一覧表示できない場合があります。 これは、他の Azure サービスによる Key Vault 選択機能にも影響します。 ファイアウォール ルールでクライアント マシンが許可されていない場合、ユーザーはキー コンテナーを一覧表示できても、キーは一覧表示できないことがあります。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

Azure portal を使用して Key Vault ファイアウォールと仮想ネットワークを構成する方法を次に示します。

1. セキュリティで保護するキー コンテナーに移動します。
2. **[ファイアウォールと仮想ネットワーク]** を選択します。
3. **[許可するアクセス元]** の **[選択されたネットワーク]** を選択します。
4. 既存の仮想ネットワークをファイアウォールと仮想ネットワークのルールに追加するには、**[+ 既存の仮想ネットワークを追加]** を選択します。
5. 表示される新しいブレードで、このキー コンテナーへのアクセスを許可するサブスクリプション、仮想ネットワーク、サブネットを選択します。 選択する仮想ネットワークとサブネットでサービス エンドポイントが有効になっていない場合は、サービス エンドポイントを有効にする必要があることを確認して、**[有効]** を選択します。 有効になるまでに最大 15 分かかることがあります。
6. **[IP ネットワーク]** では、[CIDR (Classless Inter-Domain Routing) 表記](https://tools.ietf.org/html/rfc4632)で IPv4 アドレスの範囲を入力して IPv4 アドレス範囲を追加するか、個々の IP アドレスを追加します。
7. **[保存]** を選択します。

**[+ 新しい仮想ネットワークを追加]** を選択し、新しい仮想ネットワークとサブネットを追加して、新しく作成した仮想ネットワークとサブネットのサービス エンドポイントを有効にすることもできます。 その後、プロンプトに従います。

## <a name="use-the-azure-cli"></a>Azure CLI の使用 

Azure CLI を使用して Key Vault ファイアウォールと仮想ネットワークを構成する方法を次に示します

1. [Azure CLI をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)して[サインイン](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)します。

2. 使用可能な仮想ネットワーク ルールの一覧を表示します。 このキー コンテナーに対してルールを何も設定していない場合、一覧は空になります。
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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell を使用して Key Vault ファイアウォールと仮想ネットワークを構成する方法を次に示します。

1. 最新の [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) をインストールして[サインイン](https://docs.microsoft.com/powershell/azure/authenticate-azureps)します。

2. 使用可能な仮想ネットワーク ルールの一覧を表示します。 このキー コンテナーに対してルールを何も設定していない場合、一覧は空になります。
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

## <a name="references"></a>参照

* Azure CLI コマンド: [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell コマンドレット:[Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault)、[Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule)、[Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule)、[Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>次の手順

* [Key Vault の仮想ネットワーク サービス エンドポイント](key-vault-overview-vnet-service-endpoints.md)
* [キー コンテナーのセキュリティ保護](key-vault-secure-your-key-vault.md)
