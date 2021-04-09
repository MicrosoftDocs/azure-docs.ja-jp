---
title: クイック スタート:Azure App Service で Java アプリを作成する
description: Azure App Service に、初めての Java の Hello World を数分でデプロイします。 Java アプリのデプロイには、Azure Web App Plugin for Maven が便利です。
keywords: Azure, App Service, Web アプリ, Windows, Linux, Java, Maven, クイックスタート
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-java-uiex
ms.openlocfilehash: ebd189e1cf6e053f5400b8217fc1c2fc385cdac9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701724"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>クイック スタート:Azure App Service で Java アプリを作成する

[Azure App Service](overview.md) は、非常にスケーラブルな、自己適用型の Web ホスティング サービスを提供します。  このクイックスタートでは、[Azure CLI](/cli/azure/get-started-with-azure-cli) を [Azure Web App Plugin for Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) と共に使用して .jar ファイルまたは .war ファイルをデプロイする方法を示します。 タブを使用して Java SE と Tomcat の手順を切り替えることができます。


> [!NOTE]
> IntelliJ や Eclipse のような一般的な IDE を使っても同じことができます。 [Azure Toolkit for IntelliJ のクイック スタート](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app)または [Azure Toolkit for Eclipse のクイック スタート](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)で類似するドキュメントを確認してください。


