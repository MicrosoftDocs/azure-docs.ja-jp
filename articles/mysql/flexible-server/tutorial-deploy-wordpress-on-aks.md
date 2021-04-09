---
title: チュートリアル:Azure CLI を使用して MySQL フレキシブル サーバーで WordPress を AKS クラスターにデプロイする
description: Azure Database for MySQL - フレキシブル サーバーを使用して、WordPress をすばやく構築し、AKS にデプロイする方法について説明します。
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: mvc
ms.openlocfilehash: 6f2b3d9f38fc04428678a71c9942fc7aa2182dc8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217195"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>チュートリアル:Azure Database for MySQL - フレキシブル サーバーを使用して WordPress アプリを AKS にデプロイする

このクイックスタートでは、Azure CLI を使用して、Azure Database for MySQL - フレキシブル サーバー (プレビュー) で Azure Kubernetes Service (AKS) クラスターに WordPress アプリケーションをデプロイします。 
**[AKS](../../aks/intro-kubernetes.md)** は、クラスターをすばやくデプロイして管理できるマネージド Kubernetes サービスです。 **[Azure Database for MySQL - フレキシブル サーバー (プレビュー)](overview.md)** は、データベース管理機能と構成設定のよりきめ細かな制御と柔軟性を提供するように設計されたフル マネージド データベース サービスです。 フレキシブル サーバーは、現在プレビュー段階にあります。

> [!NOTE]
> - Azure Database for MySQL フレキシブル サーバーは現在、パブリック プレビュー段階にあります
> - このクイックスタートは、Kubernetes の概念、WordPress、MySQL.に関する基礎知識があることを前提としています。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI の最新バージョンが必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

> [!NOTE]
> このクイックスタートのコマンドを (Azure Cloud Shell ではなく) ローカルで実行する場合は、必ず管理者としてコマンドを実行してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループは、Azure リソースが展開され管理される論理グループです。 [az group create][az-group-create] コマンドを使用して、*eastus* に *wordpress-project* というリソース グループを作成しましょう。

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> リソース グループの場所は、リソース グループのメタデータが保存される場所です。 また、リソースの作成時に別のリージョンを指定しない場合に、Azure でリソースが実行される場所でもあります。

次の出力例では、正常に作成されたリソース グループが示されています。

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/wordpress-project",
  "location": "eastus",
  "managedBy": null,
  "name": "wordpress-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>AKS クラスターの作成

