---
title: Azure Kubernetes Service (AKS) でカスタマーマネージド キーを使用して Azure ディスクを暗号化する
description: 独自のキー (BYOK) を使用して AKS OS ディスクとデータ ディスクを暗号化します。
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 4b1c311132cc812ccb2bbbc95c4b7414b108008c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499205"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) の Azure ディスクに独自のキー (BYOK) を使用する

Azure Storage は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 規定では、データは Microsoft のマネージド キーで暗号化されます。 暗号化キーの制御を強化するために、AKS クラスターの OS ディスクとデータ ディスクの両方の暗号化に使用する目的でカスタマーマネージド キーを提供できます。 [Linux][customer-managed-keys-linux] と [Windows][customer-managed-keys-windows] のカスタマー マネージド キーについて学習してください。

## <a name="limitations"></a>制限事項
* データ ディスク暗号化サポートは、Kubernetes バージョン 1.17 以上を実行している AKS クラスターに制限されます。
* カスタマー マネージド キーによる OS とデータ ディスクの暗号化は、AKS クラスターの作成時にのみ、有効にできます。

## <a name="prerequisites"></a>前提条件
* Key Vault を使用してマネージド ディスクを暗号化する場合、*Azure Key Vault* の論理的な削除と消去保護を有効にする必要があります。
* Azure CLI バージョン 2.11.1 以降を必要とします。

## <a name="create-an-azure-key-vault-instance"></a>Azure Key Vault インスタンスを作成する

キーを格納するには、Azure Key Vault インスタンスを使用します。  必要に応じて、Azure portal を使用して、[Azure Key Vault でカスタマーマネージド キーを構成][byok-azure-portal]できます

新しい "*リソース グループ*" を作成します。次に、新しい *Key Vault* インスタンスを作成し、論理的な削除と消去保護を有効にします。  各コマンドで同じリージョン名とリソース グループ名を使用するようにしてください。

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>DiskEncryptionSet のインスタンスを作成する

*myKeyVaultName* は、実際のキー コンテナーの名前に置き換えます。  また、次の手順を完了するには、Azure Key Vault に格納されている "*キー*" も必要です。  前の手順で作成したキー コンテナーに既存のキーを格納するか、[新しいキーを生成][key-vault-generate]します。次の *myKeyName* は、実際のキーの名前に置き換えます。
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query "[id]" -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query "[key.kid]" -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>DiskEncryptionSet にキー コンテナーへのアクセス権を付与する

前の手順で作成した DiskEncryptionSet とリソース グループを使用し、DiskEncryptionSet リソースに Azure Key Vault へのアクセス権を付与します。

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query "[identity.principalId]" -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>新しい AKS クラスターを作成し、OS ディスクを暗号化する

**新しいリソース グループ** と AKS クラスターを作成し、独自のキーを使用して OS ディスクを暗号化します。 カスタマー マネージド キーは、1.17 以降の Kubernetes バージョンでのみサポートされます。 

> [!IMPORTANT]
> AKS クラスターには必ず新しいリソース グループを作成してください

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query "[id]" -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

上の手順で作成したクラスターに新しいノード プールが追加されると、作成時に提供されたカスタマーマネージド キーが OS ディスクの暗号化に使用されます。

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>AKS クラスターのデータ ディスクを暗号化する (省略可能)
V 1.17.2 以降では、データ ディスクにキーが指定されていない場合、OS ディスク暗号化キーがデータ ディスクの暗号化に使用されます。また、AKS データ ディスクを他のキーで暗号化することもできます。

> [!IMPORTANT]
> 適切な AKS 資格情報を持っていることを確認します。 マネージド ID は、diskencryptionset がデプロイされているリソース グループへの共同作成者アクセス権を保持している必要があります。 そうしないと、マネージド ID にアクセス許可がないことを示すエラーが表示されます。

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

次の情報を含む **byok-azure-disk.yaml** というファイルを作成します。  myAzureSubscriptionId、myResourceGroup、myDiskEncrptionSetName を実際の値に置き換え、yaml を適用します。  必ず、DiskEncryptionSet がデプロイされているリソース グループを使用してください。  Azure Cloud Shell を使用する場合は、仮想または物理のシステムで作業するときと同じように、vi または nano を使用してこのファイルを作成できます。

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
次に、AKS クラスターでこのデプロイを実行します。
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>次のステップ

[AKS クラスターのセキュリティに関するベスト プラクティス][best-practices-security]を確認する

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions