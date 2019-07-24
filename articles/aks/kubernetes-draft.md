---
title: AKS と Azure Container Registry で Draft を使用します。
description: AKS と Azure Container Registry で Draft を使用します。
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: zarhoads
ms.openlocfilehash: 462cfd6ec0a6b25f85dda0245dd4f5feed7cb712
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755657"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での Draft の使用

オープン ソース ツールである Draft を使用すると、アプリケーション コンテナーをパッケージ化して Kubernetes クラスターにデプロイできるため、開発サイクル (開発に重点を置いた "内部ループ") に集中できます。 Draft はコードの開発時に、バージョン コントロールにコミットする前に機能します。 Draft を使用すると、コードの変更が発生したときに、アプリケーションを Kubernetes にすばやく再デプロイできます。 Draft の詳細については、[GitHub の Draft のドキュメント][draft-documentation]を参照してください。

この記事では、AKS で Draft と Kubernetes クラスターを使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事で詳しく説明する手順では、AKS クラスターを作成済みで、そのクラスターとの `kubectl` 接続が確立されていることを前提としています。 これらの項目が必要な場合は、[AKS のクイック スタート][aks-quickstart]を参照してください。

Azure Container Registry (ACR) のプライベート Docker レジストリが必要です。 ACR インスタンスを作成する手順については、[Azure Container Registry のクイックスタート][acr-quickstart]をご覧ください。

Helm も AKS クラスターにインストールする必要があります。 Helm のインストールと構成方法については、「[Azure Kubernetes Service (AKS) での Helm の使用][aks-helm]」をご覧ください。

