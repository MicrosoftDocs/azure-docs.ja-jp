---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: be170144fddeb1a69592f1714ec745d559665832
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982440"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Docker 対応仮想マシンの作成

テスト目的の場合は、Docker 対応の Ubuntu VM を使用して、Azure コンテナー レジストリにアクセスします。 レジストリに Azure Active Directory 認証を使用するには、VM に [Azure CLI][azure-cli] もインストールします。 既に Azure 仮想マシンがある場合は、この作成手順を省略します。

仮想マシンとコンテナー レジストリに同じリソース グループを使用できます。 このセットアップでは最終的にクリーンアップが簡素化されますが、必須ではありません。 仮想マシンと仮想ネットワークに別々のリソース グループを作成することを選択する場合は、[az group create][az-group-create] を実行します。 次の例では、リソース グループ名とレジストリの場所に対して環境変数を設定していることを前提としています。

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

ここで、[az vm create][az-vm-create] を使用して、既定の Ubuntu Azure 仮想マシンをデプロイします。 次の例では、*myDockerVM* という名前の VM を作成します。

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM が作成されるまで､数分間かかります｡ コマンドが完了したら、Azure CLI によって表示された `publicIpAddress` をメモします。 このアドレスは、VM への SSH 接続を作成するために使用します。

### <a name="install-docker-on-the-vm"></a>VM に Docker をインストールする

VM が実行されたら、VM への SSH 接続を作成します。 *publicIpAddress* を VM のパブリック IP アドレスに置き換えます。

```bash
ssh azureuser@publicIpAddress
```

次のコマンドを実行して、Ubuntu VM に Docker をインストールします。

```bash
sudo apt-get update
sudo apt install docker.io -y
```

インストールの後、次のコマンドを実行して、VM 上で Docker が正しく実行されていることを確認します。

```bash
sudo docker run -it hello-world
```

出力:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure CLI のインストール

Ubuntu 仮想マシンに Azure CLI をインストールするには、「[apt での Azure CLI のインストール](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)」の手順に従います。 次に例を示します。

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

SSH 接続を終了します。

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-group-create]: /cli/azure/group