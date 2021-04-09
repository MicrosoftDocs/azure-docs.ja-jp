---
title: チュートリアル:Azure CLI を使用して PostgreSQL フレキシブル サーバーで Django を AKS クラスターにデプロイする
description: Azure Database for PostgreSQL - フレキシブル サーバーを使用して、Django をすばやく構築し、AKS にデプロイする方法について説明します。
ms.service: postgresql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 12/10/2020
ms.custom: mvc
ms.openlocfilehash: 6e8effee91eed73193319238c2ad2f6eaf6d0473
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211279"
---
# <a name="tutorial-deploy-django-app-on-aks-with-azure-database-for-postgresql---flexible-server"></a>チュートリアル:Azure Database for PostgreSQL - フレキシブル サーバーを使用して Django アプリを AKS にデプロイする

このクイックスタートでは、Azure CLI を使用して、Azure Database for PostgreSQL - フレキシブル サーバー (プレビュー) で Azure Kubernetes Service (AKS) クラスターに Django アプリケーションをデプロイします。

**[AKS](../../aks/intro-kubernetes.md)** は、クラスターをすばやくデプロイして管理できるマネージド Kubernetes サービスです。 **[Azure Database for PostgreSQL - フレキシブル サーバー (プレビュー)](overview.md)** は、データベース管理機能と構成設定のよりきめ細かな制御と柔軟性を提供するように設計されたフル マネージド データベース サービスです。

> [!NOTE]
> - Azure Database for PostgreSQL - フレキシブル サーバーは、現在パブリック プレビュー段階にあります。
> - このクイックスタートは、Kubernetes の概念、Django、PostgreSQL に関する基礎知識があることを前提としています。

## <a name="pre-requisites"></a>前提条件
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

