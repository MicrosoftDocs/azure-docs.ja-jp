---
title: クイックスタート - Maven と Jib を使用して Java コンテナー イメージを作成し Azure Container Registry にプッシュする
description: Maven Jib プラグインを使用して、コンテナー化された Java アプリを作成し、Azure Container Registry にプッシュします。
author: KarlErickson
ms.custom: devx-track-java, devx-track-azurecli
ms.author: karler
ms.topic: quickstart
ms.date: 02/26/2020
ms.openlocfilehash: 9e400ee0bae2690a84f9cfd3f6a76359e08eabc2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92018340"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>クイックスタート: Java コンテナー イメージを作成して Azure Container Registry にプッシュする

このクイックスタートでは、Maven Jib プラグインを使用して、コンテナー化された Java アプリを作成し、Azure Container Registry にプッシュする方法について説明します。 Maven と Jib の使用は、開発者ツールを使った Azure コンテナー レジストリの対話操作の一例です。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、または[無料の Azure アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップできます。
* [Azure コマンド ライン インターフェイス (CLI)](/cli/azure/overview)。
* サポートされている Java Development Kit (JDK)。 Azure での開発時に使用可能な JDK の詳細については、<https://aka.ms/azure-jdks> を参照してください。
* Apache の [Maven](http://maven.apache.org) ビルド ツール (バージョン 3 以上)。
* [Git](https://git-scm.com) クライアント。
* [Docker](https://www.docker.com) クライアント。
* [ACR Docker 資格情報ヘルパー](https://github.com/Azure/acr-docker-credential-helper)。

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Spring Boot on Docker Getting Started Web アプリを作成する

次の手順で、Spring Boot Web アプリケーションをビルドしてローカルでテストします。

1. コマンド プロンプトから次のコマンドを使用して [Spring Boot on Docker Getting Started](https://github.com/spring-guides/gs-spring-boot-docker) サンプル プロジェクトをクローンします。

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. 完成したプロジェクトにディレクトリを変更します。

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Maven を使用してサンプル アプリをビルドして実行します。

   ```bash
   mvn package spring-boot:run
   ```

1. Web アプリをテストするには、 `http://localhost:8080` を参照するか、次の `curl` コマンドを使用します。

   ```bash
   curl http://localhost:8080
   ```

次のメッセージが表示されます。**Hello Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Azure CLI を使用して Azure Container Registry を作成する

次に、Azure リソース グループと ACR を作成します。次の手順に従ってください。

1. 次のコマンドを使用して、Azure アカウントにログインします。

   ```azurecli
   az login
   ```

1. 使用する Azure サブスクリプションを指定します。

   ```azurecli
   az account set -s <subscription ID>
   ```

1. このチュートリアルで使用する Azure リソースのリソース グループを作成します。 次のコマンド内のプレースホルダーは、実際のリソース名と場所 (`eastus`) に置き換えてください。

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. 次のコマンドを使用して、リソース グループにプライベート Azure コンテナー レジストリを作成します。 プレースホルダーは実際の値に置き換えてください。 このチュートリアルでは、後の手順で、このレジストリに Docker イメージとしてサンプル アプリをプッシュします。

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Jib を使用してアプリをコンテナー レジストリにプッシュする

最後にプロジェクトの構成を更新し、コマンド プロンプトを使用してイメージを作成、デプロイします。

> [!NOTE]
> 先ほど作成した Azure コンテナー レジストリにログインするには、Docker デーモンが実行されている必要があります。 マシンに Docker をインストールするには、[公式の Docker ドキュメント](https://docs.docker.com/install/)を参照してください。

1. Azure CLI から次のコマンドを使用して Azure Container Registry にログインします。 プレースホルダーは、実際のレジストリ名に置き換えてください。

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   `az acr` コマンドで使用する既定のレジストリ名は、`az configure` コマンドによって設定されます。

1. Spring Boot アプリケーションの完了プロジェクト ディレクトリ ("*C:\SpringBoot\gs-spring-boot-docker\complete*" や " */users/robert/SpringBoot/gs-spring-boot-docker/complete*" など) に移動し、*pom.xml* ファイルをテキスト エディターで開きます。

1. *pom.xml* ファイル内の `<properties>` コレクションを次の XML で更新します。 プレースホルダーは実際のレジストリ名に置き換えてください。さらに、`<jib-maven-plugin.version>` プロパティを追加し、その値に `2.2.0` または [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) の新しいバージョンを指定します。

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
   </properties>
   ```

1. 以下の例に示すように *pom.xml* ファイルの `<plugins>` コレクションを更新し、`<plugin>` 要素に `jib-maven-plugin` のエントリが含まれるようにします。 ここでは、Microsoft Container Registry (MCR) の基本イメージ (`mcr.microsoft.com/java/jdk:8-zulu-alpine`) が使用されていることに注目してください。このイメージには、公式にサポートされた Azure 用の JDK が含まれています。 その他公式にサポートされた JDK を含む MCR 基本イメージについては、「[Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)」、「[Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)」、「[Java SE ヘッドレス JRE](https://hub.docker.com/_/microsoft-java-jre-headless)」、「[Java SE JDK と Maven](https://hub.docker.com/_/microsoft-java-maven)」を参照してください。

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Spring Boot アプリケーション用の完了プロジェクト ディレクトリに移動し、次のコマンドを実行してイメージを作成し、そのイメージをレジストリにプッシュします。

   ```bash
   az acr login && mvn compile jib:build
   ```

> [!NOTE]
>
> `az acr login` によって作成される資格情報の有効期間は、セキュリティ上の理由から 1 時間のみです。 "*401 未承認*" エラーが発生した場合は、もう一度 `az acr login -n <your registry name>` コマンドを実行すれば、再認証を行うことができます。

## <a name="verify-your-container-image"></a>コンテナー イメージを検証する

おめでとうございます。 Azure でサポートされている JDK 上に、コンテナー化された Java App を作成し、ACR にプッシュしました。 そのイメージを Azure App Service にデプロイしたり、次のコマンドで (プレースホルダーを適宜置き換えて) ローカルにプルしたりすることによってテストしてみましょう。

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker
```

## <a name="next-steps"></a>次のステップ

その他 Microsoft によって公式にサポートされている Java 基本イメージの各種バージョンについては、次のページを参照してください。

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE ヘッドレス JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK と Maven](https://hub.docker.com/_/microsoft-java-maven)

Spring および Azure の詳細については、Azure ドキュメント センターで引き続き Spring に関するドキュメントをご確認ください。

> [!div class="nextstepaction"]
> [Azure の Spring](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>その他のリソース

詳細については、次のリソースを参照してください。

* [Java 開発者向けの Azure](/azure/java)
* [Azure DevOps と Java の操作](/azure/devops/java)
* [Docker での Spring Boot の使用開始](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Spring Boot アプリケーションを Azure App Service にデプロイする](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-on-linux#configure-maven-to-build-image-to-your-azure-container-registry)
* [Azure Web App on Linux 向けのカスタム Docker イメージを使用する](../app-service/tutorial-custom-container.md)
