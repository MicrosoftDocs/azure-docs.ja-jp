---
title: Windows で Java Web アプリを作成する - Azure App Service
description: このクイック スタートでは、Azure App Service on Windows で、初めての Java の Hello World を数分でデプロイします。
keywords: Azure, App Service, Web アプリ, Windows, Java, Maven, クイック スタート
services: app-service\web
documentationcenter: ''
author: msangapu-msft
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc
ms.openlocfilehash: 1e25f65cf7ec52b4a58386843a1d2f5796c66e65
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297357"
---
# <a name="quickstart-create-a-java-app-in-app-service"></a>クイック スタート:App Service で Java アプリを作成する

> [!NOTE]
> この記事では、Windows 上の App Service にアプリをデプロイします。 _Linux_ 上の App Service にデプロイするには、[Linux での Java Web アプリの作成](./containers/quickstart-java.md)に関するページを参照してください。
>

[Azure App Service](overview.md) では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。  このクイック スタートでは、[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) を [Azure App Service 用の Maven プラグイン](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)と共に使用して Java Web アーカイブ (WAR) ファイルをデプロイする方法を示します。

> [!NOTE]
> IntelliJ や Eclipse のような一般的な IDE を使っても同じことができます。 [Azure Toolkit for IntelliJ のクイック スタート](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app)または [Azure Toolkit for Eclipse のクイック スタート](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app)で類似するドキュメントを確認してください。
>
![Azure で実行されるサンプル アプリ](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>${SUBSCRIPTION_ID}</subscriptionId>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> この記事では、WAR ファイル内にパッケージ化された Java アプリのみを操作します。 このプラグインは、JAR Web アプリケーションもサポートしています。[Java SE JAR ファイルを App Service on Linux にデプロイする方法](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)に関するページにアクセスしてお試しください。


プラグイン構成で、次のプレースホルダーを更新します。

| プレースホルダー | 説明 |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | アプリをデプロイするサブスクリプションの一意の ID。 既定のサブスクリプションの ID は、Cloud Shell または CLI から `az account show` コマンドを使用して調べることができます。 使用可能なすべてのサブスクリプションを調べるには、`az account list` コマンドを使用します。|
| `RESOURCEGROUP_NAME` | その中にアプリを作成する新しいリソース グループの名前。 アプリのすべてのリソースを 1 つのグループ内に配置することで、それらを一緒に管理できます。 たとえば、リソース グループを削除すれば、そのアプリに関連付けられているすべてのリソースが削除されます。 この値を一意の新しいリソース グループ名 (たとえば、*myResourceGroup*) で更新します。 このリソース グループ名を使用して、後のセクションですべての Azure リソースをクリーンアップします。 |
| `WEBAPP_NAME` | Azure にデプロイされると、このアプリ名はアプリのホスト名の一部になります (WEBAPP_NAME.azurewebsites.net)。 この値を、Java アプリをホストする新しい App Service アプリの一意の名前 (たとえば、*contoso*) で更新します。 |
| `REGION` | アプリがホストされている Azure リージョン (たとえば、*westus2*)。 リージョンの一覧は、`az account list-locations` コマンドを使用して Cloud Shell または CLI から取得できます。 |

## <a name="deploy-the-app"></a>アプリケーションのデプロイ

次のコマンドを使用して、Java アプリを Azure にデプロイします。

```bash
mvn package azure-webapp:deploy
```

デプロイが完了したら、Web ブラウザーで次の URL を使用して、デプロイされたアプリケーションを参照します (たとえば、`http://<webapp>.azurewebsites.net/`)。

![Azure で実行されるサンプル アプリ](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

**お疲れさまでした。** App Service on Windows に初めての Java アプリをデプロイしました。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Java 開発者向けの Azure リソース](/java/azure/)

> [!div class="nextstepaction"]
> [カスタム ドメインをマップする](app-service-web-tutorial-custom-domain.md)
