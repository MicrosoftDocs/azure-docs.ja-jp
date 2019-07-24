---
title: (非推奨) Azure Container Service チュートリアル - DC/OS の管理
description: Azure Container Service チュートリアル - DC/OS の管理
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: fe943ae5ac7894cdd8d8e104615cea670513b7eb
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000413"
---
# <a name="deprecated-azure-container-service-tutorial---manage-dcos"></a>(非推奨) Azure Container Service チュートリアル - DC/OS の管理

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS は、最新のコンテナー化されたアプリケーションを実行するための分散プラットフォームを提供します。 Azure Container Service を使用すると、運用開始準備の整った DC/OS クラスターのプロビジョニングを簡単かつ迅速に行うことができます。 このクイック スタートでは、DC/OS クラスターをデプロイして基本的なワークロードを実行するのに必要な基本的な手順を詳しく説明します。

> [!div class="checklist"]
> * ACS DC/OS クラスターの作成
> * クラスターへの接続
> * DC/OS CLI のインストール
> * クラスターへのアプリケーションのデプロイ
> * クラスター上でのアプリケーションのスケーリング
> * DC/OS クラスター ノードのスケーリング
> * 基本的な DC/OS 管理
> * DC/OS クラスターの削除

このチュートリアルには、Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 アップグレードする必要がある場合は、「[Azure CLI のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="create-dcos-cluster"></a>DC/OS クラスターの作成

まず、[az group create](/cli/azure/group#az-group-create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、*myResourceGroup* という名前のリソース グループを *westeurope* の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westeurope
```

次に、[az acs create](/cli/azure/acs#az-acs-create) コマンドを使用して DC/OS クラスターを作成します。

次の例では、*myDCOSCluster* という名前の DC/OS クラスターを作成し、まだ SSH キーが存在しない場合は SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

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

## <a name="install-dcos-cli"></a>DC/OS CLI のインストール

[az acs dcos install-cli](/cli/azure/acs/dcos#az-acs-dcos-install-cli) コマンドを使用して DC/OS CLI をインストールします。 Azure CloudShell を使用している場合、DC/OS CLI は既にインストールされています。 Azure CLI を macOS または Linux で実行しているとき、場合によっては sudo を使用してコマンドを実行する必要があります。

```azurecli
az acs dcos install-cli
```

CLI をクラスターで使用するには、事前に SSH トンネルを使用するように構成しておく必要があります。 そのためには、次のコマンドを実行し、必要に応じてポートを調整します。

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>アプリケーションの実行

ACS DC/OS クラスターの既定のスケジュール設定メカニズムは Marathon です。 Marathon は、アプリケーションを開始し、DC/OS クラスター上でアプリケーションの状態を管理するために使用されます。 Marathon を介してアプリケーションのスケジュールを設定するには、**marathon-app.json** という名前のファイルを作成し、次の内容をコピーします。 

```json
{
  "id": "demo-app-private",
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
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
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

**TASKS** 列の値が *0/1* から *1/1* に変化すると、アプリケーションのデプロイが完了したことになります。

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>Marathon アプリケーションのスケーリング

前の例では、単一のインスタンス アプリケーションが作成されました。 アプリケーションの 3 つのインスタンスが使用できるように、このデプロイを更新するには、**marathon-app.json** ファイルを開き、インスタンス プロパティを 3 に更新します。

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 3,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

`dcos marathon app update` コマンドを使用してアプリケーションを更新します。

```azurecli
dcos marathon app update demo-app-private < marathon-app.json
```

アプリケーションのデプロイの状態を表示するには、次のコマンドを実行します。

```azurecli
dcos marathon app list
```

**TASKS** 列の値が *1/3* から *3/1* に変化すると、アプリケーションのデプロイが完了したことになります。

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>インターネットでアクセス可能なアプリの実行

ACS DC/OS クラスターは、2 つのノード セットから成ります。1 つはインターネットからアクセス可能なパブリック ノード セットであり、もう 1 つはインターネットからアクセスできないプライベート ノード セットです。 既定のセットはプライベート ノードであり、最後に示した例で使用しました。

アプリケーションをインターネットからアクセスできるようにするには、ノード セットをパブリック ノード セットにデプロイします。 そのためには、`acceptedResourceRoles` オブジェクトに値 `slave_public` を与えます。

**nginx-public.json** という名前のファイルを作成し、次の内容をコピーします。

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
dcos marathon app add nginx-public.json
```

DC/OS パブリック クラスター エージェントのパブリック IP アドレスを取得します。

```azurecli 
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

このアドレスを参照すると、既定の NGINX サイトが返されます。

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>DC/OS クラスターのスケーリング

前の例では、アプリケーションを複数のインスタンスにスケーリングしました。 DC/OS インフラストラクチャをスケーリングして、計算容量を増減することもできます。 これは、[az acs scale](/cli/azure/acs#az-acs-scale) コマンドを使用して行います。 

DC/OS エージェントの現在の数を表示するには、[az acs show](/cli/azure/acs#az-acs-show) を使用します。

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

数を 5 に増やすには、[az acs scale](/cli/azure/acs#az-acs-scale) コマンドを使用します。 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>DC/OS クラスターの削除

必要がなくなったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループ、DC/OS クラスター、およびすべての関連リソースを削除できます。

```azurecli 
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次に示す基本的な DC/OS 管理タスクについて説明しました。 

> [!div class="checklist"]
> * ACS DC/OS クラスターの作成
> * クラスターへの接続
> * DC/OS CLI のインストール
> * クラスターへのアプリケーションのデプロイ
> * クラスター上でのアプリケーションのスケーリング
> * DC/OS クラスター ノードのスケーリング
> * DC/OS クラスターの削除

次のチュートリアルでは、Azure の DC/OS でのアプリケーションの負荷分散について学習します。 

> [!div class="nextstepaction"]
> [アプリケーションを負荷分散する](container-service-load-balancing.md)
