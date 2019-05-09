---
title: Jenkins Azure Functions プラグインを使用した Azure Functions へのデプロイ
description: Jenkins Azure Functions プラグインを使用して Azure Functions にデプロイする方法を説明します
ms.service: jenkins
keywords: Jenkins, Azure, DevOps, Java, Azure Functions
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 02/23/2019
ms.openlocfilehash: bd8fa10ca0a9809891efc67ff930ab01d502eda9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58117083"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plugin"></a>Jenkins Azure Functions プラグインを使用した Azure Functions へのデプロイ

[Azure Functions](/azure/azure-functions/) はサーバーレス コンピューティング サービスです。 Azure Functions を使用すると、インフラストラクチャをプロビジョニングまたは管理することなく、オンデマンドでコードを実行できます。 このチュートリアルでは、Azure Functions プラグインを使用して Java 関数を Azure Functions にデプロイする方法を説明します。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。
- **Jenkins サーバー**: Jenkins サーバーがインストールされていない場合は、[Azure での Jenkins サーバーの作成](./install-jenkins-solution-template.md)に関する記事を参照してください。

  > [!TIP]
  > このチュートリアルに使用されているソース コードは [Visual Studio China GitHub リポジトリ](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java)にあります。

## <a name="create-a-java-function"></a>Java 関数の作成

Java ランタイム スタックを備えた Java 関数を作成するには、[Azure portal](https://portal.azure.com) または [Azure CLI](/cli/azure/?view=azure-cli-latest) を使用します。

次の手順では、Azure CLI を使用して Java 関数を作成する方法を説明します。

1. **&lt;resource_group>** プレースホルダーを実際のリソース グループ名に置き換えて、リソース グループを作成します。

    ```cli
    az group create --name <resource_group> --location eastus
    ```

1. プレースホルダーを適切な値に置き換えて、Azure ストレージ アカウントを作成します。
 
    ```cli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. プレースホルダーを適切な値に置き換えて、テスト用の関数アプリを作成します。

    ```cli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```
    
1. プレースホルダーを適切な値に置き換えて、バージョン 2.x ランタイムに更新します。

    ```cli
    az functionapp config appsettings set --name <function_app> --resource-group <resource_group> --settings FUNCTIONS_EXTENSION_VERSION=~2
    ```

## <a name="prepare-jenkins-server"></a>Jenkins サーバーの準備

次の手順では、Jenkins サーバーを準備する方法について説明します。

1. Azure に [Jenkins サーバー](https://aka.ms/jenkins-on-azure)をデプロイします。 Jenkins サーバーのインスタンスをまだインストールしていない場合は、[Azure での Jenkins サーバーの作成](./install-jenkins-solution-template.md)に関する記事でそのプロセスが説明されています。

1. SSH を使用して Jenkins インスタンスにサインインします。

1. Jenkins インスタンスで、次のコマンドを使用して maven をインストールします。

    ```terminal
    sudo apt install -y maven
    ```

1. Jenkins インスタンスのターミナルのプロンプトで、次のコマンドを実行して [Azure Functions Core Tools](/azure/azure-functions/functions-run-local) をインストールします。

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. Jenkins ダッシュボードで、次のプラグインをインストールします。

    - Azure Functions プラグイン
    - EnvInject プラグイン

1. Jenkins で Azure リソースに対する認証とアクセスを行うには、Azure サービス プリンシパルが必要です。 詳細な手順については、[Azure App Service へのデプロイ](./tutorial-jenkins-deploy-web-app-azure-app-service.md)に関する記事を参照してください。

1. Azure サービス プリンシパルを使用して、資格情報の種類 "Microsoft Azure サービス プリンシパル" を Jenkins に追加します。 [Azure App Service へのデプロイ](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins)に関するチュートリアルを参照してください。

## <a name="fork-the-sample-github-repo"></a>サンプル GitHub リポジトリのフォーク

1. [奇数または偶数のサンプル アプリ用の GitHub リポジトリにサインインします](https://github.com/VSChina/odd-or-even-function.git)。

1. GitHub の右上隅にある **[Fork]\(フォーク\)** を選択します。

1. プロンプトに従ってお客様の GitHub アカウントを選択し、フォークを完了します。

## <a name="create-a-jenkins-pipeline"></a>Jenkins パイプラインの作成

このセクションでは、[Jenkins パイプライン](https://jenkins.io/doc/book/pipeline/)を作成します。

1. Jenkins ダッシュボードで、パイプラインを作成します。

1. **[Prepare an environment for the run]\(実行環境を準備する\)** を有効にします。

1. 次の環境変数を **[Properties Content]\(プロパティのコンテンツ\)** に追加します。その際、プレースホルダーは、環境に合った適切な値に置き換えます。

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. **[Pipeline]\(パイプライン\)、[Definition]\(定義\)** セクションの順に移動し、**[Pipeline script from SCM]\(SCM からのパイプライン スクリプト\)** を選択します。

1. GitHub フォークの URL とスクリプト パス ("doc/resources/jenkins/JenkinsFile") を入力し、[JenkinsFile の例](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile)で使用します。

   ```
   node {
    stage('Init') {
        checkout scm
        }

    stage('Build') {
        sh 'mvn clean package'
        }

    stage('Publish') {
        azureFunctionAppPublish appName: env.FUNCTION_NAME, 
                                azureCredentialsId: env.AZURE_CRED_ID, 
                                filePath: '**/*.json,**/*.jar,bin/*,HttpTrigger-Java/*', 
                                resourceGroup: env.RES_GROUP, 
                                sourceDirectory: 'target/azure-functions/odd-or-even-function-sample'
        }
    }
    ```

## <a name="build-and-deploy"></a>ビルドとデプロイ

ここで、Jenkins ジョブを実行します。

1. 最初に、記事「[Azure Functions のトリガーとバインド](/azure/azure-functions/functions-bindings-http-webhook#authorization-keys)」の手順を使用して、承認キーを取得します。

1. ブラウザーでアプリの URL を入力します。 プレースホルダーを適切な値に置き換え、Java 関数に対する入力として **&lt;input_number>** の数値を指定します。

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. 次の出力例のような結果が表示されます (この場合、テストとして 365 という奇数が使用されました)。

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリケーションを引き続き使用しない場合は、作成したリソースを次の手順で削除します。

```cli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>次の手順

Azure Functions の詳細については、次のリソースを参照してください。
> [!div class="nextstepaction"]
> [Azure Functions のドキュメント](/azure/azure-functions/)
