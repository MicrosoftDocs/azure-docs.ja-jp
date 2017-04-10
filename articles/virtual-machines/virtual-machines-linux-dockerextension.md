---
title: "Azure Docker VM 拡張機能を使用する | Microsoft Docs"
description: "Docker VM 拡張機能を使用して、Resource Manager テンプレートと Azure CLI 2.0 を使って Azure で Docker 環境をすばやく安全にデプロイする方法を説明する"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: ad69080d2a6a39653291ca42b4aa182090b5bfd9
ms.lasthandoff: 03/29/2017


---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Docker VM 拡張機能を使用して Azure に Docker 環境を作成する
Docker は一般的なコンテナー管理とイメージング プラットフォームで、Linux 上のコンテナーを簡単に操作できます。 Azure では、ニーズに応じた様々な方法で Docker をデプロイできます。 この記事では、Azure CLI 2.0 での Docker VM 拡張機能と Azure Resource Manager テンプレートの使用について説明します。 これらの手順は、[Azure CLI 1.0](virtual-machines-linux-dockerextension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して実行することもできます。

## <a name="azure-docker-vm-extension-overview"></a>Azure Docker VM 拡張機能の概要
Azure Docker VM 拡張機能では、Linux 仮想マシン (VM) に Docker デーモン、Docker クライアント、Docker Compose をインストールして構成します。 Azure Docker VM 拡張機能を使うと、単に Docker マシンを使ったり、自分で Docker ホストを作成するより、管理と機能の範囲が広がります。 [Docker Compose](https://docs.docker.com/compose/overview/) などの追加機能により、Azure Docker VM 拡張機能はより堅牢な開発環境または運用環境に適したものになっています。

Docker マシンや Azure Container Service などの他のデプロイ方法について詳しくは、以下の記事をご覧ください。

* アプリのプロトタイプを短時間で作成するには、[Docker マシン](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を使って単一の Docker ホストを作成できます。
* 追加のスケジュール設定および管理ツールを提供する実稼働レベルのスケーラブルな環境を作成するには、[Azure Container Service に Docker Swarm クラスター](../container-service/container-service-deployment.md)をデプロイできます。

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Azure Docker VM 拡張機能を使ってテンプレートをデプロイする
Azure Docker VM 拡張機能を使って Docker ホストをインストールして構成する Ubuntu VM を、既存のクイックスタート テンプレートを使って作成します。 テンプレートは、「[Simple deployment of an Ubuntu VM with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)」(Docker を使用した Ubuntu VM の簡単なデプロイ) で確認できます。 最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) がインストールされ、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしている必要があります。

最初に、[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `West US` の場所に作成します。

```azurecli
 az group create --name myResourceGroup --location westus
```

次に、[az group deployment create](/cli/azure/group/deployment#create) を使用して VM をデプロイします。これには [GitHub の Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)の Azure Docker VM 拡張機能が含まれます。 `newStorageAccountName`、`adminUsername`、`adminPassword`、`dnsNameForPublicIP` に対して該当する値を次のように入力します。

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

デプロイが完了するには数分かかります。 デプロイが完了したら、[次の手順に進み](#deploy-your-first-nginx-container)、VM に SSH 接続します。 

プロンプトに制御を戻してバックグラウンドでデプロイを続行するには、上記のコマンドにオプションで `--no-wait` フラグを追加します。 この処理によって、デプロイが継続している数分の間に、CLI でその他の作業を実行できます。 [az vm show](/cli/azure/vm#show) で Docker ホストの詳細な状態を確認できます。 次の例では、`myResourceGroup` という名前のリソース グループ内にある `myDockerVM` という名前 (テンプレートによる既定の名前 - この名前は変更しないでください) の VM の状態を確認します。

```azurecli
az vm show --resource-group myResourceGroup --name myDockerVM \
  --query [provisioningState] --output tsv
```

このコマンドが `Succeeded` を返すと、デプロイは完了しており、次の手順で VM に SSH 接続できます。

## <a name="deploy-your-first-nginx-container"></a>最初の nginx コンテナーのデプロイ
DNS 名など、VM の詳細を表示するには、`az vm show -g myResourceGroup -n myDockerVM -d --query [fqdns] -o tsv` を使用します。 次のように、ローカル コンピューターから新しい Docker ホストに SSH 接続します。

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
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

動作中のコンテナーを確認するには、Web ブラウザーを開き、Docker ホストの DNS 名を入力します。

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


