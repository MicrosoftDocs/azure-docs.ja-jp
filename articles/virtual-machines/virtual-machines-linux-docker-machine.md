---
title: "Docker マシンを使用して Azure で Linux ホストを作成する | Microsoft Docs"
description: "Docker マシンを利用し、Azure で Docker ホストを作成する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: b64bafe1a8572369aa7a7e6f0d8b924fc182cd53
ms.openlocfilehash: 9440474db7eb7e3ae082505f8c95cbcf8c285cdf


---
# <a name="use-docker-machine-with-the-azure-driver"></a>Docker マシンと Azure ドライバーを使用する
[Docker](https://www.docker.com/) では、VM ではなく Linux コンテナーを使用する仮想化が提供され、共有リソース上でアプリケーション データとコンピューティングを分離します。 このトピックでは、[Docker マシン](https://docs.docker.com/machine/)をいつどのように使用するかを説明します。 `docker-machine` コマンドでは、新しい Linux VM が Azure に作成され、Linux コンテナーの docker ホストとして有効化されます。

## <a name="create-vms-with-docker-machine"></a>Docker マシンで VM を作成する
ドライバー オプション (`-d`) の `azure` ドライバー引数とその他の引数を利用し、`docker-machine create` コマンドで Azure に Docker ホスト VM を作成します。 

次の例では既定値が利用されていますが、VM のポート 80 を開いてインターネットに接続し、nginx コンテナーをテストし、`ops` を SSH のログオン ユーザーに設定し、新しい VM `machine` を呼び出します。 

`docker-machine create --driver azure` と入力すると、オプションとその既定値が表示されます。[Docker Azure ドライバーのドキュメント](https://docs.docker.com/machine/drivers/azure/)も参照してください  (2 要素認証を有効にした場合、2 つ目の要素を使用した認証が求められます)。

```bash
docker-machine create -d azure \
  --azure-ssh-user ops \
  --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> \
  --azure-open-port 80 \
  machine
```

アカウントに&2; 要素認証を設定しているかどうかにもよりますが、出力は次のようになります。

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(machine) Microsoft Azure: To sign in, use a web browser to open the page https://aka.ms/devicelogin. Enter the code <code> to authenticate.
(machine) Completed machine pre-create checks.
Creating machine...
(machine) Querying existing resource group.  name="machine"
(machine) Creating resource group.  name="machine" location="eastus"
(machine) Configuring availability set.  name="docker-machine"
(machine) Configuring network security group.  name="machine-firewall" location="eastus"
(machine) Querying if virtual network already exists.  name="docker-machine-vnet" location="eastus"
(machine) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(machine) Creating public IP address.  name="machine-ip" static=false
(machine) Creating network interface.  name="machine-nic"
(machine) Creating storage account.  name="vhdsolksdjalkjlmgyg6" location="eastus"
(machine) Creating virtual machine.  name="machine" location="eastus" size="Standard_A2" username="ops" osImage="canonical:UbuntuServer:15.10:latest"
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
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env machine
```

## <a name="configure-your-docker-shell"></a>Docker シェルを構成する
次に、 `docker-machine env <VM name>` と入力すると、シェルの構成に必要なものが表示されます。 

```bash
docker-machine env machine
```

次のような環境情報が出力されます。 IP アドレスが割り当てられています。これは VM のテストで必要になります。

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://191.237.46.90:2376"
export DOCKER_CERT_PATH="/Users/rasquill/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env machine)
```

提案された構成コマンドを実行するか、環境変数を自分で設定できます。 

## <a name="run-a-container"></a>コンテナーを実行する
これで単純な Web サーバーを実行し、すべてが正常に動作しているかどうかをテストできます。 ここでは標準の nginx イメージを使用し、ポート 80 で待機することと、VM の再起動時にコンテナーも再起動することを指定します (`--restart=always`)。 

```bash
docker run -d -p 80:80 --restart=always nginx
```

出力は次のようになります。

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>コンテナーをテストする
`docker ps`を利用し、実行中のコンテナーを調べます。

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

実行中のコンテナーを確認します。`docker-machine ip <VM name>` と入力すると、IP アドレスが見つかります (`env` コマンドで見つけたアドレスを忘れた場合)。

![実行中の ngnix コンテナー](./media/virtual-machines-linux-docker-machine/nginxsuccess.png)

## <a name="next-steps"></a>次のステップ
興味があれば、[Docker VM Extension](virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) で同じ操作を試してください。Azure CLI または Azure Resource Manager のテンプレートを使用します。 

Docker を使用した作業の例については、[HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect の[デモ](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)の「[Working with Docker (Docker を使用した作業)](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker)」を参照してください。 HealthClinic.biz のデモに関連する他のクイック スタートについては、「 [Azure Developer Tools Quickstarts (Azure 開発者ツールのクイック スタート)](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)」を参照してください。




<!--HONumber=Feb17_HO3-->


