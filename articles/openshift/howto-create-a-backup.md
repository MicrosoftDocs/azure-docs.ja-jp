---
title: Velero を使用して Azure Red Hat OpenShift 4 クラスター アプリケーションのバックアップを作成する
description: Velero を使用して Azure Red Hat OpenShift クラスター アプリケーションのバックアップを作成する方法について説明します
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro、openshift、az aro、red hat、cli
ms.custom: mvc
ms.openlocfilehash: bbfe280ed0b1b562e0f50b23a09ea159750c4a79
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217093"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>Azure Red Hat OpenShift 4 クラスター アプリケーションのバックアップを作成する

この記事では、Azure Red Hat OpenShift 4 クラスター アプリケーションのバックアップを作成するための環境を準備します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 前提条件を設定し、必要なツールをインストールする
> * Azure Red Hat OpenShift 4 アプリケーションのバックアップを作成する

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.6.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

### <a name="install-velero"></a>Velero のインストール

システムに Velero を[インストールする](https://velero.io/docs/main/basic-install/)には、お使いのオペレーティング システムに推奨される手順に従います。

### <a name="set-up-azure-storage-account-and-blob-container"></a>Azure ストレージ アカウントと BLOB コンテナーの設定

この手順では、ARO クラスターのリソース グループ以外にリソース グループを作成します。  このリソース グループにより、バックアップを保持し、新しいクラスターにアプリケーションを復元できます。

```bash
AZURE_BACKUP_RESOURCE_GROUP=Velero_Backups
az group create -n $AZURE_BACKUP_RESOURCE_GROUP --location eastus

AZURE_STORAGE_ACCOUNT_ID="velero$(uuidgen | cut -d '-' -f5 | tr '[A-Z]' '[a-z]')"
az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_ID \
    --resource-group $AZURE_BACKUP_RESOURCE_GROUP \
    --sku Standard_GRS \
    --encryption-services blob \
    --https-only true \
    --kind BlobStorage \
    --access-tier Hot

BLOB_CONTAINER=velero
az storage container create -n $BLOB_CONTAINER --public-access off --account-name $AZURE_STORAGE_ACCOUNT_ID
```

## <a name="set-permissions-for-velero"></a>Velero のアクセス許可の設定

### <a name="create-service-principal"></a>サービス プリンシパルの作成

Velero では、バックアップと復元を行うためのアクセス許可が必要です。 サービス プリンシパルを作成する場合、前の手順で定義したリソース グループにアクセスするためのアクセス許可を Velero に付与します。 この手順では、クラスターのリソース グループを取得します。

```bash
export AZURE_RESOURCE_GROUP=$(az aro show --name <name of cluster> --resource-group <name of resource group> | jq -r .clusterProfile.resourceGroupId | cut -d '/' -f 5,5)
```


```bash
AZURE_SUBSCRIPTION_ID=$(az account list --query '[?isDefault].id' -o tsv)

AZURE_TENANT_ID=$(az account list --query '[?isDefault].tenantId' -o tsv)
```

```bash
AZURE_CLIENT_SECRET=$(az ad sp create-for-rbac --name "velero" --role "Contributor" --query 'password' -o tsv \
--scopes  /subscriptions/$AZURE_SUBSCRIPTION_ID)
AZURE_CLIENT_ID=$(az ad sp list --display-name "velero" --query '[0].appId' -o tsv)

```

```bash
cat << EOF  > ./credentials-velero.yaml
AZURE_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}
AZURE_TENANT_ID=${AZURE_TENANT_ID}
AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
AZURE_CLIENT_SECRET=${AZURE_CLIENT_SECRET}
AZURE_RESOURCE_GROUP=${AZURE_RESOURCE_GROUP}
AZURE_CLOUD_NAME=AzurePublicCloud
EOF
```

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>Azure Red Hat OpenShift 4 クラスターへの Velero のインストール

この手順では、Velero をその固有のプロジェクトにインストールし、Velero によってバックアップと復元を実行するために必要な[カスタム リソース定義](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/)をインストールします。 Azure Red Hat OpenShift v4 クラスターに正常にログインしていることを確認します。


```bash
velero install \
--provider azure \
--plugins velero/velero-plugin-for-microsoft-azure:v1.1.0 \
--bucket $BLOB_CONTAINER \
--secret-file ~/path/to/credentials-velero.yaml \
--backup-location-config resourceGroup=$AZURE_BACKUP_RESOURCE_GROUP,storageAccount=$AZURE_STORAGE_ACCOUNT_ID \
--snapshot-location-config apiTimeout=15m \
--velero-pod-cpu-limit="0" --velero-pod-mem-limit="0" \
--velero-pod-mem-request="0" --velero-pod-cpu-request="0"
```

## <a name="create-a-backup-with-velero"></a>Velero によるバックアップの作成

Velero によってアプリケーションのバックアップを作成するには、このアプリケーションが含まれている名前空間を含める必要があります。  `nginx-example` 名前空間があり、その名前空間内のすべてのリソースをバックアップに含める場合は、ターミナルで次のコマンドを実行します。

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
バックアップの状態を確認するには、次を実行します。

```bash
oc get backups -n velero <name of backup> -o yaml
```

バックアップが正常に実行されると、`phase:Completed` が出力され、オブジェクトがストレージ アカウントのコンテナー内に配置されます。

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>Velero を使用してスナップショットを含むバックアップを作成する

Velero を使用してアプリケーションの永続ボリュームを含むアプリケーション バックアップを作成するには、アプリケーションが存在する名前空間を含める他に、バックアップの作成時に `snapshot-volumes=true` フラグを含める必要があります

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

バックアップの状態を確認するには、次を実行します。

```bash
oc get backups -n velero <name of backup> -o yaml
```

バックアップが正常に実行されると、`phase:Completed` が出力され、オブジェクトがストレージ アカウントのコンテナー内に配置されます。

Velero を使用してバックアップと復元を作成する方法の詳細については、[ネイティブな方法による OpenShift リソースのバックアップ](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)に関するページを参照してください

## <a name="next-steps"></a>次の手順

この記事では、Azure Red Hat OpenShift 4 クラスター アプリケーションをバックアップしました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Velero を使用して OpenShift v4 クラスター アプリケーションのバックアップを作成する
> * Velero を使用してスナップショットを含む OpenShift v4 クラスター アプリケーションのバックアップを作成する


次の記事に進み、Azure Red Hat OpenShift 4 クラスター アプリケーションの復元を作成する方法を確認してください。

* [Azure Red Hat OpenShift 4 クラスター アプリケーションの復元を作成する](howto-create-a-restore.md)
* [スナップショットを含む Azure Red Hat OpenShift 4 クラスター アプリケーションの復元を作成する](howto-create-a-restore.md)
