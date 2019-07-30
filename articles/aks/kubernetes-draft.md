---
title: Draft を使用して Azure Kubernetes Service (AKS) で開発する
description: AKS と Azure Container Registry で Draft を使用します。
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303540"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>クイック スタート:Draft を使用して Azure Kubernetes Service (AKS) で開発する

Draft は、Kubernetes クラスターでアプリケーション コンテナーをパッケージ化して実行する際に役立つオープン ソース ツールです。 Draft を使用すると、コードの変更が発生したときに、バージョン コントロールに変更をコミットすることなく、アプリケーションを Kubernetes にすばやく再デプロイできます。 Draft の詳細については、[GitHub の Draft のドキュメント][draft-documentation]を参照してください。

この記事では、Draft パッケージを使用して、AKS 上でアプリケーションを実行する方法を示します。


## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free)を作成できます。
* [Azure CLI がインストールされていること](/cli/azure/install-azure-cli?view=azure-cli-latest)。
* Docker がインストールされ構成されていること。 Docker では、[Mac][docker-for-mac]、, [Windows][docker-for-windows]、または [Linux][docker-for-linux] システム上に Docker を構成するパッケージが提供されています。
* [Helm がインストールされていること](https://github.com/helm/helm/blob/master/docs/install.md)。
* [Draft がインストールされていること][draft-documentation]。

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service クラスターを作成する

AKS クラスターを作成します。 下記のコマンドを使用すると、MyResourceGroup というリソース グループと MyAKS という AKS クラスターが作成されます。

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Azure Container Registry を作成する
ご利用の AKS クラスターで Draft を使用して自分のアプリケーションを実行するには、お使いのコンテナー イメージを格納するための Azure コンテナー レジストリが必要です。 次の例では、[az acr create][az-acr-create] を使用して、*MyResourceGroup* リソース グループ内に *Basic* SKU を使用する *MyDraftACR* という名前の ACR が作成されます。 独自の一意のレジストリ名を指定する必要があります。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 *Basic* SKU は、ストレージとスループットのバランスが取れた、開発目的のコスト最適化されたエントリ ポイントです。

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

出力は次の例のようになります。 後の手順で使用するために、自分の ACR 用の *loginServer* 値を書き留めておきます。 次の例では、*mydraftacr.azurecr.io* が *MyDraftACR* 用の *loginServer* になります。

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
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


Draft で ACR インスタンスを使用するには、まずサインインする必要があります。 サインインするには [az acr login][az-acr-login] コマンドを使用します。 次の例では、*MyDraftACR* という名前の ACR にサインインします。

```azurecli
az acr login --name MyDraftACR
```

このコマンドは、完了すると *"Login Succeeded (ログインに成功しました)"* というメッセージを返します。

## <a name="create-trust-between-aks-cluster-and-acr"></a>AKS クラスターと ACR 間に信頼を作成する

ご利用の AKS クラスターでは、自分の ACR にアクセスしてコンテナー イメージをプルして実行する必要もあります。 信頼を確立することで、ACR から AKS へのアクセスが許可されます。 AKS クラスターと ACR レジストリ間の信頼を確立するには、AKS クラスターが ACR レジストリにアクセスするために使用する Azure Active Directory サービス プリンシパルのアクセス許可を付与します。 次のコマンドでは、*MyResourceGroup* 内の *MyAKS* クラスターのサービス プリンシパルに対して、*MyResourceGroup* 内の *MyDraftACR* ACR へのアクセス許可が付与されます。

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

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

## <a name="create-a-service-account-for-helm"></a>Helm 用のサービス アカウントを作成する

RBAC が有効になった AKS クラスターに Helm をデプロイする前に、Tiller サービスのサービス アカウントとロール バインディングが必要になります。 RBAC 対応クラスター内の Helm/Tiller のセキュリティ保護の詳細については、[Tiller、名前空間、および RBAC][tiller-rbac] に関する記事を参照してください。 ご利用の AKS クラスターが RBAC に対応していない場合は、この手順をスキップしてください。

`helm-rbac.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

`kubectl apply` コマンドを使って、サービス アカウントとロール バインディングを作成します。

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Helm の構成
基本的な Tiller を AKS クラスターにデプロイするには、[helm init][helm-init] コマンドを使用します。 ご利用のクラスターが RBAC に対応していない場合は、`--service-account` 引数と値を削除します。

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Draft の構成

お使いのローカル コンピューター上に Draft が構成されていない場合は、`draft init` を実行します。

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

自分の ACR の *loginServer* を使用して Draft を構成する必要もあります。 次のコマンドでは、`draft config set` を使用して、`mydraftacr.azurecr.io` をレジストリとして使用しています。

```console
draft config set registry mydraftacr.azurecr.io
```

自分の ACR を使用するように Draft が構成され、Draft で自分の ACR にコンテナー イメージをプッシュすることができます。 ご利用の AKS クラスター内で Draft によって自分のアプリケーションが実行される際に、ACR レジストリに対してプルまたはプッシュを実行するときにパスワードもシークレットも必要ありません。 ご利用の AKS クラスターと自分の ACR の間に信頼が作成されているため、Azure Active Directory を使用して Azure Resource Manager レベルで認証が発生します。

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード

このクイックスタートでは、[Draft GitHub リポジトリのサンプル java アプリケーション][example-java]が使用されます。 GitHub からアプリケーションを複製し、`draft/examples/example-java/` ディレクトリに移動します。

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Draft を使用してサンプル アプリケーションを実行する

`draft create` コマンドを使用して、アプリケーションを準備します。

```console
draft create
```

このコマンドは、Kubernetes クラスターでアプリケーションを実行する際に使用されるアーティファクトを作成します。 これらの項目には、Dockerfile、Helm チャート、および *draft.toml* ファイル (Draft 構成ファイル) が含まれます。

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

AKS クラスターでサンプル アプリケーションを実行するには、`draft up` コマンドを使用します。

```console
draft up
```

このコマンドでは、Dockerfile をビルドしてコンテナー イメージを作成し、イメージを自分の ACR にプッシュし、Helm グラフをインストールして AKS でアプリケーションが起動されます。 このコマンドの初回の実行では、コンテナー イメージのプッシュとプルに時間がかかる可能性があります。 基本レイヤーがキャッシュされれば、アプリケーションのデプロイにかかる時間は大幅に短縮されます。

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>お使いのローカル コンピューターから実行中のサンプル アプリケーションに接続する

アプリケーションをテストするには、`draft connect` コマンドを使用します。

```console
draft connect
```

このコマンドは、Kubernetes ポッド へのセキュリティで保護された接続をプロキシします。 完了すると、提供された URL でアプリケーションにアクセスできます。

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

ブラウザーで `localhost` URL を使用してアプリケーションに移動して、サンプル アプリケーションを確認します。 上記の例では、URL は `http://localhost:49804` です。 `Ctrl+c` を使用して接続を停止します。

## <a name="access-the-application-on-the-internet"></a>インターネットでアプリケーションにアクセスする

前の手順では、AKS クラスター内のアプリケーション ポッドへのプロキシ接続を作成しました。 アプリケーションを開発してテストするときに、アプリケーションをインターネットで使用できるようにすることもできます。 アプリケーションをインターネット上に公開するには、[LoadBalancer][kubernetes-service-loadbalancer] 型の Kubernetes サービスを作成できます。

*LoadBalancer* サービスを作成するには、`charts/example-java/values.yaml` を更新します。 *service.type* の値を、*ClusterIP* から *LoadBalancer* に変更します。

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

変更を保存し、ファイルを閉じます。`draft up` を実行して、アプリケーションを再実行します。

```console
draft up
```

サービスからパブリック IP アドレスが返されるまで数分かかります。 進行状況を監視するには、`kubectl get service` コマンドと *watch* パラメーターを使用します。

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

ブラウザーで *EXTERNAL-IP* を使用して自分のアプリケーションのロード バランサーに移動して、サンプル アプリケーションを確認します。 上記の例では、IP は `52.175.224.118` です。

## <a name="iterate-on-the-application"></a>アプリケーションの反復

変更をローカルに実行し、`draft up` を再実行することで、自分のアプリケーションを反復できます。

[ src/main/java/helloworld/Hello.java の 7 行目][example-java-hello-l7]に返されたメッセージを更新します。

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

`draft up` コマンドを実行して、アプリケーションを再デプロイします。

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

更新されたアプリケーションを確認するには、お使いのロード バランサーの IP アドレスにもう一度移動し、変更内容が表示されることを確認します。

## <a name="delete-the-cluster"></a>クラスターを削除する

クラスターが不要になったら、[az group delete][az-group-delete] コマンドを使用して、リソース グループ、AKS クラスター、コンテナー レジストリ、そこに格納されているコンテナー イメージ、および関連するすべてのリソースを削除します。

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> クラスターを削除したとき、AKS クラスターで使用される Azure Active Directory サービス プリンシパルは削除されません。 サービス プリンシパルを削除する手順については、[AKS のサービス プリンシパルに関する考慮事項と削除][sp-delete]に関するページを参照してください。

## <a name="next-steps"></a>次の手順

Draft の使用方法の詳細については、GitHub の Draft のドキュメントをご覧ください。

> [!div class="nextstepaction"]
> [Draft のドキュメント][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://docs.helm.sh/helm/#helm-init
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
