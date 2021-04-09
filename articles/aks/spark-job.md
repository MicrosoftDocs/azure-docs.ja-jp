---
title: Apache Spark ジョブを Azure Kubernetes Service (AKS) で実行する
description: Azure Kubernetes Service (AKS) を使用して、大規模データ処理のための Apache Spark ジョブを作成、実行します。
author: lenadroid
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 74a3fe79291f3a5c7f5bbd664bf6d55a5fb77eae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181195"
---
# <a name="running-apache-spark-jobs-on-aks"></a>AKS での Apache Spark ジョブの実行

[Apache Spark][apache-spark] は、大規模データ処理用の高速エンジンです。 [Spark 2.3.0 リリース][spark-kubernetes-earliest-version]以降、Apache Spark では Kubernetes クラスターとのネイティブな統合がサポートされています。 Azure Kubernetes Service (AKS) は、Azure で実行される Kubernetes 管理環境です。 このドキュメントでは、Apache Spark ジョブを準備して Azure Kubernetes Service (AKS) クラスターで 実行する方法を詳しく説明します。

## <a name="prerequisites"></a>前提条件

この記事にある手順を完了するには、次のものが必要です。

* Kubernetes と [Apache Spark][spark-quickstart] に関する基礎知識。
* [Docker Hub][docker-hub] アカウント、または [Azure Container Registry][acr-create]。
* 開発システムに[インストール][azure-cli]された Azure CLI。
* システムにインストールされた [JDK 8][java-install]。
* システムにインストールされた [Apache Maven][maven-install]。
* システムにインストールされた SBT ([Scala Build Tool][sbt-install])。
* システムにインストールされた Git コマンド ライン ツール。

## <a name="create-an-aks-cluster"></a>AKS クラスターを作成する

大規模データ処理で使用される Spark は、Spark リソース要件を満たすようにサイズ調整された Kubernetes ノードを必要とします。 Azure Kubernetes Service (AKS) ノードには、最小サイズの `Standard_D3_v2` をお勧めします。

この最小推奨事項を満たしている AKS クラスターが必要な場合は、次のコマンドを実行します。

クラスターのリソース グループを作成します。

```azurecli
az group create --name mySparkCluster --location eastus
```

クラスターのサービス プリンシパルを作成します。 作成した後、次のコマンドのサービス プリンシパル appId とパスワードが必要になります。

```azurecli
az ad sp create-for-rbac --name SparkSP
```

ノードのサイズを `Standard_D3_v2` に指定し、appId およびパスワードの値を service-principal および client-secret のパラメーターとして渡して AKS クラスターを作成します。

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

AKS クラスターに接続します。

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Azure Container Registry (ACR) を使用してコンテナー イメージを格納する場合は、AKS と ACR 間の認証を構成します。 手順については、[ACR の認証に関するドキュメント][acr-aks]を参照してください。

## <a name="build-the-spark-source"></a>Spark ソースを作成する

AKS クラスターで Spark ジョブを実行する前に、Spark ソース コードをビルドしてコンテナー イメージにパッケージ化する必要があります。 Spark ソースには、このプロセスを完了するために使用できるスクリプトが含まれています。

開発システムに Spark プロジェクト リポジトリを複製します。

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

複製したリポジトリのディレクトリに変更し、Spark ソースのパスを変数に保存します。

```bash
cd spark
sparkdir=$(pwd)
```

複数の JDK バージョンがインストールされている場合は、現在のセッションではバージョン 8 を使用するように `JAVA_HOME` を設定します。

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

次のコマンドを実行して、Kubernetes がサポートする Spark ソース コードをビルドします。

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

次のコマンドは、Spark コンテナー イメージを作成してコンテナー イメージ レジストリにプッシュします。 `registry.example.com` をコンテナー レジストリの名前に置き換え、`v1` を使用したいタグに置き換えます。 Docker Hub を使用する場合、この値はレジストリの名前です。 Azure Container Registry (ACR) を使用する場合、この値は ACR ログイン サーバー名です。

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

コンテナー イメージ レジストリにコンテナー イメージをプッシュします。

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Spark ジョブを準備する

次に、Spark ジョブを準備します。 Spark ジョブを保持するために jar ファイルが使用されます。このファイルは、`spark-submit` コマンドを実行するときに必要です。 jar ファイルは、パブリック URL からアクセスできるようにするか、コンテナーイメージ内に事前にパッケージ化できます。 この例では、Pi の値を計算するサンプル jar を作成します。 この jar が Azure Storage にアップロードされます。 既存の jar がある場合は、それに置き換えてもかまいません。

