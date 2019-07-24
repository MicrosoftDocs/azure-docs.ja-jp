---
title: (非推奨) Azure Container Service チュートリアル - アプリケーションのデプロイ
description: Azure Container Service チュートリアル - アプリケーションのデプロイ
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: dafbb8d1221d5e9c6194611ad338b3714a089cea
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998767"
---
# <a name="deprecated-run-applications-in-kubernetes"></a>(非推奨) Kubernetes でアプリケーションを実行する

> [!TIP]
> Azure Kubernetes Service を使用したこのチュートリアルの更新版については、「[チュートリアル:Azure Kubernetes Service (AKS) でのアプリケーションの実行](../../aks/tutorial-kubernetes-deploy-application.md)」を参照してください。

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

このチュートリアル (4/7) では、サンプル アプリケーションを Kubernetes クラスターにデプロイします。 手順は次のとおりです。

> [!div class="checklist"]
> * Kubernetes マニフェスト ファイルを更新する
> * Kubernetes でアプリケーションを実行する
> * アプリケーションをテストする

その後のチュートリアルでは、このアプリケーションをスケールアウトし、更新します。さらに、Kubernetes クラスターを監視するように Log Analytics を構成します。

このチュートリアルは、Kubernetes の概念についての基礎知識があることを前提としています。Kubernetes の詳細については、[Kubernetes のドキュメント](https://kubernetes.io/docs/home/)を参照してください。

## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、このイメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。 

このチュートリアルを完了するには、事前に作成した `azure-vote-all-in-one-redis.yml` Kubernetes マニフェスト ファイルが必要です。 このファイルは、前のチュートリアルでは、アプリケーションのソース コードと共にダウンロードされました。 リポジトリの複製が作成されていること、およびディレクトリが複製されたディレクトリに変更されていることを確認します。

これらの手順を実行していない場合で、行いたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する](./container-service-tutorial-kubernetes-prepare-app.md)」に戻ってください。 

## <a name="update-manifest-file"></a>マニフェスト ファイルを更新する

このチュートリアルでは、Azure Container Registry (ACR) を使用してコンテナー イメージを保存しています。 アプリケーションを実行する前に、Kubernetes マニフェスト ファイルの ACR ログイン サーバー名を更新する必要があります。

ACR ログイン サーバー名を取得するには、[az acr list](/cli/azure/acr#az-acr-list) コマンドを使います。

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

マニフェスト ファイルは、ログイン サーバー名 `microsoft` で事前作成されています。 任意のテキスト エディターでファイルを開きます。 この例では、ファイルは `vi` で開きます。

```bash
vi azure-vote-all-in-one-redis.yml
```

`microsoft` を ACR ログイン サーバー名に置き換えます。 この値は、マニフェスト ファイルの **47** 行にあります。

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

ファイルを保存して閉じます。

## <a name="deploy-application"></a>アプリケーションをデプロイする

[kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) コマンドを使用してアプリケーションを実行します。 このコマンドは、マニフェスト ファイルを解析し、定義されている Kubernetes オブジェクトを作成します。

```azurecli-interactive
kubectl create -f azure-vote-all-in-one-redis.yml
```

出力:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>アプリケーションをテストする

アプリケーションをインターネットに公開する [Kubernetes サービス](https://kubernetes.io/docs/concepts/services-networking/service/)が作成されます。 このプロセスには数分かかることがあります。 

進行状況を監視するには、[kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) コマンドを `--watch` 引数と一緒に使用します。

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

最初に `azure-vote-front` サービスの **EXTERNAL-IP** が `pending` として表示されます。 EXTERNAL-IP アドレスが `pending` から `IP address` に変わったら、`CTRL-C` を使用して kubectl ウォッチ プロセスを停止します。

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

アプリケーションを表示するには、外部 IP アドレスを参照します。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Vote アプリケーションを Azure Container Service の Kubernetes クラスターにデプロイしました。 次のタスクを行います。  

> [!div class="checklist"]
> * Kubernetes マニフェスト ファイルをダウンロードする
> * Kubernetes でアプリケーションを実行する
> * アプリケーションをテストする

次のチュートリアルに進んで、Kubernetes アプリケーションとその基になっている Kubernetes インフラストラクチャ両方のスケーリングに関して学習してください。 

> [!div class="nextstepaction"]
> [Kubernetes ポッドと Kubernetes インフラストラクチャをスケーリングする](./container-service-tutorial-kubernetes-scale.md)