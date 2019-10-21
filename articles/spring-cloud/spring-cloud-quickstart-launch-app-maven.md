---
title: クイック スタート:Maven を使用してアプリケーションを起動する - Azure Spring Cloud
description: Maven を使用してサンプル アプリケーションを起動する
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: 01140e94e8d0cc47570824970801bdd0043324d7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166512"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>クイック スタート:Maven プラグインを使用して Azure Spring Cloud アプリを起動する

Azure Spring Cloud の Maven プラグインを使用すると、自分の Azure Spring Cloud サービス アプリケーションを簡単に作成および更新できます。 構成を事前定義することにより、既存の Azure Spring Cloud サービスにアプリケーションをデプロイできます。 この記事では、PiggyMetrics というサンプル アプリケーションを使用して、この機能を紹介します。

>[!Note]
> このクイックスタートを開始する前に、自分の Azure サブスクリプションで Azure Spring Cloud にアクセスできることを確認してください。  プレビュー サービスでは、お客様のサブスクリプションをこちらで許可リストに追加できるように、ご連絡をお願いしています。  Azure Spring Cloud の機能を試してみたい場合は、[このフォームに記入](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
)してください。

>[!TIP]
> Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。  最新バージョンの Git、JDK、Maven、Azure CLI など、一般的な Azure ツールがプレインストールされています。 Azure サブスクリプションにログインしている場合は、shell.azure.com から [Azure Cloud Shell](https://shell.azure.com) を起動します。  Azure Cloud Shell の詳細については、[ドキュメントを参照](../cloud-shell/overview.md)してください

このクイック スタートを完了するには、以下が必要です。

1. [Git をインストールする](https://git-scm.com/)
2. [JDK 8 をインストールする](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Maven 3.0 以上をインストールする](https://maven.apache.org/download.cgi)
4. [Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Azure サブスクリプションにサインアップする](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Azure CLI 拡張機能をインストールする

次のコマンドを使用して、Azure CLI 用の Azure Spring Cloud 拡張機能をインストールします

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Azure portal でサービス インスタンスをプロビジョニングする

1. Web ブラウザーで [Azure portal](https://portal.azure.com) を開き、自分のアカウントにサインインします。

1. **[Azure Spring Cloud]** を探し、選択して概要ページに移動します。 **[作成]** ボタンを選択して、作業を開始します。

1. 以下のガイドラインを考慮して、フォームに入力します。
    - サービス名:自分のサービス インスタンスの名前を指定します。  名前の長さは 4 文字から 32 文字で、小文字、数字、およびハイフンのみを使用できます。  サービス名の最初の文字は英字でなければならず、最後の文字は英字または数字でなければなりません。
    - サブスクリプション:このリソースに対する課金用のサブスクリプションを選択します。  そのサブスクリプションが Microsoft 側で Azure Spring Cloud の許可リストに追加されていることを確認してください。
    - リソース グループ: 新しいリソース用に新しいリソース グループを作成することをお勧めします。
    - 場所:自分のサービス インスタンスの場所を選択します。 現在サポートされている場所は、米国東部、米国西部 2、西ヨーロッパ、東南アジアなどです。
    
サービスのデプロイには約 5 分かかります。  デプロイされると、サービス インスタンスの **[概要]** ページが表示されます。

## <a name="set-up-your-configuration-server"></a>構成サーバーを設定する

1. サービスの **[概要]** ページに移動し、 **[Config Server]\(構成サーバー\)** を選択します。
1. **[既定のリポジトリ]** セクションで **[URI]** を "https://github.com/Azure-Samples/piggymetrics" に設定し、 **[ラベル]** を "構成" に設定して、 **[適用]** を選択し、変更を保存します。

## <a name="clone-and-build-the-sample-application-repository"></a>サンプル アプリケーション リポジトリを複製してビルドする

1. 次のコマンドを実行して、git リポジトリを複製します。

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. 次のコマンドを実行して、ディレクトリを変更し、プロジェクトをビルドします。

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Azure Spring Cloud 構成を生成してデプロイする

1. 以下を `pom.xml` または `settings.xml` に追加して、Maven が Azure Spring Cloud と連携できるようにします。

    ```xml
    <pluginRepositories>
      <pluginRepository>
        <id>maven.snapshots</id>
        <name>Maven Central Snapshot Repository</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
          <enabled>false</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </pluginRepository>
    </pluginRepositories>
    ```

1. 次のコマンドを実行して、構成を生成します。

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    1. `gateway`、`auth-service`、および `account-service` モジュールを選択します。

    1. 自分のサブスクリプションと Azure Spring Cloud サービス クラスターを選択します。

    1. 提示されるプロジェクトの一覧で、`gateway` に対応する番号を入力し、パブリック アクセス権を付与します。
    
    1. 構成を確認します。

1. 次のコマンドを使用して、アプリをデプロイします。

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. 前のコマンドからの出力によって提供された URL を使用して、PiggyMetrics にアクセスできます。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Maven リポジトリから Spring Cloud アプリケーションをデプロイしました。  Azure Spring Cloud の詳細を学習するには、デプロイ用のアプリの準備に関するチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Spring Cloud アプリケーションをデプロイ用に準備する](spring-cloud-tutorial-prepare-app-deployment.md)
