---
title: "Azure Container Service チュートリアル - アプリケーションの更新 | Microsoft Docs"
description: "Azure Container Service チュートリアル - アプリケーションの更新"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 72cdbfe2fe65e5152ca748cbb3989e3ef980ee50
ms.contentlocale: ja-jp
ms.lasthandoff: 07/25/2017

---

# <a name="update-an-application-in-kubernetes"></a>Kubernetes でアプリケーションを更新する

Kubernetes でアプリケーションをデプロイした後で、新しいコンテナー イメージまたはイメージ バージョンを指定することによってアプリケーションを更新できます。 アプリケーションを更新するときは、デプロイの一部だけが同時に更新されるように、更新のロールアウトがステージングされます。 このステージングされた更新により、アプリケーションは更新中も実行を続けることができ、デプロイで問題が発生した場合のロールバック メカニズムが提供されます。 

この 7 部構成の 6 番目のチュートリアルでは、サンプルの Azure Vote アプリを更新します。 以下のタスクを行います。

> [!div class="checklist"]
> * フロントエンド アプリケーション コードの更新
> * 更新後のコンテナー イメージの作成
> * Azure Container Registry へのコンテナー イメージのプッシュ
> * 更新したコンテナー イメージのデプロイ

この後のチュートリアルでは、Kubernetes クラスターを監視するように Operations Management Suite を構成します。

## <a name="before-you-begin"></a>開始する前に

これまでのチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、このイメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。 その後、Kubernetes クラスターでアプリケーションを実行しました。 

これらの手順を実行していない場合で、順番に進めたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する](./container-service-tutorial-kubernetes-prepare-app.md)」に戻ってください。 

## <a name="update-application"></a>アプリケーションを更新する

このチュートリアルの手順を実行するには、Azure Vote アプリケーションのコピーを複製しておく必要があります。 必要な場合は、次のコマンドでこの複製コピーを作成します。

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

適当なコード エディターまたはテキスト エディターで `config_file.cfg` ファイルを開きます。 このファイルは、複製されたリポジトリの次のディレクトリにあります。

```bash
 /azure-voting-app-redis/azure-vote/azure-vote/config_file.cfg
```

`VOTE1VALUE` と `VOTE2VALUE` の値を変更して、ファイルを保存します。

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

[docker-compose](https://docs.docker.com/compose/) を使用してフロントエンド イメージを再作成し、更新したアプリケーションを実行します。

```bash
docker-compose -f ./azure-voting-app-redis/docker-compose.yml up --build -d
```

## <a name="test-application-locally"></a>ローカルでアプリケーションをテストする

`http://localhost:8080` に移動し、更新したアプリケーションを確認します。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>イメージにタグを付けてプッシュする

コンテナー レジストリの loginServer で *azure-vote-front* イメージにタグを付けます。

Azure Container Registry を使っている場合は、[az acr list](/cli/azure/acr#list) コマンドでログイン サーバー名を取得します。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[docker tag](https://docs.docker.com/engine/reference/commandline/tag/) を使用してイメージにタグを付けます。 `<acrLoginServer>` を、Azure Container Registry のログイン サーバー名またはパブリック レジストリのホスト名で置き換えます。

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

[docker push](https://docs.docker.com/engine/reference/commandline/push/) を使用してレジストリにイメージをアップロードします。 `<acrLoginServer>` を、Azure Container Registry のログイン サーバー名またはパブリック レジストリのホスト名で置き換えます。

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>更新したアプリケーションをデプロイする

最大限のアップタイムを保証するには、アプリケーション ポッドの複数のインスタンスを実行する必要があります。 [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) コマンドでこの構成を確認します。

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

azure-vote-front イメージを複数のポッドで実行していない場合は、*azure-vote-front* のデプロイを拡張します。


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

アプリケーションを更新するには、[kubectl set](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#set) コマンドを使用します。 コンテナー レジストリのログイン サーバー名またはホスト名で、`<acrLoginServer>` を更新します。

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

デプロイを監視するには、[kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) コマンドを使います。 更新されたアプリケーションがデプロイされると、ポッドが終了されて、新しいコンテナー イメージで再作成されます。

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

*azure-vote-front* サービスの外部 IP アドレスを取得します。

```azurecli-interactive
kubectl get service azure-vote-front
```

その IP アドレスに移動し、更新したアプリケーションを確認します。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、アプリケーションを更新し、この更新を Kubernetes クラスターにロールアウトしました。 次のタスクを完了しました。

> [!div class="checklist"]
> * フロントエンド アプリケーション コードの更新
> * 更新後のコンテナー イメージの作成
> * Azure Container Registry へのコンテナー イメージのプッシュ
> * 更新したアプリケーションのデプロイ

次のチュートリアルに進み、Operations Management Suite で Kubernetes を監視する方法について学習してください。

> [!div class="nextstepaction"]
> [OMS で Kubernetes を監視する](./container-service-tutorial-kubernetes-monitor.md)
