---
title: "Azure Web Apps 用の Maven プラグインを使用して Azure Container Registry の Spring Boot アプリを Azure App Service にデプロイする方法"
description: "このチュートリアルでは、Maven プラグインを使用して Azure Container Registry の Spring Boot アプリケーションを Azure App Service にデプロイする方法について順を追って説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/07/2017
ms.author: robmcm;kevinzha
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: f47ee59d72ea49d62be2cb435ebaf8bc841e4198
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---

# <a name="how-to-use-the-maven-plugin-for-azure-web-apps-to-deploy-a-spring-boot-app-in-azure-container-registry-to-azure-app-service"></a>Azure Web Apps 用の Maven プラグインを使用して Azure Container Registry の Spring Boot アプリを Azure App Service にデプロイする方法

**[Spring Framework]** は、Java 開発者による Web、モバイル、および API アプリケーションの作成を支援する一般的なオープンソース フレームワークです。 このチュートリアルでは、[Spring Boot] (Spring の使用をすばやく開始するための規約駆動型手法) を使用して作成されたサンプル アプリを使用します。

この記事では、Spring Boot アプリケーションのサンプルを Azure Container Registry にデプロイし、Azure Web Apps 用の Maven プラグインを使用して、アプリケーションを Azure App Service にデプロイする方法を説明します。

> [!NOTE]
>
> Azure Web Apps の Maven プラグインは現在プレビューとして提供されています。 今後、機能が追加される予定ですが、現在は FTP 発行のみがサポートされています。
>

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を完了するには、次の前提条件を満たす必要があります。

* Azure サブスクリプション。Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典]を有効にするか、または[無料の Azure アカウント]にサインアップできます。
* [Azure コマンド ライン インターフェイス (CLI)]。
* 最新の Java Development Kit (JDK) (バージョン 1.7 以降)。
* Apache の [Maven] 構築ツール (バージョン 3)。
* [Git] クライアント。
* [Docker] クライアント。

> [!NOTE]
>
> このチュートリアルには仮想化要件があるため、仮想マシンでこの記事の手順を実行することはできません。仮想化機能を有効にした物理コンピューターを使用する必要があります。
>

## <a name="clone-the-sample-spring-boot-on-docker-web-app"></a>Docker Web アプリへの Spring Boot サンプルの複製

このセクションでは、コンテナー化された Spring Boot アプリケーションを複製してローカルでテストします。

