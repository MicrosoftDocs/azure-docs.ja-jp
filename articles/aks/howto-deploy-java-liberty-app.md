---
title: Azure Kubernetes Service (AKS) クラスターに、Open Liberty または WebSphere Liberty を使用する Java アプリケーションをデプロイする
description: Azure Kubernetes Service (AKS) クラスターに、Open Liberty または WebSphere Liberty を使用する Java アプリケーションをデプロイします。
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: java, jakartaee, javaee, microprofile, open-liberty, websphere-liberty, aks, kubernetes
ms.openlocfilehash: d0e6f2fea6894378da736ba83a90ee28402ec7f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100007136"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) クラスターに、Open Liberty または WebSphere Liberty を使用する Java アプリケーションをデプロイする

この記事では、次の方法を示します。  
* Open Liberty または WebSphere Liberty ランタイムで、Java、Java EE、Jakarta EE、または MicroProfile アプリケーションを実行します。
* Open Liberty コンテナー イメージを使用して、アプリケーションの Docker イメージを構築します。
* Open Liberty オペレーターを使用して、コンテナー化されたアプリケーションを AKS クラスターにデプロイします。   

Open Liberty オペレーターにより、Kubernetes クラスターで実行されるアプリケーションのデプロイと管理が簡単になります。 Open Liberty オペレーターにより、トレースやダンプの収集など、より高度な操作を実行することもできます。 