最後に、[Docker](https://www.docker.com) をインストールする必要があります。

## <a name="install-draft"></a>Draft のインストール

Draft CLI は、開発システムで実行され、Kubernetes クラスターへのコードのデプロイを可能にするクライアントです。 Draft CLI を Mac にインストールするには、`brew` を使用します。 その他のインストール オプションについては、[Draft のインストール ガイド][draft-documentation]をご覧ください。

> [!NOTE]
> バージョン 0.12 以前の Draft をインストールしている場合、`helm delete --purge draft` を使ってクラスターから Draft を削除してから、`rm -rf ~/.draft` を実行してローカルの構成を削除してください。 MacOS の場合は、`brew upgrade draft` を実行します。

```console
brew tap azure/draft
brew install draft
```

次に、`draft init` コマンドを使用して Draft を初期化します。

```console
draft init
```

## <a name="configure-draft"></a>Draft の構成

Draft によってコンテナー イメージをローカルにビルドした後、ローカル レジストリからデプロイする (Minikube などを使用する) か、使用するイメージ レジストリを指定します。 この記事では Azure Container Registry (ACR) を使用するため、AKS クラスターと ACR レジストリの間の信頼関係を確立した後、コンテナー イメージを ACR にプッシュするように Draft を構成する必要があります。

### <a name="create-trust-between-aks-cluster-and-acr"></a>AKS クラスターと ACR 間に信頼を作成する

AKS クラスターと ACR レジストリ間の信頼を確立するには、AKS クラスターが ACR レジストリにアクセスするために使用する Azure Active Directory サービス プリンシパルのアクセス許可を付与します。 次のコマンドを、独自の `<resourceGroupName>` を指定し、`<aksName>` を AKS クラスターの名前に置き換え、`<acrName>` を ACR レジストリの名前に置き換えて実行します。

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group <resourceGroupName> --name <aksName> --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group <resourceGroupName> --name <acrName> --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

ACR にアクセスする手順の詳細については、[ACR の認証](../container-registry/container-registry-auth-aks.md)に関する記事を参照してください。

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>ACR にプッシュされ ACR からデプロイされるように Draft を構成する

AKS と ACR 間の信頼関係を確立したら、AKS クラスターからの ACR の使用を有効にします。

1. Draft の構成の *registry* 値を設定します。 次のコマンドを、`<acrName>` を ACR レジストリの名前に置き換えて実行します。

    ```console
    draft config set registry <acrName>.azurecr.io
    ```

1. [az acr login][az-acr-login] で ACR レジストリにログオンします。

    ```azurecli
    az acr login --name <acrName>
    ```

AKS と ACR の間で信頼関係が作成されているため、ACR レジストリに対してプルまたはプッシュを実行するときにパスワードもシークレットも必要ありません。 認証は、Azure Active Directory を使用して Azure Resource Manager レベルで実行されます。

## <a name="run-an-application"></a>アプリケーションの実行

Draft の動作を確認するために、[Draft リポジトリ][draft-repo]からサンプル アプリケーションをデプロイしてみましょう。 最初に、リポジトリを複製します。

```console
git clone https://github.com/Azure/draft
```

Java の examples ディレクトリに移動します。

```console
cd draft/examples/example-java/
```

`draft create` コマンドを使用して、プロセスを開始します。 このコマンドは、Kubernetes クラスターでアプリケーションを実行する際に使用されるアーティファクトを作成します。 これらの項目には、Dockerfile、Helm チャート、および *draft.toml* ファイル (Draft 構成ファイル) が含まれます。

```
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

AKS クラスターでサンプル アプリケーションを実行するには、`draft up` コマンドを使用します。 このコマンドは、コンテナー イメージを作成するように Dockerfile をビルドして、イメージを ACR にプッシュし、最後に Helm チャートをインストールして AKS でアプリケーションを起動します。

このコマンドの初回の実行では、コンテナー イメージのプッシュとプルに時間がかかる可能性があります。 基本レイヤーがキャッシュされれば、アプリケーションのデプロイにかかる時間は大幅に短縮されます。

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

Docker イメージのプッシュで問題が発生した場合は、[az acr login][az-acr-login] で ACR レジストリに正常にログインしていることを確認した後、`draft up` コマンドをもう一度実行します。

## <a name="test-the-application-locally"></a>ローカルでアプリケーションをテストする

アプリケーションをテストするには、`draft connect` コマンドを使用します。 このコマンドは、Kubernetes ポッド へのセキュリティで保護された接続をプロキシします。 完了すると、提供された URL でアプリケーションにアクセスできます。

> [!NOTE]
> コンテナー イメージがダウンロードされ、アプリケーションが起動するまで数分かかる可能性があります。 アプリケーションにアクセスしたときにエラーが発生した場合は、接続を再試行してください。

```
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

アプリケーションにアクセスするには、`draft connect` 出力に示されたアドレスとポートで Web ブラウザーを開きます (`http://localhost:49804` など)。 

![Draft で実行されたサンプル Java アプリ](media/kubernetes-draft/sample-app.png)

プロキシ接続を停止するには、`Control+C` を使用します。

> [!NOTE]
> `draft up --auto-connect` コマンドを使用して、アプリケーションをビルドしてデプロイした後、最初に実行されるコンテナーにすぐに接続することもできます。

## <a name="access-the-application-on-the-internet"></a>インターネットでアプリケーションにアクセスする

前の手順では、AKS クラスター内のアプリケーション ポッドへのプロキシ接続を作成しました。 アプリケーションを開発してテストするときに、アプリケーションをインターネットで使用できるようにすることもできます。 アプリケーションをインターネット上に公開するには、[LoadBalancer][kubernetes-service-loadbalancer] 型の Kubernetes サービスを作成するか、[イングレス コントローラー][kubernetes-ingress]を作成します。 *LoadBalancer* サービスを作成しましょう。

最初に、*LoadBalancer* 型のサービスを作成するように *values.yaml* Draft パックを更新します。

```console
vi charts/java/values.yaml
```

*service.type* プロパティを探し、次の要約された例に示すように、その値を *ClusterIP* から *LoadBalancer* に更新します。

```yaml
[...]
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
[...]
```

ファイルを保存して閉じた後、`draft up` を使用してアプリケーションを再実行します。

```console
draft up
```

サービスからパブリック IP アドレスが返されるまで数分かかります。 進行状況を監視するには、`kubectl get service` コマンドと *watch* パラメーターを使用します。

```console
kubectl get service --watch
```

最初は、サービスの *EXTERNAL-IP* は *pending* と表示されます。

```
NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
```

EXTERNAL-IP アドレスが *pending* から IP アドレスに変わったら、`Control+C` を使用して `kubectl` ウォッチ プロセスを停止します。

```
NAME                TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

アプリケーションを表示するには、`curl` を使用してロード バランサーの外部 IP アドレスを参照します。

```
$ curl 52.175.224.118

Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>アプリケーションの反復

Draft が構成され、アプリケーションが Kubernetes で実行中であり、コードを反復する準備が整いました。 更新されたコードをテストするときは、`draft up` コマンドを毎回実行して、実行中のアプリケーションを更新します。

この例では、Java サンプル アプリケーションを更新して、表示テキストを変更します。 *Hello.java* ファイルを開きます。

```console
vi src/main/java/helloworld/Hello.java
```

表示する出力テキストを *Hello World, I'm Java in AKS!* に更新します。

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
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

更新されたアプリケーションを表示するには、ロード バランサーの IP アドレスをもう一度 curl します。

```
$ curl 52.175.224.118

Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>次の手順

Draft の使用方法の詳細については、GitHub の Draft のドキュメントをご覧ください。

> [!div class="nextstepaction"]
> [Draft のドキュメント][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[draft-repo]: https://github.com/Azure/draft

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[kubernetes-ingress]: ./ingress-basic.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[az-acr-login]: /cli/azure/acr#az-acr-login
