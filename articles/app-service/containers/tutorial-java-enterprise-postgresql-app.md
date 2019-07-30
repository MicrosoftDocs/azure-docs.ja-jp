---
title: Linux 上で Java Enterprise Web アプリを構築する - Azure App Service | Microsoft Docs
description: Azure App Service on Linux 上の Wildfly で動作する Java Enterprise アプリを取得する方法について説明します。
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: dcd1ef5c54885b758ac9a301616d79a163999bc9
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509639"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>チュートリアル:Azure で Java EE と Postgres の Web アプリを構築する

このチュートリアルでは、Azure App Service に Java Enterprise Edition (EE) Web アプリを作成し、それを Postgres データベースに接続する方法について説明します。 完了すると、Azure [App Service on Linux](app-service-linux-intro.md) で稼働中の [Azure Database for Postgres](https://azure.microsoft.com/services/postgresql/) にデータを格納する [WildFly](https://www.wildfly.org/about/) アプリケーションが完成します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Maven を使用して Java EE アプリを Azure にデプロイする
> * Azure で Postgres データベースを作成する
> * Postgres を使用するために WildFly サーバーを構成する
> * アプリを更新して再デプロイする
> * WildFly 上で単体テストを実行する

## <a name="prerequisites"></a>前提条件

1. [Git をダウンロードし、インストールします](https://git-scm.com/)
2. [Maven 3 をダウンロードし、インストールします](https://maven.apache.org/install.html)
3. [Azure CLI をダウンロードし、インストールします](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>サンプル アプリを複製して編集する

この手順では、サンプル アプリケーションを複製し、デプロイ用の Maven プロジェクト オブジェクト モデル (POM または *pom.xml)* を構成します。

### <a name="clone-the-sample"></a>サンプルを複製する

ターミナル ウィンドウで作業ディレクトリに移動し、[サンプル リポジトリ](https://github.com/Azure-Samples/wildfly-petstore-quickstart)を複製します。

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Maven POM を更新する

希望する名前と App Service のリソース グループを指定して、Maven Azure プラグインを更新します。 App Service プランまたはインスタンスを事前に作成する必要はありません。 リソース グループとアプリ サービスがまだ存在しない場合は、Maven プラグインによって作成されます。

*pom.xml* の `<plugins>` セクション (200 行目) まで下へスクロールして、変更を行います。

```xml
<!-- Azure App Service Maven plugin for deployment -->
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>${version.maven.azure.plugin}</version>
  <configuration>
    <appName>YOUR_APP_NAME</appName>
    <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup>
    <linuxRuntime>wildfly 14-jre8</linuxRuntime>
  ...
</plugin>  
```

`YOUR_APP_NAME` と `YOUR_RESOURCE_GROUP` を、App Service とリソース グループの名前に置き換えます。

## <a name="build-and-deploy-the-application"></a>アプリケーションをビルドしてデプロイする

次に、Maven を使用して目的のアプリケーションをビルドし、それを App Service にデプロイします。

### <a name="build-the-war-file"></a>.war ファイルをビルドする

このプロジェクトの POM は、Web アーカイブ (WAR) ファイルにアプリケーションをパッケージ化するように構成されています。 Maven を使用してアプリケーションをビルドします。

```bash
mvn clean install -DskipTests
```

このアプリケーションのテスト ケースは、アプリケーションが WildFly 上にデプロイされるときに実行されるように設計されています。 ローカルでビルドするテストはスキップします。お客様はアプリケーションが App Service にデプロイされたらテストを実行します。

### <a name="deploy-to-app-service"></a>App Service にデプロイする

WAR の準備ができたので、Azure プラグインを使用して App Service にデプロイできます。

```bash
mvn azure-webapp:deploy
```

デプロイが完了したら、次の手順に進みます。

### <a name="create-a-record"></a>レコードを作成する

ブラウザーを開き、 `https://<your_app_name>.azurewebsites.net/` に移動します。 おめでとうございます。Java EE アプリケーションを Azure App Service にデプロイできました。

この時点で、アプリケーションでは H2 メモリ内データベースが使用されています。 ナビゲーション バーの [管理者] をクリックし、新しいカテゴリを作成します。 お客様のメモリ内データベースのレコードは、お客様の App Service インスタンスを再起動すると失われます。 以下の手順では、Azure に Postgres データベースをプロビジョニングし、それが使用されるように WildFly を構成することで、この問題を解決します。

![[管理者] ボタンの場所](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Postgres データベースをプロビジョニングする

Postgres データベース サーバーをプロビジョニングするには、ターミナルを開き、次の例に示すように [az postgres server create](https://docs.microsoft.com/cli/azure/postgres/server) コマンドを使用します。 お使いの App Service インスタンスに対して前に指定したのと同じリソース グループを使用して、プレースホルダー (山かっこも含みます) と選択した値を配置します。 指定した管理者の資格情報によって今後のアクセスが有効になるため、後で使用するために必ずメモしておいてください。

```bash
az postgres server create \
    --name <server name> \
    --resource-group <resource group> \
    --location <location>
    --sku-name GP_Gen5_2 \
    --admin-user <administrator username> \
    --admin-password <administrator password> \
```

このコマンドを実行した後、Azure portal を参照し、お使いの Postgres データベースに移動します。 ブレードが表示されたら、[サーバー名] と [サーバー管理者ログイン名] の値をコピーします。これらは、後で必要になります。

### <a name="allow-access-to-azure-services"></a>Azure サービスへのアクセスを許可する

[Azure データベース] ブレードの **[接続のセキュリティ]** パネルで、[Azure サービスへのアクセスを許可] ボタンを **[オン]** の位置に切り替えます。

![Azure サービスへのアクセスの許可](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Postgres 用に Java アプリを更新する

次に、目的の Postgres データベースを使用できるように、Java アプリケーションに対していくつかの変更を加えます。

### <a name="add-postgres-credentials-to-the-pom"></a>POM に Postgres 資格情報を追加する

*pom.xml* で、大文字のプレースホルダーの値を、Postgres サーバー名、管理者ログイン名、およびパスワードに置き換えます。 これらのフィールドは、Azure Maven プラグイン内にあります (`<name>` タグ内ではなく、`<value>` タグ内の `YOUR_SERVER_NAME`、`YOUR_PG_USERNAME`、および `YOUR_PG_PASSWORD` を置き換えてください)。

```xml
<plugin>
      ...
      <appSettings>
      <property>
        <name>POSTGRES_CONNECTIONURL</name>
        <value>jdbc:postgresql://YOUR_SERVER_NAME:5432/postgres?ssl=true</value>
      </property>
      <property>
        <name>POSTGRES_USERNAME</name>
        <value>YOUR_PG_USERNAME</value>
      </property>
      <property>
        <name>POSTGRES_PASSWORD</name>
        <value>YOUR_PG_PASSWORD</value>
      </property>
    </appSettings>
  </configuration>
</plugin>
```

### <a name="update-the-java-transaction-api"></a>Java トランザクション API を更新する

次に、目的の Java トランザクション API (JTA) 構成を編集する必要があります。これにより、目的の Java アプリケーションが、以前に使用していた H2 メモリ内データベースではなく、Postgres と通信するようになります。 エディターで *src/main/resources/META-INF/persistence.xml* を開きます。 `<jta-data-source>` の値を `java:jboss/datasources/postgresDS` に置き換えます。 これで、お客様の JTA XML は次の設定になっているはずです。

```xml
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
```

## <a name="configure-the-wildfly-application-server"></a>WildFly アプリケーション サーバーを構成する

再構成した目的のアプリケーションをデプロイする前に、Postgres モジュールとその依存関係を設定して WildFly アプリケーション サーバーを更新する必要があります。 他の構成情報については、「[Configure WildFly server (WildFly サーバーの構成)](configure-language-java.md#configure-java-ee-wildfly)」を参照してください。

サーバーを構成するには、*wildfly_config/* ディレクトリに 4 つのファイルが必要です。

- **postgresql-42.2.5.jar**:この JAR ファイルは、Postgres 用の JDBC ドライバーです。 詳細については、[公式 Web サイト](https://jdbc.postgresql.org/index.html)を参照してください。
- **postgres-module.xml**:この XML ファイルでは、Postgres モジュール (org.postgres) の名前が宣言されます。 また、モジュールを使用するために必要なリソースと依存関係が指定されます。
- **jboss_cli_commands.cl**:このファイルには、JBoss CLI によって実行される構成コマンドが含まれています。 これらのコマンドでは、WildFly アプリケーション サーバーへの Postgres モジュールの追加、資格情報の指定、JNDI 名の宣言、タイムアウトのしきい値の設定などを行います。JBoss CLI を使い慣れていない場合は、[公式ドキュメント](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)を参照してください。
- **startup_script.sh**:最後のこのシェル スクリプトは、お客様の App Service インスタンスが起動されるたびに実行されます。 このスクリプトで実行される機能は、*jboss_cli_commands.cli* 内のコマンドを JBoss CLI にパイプ処理することの 1 つだけです。

これらのファイル、特に *jboss_cli_commands.cli* の内容を読むことを強くお勧めします。

### <a name="ftp-the-configuration-files"></a>構成ファイルを FTP で転送する

*wildfly_config/* の内容を目的の App Service インスタンスに FTP で転送する必要があります。 お客様の FTP 資格情報を取得するには、Azure portal で [App Service] ブレードの **[発行プロファイルの取得]** ボタンをクリックします。 お客様の FTP ユーザー名とパスワードは、ダウンロードした XML ドキュメント内にあります。 発行プロファイルの詳細については、[このドキュメント](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)を参照してください。

任意の FTP ツールを使用して、*wildfly_config/* 内の 4 つのファイルを */home/site/deployments/tools/* に転送します (ディレクトリではなく、ファイル自体だけを転送することに注意してください)。

### <a name="finalize-app-service"></a>App Service を最終処理する

[App Service] ブレードで [アプリケーションの設定] パネルに移動します。 [ランタイム] の下の [スタートアップ ファイル] フィールドを */home/site/deployments/tools/startup_script.sh* に設定します。こうすることで、App Service インスタンスが作成された後、WildFly サーバーが起動される前に、シェル スクリプトが確実に実行されるようになります。

最後に、お客様のアプリ サービスを再起動します。 ボタンは [概要] パネル内にあります。

## <a name="redeploy-the-application"></a>アプリケーションを再デプロイする

ターミナル ウィンドウでお客様のアプリケーションをリビルドし、再デプロイします。

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

お疲れさまでした。 お客様のアプリケーションで Postgres データベースが使用されるようになり、アプリケーションで作成されたすべてのレコードが、以前の H3 メモリ内データベースではなく、Postgres に格納されるようになりました。 これを確認するために、レコードを作成してお客様のアプリ サービスを再起動できます。 お客様のアプリケーションを再起動しても、レコードはなくなりません。

## <a name="clean-up"></a>クリーンアップ

これらのリソースが別のチュートリアルで不要である場合 (「次のステップ」を参照)、次のコマンドを実行して削除することができます。

```bash
az group delete --name <your-resource-group>
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Maven を使用して Java EE アプリを Azure にデプロイする
> * Azure で Postgres データベースを作成する
> * Postgres を使用するために WildFly サーバーを構成する
> * アプリを更新して再デプロイする
> * WildFly 上で単体テストを実行する

次のチュートリアルに進んで、カスタム DNS 名をアプリにマップする方法を確認してください。

> [!div class="nextstepaction"]
> [チュートリアル:カスタム DNS 名をアプリにマップする](../app-service-web-tutorial-custom-domain.md)

または、他のリソースを参照してください。

> [!div class="nextstepaction"]
> [Java アプリを構成する](configure-language-java.md)
