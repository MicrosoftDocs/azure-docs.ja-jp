---
title: Azure Red Hat OpenShift (ARO) でカスタマー マネージド キー (CMK) を使用して永続ボリューム要求を暗号化する
description: Azure Red Hat OpenShift の Bring Your Own Key (BYOK)/カスタマー マネージド キー (CMK) のデプロイ手順
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: encryption、byok、aro、cmk、openshift、red hat
ms.openlocfilehash: fa84096dcc44e668a6cf7ebd0369c6d3631c28d2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555620"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Azure Red Hat OpenShift (ARO) でカスタマー マネージド キー (CMK) を使用して永続ボリューム要求を暗号化する (プレビュー)

Azure Storage では、サーバー側暗号化 (SSE) を使用して、クラウドに永続化されるときにデータを自動的に[暗号化](../storage/common/storage-service-encryption.md)します。 既定では、データは Microsoft プラットフォームのマネージド キーで暗号化されます。 暗号化キーをさらに制御するために、独自のカスタマー マネージド キーを指定して、Azure Red Hat OpenShift クラスターのデータを暗号化することができます。

> [!NOTE]
> この段階では、カスタマー マネージド キーを使用した ARO 永続ボリュームの暗号化のみがサポートされています。 この機能は、現在、マスターまたはワーカー ノードのオペレーティング システム ディスクでは使用できません。

> [!IMPORTANT]
> ARO のプレビュー機能は、セルフサービスのオプトイン単位で利用できます。 プレビューは、"現状有姿のまま" および "利用可能な限度" で提供され、サービス レベル アグリーメントおよび限定保証から除外されるものとします。 ARO プレビューは、ベストエフォート ベースでカスタマー サポートによって部分的にカバーされます。 そのため、これらの機能は、運用環境での使用を意図していません。

## <a name="before-you-begin"></a>始める前に
この記事では、以下のことを前提としています。

* OpenShift バージョン 4.4 (またはそれ以上) の ARO クラスターが既に存在している。

* **oc** OpenShift コマンドライン ツール、base64 (coreutils の一部)、および **az** Azure CLI がインストールされている。

* グローバル クラスター管理者ユーザー (kubeadmin) として **oc** を使用して ARO クラスターにログインしている。

* ARO クラスターと同じサブスクリプションで "共同作成者" アクセス権を付与する権限があるアカウントで、**az** を使用して Azure CLI にログインしている。

## <a name="limitations"></a>制限事項

* カスタマー マネージド キー暗号化の可用性は、リージョンによって異なります。 特定の Azure リージョンの状態を確認するには、[Azure リージョン][supported-regions]にっ関するページを確認してください。
* Ultra Disks を使用する場合は、使用を開始する前に、まずサブスクリプションでそれらを有効にする必要があります。

## <a name="declare-cluster--encryption-variables"></a>クラスターおよび暗号化変数を宣言する
以下の変数は、カスタマー マネージド暗号化キーを有効にする ARO クラスターに適しているものに構成する必要があります。
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>お使いのサブスクリプション ID を取得する
Azure サブスクリプション ID は、CMK の構成で複数回使用されます。 それを取得し、変数として格納します。
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Azure Key Vault インスタンスを作成する
キーを格納するには、Azure Key Vault インスタンスを使用する必要があります。 消去保護が有効になっている新しいキー コンテナーを作成します。 次に、独自のカスタム キーを格納するための新しいキーをコンテナー内に作成します。

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table

# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Azure ディスク暗号化セットを作成する

Azure Disk Encryption セットは、ARO のディスクの参照ポイントとして使用されます。 これは前の手順で作成した Azure Key Vault に接続され、その場所からカスタマー マネージド キーをプルします。

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>ディスク暗号化セットに Key Vault へのアクセス権を付与する
前の手順で作成したディスク暗号化セットを使用し、ディスク暗号化セットへのアクセス権を Azure Key Vault に付与します。

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>ロールの割り当てに必要なその他の ID を取得する
ARO クラスターでディスク暗号化セットを使用して、ARO クラスターの永続的ボリューム要求 (PVC) を暗号化できるようにする必要があります。 これを行うには、新しいマネージド サービス ID (MSI) を作成します。 さらに、ARO MSI およびディスク暗号化セットに対するその他のアクセス許可も設定します。

```azurecli-interactive
# First, get the Azure Application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $buildRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity Azure Application ID.
aroMSIAppId="$(az identity show -n $msiName -g $buildRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
buildRGResourceId="$(az group show -n $buildRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>CMK 暗号化に必要なその他のロールの割り当てを実装する
前の手順で取得した変数を使用して、必要なロールの割り当てを適用します。

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>暗号化された Premium および Ultra Disks 用の k8s ストレージ クラスを作成する
Premium_LRS および UltraSSD_LRS ディスク用の CMK に使用するストレージ クラスを生成します。

```azurecli-interactive
# Premium Disks
cat > managed-premium-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```

次に、このデプロイを ARO クラスターで実行して、ストレージ クラスの構成を適用します。

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>カスタマー マネージド キーを使用して暗号化をテストする (省略可能)
お使いのクラスターで PVC 暗号化にカスタマー マネージド キーを使用しているかどうかを確認するには、新しいストレージ クラスを使用して永続ボリューム要求を作成します。 次のコード スニペットは、ポッドを作成し、Premium ディスクを使用して永続ボリューム要求をマウントします。
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-cmk-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-cmk
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-cmk-encryption
spec:
  containers:
  - name: mypod-with-cmk-encryption
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: mypod-with-cmk-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file-optional"></a>テスト ポッド構成ファイルを適用する (省略可能)
次のコマンドを実行して、テスト ポッド構成を適用し、新しい永続ボリューム要求の UID を返します。 この UID は、ディスクが CMK を使用して暗号化されていることを確認するために使用されます。
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> Azure Active Directory 内でロールの割り当てを適用すると、わずかな遅延が発生する場合があります。 この手順の実行速度によっては、"完全な Azure ディスク名を決定する" ためのコマンドが失敗する場合があります。 この問題が発生した場合は、**oc describe pvc mypod-with-cmk-encryption-pvc** の出力を確認して、ディスクが正常にプロビジョニングされたことを確認します。 ロールの割り当ての伝達が完了していない場合は、ポッドおよび PVC YAML の "*削除*" と "*再適用*" が必要になることがあります。

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>PVC ディスクが "EncryptionAtRestWithCustomerKey" で構成されていることを確認する (省略可能)
ポッドでは、CMK ストレージ クラスを参照する永続ボリューム要求を作成する必要があります。 次のコマンドを実行すると、PVC が想定どおりに展開されているかどうかが検証されます。
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions

