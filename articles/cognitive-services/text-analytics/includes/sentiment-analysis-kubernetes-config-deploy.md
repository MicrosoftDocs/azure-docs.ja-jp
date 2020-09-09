---
title: 感情分析 Kubernetes の構成とデプロイの手順
titleSuffix: Azure Cognitive Services
description: 感情分析 Kubernetes の構成とデプロイの手順
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b43299974034f55b57b86191b3556c3d5c2ee83b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877837"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>感情分析コンテナーを AKS クラスターにデプロイする

1. Azure CLI を開き、Azure にサインインします。

    ```azurecli
    az login
    ```

1. AKS クラスターにサインインします。 `your-cluster-name` と `your-resource-group` を、適切な値に置き換えます。

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    このコマンドを実行すると、次のようなメッセージが表示されます。

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Azure アカウントに利用可能な複数のサブスクリプションがあり、`az aks get-credentials` コマンドでエラーが返される場合、よくある問題は、間違ったサブスクリプションを使用していることです。 Azure CLI セッションのコンテキストを、リソースを作成したときと同じサブスクリプションを使用するように設定して、やり直してください。
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 任意のテキスト エディターを開きます。 この例では、Visual Studio Code を使います。

    ```console
    code .
    ```

1. テキスト エディターで "*sentiment.yaml*" という名前の新しいファイルを作成し、以下の YAML を貼り付けます。 必ず `billing/value` と `apikey/value` を独自の情報に置き換えてください。

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. ファイルを保存して、テキスト エディターを閉じます。
1. ターゲットとして *sentiment.yaml* ファイルを指定して Kubernetes `apply` コマンドを実行します。

    ```console
    kubectl apply -f sentiment.yaml
    ```

    コマンドによってデプロイ構成が正常に適用された後、次の出力のようなメッセージが表示されます。

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. ポッドがデプロイされたことを確認します。

    ```console
    kubectl get pods
    ```

    ポッドの実行状態の出力:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. サービスが利用できることを確認し、IP アドレスを取得します。

    ```console
    kubectl get services
    ```

    ポッドの "*センチメント*" サービスの実行状態の出力:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
