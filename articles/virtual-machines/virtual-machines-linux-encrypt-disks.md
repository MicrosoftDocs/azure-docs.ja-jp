---
title: "Azure で Linux VM のディスクを暗号化する | Microsoft Docs"
description: "セキュリティを強化するために、Azure CLI 2.0 を使用して Linux VM の仮想ディスクを暗号化する方法"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/23/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 10e6ba839130d05dc6b3c1c31681478209a73ed6
ms.lasthandoff: 03/27/2017


---
# <a name="how-to-encrypt-virtual-disks-on-a-linux-vm"></a>Linux VM の仮想ディスクを暗号化する方法
仮想マシン (VM) のセキュリティとコンプライアンスを強化するために、Azure の仮想ディスクを暗号化できます。 ディスクは、Azure Key Vault で保護されている暗号化キーを使って暗号化されます。 これらの暗号化キーを制御し、その使用を監査することができます。 この記事では、Azure CLI 2.0 を使用して Linux VM の仮想ディスクを暗号化する方法について詳しく説明します。 これらの手順は、[Azure CLI 1.0](virtual-machines-linux-encrypt-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して実行することもできます。

## <a name="quick-commands"></a>クイック コマンド
タスクをすばやく実行する必要がある場合のために、次のセクションでは、VM の仮想ディスクを暗号化するための基本的なコマンドの詳細について説明します。 詳細な情報と各手順のコンテキストが、ドキュメントの残りの部分に記載されています。[ここからお読みください](#overview-of-disk-encryption)。

最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) がインストールされ、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしている必要があります。 次の例では、パラメーター名を独自の値を置き換えます。 `myResourceGroup`、`myKey`、`myVM` などは、例として使われているパラメーター名です。

まず、[az provider register](/cli/azure/provider#register) を使用して Azure サブスクリプションで Azure Key Vault プロバイダーを有効にし、[az group create](/cli/azure/group#create) を使用してリソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `WestUS` の場所に作成します。

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location WestUS
```

[az keyvault create](/cli/azure/keyvault#create) を使用して Azure Key Vault を作成し、ディスクの暗号化で使用するために Key Vault を有効にします。 次のように、`keyvault_name` に一意の Key Vault 名を指定します。

```azurecli
keyvault_name=myUniqueKeyVaultName
az keyvault create --name $keyvault_name --resource-group myResourceGroup \
  --location WestUS --enabled-for-disk-encryption True
```

[az keyvault key create](/cli/azure/keyvault/key#create) を使用して、Key Vault に暗号化キーを作成します。 次の例では、`myKey` という名前のキーを作成します。

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```

[az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) を使用して、Azure Active Directory を使用するサービス プリンシパルを作成します。 サービス プリンシパルは、認証と Key Vault の暗号化キーの交換を処理します。 次の例では、この後のコマンドで使用するために、サービス プリンシパルの ID とパスワードの値を読み取ります。

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

パスワードは、サービス プリンシパルの作成時にのみ出力されます。 必要に応じて、パスワードを表示して記録しておきます (`echo $sp_password`)。 [az ad sp list](/cli/azure/ad/sp#list) を使用してサービス プリンシパルの一覧を表示できます。また、[az ad sp show](/cli/azure/ad/sp#show) を使用して、特定のサービス プリンシパルに関する追加情報を表示できます。

[az keyvault set-policy](/cli/azure/keyvault#set-policy) を使用して、Key Vault に対するアクセス許可を設定します。 次の例では、サービス プリンシパル ID が前のコマンドから提供されます。

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions all \
  --secret-permissions all
```

[az vm create](/cli/azure/vm#create) を使用して VM を作成し、5 GB のデータ ディスクを接続します。 ディスクの暗号化をサポートしているのは、一部の Marketplace イメージだけです。 次の例では、**CentOS 7.2n** イメージを使用して、`myVM` という名前の VM を作成します。

```azurecli
az vm create -g myResourceGroup -n myVM --image OpenLogic:CentOS:7.2n:7.2.20160629 \
  --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
  --data-disk-sizes-gb 5
```

VM に SSH 接続します。 パーティションとファイル システムを作成し、データ ディスクをマウントします。 詳細については、「[Linux VM を接続して新しいディスクをマウントする](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk)」をご覧ください。 SSH セッションを閉じます。

[az vm encryption enable](/cli/azure/vm/encryption#enable) を使用して VM を暗号化します。 次の例では、前の `ad sp create-for-rbac` コマンドの `$sp_id` 変数と `$sp_password` 変数を使用しています。

```azurecli
az vm encryption enable --resource-group myResourceGroup --name myVM \
  --aad-client-id $sp_id \
  --aad-client-secret $sp_password \
  --disk-encryption-keyvault $keyvault_name \
  --key-encryption-key myKey \
  --volume-type all
```

ディスク暗号化プロセスが完了するまで少し時間がかかります。 [az vm encryption show](/cli/azure/vm/encryption#show) を使用して、プロセスの状態を監視します。

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

状態が **EncryptionInProgress** になっています。 OS ディスクの状態が **VMRestartPending** になるまで待ち、[az vm restart](/cli/azure/vm#restart) を使用して VM を再起動します。

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

ディスク暗号化プロセスは起動プロセス中に最終処理が行われるので、数分待ってから **az vm encryption show** を使用して暗号化の状態をもう一度確認します。

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

 OS ディスクとデータ ディスクの状態がどちらも **Encrypted** になっています。

## <a name="overview-of-disk-encryption"></a>ディスク暗号化の概要
Linux VM の仮想ディスクは、[dm-crypt](https://wikipedia.org/wiki/Dm-crypt) を使って暗号化します。 Azure の仮想ディスクを暗号化するための料金はかかりません。 暗号化キーは、ソフトウェア保護を使って Azure Key Vault に格納されます。または、FIPS 140-2 レベル 2 標準に認定された Hardware Security Module (HSM) でキーをインポートまたは生成することもできます。 これらの暗号化キーの制御を維持し、その使用を監査することができます。 これらの暗号化キーは、VM に接続された仮想ディスクの暗号化/暗号化解除に使われます。 Azure Active Directory サービス プリンシパルは、VM の電源がオンまたはオフになったときにこれらの暗号化キーを発行するためのセキュリティで保護されたメカニズムを提供します。

VM 暗号化のプロセスは次のとおりです。

1. Azure Key Vault で暗号化キーを作成します。
2. ディスクの暗号化に使うことができるように暗号化キーを構成します。
3. Azure Key Vault から暗号化キーを読み取るには、適切なアクセス許可を持つ Azure Active Directory サービス プリンシパルを作成します。
4. Azure Active Directory サービス プリンシパルと、使用する適切な暗号化キーを指定して、仮想ディスクを暗号化するコマンドを発行します。
5. Azure Active Directory サービス プリンシパルは、Azure Key Vault に必要な暗号化キーを要求します。
6. 提供された暗号化キーを使って仮想ディスクが暗号化されます。

## <a name="encryption-process"></a>暗号化プロセス
ディスクの暗号化は、以下の他のコンポーネントに依存します。

* **Azure Key Vault** - ディスクの暗号化/暗号化解除プロセスに使われる暗号化キーとシークレットの保護に使われます。 
  * Azure Key Vault が既に存在する場合は、それを使うことができます。 Key Vault をディスク暗号化専用にする必要はありません。
  * 管理境界とキーの可視性を分離するため、専用の Key Vault を作成してもかまいません。
* **Azure Active Directory** - 必要な暗号化キーの安全な交換と、要求されたアクションの認証を処理します。 
  * 通常は、既存の Azure Active Directory インスタンスを使ってアプリケーションを保持できます。
  * サービス プリンシパルは、適切な暗号化キーを要求し、発行するセキュリティで保護されたメカニズムを提供します。 Azure Active Directory と統合する実際のアプリケーションを開発しているのではありません。

## <a name="requirements-and-limitations"></a>要件と制限
ディスク暗号化のサポートされるシナリオと要件:

* 次の Linux サーバー SKU - Ubuntu、CentOS、SUSE と SUSE Linux Enterprise Server (SLES)、Red Hat Enterprise Linux。
* すべてのリソース (Key Vault、ストレージ アカウント、VM など) は、同じ Azure リージョンとサブスクリプションに存在している必要があります。
* Standard A、D、DS、G、GS シリーズの VM。

現在、ディスクの暗号化は次のシナリオではサポートされていません。

* Basic レベルの VM。
* クラシック デプロイメント モデルで作成された VM。
* Linux VM での OS ディスク暗号化の無効化。
* 既に暗号化されている Linux VM での暗号化キーの更新。

## <a name="create-azure-key-vault-and-keys"></a>Azure Key Vault とキーを作成する
最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) がインストールされ、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしている必要があります。 コマンドの例全体を通して、パラメーターの例を実際の名前、場所、およびキーの値に置き換えます。 以下の例では、`myResourceGroup`、`myKeyVault`、`myAADApp` などの表記を使います。

コマンドの例全体を通して、パラメーターの例を実際の名前、場所、およびキーの値に置き換えます。 以下の例では、`myResourceGroup`、`myKey`、`myVM` などの表記を使います。

最初に、暗号化キーを格納する Azure Key Vault を作成します。 Azure Key Vault は、キー、シークレット、パスワードを格納して、アプリケーションとサービスに安全に実装できるようにします。 仮想ディスクの暗号化では、仮想ディスクの暗号化または暗号化解除に使われる暗号化キーを格納するために Key Vault を使います。 

[az provider register](/cli/azure/provider#register) を使用して Azure サブスクリプションで Azure Key Vault プロバイダーを有効にし、[az group create](/cli/azure/group#create) を使用してリソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `WestUS` の場所に作成します。

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location WestUS
```

暗号化キーおよびストレージや VM 自体などの関連するコンピューティング リソースを格納する Azure Key Vault は、同じリージョンに存在する必要があります。 [az keyvault create](/cli/azure/keyvault#create) を使用して Azure Key Vault を作成し、ディスクの暗号化で使用するために Key Vault を有効にします。 次のように、`keyvault_name` に一意の Key Vault 名を指定します。

```azurecli
keyvault_name=myUniqueKeyVaultName
az keyvault create --name $keyvault_name --resource-group myResourceGroup \
  --location WestUS --enabled-for-disk-encryption True
```

ソフトウェアまたはハードウェア セキュリティ モデル (HSM) の保護を使って、暗号化キーを格納できます。 HSM を使うには、Premium Key Vault が必要です。 ソフトウェアで保護されたキーを格納する Standard Key Vault ではなく Premium Key Vault を作成するには、追加コストがかかります。 Premium Key Vault を作成するには、前の手順で `--sku Premium` をコマンドに追加します。 ここでは Standard Key Vault を作成したので、次の例ではソフトウェアで保護されたキーを使います。 

どちらの保護モデルでも、VM が起動して仮想ディスクを復号化するときに、Azure プラットフォームは暗号化キーを要求するためのアクセスを許可される必要があります。 [az keyvault key create](/cli/azure/keyvault/key#create) を使用して、Key Vault に暗号化キーを作成します。 次の例では、`myKey` という名前のキーを作成します。

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-the-azure-active-directory-service-principal"></a>Azure Active Directory サービス プリンシパルを作成する
仮想ディスクを暗号化または暗号化解除するときは、認証と Key Vault の暗号化キーの交換を処理するアカウントを指定します。 このアカウント (Azure Active Directory サービス プリンシパル) を使用して、Azure プラットフォームは VM の代わりに適切な暗号化キーを要求できます。 サブスクリプションでは既定の Azure Active Directory インスタンスを使うことができますが、多くの場合、専用の Azure Active Directory ディレクトリが使われます。

[az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) を使用して、Azure Active Directory を使用するサービス プリンシパルを作成します。 次の例では、この後のコマンドで使用するために、サービス プリンシパルの ID とパスワードの値を読み取ります。

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

パスワードは、サービス プリンシパルの作成時にのみ表示されます。 必要に応じて、パスワードを表示して記録しておきます (`echo $sp_password`)。 [az ad sp list](/cli/azure/ad/sp#list) を使用してサービス プリンシパルの一覧を表示できます。また、[az ad sp show](/cli/azure/ad/sp#show) を使用して、特定のサービス プリンシパルに関する追加情報を表示できます。

仮想ディスクを正常に暗号化または暗号化解除するには、Key Vault に格納されている暗号化キーに対するアクセス許可を設定して、Azure Active Directory サービス プリンシパルにキーの読み取りを許可する必要があります。 [az keyvault set-policy](/cli/azure/keyvault#set-policy) を使用して、Key Vault に対するアクセス許可を設定します。 次の例では、サービス プリンシパル ID が前のコマンドから提供されます。

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions all \
  --secret-permissions all
```


## <a name="create-virtual-machine"></a>仮想マシンの作成
実際に仮想ディスクを暗号化するために、VM を作成し、データ ディスクを追加します。 [az vm create](/cli/azure/vm#create) を使用して、暗号化する VM を作成し、5 GB のデータ ディスクを接続します。 ディスクの暗号化をサポートしているのは、一部の Marketplace イメージだけです。 次の例では、**CentOS 7.2n** イメージを使用して、`myVM` という名前の VM を作成します。

```azurecli
az vm create -g myResourceGroup -n myVM --image OpenLogic:CentOS:7.2n:7.2.20160629 \
  --data-disk-sizes-gb 5
```

VM に SSH 接続して、パーティションとファイル システムを作成し、データ ディスクをマウントします。 詳細については、「[Linux VM を接続して新しいディスクをマウントする](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk)」をご覧ください。 SSH セッションを閉じます。


## <a name="encrypt-virtual-machine"></a>仮想マシンを暗号化する
仮想ディスクを暗号化するには、これまでのコンポーネントをすべてまとめます。

1. Azure Active Directory サービス プリンシパルとパスワードを指定します。
2. 暗号化されたディスクのメタデータを格納する Key Vault を指定します。
3. 実際の暗号化と暗号化解除に使う暗号化キーを指定します。
4. OS ディスク、データ ディスク、またはすべてのディスクのいずれを暗号化するかを指定します。

[az vm encryption enable](/cli/azure/vm/encryption#enable) を使用して VM を暗号化します。 次の例では、前の `ad sp create-for-rbac` コマンドの `$sp_id` 変数と `$sp_password` 変数を使用しています。

```azurecli
az vm encryption enable --resource-group myResourceGroup --name myVM \
  --aad-client-id $sp_id \
  --aad-client-secret $sp_password \
  --disk-encryption-keyvault $keyvault_name \
  --key-encryption-key myKey \
  --volume-type all
```

ディスク暗号化プロセスが完了するまで少し時間がかかります。 [az vm encryption show](/cli/azure/vm/encryption#show) を使用して、プロセスの状態を監視します。

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

出力は次の例のようになります (一部省略)。

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress",
]
```

OS ディスクの状態が **VMRestartPending** になるまで待ち、[az vm restart](/cli/azure/vm#restart) を使用して VM を再起動します。

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

ディスク暗号化プロセスは起動プロセス中に最終処理が行われるので、数分待ってから **az vm encryption show** を使用して暗号化の状態をもう一度確認します。

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

OS ディスクとデータ ディスクの状態がどちらも **Encrypted** になっています。


## <a name="add-additional-data-disks"></a>新しいデータ ディスクを追加する
データ ディスクを暗号化した後で、別の仮想ディスクを VM に追加して暗号化することもできます。 `az vm encryption enable` コマンドを実行するときに、`--sequence-version` パラメーターを使ってシーケンスのバージョンを増やします。 このシーケンス バージョン パラメーターを使うと、同じ VM に対して操作を繰り返し実行できます。

たとえば、次のようにして 2 番目の仮想ディスクを VM に追加できます。

```azurecli
az vm disk attach-new --resource-group myResourceGroup --vm-name myVM --size-in-gb 5
```

コマンドを再度実行して仮想ディスクを暗号化します。今度は、`--sequence-version` パラメーターを追加し、次のように最初の実行から値を増分します。

```azurecli
az vm encryption enable --resource-group myResourceGroup --name myVM \
  --aad-client-id $sp_id \
  --aad-client-secret $sp_password \
  --disk-encryption-keyvault $keyvault_name \
  --key-encryption-key myKey \
  --volume-type all \
  --sequence-version 2
```


## <a name="next-steps"></a>次のステップ
* 暗号化キーや Vault の削除など、Azure Key Vault の管理について詳しくは、「[CLI を使用した Key Vault の管理](../key-vault/key-vault-manage-with-cli.md)」をご覧ください。
* 暗号化されたカスタム VM を Azure にアップロードするための準備など、ディスクの暗号化について詳しくは、「[Azure Disk Encryption](../security/azure-security-disk-encryption.md)」をご覧ください。


