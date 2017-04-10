---
title: "Docker コンテナー クラスターのデプロイ - Azure CLI | Microsoft Docs"
description: "Azure CLI 2.0 を使用して Kubernetes、DC/OS、または Docker Swarm ソリューションを Azure Container Service にデプロイします"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: saudas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1dad536939f179cd8231d0f8805c1ff4335850d5
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-a-docker-container-hosting-solution-using-the-azure-cli-20"></a>Azure CLI 2.0 を使用して Docker コンテナーのホスティング ソリューションをデプロイする

Azure CLI 2.0 の `az acs` コマンドを使用すると、Azure Container Service のクラスターを作成および管理できます。 [Azure Portal](container-service-deployment.md) または Azure Container Service API を使用して、Azure Container Service クラスターをデプロイすることもできます。

`az acs` の各コマンドのヘルプについては、任意のコマンドで `-h` パラメーターを指定してください。 (例: `az acs create -h`)。



## <a name="prerequisites"></a>前提条件
Azure CLI 2.0 を使用して Azure Container Service クラスターを作成するには、以下のものが必要です。
* Azure アカウント ([無料試用版はこちら](https://azure.microsoft.com/pricing/free-trial/))
* インストールして設定した [Azure CLI 2.0](/cli/azure/install-az-cli2)

## <a name="get-started"></a>作業開始 
### <a name="log-in-to-your-account"></a>アカウントへのログイン
```azurecli
az login 
```

画面の指示に従って、対話形式でログインします。 その他の方法でのログインについては、「[Get started with Azure CLI 2.0 (Azure CLI 2.0 の概要)](/cli/azure/get-started-with-az-cli2)」を参照してください。

### <a name="set-your-azure-subscription"></a>Azure サブスクリプションの設定

複数の Azure サブスクリプションがある場合は、既定のサブスクリプションを設定します。 次に例を示します。

```
az account set --subscription "f66xxxxx-xxxx-xxxx-xxx-zgxxxx33cha5"
```


### <a name="create-a-resource-group"></a>リソース グループの作成
クラスターごとにリソース グループを作成することをお勧めします。 Azure Container Service を[使用可能](https://azure.microsoft.com/en-us/regions/services/)にする Azure リージョンを指定します。 For example:

```azurecli
az group create -n acsrg1 -l "westus"
```
次のような出力になります。

![リソース グループの作成](media/container-service-create-acs-cluster-cli/rg-create.png)


## <a name="create-an-azure-container-service-cluster"></a>Azure Container Service クラスターの作成

クラスターを作成するには、`az acs create` を使用します。
クラスターの名前と、前の手順で作成したリソース グループの名前は、必須のパラメーターです。 

その他の入力は、それぞれスイッチを使用して上書きしない限り、既定値に設定されます (次の画像を参照)。 たとえば、オーケストレーターは既定では DC/OS に設定されます。 指定しない場合は、クラスター名に基づいて DNS 名プレフィックスが作成されます。

![az acs create の使用方法](media/container-service-create-acs-cluster-cli/create-help.png)


### <a name="quick-acs-create-using-defaults"></a>既定値を使用した簡単な `acs create`
SSH RSA 公開キー ファイル `id_rsa.pub` が既定の場所にある (あるいは、[OS X と Linux](../virtual-machines/linux/mac-create-ssh-keys.md) または [Windows](../virtual-machines/linux/ssh-from-windows.md) 用に作成した) 場合は、次のようなコマンドを使用します。

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```
SSH 公開キーがない場合は、次の 2 つ目のコマンドを使用します。 このコマンドの `--generate-ssh-keys` スイッチで、キーが自動的に作成されます。

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

コマンドを入力した後、クラスターが作成されるまで約 10 分待ちます。 コマンドの出力には、マスター ノードおよびエージェント ノードの完全修飾ドメイン名 (FQDN) と、最初のマスターに接続するための SSH コマンドが含まれています。 以下に、出力の一部を示します。

![ACS create の画像](media/container-service-create-acs-cluster-cli/cluster-create.png)

> [!TIP]
> [Kubernetes チュートリアル](container-service-kubernetes-walkthrough.md)では、既定値で `az acs create` を使用して Kubernetes クラスターを作成する方法を示しています。
>

## <a name="manage-acs-clusters"></a>ACS クラスターの管理

クラスターを管理するには、追加の `az acs` コマンドを使用します。 次に例をいくつか示します。

### <a name="list-clusters-under-a-subscription"></a>サブスクリプションのクラスターの一覧表示

```azurecli
az acs list --output table
```

### <a name="list-clusters-in-a-resource-group"></a>リソース グループのクラスターの一覧表示

```azurecli
az acs list -g acsrg1 --output table
```

![acs list](media/container-service-create-acs-cluster-cli/acs-list.png)


### <a name="display-details-of-a-container-service-cluster"></a>コンテナー サービス クラスターの詳細の表示

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![acs show](media/container-service-create-acs-cluster-cli/acs-show.png)


### <a name="scale-the-cluster"></a>クラスターのスケーリング
エージェント ノードのスケールインとスケールアウトの両方が可能です。 パラメーター `new-agent-count` は、ACS クラスター内の新しいエージェントの数です。

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![acs scale](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>コンテナー サービス クラスターの削除
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
このコマンドによって、コンテナー サービスの作成中に作成されたすべてのリソース (ネットワークおよびストレージ) が削除されるわけではありません。 すべてのリソースを簡単に削除するには、個別のリソース グループに各クラスターをデプロイすることをお勧めします。 その後、クラスターが不要になったら、リソース グループを削除します。

## <a name="next-steps"></a>次のステップ
これでクラスターが機能します。接続と管理の詳細については、次のドキュメントを参照してください。

* [Azure コンテナー サービス クラスターに接続する](container-service-connect.md)
* [Azure コンテナー サービスと DC/OS の使用](container-service-mesos-marathon-rest.md)
* [Azure コンテナー サービスと Docker Swarm の使用](container-service-docker-swarm.md)
* [Azure Container Service と Kubernetes の使用](container-service-kubernetes-walkthrough.md)
