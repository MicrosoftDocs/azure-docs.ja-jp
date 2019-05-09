---
title: Azure の Linux VM で Docker Compose を使用する | Microsoft Docs
description: Linux 仮想マシンで Azure CLI を用いて、Docker と Compose をインストールして使用する方法
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 03501ea774cf58a4be88ed9155e5cfdfb99f0379
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58007194"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Docker と Compose を使用して Azure 内で複数コンテナー アプリケーションを定義して実行する
[Compose](https://github.com/docker/compose) では、単純なテキスト ファイルを使用して、複数の Docker コンテナーで構成されるアプリケーションを定義します。 次に、定義された環境をデプロイするためのあらゆる操作を実行する単一のコマンドで、アプリケーションを起動します。 たとえば、この記事では、Ubuntu VM のバックエンド MariaDB SQL Database で WordPress ブログをすばやくセットアップする方法を示します。 Compose を使用してさらに複雑なアプリケーションをセットアップすることもできます。

この記事は、2019 年 2 月 14 日に [Azure Cloud Shell](https://shell.azure.com/bash) と [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) バージョン 2.0.58 を使用して最後にテストされました。

## <a name="create-docker-host-with-azure-cli"></a>Azure CLI で Docker ホストを作成する
最新の [Azure CLI](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/reference-index) を使用して Azure アカウントにログインします。

最初に、[az group create](/cli/azure/group) で Docker 環境のリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

*cloud-init.txt* というファイルを作成し、次の構成を貼り付けます。 `sensible-editor cloud-init.txt` を入力し、ファイルを作成して使用可能なエディターの一覧を確認します。 

```yaml
#include https://get.docker.com
```

ここで [az vm create](/cli/azure/vm#az-vm-create) を使用して VM を作成します。 `--custom-data` パラメーターを使用して、cloud-init 構成ファイルを渡します。 現在の作業ディレクトリの外部に構成ファイル *cloud-init.txt* を保存していた場合には、このファイルの完全パスを指定します。 次の例では、*myDockerVM* という VM を作成し、Web トラフィックに対してポート 80 を開きます。

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

VM が作成され、パッケージがインストールされて、アプリが開始されるには、数分かかります。 Azure CLI がプロンプトに戻った後にも引き続き実行するバック グラウンド タスクがあります。 VM が作成されたら、Azure CLI によって表示される `publicIpAddress` をメモしてください。 

                 

## <a name="install-compose"></a>Compose のインストール


新しい Docker ホスト VM に SSH 接続します。 実際の IP アドレスを入力します。

```bash
ssh azureuser@10.10.111.11
```

VM に Compose をインストールします。

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>docker-compose.yml 構成ファイルの作成
`docker-compose.yml` 構成ファイルを作成して、VM 上で実行される Docker コンテナーを定義します。 このファイルでは、各コンテナーで実行するイメージ、必要な環境変数と依存関係、ポート、コンテナー間のリンクを指定します。 yml ファイルの構文の詳細については、 [Compose ファイルのリファレンス](https://docs.docker.com/compose/compose-file/)をご覧ください。

*docker-compose.yml* ファイルを作成します。 任意のテキスト エディターを使って何らかのデータをファイルに追加します。 次の例では、`sensible-editor` で目的のエディターを選択するためのメッセージを含むファイルを作成します。

```bash
sensible-editor docker-compose.yml
```

次の例を Docker Compose ファイルに貼り付けます。 この構成では、 [DockerHub Registry](https://registry.hub.docker.com/_/wordpress/) から取得したイメージを使用して、WordPress (オープン ソースのブログ作成およびコンテンツ管理システム)、およびリンクされたバックエンド MariaDB SQL Database がインストールされます。 実際の *MYSQL_ROOT_PASSWORD* を入力します。

```yml
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
sudo docker-compose up -d
```

このコマンドにより、*docker-compose.yml* で指定された Docker コンテナーが起動されます。 この手順は、完了までに 1 ～ 2 分かかります。 次のような出力が表示されます。

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


コンテナーが起動していることを確認するために、「 `sudo docker-compose ps`」と入力します。 次のような結果が表示されます。

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

ポート80 で VM 上の WordPress に直接接続できます。 Web ブラウザーを開き、VM の IP アドレス名を入力します。 WordPress スタート画面が表示されます。この画面の手順に従ってインストールを完了し、アプリケーションを使用できます。

![WordPress のスタート画面](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>次の手順
* 複数コンテナー アプリのビルドとデプロイに関するその他の例については、[Compose コマンド ラインのリファレンス](https://docs.docker.com/compose/reference/)および[ユーザー ガイド](https://docs.docker.com/compose/)をご覧ください。
* 自分で用意するか [コミュニティ](https://azure.microsoft.com/documentation/templates/)から取得した Azure リソース マネージャー テンプレートを利用して、Docker を搭載した Azure VM や Compose を搭載したアプリケーション セットアップをデプロイできます。 たとえば、 [Deploy a WordPress blog with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) テンプレートは、Docker と Compose を使用して、Ubuntu VM に WordPress と MySQL バックエンドを迅速にデプロイします。
* Docker Compose と Docker Swarm クラスターを統合できます。 シナリオについては、「 [Using Compose with Swarm (Compose と Swarm の使用)](https://docs.docker.com/compose/swarm/) 」をご覧ください。

