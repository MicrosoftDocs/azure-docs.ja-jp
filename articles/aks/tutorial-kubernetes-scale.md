---
title: Kubernetes on Azure のチュートリアル - アプリケーションのスケーリング
description: この Azure Kubernetes Service (AKS) チュートリアルでは、Kubernetes のノードとポッドをスケーリングする方法のほか、ポッドの水平自動スケーリングを導入する方法について説明します。
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: dfebb6561e83c51063515ec655153aaaa7a09c0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251371"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>チュートリアル:Azure Kubernetes Service (AKS) でのアプリケーションのスケーリング

ここまでチュートリアルに従って進めてきた場合は、AKS で Kubernetes クラスターが動作していて、サンプル Azure Vote アプリをデプロイしてあります。 このチュートリアルでは、7 つあるうちの 5 番目のパートで、アプリのポッドをスケールアウトし、ポッドの自動スケーリングを試します。 また、Azure VM ノードの数をスケーリングして、クラスターがワークロードをホストする容量を変更する方法についても説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Kubernetes ノードをスケーリングする
> * アプリケーションを実行する Kubernetes ポッドを手動でスケーリングする
> * アプリのフロントエンドを実行する自動スケーリング ポッドを構成する

後続のチュートリアルでは、Azure 投票アプリケーションが新しいバージョンに更新されます。

## <a name="before-you-begin"></a>開始する前に

これまでのチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化しました。 このイメージを Azure Container Registry にアップロードし、AKS クラスターを作成しました。 その後、AKS クラスターにアプリケーションをデプロイしました。 これらの手順を完了しておらず、順番に進めたい場合は、[チュートリアル 1 - コンテナー イメージを作成する][aks-tutorial-prepare-app]に関するページから開始してください。

このチュートリアルでは、Azure CLI バージョン 2.0.53 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="manually-scale-pods"></a>ポッドを手動でスケーリングする

前のチュートリアルで Azure Vote フロントエンドと Redis インスタンスをデプロイしたときに、レプリカを 1 つ作成しました。 ご利用のクラスターに存在するポッドの数と状態を確認するには、次のように [kubectl get][kubectl-get] コマンドを使用します。

```console
kubectl get pods
```

次の出力例を見ると、フロントエンド ポッドとバックエンド ポッドが 1 つずつ存在することがわかります。

```output
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

*azure-vote-front* のデプロイに含まれるポッドの数を手動で変更するには、[kubectl scale][kubectl-scale] コマンドを使います。 次の例では、フロントエンド ポッドの数を *5* に増やしています。

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

AKS によって追加のポッドが正常に作成されていることを確認するために、もう一度 [kubectl get pods][kubectl-get] を実行します。 しばらくすると、ポッドがクラスターで利用可能になります。

```console
kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>ポッドを自動スケールする

Kubernetes は[ポッドの水平自動スケーリング][kubernetes-hpa]をサポートしており、CPU 使用率などの選ばれたメトリックに応じて、デプロイのポッドの数を調整します。 [Metrics Server][metrics-server] は、Kubernetes にリソース使用率を提供するために使用され、AKS クラスター バージョン 1.10 以降に自動的にデプロイされます。 AKS クラスターのバージョンを確認するには、次の例に示すように、[az aks show][az-aks-show] コマンドを使用します。

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion --output table
```

> [!NOTE]
> AKS クラスターが *1.10* 未満の場合、Metrics Server は自動的にインストールされません。 Metrics Server のインストール マニフェストは、Metrics Server リリースの `components.yaml` 資産として入手できます。つまり、URL を使用してインストールすることが可能です。 これらの YAML 定義の詳細については、Readme の「[デプロイ][metrics-server-github]」セクションを参照してください。
> 
> インストール例: 
> ```console
> kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.6/components.yaml
> ```

オートスケーラーを使用するには、ポッド内のすべてのコンテナーで CPU の要求と制限が定義されている必要があります。 `azure-vote-front` のデプロイでは、フロントエンド コンテナーによって既に 0.25 CPU が要求されています。上限は 0.5 CPU です。 これらのリソース要求と制限は、次のスニペットの例に示されているように定義されています。

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

次の例では、[kubectl autoscale][kubectl-autoscale] コマンドを使って、*azure-vote-front* のデプロイのポッド数を自動スケーリングします。 すべてのポッドの平均 CPU 使用率が、要求された使用率の 50% を超えると、自動スケーラーはポッドを最大 *10* インスタンスまで増やします。 その後、少なくとも *3* インスタンスがデプロイ用に定義されます。

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

または、マニフェスト ファイルを作成して、オートスケーラーの動作とリソースの制限を定義することもできます。 `azure-vote-hpa.yaml` という名前のマニフェスト ファイルの例を次に示します。

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-back-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-back
  targetCPUUtilizationPercentage: 50 # target CPU utilization

---

apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-front-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-front
  targetCPUUtilizationPercentage: 50 # target CPU utilization
```

`kubectl apply` を使用して、`azure-vote-hpa.yaml` マニフェスト ファイルに定義されているオートスケーラーを適用します。

```console
kubectl apply -f azure-vote-hpa.yaml
```

自動スケーラーの状態を見るには、次のように `kubectl get hpa` コマンドを使用します。

```
kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Azure Vote アプリの負荷が最低になって数分が経過すると、ポッド レプリカの数は自動的に 3 に減少します。 もう一度 `kubectl get pods` を実行すると、不要なポッドが削除されていることがわかります。

## <a name="manually-scale-aks-nodes"></a>AKS ノードの手動スケーリング

前のチュートリアルでコマンドを使って Kubernetes クラスターを作成した場合、そのクラスターには 2 つのノードがあります。 クラスターのコンテナー ワークロードを増減する場合は、ノードの数を手動で調整できます。

次の例では、*myAKSCluster* という名前の Kubernetes クラスターのノードの数を 3 に増やしています。 コマンドが完了するまでに数分かかります。

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

クラスターが正常にスケーリングされると、出力は次の例のようになります。

```output
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Kubernetes クラスターの異なるスケーリング機能を使いました。 以下の方法を学習しました。

> [!div class="checklist"]
> * アプリケーションを実行する Kubernetes ポッドを手動でスケーリングする
> * アプリのフロントエンドを実行する自動スケーリング ポッドを構成する
> * Kubernetes ノードを手動でスケーリングする

次のチュートリアルに進んで、Kubernetes でのアプリケーションの更新方法について学習してください。

> [!div class="nextstepaction"]
> [Kubernetes でアプリケーションを更新する][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#deployment
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/#metrics-server

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
