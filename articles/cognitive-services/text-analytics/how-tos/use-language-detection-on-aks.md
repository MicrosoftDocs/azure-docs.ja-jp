---
title: Kubernetes Service の実行
titleSuffix: Text Analytics - Azure Cognitive Services
description: 実行するサンプルを使って言語検出コンテナーを Azure Kubernetes Service にデプロイし、Web ブラウザーでテストします。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 5486cfc376447549cd8a9f91743e2d930fc2b4c6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454792"
---
# <a name="deploy-the-language-detection-container-to-azure-kubernetes-service"></a>Azure Kubernetes Service に言語検出コンテナーをデプロイする

言語検出コンテナーをデプロイする方法について説明します。 この手順では、ローカルの Docker コンテナーを作成し、コンテナーを独自のプライベート コンテナー レジストリにプッシュし、Kubernetes クラスターでコンテナーを実行して、Web ブラウザーでテストする方法を示します。 コンテナーを使用することで、開発者の関心をインフラストラクチャの管理から切り離し、アプリケーション開発に専念させることができます。

## <a name="prerequisites"></a>前提条件

この手順には、ローカルでインストールして実行する必要があるいくつかのツールが必要です。 Azure Cloud Shell は使用しないでください。

* Azure サブスクリプションを使用してください。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* この手順で使用する[サンプル](https://github.com/Azure-Samples/cognitive-services-containers-samples)のクローンを作成できるよう、オペレーティング システム用の [Git](https://git-scm.com/downloads) を使用してください。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [Docker エンジン](https://www.docker.com/products/docker-engine)。Docker CLI がコンソール ウィンドウで動作することを確認します。
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe)。
* 適切な価格レベルの Azure リソース。 すべての価格レベルでこのコンテナーを使用するわけではありません。
    * F0 または Standard 価格レベルのみの **Text Analytics** リソース。
    * S0 価格レベルの **Cognitive Services** リソース。

## <a name="running-the-sample"></a>サンプルの実行

この手順では、言語検出用の Cognitive Services コンテナーのサンプルを読み込んで実行します。 このサンプルには、クライアント アプリケーション用と Cognitive Services コンテナー用の 2 つのコンテナーがあります。 これらの両方のイメージを Azure Container Registry にプッシュする必要があります。 独自のレジストリに設定したら、これらのイメージにアクセスしてコンテナーを実行する Azure Kubernetes Service を作成します。 コンテナーの実行中に、**kubectl** CLI を使用してコンテナーのパフォーマンスを監視します。 HTTP 要求を使用してクライアント アプリケーションにアクセスし、結果を確認します。

