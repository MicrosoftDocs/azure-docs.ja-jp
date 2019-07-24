---
title: Linux Java アプリを構成する - Azure App Service | Microsoft Docs
description: Azure App Service on Linux で実行される Java アプリを構成するについて説明します。
keywords: Azure App Service, Web アプリ, Linux, OSS, Java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/28/2019
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: b659c076974b0659c645c9b6460e458dfac8974a
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551049"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Azure App Service 向けの Linux Java アプリを構成する

Azure App Service on Linux を使用すると、Java 開発者は、完全に管理された Linux ベースのサービス上で Tomcat または Java Standard Edition (SE) パッケージ化 Web アプリケーションを迅速にビルド、デプロイ、およびスケーリングすることができます。 アプリケーションは、コマンドラインから Maven プラグインを使用して、または IntelliJ、Eclipse、Visual Studio Code などのエディターでデプロイします。

このガイドでは、App Service のビルトイン Linux コンテナーを使用する Java 開発者のために、主要な概念と手順を示します。 Azure App Service を使用したことがない場合は、まず、[Java クイック スタート](quickstart-java.md)と [PostgreSQL を使った Java のチュートリアル](tutorial-java-enterprise-postgresql-app.md)に従ってください。

## <a name="logging-and-debugging-apps"></a>アプリのログ記録とデバッグ

パフォーマンス レポート、トラフィックの視覚エフェクト、および正常性検査は、Azure portal を介して各アプリに対して使用できます。 詳細については、「[Azure App Service 診断の概要](../overview-diagnostics.md)」を参照してください。

### <a name="ssh-console-access"></a>SSH コンソール アクセス

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

詳細については、[Azure CLI を使用したログのストリーミング](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli)に関する記事を参照してください。

### <a name="app-logging"></a>アプリのログ記録

Azure portal または [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) を使用して[アプリケーションのログ記録](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag)を有効にし、アプリケーションの標準コンソール出力および標準コンソール エラー ストリームをローカル ファイル システムまたは Azure BLOB ストレージに書き込むよう App Service を構成します。 App Service のローカル ファイル システム インスタンスへのログ記録は、構成されてから 12 時間後に無効になります。 リテンション期間を長くする必要がある場合は、BLOB ストレージ コンテナーに出力を書き込むようアプリケーションを構成します。