- Bash 環境で [Azure Cloud Shell](../../cloud-shell/quickstart.md) を使用します。

   [![埋め込みの起動](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell を起動する")](https://shell.azure.com)  
- 必要に応じて、Azure CLI を[インストール](/cli/azure/install-azure-cli)して、CLI リファレンス コマンドを実行します。
  - ローカル インストールを使用する場合は、[az login](/cli/azure/reference-index#az-login) コマンドを使用して Azure CLI でサインインします。  認証プロセスを完了するには、ターミナルに表示される手順に従います。  追加のサインイン オプションについては、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)」を参照してください。
  - 初回使用時にインストールを求められたら、Azure CLI 拡張機能をインストールします。  拡張機能の詳細については、「[Azure CLI で拡張機能を使用する](/cli/azure/azure-cli-extensions-overview)」を参照してください。
  - [az version](/cli/azure/reference-index?#az_version) を実行し、インストールされているバージョンおよび依存ライブラリを検索します。 最新バージョンにアップグレードするには、[az upgrade](/cli/azure/reference-index?#az_upgrade) を実行します。 この記事では、Azure CLI の最新バージョンが必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

> [!NOTE]
> このクイックスタートのコマンドを (Azure Cloud Shell ではなく) ローカルで実行する場合は、必ず管理者としてコマンドを実行してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループは、Azure リソースが展開され管理される論理グループです。 [az group create][az-group-create] コマンドを使用して、*eastus* に *django-project* というリソース グループを作成しましょう。

```azurecli-interactive
az group create --name django-project --location eastus
```

> [!NOTE]
> リソース グループの場所は、リソース グループのメタデータが保存される場所です。 また、リソースの作成時に別のリージョンを指定しない場合に、Azure でリソースが実行される場所でもあります。

次の出力例では、正常に作成されたリソース グループが示されています。

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/django-project",
  "location": "eastus",
  "managedBy": null,
  
  "name": "django-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>AKS クラスターの作成

AKS クラスターを作成するには、[az aks create](/cli/azure/aks#az-aks-create) コマンドを使用します。 次の例では、*myAKSCluster* という名前のクラスターを 1 つのノードで作成します。 これは完了までに数分かかる場合があります。

```azurecli-interactive
az aks create --resource-group django-project --name djangoappcluster --node-count 1 --generate-ssh-keys
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
az aks get-credentials --resource-group django-project --name djangoappcluster
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

## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーを作成する
[az postgreSQL flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create) コマンドを使用して、フレキシブル サーバーを作成します。 次のコマンドでは、サービスの既定値と Azure CLI のローカル コンテキストからの値を使用してサーバーを作成します。

```azurecli-interactive
az postgres flexible-server create --public-access <YOUR-IP-ADDRESS>
```

作成されたサーバーには、次の属性があります。
- サーバーが最初にプロビジョニングされたときに、新しい空のデータベース ```postgres``` が作成されます。 このクイックスタートでは、このデータベースを使用します。
- 自動生成されたサーバー名、管理者ユーザー名、管理者パスワード、リソース グループ名 (ローカル コンテキストでまだ指定されていない場合)、およびリソース グループと同じ場所
- 残りのサーバー構成のサービスの既定値: コンピューティング レベル (汎用)、コンピューティング サイズ/SKU (2 つの仮想コアを使用する Standard_D2s_v3)、バックアップの保持期間 (7 日間)、PostgreSQL のバージョン (12)
- public-access 引数を使用すると、パブリック アクセスがファイアウォール規則で保護されたサーバーを作成できます。 IP アドレスを指定して、クライアント マシンからのアクセスを許可するファイアウォール規則を追加します。
- このコマンドはローカル コンテキストを使用しているため、```eastus``` リージョンの ```django-project``` リソース グループにサーバーが作成されます。


## <a name="build-your-django-docker-image"></a>Django Docker イメージを作成する

新しい [Django アプリケーション](https://docs.djangoproject.com/en/3.1/intro/)を作成するか、既存の Django プロジェクトを使用します。 コードが次のフォルダー構造であることを確認します。

```
└───my-djangoapp
    └───views.py
    └───models.py
    └───forms.py
    ├───templates
        . . . . . . .
    ├───static
        . . . . . . .
└───my-django-project
    └───settings.py
    └───urls.py
    └───wsgi.py
        . . . . . . .
    └─── Dockerfile
    └─── requirements.txt
    └─── manage.py
    
```
Django アプリケーションが、kubernetes アプリに割り当てられている外部 IP を使用するように、```settings.py``` の ```ALLOWED_HOSTS``` を更新します。

```python
ALLOWED_HOSTS = ['*']
```

```settings.py``` ファイルの ```DATABASES={ }``` セクションを更新します。 次のコード スニペットは、Kubernetes マニフェスト ファイルからデータベース ホスト、ユーザー名、パスワードを読み取ります。

```python
DATABASES={
   'default':{
      'ENGINE':'django.db.backends.postgresql_psycopg2',
      'NAME':os.getenv('DATABASE_NAME'),
      'USER':os.getenv('DATABASE_USER'),
      'PASSWORD':os.getenv('DATABASE_PASSWORD'),
      'HOST':os.getenv('DATABASE_HOST'),
      'PORT':'5432',
      'OPTIONS': {'sslmode': 'require'}
   }
}
```

### <a name="generate-a-requirementstxt-file"></a>requirements.txt ファイルを生成する
Django アプリケーションの依存関係を示す ```requirements.txt``` ファイルを作成します。 ```requirements.txt``` ファイルの例を次に示します。 [``` pip freeze > requirements.txt```](https://pip.pypa.io/en/stable/reference/pip_freeze/) を使用して、既存のアプリケーションの requirements.txt ファイルを生成できます。

``` text
Django==2.2.17
postgres==3.0.0
psycopg2-binary==2.8.6
psycopg2-pool==1.1
pytz==2020.4
```

### <a name="create-a-dockerfile"></a>Dockerfile を作成する
```Dockerfile``` という名前の新しいファイルを作成し、次のコード スニペットをコピーします。 この Dockerfile では、Python 3.8 を設定し、requirements.txt ファイルに示されているすべての要件をインストールします。

```docker
# Use the official Python image from the Docker Hub
FROM python:3.8.2

# Make a new directory to put our code in.
RUN mkdir /code

# Change the working directory.
WORKDIR /code

# Copy to code folder
COPY . /code/

# Install the requirements.
RUN pip install -r requirements.txt

# Run the application:
CMD python manage.py runserver 0.0.0.0:8000
```

### <a name="build-your-image"></a>イメージのビルド
```cd``` コマンドを使用して、ターミナルの ```my-django-app``` ディレクトリにいることを確認します。 次のコマンドを実行して、掲示板イメージをビルドします。

``` bash

docker build --tag myblog:latest .

```

[Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) または [Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md) にイメージをデプロイします。

> [!IMPORTANT]
>Azure Container Registry (ACR) を使用している場合は、```az aks update``` コマンドを実行して ACR アカウントを AKS クラスターに接続します。
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g django-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Kubernetes マニフェスト ファイルを作成する

Kubernetes のマニフェスト ファイルでは、どのコンテナー イメージを実行するかなど、クラスターの望ましい状態を定義します。 ```djangoapp.yaml``` という名前のマニフェスト ファイルを作成し、次の YAML 定義をコピーしましょう。

>[!IMPORTANT]
> - ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` を、実際の Django Docker イメージ名とタグに置き換えます (例: ```docker-hub-user/myblog:latest```)。
> - postgres フレキシブル サーバーの ```SERVERNAME```、```YOUR-DATABASE-USERNAME```、```YOUR-DATABASE-PASSWORD``` で下記の ```env``` セクションを更新します。


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: django-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: django-app
  template:
    metadata:
      labels:
        app: django-app
    spec:
      containers:
      - name: django-app
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.postgres.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "postgres"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - django-app
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: python-svc
spec:
  type: LoadBalancer
  ports:
    - port: 8000
  selector:
    app: django-app
```

## <a name="deploy-django-to-aks-cluster"></a>Django を AKS クラスターにデプロイする
[kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) コマンドを使用してアプリケーションをデプロイし、ご利用の YAML マニフェストの名前を指定します。

```console
kubectl apply -f djangoapp.yaml
```

次の出力例は、正常に作成されたデプロイおよびサービスを示しています。

```output
deployment "django-app" created
service "python-svc" created
```

```django-app``` デプロイにより、アプリに使用するイメージ、ポッドの数、ポッド構成など、デプロイの詳細を示すことができます。 外部 IP を介してアプリケーションを公開するために、```python-svc``` サービスが作成されます。

## <a name="test-the-application"></a>アプリケーションをテストする

アプリケーションが実行されると、Kubernetes サービスによってアプリケーション フロント エンドがインターネットに公開されます。 このプロセスが完了するまでに数分かかることがあります。

進行状況を監視するには、[kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) コマンドを `--watch` 引数と一緒に使用します。

```azurecli-interactive
kubectl get service django-app --watch
```

最初は、*django-app* サービスの *EXTERNAL-IP* が *pending* (保留中) として表示されます。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
django-app   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

*EXTERNAL-IP* アドレスが "*保留中*" から実際のパブリック IP アドレスに変わったら、`CTRL-C` を使用して `kubectl` ウォッチ プロセスを停止します。 次の出力例は、サービスに割り当てられている有効なパブリック IP アドレスを示しています。

```output
django-app  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Web ブラウザーを開いてサービスの外部 IP アドレスにアクセスすると、Django アプリケーションが表示されます。  

>[!NOTE]
> - 現在、Django サイトでは HTTPS は使用されていません。 [独自の証明書を使用して TLS を有効にする](../../aks/ingress-own-tls.md)ことをお勧めします。
> - クラスターの [HTTP ルーティング](../../aks/http-application-routing.md)を有効にすることができます。 http ルーティングを有効にすると、AKS クラスター内にイングレス コントローラーが構成されます。 アプリケーションがデプロイされると、このソリューションは、アプリケーション エンドポイントのパブリックにアクセス可能な DNS 名も作成します。

## <a name="run-database-migrations"></a>データベースの移行を実行する

Django アプリケーションの場合、データベースの移行を実行するか、静的ファイルを収集する必要があります。 ```$ kubectl exec <pod-name> -- [COMMAND]``` を使用して、これらの django シェル コマンドを実行できます。  コマンドを実行する前に、```kubectl get pods``` を使用してポッド名を確認する必要があります。 

```bash
$ kubectl get pods
```

次のような出力が表示されます。
```output
NAME                             READY   STATUS          RESTARTS   AGE
django-app-5d9cd6cd8-l6x4b     1/1     Running              0       2m
```

ポッド名を確認したら、```$ kubectl exec <pod-name> -- [COMMAND]``` コマンドを使用して django データベースの移行を実行できます。 ```/code/``` は、上記の ```Dockerfile``` で定義されているプロジェクトの作業ディレクトリです。

```bash
$ kubectl exec django-app-5d9cd6cd8-l6x4b -- python /code/manage.py migrate
```

出力は次のようになります。 
```output 
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  . . . . . . 
```

問題が発生した場合は、```kubectl logs <pod-name>``` を実行して、アプリケーションによってスローされた例外を確認してください。 アプリケーションが正常に動作している場合は、```kubectl logs``` を実行すると、次のような出力が表示されます。

```output
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
December 08, 2020 - 23:24:14
Django version 2.2.17, using settings 'django_postgres_app.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

## <a name="clean-up-the-resources"></a>リソースのクリーンアップ

Azure の課金を回避するには、不要なリソースをクリーンアップする必要があります。  クラスターが必要なくなったら、[az group delete](/cli/azure/group&preserve-view=true#az_group_delete) コマンドを使って、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除してください。

```azurecli-interactive
az group delete --name django-project --yes --no-wait
```

> [!NOTE]
> クラスターを削除したとき、AKS クラスターで使用される Azure Active Directory サービス プリンシパルは削除されません。 サービス プリンシパルを削除する手順については、[AKS のサービス プリンシパルに関する考慮事項と削除](../../aks/kubernetes-service-principal.md#additional-considerations)に関するページを参照してください。 マネージド ID を使用した場合、ID はプラットフォームによって管理されるので、削除する必要はありません。

## <a name="next-steps"></a>次のステップ

- AKS クラスターの [Kubernetes Web ダッシュボードにアクセスする](../../aks/kubernetes-dashboard.md)方法を学習する
- [継続的デプロイを有効にする](../../aks/deployment-center-launcher.md)方法を学習する
- [クラスターをスケーリングする](../../aks/tutorial-kubernetes-scale.md)方法を学習する
- [postgres フレキシブル サーバー](./quickstart-create-server-cli.md)を管理する方法を学習する
- データベース サーバーの[サーバー パラメーターを構成する](./howto-configure-server-parameters-using-cli.md)方法を学習する