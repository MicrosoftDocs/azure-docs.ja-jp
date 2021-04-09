---
title: Azure Storage へのデータ流出を制限する - Azure CLI
description: この記事では、Azure CLI を使って仮想ネットワーク サービス エンドポイント ポリシーで Azure Storage リソースへの仮想ネットワーク データ流出を制限する方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 9ce1e320a93a834a938ce95f3931d885d2214faa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98216871"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Azure CLI を使って仮想ネットワーク サービス エンドポイント ポリシーで Azure Storage アカウントへのデータの流出を管理する

仮想ネットワーク サービス エンドポイント ポリシーを使用すると、サービス エンドポイント経由で仮想ネットワーク内から Azure Storage アカウントにアクセス制御を適用できます。 これは、ワークロードのセキュリティ保護のために必要であり、どのストレージ アカウントが許可され、どのようなデータ流出が許可されるかが管理されます。
この記事では、次のことについて説明します。

* 仮想ネットワークを作成し、サブネットを追加します。
* Azure Storage のサービス エンドポイントを有効にします。
* 2 つの Azure Storage アカウントを作成し、上記で作成したサブネットからのネットワーク アクセスを許可します。
* いずれかのストレージ アカウントへのアクセスのみを許可するサービス エンドポイント ポリシーを作成します。
* サブネットに仮想マシン (VM) をデプロイします。
* サブネットから許可されているストレージ アカウントへのアクセスを確認します。
* サブネットから許可されていないストレージ アカウントへのアクセスが拒否されていることを確認します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

仮想ネットワークを作成する前に、仮想ネットワークのリソース グループと、この記事で作成された他のすべてのリソースを作成する必要があります。 [az group create](/cli/azure/group) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

[az network vnet create](/cli/azure/network/vnet) で、1 つのサブネットを含む仮想ネットワークを作成します。

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>サービス エンドポイントを有効にする 

次の例では、*Microsoft.Storage* のサービス エンドポイントをサブネット *Private* に作成します。 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>サブネットのネットワーク アクセスを制限する

[az network nsg create](/cli/azure/network/nsg) で、ネットワーク セキュリティ グループを作成します。 次の例では、*myNsgPrivate* という名前のネットワーク セキュリティ グループを作成します。

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

[az network vnet subnet update](/cli/azure/network/vnet/subnet) を使って、ネットワーク セキュリティ グループを *Private* サブネットに関連付けます。 次の例では、*myNsgPrivate* ネットワーク セキュリティ グループを *Private* サブネットに関連付けます。

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

[az network nsg rule create](/cli/azure/network/nsg/rule) でセキュリティ規則を作成します。 次の規則は、Azure Storage サービスに割り当てられたパブリック IP アドレスへの送信アクセスを許可します。 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

