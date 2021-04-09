---
title: Azure Application Gateway のメトリックを使用した AKS ポッドの自動スケーリング
description: この記事では Application Gateway メトリックと Azure Kubernetes Metrics Adapter を使用して AKS バックエンド ポッドをスケーリングする方法について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a8f015085baa8fffa6f208e9d8dd749e397c76c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397435"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Application Gateway のメトリックを使用した AKS ポッドの自動スケーリング (ベータ版)

着信トラフィックが増えると、需要に応じてアプリケーションをスケールアップすることが重要になります。

次のチュートリアルでは、Application Gateway の `AvgRequestCountPerHealthyHost` メトリックを使用してアプリケーションをスケールアップする方法について説明します。 `AvgRequestCountPerHealthyHost` は、特定のバックエンド プールに送信された平均要求と、バックエンドの HTTP 設定の組み合わせを測定します。

ここでは、次の 2 つのコンポーネントを使用します。

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) - メトリックサーバーを介して Application Gateway メトリックを公開するために、メトリック アダプターを使用します。 Azure Kubernetes Metric Adapter は、Application Gateway イングレス コントローラーと同じく、Azure のオープンソース プロジェクトです。 
* [`Horizontal Pod Autoscaler`](../aks/concepts-scale.md#horizontal-pod-autoscaler) - Application Gateway メトリックを使用してスケーリング対象のデプロイを指定するために、HPA を使用します。

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Azure Kubernetes Metric Adapter の設定

1. まず、Azure AAD サービス プリンシパルを作成し、Application Gateway のリソース グループに対する `Monitoring Reader` アクセスに割り当てます。 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. 次に、上記で作成した AAD サービス プリンシパルを使用して、[`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) をデプロイします。

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principal created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. `appgw-request-count-metric` という名前の `ExternalMetric` リソースを作成します。 このリソースは、`myResourceGroup` リソース グループの `myApplicationGateway` リソースに対して `AvgRequestCountPerHealthyHost` メトリックを公開するようにメトリック アダプタに指示します。 `filter` フィールドを使用すると、Application Gateway で特定のバックエンド プールやバックエンド HTTP 設定を対象にすることができます。

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

これで、メトリック サーバーに要求を行い、新しいメトリックが公開されているかどうかを確認することができます。
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>新しいメトリックを使用してデプロイをスケールアップする

メトリック サーバーを介して `appgw-request-count-metric` を公開できるようになったら、[`Horizontal Pod Autoscaler`](../aks/concepts-scale.md#horizontal-pod-autoscaler) を使用して対象のデプロイをスケールアップすることができます。

次の例では、サンプル デプロイ `aspnet` を対象としています。 ポッドごとに `appgw-request-count-metric` > 200 の場合、最大 `10` ポッドまでスケールアップします。

対象のデプロイ名を置き換え、次の自動スケール構成を適用します。
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

apache ベンチのようなロード テスト ツールを使用してセットアップをテストします。
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>次のステップ
- [**イングレス コントローラーの問題のトラブルシューティング**](ingress-controller-troubleshoot.md): イングレス コントローラーに関する問題のトラブルシューティングを行います。