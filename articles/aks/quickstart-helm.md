---
title: Helm を使用して Azure Kubernetes Service (AKS) で開発する
description: AKS と Azure Container Registry で Helm を使用して、クラスター内のアプリケーション コンテナーをパッケージ化して実行します。
services: container-service
author: zr-msft
ms.topic: article
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 4f5232920853908aa5ad714313ead201494caa0d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493080"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>クイック スタート:Helm を使用して Azure Kubernetes Service (AKS) で開発する

[Helm][helm] は、Kubernetes アプリケーションのライフサイクルをインストールおよび管理するのに役立つオープン ソースのパッケージ化ツールです。 *APT* や *Yum* などの Linux パッケージ マネージャーと同様に、Helm を使用すると、構成済みの Kubernetes リソースのパッケージである Kubernetes チャートが管理されます。

このクイックスタートでは、Helm を使用して、AKS でアプリケーションをパッケージ化して実行します。 Helm を使用した既存のアプリケーションのインストールの詳細については、[AKS での Helm を使用した既存のアプリケーションのインストール][helm-existing]に関する攻略ガイドを参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free)を作成できます。
* [Azure CLI がインストールされていること](/cli/azure/install-azure-cli)。
* [Helm v3 がインストールされていること][helm-install]。

## <a name="create-an-azure-container-registry"></a>Azure Container Registry を作成する
Helm を使用してご利用の AKS クラスターで自分のアプリケーションを実行するには、お使いのコンテナー イメージを Azure Container Registry (ACR) に格納する必要があります。 Azure 内で一意となる、5 から 50 文字の英数字を含むレジストリ名を指定します。 *Basic* SKU は、ストレージとスループットのバランスが取れた、開発目的のコスト最適化されたエントリ ポイントです。

下の例では、[az acr create][az-acr-create] を使用して、*MyResourceGroup* 内に *Basic* SKU を使用する *MyHelmACR* という名前の ACR を作成します。

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

出力は次の例のようになります。 後の手順で使用するために、自分の ACR 用の *loginServer* 値を書き留めておきます。 次の例では、*myhelmacr.azurecr.io* が *MyHelmACR* 用の *loginServer* になります。

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
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

## <a name="create-an-aks-cluster"></a>AKS クラスターを作成する

新しい AKS クラスターでは、お使いの ACR にアクセスしてコンテナー イメージをプルして実行する必要があります。 次のコマンドを使用します。
* *MyAKS* という名前の AKS クラスターを作成し、*MyHelmACR* をアタッチします。
* *MyAKS* クラスターに *MyHelmACR* ACR へのアクセスを許可します。


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>ご利用の AKS クラスターに接続する

Kubernetes クラスターにローカルで接続するには、Kubernetes のコマンドライン クライアントである [kubectl][kubectl] を使用します。 Azure Cloud Shell を使用している場合、`kubectl` は既にインストールされています。 

1. `az aks install-cli` コマンドを使用して、`kubectl` をローカルにインストールします。

    ```azurecli
    az aks install-cli
    ```

2. `az aks get-credentials` コマンドを使用して、Kubernetes クラスターに接続するように `kubectl` を構成します。 次のコマンド例では、*MyResourceGroup* の *MyAKS* という名前の AKS クラスター用の資格情報が取得されます。  

    ```azurecli
    az aks get-credentials --resource-group MyResourceGroup --name MyAKS
    ```

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード

このクイック スタートでは、[Azure Dev Spaces サンプル リポジトリの Node.js アプリケーションの例][example-nodejs]を使用します。 GitHub からアプリケーションを複製し、`dev-spaces/samples/nodejs/getting-started/webfrontend` ディレクトリに移動します。

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Dockerfile を作成する

以下のコマンドを使用して、新しい *Dockerfile* ファイルを作成します。

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>サンプル アプリケーションをビルドして ACR にプッシュする

上記の Dockerfile を使用して [az acr build][az-acr-build] コマンドを実行し、イメージをビルドしてレジストリにプッシュします。 コマンドの最後にある `.` では、Dockerfile の位置を設定します (この場合は現在のディレクトリです)。

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>Helm グラフを作成する

`helm create` コマンドを使用して Helm グラフを生成します。

```console
helm create webfrontend
```

*webfrontend/values.yaml* を更新します。
* 前の手順でメモしたレジストリの loginServer を *myhelmacr.azurecr.io* のように置き換えます。
* `image.repository` を `<loginServer>/webfrontend` に変更します。
* `service.type` を `LoadBalancer` に変更します。

次に例を示します。

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: myhelmacr.azurecr.io/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

*webfrontend/Chart.yaml* の`appVersion` を `v1` に更新します。 次に例を示します。

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Helm グラフを実行する

`helm install` コマンドを使用して、Helm グラフを使用してアプリケーションをインストールします。

```console
helm install webfrontend webfrontend/
```

サービスからパブリック IP アドレスが返されるまで数分かかります。 `kubectl get service` コマンドと `--watch` 引数を使用して、進行状況を監視します。

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

ブラウザーで `<EXTERNAL-IP>` を使用して自分のアプリケーションのロード バランサーに移動して、サンプル アプリケーションを確認します。

## <a name="delete-the-cluster"></a>クラスターを削除する

[az group delete][az-group-delete] コマンドを使用して、リソース グループ、AKS クラスター、コンテナー レジストリ、ACR に格納されているコンテナー イメージ、および関連するすべてのリソースを削除します。

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> クラスターを削除したとき、AKS クラスターで使用される Azure Active Directory サービス プリンシパルは削除されません。 サービス プリンシパルを削除する手順については、[AKS のサービス プリンシパルに関する考慮事項と削除][sp-delete]に関するページを参照してください。
> 
> マネージド ID を使用した場合、ID はプラットフォームによって管理されるので、削除する必要はありません。

## <a name="next-steps"></a>次のステップ

Helm の使用方法の詳細については、Helm のドキュメントを参照してください。

> [!div class="nextstepaction"]
> [Helm のドキュメント][helm-documentation]

[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
