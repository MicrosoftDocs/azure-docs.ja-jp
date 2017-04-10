---
title: "Azure CLI 2.0 を使用して Linux VM に MongoDB をインストールする | Microsoft Docs"
description: "Azure CLI 2.0 を使用して Linux 仮想マシンに MongoDB をインストールして構成する方法について説明する"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/14/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 137d30ba26fd8b9bb433fb2c3a13906afa4efedb
ms.lasthandoff: 03/29/2017


---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Linux VM に MongoDB をインストールして構成する方法
[MongoDB](http://www.mongodb.org) は、高いパフォーマンスを特徴とし、広く普及しているオープン ソースの NoSQL データベースです。 この記事では、Azure CLI 2.0 を使用して Linux VM に MongoDB をインストールして構成する方法を説明します。 これらの手順は、[Azure CLI 1.0](virtual-machines-linux-install-mongodb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して実行することもできます。 次の方法が詳しくわかる例を示します。

* [基本的な MongoDB インスタンスを手動でインストールして構成する](#manually-install-and-configure-mongodb-on-a-vm)
* [Resource Manager テンプレートを使って基本的な MongoDB インスタンスを作成する](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Resource Manager テンプレートを使ってレプリカ セットを含む複雑な MongoDB シャード化クラスターを作成する](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>VM に MongoDB を手動でインストールして構成する
MongoDB では、Red Hat/CentOS、SUSE、Ubuntu、Debian などの Linux ディストリビューション用の[インストール手順が提供](https://docs.mongodb.com/manual/administration/install-on-linux/)されています。 次の例では、`~/.ssh/id_rsa.pub` に格納されている SSH キーを使って `CentOS` VM を作成します。 この環境を作成するには、最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしている必要があります。

[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `West US` の場所に作成します。

```azurecli
 az group create --name myResourceGroup --location westus
```

[az vm create](/cli/azure/vm#create) を実行して VM を作成します。 次の例では、SSH 公開キー認証を使用して `myVM` という名前の VM に `azureuser` という名前のユーザーを作成し、パブリック DNS エントリ `mypublicdns` を作成します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --public-ip-address-dns-name mypublicdns
```

VM のパブリック DNS アドレスを使用して VM にログオンします。 パブリック DNS アドレスを確認するには、[az vm show](/cli/azure/vm#show) を実行します。

```azurecli
az vm show -g myResourceGroup -n myVM -d --query [fqdns] -o tsv
```

独自のユーザー名とパブリック DNS アドレスを使用して、VM に SSH でアクセスします。

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

MongoDB のインストール ソースを追加するには、次のように `yum` リポジトリ ファイルを作成します。

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.2.repo
```

編集のために MongoDB リポジトリ ファイルを開きます。 次の行を追加します。

```sh
[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
```

次のように `yum` を使って MongoDB をインストールします。

```bash
sudo yum install -y mongodb-org
```

既定では、MongoDB にアクセスできない SELinux が CentOS イメージに適用されます。 ポリシー管理ツールをインストールし、既定の TCP ポート 27017 での MongoDB の動作を許可するように SELinux を構成します。 

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

MongoDB サービスを起動します。

```bash
sudo service mongod start
```

ローカル `mongo` クライアントを使って接続することにより、MongoDB のインストールを確認します。

```bash
mongo
```

データをいくつか追加して検索することにより、MongoDB インスタンスをテストします。

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

必要な場合は、システムの再起動時に自動的に起動するように MongoDB を構成します。

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>テンプレートを使って CentOS に基本的な MongoDB インスタンスを作成する
GitHub にある次の Azure クイックスタート テンプレートを使って、単一の CentOS VM に基本的な MongoDB インスタンスを作成できます。 このテンプレートは、Linux 用のカスタム スクリプト拡張機能を使って、新しく作成した CentOS VM に `yum` リポジトリを追加してから、MongoDB をインストールします。

* [CentOS 上の基本的な MongoDB インスタンス](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

この環境を作成するには、最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしている必要があります。 最初に、[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `West US` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westus
```

次に、[az group deployment create](/cli/azure/group/deployment#create) を実行して MongoDB テンプレートをデプロイします。 必要に応じて、独自のリソース名とサイズを定義します (例: `newStorageAccountName`、`virtualNetworkName`、`vmSize`)。

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"},
    "virtualNetworkName": {"value": "myVnet"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myVM"},
    "publicIPAddressName": {"value": "myPublicIP"},
    "nicName": {"value": "myNic"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

VM のパブリック DNS アドレスを使用して VM にログオンします。 パブリック DNS アドレスを確認するには、[az vm show](/cli/azure/vm#show) を実行します。

```azurecli
az vm show -g myResourceGroup -n myVM -d --query [fqdns] -o tsv
```

独自のユーザー名とパブリック DNS アドレスを使用して、VM に SSH でアクセスします。

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

ローカル `mongo` クライアントを使って接続することにより、MongoDB のインストールを確認します。

```bash
mongo
```

データをいくつか追加して検索することにより、インスタンスをテストします。

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>テンプレートを使って CentOS に複雑な MongoDB シャード化クラスターを作成する
GitHub にある次の Azure クイックスタート テンプレートを使って、複雑な MongoDB シャード化クラスターを作成できます。 このテンプレートは、[MongoDB シャード化クラスターのベスト プラクティス](https://docs.mongodb.com/manual/core/sharded-cluster-components/)に従って、冗長性と高可用性を提供します。 このテンプレートは、2 つのシャードと、各レプリカ セット内に 3 つのノードを作成します。 3 つのノードを含む 1 つの構成サーバー レプリカ セットも作成され、さらに 2 つの `mongos` ルーター サーバーが作成されてシャード全体のアプリケーションの一貫性が提供されます。

* [CentOS 上の MongoDB シャード化クラスター](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> この複雑な MongoDB シャード化クラスターをデプロイするには、20 個より多くのコアが必要です。サブスクリプションに対するリージョンごとの既定のコア数は、通常 20 個です。 コア数を増やすには、Azure サポート要求を提出してください。

この環境を作成するには、最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしている必要があります。 最初に、[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `West US` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westus
```

次に、[az group deployment create](/cli/azure/group/deployment#create) を実行して MongoDB テンプレートをデプロイします。 必要に応じて、独自のリソース名とサイズを定義します (例: `mongoAdminUsername`、`sizeOfDataDiskInGB`、`configNodeVmSize`)。

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster --no-wait
```

すべての VM インスタンスのデプロイと構成には 1 時間以上かかることがあります。 前のコマンドの最後で `--no-wait` フラグを使用しているので、Azure プラットフォームでテンプレートのデプロイが受け入れられたら、コマンド プロンプトの制御権が戻ります。 そこで [az group deployment show](/cli/azure/group/deployment#show) を実行してデプロイの状態を確認できます。 次の例では、`myResourceGroup` リソース グループの `myMongoDBCluster` デプロイメントの状態を表示します。

```azurecli
az group deployment show --resource-group myResourceGroup --name myMongoDBCluster \
    --query [properties.provisioningState] --output tsv
```

## <a name="next-steps"></a>次のステップ
この記事の例では、VM からローカルに MongoDB インスタンスに接続しました。 別の VM またはネットワークから MongoDB インスタンスに接続する場合は、適切な[ネットワーク セキュリティ グループ規則を作成する](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)必要があります。

テンプレートを使った作成の詳細については、「[Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md)」をご覧ください。

Azure Resource Manager テンプレートは、カスタム スクリプト拡張機能を使って、スクリプトをダウンロードして VM で実行します。 詳しくは、「[Azure カスタム スクリプト拡張機能と Linux 仮想マシンの使用](virtual-machines-linux-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。