1. コマンド プロンプトまたはターミナル ウィンドウを開き、Spring Boot アプリケーションを保持するためのローカル ディレクトリを作成して、次の例のようにそのディレクトリを変更します。
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- または --
   ```shell
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. [Docker での Spring Boot の使用開始]のサンプル プロジェクトを今作成したディレクトリに複製します。次に例を示します。
   ```shell
   git clone -b private-registry https://github.com/Microsoft/gs-spring-boot-docker
   ```

1. 完成したプロジェクトにディレクトリを変更します。次に例を示します。
   ```shell
   cd gs-spring-boot-docker/complete
   ```

1. Maven を使用して JAR ファイルを構築します。次に例を示します。
   ```shell
   mvn clean package
   ```

1. Web アプリを作成したら、次の例のように Maven を使用して Web アプリを起動します。
   ```shell
   mvn spring-boot:run
   ```

1. Web アプリのテストは、Web ブラウザーを使用してアプリをローカルで参照して行います。 たとえば、curl を使用できる場合は次のようなコマンドを実行できます。
   ```shell
   curl http://localhost:8080
   ```

1. 次のメッセージが表示されるはずです。**Hello Docker World**

   ![サンプル アプリをローカルに参照する][SB01]

## <a name="create-an-azure-service-principal"></a>Azure サービス プリンシパルの作成

このセクションでは、Azure にコンテナーをデプロイするときに、Maven プラグインが使用する Azure サービス プリンシパルを作成します。

1. コマンド プロンプトを開きます。

1. Azure CLI を使用して、Azure アカウントにサインインします。
   ```azurecli
   az login
   ```
   指示に従って、サインインを完了します。

1. Azure サービス プリンシパルを作成します。
   ```azurecli
   az ad sp create-for-rbac --name "uuuuuuuu" --password "pppppppp"
   ```
   ここでは `uuuuuuuu` がサービス プリンシパルのユーザー名で、`pppppppp` がパスワードです。

1. Azure が次の例のように JSON で応答します。
   ```json
   {
      "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "displayName": "uuuuuuuu",
      "name": "http://uuuuuuuu",
      "password": "pppppppp",
      "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
   }
   ```

   > [!NOTE]
   >
   > Maven プラグインを構成して Azure にコンテナーをデプロイするときに、この JSON の応答にある値を使用します。 `aaaaaaaa``uuuuuuuu``pppppppp``tttttttt` はプレースホルダーの値であり、次のセクションで Maven の `settings.xml` ファイルを構成するときにこれらの値を値の各要素にマップしやすくするために、ここでこのように使用しています。
   >
   >

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Azure CLI を使用して Azure Container Registry を作成する

1. コマンド プロンプトを開きます。

1. Azure アカウントにログインします。
   ```azurecli
   az login
   ```

1. この記事で使用する Azure リソースのリソース グループを作成します。
   ```azurecli
   az group create --name=wingtiptoysresources --location=westus
   ```
   この例の `wingtiptoysresources` をリソース グループの一意の名前に置き換えます。

1. リソース グループ内に、Spring Boot アプリ用のプライベートな Azure コンテナー レジストリを作成します。 
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoysresources --location westus --name wingtiptoysregistry --sku Basic
   ```
   この例の `wingtiptoysregistry` をコンテナー レジストリの一意の名前に置き換えます。

1. コンテナー レジストリのパスワードを取得します。
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```
   Azure が次の例のようにパスワードを返します。
   ```json
   {
      "name": "password",
      "value": "xxxxxxxxxx"
   }
   ```

## <a name="add-your-azure-container-registry-and-azure-service-principal-to-your-maven-settings"></a>Maven の設定に Azure コンテナー レジストリと Azure サービス プリンシパルを追加する

1. Maven の `settings.xml` ファイルをテキスト エディターで開くと、次の例のようにパスが記載されていることがあります。
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

1. この記事の前のセクションで説明した Azure Container Registry のアクセス設定を、次の例のように *settings.xml* ファイルの `<servers>` コレクションに追加します。

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>xxxxxxxxxx</password>
      </server>
   </servers>
   ```
   各値の説明:
   要素 | Description
   ---|---|---
   `<id>` | プライベートな Azure コンテナー レジストリの名前が含まれています。
   `<username>` | プライベートな Azure コンテナー レジストリの名前が含まれています。
   `<password>` | この記事の前のセクションで取得したパスワードが含まれています。

1. この記事の前のセクションで説明した Azure サービス プリンシパル設定を、次の例のように *settings.xml* ファイルの `<servers>` コレクションに追加します。

   ```xml
   <servers>
      <server>
        <id>azure-auth</id>
         <configuration>
            <client>aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa</client>
            <tenant>tttttttt-tttt-tttt-tttt-tttttttttttt</tenant>
            <key>pppppppp</key>
            <environment>AZURE</environment>
         </configuration>
      </server>
   </servers>
   ```
   各値の説明:
   要素 | Description
   ---|---|---
   `<id>` | Web アプリを Azure にデプロイする際、セキュリティ設定を検索するために Maven が使用する一意の名前を指定します。
   `<client>` | サービス プリンシパルの `appId` 値が含まれています。
   `<tenant>` | サービス プリンシパルの `tenant` 値が含まれています。
   `<key>` | サービス プリンシパルの `password` 値が含まれています。
   `<environment>` | ターゲットの Azure クラウド環境を定義します。この例では `AZURE` です。 (環境の全リストは、「[Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]」のドキュメントに記載しています)

1. *settings.xml* ファイルを保存して閉じます。

## <a name="build-your-docker-container-image-and-push-it-to-your-azure-container-registry"></a>Docker コンテナー イメージを構築し、Azure コンテナー レジストリにプッシュする