![Azure App Service で実行されているサンプル アプリ](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java アプリを作成する

# <a name="java-se"></a>[Java SE](#tab/javase)

[Spring Boot Getting Started](https://github.com/spring-guides/gs-spring-boot) サンプル プロジェクトをクローンします。

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

完成したプロジェクトにディレクトリを変更します。

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Cloud Shell プロンプトで次の Maven コマンドを実行して、`helloworld` という名前の新しいアプリを作成します。

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

次に、作業ディレクトリをプロジェクト フォルダーに変更します。

```bash
cd helloworld
```

---

## <a name="configure-the-maven-plugin"></a>Maven プラグインを構成する

Azure App Service へのデプロイ プロセスでは、Azure CLI から Azure 資格情報が自動的に使用されます。 Azure CLI がローカル環境にインストールされていない場合、ユーザーは Maven プラグインにより Oauth またはデバイス ログインを使用して認証されます。 詳細については、[Maven プラグインによる認証](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)に関するページを参照してください。

次の Maven コマンドを実行して、デプロイを構成します。 このコマンドは、App Service オペレーティング システム、Java バージョン、および Tomcat バージョンを設定するのに役立ちます。

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. **Subscription** オプションのプロンプトが表示されたら、行頭に番号出力を入力して適切な `Subscription` を選択します。
1. **Web App** オプションのプロンプトが表示されたら、Enter キーを押して既定のオプションである `<create>` を受け入れるか、既存のアプリを選択します。
1. **OS** オプションのプロンプトが表示されたら、「`3`」と入力して **Windows** を選択します。
1. **価格レベル** オプションのプロンプトが表示されたら、「`2`」と入力して **B2** を選択します。
1. Enter キーを押して、既定の Java バージョンである **Java 8** を使用します。
1. 最後のプロンプトで Enter キーを押して、選択内容を確認します。

    要約出力は、次に示すスニペットのようになります。

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. **Subscription** オプションのプロンプトが表示されたら、行頭に番号出力を入力して適切な `Subscription` を選択します。
1. **Web App** オプションのプロンプトが表示されたら、Enter キーを押して既定のオプションである `<create>` を受け入れるか、既存のアプリを選択します。
1. **OS** オプションのプロンプトが表示されたら、「`3`」と入力して **Windows** を選択します。
1. **価格レベル** オプションのプロンプトが表示されたら、「`2`」と入力して **B2** を選択します。
1. Enter キーを押して、既定の Java バージョンである **Java 8** を使用します。
1. Enter キーを押して、既定の Web コンテナーである **Tomcat 8.5** を使用します。
1. 最後のプロンプトで Enter キーを押して、選択内容を確認します。

    要約出力は、次に示すスニペットのようになります。

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. **Subscription** オプションのプロンプトが表示されたら、行頭に番号出力を入力して適切な `Subscription` を選択します。
1. **Web App** オプションのプロンプトが表示されたら、Enter キーを押して既定のオプションである `<create>` を受け入れるか、既存のアプリを選択します。
1. **OS** オプションのプロンプトが表示されたら、Enter キーを押して **Linux** を選択します。
1. **価格レベル** オプションのプロンプトが表示されたら、「`2`」と入力して **B2** を選択します。
1. Enter キーを押して、既定の Java バージョンである **Java 8** を使用します。
1. 最後のプロンプトで Enter キーを押して、選択内容を確認します。

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. **Subscription** オプションのプロンプトが表示されたら、行頭に番号出力を入力して適切な `Subscription` を選択します。
1. **Web App** オプションのプロンプトが表示されたら、Enter キーを押して既定のオプションである `<create>` を受け入れるか、既存のアプリを選択します。
1. **OS** オプションのプロンプトが表示されたら、Enter キーを押して **Linux** を選択します。
1. **価格レベル** オプションのプロンプトが表示されたら、「`2`」と入力して **B2** を選択します。
1. Enter キーを押して、既定の Java バージョンである **Java 8** を使用します。
1. Enter キーを押して、既定の Web コンテナーである **Tomcat 8.5** を使用します。
1. 最後のプロンプトで Enter キーを押して、選択内容を確認します。

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

必要に応じて、`pom.xml` 内で App Service の構成を直接変更できます。 いくつかの一般的なものを次に示します。

プロパティ | 必須 | 説明 | Version
---|---|---|---
`<schemaVersion>` | false | 構成スキーマのバージョンを指定します。 サポートされる値は `v1`、`v2` です。 | 1.5.2
`<subscriptionId>` | false | サブスクリプション ID を指定します。 | 0.1.0 以降
`<resourceGroup>` | true | Web アプリの Azure リソース グループ。 | 0.1.0 以降
`<appName>` | true | Web アプリの名前。 | 0.1.0 以降
`<region>` | true | Web アプリがホストされるリージョンを指定します。既定値は **westeurope** です。 すべての有効なリージョンについては、「[サポートされているリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=app-service)」を参照してください。 | 0.1.0 以降
`<pricingTier>` | false | Web アプリの価格レベル。 運用ワークロードの場合の既定値は **P1V2** ですが、Java Dev/Test の場合は **B2** が推奨される最小構成です。 [詳細情報](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0 以降
`<runtime>` | true | ランタイム環境の構成の詳細については、[こちら](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)を参照してください。 | 0.1.0 以降
`<deployment>` | true | デプロイ構成の詳細については、[こちら](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)を参照してください。 | 0.1.0 以降

`<appName>` と `<resourceGroup>` の値 (デモでは `helloworld-1590394316693` と `helloworld-1590394316693-rg`) に注意してください。これらは後で使用します。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>アプリケーションのデプロイ

Maven プラグインでは、Azure CLI からのアカウント資格情報を App Services へのデプロイに使用します。 続行する前に、[Azure CLI にサインイン](/cli/azure/authenticate-azure-cli)します。

```azurecli
az login
```

次のコマンドを使用して、対象の Java アプリを Azure にデプロイできます。

```bash
mvn package azure-webapp:deploy
```

デプロイが完了すると、アプリケーションは `http://<appName>.azurewebsites.net/` で準備が整います (デモでは `http://helloworld-1590394316693.azurewebsites.net`)。 ローカル Web ブラウザーで URL を開くと、次のように表示されます

![Azure App Service で実行されているサンプル アプリ](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**お疲れさまでした。** App Service に初めての Java アプリをデプロイできました。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

前の手順では、リソース グループ内に Azure リソースを作成しました。 今後これらのリソースが必要になることが予想されない場合は、ポータルからリソース グループを削除するか、Cloud Shell で次のコマンドを実行します。

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

このコマンドの実行には、少し時間がかかる場合があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Java を使用して Azure DB for PostgreSQL に接続する](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [CI/CD を設定します](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [料金情報](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [ログとメトリックの集計](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [スケールアップ](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Java 開発者向けの Azure リソース](/java/azure/)

> [!div class="nextstepaction"]
> [Java アプリの構成](configure-language-java.md)
