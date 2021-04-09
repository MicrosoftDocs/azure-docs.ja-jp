---
title: Packer を使用して Linux Azure VM のイメージを作成する
description: Packer を使用して Azure に Linux 仮想マシンのイメージを作成する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.collection: linux
ms.openlocfilehash: 4d85106cb78b5f4799a78d18463d83594f54dbac
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556742"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Packer を使用して Azure に Linux 仮想マシンのイメージを作成する方法
Azure の各仮想マシン (VM) は、Linux ディストリビューションと OS のバージョンを定義するイメージから作成されます。 イメージには、プリインストールされているアプリケーションと構成を含めることができます。 Azure Marketplace には、ほとんどのディストリビューションおよびアプリケーション環境用の自社製およびサード パーティ製のイメージが数多く用意されています。また、ニーズに合わせて独自のイメージを作成することもできます。 この記事では、オープン ソース ツール [Packer](https://www.packer.io/) を使用して Azure に独自のイメージを定義およびビルドする方法について、詳しく説明します。

> [!NOTE]
> Azure では、お客様独自のカスタム イメージを定義して作成できるサービス (Azure Image Builder (プレビュー)) が提供されるようになっています。 Azure Image Builder は Packer が基になっているため、既存の Packer シェル プロビジョナー スクリプトを使うこともできます。 Azure Image Builder の概要については、「[Azure Image Builder で Linux VM を作成する](image-builder.md)」をご覧ください。


## <a name="create-azure-resource-group"></a>Azure リソース グループを作成する
ビルド プロセス中、Packer はソース VM をビルドする際に一時的な Azure リソースを作成します。 イメージとして使用するためにそのソース VM をキャプチャするには、リソース グループを定義する必要があります。 Packer のビルド プロセスからの出力は、このリソース グループに格納されます。

[az group create](/cli/azure/group) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Azure 資格情報の作成
Packer はサービス プリンシパルを使用して Azure で認証されます。 Azure のサービス プリンシパルは、アプリケーション、サービス、および Packer などのオートメーション ツールで使用できるセキュリティ ID です。 Azure でサービス プリンシパルが実行できる操作を設定するアクセス許可の制御と定義を行います。

[az ad sp create-for-rbac](/cli/azure/ad/sp) でサービス プリンシパルを作成し、Packer が必要とする資格情報を出力します。

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

上記のコマンドの出力例は、次のとおりです。

```output
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Azure に対して認証するには、[az account show](/cli/azure/account) で Azure サブスクリプション ID を取得する必要もあります。

```azurecli
az account show --query "{ subscription_id: id }"
```

次の手順で、これら 2 つのコマンドからの出力を使用します。


## <a name="define-packer-template"></a>Packer テンプレートを定義する
イメージをビルドするには、テンプレートを JSON ファイルとして作成します。 テンプレートでは、実際のビルド プロセスを実行するビルダーとプロビジョナーを定義します。 Packer には [Azure 用のプロビジョナー](https://www.packer.io/docs/builders/azure.html)が用意されており、前の手順で作成したサービス プリンシパルの資格情報などの Azure リソースを定義できます。

*ubuntu.json* というファイルを作成し、次のコンテンツを貼り付けます。 次に対応する実際の値を入力します。

| パラメーター                           | 入手場所 |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | `az ad sp` 作成コマンドの出力の最初の行 - *appId* |
| *client_secret*                     | `az ad sp` 作成コマンドの出力の 2 つ目の行 - *password* |
| *tenant_id*                         | `az ad sp` 作成コマンドの出力の 3 つ目の行 - *tenant* |
| *subscription_id*                   | `az account show` コマンドからの出力 |
| *managed_image_resource_group_name* | 最初の手順で作成したリソース グループの名前 |
| *managed_image_name*                | 作成されるマネージド ディスク イメージの名前 |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

このテンプレートでは、Ubuntu 16.04 LTS イメージをビルドし、NGINX をインストールした後、VM をプロビジョニング解除します。

> [!NOTE]
> このテンプレートでユーザーの資格情報を展開する場合は、Azure エージェントをプロビジョニング解除するプロビジョナー コマンドを `deprovision+user` ではなく `-deprovision` を読み取るように調整します。
> `+user` フラグは、ソース VM からすべてのユーザー アカウントを削除します。


## <a name="build-packer-image"></a>Packer イメージをビルドする
まだローカル コンピューターに Packer がインストールされていない場合は、[手順に従って Packer をインストールしてください](https://www.packer.io/docs/install)。

次のように Packer テンプレート ファイルを指定してイメージをビルドします。

```bash
./packer build ubuntu.json
```

上記のコマンドの出力例は、次のとおりです。

```output
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Packer が VM をビルド、プロビジョナーを実行、およびデプロイメントをクリーンアップするのに数分かかります。


## <a name="create-vm-from-azure-image"></a>Azure イメージから VM を作成する
[az vm create](/cli/azure/vm) を使用して、イメージから VM を作成できるようになりました。 `--image` パラメーターで作成したイメージを指定します。 次の例では、*myPackerImage* から *myVM* という名前の VM を作成し、SSH キーを生成します (まだ存在していない場合)。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

自分の Packer イメージとは異なるリソース グループまたはリージョンで VM を作成する場合は、イメージ名ではなく、イメージ ID を指定します。 [az image show](/cli/azure/image#az-image-show) を使用してイメージ ID を取得できます。

VM の作成には数分かかります。 VM が作成されたら、Azure CLI によって表示される `publicIpAddress` をメモしてください。 このアドレスは、Web ブラウザーから NGINX サイトにアクセスするために使用します。

Web トラフィックが VM にアクセスできるようにするには、[az vm open-port](/cli/azure/vm) を使用してインターネットからポート 80 を開きます。

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>VM と NGINX をテストする
Web ブラウザーを開き、アドレス バーに「`http://publicIpAddress`」と入力できるようになりました。 VM 作成処理で取得した独自のパブリック IP アドレスを指定します。 既定の NGINX ページは次の例のように表示されます。

![NGINX の既定のサイト](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>次のステップ
[Azure Image Builder](image-builder.md) では既存の Packer プロビジョナー スクリプトを使うこともできます。
