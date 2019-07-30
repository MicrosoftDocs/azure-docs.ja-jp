---
title: Linux での Java Web アプリの作成 - Azure App Service
description: このクイック スタートでは、Azure App Service on Linux で、初めての Java の Hello World を数分でデプロイします。
keywords: Azure, App Service, Web アプリ, Linux, Java, Maven, クイック スタート
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 30689e05a2567646ff541818dc68a90c13da7a56
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297255"
---
# <a name="quickstart-create-a-java-app-in-app-service-on-linux"></a>クイック スタート:App Service on Linux で Java アプリを作成する

[App Service on Linux](app-service-linux-intro.md) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このクイック スタートでは、[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) を [Azure App Service 用の Maven プラグイン](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)と共に使用して Java Web アーカイブ (WAR) ファイルをデプロイする方法を示します。
> [!NOTE]
>
> IntelliJ や Eclipse のような一般的な IDE を使っても同じことができます。 [Azure Toolkit for IntelliJ のクイック スタート](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app)または [Azure Toolkit for Eclipse のクイック スタート](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app)で類似するドキュメントを確認してください。
>
![Azure で実行されるサンプル アプリ](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java アプリを作成する

Cloud Shell プロンプトで次の Maven コマンドを実行して、`helloworld` という名前の新しいアプリを作成します。

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Maven プラグインを構成する

Maven からデプロイするには、Cloud Shell でコード エディターを使用して `helloworld` ディレクトリ内のプロジェクト `pom.xml` ファイルを開きます。 

```bash
code pom.xml
```

次に、`pom.xml` ファイルの `<build>` 要素内に次のプラグイン定義を追加します。

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Linux           -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
    </plugin>
</plugins>
```

Azure App Service へのデプロイ プロセスでは、Azure CLI からアカウントの資格情報を使います。 続行する前に、[Azure CLI にサインイン](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)します。

```azurecli
az login
```

次に、デプロイを構成し、[コマンド プロンプト] で maven コマンド `mvn azure-webapp:config` を実行し、 **[Confirm (Y/N)]\(確認 (Y/N)\)** プロンプトが表示されるまで **Enter** キーを押して既定の構成を使用し、 **'y'** キーを押して構成を完了します。

```cmd
~@Azure:~/helloworld$ mvn azure-webapp:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.7.0:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. jre8 [*]
2. java11
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. jre8
3. TOMCAT 8.5 [*]
4. WILDFLY 14
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```

> [!NOTE]
> この記事では、WAR ファイル内にパッケージ化された Java アプリのみを操作します。 このプラグインは、JAR Web アプリケーションもサポートしています。[Java SE JAR ファイルを App Service on Linux にデプロイする方法](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)に関するページにアクセスしてお試しください。

もう一度 `pom.xml` に移動してプラグイン構成が更新されていることを確認します。必要に応じて、pom ファイルで App Service の他の構成を直接変更できます。その一般的なものを次に示します。

 プロパティ | 必須 | 説明 | Version
---|---|---|---
`<schemaVersion>` | false | 構成スキーマのバージョンを指定します。 サポートされる値は `v1`、`v2` です。 | 1.5.2
`<resourceGroup>` | true | Web アプリの Azure リソース グループ。 | 0.1.0 以降
`<appName>` | true | Web アプリの名前。 | 0.1.0 以降
[`<region>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) | true | Web アプリがホストされるリージョンを指定します。既定値は **westus** です。 すべての有効なリージョンについては、「[サポートされているリージョン](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region)」を参照してください。 | 0.1.0 以降
[`<pricingTier>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme##pricingtier) | false | Web アプリの価格レベル。 既定値は **P1V2** です。| 0.1.0 以降
[`<runtime>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting) | true | ランタイム環境の構成の詳細については、[こちら](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting)を参照してください。 | 0.1.0 以降
[`<deployment>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting) | true | デプロイ構成の詳細については、[こちら](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting)を参照してください。 | 0.1.0 以降

## <a name="deploy-the-app"></a>アプリケーションのデプロイ

次のコマンドを使用して、Java アプリを Azure にデプロイします。

```bash
mvn package azure-webapp:deploy
```

デプロイが完了したら、Web ブラウザーで次の URL を使用して、デプロイされたアプリケーションを参照します (たとえば、`http://<webapp>.azurewebsites.net`)。 

![Azure で実行されるサンプル アプリ](media/quickstart-java/java-hello-world-in-browser.png)

**お疲れさまでした。** App Service on Linux に初めての Java アプリをデプロイしました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

前の手順では、リソース グループ内に Azure リソースを作成しました。 これらのリソースが将来必要になると想定していない場合、Cloud Shell で次のコマンドを実行して、リソース グループを削除します。

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

このコマンドの実行には、少し時間がかかる場合があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル:Java Enterprise アプリと PostgreSQL](tutorial-java-enterprise-postgresql-app.md)

> [!div class="nextstepaction"]
> [Java アプリを構成する](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Jenkins での CI/CD](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [その他の Java 開発者向けの Azure リソース](/java/azure/)
