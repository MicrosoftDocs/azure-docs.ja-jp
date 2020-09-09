---
title: クイックスタート - アプリをビルドして Azure Spring Cloud にデプロイする
description: Azure Spring Cloud へのアプリのデプロイについて説明します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 8931c22c3656cf9708756153268ab1d9d87b8343
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050830"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>クイック スタート:アプリをビルドして Azure Spring Cloud にデプロイする

このドキュメントでは、以下を使用して、マイクロサービス アプリケーションをビルドし、Azure Spring Cloud にデプロイする方法について説明します。
* Azure CLI
* Maven プラグイン
* Intellij

Azure CLI または Maven を使用してデプロイする前に、[Azure Spring Cloud のインスタンスのプロビジョニング](spring-cloud-quickstart-provision-service-instance.md)と[構成サーバーの設定](spring-cloud-quickstart-setup-config-server.md)を行う例を完了してください。

## <a name="prerequisites"></a>前提条件

* [JDK 8 をインストールする](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Azure サブスクリプションにサインアップする](https://azure.microsoft.com/free/)
* (オプション) [Azure CLI バージョン 2.0.67 以降をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)し、`az extension add --name spring-cloud` コマンドを使用して Azure Spring Cloud 拡張機能をインストールする
* (オプション) [Azure Toolkit for IntelliJ をインストール](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/)し、[サインイン](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)する

## <a name="deployment-procedures"></a>デプロイの手順

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>マイクロサービス アプリケーションをローカルにビルドする

1. Azure Cloud アカウントにサンプル アプリ リポジトリを複製します。  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. ディレクトリを変更し、プロジェクトをビルドします。

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

プロジェクトのコンパイルには、約 5 分かかります。 完了すると、各サービスのそれぞれのフォルダーに、個別の JAR ファイルができているはずです。

### <a name="create-and-deploy-the-apps"></a>アプリを作成してデプロイする

1. 次のコマンドを使用して、既定のリソース グループ名とクラスター名を設定します。

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. 前の手順でビルドした JAR ファイルを使用して、Azure Spring Cloud マイクロサービスを作成します。 3 つのアプリ (**gateway**、**auth-service**、および **account-service**) を作成します。

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. 前の手順で作成したアプリケーションを Azure にデプロイする必要があります。 以下のコマンドを実行して、3 つのすべてのアプリケーションをデプロイします。

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>ゲートウェイにパブリック エンドポイントを割り当てる

Web ブラウザーを介してアプリケーションにアクセスする手段が必要です。 ゲートウェイ アプリケーションには、パブリックに公開されているエンドポイントが必要です。

1. 次のコマンドを使用してエンドポイントを割り当ててください。

    ```azurecli
    az spring-cloud app update -n gateway --is-public true
    ```

2. アプリケーションが実行されていることを確認できるように、**gateway** アプリケーションに対してパブリック IP を求めるクエリを実行します。

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>サンプル アプリケーション リポジトリを複製してビルドする

1. 次のコマンドを実行して、Git リポジトリを複製します。

    ```
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. 次のコマンドを実行して、ディレクトリを変更し、プロジェクトをビルドします。

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>構成を生成し、Azure Spring Cloud にデプロイする

1. 親 POM を含む PiggyMetrics のルート フォルダー内で次のコマンドを実行して、構成を生成します。 既に Azure CLI でサインインしている場合、コマンドは資格情報を自動的に取得します。 そうでない場合は、プロンプトの指示に従ってサインインします。 詳細については、[Wiki ページ](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)を参照してください。

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```
    
    選択を求めるメッセージが表示されます。
    * **モジュール:** `gateway`、`auth-service`、および `account-service` を選択します。
    * **サブスクリプション:** これは、Azure Spring Cloud インスタンスを作成するために使用されるサブスクリプションです。
    * **サービス インスタンス:** これは、自分の Azure Spring Cloud インスタンスの名前です。
    * **パブリック エンドポイント:** 提示されるプロジェクトの一覧で、`gateway` に対応する番号を入力します。  これで、パブリック アクセスが与えられます。

1. POM にプラグインの依存関係と構成が含まれるようになりました。 次のコマンドを使用して、アプリをデプロイします。 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>IntelliJ でサンプル プロジェクトをインポートする

1. このチュートリアルのソース リポジトリをダウンロードして解凍するか、Git の `git clone https://github.com/Azure-Samples/piggymetrics` を使用して複製します 

1. IntelliJ の **[Welcome]\(ようこそ\)** ダイアログを開き、 **[Import Project]\(プロジェクトのインポート\)** を選択してインポート ウィザードを開きます。

1. `piggymetric` フォルダーを選択します。

    ![プロジェクトのインポート](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>ゲートウェイ アプリを Azure Spring Cloud にデプロイする
Azure にデプロイするには、Azure アカウントで Azure Toolkit for IntelliJ にサインインし、自分のサブスクリプションを選択する必要があります。 サインインの詳細については、「[インストールとサインイン](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)」を参照してください。

1. IntelliJ のプロジェクト エクスプローラーでプロジェクトを右クリックし、 **[Azure]**  ->  **[Deploy to Azure Spring Cloud]\(Azure Spring Cloud にデプロイ\)** を選択します。

    ![Azure へのデプロイ 1](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. **[Name]\(名前\)** フィールドで、構成を示す既存の**名前**に「 *:gateway*」を追加します。
1. **[Artifact]\(成果物\)** ボックスで、 *[com.piggymetrics:gateway:1.0-SNAPSHOT]* を選択します。
1. **[Subscription]\(サブスクリプション\)** ボックスで、自分のサブスクリプションを確認します。
1. **[Spring Cloud]** ボックスで、「[Azure Spring Cloud インスタンスをプロビジョニングする](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance)」で作成した Azure Spring Cloud のインスタンスを選択します。
1. **[Public Endpoint]\(パブリック エンドポイント\)** を *[Enable]\(有効化\)* に設定します。
1. **[App:]\(アプリ:\)** ボックスで、 **[Create app]\(アプリの作成\)** を選択します。
1. 「*gateway*」と入力し、 **[OK]** をクリックします。

    ![Azure へのデプロイ [OK]](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. ダイアログの **[Before launch]\(起動前\)** セクションで、 *[Run Maven Goal]\(Maven 目標の実行\)* をダブルクリックします。
1. **[Working directory]\(作業ディレクトリ\)** ボックスで、*piggymetrics/gateway* フォルダーに移動します。
1. **[Command line]\(コマンド ライン\)** ボックスに、「*package -DskipTests*」と入力します。 **[OK]** をクリックします。
1. **[Deploy Azure Spring Cloud app]\(Azure Spring Cloud アプリのデプロイ\)** ダイアログの下部にある **[Run]\(実行\)** ボタンをクリックして、デプロイを開始します。 このプラグインは、`gateway` アプリに対して `mvn package` コマンドを実行し、`package` コマンドによって生成された jar をデプロイします。

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>auth-service アプリおよび account-service アプリを Azure Spring Cloud にデプロイする
上記の手順を繰り返して、`auth-service` アプリと `account-service` アプリを Azure Spring Cloud にデプロイすることができます。

1. `auth-service` アプリを特定するために、 **[Name]\(名前\)** と **[Artifact]\(成果物\)** を変更します。
1. **[App:]\(アプリ\)** ボックスで **[Create app]\(アプリの作成\)** を選択して、`auth-service` アプリを作成します。
1. **[Public Endpoint]\(パブリック エンドポイント\)** オプションが *[Disabled]\(無効化\)* に設定されていることを確認します。
1. ダイアログの **[Before launch]\(起動前\)** セクションで、 **[Working directory]\(作業ディレクトリ\)** を *piggymetrics/auth-service* フォルダーに切り替えます。
1. **[Deploy Azure Spring Cloud app]\(Azure Spring Cloud アプリのデプロイ\)** ダイアログの下部にある **[Run]\(実行\)** ボタンをクリックして、デプロイを開始します。 
1. これらの手順を繰り返して、`account-service` を構成し、デプロイします。
---

前の手順の出力によって提供された URL に移動して、PiggyMetrics アプリケーションにアクセスします。 例: `https://<service instance name>-gateway.azuremicroservices.io`

![PiggyMetrics へのアクセス](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

また、Azure portal に移動して URL を検索することもできます。 
1. サービスに移動します
2. **[アプリ]** を選択します
3. **[gateway]** を選択します

    ![アプリへの移動](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. **[gateway | Overview]\(ゲートウェイ | 概要\)** ページで、URL を見つけます

    ![2 つ目のアプリへの移動](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする
前の手順では、リソース グループ内に Azure リソースを作成しました。 今後これらのリソースが必要になることが予想されない場合は、ポータルからリソース グループを削除するか、Cloud Shell で次のコマンドを実行します。
```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```
前の手順では、既定のリソース グループ名も設定しました。 この既定の設定を解除するには、Cloud Shell で次のコマンドを実行します。
```azurecli
az configure --defaults group=
```
## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [ログ、メトリック、およびトレース](spring-cloud-quickstart-logs-metrics-tracing.md)