Open Liberty の詳細については、[Open Liberty プロジェクトのページ](https://openliberty.io/)を参照してください。 IBM WebSphere Liberty の詳細については、[WebSphere Liberty の製品ページ](https://www.ibm.com/cloud/websphere-liberty)を参照してください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* この記事では、Azure CLI の最新バージョンが必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。
* このガイドのコマンドを (Azure Cloud Shell ではなく) ローカルで実行する場合:
  * Unix のようなオペレーティング システム (Ubuntu、macOS、Linux 用 Windows サブシステムなど) がインストールされているローカル マシンを準備します。
  * Java SE 実装 (たとえば [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)) をインストールします。
  * [Maven](https://maven.apache.org/download.cgi) 3.5.0 以上をインストールします。
  * お使いの OS 用の [Docker](https://docs.docker.com/get-docker/) をインストールします。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループは、Azure リソースが展開され管理される論理グループです。  

[az group create](/cli/azure/group#az_group_create) コマンドを使用して、*eastus* の場所に *java-liberty-project* というリソース グループを作成します。 このリソース グループは、後で Azure Container Registry (ACR) インスタンスと AKS クラスターを作成するために使用されます。 

```azurecli-interactive
RESOURCE_GROUP_NAME=java-liberty-project
az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-an-acr-instance"></a>ACR インスタンスを作成する

[az acr create](/cli/azure/acr#az_acr_create) コマンドを使用して ACR インスタンスを作成します。 次の例では、*youruniqueacrname* という名前の ACR インスタンスを作成します。 *youruniqueacrname* が Azure 内で一意であることを確認します。

```azurecli-interactive
REGISTRY_NAME=youruniqueacrname
az acr create --resource-group $RESOURCE_GROUP_NAME --name $REGISTRY_NAME --sku Basic --admin-enabled
```

しばらくすると、次のものを含む JSON 出力が表示されます。

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>ACR インスタンスに接続する

イメージをプッシュする前に、ACR インスタンスにサインインする必要があります。 次のコマンドを実行して、接続を確認します。

```azurecli-interactive
LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

ACR インスタンスに正常にログインした場合は、コマンド出力の最後に `Login Succeeded` が表示されます。

## <a name="create-an-aks-cluster"></a>AKS クラスターを作成する

AKS クラスターを作成するには、[az aks create](/cli/azure/aks#az_aks_create) コマンドを使用します。 次の例では、*myAKSCluster* という名前のクラスターを 1 つのノードで作成します。 これは完了までに数分かかる場合があります。

```azurecli-interactive
CLUSTER_NAME=myAKSCluster
az aks create --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --node-count 1 --generate-ssh-keys --enable-managed-identity
```

数分後、コマンドが完了し、次のものを含むクラスターに関する情報が JSON 形式で返されます。

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>AKS クラスターに接続する

Kubernetes クラスターを管理するには、Kubernetes のコマンドライン クライアントである [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) を使用します。 Azure Cloud Shell を使用している場合、`kubectl` は既にインストールされています。 `kubectl` をローカルにインストールするには、[az aks install-cli](/cli/azure/aks#az_aks_install_cli) コマンドを使用します。

```azurecli-interactive
az aks install-cli
```

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials](/cli/azure/aks#az_aks_get_credentials) コマンドを使用します。 このコマンドは、資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --overwrite-existing
```

> [!NOTE]
> 上記のコマンドは、[Kubernetes 構成ファイル](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)の既定の場所 (`~/.kube/config`) を使用します。 *--file* を使用すると、Kubernetes 構成ファイルに対して別の場所を指定できます。

クラスターへの接続を確認するには、クラスター ノードの一覧を返す [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) コマンドを使用します。

```azurecli-interactive
kubectl get nodes
```

次の出力例は、前の手順で作成した単一ノードを示しています。 ノードの状態が "*準備完了*" であることを確認します。

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>Open Liberty オペレーターをインストールする

クラスターを作成して接続したら、次のコマンドを実行して [Open Liberty オペレーター](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.0)をインストールします。

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>アプリケーション イメージをビルドする

AKS クラスターに Liberty アプリケーションをデプロイして実行するには、[Open Liberty コンテナー イメージ](https://github.com/OpenLiberty/ci.docker) または [WebSphere Liberty コンテナー イメージ](https://github.com/WASdev/ci.docker)を使用して、アプリケーションを Docker イメージとしてコンテナー化します。

1. このガイドのサンプル コードをクローンします。 サンプルは [GitHub](https://github.com/Azure-Samples/open-liberty-on-aks) にあります。
1. ディレクトリをローカル クローンの `javaee-app-simple-cluster` に変更します。
1. `mvn clean package` を実行してアプリケーションをパッケージ化します。
1. `mvn liberty:dev` を実行してアプリケーションをテストします。 成功すると、コマンドの出力に `The defaultServer server is ready to run a smarter planet.` が表示されます。 `CTRL-C` を使用してアプリケーションを停止します。
1. 以下のいずれかのコマンドを実行してアプリケーション イメージをビルドし、ACR インスタンスにプッシュします。
   * 軽量のオープン ソース Java™ ランタイムとして Open Liberty を使用する場合は、Open Liberty 基本イメージを使用してビルドします。

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME .
     ```

   * Open Liberty の商用バージョンを使用する場合は、WebSphere Liberty 基本イメージを使用してビルドします。

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>AKS クラスターにアプリケーションをデプロイする

次の手順に従って、AKS クラスターに Liberty アプリケーションをデプロイします。

1. AKS クラスターが ACR インスタンスからイメージをプルするための認証を受けるように、プル シークレットを作成します。

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. 現在の作業ディレクトリがローカル クローンの `javaee-app-simple-cluster` であることを確認します。
1. 次のコマンドを実行して、3 つのレプリカを持つ Liberty アプリケーションを AKS クラスターにデプロイします。 コマンドの出力もインラインで表示されます。

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-app-simple-cluster"
   cat openlibertyapplication.yaml | sed -e "s/\${Container_Registry_URL}/${LOGIN_SERVER}/g" | sed -e "s/\${REPLICAS}/3/g" | kubectl apply -f -

   openlibertyapplication.openliberty.io/javaee-app-simple-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication javaee-app-simple-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-app-simple-cluster   youruniqueacrname.azurecr.io/javaee-cafe-simple:1.0.0             True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment javaee-app-simple-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-app-simple-cluster   0/3     3            0           20s
   ```

1. [`READY`] 列の下に `3/3` が、[`AVAILABLE`] 列の下に `3` が表示されるまで待ってから、`CTRL-C` を使用して、`kubectl` ウォッチ プロセスを停止します。

### <a name="test-the-application"></a>アプリケーションをテストする

アプリケーションが実行されると、Kubernetes ロード バランサー サービスによってアプリケーション フロント エンドがインターネットに公開されます。 このプロセスの完了にはしばらく時間がかかることがあります。

進行状況を監視するには、[kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) コマンドを `--watch` 引数と一緒に使用します。

```azurecli-interactive
kubectl get service javaee-app-simple-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-app-simple-cluster   LoadBalancer   10.0.251.169   52.152.189.57   80:31732/TCP     68s
```

*EXTERNAL-IP* アドレスが *保留中* から実際のパブリック IP アドレスに変わったら、`CTRL-C` を使用して `kubectl` ウォッチ プロセスを停止します。

Web ブラウザーでサービスの外部 IP アドレス (上記の例では `52.152.189.57`) を開き、アプリケーションのホーム ページを表示します。 ページの左上にアプリケーション レプリカのポッド名が表示されることを確認します。 数分待ってページを最新の情報に更新すると、AKS クラスターによって提供される負荷分散のため、別のポッド名が表示されます。

:::image type="content" source="./media/howto-deploy-java-liberty-app/deploy-succeeded.png" alt-text="AKS に正常にデプロイされた Java Liberty アプリケーション":::

>[!NOTE]
> - 現在、アプリケーションでは HTTPS は使用されていません。 [独自の証明書を使用して TLS を有効にする](ingress-own-tls.md)ことをお勧めします。

## <a name="clean-up-the-resources"></a>リソースのクリーンアップ

Azure の課金を回避するには、不要なリソースをクリーンアップする必要があります。  クラスターが必要なくなったら、[az group delete](/cli/azure/group#az_group_delete) コマンドを使って、リソース グループ、コンテナー サービス、コンテナー レジストリ、およびすべての関連リソースを削除してください。

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME --yes --no-wait
```

## <a name="next-steps"></a>次のステップ

このガイドで使用された以下のリファレンスから、より多くのことを学習できます。

* [Azure Kubernetes Service](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Open Liberty](https://openliberty.io/)
* [Open Liberty オペレーター](https://github.com/OpenLiberty/open-liberty-operator)
* [Open Liberty サーバーの構成](https://openliberty.io/docs/ref/config/)
* [Liberty Maven プラグイン](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Open Liberty コンテナー イメージ](https://github.com/OpenLiberty/ci.docker)
* [WebSphere Liberty コンテナー イメージ](https://github.com/WASdev/ci.docker)
