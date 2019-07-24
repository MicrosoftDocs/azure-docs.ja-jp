---
title: Docker マシンを使用して Azure で Linux ホストを作成する | Microsoft Docs
description: Docker マシンを使用して、Azure で Docker ホストを作成する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: da0e393dc2ae0d93ecc49745a42ffac4669ed74b
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326308"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Docker マシンを使用して、Azure で Docker ホストを作成する方法
この記事では、[Docker マシン](https://docs.docker.com/machine/)を使用して、Azure で Docker ホストを作成する方法を詳しく説明します。 `docker-machine` コマンドを実行すると、Azure で Linux 仮想マシン (VM) が作成され、次に Docker がインストールされます。 これにより、同じローカル ツールとワークフローを使用して、Azure で Docker ホストを管理できます。 Windows 10 で Docker コンピューターを使用するには、Linux バッシュを使用する必要があります。

## <a name="create-vms-with-docker-machine"></a>Docker マシンで VM を作成する
まず、次のように、[az account show](/cli/azure/account) を使用して Azure サブスクリプション ID を取得します。

```azurecli
sub=$(az account show --query "id" -o tsv)
```

`docker-machine create` で *azure* をドライバーとして指定することによって、Azure に Docker ホスト VM を作成します。 詳細については、[Docker の Azure ドライバー ドキュメント](https://docs.docker.com/machine/drivers/azure/)を参照してください。

次の例では、"Standard D2 v2" プランに基づいて *myVM* という名前の VM と *azureuser* という名前のユーザー アカウントを作成し、ホスト VM でポート *80* を開きます。 画面の指示に従って Azure アカウントにログインし、リソースを作成および管理するアクセス許可を Docker マシンに付与します。

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_DS2_v2" \
    myvm
```

出力は次の例のようになります。

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvm) Completed machine pre-create checks.
Creating machine...
(myvm) Querying existing resource group.  name="docker-machine"
(myvm) Creating resource group.  name="docker-machine" location="westus"
(myvm) Configuring availability set.  name="docker-machine"
(myvm) Configuring network security group.  name="myvm-firewall" location="westus"
(myvm) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvm) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvm) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvm) Creating public IP address.  name="myvm-ip" static=false
(myvm) Creating network interface.  name="myvm-nic"
(myvm) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvm) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvm
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvm
```

## <a name="configure-your-docker-shell"></a>Docker シェルを構成する
Azure で Docker ホストに接続するには、適切な接続設定を定義します。 出力の最後に示されている、次のような、Docker ホストの接続情報を表示します。 

```bash
docker-machine env myvm
```

出力は次の例のようになります。

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvm)
```

接続設定を定義するには、提案された構成コマンド (`eval $(docker-machine env myvm)`) を実行することも、手動で環境変数を設定することもできます。 

## <a name="run-a-container"></a>コンテナーを実行する
実行中のコンテナーを確認するには、基本的な NGINX Web サーバーを実行します。 `docker run` を使用してコンテナーを作成し、次のように Web トラフィック用にポート 80 を公開します。

```bash
docker run -d -p 80:80 --restart=always nginx
```

出力は次の例のようになります。

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

`docker ps` を使用して、実行中のコンテナーを表示します。 次のサンプル出力は、ポート 80 を公開して実行中の NGINX コンテナーを示しています。

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>コンテナーをテストする
次のようにして、Docker ホストのパブリック IP アドレスを取得します。


```bash
docker-machine ip myvm
```

実行中のコンテナーを確認するには、Web ブラウザーを開き、前のコマンドの出力に示されているパブリック IP アドレスを入力します。

![実行中の ngnix コンテナー](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>次の手順
Docker Compose の使用例については、[Azure での Docker と Compose の使用](docker-compose-quickstart.md)に関するページをご覧ください。
