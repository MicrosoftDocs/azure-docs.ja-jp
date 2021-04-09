---
title: Azure Red Hat OpenShift 4 で Azure Files StorageClass を作成する
description: Azure Red Hat OpenShift で Azure Files StorageClass を作成する方法を確認する
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 10/16/2020
author: grantomation
ms.author: b-grodel
keywords: aro、openshift、az aro、red hat、cli、azure file
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 039aa3cce6615e71960db810ae383d22d7bcd909
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212979"
---
# <a name="create-an-azure-files-storageclass-on-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 4 で Azure Files StorageClass を作成する

この記事では、Azure Files を使用して ReadWriteMany (RWX) ストレージを動的にプロビジョニングする Azure Red Hat OpenShift 4 用の StorageClass を作成します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 前提条件を設定し、必要なツールをインストールする
> * Azure Files プロビジョナーを使用して Azure Red Hat OpenShift 4 StorageClass を作成する

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.6.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

Azure Red Hat OpenShift 4 クラスターをお使いのサブスクリプションにデプロイします。「[Azure Red Hat OpenShift 4 クラスターを作成する](tutorial-create-cluster.md)」を参照してください


### <a name="set-up-azure-storage-account"></a>Azure ストレージ アカウントを設定する

この手順では、Azure Red Hat OpenShift (ARO) クラスターのリソース グループ以外にリソース グループを作成します。 このリソース グループには、Azure Red Hat OpenShift の動的なプロビジョナーによって作成される Azure Files 共有が格納されます。

```bash
AZURE_FILES_RESOURCE_GROUP=aro_azure_files
LOCATION=eastus

az group create -l $LOCATION -n $AZURE_FILES_RESOURCE_GROUP

AZURE_STORAGE_ACCOUNT_NAME=aroazurefilessa

az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_NAME \
    --resource-group $AZURE_FILES_RESOURCE_GROUP \
    --kind StorageV2 \
    --sku Standard_LRS
```

## <a name="set-permissions"></a>アクセス許可を設定する
### <a name="set-resource-group-permissions"></a>リソース グループのアクセス許可を設定する

ARO サービス プリンシパルには、新しい Azure ストレージ アカウントのリソース グループに対する 'listKeys' アクセス許可が必要です。 これを実現するには、'共同作成者' ロールを割り当てます。

```bash
ARO_RESOURCE_GROUP=aro-rg
CLUSTER=cluster
ARO_SERVICE_PRINCIPAL_ID=$(az aro show -g $ARO_RESOURCE_GROUP -n $CLUSTER --query servicePrincipalProfile.clientId -o tsv)

az role assignment create --role Contributor --assignee $ARO_SERVICE_PRINCIPAL_ID -g $AZURE_FILES_RESOURCE_GROUP
```

### <a name="set-aro-cluster-permissions"></a>ARO クラスターのアクセス許可を設定する

OpenShift 永続ボリュームのバインダー サービス アカウントには、シークレットを読み取る機能が必要です。 これを実現するには、OpenShift クラスターのロールを作成して割り当てます。
```bash
ARO_API_SERVER=$(az aro list --query "[?contains(name,'$CLUSTER')].[apiserverProfile.url]" -o tsv)

oc login -u kubeadmin -p $(az aro list-credentials -g $ARO_RESOURCE_GROUP -n $CLUSTER --query=kubeadminPassword -o tsv) $APISERVER

oc create clusterrole azure-secret-reader \
    --verb=create,get \
    --resource=secrets

oc adm policy add-cluster-role-to-user azure-secret-reader system:serviceaccount:kube-system:persistent-volume-binder
```

## <a name="create-storageclass-with-azure-files-provisioner"></a>Azure Files プロビジョナーを使用して StorageClass を作成する

この手順では、Azure Files プロビジョナーを使用して StorageClass を作成します。 ARO クラスターが現在のリソース グループの外部にあるストレージ アカウントを認識できるようにするため、StorageClass マニフェストにストレージ アカウントの詳細を記述する必要があります。

ストレージのプロビジョニング中に、マウント資格情報用に secretName という名前のシークレットが作成されます。 マルチテナントのコンテキストでは、secretNamespace の値を明示的に設定することを強くお勧めします。そうしないと、ストレージ アカウントの資格情報が他のユーザーによって読み取られる可能性があります。

```bash
cat << EOF >> azure-storageclass-azure-file.yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azure-file
provisioner: kubernetes.io/azure-file
parameters:
  location: $LOCATION
  secretNamespace: kube-system
  skuName: Standard_LRS
  storageAccount: $AZURE_STORAGE_ACCOUNT_NAME
  resourceGroup: $AZURE_FILES_RESOURCE_GROUP
reclaimPolicy: Delete
volumeBindingMode: Immediate
EOF

oc create -f azure-storageclass-azure-file.yaml
```

## <a name="change-the-default-storageclass-optional"></a>既定の StorageClass を変更する (省略可能)

ARO の既定の StorageClass は、マネージド premium と呼ばれ、azure-disk プロビジョナーを使用します。 これを変更するには、StorageClass マニフェストに対して patch コマンドを発行します。

```bash
oc patch storageclass managed-premium -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

oc patch storageclass azure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## <a name="verify-azure-file-storageclass-optional"></a>Azure Files StorageClass を検証する (省略可能)

新しいアプリケーションを作成し、それにストレージを登録します。

```bash
oc new-project azfiletest
oc new-app -template httpd-example

#Wait for the pod to become Ready
curl $(oc get route httpd-example -n azfiletest -o jsonpath={.spec.host})

oc set volume dc/httpd-example --add --name=v1 -t pvc --claim-size=1G -m /data

#Wait for the new deployment to rollout
export POD=$(oc get pods --field-selector=status.phase==Running -o jsonpath={.items[].metadata.name})
oc exec $POD -- bash -c "mkdir ./data"
oc exec $POD -- bash -c "echo 'azure file storage' >> /data/test.txt"

oc exec $POD -- bash -c "cat /data/test.txt"
azure file storage
```
test.txt ファイルは、Azure portal の Storage Explorer でも表示されます。

## <a name="next-steps"></a>次のステップ

この記事では、Microsoft Azure Files と Azure Red Hat OpenShift 4 を使用して、動的な永続ストレージを作成しました。 以下の方法を学習しました。

> [!div class="checklist"]
> * ストレージ アカウントを作成する
> * Azure Files プロビジョナーを使用して Azure Red Hat OpenShift 4 クラスターに StorageClass を作成する

次の記事に進んで、Azure Red Hat OpenShift 4 でサポートされているリソースについて確認してください。

* [Azure Red Hat OpenShift のサポート ポリシー](support-policies-v4.md)
