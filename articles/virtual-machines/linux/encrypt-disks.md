---
title: Azure で Linux VM のディスクを暗号化する | Microsoft Docs
description: セキュリティを強化するために、Azure CLI を使用して Linux VM の仮想ディスクを暗号化する方法
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: f38dab21d44e5ed4a01d841a515e8c50e1c713a6
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667894"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Azure で Linux 仮想マシンを暗号化する方法

仮想マシン (VM) のセキュリティとコンプライアンスを強化するために、仮想ディスクと VM 自体を暗号化できます。 VM は、Azure Key Vault で保護されている暗号化キーを使って暗号化されます。 これらの暗号化キーを制御し、その使用を監査することができます。 この記事では、Azure CLI を使用して Linux VM の仮想ディスクを暗号化する方法について詳しく説明します。 

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.30 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="overview-of-disk-encryption"></a>ディスク暗号化の概要
Linux VM の仮想ディスクは、[dm-crypt](https://wikipedia.org/wiki/Dm-crypt) を使って暗号化します。 Azure の仮想ディスクを暗号化するための料金はかかりません。 暗号化キーは、ソフトウェア保護を使って Azure Key Vault に格納されます。または、FIPS 140-2 レベル 2 標準に認定された Hardware Security Module (HSM) でキーをインポートまたは生成することもできます。 これらの暗号化キーの制御を維持し、その使用を監査することができます。 これらの暗号化キーは、VM に接続された仮想ディスクの暗号化/暗号化解除に使われます。 

VM 暗号化のプロセスは次のとおりです。

1. Azure Key Vault で暗号化キーを作成します。
1. ディスクの暗号化に使うことができるように暗号化キーを構成します。
1. 仮想ディスクのディスク暗号化を有効にします。
1. Azure Key Vault から必要な暗号化キーが要求されます。
1. 提供された暗号化キーを使って仮想ディスクが暗号化されます。


## <a name="requirements-and-limitations"></a>要件と制限
ディスク暗号化のサポートされるシナリオと要件:

* 次の Linux サーバー SKU - Ubuntu、CentOS、SUSE と SUSE Linux Enterprise Server (SLES)、Red Hat Enterprise Linux。
* すべてのリソース (Key Vault、ストレージ アカウント、VM など) は、同じ Azure リージョンとサブスクリプションに存在している必要があります。
* Standard A、D、DS、G、GS などの VM シリーズ。
* 既に暗号化されている Linux VM での暗号化キーの更新。

現在、ディスクの暗号化は次のシナリオではサポートされていません。

* Basic レベルの VM。
* クラシック デプロイ モデルで作成された VM。
* Linux VM での OS ディスク暗号化の無効化。
* カスタム Linux イメージの使用。

サポートされているシナリオと制限事項の詳細については、[IaaS VM の Azure Disk Encryption](../../security/azure-security-disk-encryption.md) に関するページをご覧ください。


## <a name="create-an-azure-key-vault-and-keys"></a>Azure Key Vault とキーを作成する
次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*myKey*、*myVM* といったパラメーター名にします。

最初に、暗号化キーを格納する Azure Key Vault を作成します。 Azure Key Vault は、キー、シークレット、パスワードを格納して、アプリケーションとサービスに安全に実装できるようにします。 仮想ディスクの暗号化では、仮想ディスクの暗号化または暗号化解除に使われる暗号化キーを格納するために Key Vault を使います。

[az provider register](/cli/azure/provider#az-provider-register) を使用して Azure サブスクリプションで Azure Key Vault プロバイダーを有効にし、[az group create](/cli/azure/group#az-group-create) を使用してリソース グループを作成します。 次の例では、リソース グループ名 *myResourceGroup* を `eastus` に作成します。

```azurecli-interactive
az provider register -n Microsoft.KeyVault
resourcegroup="myResourceGroup"
az group create --name $resourcegroup --location eastus
```

暗号化キーおよびストレージや VM 自体などの関連するコンピューティング リソースを格納する Azure Key Vault は、同じリージョンに存在する必要があります。 [az keyvault create](/cli/azure/keyvault#az-keyvault-create) を使用して Azure Key Vault を作成し、ディスクの暗号化で使用するために Key Vault を有効にします。 次のように、*keyvault_name* の一意の Key Vault 名を指定します。

```azurecli-interactive
keyvault_name=myvaultname$RANDOM
az keyvault create \
    --name $keyvault_name \
    --resource-group $resourcegroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

ソフトウェアまたはハードウェア セキュリティ モデル (HSM) の保護を使って、暗号化キーを格納できます。 HSM を使うには、Premium Key Vault が必要です。 ソフトウェアで保護されたキーを格納する Standard Key Vault ではなく Premium Key Vault を作成するには、追加コストがかかります。 Premium Key Vault を作成するには、前の手順で `--sku Premium` をコマンドに追加します。 ここでは Standard Key Vault を作成したので、次の例ではソフトウェアで保護されたキーを使います。

どちらの保護モデルでも、VM が起動して仮想ディスクを復号化するときに、Azure プラットフォームは暗号化キーを要求するためのアクセスを許可される必要があります。 [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create) を使用して、Key Vault に暗号化キーを作成します。 次の例では、*myKey* という名前のキーを作成します。

```azurecli-interactive
az keyvault key create \
    --vault-name $keyvault_name \
    --name myKey \
    --protection software
```


## <a name="create-a-virtual-machine"></a>仮想マシンの作成
[az vm create](/cli/azure/vm#az-vm-create) を使用して VM を作成し、5 GB のデータ ディスクを接続します。 ディスクの暗号化をサポートしているのは、一部の Marketplace イメージだけです。 次の例は、*Ubuntu 16.04 LTS* イメージを使用して、*myVM* という名前の VM を作成します。

```azurecli-interactive
az vm create \
    --resource-group $resourcegroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

前のコマンドからの出力で示されている *publicIpAddress* を使用して VM に SSH 接続します。 パーティションとファイル システムを作成し、データ ディスクをマウントします。 詳細については、「[Linux VM を接続して新しいディスクをマウントする](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk)」をご覧ください。 SSH セッションを閉じます。


## <a name="encrypt-the-virtual-machine"></a>仮想マシンを暗号化する


[az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) を使用して VM を暗号化します。

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

ディスク暗号化プロセスが完了するまで少し時間がかかります。 [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) を使用して、プロセスの状態を監視します。

```azurecli-interactive
az vm encryption show --resource-group $resourcegroup --name myVM --query 'status'
```

完了すると、出力は次の例のようになります。

```json
[
  {
    "code": "ProvisioningState/succeeded",
    "displayStatus": "Provisioning succeeded",
    "level": "Info",
    "message": "Encryption succeeded for all volumes",
    "time": null
  }
]
```


## <a name="add-additional-data-disks"></a>新しいデータ ディスクを追加する
データ ディスクを暗号化したら、別の仮想ディスクを VM に追加して暗号化できます。 

データ ディスクが VM に追加されたら、次のように仮想ディスクを暗号化するコマンドを再実行します。

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type data
```


## <a name="next-steps"></a>次の手順
* 暗号化キーや Vault の削除など、Azure Key Vault の管理について詳しくは、「[CLI を使用した Key Vault の管理](../../key-vault/key-vault-manage-with-cli2.md)」をご覧ください。
* 暗号化されたカスタム VM を Azure にアップロードするための準備など、ディスクの暗号化について詳しくは、「[Azure Disk Encryption](../../security/azure-security-disk-encryption.md)」をご覧ください。