![サンプルのコンテナーの実行に関する概念的な構想](../media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>サンプルのコンテナー

このサンプルには 2 つのコンテナー イメージがあり、その 1 つはフロントエンドの Web サイトのイメージです。 2 つ目のイメージは、テキストの検出された言語 (カルチャ) を返す言語検出コンテナーです。 完了したら、両方のコンテナーが外部 IP からアクセス可能になります。

### <a name="the-language-frontend-container"></a>言語フロントエンド コンテナー

この Web サイトは、言語検出エンドポイントの要求を行う独自のクライアント側アプリケーションに相当します。 この手順が完了したら、`http://<external-IP>/<text-to-analyze>` を使用してブラウザーで Web サイトのコンテナーにアクセスして、検出された言語の文字の文字列を取得します。 この URL の例は `http://132.12.23.255/helloworld!` です。 ブラウザーでの結果は `English` です。

### <a name="the-language-container"></a>言語コンテナー

言語検出コンテナーは、この特定の手順では、外部の要求にアクセスできます。 コンテナーは、標準の Cognitive Services コンテナーに固有の言語検出 API を使用できるように、まったく変更されていません。

このコンテナーの場合、その API は言語検出の POST 要求です。 すべての Cognitive Services コンテナーと同様、コンテナーの詳細については、ホストされている Swagger 情報 (`http://<external-IP>:5000/swagger/index.html`) を参照してください。

ポート 5000 は、Cognitive Services コンテナーで使用される既定のポートです。

## <a name="create-azure-container-registry-service"></a>Azure Container Registry サービスを作成する

Azure Kubernetes Service にコンテナーをデプロイするには、コンテナー イメージがアクセス可能である必要があります。 このイメージをホストする、独自の Azure Container Registry サービスを作成します。

1. Azure CLI にサインインする

    ```azurecli
    az login
    ```

1. `cogserv-container-rg` という名前のリソース グループを作成して、この手順で作成したすべてのリソースを保持します。

    ```azurecli
    az group create --name cogserv-container-rg --location westus
    ```

1. ご自分の名前とその後に `registry` が続く形式 (`pattyregistry` など) で、独自の Azure Container Registry を作成します。 名前にダッシュや下線の文字を使用しないでください。

    ```azurecli
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    結果を保存して、**loginServer** プロパティを取得します。 これは、後で `language.yml` ファイルで使用する、ホストされているコンテナーのアドレスの一部となります。

    ```console
    > az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. コンテナー レジストリにサイン インします。 レジストリにイメージをプッシュする前に、ログインする必要があります。

    ```azurecli
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Web サイトの Docker イメージを取得する

1. この手順で使用するサンプル コードは、Cognitive Services コンテナーのサンプル リポジトリにあります。 リポジトリを複製して、サンプルのローカル コピーを作成します。

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    リポジトリがローカル コンピューターに作成されたら、[\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) ディレクトリで Web サイトを検索します。 この Web サイトは、言語検出コンテナーでホストされる言語検出 API を呼び出すクライアント アプリケーションとして機能します。  

1. この Web サイトの Docker イメージをビルドします。 次のコマンドを実行するときに、Dockerfile がある [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) ディレクトリにコンソールがあることを確認します。

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    コンテナー レジストリのバージョンを追跡するには、バージョン形式 (`v1` など) を含むタグを追加します。 

1. コンテナー レジストリにイメージをプッシュします。 これには数分かかることがあります。 

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    `unauthorized: authentication required` エラーが表示された場合、`az acr login --name <your-container-registry-name>` コマンドを使用してログインします。 

    プロセスが完了すると、結果は次の例のようになります。

    ```console
    > docker push pattyregistry.azurecr.io/language-frontend:v1
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>言語検出 Docker イメージを取得する 

1. ローカル コンピューターに最新バージョンの Docker イメージをプルします。 これには数分かかることがあります。 このコンテナーの新しいバージョンがある場合は、値を `1.1.006770001-amd64-preview` から新しいバージョンに変更します。 

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. コンテナー レジストリを含むイメージをタグ付けします。 最新バージョンを検索し、より新しいバージョンがある場合はバージョン `1.1.006770001-amd64-preview` を置き換えます。 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. コンテナー レジストリにイメージをプッシュします。 これには数分かかることがあります。 

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>コンテナー レジストリの資格情報を取得する

次の手順は、この手順で後で作成する Azure Kubernetes Service で、コンテナー レジストリへの接続に必要な情報を取得するために必要です。

1. サービス プリンシパルを作成します。

    ```azurecli
    az ad sp create-for-rbac --skip-assignment
    ```

    手順 3 で割り当てたパラメーター `<appId>` の結果の値 `appId` を保存します。 次のセクションの client-secret パラメーター `<client-secret>` のために `password` を保存します。

    ```console
    > az ad sp create-for-rbac --skip-assignment
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. コンテナー レジストリ ID を取得します。

    ```azurecli
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    次の手順で、スコープ パラメーター値、`<acrId>` の出力を保存します。 次のように表示されます。

    ```console
    > az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    このセクションの手順 3 の完全な値を保存します。 

1. コンテナー イメージに格納されているイメージを使用するために、AKS クラスターに適切なアクセス権を付与するには、ロールの割り当てを作成します。 `<appId>` と `<acrId>` を、前の 2 つの手順で収集した値に置き換えます。

    ```azurecli
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Azure Kubernetes Service を作成する

1. Kubernetes クラスターを作成します。 name パラメーターを除き、すべてのパラメーター値は前のセクションと同じです。 作成したユーザーとその目的を示す名前 (`patty-kube` など) を選択します。 

    ```azurecli
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    この手順には数分かかることがあります。 結果は次のとおりです。 

    ```console
    > az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    サービスが作成されますが、Web サイトのコンテナーや言語検出コンテナーはまだありません。  

1. Kubernetes クラスターの資格情報を取得します。 

    ```azurecli
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Kubernetes サービスにオーケストレーションの定義を読み込む

このセクションでは、**kubectl** CLI を使用して Azure Kubernetes Service と通信します。 

1. オーケストレーションの定義を読み込む前に、**kubectl** がノードにアクセスできることを確認します。

    ```console
    kubectl get nodes
    ```

    応答は次のようになります。

    ```console
    > kubectl get nodes
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. 次のファイルをコピーし、`language.yml` という名前を付けます。 このファイルには、2 種類のコンテナー (`language-frontend` Web サイトのコンテナーと`language` 検出コンテナー) のそれぞれに、`service` セクションと `deployment` セクションがあります。 

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. 次の表に基づいて `language.yml` の言語フロントエンドのデプロイの行を変更して、独自のコンテナー レジストリのイメージ名、クライアント シークレット、およびテキスト分析の設定を追加します。

    言語フロントエンドのデプロイ設定|目的|
    |--|--|
    |行 32<br> `image` プロパティ|コンテナー レジストリ内のフロントエンド イメージのイメージの場所<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |行 44<br> `name` プロパティ|イメージの Container Registry シークレット (前のセクションでは `<client-secret>` と呼ばれています)。|

1. 次の表に基づいて `language.yml` の言語のデプロイの行を変更して、独自のコンテナー レジストリのイメージ名、クライアント シークレット、およびテキスト分析の設定を追加します。

    |言語のデプロイの設定|目的|
    |--|--|
    |行 78<br> `image` プロパティ|コンテナー レジストリ内の言語イメージのイメージの場所<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |行 95<br> `name` プロパティ|イメージの Container Registry シークレット (前のセクションでは `<client-secret>` と呼ばれています)。|
    |行 91<br> `apiKey` プロパティ|テキスト分析リソース キー|
    |行 92<br> `billing` プロパティ|テキスト分析リソースの課金エンドポイント。<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    **apiKey** と **課金エンドポイント**は、Kubernetes オーケストレーションの定義の一部として設定されているため、Web サイト コンテナーがこれらについて認識したり要求の一部として渡したりする必要はありません。 Web サイト コンテナーは、そのオーケストレーター名 `language` によって言語検出コンテナーを示します。 

1. このサンプルのオーケストレーションの定義ファイルを、`language.yml` を作成して保存したフォルダーから読み込みます。 

    ```console
    kubectl apply -f language.yml
    ```

    応答は次のとおりです。

    ```console
    > kubectl apply -f language.yml
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>コンテナーの外部 IP を取得する

2 つのコンテナーについて、`language-frontend` と `language` のサービスが実行中であることを確認し、外部 IP アドレスを取得します。 

```console
kubectl get all
```

```console
> kubectl get all
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

サービスの `EXTERNAL-IP` が保留中として表示される場合は、次の手順に進む前に、IP アドレスが表示されるまでコマンドを再実行します。 

## <a name="test-the-language-detection-container"></a>言語検出コンテナーをテストする

ブラウザーを開いて、前のセクションの `language` コンテナーの外部 IP (`http://<external-ip>:5000/swagger/index.html`) に移動します。 API の `Try it` 機能を使用して、言語検出エンドポイントをテストすることができます。 

![コンテナーの Swagger のドキュメントを表示する](../media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>クライアント アプリケーション コンテナーをテストする

`http://<external-ip>/helloworld` の形式を使用して、ブラウザーで URL を `language-frontend` コンテナーの外部 IP に変更します。 `helloworld` の英語のカルチャ テキスト は `English` と予測されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

クラスターの作業が終了したら、Azure リソース グループを削除します。 

```azure-cli
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>関連情報

* [Docker ユーザーのための kubectl](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>次の手順 

* さらに [Cognitive Services コンテナー](../../cognitive-services-container-support.md)を使用する
* [Text Analytics 接続済みサービス](../vs-text-connected-service.md)を使用する


<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->