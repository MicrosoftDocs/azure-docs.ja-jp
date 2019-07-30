---
title: Azure Kubenetes Service と共に Jenkins 用の Azure Dev Spaces プラグインを使用する
description: 継続的インテグレーション パイプラインで Azure Dev Spaces プラグインを使用する方法について説明します。
author: tomarchermsft
ms.author: tarcher
ms.service: jenkins
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/18/2019
ms.openlocfilehash: f5f74ebeb803a5c493f1dbedb6501adf3a88c215
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785667"
---
<!-- GMinchAQ, 06/18/19 -->

# <a name="tutorial-using-the-azure-dev-spaces-plugin-for-jenkins-with-azure-kubenetes-service"></a>チュートリアル:Azure Kubenetes Service と共に Jenkins 用の Azure Dev Spaces プラグインを使用する 

Azure Dev Spaces では、Azure Kubernetes Service (AKS) で実行されるマイクロサービス アプリケーションをテストして反復的に開発でき、依存関係を複製したりモックしたりする必要がありません。 Jenkins 用の Azure Dev Spaces プラグインは、継続的インテグレーションとデリバリー (CI/CD) のパイプラインで開発空間を使用するのに役立ちます。

このチュートリアルでは、Azure Container Registry (ACR) も使用します。 ACR でイメージを格納し、ACR タスクで Docker と Helm の成果物をビルドします。 成果物の生成に ACR と ACR タスクを使用すると、追加のソフトウェア (Docker など) を自分の Jenkins サーバーにインストールする必要がなくなります。 

このチュートリアルでは、以下のタスクを完了します。

> [!div class="checklist"]
> * Azure Dev Spaces が有効な AKS クラスターを作成する
> * マルチサービス アプリケーションを AKS にデプロイする
> * Jenkins サーバーを準備する
> * Jenkins パイプラインで Azure Dev Spaces プラグインを使用して、プロジェクトへのマージ前にコードの変更をプレビューする

