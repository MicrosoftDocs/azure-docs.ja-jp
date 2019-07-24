---
title: (非推奨) Azure Container Service クイック スタート - DC/OS クラスターのデプロイ
description: Azure Container Service クイックスタート - DC/OS のデプロイ
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: efaf82c3f378f572c289b587dbe5df1923a58c62
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000514"
---
# <a name="deprecated-deploy-a-dcos-cluster"></a>(非推奨) Docker DC/OS クラスターのデプロイ

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS は、最新のコンテナー化されたアプリケーションを実行するための分散プラットフォームを提供します。 Azure Container Service を使用すると、運用開始準備の整った DC/OS クラスターのプロビジョニングを簡単かつ迅速に行うことができます。 このクイック スタートでは、DC/OS クラスターをデプロイして基本的なワークロードを実行するのに必要な基本的な手順を詳しく説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このチュートリアルには、Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 アップグレードする必要がある場合は、「[Azure CLI のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="log-in-to-azure"></a>Azure にログインする 

[az login](/cli/azure/reference-index#az-login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az-group-create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>DC/OS クラスターの作成

[az acs create](/cli/azure/acs#az-acs-create) コマンドを使用して DC/OS クラスターを作成します。

次の例では、*myDCOSCluster* という名前の DC/OS クラスターを作成し、まだ SSH キーが存在しない場合は SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

制限付き試用版を使用する場合など、Azure サブスクリプションによって Azure リソースへのアクセスが制限される場合もあります。 使用可能なコア数が限られているためにデプロイが失敗した場合は、`--agent-count 1` を [az acs create](/cli/azure/acs#az-acs-create) コマンドに追加して、既定のエージェント数を減らします。 

数分してコマンドが完了すると、デプロイに関する情報が返されます。

## <a name="connect-to-dcos-cluster"></a>DC/OS クラスターへの接続

DC/OS クラスターが作成されたら、SSH トンネルを通してアクセスすることができます。 DC/OS マスターのパブリック IP アドレスを返すには、次のコマンドを実行します。 この IP アドレスは変数に格納され、次の手順で使用されます。

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

SSH トンネルを作成するには、次のコマンドを実行し、画面に表示される指示に従います。 ポート 80 が既に使用されている場合、コマンドは失敗します。 トンネリングするポートを未使用のポートに更新します (`85:localhost:80` など)。 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

SSH トンネルは、`http://localhost` を参照することでテストできます。 80 以外のポートが使用されている場合は、場所が一致するするように調整します。 

SSH トンネルが正常に作成されている場合は、DC/OS ポータルが返されます。

![DCOS UI](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>DC/OS CLI のインストール

DC/OS コマン ドライン インターフェイスは、コマンドラインから DC/OS クラスターを管理するために使用されます。 [az acs dcos install-cli](/cli/azure/acs/dcos#az-acs-dcos-install-cli) コマンドを使用して DC/OS CLI をインストールします。 Azure CloudShell を使用している場合、DC/OS CLI は既にインストールされています。 

Azure CLI を macOS または Linux で実行しているとき、場合によっては sudo を使用してコマンドを実行する必要があります。

```azurecli
az acs dcos install-cli
```

CLI をクラスターで使用するには、事前に SSH トンネルを使用するように構成しておく必要があります。 そのためには、次のコマンドを実行し、必要に応じてポートを調整します。

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>アプリケーションの実行

ACS DC/OS クラスターの既定のスケジュール設定メカニズムは Marathon です。 Marathon は、アプリケーションを開始し、DC/OS クラスター上でアプリケーションの状態を管理するために使用されます。 Marathon を介してアプリケーションのスケジュールを設定するには、*marathon-app.json* という名前のファイルを作成し、次の内容をコピーします。 

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

次のコマンドを実行することで、DC/OS クラスターで実行するアプリケーションのスケジュールを設定します。

```azurecli
dcos marathon app add marathon-app.json
```

アプリケーションのデプロイの状態を表示するには、次のコマンドを実行します。

```azurecli
dcos marathon app list
```

**WAITING** 列の値が *True* から *False* に変化すると、アプリケーションのデプロイが完了したことになります。

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

DC/OS クラスター エージェントのパブリック IP アドレスを取得します。

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

このアドレスを参照すると、既定の NGINX サイトが返されます。

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>DC/OS クラスターの削除

必要がなくなったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループ、DC/OS クラスター、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、DC/OS クラスターをデプロイし、このクラスター上で単純な Docker コンテナーを実行しました。 Azure Container Service をより深く学習するには、ACS チュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ACS DC/OS クラスターを管理する](container-service-dcos-manage-tutorial.md)