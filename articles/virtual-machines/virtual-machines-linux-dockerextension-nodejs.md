---
title: "Azure CLI 1.0 で Azure Docker VM 拡張機能を使用する | Microsoft Docs"
description: "Docker VM 拡張機能を使用して、Resource Manager テンプレートを使って Azure で Docker 環境をすばやく安全にデプロイする方法を説明します。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: a6230ae2e9b22655988cd25e5f3660bf1bc214d7
ms.lasthandoff: 03/21/2017


---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension-with-the-azure-cli-10"></a>Azure CLI 1.0 で Docker VM 拡張機能を使用して Azure に Docker 環境を作成する
Docker は一般的なコンテナー管理およびイメージング プラットフォームで、Linux (および Windows) 上のコンテナーを簡単に操作できます。 Azure では、ニーズに応じた様々な方法で Docker をデプロイできます。 この記事では、Docker VM 拡張機能と Azure Resource Manager テンプレートの使用について説明します。 

Docker マシンや Azure Container Service などの他のデプロイ方法について詳しくは、以下の記事をご覧ください。

* アプリのプロトタイプを短時間で作成するには、[Docker マシン](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を使って単一の Docker ホストを作成できます。
* さらに大きくて安定した環境の場合は、Azure Docker VM 拡張機能を使うことができます。この拡張機能は、一貫したコンテナー デプロイメントを生成するための [Docker Compose](https://docs.docker.com/compose/overview/) もサポートします。 この記事では、Azure Docker VM 拡張機能の使用を詳しく説明します。
* 追加のスケジュール設定および管理ツールを提供する実稼働レベルのスケーラブルな環境を作成するには、[Azure Container Service に Docker Swarm クラスター](../container-service/container-service-deployment.md)をデプロイできます。

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](#azure-docker-vm-extension-overview) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0](virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - Resource Manager デプロイ モデル用の次世代 CLI 

## <a name="azure-docker-vm-extension-overview"></a>Azure Docker VM 拡張機能の概要
Azure Docker VM 拡張機能では、Linux 仮想マシン (VM) に Docker デーモン、Docker クライアント、Docker Compose をインストールして構成します。 Azure Docker VM 拡張機能を使うと、単に Docker マシンを使ったり、自分で Docker ホストを作成するより、管理と機能の範囲が広がります。 [Docker Compose](https://docs.docker.com/compose/overview/) などの追加機能により、Azure Docker VM 拡張機能はより堅牢な開発環境または運用環境に適したものになっています。

Azure Resource Manager テンプレートは、環境の全体構造を定義します。 テンプレートを使うと、Docker ホスト VM、記憶域、役割に基づくアクセス制御 (RBAC)、診断などのリソースを作成して構成できます。 テンプレートを再利用することで、一貫した方法で追加のデプロイを作成できます。 Azure Resource Manager とテンプレートについて詳しくは、「[Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」をご覧ください。 

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Azure Docker VM 拡張機能を使ってテンプレートをデプロイする
Azure Docker VM 拡張機能を使って Docker ホストをインストールして構成する Ubuntu VM を、既存のクイックスタート テンプレートを使って作成します。 テンプレートは、「[Simple deployment of an Ubuntu VM with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)」(Docker を使用した Ubuntu VM の簡単なデプロイ) で確認できます。 

[最新の Azure CLI](../cli-install-nodejs.md) をインストールし、次のように Resource Manager モードを使ってログインする必要があります。

```azurecli
azure config mode arm
```

テンプレートの URI を指定し、Azure CLI を使ってテンプレートをデプロイします。 次の例では、`myResourceGroup` という名前のリソース グループを `WestUS` の場所に作成します。 次のように、独自のリソース グループ名と場所を使います。

```azurecli
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

メッセージに従って、ストレージ アカウントの名前、ユーザー名とパスワード、および DNS 名を入力します。 出力は次の例のようになります。

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicip
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Azure CLI は数秒でプロンプトに戻りますが、Azure Docker VM 拡張機能による Docker ホストの作成と構成はまだ行われています。 デプロイが完了するには数分かかります。 `azure vm show` コマンドを使うと、Docker ホストの詳細な状態を確認できます。

次の例では、`myResourceGroup` という名前のリソース グループ内にある `myDockerVM` という名前 (テンプレートによる既定の名前 - この名前は変更しないでください) の VM の状態を確認します。 前の手順で作成したリソース グループの名前を入力します。

```azurecli
azure vm show -g myResourceGroup -n myDockerVM
```

`azure vm show` コマンドの出力は次の例のようになります。

```azurecli
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicip.westus.cloudapp.azure.com]
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

出力の先頭付近に VM の `ProvisioningState` が表示されます。 ここで " `Succeeded`" と表示されている場合デプロイは完了しており、VM に SSH で接続できます。

出力の最後の方にある `FQDN` に、Docker ホストの完全修飾ドメイン名が表示されます。 この FQDN を、後の手順で Docker ホストに SSH 接続するときに使います。

## <a name="deploy-your-first-nginx-container"></a>最初の nginx コンテナーのデプロイ
デプロイが完了したら、ローカル コンピューターから新しい Docker ホストに SSH 接続します。 次のように、独自のユーザー名と FQDN を入力します。

```bash
ssh ops@mypublicip.westus.cloudapp.azure.com
```

Docker ホストにログインした後、nginx コンテナーを実行します。

```bash
sudo docker run -d -p 80:80 nginx
```

nginx イメージがダウンロードされてコンテナーが起動され、出力は次の例のようになります。

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Docker ホストで実行しているコンテナーの状態を、次のようにして確認します。

```bash
sudo docker ps
```

出力は次の例のようになり、nginx コンテナーが実行していて、TCP ポート 80 と 443 が転送されていることが示されます。

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

動作中のコンテナーを確認するには、Web ブラウザーを開き、Docker ホストの FQDN 名を入力します。

![実行中の ngnix コンテナー](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Azure Docker VM 拡張機能テンプレート リファレンス
前の例では、既存のクイックスタート テンプレートを使いました。 独自の Resource Manager テンプレートを使って Azure Docker VM 拡張機能をデプロイすることもできます。 そのためには、次の記述を Resource Manager テンプレートに追加して、VM の `vmName` を適切に定義します。

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Resource Manager テンプレートの詳しい使い方は、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
[Docker デーモン TCP ポートを構成](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket)したり、[Docker セキュリティ](https://docs.docker.com/engine/security/security/)について理解したり、[Docker Compose](https://docs.docker.com/compose/overview/) を使ってコンテナーをデプロイすることができます。 Azure Docker VM 拡張機能自体の詳細については、「[GitHub プロジェクト](https://github.com/Azure/azure-docker-extension/)」をご覧ください。

Azure での Docker のデプロイの他のオプションについて詳しくは、以下をご覧ください。

* [Docker マシンと Azure ドライバーを使用する](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  
* [Docker と Compose を使用して Azure 仮想マシン上で複数コンテナー アプリケーションを定義して実行する](virtual-machines-linux-docker-compose-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure コンテナー サービス クラスターのデプロイ](../container-service/container-service-deployment.md)