Spark ジョブのプロジェクトを作成するディレクトリを作成します。

```bash
mkdir myprojects
cd myprojects
```

テンプレートから Scala プロジェクトを作成します。

```bash
sbt new sbt/scala-seed.g8
```

メッセージが表示されたら、プロジェクト名として `SparkPi` を入力します。

```bash
name [Scala Seed Project]: SparkPi
```

新しく作成されたプロジェクト ディレクトリに移動します。

```bash
cd sparkpi
```

次のコマンドを実行して SBT プラグインを追加します。これにより、プロジェクトを jar ファイルとしてパッケージ化できます。

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

次のコマンドを実行して、新しく作成されたプロジェクトにサンプル コードをコピーし、必要なすべての依存関係を追加します。

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11"/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Jar の中にプロジェクトをパッケージ化するには、次のコマンドを実行します。

```bash
sbt assembly
```

パッケージ化が成功すると、次のような出力が表示されます。

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>ジョブをストレージにコピーする

Azure Storage アカウントと、jar ファイルを保持するコンテナーを作成します。

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

次のコマンドを使用して、Azure ストレージ アカウントに jar ファイルをアップロードします。

```azurecli
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

これで、変数 `jarUrl` に、jar ファイルにパブリックにアクセスできるパスが格納されます。

## <a name="submit-a-spark-job"></a>Spark ジョブを送信する

次のコードを使用して、別のコマンドラインで kube-proxy を起動します。

```bash
kubectl proxy
```

Spark リポジトリのルートに移動します。

```bash
cd $sparkdir
```

ジョブを実行するための十分なアクセス許可を持つサービス アカウントを作成します。

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

`spark-submit` を使用してジョブを送信します。

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

この操作によって、シェル セッションにジョブの状態をストリーミングする Spark ジョブが開始されます。 ジョブの実行中に、kubectl get pods コマンドを使用して、Spark ドライバー ポッドと Executor ポッドを確認できます。 これらのコマンドを実行するには、2 つ目のターミナル セッションを開きます。

```console
kubectl get pods
```

```output
NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

ジョブの実行中は、Spark UI にアクセスすることもできます。 2 つ目のターミナル セッションで、`kubectl port-forward` コマンドを使用して、Spark UI へのアクセスを用意します。

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Spark UI にアクセスするには、ブラウザーでアドレス `127.0.0.1:4040` を開きます。

![Spark UI](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>ジョブの結果とログを取得する

ジョブが終了すると、ドライバー ポッドは "完了" 状態になります。 次のコマンドを使用して、ポッドの名前を取得します。

```bash
kubectl get pods --show-all
```

出力:

```output
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

`kubectl logs` コマンドを使用して、spark ドライバー ポッドからログを取得します。 ポッド名をドライバー ポッドの名前に置き換えます。

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

これらのログの中で、Pi の値である Spark ジョブの結果を確認できます。

```output
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>コンテナー イメージを使用して jar をパッケージ化する

上記の例では、Spark jar ファイルは Azure Storage にアップロードされました。 別のオプションとして、カスタムでビルドした Docker イメージに jar ファイルをパッケージ化できます。

これを行うには、`$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` ディレクトリにある Spark イメージの `dockerfile` を検索します。 `WORKDIR` 宣言と `ENTRYPOINT` 宣言の間に、Spark ジョブ `jar` 用の `ADD` ステートメントを追加します。

Jar パスの場所を、開発システム上の `SparkPi-assembly-0.1.0-SNAPSHOT.jar` ファイルの場所に更新します。 独自のカスタム jar ファイルを使用することもできます。

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Spark スクリプトが含まれるイメージをビルドしてプッシュします。

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

ジョブを実行するときに、リモート jar URL を示す代わりに、`local://` スキームと Docker イメージ内の jar ファイルへのパスを使用できます。

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Spark [ドキュメント][spark-docs]から引用: "Kubernetes スケジューラは現在、試験段階です。 将来のバージョンで、構成、コンテナー イメージおよびエントリポイントに関する動作が変更される可能性があります。"

## <a name="next-steps"></a>次のステップ

詳細については、Spark ドキュメントをご覧ください。

> [!div class="nextstepaction"]
> [Spark ドキュメント][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: /azure/developer/java/fundamentals/java-jdk-long-term-support
[maven-install]: https://maven.apache.org/install.html
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-kubernetes-earliest-version]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-quickstart]: ./index.yml
[azure-cli]: /cli/azure/
[storage-account]: ../storage/blobs/storage-quickstart-blobs-cli.md
