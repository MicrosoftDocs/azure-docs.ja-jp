---
title: Azure CLI による Azure スケール セットのディスクの暗号化
description: Azure CLI を使用して、Windows 仮想マシン スケール セット内の VM インスタンスおよび接続されたディスクを暗号化する方法について説明します
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 302f53bd218a2e01a039be4780a0e2ff5974e7b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102215954"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Azure CLI による仮想マシン スケール セットの OS および接続されているデータ ディスクの暗号化

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、Azure CLI を使用して仮想マシン スケール セットを作成、暗号化する方法を示します。 仮想マシン スケール セットへの Azure Disk Encryption の適用の詳細については、「[仮想マシン スケール セットの Azure Disk Encryption](disk-encryption-overview.md)」を参照してください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.31 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-scale-set"></a>スケール セットを作成する

スケール セットを作成する前に、[az group create](/cli/azure/group) を使ってリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

ここでは、[az vmss create](/cli/azure/vmss) を使って仮想マシン スケール セットを作成します。 以下の例では、*myScaleSet* という名前のスケール セットを作成します。このスケール セットは、変更が適用されると自動的に更新するように設定され、SSH キーが *~/.ssh/id_rsa* に存在しない場合は生成します。 VM インスタンスのそれぞれに 32GB のディスクが接続され､Azure [Custom Script Extension](../virtual-machines/extensions/custom-script-linux.md) では､[az vmss extension set](/cli/azure/vmss/extension) を使用してデータ ディスクが作成されます｡

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>ディスクの暗号化を有効にした Azure キー コンテナーを作成する

Azure Key Vault は、キー、シークレット、パスワードを格納して、アプリケーションとサービスに安全に実装できるようにします。 暗号化キーは、ソフトウェア保護を使って Azure Key Vault に格納されます。または、FIPS 140-2 レベル 2 標準に認定された Hardware Security Module (HSM) でキーをインポートまたは生成することもできます。 これらの暗号化キーは、VM に接続された仮想ディスクの暗号化/暗号化解除に使われます。 これらの暗号化キーの制御を維持し、その使用を監査することができます。

一意の *keyvault_name* を定義します｡ [az keyvault create](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) を使用して､スケール セットと同じサブスクリプションとリージョンに KeyVault を作成し､ *--enabled-for-disk-encryption* アクセス ポリシーを設定します｡

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>既存の Key Vault を使用する

この手順は、ディスク暗号化で使用する既存の Key Vault がある場合にのみ必要です。 前のセクションで Key Vault を作成した場合は、この手順をスキップしてください。

一意の *keyvault_name* を定義します｡ [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) を使用して KeyVault を更新し､ *--enabled-for-disk-encryption* アクセス ポリシーを設定します｡

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>暗号化を有効にする

スケールセット内の VM インスタンスを暗号化するには、まず [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show) を使用して Key Vault のリソース ID に関する一部情報を取得します。 これらの変数は､この後､[az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable) を使用した暗号化プロセスの開始に使用されます｡

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

暗号化プロセスを開始するのに 1~2 分ほどかかります｡

前の手順で作成したスケールセットのアップグレード ポリシーが *automatic* に設定されているため､各 VM インスタンスは自動的に暗号化プロセスを開始します｡ アップグレード ポリシーが manual に設定されているスケール セットでは､[az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances) を使用して手動で 各 VM インスタンスに対して暗号化プロセスを開始します｡

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>キーをラップする KEK を使用して暗号化を有効にします。

仮想マシン スケール セットを暗号化するときに、セキュリティ強化のためキーの暗号化キーを使用することもできます。

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  disk-encryption-keyvault パラメーターの値の構文は、完全な識別子の文字列です。</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> disk-encryption-keyvault パラメーターの値の構文は、以下のように KEK までのフル URI です。</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>暗号化の進行状況を確認する

ディスク暗号化の進行状況を確認するには､[az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show) を使用します｡

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

VM インスタンスが暗号化されていると､以下の出力例に見られるようにステータスコード *EncryptionState/encrypted* が報告されます｡

```console
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>暗号化を無効にする

暗号化された VM インスタンス ディスクを使用しない場合は、次のように [az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable) で暗号化を無効にすることができます。

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>次のステップ

- この記事では、Azure CLI を使用して仮想マシン スケール セットを暗号化しました。 また、[Azure PowerShell](disk-encryption-powershell.md) または [Azure Resource Manager テンプレート](disk-encryption-azure-resource-manager.md)も使用できます。
- 別の拡張機能がプロビジョニングされた後で Azure Disk Encryption を適用する場合、[extension sequencing](virtual-machine-scale-sets-extension-sequencing.md)を使用できます。 
- Linux スケール セット データ ディスクの暗号化のエンドツーエンド バッチ ファイルの例については、[こちら](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat)を参照してください。 この例では、リソース グループ、Linux スケール セットを作成し、5 GB のデータ ディスクをマウントし、仮想マシン スケール セットを暗号化します。