各ネットワーク セキュリティ グループには、さまざまな[既定のセキュリティ規則](./network-security-groups-overview.md#default-security-rules)が含まれています。 次の規則は、すべてのパブリック IP アドレスへの送信アクセスを許可する既定のセキュリティ規則をオーバーライドします。 `destination-address-prefix "Internet"` オプションは、すべてのパブリック IP アドレスへの送信アクセスを拒否します。 この規則は、優先度が高い前の規則によってオーバーライドされます。これにより、Azure Storage のパブリック IP アドレスへのアクセスが許可されます。

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

次の規則は、任意の場所からサブネットへの SSH 受信トラフィックを許可します。 この規則は、インターネットからのすべての受信トラフィックを拒否する既定のセキュリティ規則をオーバーライドします。 後のステップで接続をテストできるように、サブネットへの SSH が許可されます。

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Azure Storage アカウントに対するネットワーク アクセスを制限する

このセクションでは、サービス エンドポイントを介して仮想ネットワーク内の特定のサブネットから Azure Storage アカウントのネットワーク アクセスを制限する手順を示します。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[az storage account create](/cli/azure/storage/account) で Azure ストレージ アカウントを 2 つ作成します。

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

ストレージ アカウントを作成した後、[az storage account show-connection-string](/cli/azure/storage/account) を使って、ストレージ アカウントの接続文字列を変数に取得します。 接続文字列は、後の手順でファイル共有を作成するときに使います。

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>変数の内容を表示し、出力で返された **AccountKey** の値を書き留めます。後の手順でこの値を使います。

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>ストレージ アカウントにファイル共有を作成する

[az storage share create](/cli/azure/storage/share) を使って、ストレージ アカウントにファイル共有を作成します。 後の手順では、このファイル共有をマウントして、それへのネットワーク アクセスを確認します。

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>ストレージ アカウントへのすべてのネットワーク アクセスを拒否する

既定では、ストレージ アカウントは、任意のネットワーク上のクライアントからのネットワーク接続を受け入れます。 選んだネットワークへのアクセスを制限するには、[az storage account update](/cli/azure/storage/account) で既定のアクションを *Deny* に変更します。 ネットワーク アクセスが拒否されると、ストレージ アカウントには、どのネットワークからもアクセスできなくなります。

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>仮想ネットワーク サブネットからのネットワーク アクセスを有効にする

[az storage account network-rule add](/cli/azure/storage/account/network-rule) を使って、*Private* サブネットからストレージ アカウントへのネットワーク アクセスを許可します。

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>有効なストレージ アカウントへのアクセスを許可するポリシーを適用する

Azure サービス エンドポイント ポリシーは、Azure Storage でのみ使用できます。 そのため、この例のセットアップでは、このサブネット上の *Microsoft.Storage* のサービス エンドポイントを有効にします。

サービス エンドポイント ポリシーは、サービス エンドポイントに適用されます。 まず、サービス エンドポイント ポリシーを作成します。 次に、このサブネットに対して承認される Azure Storage アカウントのポリシー定義をこのポリシーのもとで作成します

サービス エンドポイント ポリシーを作成する

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

許可されているストレージ アカウントのリソース URI を変数に保存します。 以下のコマンドを実行する前に、 *\<your-subscription-id>* を、お使いのサブスクリプション ID の実際の値に置き換えます。

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

上記の Azure Storage アカウントを許可するためのポリシー定義を作成し、サービス エンドポイント ポリシーに追加します。

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

仮想ネットワーク サブネットを更新して、前の手順で作成したサービス エンドポイント ポリシーに関連付けます。

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Azure Storage アカウントへのアクセス制限を検証する

### <a name="create-the-virtual-machine"></a>仮想マシンの作成

ストレージ アカウントへのネットワーク アクセスをテストするには、サブネットに VM をデプロイします。

[az vm create](/cli/azure/vm) を使用して、*Private* サブネット内に VM を作成します。 既定のキーの場所にまだ SSH キーが存在しない場合は、コマンドを使って SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

VM の作成には数分かかります。 作成された後、返された出力の **publicIpAddress** を書き留めておきます。 このアドレスは、後の手順で、インターネットから VM にアクセスするときに使います。

### <a name="confirm-access-to-storage-account"></a>ストレージ アカウントへのアクセスを確認する

*myVmPrivate* VM に SSH で接続します。 *\<publicIpAddress>* を *myVmPrivate* VM のパブリック IP アドレスに置き換えます。

```bash 
ssh <publicIpAddress>
```

マウント ポイントのフォルダーを作成します。

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

作成したディレクトリに Azure のファイル共有をマウントします。 以下のコマンドを実行する前に、 *\<storage-account-key>* を **$saConnectionString1** の *AccountKey* の値に置き換えます。

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

`user@myVmPrivate:~$` というプロンプトが表示されます。 Azure ファイル共有は */mnt/MyAzureFileShare* に正常にマウントされました。

### <a name="confirm-access-is-denied-to-storage-account"></a>ストレージ アカウントへのアクセスが拒否されたことを確認する

同じ VM *myVmPrivate* から、マウント ポイントのディレクトリを作成します。

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

ストレージ アカウント *notallowedstorageacc* から、作成したディレクトリに Azure ファイル共有をマウントしてみます。 この記事では、最新バージョンの Ubuntu を展開してあるものとします。 以前のバージョンの Ubuntu を使っている場合は、「[Linux でのマウント](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」でファイル共有のマウントに関する追加説明を参照してください。 

以下のコマンドを実行する前に、 *\<storage-account-key>* を **$saConnectionString2** の *AccountKey* の値に置き換えます。

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

アクセスが拒否され、`mount error(13): Permission denied` エラーが発生します。このストレージ アカウントは、サブネットに適用したサービス エンドポイント ポリシーの許可リストに含まれていないためです。 

*myVmPublic* VM への SSH セッションを終了します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、[az group delete](/cli/azure) を使用して、リソース グループとそのグループに含まれているすべてのリソースを削除します。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次のステップ

この記事では、Azure 仮想ネットワーク サービス エンドポイントのサービス エンドポイント ポリシーを Azure Storage に適用しました。 Azure Storage アカウントを作成し、ネットワーク アクセスを、仮想ネットワーク サブネットの特定のストレージ アカウントだけに制限 (つまり、他は拒否) しました。 サービス エンドポイント ポリシーの詳細については、[サービス エンドポイント ポリシーの概要](virtual-network-service-endpoint-policies-overview.md)に関する記事を参照してください。