アプリケーションで [Logback](https://logback.qos.ch/) または [Log4j](https://logging.apache.org/log4j) をトレースに使用している場合は、「[Application Insights を使用した Java トレース ログの探索](/azure/application-insights/app-insights-java-trace-logs)」にあるログ記録フレームワークの構成手順に従って、これらのトレースを確認のために Azure Application Insights に転送することができます。

## <a name="customization-and-tuning"></a>カスタマイズとチューニング

Azure App Service for Linux では、Azure portal および CLI を使用したチューニングとカスタマイズが追加設定なしでサポートされています。 Java 以外の特定の Web アプリの構成については、次の記事を確認してください。

- [App Service の設定の構成](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [カスタム ドメインの設定](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [SSL の有効化](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [CDN の追加](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Java ランタイム オプションを設定する

Tomcat と Java SE の両方の環境で、割り当てられたメモリまたはその他の JVM ランタイムのオプションを設定するには、次に示すように[アプリケーション設定](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)で JAVA_OPTS を設定します。 App Service Linux では、開始時にこの設定が環境変数として Java ランタイムに渡されます。

Azure portal の Web アプリ用の **[アプリケーション設定]** で、`$JAVA_OPTS -Xms512m -Xmx1204m` などの追加の設定を含む、`JAVA_OPTS` という名前の新しいアプリ設定を作成します。

Azure App Service Linux Maven プラグインからアプリ設定を構成するには、Azure プラグイン セクションで設定/値のタグを追加します。 次の例では、特定の最小および最大の Java ヒープ サイズを設定します。

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>$JAVA_OPTS -Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

App Service プランで 1 つのデプロイ スロットを使用して 1 つのアプリケーションを実行している開発者は、次のオプションを使用できます。

- B1 と S1 のインスタンス: -Xms1024m -Xmx1024m
- B2 と S2 のインスタンス: -Xms3072m -Xmx3072m
- B3 と S3 のインスタンス: -Xms6144m -Xmx6144m


アプリケーション ヒープ設定をチューニングする際には、App Service プランの詳細を確認し、複数のアプリケーションおよびデプロイ スロットのニーズを考慮して、メモリの最適な割り当てを特定する必要があります。

### <a name="turn-on-web-sockets"></a>Web ソケットを有効にする

Azure portal のアプリケーション用の **[アプリケーション設定]** で、Web ソケットのサポートを有効にします。 設定を有効にするには、アプリケーションを再起動する必要があります。

Azure CLI を使用して、次のコマンドで Web ソケットのサポートを有効にします。

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

その後、アプリケーションを再起動します。

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>既定の文字エンコーディングを設定する

Azure portal の Web アプリ用の **[アプリケーション設定]** で、値 `$JAVA_OPTS -Dfile.encoding=UTF-8` を含む、`JAVA_OPTS` という名前の新しいアプリ設定を作成します。

または、App Service Maven プラグインを使用してアプリ設定を構成できます。 プラグイン構成で、設定名および値のタグを追加します。

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>$JAVA_OPTS -Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

## <a name="secure-applications"></a>セキュリティで保護されたアプリケーション

App Service for Linux で実行される Java アプリケーションは、他のアプリケーションと同じ一連の[セキュリティのベスト プラクティス](/azure/security/security-paas-applications-using-app-services)を備えています。 

### <a name="authenticate-users"></a>ユーザーを認証する

**[認証/承認]** オプションを使用して、Azure portal でアプリ認証を設定します。 そこから、Azure Active Directory、または Facebook、Google、GitHub などのソーシャル ログインを使用して、認証を有効にすることができます。 Azure portal の構成は、1 つの認証プロバイダーを構成するときにのみ機能します。 詳細については、「[Azure Active Directory ログインを使用するよう App Service アプリを構成する](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)」と、他の ID プロバイダーの関連記事を参照してください。

複数のサインイン プロバイダーを有効にする必要がある場合は、[App Service 認証のカスタマイズ](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)に関する記事の手順に従います。

 Spring Boot 開発者は、[Azure Active Directory Spring Boot スターター](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)を使用して、使い慣れた Spring Security の注釈と API を使用してアプリケーションをセキュリティで保護することができます。

### <a name="configure-tlsssl"></a>TLS/SSL を構成する

[既存のカスタム SSL 証明書のバインド](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)に関する記事の手順に従って、既存の SSL 証明書をアップロードし、アプリケーションのドメイン名にバインドします。 既定では、アプリケーションで引き続き HTTP 接続が許可されます。チュートリアルの特定の手順に従って、SSL と TLS を適用します。

## <a name="configure-apm-platforms"></a>APM プラットフォームを構成する

このセクションでは、NewRelic と AppDynamics アプリケーション パフォーマンスの監視 (APM) プラットフォームで、Linux 上の Azure App Service にデプロイされた Java アプリケーションを接続する方法を示します。

[New Relic を構成する](#configure-new-relic)
[AppDynamics を構成する](#configure-appdynamics)

### <a name="configure-new-relic"></a>New Relic の構成

1. [NewRelic.com](https://newrelic.com/signup) で NewRelic アカウントを作成します
2. NewRelic より Java エージェントをダウンロードします。ファイル名はおよそ `newrelic-java-x.x.x.zip` のようになります。
3. ライセンス キーをコピーします。これは後ほどエージェントの構成を行う際に必要です。
4. [App Service インスタンスに SSH で接続し](app-service-linux-ssh-support.md)、新しいディレクトリ `/home/site/wwwroot/apm` を作成します。
5. `/home/site/wwwroot/apm` の下にあるディレクトリにアンパックされた NewRelic Java エージェント ファイルをアップロードします。 エージェント用のファイルは `/home/site/wwwroot/apm/newrelic` 内にある必要があります。
6. `/home/site/wwwroot/apm/newrelic/newrelic.yml` で YAML ファイルを変更し、プレース ホルダー ライセンスの値を独自のライセンス キーに置き換えます。
7. Azure portal で App Service でアプリケーションを参照し、新しいアプリケーション設定を作成します。
    - もしアプリが **Java SE** を使用している場合、`JAVA_OPTS` の名前および `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` の値を持つ環境変数を作成します。
    - **Tomcat** を使用している場合、`CATALINA_OPTS`の名前および `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` の値を持つ環境変数を作成します。
    - **WildFly** を使用している場合は、Java エージェントのインストールおよび JBoss 構成に関するガイダンスについて、[こちら](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java)の New Relic のドキュメントを参照してください。
    - `JAVA_OPTS` または `CATALINA_OPTS` 用の環境変数がすでにある場合、`javaagent` のオプションを現在値の最期に追加します。

### <a name="configure-appdynamics"></a>AppDynamics の構成

1. [AppDynamics.com](https://www.appdynamics.com/community/register/) で AppDynamics アカウントを作成します
1. AppDynamics の web サイトより Java エージェントをダウンロードします。ファイル名はおよそ `AppServerAgent-x.x.x.xxxxx.zip` のようになります
1. [App Service インスタンスに SSH で接続し](app-service-linux-ssh-support.md)、新しいディレクトリ `/home/site/wwwroot/apm` を作成します。
1. `/home/site/wwwroot/apm`の下にあるディレクトリに Java エージェント ファイルをアップロードします。 エージェント用のファイルは `/home/site/wwwroot/apm/appdynamics` 内にある必要があります。
1. Azure portal で App Service でアプリケーションを参照し、新しいアプリケーション設定を作成します。
    - **Java SE** を使用している場合、`JAVA_OPTS` の名前および `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` の値を持ち、`<app-name>` の App Service 名を持つ環境変数を作成します。
    - **Tomcat** を使用している場合、`CATALINA_OPTS` の名前および `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` の値を持ち、`<app-name>` の App Service 名を持つ環境変数を作成します。
    - **WildFly** を使用している場合は、Java エージェントのインストールおよび JBoss 構成に関するガイダンスについて、[こちら](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings)の AppDynamics のドキュメントを参照してください。

## <a name="configure-tomcat"></a>Tomcat を構成する

### <a name="connect-to-data-sources"></a>データ ソースへの接続

>[!NOTE]
> アプリケーションで Spring Boot または Spring Framework を使用している場合は、Spring データ JPA のデータベース接続情報を環境変数として設定できます [アプリケーションのプロパティ ファイル内]。 その後、Azure portal または CLI で、[アプリ設定](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)を使用してアプリケーションに対してこれらの値を定義することができます。

これらの説明は、すべてのデータベース接続に適用されます。 プレースホルダーを、選択したデータベースのドライバーのクラス名と JAR ファイルに置き換える必要があります。 一般的なデータベースのクラス名とドライバーのダウンロードを含む表を次に示します。

| Database   | ドライバーのクラス名                             | JDBC ドライバー                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [ダウンロード](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [ダウンロード](https://dev.mysql.com/downloads/connector/j/) ("プラットフォームに依存しない" を選択) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [ダウンロード](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Java Database Connectivity (JDBC) または Java Persistence API (JPA) を使用するように Tomcat を構成するには、まず、起動時に Tomcat によって読み込まれる `CATALINA_OPTS` 環境変数をカスタマイズします。 [App Service Maven プラグイン](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)のアプリ設定を使用して、次の値を設定します。

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

または、Azure Portal の [アプリケーションの設定] ブレードで環境変数を設定します。

次に、データ ソースを Tomcat サーブレットで実行されている 1 つのアプリケーション、またはすべてのアプリケーションのどちらに対して使用可能にする必要があるかを判定します。

#### <a name="application-level-data-sources"></a>アプリケーション レベル データ ソース

1. プロジェクトの `META-INF/` ディレクトリ内に `context.xml` ファイルを作成します。 `META-INF/` ディレクトリが存在しない場合は作成します。

2. `context.xml` で、`Context` 要素を追加してデータ ソースを JNDI アドレスにリンクします。 `driverClassName` プレースホルダーを、上の表にあるドライバーのクラス名に置き換えます。

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ```

3. アプリケーションでこのデータ ソースを使用するよう、アプリケーションの `web.xml` を更新します。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>共有のサーバーレベル リソース

1. 構成がまだ存在しない場合は、SSH を使用して、App Service Linux インスタンス上で `/usr/local/tomcat/conf` の内容を `/home/tomcat/conf` にコピーします。
    ```
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. `server.xml` の `<Server>` 要素内に Context 要素を追加します。

    ```xml
    <Server>
    ...
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ...
    </Server>
    ```

3. アプリケーションでこのデータ ソースを使用するよう、アプリケーションの `web.xml` を更新します。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>構成を完了する

最後に、ドライバーの JAR を Tomcat クラスパス内に配置し、App Service を再起動します。

1. JDBC ドライバー ファイルが Tomcat クラスローダーで確実に使用できるように、これらのファイルを `/home/tomcat/lib` ディレクトリに配置します。 (このディレクトリがまだ存在しない場合は作成します。)これらのファイルを App Service インスタンスにアップロードするには、次の手順を行います。
    1. [Cloud Shell](https://shell.azure.com) で、Web アプリ拡張機能をインストールします。

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. 次の CLI コマンドを実行して、ローカル システムから App Service への SSH トンネルを作成します。

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. SFTP クライアントを使用してローカルのトンネル ポートに接続し、ファイルを `/home/tomcat/lib` フォルダーにアップロードします。

    あるいは、FTP クライアントを使用して JDBC ドライバーをアップロードできます。 [FTP 資格情報を取得するための手順](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)に従ってください。

2. サーバー レベルのデータ ソースを作成した場合は、App Service Linux アプリケーションを再起動します。 Tomcat が `CATALINA_HOME` を `/home/tomcat/conf` にリセットし、更新された構成を使用します。

## <a name="configure-wildfly-server"></a>WildFly サーバーを構成する

[App Service によるスケーリング](#scale-with-app-service)
[アプリケーション サーバー構成のカスタマイズ](#customize-application-server-configuration)
[モジュールと依存関係](#modules-and-dependencies)
[データ ソース](#data-sources)
[メッセージング プロバイダーを有効にする](#enable-messaging-providers)
[セッション管理キャッシュを構成する](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>App Service によるスケーリング

App Service on Linux で実行されている WildFly アプリケーション サーバーは、ドメイン構成ではなく、スタンドアロン モードで実行されます。 App Service プランをスケールアウトすると、各 WildFly インスタンスがスタンドアロン サーバーとして構成されます。

 [スケール ルール](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md)および[インスタンス数の増加](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)によって、アプリケーションを垂直方向または水平方向にスケーリングします。

### <a name="customize-application-server-configuration"></a>アプリケーション サーバー構成のカスタマイズ

Web アプリのインスタンスはステートレスなので、起動される各新しいインスタンスを、アプリケーションで必要な Wildfly 構成をサポートするように起動時に構成する必要があります。
WildFly CLI を呼び出して次のことを行うように、スタートアップ Bash スクリプトを記述できます。

- データ ソースを設定します
- メッセージング プロバイダーを構成します
- Wildfly サーバーの構成に、他のモジュールおよび依存関係を追加します。

 このスクリプトは、Wildfly の稼働中で、ただしアプリケーションが起動する前に実行されます。 スクリプトでは、`/opt/jboss/wildfly/bin/jboss-cli.sh` から呼び出される [JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) を使用して、サーバーの起動後に必要な構成や変更によってアプリケーション サーバーを構成する必要があります。

Wildfly を構成するために、CLI の対話モードを使用しないでください。 代わりに、`--file` コマンドを使用して、JBoss CLI にコマンドのスクリプトを指定できます。次に例を示します。

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

App Service インスタンスの `/home/site/deployments/tools` にスタートアップ スクリプトをアップロードします。 FTP 資格情報の取得の手順については、[このドキュメント](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#userscope)を参照してください。

Azure portal の **[Startup Script]\(スタートアップ スクリプト\)** フィールドに、スタートアップ シェル スクリプトの場所 (`/home/site/deployments/tools/your-startup-script.sh` など) を設定します。

スクリプトで使用する環境変数を渡すために、アプリケーション構成で[アプリ設定](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)を指定します。 アプリケーション設定では、バージョン コントロールされていないアプリケーションを構成するために必要な接続文字列と他のシークレットを保持します。

### <a name="modules-and-dependencies"></a>モジュールと依存関係

JBoss CLI を使用して、Wildfly クラスパスにモジュールとそれらの依存関係をインストールするには、それぞれ独自のディレクトリに次のファイルを作成する必要があります。 一部のモジュールと依存関係には、JNDI の名前付けやその他の API 固有の構成などの追加の構成が必要になることがあるため、このリストは、ほとんどの場合で依存関係を構成するために必要な最小限のセットです。

- [XML モジュール記述子](https://jboss-modules.github.io/jboss-modules/manual/#descriptors)。 この XML ファイルには、名前、属性、およびモジュールの依存関係を定義します。 この [module.xml ファイルの例](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource)では、Postgres モジュール、その JAR ファイル JDBC 依存関係、およびその他の必要なモジュール依存関係を定義しています。
- モジュールに必要なすべての JAR ファイル依存関係。
- 新しいモジュールを構成するための JBoss CLI コマンドを含むスクリプト。 このファイルには、JBoss CLI によって実行され、依存関係を使用するようにサーバーを構成するためのコマンドが含まれます。 モジュール、データ ソース、およびメッセージング プロバイダーを追加するコマンドについては、[このドキュメント](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)を参照してください。
- JBoss CLI を呼び出し、前の手順のスクリプトを実行する Bash スタートアップ スクリプト。 このファイルは、App Service インスタンスが再起動されるときか、またはスケールアウト時に新しいインスタンスがプロビジョニングされるときに実行されます。このスタートアップ スクリプトでは、JBoss コマンドが JBoss CLI に渡されるため、アプリケーションの他の任意の構成を実行できます。 少なくとも、このファイルには、JBoss CLI スクリプトコマンドを JBoss CLI に渡す 1 つのコマンドを指定できます。

```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli`
```

モジュールのファイルと内容を用意したら、次の手順に従って、Wildfly アプリケーション サーバーにモジュールを追加します。

1. ファイルを App Service インスタンスの `/home/site/deployments/tools` に FTP で送信します。 FTP 資格情報の取得の手順については、このドキュメントを参照してください。
2. Azure portal の [アプリケーション設定] ブレードで、[Startup Script]\(スタートアップ スクリプト\) フィールドにスタートアップ シェル スクリプトの場所 (例: `/home/site/deployments/tools/your-startup-script.sh`) を設定します。
3. portal の **[概要]** セクションの **[再起動]** ボタンを押すか、または Azure CLI を使用して、App Service インスタンスを再起動します。

### <a name="data-sources"></a>データ ソース

データ ソース接続用に Wildfly を構成するには、先述のモジュールのインストールと依存関係のセクションで説明した同じ手順に従います。 任意の Azure データベース サービスについても同じ手順に従うことができます。

1. データベースの種類に応じた JDBC ドライバーをダウンロードします。 便宜上、ここでは [Postgres](https://jdbc.postgresql.org/download.html) と [MySQL](https://dev.mysql.com/downloads/connector/j/) 用のドライバーとします。 ダウンロードをアンパックし、.Jar ファイルを取得します。
2. 「モジュールと依存関係」で説明した手順に従って、XML モジュール記述子、JBoss CLI スクリプト、スタートアップ スクリプト、および JDBC .jar 依存関係を作成してアップロードします。

[PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7)、[MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)、および [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) を使用した Wildfly の構成の詳細を参照できます。 先述の汎用化されたアプローチと共にカスタマイズされた手順を使用して、データ ソース定義をサーバーに追加します。

### <a name="enable-messaging-providers"></a>メッセージング プロバイダーを有効にする

メッセージング メカニズムとして Service Bus を使用してメッセージ ドリブン Bean を有効にするには:

1. [Apache QPId JMS メッセージング ライブラリ](https://qpid.apache.org/proton/index.html)を使用します。 アプリケーションの pom.xml (またはその他のビルド ファイル) にこの依存関係を含めます。

2. [Service Bus リソース](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)を作成します。 Azure Service Bus 名前空間とその名前空間内のキューおよび送受信機能を含む共有アクセス ポリシーを作成します。

3. ポリシーの主キーを URL エンコードするか、または [Service Bus SDK を使用](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory)して、共有アクセス ポリシー キーをコードに渡します。

4. モジュール XML 記述子、.jar 依存関係、JBoss CLI コマンド、および JMS プロバイダーのスタートアップ スクリプトを使用して、モジュールと依存関係のインストールのセクションで説明されている手順に従います。 4 つのファイルに加えて、JMS キューおよびトピックの JNDI 名を定義する XML ファイルを作成する必要もあります。 参照構成ファイルについては、[このリポジトリ](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig)を参照してください。

### <a name="configure-session-management-caching"></a>セッション管理キャッシュの構成

既定で App Service on Linux では、セッション アフィニティ Cookie を使用して、既存のセッションによるクライアント要求がアプリケーションの同じインスタンスにルーティングされるようにしています。 この既定の動作には、構成は必要ありませんが、いくつかの制限があります。

- アプリケーション インスタンスが再起動されるか、またはスケール ダウンされると、アプリケーション サーバーのユーザー セッションの状態が失われます。
- アプリケーションに長いセッション タイムアウト設定または固定数のユーザーがある場合、新しいセッションだけが新しく起動されたインスタンスにルーティングされるため、自動スケーリングされた新しいインスタンスが、負荷を受け取るまでにしばらく時間がかかることがあります。

[Azure Cache for Redis](/azure/azure-cache-for-redis/) などの外部セッション ストアを使用するように Wildfly を構成できます。 [既存の ARR インスタンス アフィニティ構成を無効にして](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)、セッション Cookie ベースのルーティングをオフにし、構成済みの Wildfly セッション ストアが干渉せずに動作できるようにする必要があります。

## <a name="docker-containers"></a>Docker コンテナー

Azure がサポートしている Zulu JDK をコンテナーで使用するには、[サポートされている Azul Zulu Enterprise for Azure のダウンロード ページ](https://www.azul.com/downloads/azure-only/zulu/)に記載されている事前構築済みのイメージをプルして使用するか、[Microsoft Java GitHub リポジトリ](https://github.com/Microsoft/java/tree/master/docker)にある `Dockerfile` の例を使用するようにします。

## <a name="statement-of-support"></a>サポートのステートメント

### <a name="runtime-availability"></a>ランタイムの可用性

App Service for Linux では、Java Web アプリケーションのマネージド ホスティング用に次の 2 つのランタイムがサポートされています。

- Web アーカイブ (WAR) ファイルとしてパッケージ化されたアプリケーションを実行するための [Tomcat サーブレット コンテナー](https://tomcat.apache.org/)。 サポートされているバージョンは 8.5 と 9.0 です。
- Java アーカイブ (JAR) ファイルとしてパッケージ化されたアプリケーションを実行するための Java SE ランタイム環境。 サポートされているバージョンは Java 8 と 11 です。

### <a name="jdk-versions-and-maintenance"></a>JDK のバージョンとメンテナンス

Azure でサポートされている Java Development Kit (JDK) は、[Azul Systems](https://www.azul.com/) で提供されている [Zulu](https://www.azul.com/downloads/azure-only/zulu/) です。

メジャー バージョンの更新プログラムは、Azure App Service for Linux の新しいランタイム オプションによって提供されます。 お客様は、App Service デプロイを構成することで Java のこれらの新しいバージョンに更新します。また、主要な更新プログラムをテストし、ニーズを満たしていることを確認する必要があります。

サポートされている JDK は、毎年 1 月、4 月、7 月、および 10 月の四半期ごとに自動的に適用されます。

### <a name="security-updates"></a>セキュリティ更新プログラム

重大なセキュリティの脆弱性のパッチおよび修正プログラムは、Azul Systems から利用可能になり次第リリースされます。 "重大な" 脆弱性は、[NIST Common Vulnerability Scoring System バージョン 2](https://nvd.nist.gov/cvss.cfm) で 9.0 以上の基本スコアにより定義されます。

### <a name="deprecation-and-retirement"></a>廃止と提供終了

サポートされている Java ランタイムが廃止予定の場合、影響を受けるランタイムを使用している Azure の開発者には、ランタイム終了の少なくとも 6 か月前までに廃止の通知が送信されます。

### <a name="local-development"></a>ローカル開発

開発者は、[Azul のダウンロード サイト](https://www.azul.com/downloads/azure-only/zulu/)から運用エディションの Azul Zulu Enterprise JDK をローカルでのデプロイ用にダウンロードできます。

### <a name="development-support"></a>開発サポート

[正規の Azure サポート プラン](https://azure.microsoft.com/support/plans/)を利用して Azure または [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 用の開発を行う際には、[Azure がサポートする Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) の製品サポートを利用できます。

### <a name="runtime-support"></a>ランタイム サポート

[正規のサポート プラン](https://azure.microsoft.com/support/plans/)の対象である開発者は、Azure サポートを利用して Azul Zulu JDK に関する[問題を投稿](/azure/azure-supportability/how-to-create-azure-support-request)できます。

## <a name="next-steps"></a>次の手順

[Java 開発者向けの Azure](/java/azure/) センターにアクセスして、Azure クイック スタート、チュートリアル、および Java リファレンス ドキュメントを入手してください。

Java 開発に限られない、App Service for Linux の使用に関する一般的な質問については、[App Service Linux の FAQ](app-service-linux-faq.md) で回答されています。