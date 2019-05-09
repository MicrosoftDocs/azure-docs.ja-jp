---
title: Azure CLI による Azure スケール セットのディスクの暗号化 | Microsoft Docs
description: Azure CLI を使用して、Linux 仮想マシン スケール セットの VM インスタンスと接続されているディスクを暗号化する方法について説明します
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: cynthn
ms.openlocfilehash: 417772b2e955b1a3664dd495f292a76ab2819165
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734523"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli-preview"></a>Azure CLI (プレビュー) による仮想マシン スケール セットの OS および接続されているデータ ディスクの暗号化

業界標準の暗号化テクノロジを使用して保存データを保護および防御するために、仮想マシン スケール セットは Azure Disk Encryption (ADE) をサポートしています。 暗号化は､Windows および Linux の仮想マシン スケール セットに対して有効にすることができます。 詳細は、[Windows 用および Linux 用の Azure Disk Encryption](../security/azure-security-disk-encryption.md) に関するページを参照してください。

> [!NOTE]
>  仮想マシン スケール セット用 Azure Disk Encryption は、現在パブリック プレビュー段階であり、すべての Azure パブリック リージョンで利用できます。

Azure Disk Encryption は次の場合にサポートされます。
- マネージド ディスクで作成されたスケール セット。ネイティブ (または管理対象ではない) ディスク スケール セットの場合はサポートされません。
- Windows スケール セット内の OS とデータ ボリューム。 Windows スケール セットの OS とデータ ボリュームでは、暗号化の無効化がサポートされています。
- Linux スケール セット内のデータ ボリューム。 Linux スケール セット用の現在のプレビューでは、OS ディスクの暗号化はサポートされていません。

現在のプレビューでは、スケール セット VM の再イメージ操作とアップグレード操作はサポートされていません。 仮想マシンのスケール セット プレビューの Azure Disk Encryption は、テスト環境でのみ推奨されます。 プレビューでは、暗号化スケール セット内の OS イメージをアップグレードする必要のある運用環境でディスクの暗号化を有効にしないでください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、Azure CLI バージョン 2.0.31 以降を実行していることがこのチュートリアルの要件になります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="register-for-disk-encryption-preview"></a>ディスク暗号化プレビューの登録をする

仮想マシン スケール セットのプレビューのための Azure ディスク暗号化では、 [az feature register](/cli/azure/feature) を使用してサブスクリプションを自己登録する必要があります。 ディスク暗号化のプレビュー機能を初めて利用する場合は､以下の手順を行えばよいだけです｡

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

登録要求が伝達されるのに最大 10 分時間がかかることがあります｡ 登録状態は､[az feature show](/cli/azure/feature) で確認できます｡ `State` から *Registered* と報告されたら､[az provider register](/cli/azure/provider) を使って *Microsoft.Compute* プロバイダーを登録し直します。

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="create-a-scale-set"></a>スケール セットを作成する

スケール セットを作成する前に、[az group create](/cli/azure/group) を使ってリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

ここでは、[az vmss create](/cli/azure/vmss) を使って仮想マシン スケール セットを作成します。 以下の例では、*myScaleSet* という名前のスケール セットを作成します。このスケール セットは、変更が適用されると自動的に更新するように設定され、SSH キーが *~/.ssh/id_rsa* に存在しない場合は生成します。 VM インスタンスのそれぞれに 32GB のディスクが接続され､Azure [Custom Script Extension](../virtual-machines/linux/extensions-customscript.md) では､[az vmss extension set](/cli/azure/vmss/extension) を使用してデータ ディスクが作成されます｡

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

一意の *keyvault_name* を定義します｡ [az keyvault create](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) を使用して､スケール セットと同じサブスクリプションとリージョンに KeyVault を作成し､*--enabled-for-disk-encryption* アクセス ポリシーを設定します｡

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>既存の Key Vault を使用する

この手順は、ディスク暗号化で使用する既存の Key Vault がある場合にのみ必要です。 前のセクションで Key Vault を作成した場合は、この手順をスキップしてください。

一意の *keyvault_name* を定義します｡ [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) を使用して KeyVault を更新し､*--enabled-for-disk-encryption* アクセス ポリシーを設定します｡

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

## <a name="check-encryption-progress"></a>暗号化の進行状況を確認する

ディスク暗号化の進行状況を確認するには､[az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show) を使用します｡

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

VM インスタンスが暗号化されていると､以下の出力例に見られるようにステータスコード *EncryptionState/encrypted* が報告されます｡

```bash
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

暗号化された VM インスタンス ディスクを使用しない場合は、次のように [az vmss encryption disable](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) で暗号化を無効にすることができます。

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>次の手順

この記事では、Azure CLI を使用して仮想マシン スケール セットを暗号化しました。 [Azure PowerShell](virtual-machine-scale-sets-encrypt-disks-ps.md) や、[Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) または [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox) 用のテンプレートを利用することもできます。

Linux スケール セット データ ディスクの暗号化のエンドツーエンド バッチ ファイルの例については、[こちら](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat)を参照してください。 この例では、リソース グループ、Linux スケール セットを作成し、5 GB のデータ ディスクをマウントし、仮想マシン スケール セットを暗号化します。
