---
title: チュートリアル:MongoDB を使用する Linux Java アプリ
description: Azure で実行されている MongoDB (Cosmos DB) に接続して、データ駆動型 Linux Java アプリを Azure App Service で動作させる方法について説明します。
author: rloutlaw
ms.author: routlaw
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: mvc, seodec18, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 5a481e7ef37e36578b7f71a7afe70dcad737de89
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217467"
---
# <a name="tutorial-build-a-java-spring-boot-web-app-with-azure-app-service-on-linux-and-azure-cosmos-db"></a>チュートリアル:Azure App Service on Linux と Azure Cosmos DB を使用して Java Spring Boot Web アプリを構築する

このチュートリアルでは、Azure で Java Web アプリを構築、構成、デプロイ、およびスケーリングするプロセスを、順を追って説明します。 完了すると、[Azure App Service on Linux](overview.md) で実行中の [Azure Cosmos DB](../cosmos-db/index.yml) にデータを格納する [Spring Boot](https://projects.spring.io/spring-boot/) アプリケーションが完成します。

![Azure Cosmos DB にデータを格納する Spring Boot アプリケーション](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Cosmos DB データベースを作成する。
> * サンプル アプリをデータベースに接続し、ローカルでテストする
> * サンプル アプリを Azure にデプロイする
> * App Service から診断ログをストリーム配信する
> * 他のインスタンスを追加してサンプル アプリをスケールアウトする

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* 開発コンピューターに [Azure CLI](/cli/azure/overview) がインストールされている。 
* [Git](https://git-scm.com/)
* [Java JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>サンプルの TODO アプリを複製してリポジトリを準備する

このチュートリアルでは、[Spring Data Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb) を基盤とする Spring REST API を呼び出す Web UI を備えた、サンプルの TODO リスト アプリを使用します。 このアプリのコードは、[GitHub](https://github.com/Microsoft/spring-todo-app) で入手できます。 Spring と Cosmos DB を使用して Java アプリを記述することの詳細については、[Azure Cosmos DB SQL API を備える Spring Boot Starter のチュートリアル](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)と、[Spring Data Azure Cosmos DB のクイック スタート](https://github.com/Microsoft/spring-data-cosmosdb#quick-start)を参照してください。


お使いのターミナルで次のコマンドを実行して、サンプル リポジトリを複製し、サンプル アプリの環境をセットアップします。

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Azure Cosmos DB を作成する

以下の手順に従って、お使いのサブスクリプション内に Azure Cosmos DB データベースを作成します。 TODO リスト アプリは、このデータベースに接続し、実行時にデータを格納します。アプリケーションの状態は、アプリケーションを実行する場所に関わらず永続化されます。

1. Azure CLI にログインして、ログイン資格情報に接続されるサブスクリプションが複数ある場合は、必要に応じてサブスクリプションを設定します。

    ```azurecli
    az login
    az account set -s <your-subscription-id>
    ```   

2. Azure リソース グループを作成して、そのリソース グループの名前をメモします。

    ```azurecli
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. 種類が `GlobalDocumentDB` の Azure Cosmos DB を作成します。 Cosmos DB の名前には小文字のみを使用する必要があります。 コマンドからの応答内の `documentEndpoint` フィールドをメモします。

    ```azurecli
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. アプリに接続するための Azure Cosmos DB キーを取得します。 `primaryMasterKey` と `documentEndpoint` は、次の手順で必要になるため、近くに保管しておきます。

    ```azurecli
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>TODO アプリのプロパティを構成する

お使いのコンピューターでターミナルを開きます。 複製されたリポジトリ内にサンプル スクリプト ファイルをコピーします。これでそのファイルを、先ほど作成した Cosmos DB データベースのためにカスタマイズできます。

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
お好きなエディターで `.scripts/set-env-variables.sh` を編集して、Azure Cosmos DB の接続情報を指定します。 App Service Linux 構成の場合は、前と同じリージョン (`your-resource-group-region`) と、Cosmos DB データベースの作成時に使用したリソース グループ (`your-azure-group-name`) を使用します。 どの Azure デプロイ内の Web アプリ名も複製できないために一意である WEBAPP_NAME を選択します。

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

# App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

その後、スクリプトを実行します。

```bash
source .scripts/set-env-variables.sh
```
   
これらの環境変数は、TODO リスト アプリの `application.properties` で使用されます。 プロパティ ファイル内のフィールドによって、Spring Data 用の既定のリポジトリ構成が設定されます。

```properties
azure.cosmosdb.uri=${COSMOSDB_URI}
azure.cosmosdb.key=${COSMOSDB_KEY}
azure.cosmosdb.database=${COSMOSDB_DBNAME}
```

```java
@Repository
public interface TodoItemRepository extends DocumentDbRepository<TodoItem, String> {
}
```

次にサンプル アプリは、`com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` からインポートされた `@Document` 注釈を使用して、Cosmos DB によって格納され、管理されるエンティティ型を設定します。

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

Maven を使用してサンプルを実行します。

```bash
mvn package spring-boot:run
```

出力は次のようになります。

```output
bash-3.2$ mvn package spring-boot:run
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 


[INFO] SimpleUrlHandlerMapping - Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] SimpleUrlHandlerMapping - Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] WelcomePageHandlerMapping - Adding welcome page: class path resource [static/index.html]
2018-10-28 15:04:32.101  INFO 7673 --- [           main] c.m.azure.documentdb.DocumentClient      : Initializing DocumentClient with serviceEndpoint [https://sample-cosmos-db-westus.documents.azure.com:443/], ConnectionPolicy [ConnectionPolicy [requestTimeout=60, mediaRequestTimeout=300, connectionMode=Gateway, mediaReadMode=Buffered, maxPoolSize=800, idleConnectionTimeout=60, userAgentSuffix=;spring-data/2.0.6;098063be661ab767976bd5a2ec350e978faba99348207e8627375e8033277cb2, retryOptions=com.microsoft.azure.documentdb.RetryOptions@6b9fb84d, enableEndpointDiscovery=true, preferredLocations=null]], ConsistencyLevel [null]
[INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
[INFO] TomcatWebServer - Tomcat started on port(s): 8080 (http) with context path ''
[INFO] TodoApplication - Started TodoApplication in 45.573 seconds (JVM running for 76.534)
```

アプリが開始されたら、リンク `http://localhost:8080/` を使用して Spring TODO アプリにローカルでアクセスできます。

 ![Spring TODO アプリにローカルでアクセスする](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

TODO アプリケーションを開始したというメッセージではなく、例外が表示される場合は、前の手順の `bash` スクリプトによって環境変数が正しくエクスポートされたかを確認し、値が、作成した Azure Cosmos DB データベースに対して正しいことを確認してください。

## <a name="configure-azure-deployment"></a>Azure デプロイを構成する

`initial/spring-boot-todo` ディレクトリ内の `pom.xml` ファイルを開き、次に示す [Azure Web App Plugin for Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) の構成を追加します。

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.11.0</version>
        <configuration>
            <schemaVersion>v2</schemaVersion>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>

            <!-- Java Runtime Stack for Web App on Linux-->
            <runtime>
                 <os>linux</os>
                 <javaVersion>jre8</javaVersion>
                 <webContainer>jre8</webContainer>
             </runtime>
             <deployment>
                 <resources>
                 <resource>
                     <directory>${project.basedir}/target</directory>
                     <includes>
                     <include>*.jar</include>
                     </includes>
                 </resource>
                 </resources>
             </deployment>

            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property> 
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Dserver.port=80</value>
                </property>
            </appSettings>

        </configuration>
    </plugin>           
    ...
</plugins>
```

## <a name="deploy-to-app-service-on-linux"></a>App Service on Linux にデプロイする

`mvn azure-webapp:deploy` の Maven 目標を使用して、TODO アプリを Azure App Service on Linux にデプロイします。

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.11.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Auth Type : AZURE_CLI, Auth Files : [C:\Users\testuser\.azure\azureProfile.json, C:\Users\testuser\.azure\accessTokens.json]
[INFO] Subscription : xxxxxxxxx
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource to /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Renaming /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7/spring-todo-app-2.0-SNAPSHOT.jar to app.jar
[INFO] Deploying the zip package spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7718326714198381983.zip...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2019-11-06T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

出力には、デプロイされたアプリケーションへの URL が含まれています (この例では `https://spring-todo-app.azurewebsites.net`)。 この URL を Web ブラウザーにコピーするか、ターミナル ウィンドウで次のコマンドを実行すると、アプリを読み込むことができます。

```bash
curl https://spring-todo-app.azurewebsites.net
```

アドレス バーにリモート URL が表示されて、実行されているアプリが表示されるはずです。

 ![リモート URL で実行中の Spring Boot アプリケーション](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]


## <a name="scale-out-the-todo-app"></a>TODO アプリをスケールアウトする

別のワーカーを追加することで、アプリケーションをスケールアウトします。

```azurecli
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

これらのリソースが別のチュートリアルで不要である場合 (「[次のステップ](#next)」を参照)、Cloud Shell で次のコマンドを実行して削除することができます。 
  
```azurecli
az group delete --name <your-azure-group-name>
```

<a name="next"></a>

## <a name="next-steps"></a>次のステップ

[Java 開発者向けの Azure](/java/azure/)
[Spring Boot](https://spring.io/projects/spring-boot)、[Cosmos DB 用の Spring データ](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)、[Azure Cosmos DB](../cosmos-db/introduction.md)、および [App Service Linux](overview.md)。

App Service on Linux での Java アプリの実行の詳細について開発者ガイドで確認してください。

> [!div class="nextstepaction"] 
> [App Service Linux の Java 開発ガイド](configure-language-java.md?pivots=platform-linux)