1. Spring Boot アプリケーションの完了プロジェクトディレクトリ ("*C:\SpringBoot\gs-spring-boot-docker\complete*" や "*/users/robert/SpringBoot/gs-spring-boot-docker/complete*" など) に移動し、*pom.xml* ファイルをテキスト エディターで開きます。

1. *pom.xml* ファイル内の `<properties>` コレクションを、このチュートリアルの前のセクションにあった Azure Container Registry のログイン サーバー値で更新します。次に例を示します。

   ```xml
   <properties>
      <azure.containerRegistry>wingtiptoysregistry</azure.containerRegistry>
      <docker.image.prefix>${azure.containerRegistry}.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <maven.build.timestamp.format>yyyyMMddHHmmssSSS</maven.build.timestamp.format>
   </properties>
   ```
   各値の説明:
   要素 | Description
   ---|---|---
   `<azure.containerRegistry>` | プライベートな Azure コンテナー レジストリの名前を指定します。
   `<docker.image.prefix>` | プライベートな Azure コンテナー レジストリの URL (プライベートなコンテナー レジストリの名前に ".azurecr.io" が追加されたもの) を指定します。

1. *pom.xml* ファイル内の Docker プラグインの `<plugin>` に、このチュートリアルの前の手順で説明したログイン サーバー アドレスとレジストリ名の正しいプロパティが含まれていることを確認します。 For example:

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <registryUrl>https://${docker.image.prefix}</registryUrl>
         <serverId>${azure.containerRegistry}</serverId>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
      </configuration>
   </plugin>
   ```
   各値の説明:
   要素 | Description
   ---|---|---
   `<serverId>` | プライベートな Azure コンテナー レジストリの名前を含むプロパティを指定します。
   `<registryUrl>` | プライベートな Azure コンテナー レジストリの URL を含むプロパティを指定します。

1. Spring Boot アプリケーション用の完了プロジェクト ディレクトリに移動し、次のコマンドを実行してアプリケーションをリビルドし、コンテナーを Azure コンテナー レジストリにプッシュします。

   ```
   mvn package docker:build -DpushImage 
   ```

1. 省略可能: [Azure Portal] を参照して、コンテナー レジストリに **gs-spring-boot-docker** という名前の Docker コンテナー イメージがあることを確認します。

   ![Azure Portal でコンテナーを確認][CR01]

## <a name="customize-your-pomxml-then-build-and-deploy-your-container-to-azure"></a>pom.xml をカスタマイズしてコンテナーを構築し Azure にデプロイする

Spring Boot アプリケーションの `pom.xml` ファイルをテキスト エディターで開き、`azure-webapp-maven-plugin` の `<plugin>` 要素を見つけます。 この要素は次の例のようになっています。

   ```xml
   <plugin>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-webapp-maven-plugin</artifactId>
      <version>0.1.3</version>
      <configuration>
         <authentication>
            <serverId>azure-auth</serverId>
         </authentication>
         <resourceGroup>wingtiptoysresources</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
            <registryUrl>https://${docker.image.prefix}</registryUrl>
            <serverId>${azure.containerRegistry}</serverId>
         </containerSettings>
         <appSettings>
            <property>
               <name>PORT</name>
               <value>8080</value>
            </property>
         </appSettings>
      </configuration>
   </plugin>
   ```

Maven プラグイン用に変更できる値は複数あります。これらの要素に関する詳しい説明はそれぞれ「[Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]」のドキュメントに記載されています。 この記事でも、次のように重要な値については説明します。

要素 | Description
---|---|---
`<version>` | [Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]のバージョンを指定します。 最新バージョンを使用していることを確認するために、[Maven Central Respository](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22) で一覧表示されているバージョンを確認してください。
`<authentication>` | Azure の認証情報を指定します。この例では `azure-auth` を含む `<serverId>` 要素が認証情報です。Maven はこの値を、この記事の前のセクションで定義した Maven の*settings.xml* ファイル内にある Azure サービス プリンシパルを見つけるために使用します。
`<resourceGroup>` | ターゲット リソース グループを指定します。この例では `wingtiptoysresources` です。 リソース グループが存在しない場合は、デプロイ中に新しいリソース グループが作成されます。
`<appName>` | Web アプリのターゲット名を指定します。 この例では、ターゲット名は `maven-linux-app-${maven.build.timestamp}` です。混乱を避けるため、この例ではサフィックスの `${maven.build.timestamp}` を追加しています。 (タイムスタンプは省略可能です。アプリ名には一意の文字列を指定できます。)
`<region>` | ターゲット リージョンを指定します。この例では `westus` です。 (全リストは、「[Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]」のドキュメントに記載しています。)
`<containerSettings>` | コンテナーの名前を含むプロパティと URL を含むプロパティを指定します。
`<appSettings>` | Azure に Web アプリをデプロイするときに使用するために、Maven 用の一意の設定を指定します。 この例では、`<property>` 要素には、アプリのポートを指定する子要素の名前と値のペアが含まれています。

> [!NOTE]
>
> 既定と異なるポートに変更する場合のみ、この例のポート番号を変更する設定が必要になります。
>

1. *pom.xml* ファイルに変更を加える場合は、以前使用していたコマンド プロンプトまたはターミナル ウィンドウで、次の例のように Maven を使用して JAR ファイルをリビルドします。
   ```shell
   mvn clean package
   ```

1. Maven を使用して次の例のように Azure に Web アプリをデプロイします。
   ```shell
   mvn azure-webapp:deploy
   ```

Maven が Web アプリを Azure にデプロイします。Web アプリが存在しない場合は新たに作成されます。

> [!NOTE]
>
> デプロイ開始時に、*pom.xml* ファイルの `<region>` 要素で指定したリージョンに十分な数の使用可能なサーバーがない場合は、次の例のようなエラーが表示されることがあります。
>
> ```
> [INFO] Start deploying to Web App maven-linux-app-20170804...
> [INFO] ------------------------------------------------------------------------
> [INFO] BUILD FAILURE
> [INFO] ------------------------------------------------------------------------
> [INFO] Total time: 31.059 s
> [INFO] Finished at: 2017-08-04T12:15:47-07:00
> [INFO] Final Memory: 51M/279M
> [INFO] ------------------------------------------------------------------------
> [ERROR] Failed to execute goal com.microsoft.azure:azure-webapp-maven-plugin:0.1.3:deploy (default-cli) on project gs-spring-boot-docker: null: MojoExecutionException: CloudException: OnError while emitting onNext value: retrofit2.Response.class
> ```
>
> この場合、別のリージョンを指定し、Maven コマンドを再実行してアプリケーションをデプロイできます。
>
>

Web アプリのデプロイが完了すると、[Azure Portal] を使用して Web アプリを管理できるようになります。

* Web アプリは **App Services** に一覧表示されます。

   ![Azure Portal の App Services に一覧表示される Web アプリ][AP01]

* Web アプリの URL は、Web アプリの **[概要]** に一覧表示されます。

   ![Web アプリの URL の決定][AP02]

## <a name="next-steps"></a>次のステップ

この記事で説明しているさまざまなテクノロジの詳細については、次の記事をご覧ください。

* [Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]

* [Azure CLI から Azure へのログイン](/azure/xplat-cli-connect)

* [Azure CLI 2.0 で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Maven の設定リファレンス](https://maven.apache.org/settings.html)

* [Maven 用の Docker プラグイン]

<!-- URL List -->

[Azure コマンド ライン インターフェイス (CLI)]: /cli/azure/overview
[Azure Container Service (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Azure Portal]: https://portal.azure.com/
[Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Using a custom Docker image for Azure Web App on Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[Maven 用の Docker プラグイン]: https://github.com/spotify/docker-maven-plugin
[無料の Azure アカウント]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN サブスクライバーの特典]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Docker での Spring Boot の使用開始]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/SB01.png
[CR01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/CR01.png
[AP01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/AP01.png
[AP02]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/AP02.png

