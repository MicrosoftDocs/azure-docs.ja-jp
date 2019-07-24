---
title: (非推奨) Azure DC/OS クラスターのファイル共有
description: Azure Container Service でファイル共有を作成し、DC/OS クラスターにマウントします
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: e6651fc5988a1e1830807219cda02ab057db9a4f
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329822"
---
# <a name="deprecated-create-and-mount-a-file-share-to-a-dcos-cluster"></a>(非推奨) ファイル共有を作成して DC/OS クラスターにマウントする

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

このチュートリアルでは、Azure でファイル共有を作成し、DC/OS クラスターの各エージェントとマスターにマウントする方法について説明します。 ファイル共有を設定すると、クラスター間で構成、アクセス、ログなどのファイルが共有しやすくなります。 このチュートリアルでは、次のタスクを実行します。

> [!div class="checklist"]
> * Azure のストレージ アカウントの作成
> * ファイル共有を作成する
> * DC/OS クラスターに共有をマウントする

このチュートリアルの手順を実行するには、ACS DC/OS クラスターが必要です。 必要に応じて、[このサンプル スクリプト](./../kubernetes/scripts/container-service-cli-deploy-dcos.md)で作成できます。

このチュートリアルには、Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 アップグレードする必要がある場合は、「[Azure CLI のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Microsoft Azure でのファイル共有の作成

ACS DC/OS クラスターで Azure ファイル共有を使用するには、事前にストレージ アカウントとファイル共有を作成しておく必要があります。 次のスクリプトを実行して、ストレージおよびファイル共有を作成します。 お客様の環境で、該当するパラメーターを更新します。

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>クラスターでの共有のマウント

次に、ファイル共有をクラスター内のすべての仮想マシンにマウントする必要があります。 このタスクは、cifs ツール/プロトコルを使用して実行されます。 マウント操作は、クラスターの各ノード上で手動で行うことも、クラスター内で各ノードに対してスクリプトを実行して行うこともできます。

以下の例では、2 つのスクリプトを実行します。1 つは Azure ファイル共有をマウントするスクリプトです。もう 1 つは DC/OS クラスターの各ノードでこのマウント用スクリプトを実行するスクリプトです。

最初に、Azure ストレージ アカウント名とアクセス キーが必要です。 この情報の取得には、次のコマンドを実行します。 これらの値は、後の手順で使用するのでそれぞれメモしておきます。

ストレージ アカウント名: 

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

ストレージ アカウント アクセス キー: 

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

次に、DC/OS マスターの FQDN を取得し、それを変数に格納します。

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

マスター ノードに秘密キーをコピーします。 このキーは、クラスター内のすべてのノードとの ssh 接続を作成するのに必要となります。 クラスターの作成時に既定値ではない値が使用されている場合は、ユーザー名を更新します。 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

DC/OS に基づくクラスターのマスター (または最初のマスター) との SSH 接続を作成します。 クラスターの作成時に既定値ではない値が使用されている場合は、ユーザー名を更新します。

```azurecli-interactive
ssh azureuser@$FQDN
```

**cifsMount.sh** という名前のファイルを作成し、このファイルに次の内容をコピーします。 

Azure ファイル共有をマウントするには、次のスクリプトを使用します。 これまでに収集した情報を使用して、`STORAGE_ACCT_NAME` 変数と `ACCESS_KEY` 変数を更新します。

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
2 つ目のファイルとして **getNodesRunScript.sh** という名前のファイルを作成し、このファイルに次の内容をコピーします。 

このスクリプトは、すべてのクラスター ノードを検出し、**cifsMount.sh** スクリプトを実行して各クラスター ノードにファイル共有をマウントします。

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

クラスターのすべてのノードに Azure ファイル共有をマウントするスクリプトを実行します。

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

これで、クラスターの各ノードにある `/mnt/share/dcosshare` にアクセスできるようになりました。

## <a name="next-steps"></a>次の手順

このチュートリアルは、次の手順を使用して、Azure ファイル共有を DC/OS クラスターで利用できるようにしました。

> [!div class="checklist"]
> * Azure のストレージ アカウントの作成
> * ファイル共有を作成する
> * DC/OS クラスターに共有をマウントする

次のチュートリアルでは、Azure での Azure Container Registry と DC/OS の統合について学習します。  

> [!div class="nextstepaction"]
> [アプリケーションを負荷分散する](container-service-dcos-acr.md)
