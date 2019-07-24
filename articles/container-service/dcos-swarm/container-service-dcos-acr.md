---
title: (非推奨) Azure DC/OS クラスターでの ACR の使用
description: Azure Container Service の DC/OS クラスターで Azure Container Registry を使用する
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 8319f2f5405271679d0c11d4ac68492cdec8fc14
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100123"
---
# <a name="deprecated-use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>(非推奨) DC/OS クラスターで ACR を使用してアプリケーションをデプロイする

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

この記事では、DC/OS クラスターで Azure Container Registry を使う方法について説明します。 ACR を使うと、コンテナー イメージをプライベートに保存して管理することができます。 このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * Azure Container Registry をデプロイする (必要な場合)
> * DC/OS クラスターで ACR 認証を構成する
> * Azure Container Registry にイメージをアップロードする
> * Azure Container Registry からコンテナー イメージを実行する

このチュートリアルの手順を実行するには、ACS DC/OS クラスターが必要です。 必要に応じて、[このサンプル スクリプト](./../kubernetes/scripts/container-service-cli-deploy-dcos.md)で作成できます。

このチュートリアルには、Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 アップグレードする必要がある場合は、「[Azure CLI のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Azure Container Registry のデプロイ

必要な場合は、[az acr create](/cli/azure/acr#az-acr-create) コマンドで Azure Container Registry を作成します。 

次の例では、ランダムに生成される名前でレジストリを作成します。 `--admin-enabled` 引数を使って管理者アカウントでレジストリを構成することもできます。

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

レジストリを作成すると、Azure CLI では次のようなデータが出力されるようになります。 後の手順で使うので、`name` と `loginServer` を書き留めておいてください。

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

[az acr credential show](/cli/azure/acr/credential) コマンドを使って、コンテナー レジストリの資格情報を取得します。 `--name` は前の手順で書き留めたものに置き換えます。 後の手順で必要になるので、パスワードの 1 つを書き留めておきます。

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Azure Container Registry について詳しくは、「[プライベート Docker コンテナー レジストリの概要](../../container-registry/container-registry-intro.md)」をご覧ください。 

## <a name="manage-acr-authentication"></a>ACR 認証を管理する

従来の方法でプライベート レジストリからイメージをプッシュおよびプルするには、まずレジストリに対して認証を行います。 そのためには、プライベート レジストリにアクセスする必要がある任意のクライアントで、`docker login` コマンドを使います。 DC/OS クラスターは複数のノードを含むことができ、すべてのノードで ACR の認証を行う必要があります。各ノードでこのプロセスを自動化しておくと便利です。 

### <a name="create-shared-storage"></a>共有記憶域を作成する

このプロセスでは、クラスターの各ノードでマウントされている Azure ファイル共有を使います。 共有記憶域をまだ設定していない場合は、「[ファイル共有を作成し、DC/OS クラスターにマウントする](container-service-dcos-fileshare.md)」をご覧ください。

### <a name="configure-acr-authentication"></a>ACR 認証を構成する

最初に、DC/OS マスターの FQDN を取得し、それを変数に格納します。

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

DC/OS に基づくクラスターのマスター (または最初のマスター) との SSH 接続を作成します。 クラスターの作成時に既定以外の値が使用されている場合は、ユーザー名を更新します。

```azurecli-interactive
ssh azureuser@$FQDN
```

次のコマンドを実行して、Azure Container Registry にログインします。 `--username` はコンテナー レジストリの名前に置き換え、`--password` は提供されたパスワードの 1 つに置き換えます。 この例の最後の引数 *mycontainerregistry.azurecr.io* は、コンテナー レジストリの loginServer 名に置き換えます。 

このコマンドは、認証値を `~/.docker` パスの下にローカルに格納します。

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

コンテナー レジストリの認証値を含む圧縮ファイルを作成します。

```azurecli-interactive
tar czf docker.tar.gz .docker
```

このファイルを、クラスターの共有記憶域にコピーします。 これにより、DC/OS クラスターのすべてのノードでファイルを使うことができるようになります。

```azurecli-interactive
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>ACR にイメージをアップロードする

開発用コンピューターまたは Docker がインストールされている他のシステムで、イメージを作成し、Azure Container Registry にそれをアップロードします。

Ubuntu イメージからコンテナーを作成します。

```azurecli-interactive
docker run ubuntu --name base-image
```

コンテナーを新しいイメージにキャプチャします。 イメージ名には、コンテナー レジストリの `loginServer` 名が `loginServer/imageName` の形式で含まれる必要があります。

```azurecli-interactive
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
```

Azure Container Registry にログインします。 名前は loginServer 名に、--username はコンテナー レジストリの名前に、--password は提供されたパスワードの 1 つに、それぞれ置き換えます。

```azurecli-interactive
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

最後に、ACR レジストリにイメージをアップロードします。 この例では、dcos-demo という名前のイメージをアップロードします。

```azurecli-interactive
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>ACR からイメージを実行する

ACR レジストリからイメージを使うには、*acrDemo.json* という名前のファイルを作成し、次のテキストをそれにコピーします。 イメージ名は、コンテナー レジストリの loginServer 名とイメージ名に置き換えます (例: `loginServer/imageName`)。 `uris` プロパティを書き留めておきます。 このプロパティは、コンテナー レジストリ認証ファイルの場所を保持しています。この例では、DC/OS クラスターの各ノードでマウントされている Azure ファイル共有です。

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

DC/OC CLI を使ってアプリケーションをデプロイします。

```azurecli-interactive
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Container Registry を使うように DC/OS を構成し、次のタスクが含まれました。

> [!div class="checklist"]
> * Azure Container Registry をデプロイする (必要な場合)
> * DC/OS クラスターで ACR 認証を構成する
> * Azure Container Registry にイメージをアップロードする
> * Azure Container Registry からコンテナー イメージを実行する
