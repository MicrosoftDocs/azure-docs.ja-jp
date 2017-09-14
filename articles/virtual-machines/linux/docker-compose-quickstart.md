---
title: "Azure の Linux VM で Docker Compose を使用する | Microsoft Docs"
description: "Linux 仮想マシンで Azure CLI を用いて、Docker と Compose を使用する方法"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 541722cb02dd991228726e62a2304b49cdd806f2
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Docker と Compose を使用して Azure 内で複数コンテナー アプリケーションを定義して実行する
[Compose](http://github.com/docker/compose) では、単純なテキスト ファイルを使用して、複数の Docker コンテナーで構成されるアプリケーションを定義します。 次に、定義された環境をデプロイするためのあらゆる操作を実行する単一のコマンドで、アプリケーションを起動します。 たとえば、この記事では、Ubuntu VM のバックエンド MariaDB SQL Database で WordPress ブログをすばやくセットアップする方法を示します。 Compose を使用してさらに複雑なアプリケーションをセットアップすることもできます。


## <a name="set-up-a-linux-vm-as-a-docker-host"></a>Docker ホストとしての Linux VM のセットアップ
Azure のさまざまな手順と Azure Marketplace で入手できるイメージまたは Resource Manager テンプレートを使用して、Linux VM を作成し、Docker ホストとしてセットアップできます。 たとえば、[クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)を使って Azure Docker VM 拡張機能で Ubuntu VM を簡単に作成する方法については、「[Docker VM 拡張機能を使用した環境のデプロイ](dockerextension.md)」をご覧ください。 

Docker VM 拡張機能を使用すると、VM が自動的に Docker ホストとしてセットアップされ、Compose が既にインストールされた状態になります。


### <a name="create-docker-host-with-azure-cli-20"></a>Azure CLI 2.0 で Docker ホストを作成する
最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/#login) を使用して Azure アカウントにログインします。

最初に、[az group create](/cli/azure/group#create) で Docker 環境のリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを場所 *westus* に作成します。

```azurecli
az group create --name myResourceGroup --location westus
```

次に、[az group deployment create](/cli/azure/group/deployment#create) を使用して VM をデプロイします。これには [GitHub の Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)の Azure Docker VM 拡張機能が含まれます。 *newStorageAccountName*、*adminUsername*、*adminPassword*、*dnsNameForPublicIP* に独自の値を指定します。

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

デプロイが完了するには数分かかります。 デプロイが完了したら、[次の手順に進み](#verify-that-compose-is-installed)、VM に SSH 接続します。 

プロンプトに制御を戻してバックグラウンドでデプロイを続行するには、上記のコマンドにオプションで `--no-wait` フラグを追加します。 この処理によって、デプロイが継続している数分の間に、CLI でその他の作業を実行できます。 [az vm show](/cli/azure/vm#show) で Docker ホストの詳細な状態を確認できます。 次の例では、*myResourceGroup* という名前のリソース グループ内にある *myDockerVM* という名前 (テンプレートによる既定の名前 - この名前は変更しないでください) の VM の状態を確認します。

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --query [provisioningState] \
    --output tsv
```

このコマンドが *Succeeded* を返すと、デプロイは完了しており、次の手順で VM に SSH 接続できます。


## <a name="verify-that-compose-is-installed"></a>Compose がインストールされていることを確認する
デプロイが完了したら、デプロイ時に指定した DNS 名を使用して、SSH で新しい Docker ホストに接続します。 `az vm show -g myResourceGroup -n myDockerVM -d --query [fqdns] -o tsv` を使うと、DNS 名など、VM の詳細を見ることができます。

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

VM に Compose がインストールされていることを確認するには、次のコマンドを実行します。

```bash
docker-compose --version
```

*docker-compose 1.6.2, build 4d72027* のような出力が表示されます。

> [!TIP]
> 別の方法で作成した Docker ホストに Compose を自分でインストールする必要がある場合は、 [Compose のドキュメント](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md)をご覧ください。


## <a name="create-a-docker-composeyml-configuration-file"></a>docker-compose.yml 構成ファイルの作成
次に、 `docker-compose.yml` というテキスト構成ファイルを作成して、VM 上で実行される Docker コンテナーを定義します。 このファイルでは、各コンテナーで実行するイメージ (イメージは Dockerfile からのビルドも使用できます)、必要な環境変数と依存関係、ポート、コンテナー間のリンクを指定します。 yml ファイルの構文の詳細については、 [Compose ファイルのリファレンス](https://docs.docker.com/compose/compose-file/)をご覧ください。

次のように *docker-compose.yml* ファイルを作成します。

```bash
touch docker-compose.yml
```

任意のテキスト エディターを使って何らかのデータをファイルに追加します。 次の例では、*vi* エディターを使います。

```bash
vi docker-compose.yml
```

次の例をテキスト ファイルに貼り付けます。 この構成では、 [DockerHub Registry](https://registry.hub.docker.com/_/wordpress/) から取得したイメージを使用して、WordPress (オープン ソースのブログ作成およびコンテンツ管理システム)、およびリンクされたバックエンド MariaDB SQL Database がインストールされます。 次のように独自の *MYSQL_ROOT_PASSWORD* を入力します。

```sh
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Compose によるコンテナーの起動
*docker-compose.yml* ファイルと同じディレクトリで、次のコマンドを実行します (環境によっては、`sudo` を実行するのに `docker-compose` が必要な場合があります)。

```bash
docker-compose up -d
```

このコマンドにより、*docker-compose.yml* で指定された Docker コンテナーが起動されます。 この手順は、完了までに 1 ～ 2 分かかります。 次の例のような出力が表示されます。

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> コンテナーがバックグラウンドで継続的に実行されるように、必ず起動時に **-d** オプションを使用してください。


コンテナーが起動していることを確認するために、「 `docker-compose ps`」と入力します。 次のような結果が表示されます。

```bash
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

ポート80 で VM 上の WordPress に直接接続できます。 Web ブラウザーを開き、VM の DNS 名を入力します (例: `http://mypublicdns.westus.cloudapp.azure.com`)。 WordPress スタート画面が表示されます。この画面の手順に従ってインストールを完了し、アプリケーションを使用できます。

![WordPress のスタート画面][wordpress_start]

## <a name="next-steps"></a>次のステップ
* Docker VM の Docker および Compose を構成するその他のオプションについては、[Docker VM 拡張機能のユーザー ガイド](https://github.com/Azure/azure-docker-extension/blob/master/README.md) をご覧ください。 たとえば、Docker VM 拡張機能の構成に直接 Compose yml ファイル (JSON に変換) を配置する方法があります。
* 複数コンテナー アプリのビルドとデプロイに関するその他の例については、[Compose コマンド ラインのリファレンス](http://docs.docker.com/compose/reference/)および[ユーザー ガイド](http://docs.docker.com/compose/)をご覧ください。
* 自分で用意するか [コミュニティ](https://azure.microsoft.com/documentation/templates/)から取得した Azure リソース マネージャー テンプレートを利用して、Docker を搭載した Azure VM や Compose を搭載したアプリケーション セットアップをデプロイできます。 たとえば、 [Deploy a WordPress blog with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) テンプレートは、Docker と Compose を使用して、Ubuntu VM に WordPress と MySQL バックエンドを迅速にデプロイします。
* Docker Compose と Docker Swarm クラスターを統合できます。 シナリオについては、「 [Using Compose with Swarm (Compose と Swarm の使用)](https://docs.docker.com/compose/swarm/) 」をご覧ください。

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png

