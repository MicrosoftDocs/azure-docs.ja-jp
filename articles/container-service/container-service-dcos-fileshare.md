---
title: "Azure DC/OS クラスターのファイル共有 | Microsoft Docs"
description: "Azure Container Service でファイル共有を作成し、DC/OS クラスターにマウントします"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Mesos, Azure, FileShare, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 6d40821327a9df47bb85ea12ecd33e4a0f49e39e
ms.lasthandoff: 03/22/2017


---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>ファイル共有を作成し、DC/OS クラスターにマウントする
この記事では、Azure でファイル共有を作成し、DC/OS クラスターの各エージェントとマスターにマウントする方法について説明します。 ファイル共有を設定すると、クラスター間で構成、アクセス、ログなどのファイルが共有しやすくなります。

この例での操作には、Azure Container Service で構成された DC/OS クラスターが必要です。 [Azure Container Service クラスターのデプロイ](container-service-deployment.md)に関するページをご覧ください。

## <a name="create-a-file-share-on-microsoft-azure"></a>Microsoft Azure でのファイル共有の作成
### <a name="using-the-portal"></a>ポータルの使用

1. ポータルにログインします。
2. ストレージ アカウントを作成します。
   
  ![Azure Container Service の [ストレージ アカウントの作成]](media/container-service-dcos-fileshare/createSA.png)

3. 作成されたら、**[サービス]** セクションで **[ファイル]** をクリックします。
   
  ![Azure Container Service の [ファイル] セクション](media/container-service-dcos-fileshare/filesServices.png)

4. **[+ ファイル共有]** をクリックし、この新しい共有の名前を入力します (**[クォータ]** は必須ではありません)。
   
  ![Azure Container Service の [+ ファイル共有]](media/container-service-dcos-fileshare/newFileShare.png)  

### <a name="using-azure-cli-20"></a>Azure CLI 2.0 の使用

必要な場合は、[Azure CLI をインストールして設定](/cli/azure/install-azure-cli.md)します。

```azurecli
################# Change these four parameters ##############
DCOS_PERS_STORAGE_ACCOUNT_NAME=anystorageaccountname
DCOS_PERS_RESOURCE_GROUP=AnyResourceGroupName
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=demoshare
#############################################################

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>クラスターでの共有のマウント

次に、cifs ツール/プロトコルを使用して、この共有を、クラスター内のすべての仮想マシンにマウントする必要があります。 これを行うには、コマンド ライン `mount -t cifs` を使用します。

ここでは次の例を使用します。
* ストレージ アカウント名 **`anystorageaccountname`**
* 架空のアカウント キー **`P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==`** 
* マウント ポイント **`/mnt/share/demoshare`**

```bash
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```

クラスターの仮想マシン (マスター ノードとエージェント ノード) でこのコマンドを実行します。 エージェント数が多い場合は、スクリプトを作成してこのプロセスを自動化することをお勧めします。  

### <a name="set-up-scripts"></a>スクリプトの設定

1. まず、DC/OS に基づくクラスターのマスター (または最初のマスター) に SSH 接続します。 たとえば、`ssh userName@masterFQDN –A –p 22` を使用します。ここで、masterFQDN は、マスター VM の完全修飾ドメイン名です。

2. 秘密キーをマスターの作業ディレクトリ (~) にコピーします。

3. コマンド `chmod 600 yourPrivateKeyFile` を使用して、そのアクセス許可を変更します。

4. `ssh-add yourPrivateKeyFile` コマンドを使用して、秘密キーをインポートします。 最初に動作しない場合は、`eval ssh-agent -s` を実行しなければならないことがあります。

5. vi、nano、vim など、任意のエディターを使用して、マスターから次の 2 つのファイルを作成します。 
  
  * **cifsMount.sh** ファイル。各 VM で実行するスクリプトが含まれます 
  * **mountShares.sh** ファイル。すべての SSH 接続を開始するためのファイルで、最初のスクリプトを呼び出します


```bash
# cifsMount.sh

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```
  
```bash
# mountShares.sh

# Install jq used for the next command
sudo apt-get install jq

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share on the current vm (master)
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes
  
# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
  do
    ssh `whoami`@$line -o StrictHostKeyChecking=no -i yourPrivateKeyFile < ./cifsMount.sh
    done
```  
> [!IMPORTANT]
> **mount** コマンドは、ストレージ アカウント名、パスワードなど、独自の設定で変更する必要があります。
>  

このスクリプトを作成したフォルダーには、現在次の 3 つのファイルがあります。  

* **cifsMount.sh**
* **mountShares.sh**
* **yourPrivateKeyFile** 

### <a name="run-the-scripts"></a>スクリプトの実行

`sh mountShares.sh` コマンドで **mountShares.sh** ファイルを実行します。

端末に結果が出力されます。 スクリプトの完了後、クラスターのファイル共有を使用できます。

スクリプトは最適化できますが、この例はシンプルで、その目的はわかりやすくガイダンスを提供することです。

> [!NOTE] 
> この方法は、高い IOPS を必要とするシナリオではお勧めしませんが、クラスター間でドキュメントや情報を共有するときに使用すると非常に便利です。
>

## <a name="next-steps"></a>次のステップ
* [DC/OS コンテナーの管理](container-service-mesos-marathon-ui.md)で詳細を確認します。
* [Marathon REST API](container-service-mesos-marathon-rest.md) を使用して DC/OS コンテナーを管理します。
