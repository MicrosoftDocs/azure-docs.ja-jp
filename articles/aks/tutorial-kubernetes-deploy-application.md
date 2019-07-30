---
title: Kubernetes on Azure のチュートリアル - アプリケーションのデプロイ
description: この Azure Kubernetes Service (AKS) チュートリアルでは、Azure Container Registry に格納されているカスタム イメージを使用して、複数コンテナーのアプリケーションをクラスターにデプロイします。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: be4d3fd298a7c08aa640585beb741bad18a840ef
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614337"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>チュートリアル:Azure Kubernetes Service (AKS) でのアプリケーションの実行

Kubernetes には、コンテナー化されたアプリケーション用の分散プラットフォームが用意されています。 独自のアプリケーションとサービスをビルドして Kubernetes クラスターにデプロイし、クラスターで可用性と接続性を管理できるようにします。 このチュートリアル (4/7) では、サンプル アプリケーションを Kubernetes クラスターにデプロイします。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Kubernetes マニフェスト ファイルを更新する
> * Kubernetes でアプリケーションを実行する
> * アプリケーションをテストする

追加のチュートリアルでは、このアプリケーションがスケールアウトされて更新されます。

このクイックスタートは、Kubernetes の基本的な概念を理解していることを前提としています。 詳細については、「[Azure Kubernetes Services (AKS) における Kubernetes の中心概念][kubernetes-concepts]」を参照してください。

## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、このイメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。

このチュートリアルを完了するには、事前に作成した `azure-vote-all-in-one-redis.yaml` Kubernetes マニフェスト ファイルが必要です。 このファイルは、前のチュートリアルでは、アプリケーションのソース コードと共にダウンロードされました。 リポジトリの複製が作成されていること、およびディレクトリが複製されたディレクトリに変更されていることを確認します。 これらの手順を完了しておらず、順番に進めたい場合は、[チュートリアル 1 - コンテナー イメージを作成する][aks-tutorial-prepare-app]に関するページから開始してください。

このチュートリアルでは、Azure CLI バージョン 2.0.53 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="update-the-manifest-file"></a>マニフェスト ファイルを更新する

これらのチュートリアルでは、Azure Container Registry (ACR) インスタンスがサンプル アプリケーション用のコンテナー イメージを格納しています。 アプリケーションをデプロイするには、Kubernetes マニフェスト ファイル内のイメージ名を、ACR ログイン サーバー名が含まれるように更新する必要があります。

次のように、[az acr list][az-acr-list] コマンドを使用して、ACR ログイン サーバー名を取得します。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

最初のチュートリアルで複製した git repo のサンプル マニフェスト ファイルは、*microsoft* というログイン サーバー名を使用します。 現在の場所が、複製された *azure-voting-app-redis* ディレクトリ内であることを確認し、テキスト エディター (`vi` など) でマニフェスト ファイルを開きます。

```console
vi azure-vote-all-in-one-redis.yaml
```

*microsoft* は、実際の ACR ログイン サーバー名に置き換えてください。 イメージ名は、マニフェスト ファイルの 51 行目にあります。 次の例は、既定のイメージ名を示しています。

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

マニフェスト ファイルが次の例のようになるように、独自の ACR ログイン サーバー名を指定します。

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

ファイルを保存して閉じます。 `vi` では、`:wq` を使用します。

## <a name="deploy-the-application"></a>アプリケーションのデプロイ

ご利用になるアプリケーションをデプロイするには、[kubectl apply][kubectl-apply] コマンドを使用します。 このコマンドは、マニフェスト ファイルを解析し、定義されている Kubernetes オブジェクトを作成します。 次の例に示すように、サンプルのマニフェスト ファイルを指定します。

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

次の出力例では、AKS クラスター内で正常に作成されたリソースが示されています。

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>アプリケーションをテストする

アプリケーションが実行されると、Kubernetes サービスによってアプリケーション フロント エンドがインターネットに公開されます。 このプロセスが完了するまでに数分かかることがあります。

進行状況を監視するには、[kubectl get service][kubectl-get] コマンドを `--watch` 引数と一緒に使用します。

```console
kubectl get service azure-vote-front --watch
```

最初に、*azure-vote-front* サービスの *EXTERNAL-IP* が "*保留中*" として表示されます。

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

*EXTERNAL-IP* アドレスが "*保留中*" から実際のパブリック IP アドレスに変わったら、`CTRL-C` を使用して `kubectl` ウォッチ プロセスを停止します。 次の出力例は、サービスに割り当てられている有効なパブリック IP アドレスを示しています。

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

アプリケーションが動作していることを確認するには、Web ブラウザーを開いてサービスの外部 IP アドレスにアクセスします。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-tutorials/azure-vote.png)

アプリケーションが読み込まれなかった場合、イメージ レジストリに関する承認の問題が原因になっている可能性があります。 コンテナーのステータスを表示するには、`kubectl get pods` コマンドを使用します。 コンテナー イメージをプルできない場合は、[Kubernetes シークレットによる Container Registry へのアクセスの許可](https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、サンプルの Azure vote アプリケーションを AKS の Kubernetes クラスターにデプロイしました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Kubernetes マニフェスト ファイルを更新する
> * Kubernetes でアプリケーションを実行する
> * アプリケーションをテストする

次のチュートリアルに進んで、Kubernetes アプリケーションとその基になっている Kubernetes インフラストラクチャのスケーリング方法に関して学習してください。

> [!div class="nextstepaction"]
> [Kubernetes ポッドと Kubernetes インフラストラクチャをスケーリングする][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
[kubernetes-concepts]: concepts-clusters-workloads.md
[kubernetes-service]: concepts-network.md#services
