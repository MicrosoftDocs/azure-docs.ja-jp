---
title: Azure Red Hat OpenShift 4 クラスターに Open Liberty/WebSphere Liberty を使用する Java アプリケーションをデプロイする
description: Azure Red Hat OpenShift 4 クラスターに、Open Liberty/WebSphere Liberty を使用する Java アプリケーションをデプロイします。
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: java, jakartaee, javaee, microprofile, open-liberty, websphere-liberty, aro, openshift, red hat
ms.openlocfilehash: 08fd3ab112498a983b438d5ba1f1f100816cbf5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212996"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Azure Red Hat OpenShift 4 クラスターに Open Liberty/WebSphere Liberty を使用する Java アプリケーションをデプロイする

このガイドでは、Open Liberty オペレーターを使用して、Java、Java EE、[Jakarta EE](https://jakarta.ee/)、[MicroProfile](https://microprofile.io/) のアプリケーションを Open Liberty/WebSphere Liberty ランタイムで実行した後、コンテナー化されたアプリケーションを Azure Red Hat OpenShift (ARO) 4 クラスターにデプロイする方法を示します。 この記事では、Liberty アプリケーションの準備、アプリケーションの Docker イメージの構築、コンテナー化されたアプリケーションの ARO 4 クラスターでの実行について説明します。  Open Liberty の詳細については、[Open Liberty プロジェクトのページ](https://openliberty.io/)を参照してください。 IBM WebSphere Liberty の詳細については、[WebSphere Liberty の製品ページ](https://www.ibm.com/cloud/websphere-liberty)を参照してください。

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>前提条件

このガイドを正しく進めるには、以下の前提条件を満たす必要があります。

> [!NOTE]
> OpenShift クラスターを作成して実行するには、Azure Red Hat OpenShift に少なくとも 40 コアが必要です。 新しい Azure サブスクリプションの既定の Azure リソース クォータは、この要件を満たしていません。 リソースの制限の引き上げを依頼するには、「[標準クォータ:VM シリーズでの制限の引き上げ](../azure-portal/supportability/per-vm-quota-requests.md)」を参照してください。 試用版サブスクリプションはクォータ引き上げの対象ではないことに注意してください。クォータ引き上げを要求する前に、[従量課金制のサブスクリプションにアップグレード](../cost-management-billing/manage/upgrade-azure-subscription.md)してください。

1. Unix のようなオペレーティング システム (Ubuntu、macOS など) がインストールされているローカル マシンを準備します。
1. Java SE 実装 (たとえば [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)) をインストールします。
1. [Maven](https://maven.apache.org/download.cgi) 3.5.0 以上をインストールします。
1. お使いの OS 用の [Docker](https://docs.docker.com/get-docker/) をインストールします。
1. [Azure CLI](/cli/azure/install-azure-cli) 2.0.75 以降をインストールします。
1. [`envsubst`](https://command-not-found.com/envsubst) がオペレーティング システムに事前にインストールされていないかどうかを調べてインストールします。
1. このサンプルのコードをローカル システムにクローンします。 サンプルは [GitHub](https://github.com/Azure-Samples/open-liberty-on-aro) にあります。
1. [Azure Red Hat OpenShift 4 クラスターの作成](./tutorial-create-cluster.md)に関するページの手順に従います。

   "Red Hat プル シークレットを取得する" 手順は省略可能のラベルが付いていますが、**この記事には必須です**。  プル シークレットを使用すると、Azure Red Hat OpenShift クラスターで、Open Liberty オペレーターを見つけることができます。

   クラスターでメモリを集中的に使用するアプリケーションを実行する予定の場合は、`--worker-vm-size` パラメーターを使用して、ワーカー ノードにとって適切な仮想マシン サイズを指定します。 たとえば `Standard_E4s_v3` は、クラスターに Elasticsearch オペレーターをインストールするための仮想マシンの最小サイズです。 詳細については、次を参照してください。

   * [クラスターを作成するための Azure CLI](/cli/azure/aro#az-aro-create)
   * [メモリ最適化でサポートされる仮想マシンのサイズ](./support-policies-v4.md#memory-optimized)
   * [Elasticsearch オペレーターをインストールするための前提条件](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. [Azure Red Hat OpenShift 4 クラスターへの接続](./tutorial-connect-cluster.md)に関するページの手順に従って、クラスターに接続します。
   * この記事では後で `oc` コマンドを使用するため、「OpenShift CLI をインストールする」の手順に必ず従ってください。
   * `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` のようなクラスター コンソールの URL を書き留めます。
   * `kubeadmin` の資格情報を書き留めます。

1. ユーザー `kubeadmin` のトークンを使用して OpenShift CLI にサインインできることを確認します。

### <a name="enable-the-built-in-container-registry-for-openshift"></a>OpenShift 用の組み込みコンテナー レジストリを有効にする

このチュートリアルの手順では、OpenShift からアクセスできるコンテナー レジストリにプッシュする必要がある Docker イメージを作成します。 最も単純なオプションは、OpenShift に用意されている組み込みレジストリを使用することです。 組み込みのコンテナー レジストリを有効にするには「[Azure Red Hat OpenShift 4 の組み込みのコンテナー レジストリを構成する](built-in-container-registry.md)」の手順に従います。 この記事では、それらの手順のうち、3 つの項目を使用します。

* OpenShift Web コンソールにサインインするための Azure AD ユーザーのユーザー名とパスワード。
* OpenShift CLI にサインインするための手順に従った後の `oc whoami` の出力。  この値は、説明上 **aad-user** と呼びます。
* コンテナー レジストリの URL。

組み込みコンテナー レジストリを有効にする手順を完了したときに、これらの項目を書き留めておきます。

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Java アプリ用の OpenShift 名前空間を作成する

1. `kubeadmin` の資格情報を使用して、ブラウザーから OpenShift Web コンソールにサインインします。
2. **[管理]**  >  **[名前空間]**  >  **[名前空間の作成]** と移動します。
3. 次に示すように、 **[名前]** に `open-liberty-demo` を入力し、 **[作成]** を選択します。

   ![名前空間を作成する](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>デモ プロジェクトのために管理者を作成する

イメージ管理以外に、**aad-user** には、ARO 4 クラスターのデモ プロジェクトでリソースを管理するための管理アクセス許可も付与されます。  以下の手順に従って、OpenShift CLI にサインインし、**aad-user** に必要な特権を付与します。

1. `kubeadmin` の資格情報を使用して、ブラウザーから OpenShift Web コンソールにサインインします。
1. Web コンソールの右上で、サインインしたユーザーのコンテキスト メニューを展開し、 **[ログイン コマンドのコピー]** を選択します。
1. 必要に応じて、同じユーザーを使用して新しいタブ ウィンドウにサインインします。
1. **[Display Token]\(トークンの表示\)** を選択します。
1. 次に示すように、 **[このトークンを使用してログイン]** の下に示されている値をクリップボードにコピーし、それをシェルで実行します。
1. 以下のコマンドを実行して、名前空間 `open-liberty-demo` の **aad-user** に、`admin` ロールを付与します。

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Open Liberty OpenShift オペレーターをインストールする

クラスターを作成して接続したら、Open Liberty オペレーターをインストールします。  Open Liberty オペレーターのメイン開始ページは、[GitHub](https://github.com/OpenLiberty/open-liberty-operator) にあります。

1. `kubeadmin` の資格情報を使用して、ブラウザーから OpenShift Web コンソールにサインインします。
2. **[オペレーター]**  >  **[オペレーター ハブ]** と移動して、**Open Liberty オペレーター** を検索します。
3. 検索結果から **[Open Liberty オペレーター]** を選択します。
4. **[インストール]** を選択します。
5. **[オペレーター サブスクリプションの作成]** ポップアップで、 **[インストール モード]** の **[クラスター上のすべての名前空間 (既定)]** 、 **[更新チャネル]** の **[ベータ]** 、 **[承認方法]** の **[自動]** をオンにします。

   ![Open Liberty オペレーターのオペレーター サブスクリプションを作成する](./media/howto-deploy-java-liberty-app/install-operator.png)
6. **[サブスクライブ]** を選択し、Open Liberty オペレーターが表示されるまで 1 ～ 2 分待機します。
7. 状態が "成功" の Open Liberty オペレーターに注意します。  そうでない場合は、問題を診断して解決してから続行してください。
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Open Liberty オペレーターがインストールされたことを示すインストール済みのオペレーター。":::

## <a name="prepare-the-liberty-application"></a>Liberty アプリケーションを準備する

このガイドでは、例として Java EE 8 アプリケーションを使用します。 Open Liberty は [Java EE 8 フル プロファイル](https://javaee.github.io/javaee-spec/javadocs/)互換サーバーであるため、このアプリケーションを簡単に実行できます。  Open Liberty は [Jakarta EE 8 フル プロファイル互換](https://jakarta.ee/specifications/platform/8/apidocs/)でもあります。

### <a name="run-the-application-on-open-liberty"></a>Open Liberty でアプリケーションを実行する

Open Liberty でアプリケーションを実行するには、[Liberty Maven プラグイン](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)で、デプロイのためにアプリケーションをパッケージ化できるように、Open Liberty サーバー構成ファイルを作成する必要があります。 アプリケーションを OpenShift にデプロイするためには、Liberty Maven プラグインは必要ありません。  ただしこの例では、それを Open Liberty の開発者 (dev) モードで使用します。  開発者モードでは、アプリケーションをローカルで簡単に実行できます。 ローカル コンピューターで以下の手順を完了します。

1. `2-simple/src/main/liberty/config/server.xml` を `1-start/src/main/liberty/config` にコピーし、既存の長さゼロのファイルを上書きします。 この `server.xml` によって Java EE 機能を使用して、Open Liberty サーバーを構成します。
1. `2-simple/pom.xml` を `1-start/pom.xml` にコピーします。  この手順で `liberty-maven-plugin` を POM に追加します。
1. ディレクトリをローカル クローンの `1-start` に変更します。
1. コンソールで `mvn clean package` を実行して、ディレクトリ `./target` 内に war パッケージ `javaee-cafe.war` を生成します。
1. `mvn liberty:dev` を実行して、dev モードで Open Liberty を起動します。
1. サーバーが起動するまで待機します。 コンソールの出力は、次のメッセージで終了するはずです。

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. ブラウザーで `http://localhost:9080/` を開き、アプリケーションのホームページにアクセスします。 アプリケーションの外観は次のイメージのようになります。

   ![JavaEE Cafe Web UI](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. **Ctrl + C** キーを押して、アプリケーションと Open Liberty サーバーを停止します。

ローカル クローンのディレクトリ `2-simple` には、上の変更が既に適用されている Maven プロジェクトが表示されます。

## <a name="prepare-the-application-image"></a>アプリケーション イメージを準備する

ARO 4 クラスターに Liberty アプリケーションをデプロイして実行するには、[Open Liberty コンテナー イメージ](https://github.com/OpenLiberty/ci.docker) または [WebSphere Liberty コンテナー イメージ](https://github.com/WASdev/ci.docker)を使用して、アプリケーションを Docker イメージとしてコンテナー化します。

### <a name="build-application-image"></a>アプリケーション イメージをビルドする

以下の手順を完了してアプリケーション イメージをビルドします。

1. ディレクトリをローカル クローンの `2-simple` に変更します。
2. `mvn clean package` を実行してアプリケーションをパッケージ化します。
3. 以下のいずれかのコマンドを実行してアプリケーション イメージをビルドします。
   * Open Liberty 基本イメージをビルドします。

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * WebSphere Liberty 基本イメージをビルドします。

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Docker を使用してローカルでアプリケーションを実行する

コンテナー化されたアプリケーションをリモート クラスターにデプロイする前に、それが機能するかどうかを検証するため、ローカルの Docker を使用して実行します。

1. コンソールで `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0` を実行します。
2. Liberty サーバーが起動し、アプリケーションが正常にデプロイされるのを待機します。
3. ブラウザーで `http://localhost:9080/` を開き、アプリケーションのホームページにアクセスします。
4. **Ctrl + C** キーを押して、アプリケーションと Liberty サーバーを停止します。

### <a name="push-the-image-to-the-container-image-registry"></a>コンテナー イメージ レジストリにイメージをプッシュする

アプリケーションの状態に問題がなければ、下記の手順に従って、それを組み込みのコンテナー イメージ レジストリにプッシュします。

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Azure AD ユーザーとして OpenShift CLI にログインする

1. Azure AD ユーザーの資格情報を使用して、ブラウザーから OpenShift Web コンソールにサインインします。

   1. InPrivate、Incognito、またはその他同等のブラウザー ウィンドウ機能を使用して、コンソールにサインインします。
   1. **[openid]** を選択します。

   > [!NOTE]
   > ここでサインインに使用するユーザー名とパスワードをメモします。 このユーザー名とパスワードは、この記事および他の記事の他のアクションで管理者用に使用します。
1. 次の手順に従い、OpenShift CLI を使用してサインインします。  このプロセスは `oc login` と呼ばれています。
   1. Web コンソールの右上で、サインイン済みユーザーのコンテキスト メニューを展開し、 **[Copy Login Command]\(ログイン コマンドのコピー\)** を選択します。
   1. 必要に応じて、同じユーザーを使用して新しいタブ ウィンドウにサインインします。
   1. **[Display Token]\(トークンの表示\)** を選択します。
   1. 次に示すように、 **[このトークンを使用してログイン]** の下に示されている値をクリップボードにコピーし、それをシェルで実行します。

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>OpenShift 用のコンテナー レジストリにコンテナー イメージをプッシュする

以下のコマンドを実行して、OpenShift 用のコンテナー レジストリにイメージをプッシュします。

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

成功した場合の出力は次のようになります。

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

次のコマンドで、組み込みコンテナー イメージ レジストリにイメージをプッシュします。

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>ARO 4 クラスターにアプリケーションをデプロイする

これで、以前の前提条件の作業時に作成した Azure Red Hat OpenShift 4 クラスターに、サンプルの Liberty アプリケーションをデプロイできるようになりました。

### <a name="deploy-the-application-from-the-web-console"></a>Web コンソールからアプリケーションをデプロイする

Open Liberty オペレーターを使用して Liberty アプリケーションを管理するので、"OpenLibertyApplication" 型の *カスタム リソース定義* のインスタンスを作成する必要があります。 その後は、デプロイに必要な OpenShift リソースの管理のすべての側面が、オペレーターによって処理されます。

1. Azure AD ユーザーの資格情報を使用して、ブラウザーから OpenShift Web コンソールにサインインします。
1. **[ホーム]** を展開し、 **[プロジェクト]**  >  **[open-liberty-demo]** と選択します。
1. **[オペレーター]**  >  **[インストール済みオペレーター]** と移動します。
1. ページの中央で、 **[Open Liberty オペレーター]** を選択します。
1. ページの中央で、 **[Open Liberty アプリケーション]** を選択します。  ユーザー インターフェイス内の項目のナビゲーションは、使用中のテクノロジの実際の包含階層を反映しています。
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![ARO Java の包含](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. **[OpenLibertyApplication の作成]** を選択します
1. 生成された yaml を自分のものに置き換えます。これは、`<path-to-repo>/2-simple/openlibertyapplication.yaml` にあります。
1. **［作成］** を選択します OpenLibertyApplications の一覧に戻ります。
1. **[javaee-cafe-simple]** を選択します。
1. ページの中央で、 **[リソース]** を選択します。
1. テーブルで、 **[種類]** が **[ルート]** である **javaee-cafe-simple** のリンクを選択します。
1. 表示されるページで、 **[場所]** の下のリンクを選択します。

ブラウザーでアプリケーションのホーム ページが開かれていることがわかります。

### <a name="delete-the-application-from-the-web-console"></a>Web コンソールからアプリケーションを削除する

アプリケーションの操作を終えたら、以下の手順に従って、Open Shift からアプリケーションを削除します。

1. 左側のナビゲーション ウィンドウで、 **[オペレーター]** のエントリを展開します。
1. **[インストール済みオペレーター]** を選択します。
1. **[Open Liberty オペレーター]** を選択します。
1. ページの中央で、 **[Open Liberty アプリケーション]** を選択します。
1. 縦の省略記号 (3 つの垂直ドット) を選択してから、 **[OpenLiberty アプリケーションの削除]** を選択します。

### <a name="deploy-the-application-from-cli"></a>CLI からアプリケーションをデプロイする

Web コンソール GUI を使用する代わりに、CLI からアプリケーションをデプロイできます。 まだの場合は、Red Hat のドキュメント「[CLI の使用開始](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html)」に従って、`oc` コマンドライン ツールをダウンロードしてインストールします。

1. Azure AD ユーザーの資格情報を使用して、ブラウザーから OpenShift Web コンソールにサインインします。
2. Azure AD ユーザーのトークンを使用して OpenShift CLI にサインインします。
3. ディレクトリをローカル クローンの `2-simple` に変更し、以下のコマンドを実行して Liberty アプリケーションを ARO 4 クラスターにデプロイします。  コマンドの出力もインラインで表示されます。

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. 続行する前に、`READY` 列の下の `1/1` を確認します。  そうなっていない場合は、問題を診断して解決してから続行してください。
5. 次に示すように、`oc get route` コマンドを使用して、アプリケーションへのルートのホストを検出します。

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Liberty アプリケーションが稼働状態になったら、ブラウザーで **Route Host** の出力を開き、アプリケーションのホーム ページにアクセスします。

### <a name="delete-the-application-from-cli"></a>CLI からアプリケーションを削除する

次のコマンドを実行して、CLI からアプリケーションを削除します。

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

「[チュートリアル:Azure Red Hat OpenShift 4 クラスターを削除する](./tutorial-delete-cluster.md)」の手順に従って、ARO クラスターを削除します。

## <a name="next-steps"></a>次のステップ

このガイドでは、以下の方法について学習しました。
> [!div class="checklist"]
>
> * Liberty アプリケーションを準備する
> * アプリケーション イメージをビルドする
> * GUI と CLI を使用して、コンテナー化されたアプリケーションを ARO 4 クラスターで実行する

このガイドで使用された以下のリファレンスから、より多くのことを学習できます。

* [Open Liberty](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Open Liberty オペレーター](https://github.com/OpenLiberty/open-liberty-operator)
* [Open Liberty サーバーの構成](https://openliberty.io/docs/ref/config/)
* [Liberty Maven プラグイン](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Open Liberty コンテナー イメージ](https://github.com/OpenLiberty/ci.docker)
* [WebSphere Liberty コンテナー イメージ](https://github.com/WASdev/ci.docker)