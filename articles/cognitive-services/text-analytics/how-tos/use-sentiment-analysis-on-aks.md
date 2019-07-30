---
title: Azure Kubernetes Services を実行する
titleSuffix: Text Analytics - Azure Cognitive Services
description: Text Analytics コンテナーを感情分析イメージと一緒に Azure Kubernetes Services にデプロイし、それを Web ブラウザーでテストします。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: a419ed3b9c0d2c4db9c552642dc5c662786f6730
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561247"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-services-aks"></a>Azure Kubernetes Services (AKS) への感情分析コンテナーのデプロイ

Cognitive Services [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) コンテナーを感情分析イメージと一緒に Azure Kubernetes Services (AKS) にデプロイする方法について説明します。 この手順では、Text Analytics リソースの作成、関連付けられている感情分析イメージの作成、およびこの 2 つのオーケストレーションをブラウザーから実行する機能について例示します。 コンテナーを使用することで、開発者の関心をインフラストラクチャの管理から切り離し、アプリケーション開発に専念させることができます。

## <a name="prerequisites"></a>前提条件

この手順には、ローカルでインストールして実行する必要があるいくつかのツールが必要です。 Azure Cloud Shell は使用しないでください。

* Azure サブスクリプションを使用してください。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* テキスト エディター。たとえば、[Visual Studio Code](https://code.visualstudio.com/download)。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) をインストールします。
* [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) をインストールします。
* 適切な価格レベルの Azure リソース。 すべての価格レベルでこのコンテナーを使用するわけではありません。
    * F0 または Standard 価格レベルのみの **Text Analytics** リソース。
    * S0 価格レベルの **Cognitive Services** リソース。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Kubernetes Services (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-text-analytics-container-to-an-aks-cluster"></a>AKS クラスターへの Text Analytics コンテナーのデプロイ

1. Azure CLI を開き、Azure にログインします

    ```azurecli
    az login
    ```

1. AKS クラスターにサインインします (`your-cluster-name` と `your-resource-group` を適切な値に置き換えます)

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    このコマンドを実行すると、次のようなメッセージがレポートされます。

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Azure アカウントに利用可能な複数のサブスクリプションがあり、`az aks get-credentials` コマンドでエラーが返される場合、よくある問題は、間違ったサブスクリプションを使用していることです。 Azure CLI セッションのコンテキストを、リソースを作成したときと同じサブスクリプションを使用するように設定して、やり直してください。
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 任意のテキスト エディターを開きます (この例では __Visual Studio Code__)。

    ```azurecli
    code .
    ```

1. テキスト エディターで _sentiment.yaml_ という名前の新しいファイルを作成し、以下の YAML を貼り付けます。 必ず `billing/value` と `apikey/value` を独自の値に置き換えてください。

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
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # < Your endpoint >
            - name: apikey
              value: # < Your API Key >
     
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

1. このファイルを保存して、テキスト エディターを閉じます。
1. ターゲットとして _sentiment.yaml_ を指定して Kubernetes `apply` コマンドを実行します。

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    コマンドがデプロイ構成を正常に適用した後、次のようなメッセージが出力されます。

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. POD がデプロイされたことを確認します。

    ```console
    kubectl get pods
    ```

    POD の実行状態が出力されます。

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. サービスが利用できることを確認し、IP アドレスを取得します。

    ```console
    kubectl get services
    ```

    POD の "_センチメント_" サービスの実行状態が出力されます。

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>次の手順

* さらに [Cognitive Services コンテナー](../../cognitive-services-container-support.md)を使用する
* [Text Analytics 接続済みサービス](../vs-text-connected-service.md)を使用する
