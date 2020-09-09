---
title: Helm を使用して Azure Kubernetes Service (AKS) で開発する
description: AKS と Azure Container Registry で Helm を使用して、クラスター内のアプリケーション コンテナーをパッケージ化して実行します。
services: container-service
author: zr-msft
ms.topic: article
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: 0ca2d7ccc863e2208db1212ef3d3f10fa709d069
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407117"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>クイック スタート:Helm を使用して Azure Kubernetes Service (AKS) で開発する

[Helm][helm] は、Kubernetes アプリケーションのライフサイクルをインストールおよび管理するのに役立つオープン ソースのパッケージ化ツールです。 *APT* や *Yum* などの Linux パッケージ マネージャーと同様に、Helm は、構成済みの Kubernetes リソースのパッケージである Kubernetes チャートの管理に使用されます。

この記事では、Helm を使用して、AKS 上でアプリケーションをパッケージ化して実行する方法を示します。 Helm を使用した既存のアプリケーションのインストールの詳細については、「[AKS で Helm を使用して既存のアプリケーションをインストールする][helm-existing]」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free)を作成できます。
* [Azure CLI がインストールされていること](/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [Helm v3 がインストールされていること][helm-install]。

## <a name="create-an-azure-container-registry"></a>Azure Container Registry を作成する
ご利用の AKS クラスターで Helm を使用して自分のアプリケーションを実行するには、お使いのコンテナー イメージを格納するための Azure Container Registry が必要です。 次の例では、[az acr create][az-acr-create] を使用して、*MyResourceGroup* リソース グループ内に *Basic* SKU を使用する *MyHelmACR* という名前の ACR が作成されます。 独自の一意のレジストリ名を指定する必要があります。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 *Basic* SKU は、ストレージとスループットのバランスが取れた、開発目的のコスト最適化されたエントリ ポイントです。

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

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service クラスターを作成する

AKS クラスターを作成します。 次のコマンドは、MyAKS という名前の AKS クラスターを作成し、MyHelmACR をアタッチします。

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

AKS クラスターでは、お使いの ACR にアクセスしてコンテナー イメージをプルして実行する必要があります。 上記のコマンドでは、*MyAKS* クラスターに *MyHelmACR* ACR へのアクセスも許可します。

## <a name="connect-to-your-aks-cluster"></a>ご利用の AKS クラスターに接続する

お使いのローカル コンピューターから Kubernetes クラスターに接続するには、[kubectl][kubectl] (Kubernetes コマンドライン クライアント) を使用します。

Azure Cloud Shell を使用している場合、`kubectl` は既にインストールされています。 [az aks install-cli][] コマンドを使用してローカルにインストールすることもできます。

```azurecli
az aks install-cli
```

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials][] コマンドを使用します。 次の例では、*MyResourceGroup* の *MyAKS* という名前の AKS クラスター用の資格情報が取得されます。

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

以下を使用して、新しい *Dockerfile* ファイルを作成します。

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

[az acr build][az-acr-build] コマンドを使用して、前の手順で作成した Dockerfile を使用してイメージをビルドし、レジストリにプッシュします。 コマンドの最後にある `.` では、Dockerfile の位置を設定します。この場合は現在のディレクトリです。

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

*webfrontend/values.yaml* に以下の更新を加えます。 前の手順でメモしたレジストリの loginServer を *myhelmacr.azurecr.io* のように置き換えます。

* `image.repository` を `<loginServer>/webfrontend` に変更します。
* `service.type` を `LoadBalancer` に変更します。

次に例を示します。

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: *myhelmacr.azurecr.io*/webfrontend
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

サービスからパブリック IP アドレスが返されるまで数分かかります。 進行状況を監視するには、`kubectl get service` コマンドと *watch* パラメーターを使用します。

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

ブラウザーで `<EXTERNAL-IP>` を使用して自分のアプリケーションのロード バランサーに移動して、サンプル アプリケーションを確認します。

## <a name="delete-the-cluster"></a>クラスターを削除する

クラスターが不要になったら、[az group delete][az-group-delete] コマンドを使用して、リソース グループ、AKS クラスター、コンテナー レジストリ、そこに格納されているコンテナー イメージ、および関連するすべてのリソースを削除します。

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> クラスターを削除したとき、AKS クラスターで使用される Azure Active Directory サービス プリンシパルは削除されません。 サービス プリンシパルを削除する手順については、[AKS のサービス プリンシパルに関する考慮事項と削除][sp-delete]に関するページを参照してください。 マネージド ID を使用した場合、ID はプラットフォームによって管理されるので、削除する必要はありません。

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