このチュートリアルでは、主要な Azure サービス、AKS、ACR、Azure Dev Spaces、Jenkins の[パイプライン](https://jenkins.io/doc/book/pipeline/)とプラグイン、GitHub に関する中級の知識があることを前提としています。 kubectl や Helm など、サポート ツールの基本的な素養があると役に立ちます。

## <a name="prerequisites"></a>前提条件

* Azure アカウント。 Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

* GitHub アカウント。 GitHub アカウントをお持ちでない場合は、開始する前に[無料アカウント](https://github.com/)を作成してください。

* [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) 拡張機能がインストールされた [Visual Studio Code](https://code.visualstudio.com/download)。

* [Azure CLI のインストール](/cli/azure/install-azure-cli?view=azure-cli-latest) (バージョン 2.0.43 以上)。

* Jenkins マスター サーバー Jenkins マスターがまだない場合は、この [クイックスタート](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template)の手順に従って、Azure で [Jenkins](https://aka.ms/jenkins-on-azure)  をデプロイしてください。 

* このチュートリアルの後ろで説明されているとおり、Jenkins サーバーには Helm と kubectl をインストールし、それらを Jenkins アカウントで使用できるようにする必要があります。

* VS Code、VS Code ターミナルまたは WSL、Bash。 


## <a name="create-azure-resources"></a>Azure リソースを作成する

このセクションでは、Azure リソースを作成します。

* このチュートリアル用の Azure リソースがすべて含まれるリソース グループ。
* [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) (AKS) クラスター。
* Docker イメージをビルド (ACR タスクを使用) して格納するための [Azure コンテナー レジストリ](https://docs.microsoft.com/azure/container-registry/) (ACR)。

1. リソース グループを作成します。

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. AKS クラスターを作成します。 [Dev Spaces がサポートされるリージョン](../dev-spaces/about.md#supported-regions-and-configurations)で AKS クラスターを作成します。

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Dev Spaces が使用されるよう AKS を構成します。

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    この手順では、`azds` CLI 拡張機能をインストールします。

4. コンテナー レジストリを作成します。

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>サンプル アプリを AKS クラスターにデプロイする

このセクションでは、開発空間を設定し、前のセクションで作成した AKS クラスターにサンプル アプリケーションをデプロイします。 アプリケーションは、*webfrontend* と *mywebapi* の 2 つの部分で構成されます。 どちらのコンポーネントも開発空間でデプロイされます。 このチュートリアルの後半で、mywebapi に対して pull request を送信し、Jenkins で CI パイプラインをトリガーします。

Azure Dev Spaces の使用、および Azure Dev Spaces を使用したマルチサービスの開発の詳細については、「[Azure Dev Spaces での Java の使用](https://docs.microsoft.com/azure/dev-spaces/get-started-java)」と「[Azure Dev Spaces を使用したマルチサービスの開発](https://docs.microsoft.com/azure/dev-spaces/multi-service-java)」を参照してください。 これらのチュートリアルでは、ここには記載されていないその他の背景情報が提供されます。

1. GitHub から https://github.com/Azure/dev-spaces リポジトリをダウンロードします。

2. VS Code で `samples/java/getting-started/webfrontend` フォルダーを開きます。 (デバッグ アセットの追加やプロジェクトの復元を行うための既定のプロンプトはすべて無視できます。)

3. 次のように `/src/main/java/com/ms/sample/webfrontend/Application.java` を更新します。

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. **[表示]** 、 **[ターミナル]** の順にクリックして、VS Code で統合ターミナルを開きます。

5. `azds prep` コマンドを実行して、開発空間で実行する自分のアプリケーションを準備します。 自分のアプリケーションを適切に準備するには、このコマンドを `dev-spaces/samples/java/getting-started/webfrontend` で実行する必要があります。

    ```bash
    azds prep --public
    ```

    Dev Spaces CLI の `azds prep` コマンドにより、既定の設定で Docker と Kubernetes のアセットが生成されます。 これらのファイルはプロジェクトの有効期間中、保持されます。また、これらはカスタマイズできます。

    * `./Dockerfile` と `./Dockerfile.develop` では、アプリのコンテナー イメージのほか、ソース コードがコンテナー内でどのようにビルドされ、実行されるかを記述しています。
    * `./charts/webfrontend` の下の [Helm チャート](https://helm.sh/docs/developing_charts/)は、Kubernetes にコンテナーを展開する方法を記述しています。
    * `./azds.yaml` は、Azure Dev Spaces の構成ファイルです。

    詳細については、「[Azure Dev Spaces のしくみと構成方法](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)」を参照してください。

6. `azds up` コマンドを使用し、AKS でアプリケーションをビルドして実行します。

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>コンソールの出力をスキャンして、`up` コマンドによって作成された URL に関する情報を探します。 形式は次のようになります。

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    ブラウザー ウィンドウでこの URL を開くと、Web アプリが表示されるはずです。 コンテナーが実行されると、`stdout` と `stderr` の出力がターミナル ウィンドウにストリーミングされます。

8. 次に、*mywebapi* を設定してデプロイします。

    1. ディレクトリを `dev-spaces/samples/java/getting-started/mywebapi` に変更します

    2. ラン

        ```bash
        azds prep
        ```

    3. ラン

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Jenkins サーバーの準備

このセクションでは、サンプル CI パイプラインを実行する Jenkins サーバーを準備します。

* プラグインをインストールする
* Helm と Kubernetes の CLI をインストールする
* 資格情報を追加する

### <a name="install-plugins"></a>プラグインをインストールする

1. 自分の Jenkins サーバーにサインインします。 **[Manage Jenkins]\(Jenkins の管理\)、[Manage Plugins]\(プラグインの管理\)** の順に選択します。
2. **[Available]\(利用可能\)** タブで、次のプラグインを選択します。
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Azure Container Registry タスク](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Environment Injector](https://plugins.jenkins.io/envinject)
    * [GitHub Integration](https://plugins.jenkins.io/github-pullrequest)

    これらのプラグインが一覧に表示されない場合、 **[Installed]\(インストール済み\)** タブをチェックして、これらが既にインストール済みであるかどうかを確認してください。

3. プラグインをインストールするには、 **[Download now and install after restart]\(今すぐダウンロードし、再起動後にインストール\)** を選択します。

4. 自分の Jenkins サーバーを再起動して、インストールを完了します。

### <a name="install-helm-and-kubectl"></a>Helm と kubectl をインストールする

サンプル パイプラインでは、開発空間へのデプロイに Helm と kubectl を使用します。 Jenkins のインストール時に、*jenkins* という名前の管理者アカウントが作成されます。 jenkins ユーザーが Helm と kubectl の両方にアクセスできる必要があります。

1. Jenkins マスターに SSH 接続を行います。 

2. `jenkins` ユーザーに切り替えます。
    ```bash
    sudo su jenkins
    ```

3. Helm CLI をインストールします。 詳細については、「[Installing Helm (Helm のインストール)](https://helm.sh/docs/using_helm/#installing-helm)」を参照してください。

4. kubectl をインストールします。 詳細については、[**az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm) に関するページを参照してください。

### <a name="add-credentials-to-jenkins"></a>資格情報を Jenkins に追加する

1. Jenkins で Azure リソースに対する認証とアクセスを行うには、Azure サービス プリンシパルが必要です。 サービス プリンシパルを作成するには、Azure App Service へのデプロイに関するチュートリアルの「 [サービス プリンシパルの作成](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) 」セクションを参照してください。 `create-for-rbac` の出力のコピーを保存しておいてください。次の手順を完了するために、この情報が必要なためです。 出力は次のようになります。

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. 前の手順のサービス プリンシパル情報を使用して、Jenkins で種類が "*Microsoft Azure サービス プリンシパル*" の資格情報を追加します。 下記のスクリーンショットにある名前は、`create-for-rbac` の出力に対応します。

    **[ID]** フィールドは、自分のサービス プリンシパル用の Jenkins 資格情報名です。 例では、`displayName` の値 (この場合 `xxxxxxxjenkinssp`) が使用されていますが、任意のテキストを使用できます。 この資格情報名は、次のセクションにある AZURE_CRED_ID 環境変数の値です。

    ![サービス プリンシパルの資格情報を Jenkins に追加する](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    **[Description]\(説明\)** は省略可能です。 より詳細な手順については、Azure App Service へのデプロイに関するチュートリアルの「[Jenkins へのサービス プリンシパルの追加](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) 」セクションを参照してください。 



3. 自分の ACR 資格情報を表示するには、このコマンドを実行します。

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    JSON 出力をコピーします。これは次のようになります。

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. Jenkins で種類が "*ユーザー名とパスワード*" の資格情報を追加します。 **username** は前の手順のユーザー名です (この例では `acr01`)。 **password** は 1 つ目のパスワードの値です (この例では `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`)。 この資格情報の **ID** は、ACR_CRED_ID の値です。

5. AKS の資格情報を設定します。 Jenkins で種類が "*Kubernetes 構成 (kubeconfig)* " の資格情報を追加します ([Enter directly]\(直接入力する\) オプションを使用します)。 自分の AKS クラスターのアクセス資格情報を取得するには、次のコマンドを実行します。

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   この資格情報の **ID** は、次のセクションにある KUBE_CONFIG_ID の値です。

## <a name="create-a-pipeline"></a>パイプラインを作成する。

例のパイプラインで選択されているシナリオは、現実のパターンに基づいています。テストとレビューのために、提案された変更をビルドして Azure 開発空間にデプロイする CI パイプラインが pull request によってトリガーされます。 レビューの結果に応じて、変更はマージされて AKS にデプロイされるか、破棄されます。 最後に、開発空間が削除されます。

Jenkins パイプラインの構成と Jenkinsfile では、CI パイプラインのステージを定義します。 このフローチャートは、Jenkinsfile によって定義されたパイプラインのステージと意思決定ポイントを示しています。

![Jenkins パイプラインのフロー](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. [https://github.com/azure-devops/mywebapi](https://github.com/azure-devops/mywebapi ) から、変更されたバージョンの *mywebapi* プロジェクトをダウンロードします。 このプロジェクトには、*Jenkinsfile*、*Dockerfile*、Helm チャートなど、パイプラインの作成に必要ないくつかのファイルが含まれています。

2. Jenkins にログインします。 左側のメニューで **[Add Item]\(項目の追加\)** を選択します。

3. **[Pipeline]\(パイプライン\)** を選択してから、 **[Enter an item name]\(項目の名前を入力する\)** ボックスに名前を入力します。 **[OK]** を選択すると、パイプラインの構成画面が自動的に開きます。

4. **[General]\(全般\)** タブで、 **[Prepare an environment for the run]\(実行用の環境を準備\)** をオンにします。 

5. **[Keep Jenkins Environment Variables]\(Jenkins の環境変数を維持する\)** と **[Keep Jenkins Build Variables]\(Jenkins のビルド変数を維持する\)** をオンにします。

6. **[Properties Content]\(プロパティ コンテンツ\)** ボックスで、次の環境変数を入力します。

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    前のセクションで与えられたサンプルの値を使用すると、環境変数の一覧はこのようになります。

    ![Jenkins パイプラインの環境変数](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. **[Pipeline]\(パイプライン\) の [Definition]\(定義\)** で、 **[Pipeline script from SCM]\(SCM からのパイプライン スクリプト\)** を選択します。
8. **[SCM]** で **[Git]** を選択して、自分のリポジトリ URL を入力します。
9. **[Branch Specifier]\(ブランチ指定子\)** に「`refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`」と入力します。
10. SCM リポジトリの URL とスクリプトのパス "Jenkinsfile" を入力します。
11. **[Lightweight checkout]\(軽量チェックアウト\)** をオンにする必要があります。

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>パイプラインをトリガーする pull request を作成する

このセクションの手順 3 を完了するには、Jenkinsfile のコメント部分が必要になります。これがないと、新旧バージョンを並べて表示しようとした場合に、404 エラーが表示されます。 既定では、PR のマージを選択すると、以前の共有バージョンの mywebapi が削除され、新しいバージョンに置き換えられます。 手順 1 を完了する前に、Jenkinsfile に次の変更を行ってください。

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java` に変更を行って、pull request を作成します。 例:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Jenkins にサインインしてパイプライン名を選択してから、 **[Build Now]\(今すぐビルド\)** を選択します。 

    *Webhook* を設定して Jenkins パイプラインを自動的にトリガーすることもできます。 pull request が入力されると、GitHub によって Jenkins への POST が発行され、パイプラインがトリガーされます。 Webhook の設定の詳細については、[GitHub への Jenkins の接続](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github)に関するページを参照してください。

3. 変更を現在の共有バージョンと比較します。

    1. ブラウザーを開いて、共有バージョン `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io` に移動します。 URL は TEST_ENDPOINT に含まれています。

    2. 別のタブを開いて、PR 開発空間 URL を入力します。 これは、`https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io` のようになります。 リンクは、Jenkins ジョブで **[Build History]\(ビルド履歴\)、<ビルド番号>、[Console Output]\(コンソール出力\)** の順に移動すると見つかります。 `aksapp` のページを検索します。または、プレフィックスだけを確認するために、`azdsprefix` を検索します。

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>子開発空間への URL の構築

pull request を送信すると、Jenkins によって、チームの共有開発空間に基づいて子開発空間が作成され、その子開発空間で pull request のコードが実行されます。 子開発空間への URL は、`http://$env.azdsprefix.<test_endpoint>` という形式になります。 

**$env.azdsprefix** は、Azure Dev Spaces プラグインによるパイプラインの実行中に **devSpacesCreate** によって設定されます。

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

`test_endpoint` は、[「サンプル アプリを AKS クラスターにデプロイする」の手順 7](#test_endpoint) で `azds up` を使用して先ほどデプロイした webfrontend アプリへの URL です。 `$env.TEST_ENDPOINT` の値は、パイプラインの構成で設定されます。 

次のコード スニペットは、子開発空間 URL が `smoketest` ステージでどのように使用されるかを示します。 コードによって、子開発空間 TEST_ENDPOINT が使用できるかどうかが確認され、使用できる場合には、あいさつ文が stdout にダウンロードされます。

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

サンプル アプリケーションの使用が完了したら、リソース グループを削除して Azure リソースをクリーンアップします。

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>次の手順

この記事では、Jenkins 用の Azure Dev Spaces プラグインと Azure Container Registry プラグインを使用して、コードをビルドして開発空間にデプロイする方法について説明しました。

次のリソースの一覧では、Azure Dev Spaces、ACR タスク、Jenkins による CI/CD に関する詳細情報を提供します。

Azure Dev Spaces:
* [Azure Dev Spaces のしくみと構成方法](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

ACR タスク:
* [ACR タスクを使用して OS とフレームワークの修正プログラムの適用を自動化する](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)
* [コードのコミット時の自動ビルド](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

Azure 上の Jenkins による CI/CD:
* [Jenkins の継続的なデプロイ](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)
