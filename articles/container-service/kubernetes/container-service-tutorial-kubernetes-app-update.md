---
title: (非推奨) Azure Container Service チュートリアル - アプリケーションの更新
description: Azure Container Service チュートリアル - アプリケーションの更新
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3c0be935a4ffb51c03d2f63b14ab7c0c713dd2ae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006253"
---
# <a name="deprecated-update-an-application-in-kubernetes"></a>(非推奨) Kubernetes でアプリケーションを更新する

> [!TIP]
> Azure Kubernetes Service を使用したこのチュートリアルの更新版については、「[チュートリアル:Azure Kubernetes Service (AKS) でのアプリケーションの更新](../../aks/tutorial-kubernetes-app-update.md)」を参照してください。

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Kubernetes でアプリケーションをデプロイした後で、新しいコンテナー イメージまたはイメージ バージョンを指定することによってアプリケーションを更新できます。 アプリケーションを更新するときは、デプロイの一部だけが同時に更新されるように、更新がステージングされます。 この段階的な更新プログラムを使用すると、アプリケーションの更新中も引き続きアプリケーションを実行することができます。 デプロイ エラーが発生した場合のロールバック メカニズムも提供されています。 

この 7 部構成の 6 番目のチュートリアルでは、サンプルの Azure Vote アプリを更新します。 以下のタスクを行います。

> [!div class="checklist"]
> * フロントエンド アプリケーション コードの更新
> * 更新後のコンテナー イメージの作成
> * Azure Container Registry へのコンテナー イメージのプッシュ
> * 更新したコンテナー イメージのデプロイ

この後のチュートリアルでは、Kubernetes クラスターを監視するように Log Analytics を構成します。

## <a name="before-you-begin"></a>開始する前に

これまでのチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、このイメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。 その後、Kubernetes クラスターでアプリケーションを実行しました。 

アプリケーション リポジトリも複製しましたが、それにはアプリケーションのソース コードと、このチュートリアルで使用する事前作成された Docker Compose ファイルが含まれています。 リポジトリの複製が作成されていること、およびディレクトリが複製されたディレクトリに変更されていることを確認します。 内部には、`azure-vote` という名前のディレクトリと `docker-compose.yml` という名前のファイルがあります。

これらの手順を実行していない場合で、順番に進めたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する](./container-service-tutorial-kubernetes-prepare-app.md)」に戻ってください。 

## <a name="update-application"></a>アプリケーションを更新する

このチュートリアルでは、アプリケーションを変更し、更新したアプリケーションを Kubernetes クラスターにデプロイします。 

アプリケーションのソース コードは、`azure-vote` ディレクトリ内にあります。 適当なコード エディターまたはテキスト エディターで `config_file.cfg` ファイルを開きます。 この例では、 `vi` が使用されます。

```bash
vi azure-vote/azure-vote/config_file.cfg
```

`VOTE1VALUE` と `VOTE2VALUE` の値を変更して、ファイルを保存します。

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

ファイルを保存して閉じます。

## <a name="update-container-image"></a>コンテナー イメージの更新

[docker-compose](https://docs.docker.com/compose/) を使用してフロントエンド イメージを再作成し、更新したアプリケーションを実行します。 引数 `--build` は、Docker Compose にアプリケーション イメージの再作成を指示するために使用されます。

```bash
docker-compose up --build -d
```

## <a name="test-application-locally"></a>ローカルでアプリケーションをテストする

`http://localhost:8080` に移動し、更新したアプリケーションを確認します。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>イメージにタグを付けてプッシュする

コンテナー レジストリの loginServer で `azure-vote-front` イメージにタグを付けます。 

[az acr list](/cli/azure/acr#az-acr-list) コマンドを使用して、ログイン サーバー名を取得します。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[docker tag](https://docs.docker.com/engine/reference/commandline/tag/) を使用してイメージにタグを付けます。 `<acrLoginServer>` を、Azure Container Registry のログイン サーバー名またはパブリック レジストリのホスト名で置き換えます。 また、イメージのバージョンは `redis-v2` に更新されます。

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

[docker push](https://docs.docker.com/engine/reference/commandline/push/) を使用してレジストリにイメージをアップロードします。 `<acrLoginServer>` を Azure Container Registry のログイン サーバー名に置き換えます。

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>更新したアプリケーションをデプロイする

最大限のアップタイムを保証するには、アプリケーション ポッドの複数のインスタンスを実行する必要があります。 [kubectl get pod](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) コマンドでこの構成を確認します。

```bash
kubectl get pod
```

出力:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

azure-vote-front イメージを複数のポッドで実行していない場合は、`azure-vote-front` のデプロイを拡張します。


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

アプリケーションを更新するには、[kubectl set](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set) コマンドを使用します。 コンテナー レジストリのログイン サーバー名またはホスト名で、`<acrLoginServer>` を更新します。

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

デプロイを監視するには、[kubectl get pod](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) コマンドを使います。 更新されたアプリケーションがデプロイされると、ポッドが終了されて、新しいコンテナー イメージで再作成されます。

```azurecli-interactive
kubectl get pod
```

出力:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>更新されたアプリケーションをテストする

`azure-vote-front` サービスの外部 IP アドレスを取得します。

```azurecli-interactive
kubectl get service azure-vote-front
```

その IP アドレスに移動し、更新したアプリケーションを確認します。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、アプリケーションを更新し、この更新を Kubernetes クラスターにロールアウトしました。 次のタスクを完了しました。

> [!div class="checklist"]
> * フロントエンド アプリケーション コードの更新
> * 更新後のコンテナー イメージの作成
> * Azure Container Registry へのコンテナー イメージのプッシュ
> * 更新したアプリケーションのデプロイ

次のチュートリアルに進み、Log Analytics で Kubernetes を監視する方法について学習してください。

> [!div class="nextstepaction"]
> [Log Analytics で Kubernetes を監視する](./container-service-tutorial-kubernetes-monitor.md)