AKS クラスターを作成するには、[az aks create](/cli/azure/aks#az-aks-create) コマンドを使用します。 次の例では、*myAKSCluster* という名前のクラスターを 1 つのノードで作成します。 これは完了までに数分かかる場合があります。

```azurecli-interactive
az aks create --resource-group wordpress-project --name myAKSCluster --node-count 1 --generate-ssh-keys
```

数分後、コマンドが完了し、クラスターに関する情報が JSON 形式で返されます。

> [!NOTE]
> AKS クラスターを作成すると、AKS リソースを保存するための 2 つ目のリソース グループが自動的に作成されます。 「[AKS と一緒にリソース グループが 2 つ作成されるのはなぜでしょうか?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)」を参照してください。

## <a name="connect-to-the-cluster"></a>クラスターに接続する

Kubernetes クラスターを管理するには、Kubernetes のコマンドライン クライアントである [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) を使用します。 Azure Cloud Shell を使用している場合、`kubectl` は既にインストールされています。 `kubectl` をローカルにインストールするには、[az aks install-cli](/cli/azure/aks#az-aks-install-cli) コマンドを使用します。

```azurecli-interactive
az aks install-cli
```

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials](/cli/azure/aks#az-aks-get-credentials) コマンドを使用します。 このコマンドは、資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。

```azurecli-interactive
az aks get-credentials --resource-group wordpress-project --name myAKSCluster
```

> [!NOTE]
> 上記のコマンドは、[Kubernetes 構成ファイル](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)の既定の場所 (`~/.kube/config`) を使用します。 *--file* を使用すると、Kubernetes 構成ファイルに対して別の場所を指定できます。

クラスターへの接続を確認するには、クラスター ノードの一覧を返す [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) コマンドを使用します。

```azurecli-interactive
kubectl get nodes
```

次の出力例は、前の手順で作成した単一ノードを示しています。 ノードの状態が "*準備完了*" であることを確認します。

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - フレキシブル サーバーを作成する
[az mysql flexible-server create](/cli/azure/mysql/flexible-server) コマンドを使用して、フレキシブル サーバーを作成します。 次のコマンドでは、サービスの既定値と Azure CLI のローカル コンテキストからの値を使用してサーバーを作成します。

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

作成されたサーバーには、次の属性があります。
- サーバーが最初にプロビジョニングされたときに、新しい空のデータベース ```flexibleserverdb``` が作成されます。 このクイックスタートでは、このデータベースを使用します。
- 自動生成されたサーバー名、管理者ユーザー名、管理者パスワード、リソース グループ名 (ローカル コンテキストでまだ指定されていない場合)、およびリソース グループと同じ場所
- 残りのサーバー構成のサービスの既定値: コンピューティング レベル (バースト可能)、コンピューティング サイズ/SKU (B1MS)、バックアップの保持期間 (7 日間)、および MySQL のバージョン (5.7)
- public-access 引数を使用すると、パブリック アクセスがファイアウォール規則で保護されたサーバーを作成できます。 IP アドレスを指定して、クライアント マシンからのアクセスを許可するファイアウォール規則を追加します。
- このコマンドはローカル コンテキストを使用しているため、```eastus``` リージョンの ```wordpress-project``` リソース グループにサーバーが作成されます。


### <a name="build-your-wordpress-docker-image"></a>WordPress Docker イメージを作成する

[最新の WordPress](https://wordpress.org/download/) バージョンをダウンロードします。 プロジェクトの新しいディレクトリ ```my-wordpress-app``` を作成し、次の単純なフォルダー構造を使用します。

```
└───my-wordpress-app
    └───public
        ├───wp-admin
        │   ├───css
        . . . . . . .
        ├───wp-content
        │   ├───plugins
        . . . . . . .
        └───wp-includes
        . . . . . . .
        ├───wp-config-sample.php
        ├───index.php
        . . . . . . .
    └─── Dockerfile

```


```wp-config-sample.php``` の名前を ```wp-config.php``` に変更し、21 から 32 行目をこのコード スニペットに置き換えます。 次のコード スニペットは、Kubernetes マニフェスト ファイルからデータベース ホスト、ユーザー名、パスワードを読み取ります。

```php
//Using environment variables for DB connection information

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */

$connectstr_dbhost = getenv('DATABASE_HOST');
$connectstr_dbusername = getenv('DATABASE_USERNAME');
$connectstr_dbpassword = getenv('DATABASE_PASSWORD');

/** MySQL database name */
define('DB_NAME', 'flexibleserverdb');

/** MySQL database username */
define('DB_USER', $connectstr_dbusername);

/** MySQL database password */
define('DB_PASSWORD',$connectstr_dbpassword);

/** MySQL hostname */
define('DB_HOST', $connectstr_dbhost);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/** SSL*/
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
```

### <a name="create-a-dockerfile"></a>Dockerfile を作成する
新しい Dockerfile を作成し、次のコード スニペットをコピーします。 この Dockerfile では、PHP を使用して Apache Web サーバーをセットアップし、mysqli 拡張機能を有効にします。

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

### <a name="build-your-docker-image"></a>Docker イメージをビルドする
```cd``` コマンドを使用して、ターミナルの ```my-wordpress-app``` ディレクトリにいることを確認します。 次のコマンドを実行して、イメージをビルドします。

``` bash

docker build --tag myblog:latest .

```

[Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) または [Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md) にイメージをデプロイします。

> [!IMPORTANT]
>Azure Container Registry (ACR) を使用している場合は、```az aks update``` コマンドを実行して ACR アカウントを AKS クラスターに接続します。
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Kubernetes マニフェスト ファイルを作成する

Kubernetes のマニフェスト ファイルでは、どのコンテナー イメージを実行するかなど、クラスターの望ましい状態を定義します。 `mywordpress.yaml` という名前のマニフェスト ファイルを作成し、次の YAML 定義をコピーしましょう。

>[!IMPORTANT]
> - ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` を、実際の WordPress Docker イメージ名とタグに置き換えます (例: ```docker-hub-user/myblog:latest```)。
> - MySQL フレキシブル サーバーの ```SERVERNAME```、```YOUR-DATABASE-USERNAME```、```YOUR-DATABASE-PASSWORD``` で下記の ```env``` セクションを更新します。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-blog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-blog
  template:
    metadata:
      labels:
        app: wordpress-blog
    spec:
      containers:
      - name: wordpress-blog
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.mysql.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "flexibleserverdb"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - wordpress-blog
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: php-svc
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: wordpress-blog
```

## <a name="deploy-wordpress-to-aks-cluster"></a>WordPress を AKS クラスターにデプロイする
[kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) コマンドを使用してアプリケーションをデプロイし、ご利用の YAML マニフェストの名前を指定します。

```console
kubectl apply -f mywordpress.yaml
```

次の出力例は、正常に作成されたデプロイおよびサービスを示しています。

```output
deployment "wordpress-blog" created
service "php-svc" created
```

## <a name="test-the-application"></a>アプリケーションをテストする

アプリケーションが実行されると、Kubernetes サービスによってアプリケーション フロント エンドがインターネットに公開されます。 このプロセスが完了するまでに数分かかることがあります。

進行状況を監視するには、[kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) コマンドを `--watch` 引数と一緒に使用します。

```azurecli-interactive
kubectl get service wordpress-blog --watch
```

最初は、*wordpress-blog* サービスの *EXTERNAL-IP* が *pending* (保留中) として表示されます。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

*EXTERNAL-IP* アドレスが "*保留中*" から実際のパブリック IP アドレスに変わったら、`CTRL-C` を使用して `kubectl` ウォッチ プロセスを停止します。 次の出力例は、サービスに割り当てられている有効なパブリック IP アドレスを示しています。

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>WordPress を参照する

Web ブラウザーを開いてサービスの外部 IP アドレスにアクセスすると、WordPress のインストール ページが表示されます。

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="AKS および MySQL フレキシブル サーバーでの WordPress のインストールの成功":::

>[!NOTE]
> - 現在、WordPress サイトでは HTTPS は使用されていません。 [独自の証明書を使用して TLS を有効にする](../../aks/ingress-own-tls.md)ことをお勧めします。
> - クラスターの [HTTP ルーティング](../../aks/http-application-routing.md)を有効にすることができます。

## <a name="clean-up-the-resources"></a>リソースのクリーンアップ

Azure の課金を回避するには、不要なリソースをクリーンアップする必要があります。  クラスターが必要なくなったら、[az group delete](/cli/azure/group#az_group_delete) コマンドを使って、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除してください。

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> クラスターを削除したとき、AKS クラスターで使用される Azure Active Directory サービス プリンシパルは削除されません。 サービス プリンシパルを削除する手順については、[AKS のサービス プリンシパルに関する考慮事項と削除](../../aks/kubernetes-service-principal.md#additional-considerations)に関するページを参照してください。 マネージド ID を使用した場合、ID はプラットフォームによって管理されるので、削除する必要はありません。

## <a name="next-steps"></a>次のステップ

- AKS クラスターの [Kubernetes Web ダッシュボードにアクセスする](../../aks/kubernetes-dashboard.md)方法を学習する
- [クラスターをスケーリングする](../../aks/tutorial-kubernetes-scale.md)方法を学習する
- [MySQL フレキシブル サーバー](./quickstart-create-server-cli.md)を管理する方法を学習する
- データベース サーバーの[サーバー パラメーターを構成する](./how-to-configure-server-parameters-cli.md)方法を学習する

