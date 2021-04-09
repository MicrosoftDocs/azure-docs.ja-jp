---
title: PowerShell を使用して Key Vault を設定する
description: 仮想マシンで使用するために PowerShell を使用して Key Vault を設定する方法。
author: mimckitt
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: 124ab121186ff7c9047515f2ee1ff51cee1114b8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557490"
---
# <a name="set-up-key-vault-for-virtual-machines-using-azure-powershell"></a>Azure PowerShell を使用して仮想マシンの Key Vault を設定する

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Resource Manager スタックでは、Key Vault のリソース プロバイダーにより提供されるリソースとしてシークレット/証明書がモデル化されます。 Key Vault の詳細については、「 [Azure Key Vault とは](../../key-vault/general/overview.md)

> [!NOTE]
> 1. Key Vault を Azure Resource Manager 仮想マシンと共に使用するには、Key Vault の **EnabledForDeployment** プロパティを True に設定する必要があります。 この設定は、さまざまなクライアントで実行できます。
> 2. Key Vault は、仮想マシンと同じサブスクリプションと場所に作成する必要があります。
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>PowerShell を使用して Key Vault を設定する
PowerShell を使用して Key Vault を作成するには、「[PowerShell を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../../key-vault/secrets/quick-create-powershell.md)」を参照してください。

新しい Key Vault の場合は、次の PowerShell コマンドレットを使用することができます。

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment
```

既存の Key Vault の場合は、次の PowerShell コマンドレットを使用することができます。

```azurepowershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment
```

## <a name="use-cli-to-set-up-key-vault"></a>CLI を使用して Key Vault を設定する
コマンド ライン インターフェイス (CLI) を使用して Key Vault を作成するには、「 [CLI を使用した Key Vault の管理](../../key-vault/general/manage-with-cli2.md#create-a-key-vault)」を参照してください。

CLI の場合、デプロイ ポリシーを割り当てる前に、Key Vault を作成する必要があります。 この処理には、次のコマンドを使用できます。

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
```

さらに、テンプレートのデプロイで使用するために Key Vault を有効にするには、次のコマンドを実行します。

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
```

## <a name="use-templates-to-set-up-key-vault"></a>テンプレートを使用して Key Vault を設定する
テンプレートを使用する場合、Key Vault リソースの `enabledForDeployment` プロパティを `true` に設定する必要があります。

```config
{
  "type": "Microsoft.KeyVault/vaults",
  "name": "ContosoKeyVault",
  "apiVersion": "2015-06-01",
  "location": "<location-of-key-vault>",
  "properties": {
    "enabledForDeployment": "true",
    ....
    ....
  }
}
```

テンプレートを使用して、Key Vault の作成時に構成できるその他のオプションについては、「 [Create a Key Vault](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)」を参照してください。
