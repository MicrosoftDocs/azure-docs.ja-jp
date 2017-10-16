---
title: "Azure Web Apps 用の Maven プラグインを使用して、Spring Boot アプリを Azure にデプロイする方法"
description: "Azure Web Apps 用の Maven プラグインを使って、Spring Boot アプリを Azure にデプロイする方法について説明します。"
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/07/2017
ms.author: robmcm;kevinzha
ms.openlocfilehash: f22089349e149d8e71df88d5c173869d2a5abad7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-maven-plugin-for-azure-web-apps-to-deploy-a-spring-boot-app-to-azure"></a>Azure Web Apps 用の Maven プラグインを使用して、Spring Boot アプリを Azure にデプロイする方法

[Apache Maven](http://maven.apache.org/) 用の [Azure Web Apps 用 Maven プラグイン](https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin) は、Maven プロジェクトに Azure App Service をシームレスに統合し、開発者が Web アプリを Azure App Service にデプロイする作業を効率化します。

この記事では、Azure Web Apps 用の Maven プラグインを使って、Spring Boot アプリケーションのサンプルを Azure App Service にデプロイする方法について説明します。

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

## <a name="clone-the-sample-spring-boot-web-app"></a>Spring Boot Web アプリのサンプルを複製する

このセクションでは、完成した Spring Boot アプリケーションを複製してローカルでテストします。

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

1. [Spring Boot Getting Started] サンプル プロジェクトを作成したディレクトリに複製します。次に例を示します。
   ```shell
   git clone https://github.com/microsoft/gs-spring-boot
   ```

1. 完成したプロジェクトにディレクトリを変更します。次に例を示します。
   ```shell
   cd gs-spring-boot/complete
   ```

1. Maven を使用して JAR ファイルを構築します。次に例を示します。
   ```shell
   mvn clean package
   ```

1. Web アプリを作成したら、次の例のように Maven を使って Web アプリを起動します。
   ```shell
   mvn spring-boot:run
   ```

1. Web アプリのテストは、Web ブラウザーを使用してアプリをローカルで参照して行います。 たとえば、curl を使うことができる場合は次のようなコマンドを実行できます。
   ```shell
   curl http://localhost:8080
   ```

1. **Greetings from Spring Boot! (Spring Boot からのあいさつ)** というメッセージが表示されます。

## <a name="create-an-azure-service-principal"></a>Azure サービス プリンシパルを作成する

このセクションでは、Azure に Web アプリをデプロイするときに、Maven プラグインが使う Azure サービス プリンシパルを作成します。

1. コマンド プロンプトを開きます。

1. Azure CLI を使って、Azure アカウントにサインインします。
   ```shell
   az login
   ```
   指示に従って、サインインを完了します。

1. Azure サービス プリンシパルを作成します。
   ```shell
   az ad sp create-for-rbac --name "uuuuuuuu" --password "pppppppp"
   ```
   ここでは `uuuuuuuu` がサービス プリンシパルのユーザー名で、`pppppppp` がパスワードです。

1. Azure が次の例のような JSON で応答します。
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
   > Maven プラグインを構成して Azure に Web アプリをデプロイするときに、この JSON の応答にある値を使います。 `aaaaaaaa`、`uuuuuuuu`、`pppppppp`、`tttttttt` はプレースホルダーの値であり、次のセクションで Maven の `settings.xml` ファイルを構成するときにこれらの値を値の各要素にマップしやすくするために、ここでこのように使います。
   >
   >

## <a name="configure-maven-to-use-your-azure-service-principal"></a>Azure サービス プリンシパルを使用するように Maven を構成する

このセクションでは、Azure サービス プリンシパルの値を使って、Web アプリを Azure にデプロイするときに Maven が使う認証を構成します。

1. Maven の `settings.xml` ファイルをテキスト エディターで開くと、次の例のようにパスが記載されていることがあります。
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

1. このチュートリアルの前のセクションで説明した Azure サービス プリンシパルの設定を、次の例のように *settings.xml* ファイルの `<servers>` コレクションに追加します。

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
   `<id>` | Web アプリを Azure にデプロイするとき、セキュリティ設定を検索するために Maven が使う一意の名前を指定します。
   `<client>` | サービス プリンシパルの `appId` 値が含まれています。
   `<tenant>` | サービス プリンシパルの `tenant` 値が含まれています。
   `<key>` | サービス プリンシパルの `password` 値が含まれています。
   `<environment>` | ターゲットの Azure クラウド環境を定義します。この例では `AZURE` です  (環境の全リストは、「[Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]」のドキュメントに記載しています)

1. *settings.xml* ファイルを保存して閉じます。

## <a name="optional-customize-your-pomxml-before-deploying-your-web-app-to-azure"></a>省略可能: Web アプリを Azure にデプロイする前に pom.xml をカスタマイズします。

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
         <resourceGroup>maven-plugin</resourceGroup>
         <appName>maven-web-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <javaVersion>1.8</javaVersion>
         <deploymentType>ftp</deploymentType>
         <resources>
            <resource>
               <directory>${project.basedir}/target</directory>
               <targetPath>/</targetPath>
               <includes>
                  <include>*.jar</include>
               </includes>
            </resource>
            <resource>
               <directory>${project.basedir}</directory>
               <targetPath>/</targetPath>
               <includes>
                  <include>web.config</include>
               </includes>
            </resource>
         </resources>
      </configuration>
   </plugin>
   ```

Maven プラグイン用に変更できる値は複数あります。これらの要素に関する詳しい説明はそれぞれ「[Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]」のドキュメントに記載されています。 この記事でも、次のように重要な値については説明します。

要素 | Description
---|---|---
`<version>` | [Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]のバージョンを指定します。 最新バージョンを使用していることを確認するために、[Maven Central Respository](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22) で一覧表示されているバージョンを確認してください。
`<authentication>` | Azure の認証情報を指定します。この例では `azure-auth` を含む `<serverId>` 要素が認証情報です。Maven はこの値を、この記事の前のセクションで定義した Maven の*settings.xml* ファイル内にある Azure サービス プリンシパルを見つけるために使います。
`<resourceGroup>` | ターゲット リソース グループを指定します。この例では `maven-plugin` です。 リソース グループが存在しない場合は、デプロイ中に新しいリソース グループが作成されます。
`<appName>` | Web アプリのターゲット名を指定します。 この例では、ターゲット名は `maven-web-app-${maven.build.timestamp}` です。混乱を避けるため、この例ではサフィックスの `${maven.build.timestamp}` を追加しています  (タイムスタンプは省略可能です。アプリ名には一意の文字列を指定できます)。
`<region>` | ターゲット リージョンを指定します。この例では `westus` です  (完全なリストについては、「[Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]」(Azure Web Apps 用の Maven プラグイン) をご覧ください)。
`<javaVersion>` | Web アプリの Java ランタイム バージョンを指定します  (完全なリストについては、「[Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]」(Azure Web Apps 用の Maven プラグイン) をご覧ください)。
`<deploymentType>` | Web アプリのデプロイの種類を指定します。 現時点では `ftp` のみがサポートされていますが、他のデプロイの種類のサポートも開発中です。
`<resources>` | Web アプリを Azure にデプロイするときに Maven が使うリソースとターゲットの場所を指定します。 この例では、2 つの `<resource>` 要素で、Maven が Web アプリの JAR ファイルと Spring Boot プロジェクトからの *web.config* ファイルをデプロイすることを指定しています。

## <a name="build-and-deploy-your-web-app-to-azure"></a>Web アプリをビルドして Azure にデプロイする

この記事の前のセクションで説明した設定をすべて構成した後、Azure に Web アプリをデプロイします。 そのためには、次の手順を実行してください。

1. *pom.xml* ファイルを変更する場合は、以前使っていたコマンド プロンプトまたはターミナル ウィンドウで、次の例のように Maven を使って JAR ファイルをリビルドします。
   ```shell
   mvn clean package
   ```

1. Maven を使って次の例のように Azure に Web アプリをデプロイします。
   ```shell
   mvn azure-webapp:deploy
   ```

Maven が Web アプリを Azure にデプロイします。Web アプリが存在しない場合は新たに作成されます。

Web アプリのデプロイが完了すると、[Azure Portal] を使って Web アプリを管理できるようになります。

* Web アプリは **App Services** に一覧表示されます。

   ![Azure Portal の App Services に一覧表示される Web アプリ][AP01]

* Web アプリの URL は、Web アプリの **[概要]** に一覧表示されます。

   ![Web アプリの URL の決定][AP02]

<!--
##  OPTIONAL: Configure the embedded Tomcat server to run on a different port

The embedded Tomcat server in the sample Spring Boot application is configured to run on port 8080 by default. However, if you want to run the embedded Tomcat server to run on a different port, such as port 80 for local testing, you can configure the port by using the following steps.

1. Go to the *resources* directory (or create the directory if it does not exist); for example:
   ```shell
   cd src/main/resources
   ```

1. Open the *application.yml* file in a text editor if it exists, or create a new YAML file if it does not exist.

1. Modify the **server** setting so that the server runs on port 80; for example:
   ```yaml
   server:
      port: 80
   ```

1. Save and close the *application.yml* file.
-->

## <a name="next-steps"></a>次のステップ

この記事で説明しているさまざまなテクノロジの詳細については、次の記事をご覧ください。

* [Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]

* [Azure CLI から Azure へのログイン](/azure/xplat-cli-connect)

* [Azure Web Apps 用の Maven プラグインを使用して、コンテナー化された Spring Boot アプリを Azure にデプロイする方法](containers/app-service-web-deploy-containerized-spring-boot-app-with-maven-plugin.md)

* [Azure CLI 2.0 で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Maven の設定リファレンス](https://maven.apache.org/settings.html)

<!-- URL List -->

[Azure コマンド ライン インターフェイス (CLI)]: /cli/azure/overview
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Azure Portal]: https://portal.azure.com/
[無料の Azure アカウント]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN サブスクライバーの特典]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot Getting Started]: https://github.com/microsoft/gs-spring-boot
[Spring Framework]: https://spring.io/
[Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin

<!-- IMG List -->

[AP01]: ./media/app-service-web-deploy-spring-boot-app-with-maven-plugin/AP01.png
[AP02]: ./media/app-service-web-deploy-spring-boot-app-with-maven-plugin/AP02.png
