---
title: Windows Java アプリを構成する
description: Azure App Service の Windows VM インスタンスで Java アプリが実行されるように構成する方法について説明します。 この記事では、最も一般的な構成タスクを紹介しています。
keywords: azure app service, web アプリ, windows, oss, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 5d94da91428da2270e0f690df4dcd43ae43d8597
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961654"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Azure App Service 向けの Java アプリを構成する

::: zone pivot="platform-windows"  

Azure App Service を使用すると、Java 開発者は、完全に管理された Windows ベースのサービス上で Tomcat Web アプリケーションを迅速にビルド、デプロイ、およびスケーリングすることができます。 アプリケーションは、コマンドラインから Maven プラグインを使用して、または IntelliJ、Eclipse、Visual Studio Code などのエディターでデプロイします。

このガイドでは、App Service を使用する Java 開発者向けに主要な概念と手順を示します。 Azure App Service を使用したことがない場合は、まず [Java クイック スタート](quickstart-java.md)をお読みください。 Java 開発に限られない、App Service の使用に関する一般的な質問については、[App Service Windows の FAQ](faq-configuration-and-management.md) で回答されています。

## <a name="deploying-your-app"></a>アプリのデプロイ

[Maven 用 Azure Web アプリ プラグイン](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)を使用して、.war ファイルをデプロイできます。 一般的な IDE を使用したデプロイは、[Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) または [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse) でもサポートされています。

それ以外の場合、デプロイの方法はアーカイブの種類によって異なります。

- .war ファイルを Tomcat にデプロイするには、`/api/wardeploy/` エンドポイントを使用してアーカイブ ファイルを POST します。 この API の詳細については、[このドキュメント](./deploy-zip.md#deploy-war-file)を参照してください。
- Java SE に .jar ファイルをデプロイするには、Kudu サイトの `/api/zipdeploy/` エンドポイントを使用します。 この API の詳細については、[このドキュメント](./deploy-zip.md#rest)を参照してください。

FTP を使用して .war や .jar をデプロイしないでください。 FTP ツールは、スタートアップ スクリプト、依存関係、またはその他のランタイム ファイルをアップロードするために設計されています。 これは、Web アプリをデプロイするための最適な選択肢ではありません。

## <a name="logging-and-debugging-apps"></a>アプリのログ記録とデバッグ

パフォーマンス レポート、トラフィックの視覚エフェクト、および正常性検査は、Azure portal を介して各アプリに対して使用できます。 詳細については、「[Azure App Service 診断の概要](overview-diagnostics.md)」を参照してください。

### <a name="use-flight-recorder"></a>Flight Recorder を使用する

Azul JVM を使用する App Service のすべての Java ランタイムには、Zulu Flight Recorder が付属しています。 これを使用すると、JVM、システム、および Java レベルのイベントを記録して、Java アプリケーションの動作を監視し、問題のトラブルシューティングを行うことができます。

時間を指定して記録を行うには、Java アプリケーションの PID (プロセス ID) が必要です。 PID を検索するには、ブラウザーで Web アプリの SCM サイト (https://<your-site-name>.scm.azurewebsites.net/ProcessExplorer/) を開きます。 このページには、Web アプリで実行中のプロセスが表示されます。 テーブル内で "java" という名前のプロセスを探し、対応する PID (プロセス ID) をコピーします。

次に、SCM サイト上部のツール バーにある **デバッグ コンソール** を開き、次のコマンドを実行します。 `<pid>` を、先ほどコピーしたプロセス ID に置き換えます。 このコマンドにより、Java アプリケーションのプロファイラーによる 30 秒の記録が開始され、`D:\home` ディレクトリに `timed_recording_example.jfr` という名前のファイルが生成されます。

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

詳しくは、[Jcmd コマンド リファレンス](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)をご覧ください。

#### <a name="analyze-jfr-files"></a>`.jfr` ファイルの分析

JFR ファイルをご自分のローカル コンピューターにダウンロードするには、[FTPS](deploy-ftp.md) を使います。 JFR ファイルを分析するには、[Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/) をダウンロードしてインストールします。 Zulu Mission Control については、[Azul のドキュメント](https://docs.azul.com/zmc/)と[インストール手順](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)をご覧ください。

### <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

詳細については、[Cloud Shell でのログのストリーミング](troubleshoot-diagnostic-logs.md#in-cloud-shell)に関する記事をご覧ください。

### <a name="app-logging"></a>アプリのログ記録

Azure portal または [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) を使用して[アプリケーションのログ記録](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)を有効にし、アプリケーションの標準コンソール出力および標準コンソール エラー ストリームをローカル ファイル システムまたは Azure BLOB ストレージに書き込むよう App Service を構成します。 App Service のローカル ファイル システム インスタンスへのログ記録は、構成されてから 12 時間後に無効になります。 リテンション期間を長くする必要がある場合は、BLOB ストレージ コンテナーに出力を書き込むようアプリケーションを構成します。 Java と Tomcat のアプリ ログは */LogFiles/Application/* ディレクトリにあります。

アプリケーションで [Logback](https://logback.qos.ch/) または [Log4j](https://logging.apache.org/log4j) をトレースに使用している場合は、「[Application Insights を使用した Java トレース ログの探索](../azure-monitor/app/java-trace-logs.md)」にあるログ記録フレームワークの構成手順に従って、これらのトレースを確認のために Azure Application Insights に転送することができます。


## <a name="customization-and-tuning"></a>カスタマイズとチューニング

Azure App Service では、Azure portal および CLI を使用したチューニングとカスタマイズが追加設定なしでサポートされています。 Java 以外の特定の Web アプリの構成については、次の記事を確認してください。

- [アプリケーションの設定の構成](configure-common.md#configure-app-settings)
- [カスタム ドメインの設定](app-service-web-tutorial-custom-domain.md)
- [TLS バインドの構成](configure-ssl-bindings.md)
- [CDN の追加](../cdn/cdn-add-to-web-app.md)
- [Kudu サイトを構成する](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Java ランタイム オプションを設定する

割り当てられたメモリまたはその他の JVM ランタイムのオプションを設定するには、[アプリ設定](configure-common.md#configure-app-settings)を作成して `JAVA_OPTS` と名付け、オプションを指定します。 App Service では、開始時にこの設定が環境変数として Java ランタイムに渡されます。

Azure portal の Web アプリ用の **[アプリケーション設定]** で、`-Xms512m -Xmx1204m` などの追加の設定を含む、`JAVA_OPTS` という名前の新しいアプリ設定を作成します。

Maven プラグインからアプリ設定を構成するには、Azure プラグイン セクションで設定/値のタグを追加します。 次の例では、特定の最小および最大の Java ヒープ サイズを設定します。

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

App Service プランで 1 つのデプロイ スロットを使用して 1 つのアプリケーションを実行している開発者は、次のオプションを使用できます。

- B1 および S1 インスタンス: `-Xms1024m -Xmx1024m`
- B2 および S2 インスタンス: `-Xms3072m -Xmx3072m`
- B3 および S3 インスタンス: `-Xms6144m -Xmx6144m`

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

Azure portal の Web アプリ用の **[アプリケーション設定]** で、値 `-Dfile.encoding=UTF-8` を含む、`JAVA_OPTS` という名前の新しいアプリ設定を作成します。

または、App Service Maven プラグインを使用してアプリ設定を構成できます。 プラグイン構成で、設定名および値のタグを追加します。

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>JSP ファイルをプリコンパイルする

Tomcat アプリケーションのパフォーマンスを向上させるには、App Service にデプロイする前に、JSP ファイルをコンパイルします。 Apache Sling によって提供されている [Maven プラグイン](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html)を使用するか、この [Ant ビルド ファイル](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)を使用できます。

## <a name="secure-applications"></a>セキュリティで保護されたアプリケーション

App Service で実行される Java アプリケーションは、他のアプリケーションと同じ一連の[セキュリティのベスト プラクティス](../security/fundamentals/paas-applications-using-app-services.md)を備えています。

### <a name="authenticate-users-easy-auth"></a>ユーザーを認証する (Easy Auth)

**[認証/承認]** オプションを使用して、Azure portal でアプリ認証を設定します。 そこから、Azure Active Directory、または Facebook、Google、GitHub などのソーシャル ログインを使用して、認証を有効にすることができます。 Azure portal の構成は、1 つの認証プロバイダーを構成するときにのみ機能します。 詳細については、「[Azure Active Directory ログインを使用するよう App Service アプリを構成する](configure-authentication-provider-aad.md)」と、他の ID プロバイダーの関連記事を参照してください。 複数のサインイン プロバイダーを有効にする必要がある場合は、[App Service 認証のカスタマイズ](app-service-authentication-how-to.md)に関する記事の手順に従います。

#### <a name="tomcat"></a>Tomcat

Tomcat アプリケーションでは、Principal オブジェクトを Map オブジェクトにキャストすることで、サーブレットから直接ユーザーの要求にアクセスできます。 Map オブジェクトによって、各要求の種類がその種類の要求のコレクションにマップされます。 以下のコードで、`request` は `HttpServletRequest` のインスタンスです。

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

これで `Map` オブジェクトの特定の要求を検査できます。 たとえば、次のコード スニペットでは、すべての要求の種類を反復処理し、各コレクションのコンテンツを出力します。

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

ユーザーをサインアウトさせるには、`/.auth/ext/logout` パスを使用します。 他のアクションを実行する場合は、「[Azure App Service 上での認証と承認の高度な使用方法](./app-service-authentication-how-to.md)」を参照してください。 Tomcat の [HttpServletRequest インターフェイス](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)とそのメソッドに関する公式ドキュメントもあります。 次のサーブレット メソッドも、ご利用の App Service 構成に基づいてハイドレートされます。

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

この機能を無効にするには、`1` の値で `WEBSITE_AUTH_SKIP_PRINCIPAL` という名前のアプリケーション設定を作成します。 App Service によって追加されるすべてのサーブレット フィルターを無効にするには、`1` の値を含む `WEBSITE_SKIP_FILTERS` という名前の設定を作成します。

### <a name="configure-tlsssl"></a>TLS/SSL を構成する

「[Azure App Service で TLS バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)」の手順に従って、既存の TLS/SSL 証明書をアップロードし、アプリケーションのドメイン名にバインドします。 既定では、アプリケーションで引き続き HTTP 接続が許可されます。チュートリアルの特定の手順に従って、SSL と TLS を適用します。

### <a name="use-keyvault-references"></a>KeyVault 参照を使用する

[Azure Key Vault](../key-vault/general/overview.md) では、アクセス ポリシーと監査履歴を使用した一元的なシークレット管理を提供しています。 シークレット (パスワードや接続文字列など) を KeyVault に格納し、環境変数を使用してアプリケーション内でこれらのシークレットにアクセスすることができます。

最初に、[Key Vault へのアクセス権をアプリに付与](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)したり、[アプリケーション設定で自分のシークレットに対する KeyVault 参照を設定](app-service-key-vault-references.md#reference-syntax)したりするための手順に従います。 App Service のターミナルにリモートでアクセスしている間に環境変数を出力することで、シークレットへの参照が解決されることを確認できます。

Spring または Tomcat 構成ファイルにこれらのシークレットを挿入するには、環境変数の挿入構文 (`${MY_ENV_VAR}`) を使用します。 Spring の構成ファイルについては、[外部化された構成](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)に関するこちらのドキュメントを参照してください。


## <a name="configure-apm-platforms"></a>APM プラットフォームを構成する

このセクションでは、NewRelic と AppDynamics アプリケーション パフォーマンスの監視 (APM) プラットフォームで、Linux 上の Azure App Service にデプロイされた Java アプリケーションを接続する方法を示します。

### <a name="configure-new-relic"></a>New Relic の構成

1. [NewRelic.com](https://newrelic.com/signup) で New Relic アカウントを作成します
2. NewRelic から Java エージェントをダウンロードします。ファイル名は *newrelic-java-x.x.x.zip* のようになります。
3. ライセンス キーをコピーします。これは後ほどエージェントの構成を行う際に必要です。
4. [Kudu コンソール](https://github.com/projectkudu/kudu/wiki/Kudu-console)を使用して、新しいディレクトリ */home/site/wwwroot/apm* を作成します。
5. */home/site/wwwroot/apm* の下にあるディレクトリにアンパックされた New Relic Java エージェント ファイルをアップロードします。 エージェント用のファイルは */home/site/wwwroot/apm/newrelic* 内にある必要があります。
6. */home/site/wwwroot/apm/newrelic/newrelic.yml* で YAML ファイルを変更し、プレース ホルダー ライセンスの値を独自のライセンス キーに置き換えます。
7. Azure portal で App Service でアプリケーションを参照し、新しいアプリケーション設定を作成します。
    - もしアプリが **Java SE** を使用している場合、`JAVA_OPTS` の名前および `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` の値を持つ環境変数を作成します。
    - **Tomcat** を使用している場合、`CATALINA_OPTS`の名前および `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` の値を持つ環境変数を作成します。

### <a name="configure-appdynamics"></a>AppDynamics の構成

1. [AppDynamics.com](https://www.appdynamics.com/community/register/) で AppDynamics アカウントを作成します
2. AppDynamics の Web サイトから Java エージェントをダウンロードします。ファイル名は *AppServerAgent-x.x.x.xxxxx.zip* のようになります
3. [Kudu コンソール](https://github.com/projectkudu/kudu/wiki/Kudu-console)を使用して、新しいディレクトリ */home/site/wwwroot/apm* を作成します。
4. */home/site/wwwroot/apm* の下にあるディレクトリに Java エージェント ファイルをアップロードします。 エージェント用のファイルは */home/site/wwwroot/apm/appdynamics* 内にある必要があります。
5. Azure portal で App Service でアプリケーションを参照し、新しいアプリケーション設定を作成します。
    - **Java SE** を使用している場合、`JAVA_OPTS` の名前および `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` の値を持ち、`<app-name>` の App Service 名を持つ環境変数を作成します。
    - **Tomcat** を使用している場合、`CATALINA_OPTS` の名前および `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` の値を持ち、`<app-name>` の App Service 名を持つ環境変数を作成します。

>  `JAVA_OPTS` または `CATALINA_OPTS` 用の環境変数がすでにある場合、`-javaagent:/...` のオプションを現在値の最期に追加します。

## <a name="data-sources"></a>データ ソース

### <a name="tomcat"></a>Tomcat

これらの説明は、すべてのデータベース接続に適用されます。 プレースホルダーを、選択したデータベースのドライバーのクラス名と JAR ファイルに置き換える必要があります。 一般的なデータベースのクラス名とドライバーのダウンロードを含む表を次に示します。

| データベース   | ドライバーのクラス名                             | JDBC ドライバー                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [ダウンロード](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [ダウンロード](https://dev.mysql.com/downloads/connector/j/) ("プラットフォームに依存しない" を選択) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [ダウンロード](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Java Database Connectivity (JDBC) または Java Persistence API (JPA) を使用するように Tomcat を構成するには、まず、起動時に Tomcat によって読み込まれる `CATALINA_OPTS` 環境変数をカスタマイズします。 [App Service Maven プラグイン](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)のアプリ設定を使用して、次の値を設定します。

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

または、Azure portal の **[構成]**  >  **[アプリケーション設定]** ページで環境変数を設定します。

次に、データ ソースを Tomcat サーブレットで実行されている 1 つのアプリケーション、またはすべてのアプリケーションのどちらに対して使用可能にする必要があるかを判定します。

#### <a name="application-level-data-sources"></a>アプリケーション レベル データ ソース

1. プロジェクトの *META-INF/* ディレクトリ内に *context.xml* ファイルを作成します。 *META-INF/* ディレクトリが存在しない場合は作成します。

2. *context.xml* で、`Context` 要素を追加してデータ ソースを JNDI アドレスにリンクします。 `driverClassName` プレースホルダーを、上の表にあるドライバーのクラス名に置き換えます。

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

3. アプリケーションでこのデータ ソースを使用するよう、アプリケーションの *web.xml* を更新します。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>構成を完了する

最後に、ドライバーの JAR を Tomcat クラスパス内に配置し、App Service を再起動します。 JDBC ドライバー ファイルが Tomcat クラスローダーで確実に使用できるように、これらのファイルを */home/tomcat/lib* ディレクトリに配置します。 (このディレクトリがまだ存在しない場合は作成します。)これらのファイルを App Service インスタンスにアップロードするには、次の手順を行います。

1. [Cloud Shell](https://shell.azure.com) で、Web アプリ拡張機能をインストールします。

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. 次の CLI コマンドを実行して、ローカル システムから App Service への SSH トンネルを作成します。

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. SFTP クライアントを使用してローカルのトンネル ポートに接続し、ファイルを */home/tomcat/lib* フォルダーにアップロードします。

あるいは、FTP クライアントを使用して JDBC ドライバーをアップロードできます。 [FTP 資格情報を取得するための手順](deploy-configure-credentials.md)に従ってください。

## <a name="configuring-tomcat"></a>Tomcat の構成

Tomcat の `server.xml` または他の構成ファイルを編集するには、最初にポータルで Tomcat のメジャー バージョンを確認してメモします。

1. `env` コマンドを実行して、ご使用のバージョンの Tomcat ホーム ディレクトリを見つけます。 `AZURE_TOMCAT`で始まり、ご使用のメジャー バージョンに一致する環境変数を検索します。 たとえば、`AZURE_TOMCAT85_HOME` は Tomcat 8.5 を指しています。
1. ご使用のバージョンの Tomcat ホーム ディレクトリを特定したら、構成ディレクトリを `D:\home` にコピーします。 たとえば、`AZURE_TOMCAT85_HOME` の値が `D:\Program Files (x86)\apache-tomcat-8.5.37` の場合、コピーされたディレクトリの新しいパスは `D:\home\apache-tomcat-8.5.37` になります。

最後に、お客様のアプリ サービスを再起動します。 デプロイメントの場所は、以前と同様に `D:\home\site\wwwroot\webapps` になります。

## <a name="configure-java-se"></a>Java SE を構成する

Windows 上の Java SE で JAR アプリケーションを実行している場合、`server.port` は、アプリケーションの起動時にコマンドライン オプションとして渡されます。 HTTP ポートは、環境変数 `HTTP_PLATFORM_PORT` から手動で解決できます。 この環境変数の値は、アプリケーションがリッスンする HTTP ポートになります。 

## <a name="java-runtime-statement-of-support"></a>Java ランタイムのサポート ステートメント

### <a name="jdk-versions-and-maintenance"></a>JDK のバージョンとメンテナンス

Azure でサポートされている Java Development Kit (JDK) は、[Azul Systems](https://www.azul.com/) で提供されている [Zulu](https://www.azul.com/downloads/azure-only/zulu/) です。

メジャー バージョンの更新プログラムは、Azure App Service for Windows の新しいランタイム オプションによって提供されます。 お客様は、App Service デプロイを構成することで Java のこれらの新しいバージョンに更新します。また、主要な更新プログラムをテストし、ニーズを満たしていることを確認する必要があります。

サポートされている JDK は、毎年 1 月、4 月、7 月、および 10 月の四半期ごとに自動的に適用されます。 Azure 上の Java の詳細については、[こちらのサポート ドキュメント](/azure/developer/java/fundamentals/java-jdk-long-term-support)を参照してください。

### <a name="security-updates"></a>セキュリティ更新プログラム

重大なセキュリティの脆弱性のパッチおよび修正プログラムは、Azul Systems から利用可能になり次第リリースされます。 "重大な" 脆弱性は、[NIST Common Vulnerability Scoring System バージョン 2](https://nvd.nist.gov/vuln-metrics/cvss) で 9.0 以上の基本スコアにより定義されます。

Tomcat 8.0 は、[2018 年 9 月 30 日にサポートが終了 (EOL)](https://tomcat.apache.org/tomcat-80-eol.html) しました。 ランタイムは Azure App Service で引き続き機能しますが、Azure では Tomcat 8.0 へのセキュリティ更新プログラムは適用されません。 可能であれば、Tomcat 8.5 または 9.0 にアプリケーションを移行してください。 Azure App Service では、Tomcat 8.5 と 9.0 の両方を使用できます。 詳細については、[Tomcat の公式 Web サイト](https://tomcat.apache.org/whichversion.html)を参照してください。 

### <a name="deprecation-and-retirement"></a>廃止と提供終了

サポートされている Java ランタイムが廃止予定の場合、影響を受けるランタイムを使用している Azure の開発者には、ランタイム終了の少なくとも 6 か月前までに廃止の通知が送信されます。

### <a name="local-development"></a>ローカル開発

開発者は、[Azul のダウンロード サイト](https://www.azul.com/downloads/azure-only/zulu/)から運用エディションの Azul Zulu Enterprise JDK をローカルでのデプロイ用にダウンロードできます。

### <a name="development-support"></a>開発サポート

[正規の Azure サポート プラン](https://azure.microsoft.com/support/plans/)を利用して Azure または [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 用の開発を行うとき、Microsoft では [Azure がサポートする Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) の製品サポートを利用できます。

### <a name="runtime-support"></a>ランタイム サポート

[正規のサポート プラン](https://azure.microsoft.com/support/plans/)の対象である開発者は、Azure サポートを利用して Azul Zulu JDK に関する[問題を投稿](../azure-portal/supportability/how-to-create-azure-support-request.md)できます。

## <a name="next-steps"></a>次のステップ

このトピックでは、Azure App Service for Windows の Java ランタイムのサポート ステートメントを提供します。

- Azure App Service での Web アプリケーションのホストの詳細については、「[App Service の概要](overview.md)」を参照してください。
- Azure 開発に関する Java については、「[Java 開発者向けの Azure](/java/azure/?view=azure-java-stable)」を参照してください。

::: zone-end

::: zone pivot="platform-linux"

Azure App Service on Linux を使用すると、Java 開発者は、完全に管理された Linux ベースのサービス上で Tomcat または Java Standard Edition (SE) パッケージ化 Web アプリケーションを迅速にビルド、デプロイ、およびスケーリングすることができます。 アプリケーションは、コマンドラインから Maven プラグインを使用して、または IntelliJ、Eclipse、Visual Studio Code などのエディターでデプロイします。

このガイドでは、App Service のビルトイン Linux コンテナーを使用する Java 開発者のために、主要な概念と手順を示します。 Azure App Service を使用したことがない場合は、[Java クイックスタート](quickstart-java.md)に従ってください。

## <a name="deploying-your-app"></a>アプリのデプロイ

[Azure App Service に Maven プラグイン](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)を使用して、.jar ファイルと .war ファイルの両方をデプロイできます。 一般的な IDE を使用したデプロイは、[Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) または [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse) でもサポートされています。

それ以外の場合、デプロイの方法はアーカイブの種類によって異なります。

- .war ファイルを Tomcat にデプロイするには、`/api/wardeploy/` エンドポイントを使用してアーカイブ ファイルを POST します。 この API の詳細については、[このドキュメント](./deploy-zip.md#deploy-war-file)を参照してください。
- Java SE イメージ上に .jar ファイルをデプロイするには、Kudu サイトの `/api/zipdeploy/` エンドポイントを使用します。 この API の詳細については、[このドキュメント](./deploy-zip.md#rest)を参照してください。

FTP を使用して .war や .jar をデプロイしないでください。 FTP ツールは、スタートアップ スクリプト、依存関係、またはその他のランタイム ファイルをアップロードするために設計されています。 これは、Web アプリをデプロイするための最適な選択肢ではありません。

## <a name="logging-and-debugging-apps"></a>アプリのログ記録とデバッグ

パフォーマンス レポート、トラフィックの視覚エフェクト、および正常性検査は、Azure portal を介して各アプリに対して使用できます。 詳細については、「[Azure App Service 診断の概要](overview-diagnostics.md)」を参照してください。

### <a name="ssh-console-access"></a>SSH コンソール アクセス

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

詳細については、[Cloud Shell でのログのストリーミング](troubleshoot-diagnostic-logs.md#in-cloud-shell)に関する記事をご覧ください。

### <a name="app-logging"></a>アプリのログ記録

Azure portal または [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) を使用して[アプリケーションのログ記録](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)を有効にし、アプリケーションの標準コンソール出力および標準コンソール エラー ストリームをローカル ファイル システムまたは Azure BLOB ストレージに書き込むよう App Service を構成します。 App Service のローカル ファイル システム インスタンスへのログ記録は、構成されてから 12 時間後に無効になります。 リテンション期間を長くする必要がある場合は、BLOB ストレージ コンテナーに出力を書き込むようアプリケーションを構成します。 Java と Tomcat のアプリ ログは */home/LogFiles/Application/* ディレクトリにあります。

>[!NOTE]
>App Service のローカル ファイル システムへのログ記録が 12 時間後に無効になるというのは、Window ベースの App Services にのみ適用されます。 Linux ベースの App Services の Azure Blob Storage のログ記録は [Azure Monitor (プレビュー) を使用してのみ構成できます](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) 

アプリケーションで [Logback](https://logback.qos.ch/) または [Log4j](https://logging.apache.org/log4j) をトレースに使用している場合は、「[Application Insights を使用した Java トレース ログの探索](../azure-monitor/app/java-trace-logs.md)」にあるログ記録フレームワークの構成手順に従って、これらのトレースを確認のために Azure Application Insights に転送することができます。

### <a name="troubleshooting-tools"></a>トラブルシューティング ツール

組み込みの Java イメージは [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) オペレーティング システムに基づいています。 `apk` パッケージ マネージャーを使用し、トラブルシューティングのツールやコマンドをインストールします。

### <a name="flight-recorder"></a>Flight Recorder

App Service 上のすべての Linux Java イメージには Zulu Flight Recorder がインストールされているので、JVM に簡単に接続して、プロファイラーの記録を開始したり、ヒープ ダンプを生成したりできます。

#### <a name="timed-recording"></a>時間指定の記録

始めるには、対象のアプリ サービスに SSH で接続し、`jcmd` コマンドを実行して、実行されているすべての Java プロセスの一覧を表示します。 jcmd 自体に加えて、実行されているご自分の Java アプリケーションとプロセス ID 番号 (pid) が表示されます。

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

JVM の 30 秒間の記録を開始するには、次のコマンドを実行します。 これにより、JVM がプロファイリングされて、*jfr_example.jfr* という名前の JFR ファイルがホーム ディレクトリに作成されます。 (116 は、ご自分の Java アプリの pid に置き換えてください。)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

30 秒の期間中には、`jcmd 116 JFR.check` を実行することで、記録が取得されていることを確認できます。 これにより、特定の Java プロセスに対するすべての記録が表示されます。

#### <a name="continuous-recording"></a>継続的な記録

Zulu Flight Recorder を使うと、ランタイムのパフォーマンスに対する影響を最小限にして、Java アプリケーションを継続的にプロファイリングできます ([出典](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf))。 そのためには、次の Azure CLI コマンドを実行し、必要な構成で JAVA_OPTS という名前のアプリ設定を作成します。 JAVA_OPTS アプリ設定の内容は、アプリの起動時に `java` コマンドに渡されます。

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

記録が開始されたら、`JFR.dump` コマンドを使用して、いつでも現在の記録データをダンプできます。

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

詳しくは、[Jcmd コマンド リファレンス](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)をご覧ください。

### <a name="analyzing-recordings"></a>記録の分析

JFR ファイルをご自分のローカル コンピューターにダウンロードするには、[FTPS](deploy-ftp.md) を使います。 JFR ファイルを分析するには、[Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/) をダウンロードしてインストールします。 Zulu Mission Control については、[Azul のドキュメント](https://docs.azul.com/zmc/)と[インストール手順](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)をご覧ください。

## <a name="customization-and-tuning"></a>カスタマイズとチューニング

Azure App Service for Linux では、Azure portal および CLI を使用したチューニングとカスタマイズが追加設定なしでサポートされています。 Java 以外の特定の Web アプリの構成については、次の記事を確認してください。

- [アプリケーションの設定の構成](configure-common.md#configure-app-settings)
- [カスタム ドメインの設定](app-service-web-tutorial-custom-domain.md)
- [SSL バインドの構成](configure-ssl-bindings.md)
- [CDN の追加](../cdn/cdn-add-to-web-app.md)
- [Kudu サイトを構成する](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Java ランタイム オプションを設定する

Tomcat と Java SE の両方の環境で、割り当てられたメモリまたはその他の JVM ランタイムのオプションを設定するには、[アプリ設定](configure-common.md#configure-app-settings)を作成して `JAVA_OPTS` と名付け、オプションを指定します。 App Service Linux では、開始時にこの設定が環境変数として Java ランタイムに渡されます。

Azure portal の Web アプリ用の **[アプリケーション設定]** で、`-Xms512m -Xmx1204m` などの追加の設定を含む、`JAVA_OPTS` という名前の新しいアプリ設定を作成します。

Maven プラグインからアプリ設定を構成するには、Azure プラグイン セクションで設定/値のタグを追加します。 次の例では、特定の最小および最大の Java ヒープ サイズを設定します。

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

App Service プランで 1 つのデプロイ スロットを使用して 1 つのアプリケーションを実行している開発者は、次のオプションを使用できます。

- B1 および S1 インスタンス: `-Xms1024m -Xmx1024m`
- B2 および S2 インスタンス: `-Xms3072m -Xmx3072m`
- B3 および S3 インスタンス: `-Xms6144m -Xmx6144m`

アプリケーション ヒープ設定をチューニングする際には、App Service プランの詳細を確認し、複数のアプリケーションおよびデプロイ スロットのニーズを考慮して、メモリの最適な割り当てを特定する必要があります。

JAR アプリケーションをデプロイする場合、組み込みのイメージによりアプリが正しく識別されるよう、名前を *app.jar* にしてください。 (Maven プラグインでは、名前がこのように自動的に変更されます。)JAR の名前を *app.jar* に変更しない場合、JAR を実行するコマンドが含まれるシェル スクリプトをアップロードできます。 その後、ポータルの構成セクションで [[スタートアップ ファイル]](faq-app-service-linux.md#built-in-images) テキストボックスにこのスクリプトの完全なパスを貼り付けます。 スタートアップ スクリプトは、配置先のディレクトリからは実行されません。 そのため、スタートアップ スクリプトでファイルを参照するには、常に絶対パスを使用します (例: `java -jar /home/myapp/myapp.jar`)。

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

Azure portal の Web アプリ用の **[アプリケーション設定]** で、値 `-Dfile.encoding=UTF-8` を含む、`JAVA_OPTS` という名前の新しいアプリ設定を作成します。

または、App Service Maven プラグインを使用してアプリ設定を構成できます。 プラグイン構成で、設定名および値のタグを追加します。

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>スタートアップ タイムアウトの調整

Java アプリケーションが特に大きい場合、スタートアップの時間制限を増やす必要があります。 これを行うには、アプリケーション設定を作成し、`WEBSITES_CONTAINER_START_TIME_LIMIT` と App Service がタイムアウトするまでの時間を秒数で設定します。最大値は `1800` 秒です。

### <a name="pre-compile-jsp-files"></a>JSP ファイルをプリコンパイルする

Tomcat アプリケーションのパフォーマンスを向上させるには、App Service にデプロイする前に、JSP ファイルをコンパイルします。 Apache Sling によって提供されている [Maven プラグイン](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html)を使用するか、この [Ant ビルド ファイル](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)を使用できます。

## <a name="secure-applications"></a>セキュリティで保護されたアプリケーション

App Service for Linux で実行される Java アプリケーションは、他のアプリケーションと同じ一連の[セキュリティのベスト プラクティス](../security/fundamentals/paas-applications-using-app-services.md)を備えています。

### <a name="authenticate-users-easy-auth"></a>ユーザーを認証する (Easy Auth)

**[認証/承認]** オプションを使用して、Azure portal でアプリ認証を設定します。 そこから、Azure Active Directory、または Facebook、Google、GitHub などのソーシャル ログインを使用して、認証を有効にすることができます。 Azure portal の構成は、1 つの認証プロバイダーを構成するときにのみ機能します。 詳細については、「[Azure Active Directory ログインを使用するよう App Service アプリを構成する](configure-authentication-provider-aad.md)」と、他の ID プロバイダーの関連記事を参照してください。 複数のサインイン プロバイダーを有効にする必要がある場合は、[App Service 認証のカスタマイズ](app-service-authentication-how-to.md)に関する記事の手順に従います。

#### <a name="tomcat"></a>Tomcat

Tomcat アプリケーションでは、Principal オブジェクトを Map オブジェクトにキャストすることで、サーブレットから直接ユーザーの要求にアクセスできます。 Map オブジェクトによって、各要求の種類がその種類の要求のコレクションにマップされます。 以下のコードで、`request` は `HttpServletRequest` のインスタンスです。

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

これで `Map` オブジェクトの特定の要求を検査できます。 たとえば、次のコード スニペットでは、すべての要求の種類を反復処理し、各コレクションのコンテンツを出力します。

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

ユーザーをサインアウトさせるには、`/.auth/ext/logout` パスを使用します。 他のアクションを実行する場合は、「[Azure App Service 上での認証と承認の高度な使用方法](./app-service-authentication-how-to.md)」を参照してください。 Tomcat の [HttpServletRequest インターフェイス](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)とそのメソッドに関する公式ドキュメントもあります。 次のサーブレット メソッドも、ご利用の App Service 構成に基づいてハイドレートされます。

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

この機能を無効にするには、`1` の値で `WEBSITE_AUTH_SKIP_PRINCIPAL` という名前のアプリケーション設定を作成します。 App Service によって追加されるすべてのサーブレット フィルターを無効にするには、`1` の値を含む `WEBSITE_SKIP_FILTERS` という名前の設定を作成します。

#### <a name="spring-boot"></a>Spring Boot

Spring Boot 開発者は、[Azure Active Directory Spring Boot スターター](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)を使用して、使い慣れた Spring Security の注釈と API を使用してアプリケーションをセキュリティで保護することができます。 *application.properties* ファイルで最大ヘッダー サイズを大きくしてください。 `16384` の値をお勧めします。

### <a name="configure-tlsssl"></a>TLS/SSL を構成する

「[Azure App Service で SSL バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)」の手順に従って、既存の SSL 証明書をアップロードし、アプリケーションのドメイン名にバインドします。 既定では、アプリケーションで引き続き HTTP 接続が許可されます。チュートリアルの特定の手順に従って、SSL と TLS を適用します。

### <a name="use-keyvault-references"></a>KeyVault 参照を使用する

[Azure Key Vault](../key-vault/general/overview.md) では、アクセス ポリシーと監査履歴を使用した一元的なシークレット管理を提供しています。 シークレット (パスワードや接続文字列など) を KeyVault に格納し、環境変数を使用してアプリケーション内でこれらのシークレットにアクセスすることができます。

最初に、[Key Vault へのアクセス権をアプリに付与](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)したり、[アプリケーション設定で自分のシークレットに対する KeyVault 参照を設定](app-service-key-vault-references.md#reference-syntax)したりするための手順に従います。 App Service のターミナルにリモートでアクセスしている間に環境変数を出力することで、シークレットへの参照が解決されることを確認できます。

Spring または Tomcat 構成ファイルにこれらのシークレットを挿入するには、環境変数の挿入構文 (`${MY_ENV_VAR}`) を使用します。 Spring の構成ファイルについては、[外部化された構成](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)に関するこちらのドキュメントを参照してください。

### <a name="using-the-java-key-store"></a>Java キー ストアの使用

既定では、[App Service Linux にアップロードされた](configure-ssl-certificate.md)パブリック証明書またはプライベート証明書は、コンテナーの起動時にそれぞれの Java キー ストアに読み込まれます。 証明書のアップロード後、証明書が Java キー ストアに読み込まれるようにするために、App Service を再起動する必要があります。 パブリック証明書は `$JAVA_HOME/jre/lib/security/cacerts` のキー ストアに読み込まれ、プライベート証明書は `$JAVA_HOME/lib/security/client.jks` に格納されます。

Java キー ストアの証明書を使用して JDBC 接続を暗号化するために、追加の構成が必要になる場合があります。 選択した JDBC ドライバーのドキュメントを参照してください。

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Java キー ストアを初期化する

`import java.security.KeyStore` オブジェクトを初期化するには、キー ストア ファイルをパスワードと共に読み込みます。 両方のキー ストアの既定のパスワードは "changeit" です。

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>キー ストアを手動で読み込む

キー ストアに証明書を手動で読み込むことができます。 アプリ設定 `SKIP_JAVA_KEYSTORE_LOAD` を作成し、値を `1` に設定して、証明書がキー ストアに自動的に読み込まれないように App Service を無効にします。 Azure portal 経由で App Service にアップロードされたすべてのパブリック証明書は `/var/ssl/certs/` に格納されます。 プライベート証明書は `/var/ssl/private/` に格納されます。

App Service への [SSH 接続を開き](configure-linux-open-ssh-session.md)、コマンド `keytool` を実行することで、Java キー ツールと対話することやデバッグを行うことができます。 コマンドの一覧については、[キー ツールのドキュメント](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)を参照してください。 キー　ストア API の詳細については、[公式ドキュメント](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)を参照してください。

## <a name="configure-apm-platforms"></a>APM プラットフォームを構成する

このセクションでは、NewRelic と AppDynamics アプリケーション パフォーマンスの監視 (APM) プラットフォームで、Linux 上の Azure App Service にデプロイされた Java アプリケーションを接続する方法を示します。

### <a name="configure-new-relic"></a>New Relic の構成

1. [NewRelic.com](https://newrelic.com/signup) で NewRelic アカウントを作成します
2. NewRelic から Java エージェントをダウンロードします。ファイル名は *newrelic-java-x.x.x.zip* のようになります。
3. ライセンス キーをコピーします。これは後ほどエージェントの構成を行う際に必要です。
4. [App Service インスタンスに SSH で接続し](configure-linux-open-ssh-session.md)、新しいディレクトリ */home/site/wwwroot/apm* を作成します。
5. */home/site/wwwroot/apm* の下にあるディレクトリにアンパックされた NewRelic Java エージェント ファイルをアップロードします。 エージェント用のファイルは */home/site/wwwroot/apm/newrelic* 内にある必要があります。
6. */home/site/wwwroot/apm/newrelic/newrelic.yml* で YAML ファイルを変更し、プレース ホルダー ライセンスの値を独自のライセンス キーに置き換えます。
7. Azure portal で App Service でアプリケーションを参照し、新しいアプリケーション設定を作成します。
    - もしアプリが **Java SE** を使用している場合、`JAVA_OPTS` の名前および `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` の値を持つ環境変数を作成します。
    - **Tomcat** を使用している場合、`CATALINA_OPTS`の名前および `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` の値を持つ環境変数を作成します。

### <a name="configure-appdynamics"></a>AppDynamics の構成

1. [AppDynamics.com](https://www.appdynamics.com/community/register/) で AppDynamics アカウントを作成します
2. AppDynamics の Web サイトから Java エージェントをダウンロードします。ファイル名は *AppServerAgent-x.x.x.xxxxx.zip* のようになります
3. [App Service インスタンスに SSH で接続し](configure-linux-open-ssh-session.md)、新しいディレクトリ */home/site/wwwroot/apm* を作成します。
4. */home/site/wwwroot/apm* の下にあるディレクトリに Java エージェント ファイルをアップロードします。 エージェント用のファイルは */home/site/wwwroot/apm/appdynamics* 内にある必要があります。
5. Azure portal で App Service でアプリケーションを参照し、新しいアプリケーション設定を作成します。
    - **Java SE** を使用している場合、`JAVA_OPTS` の名前および `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` の値を持ち、`<app-name>` の App Service 名を持つ環境変数を作成します。
    - **Tomcat** を使用している場合、`CATALINA_OPTS` の名前および `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` の値を持ち、`<app-name>` の App Service 名を持つ環境変数を作成します。

    > [!NOTE]
    > `JAVA_OPTS` または `CATALINA_OPTS` 用の環境変数がすでにある場合、`-javaagent:/...` のオプションを現在値の最期に追加します。
    
## <a name="configure-jar-applications"></a>JAR アプリケーションの構成

### <a name="starting-jar-apps"></a>JAR アプリの開始

App Service で想定されている JAR アプリケーションの既定の名前は *app.jar* です。 この名前の場合、自動的に実行されます。 Maven ユーザーの場合、`<finalName>app</finalName>` を *pom.xml* の `<build>` セクションに含めることで JAR 名を設定できます。 `archiveFileName` プロパティを設定することによって、[Gradle で同じことを行うことができます](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName)。

JAR に別の名前を使用する場合、JAR ファイルを実行する[スタートアップ コマンド](faq-app-service-linux.md#built-in-images)を指定する必要もあります。 たとえば、「 `java -jar my-jar-app.jar` 」のように入力します。 スタートアップ コマンドの値は、ポータルの [構成] > [全般設定] の下、または `STARTUP_COMMAND` という名前のアプリケーション設定を使用して設定できます。

### <a name="server-port"></a>サーバー ポート

App Service Linux では着信要求がポート 80 にルーティングされるため、アプリケーションもポート 80 でリッスンする必要があります。 これはアプリケーションの構成 (Spring の *application.properties* ファイルなど)、またはスタートアップ コマンド (たとえば、`java -jar spring-app.jar --server.port=80`) で行うことができます。 一般的な Java フレームワークについては、次のドキュメントを参照してください。

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>データ ソース

### <a name="tomcat"></a>Tomcat

これらの説明は、すべてのデータベース接続に適用されます。 プレースホルダーを、選択したデータベースのドライバーのクラス名と JAR ファイルに置き換える必要があります。 一般的なデータベースのクラス名とドライバーのダウンロードを含む表を次に示します。

| データベース   | ドライバーのクラス名                             | JDBC ドライバー                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [ダウンロード](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [ダウンロード](https://dev.mysql.com/downloads/connector/j/) ("プラットフォームに依存しない" を選択) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [ダウンロード](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Java Database Connectivity (JDBC) または Java Persistence API (JPA) を使用するように Tomcat を構成するには、まず、起動時に Tomcat によって読み込まれる `CATALINA_OPTS` 環境変数をカスタマイズします。 [App Service Maven プラグイン](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)のアプリ設定を使用して、次の値を設定します。

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

または、Azure portal の **[構成]**  >  **[アプリケーション設定]** ページで環境変数を設定します。

次に、データ ソースを Tomcat サーブレットで実行されている 1 つのアプリケーション、またはすべてのアプリケーションのどちらに対して使用可能にする必要があるかを判定します。

#### <a name="application-level-data-sources"></a>アプリケーション レベル データ ソース

1. プロジェクトの *META-INF/* ディレクトリ内に *context.xml* ファイルを作成します。 *META-INF/* ディレクトリが存在しない場合は作成します。

2. *context.xml* で、`Context` 要素を追加してデータ ソースを JNDI アドレスにリンクします。 `driverClassName` プレースホルダーを、上の表にあるドライバーのクラス名に置き換えます。

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

3. アプリケーションでこのデータ ソースを使用するよう、アプリケーションの *web.xml* を更新します。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>共有のサーバーレベル リソース

サーバーレベルの共有データ ソースを追加するには、Tomcat のserver.xml を編集する必要があります。 まず、[スタートアップ スクリプト](faq-app-service-linux.md#built-in-images)をアップロードし、 **[構成]**  >  **[スタートアップ コマンド]** でスクリプトへのパスを設定します。 [FTP](deploy-ftp.md) を使用してスタートアップ スクリプトをアップロードできます。

スタートアップ スクリプトによって、server.xml ファイルへの [xsl 変換](https://www.w3schools.com/xml/xsl_intro.asp)が作成され、結果の xml ファイルが `/usr/local/tomcat/conf/server.xml` に出力されます。 スタートアップ スクリプトでは、apk を使用して libxslt をインストールする必要があります。 xsl ファイルとスタートアップ スクリプトは FTP 経由でアップロードできます。 以下にスタートアップ スクリプトの例を示します。

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

xsl ファイルの例は次のとおりです。 この xsl ファイルの例では、新しいコネクタ ノードが Tomcat server.xml に追加されます。

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>構成を完了する

最後に、ドライバーの JAR を Tomcat クラスパス内に配置し、App Service を再起動します。

1. JDBC ドライバー ファイルが Tomcat クラスローダーで確実に使用できるように、これらのファイルを */home/tomcat/lib* ディレクトリに配置します。 (このディレクトリがまだ存在しない場合は作成します。)これらのファイルを App Service インスタンスにアップロードするには、次の手順を行います。

    1. [Cloud Shell](https://shell.azure.com) で、Web アプリ拡張機能をインストールします。

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. 次の CLI コマンドを実行して、ローカル システムから App Service への SSH トンネルを作成します。

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. SFTP クライアントを使用してローカルのトンネル ポートに接続し、ファイルを */home/tomcat/lib* フォルダーにアップロードします。

    あるいは、FTP クライアントを使用して JDBC ドライバーをアップロードできます。 [FTP 資格情報を取得するための手順](deploy-configure-credentials.md)に従ってください。

2. サーバー レベルのデータ ソースを作成した場合は、App Service Linux アプリケーションを再起動します。 Tomcat が `CATALINA_BASE` を `/home/tomcat` にリセットし、更新された構成を使用します。

### <a name="spring-boot"></a>Spring Boot

Spring Boot アプリケーション内のデータ ソースに接続するには、接続文字列を作成し、それらを *application.properties* ファイルに挿入することをお勧めします。

1. App Service ページの [構成] セクションに文字列の名前を設定し、JDBC 接続文字列を値フィールドに貼り付けて、タイプを [カスタム] に設定します。 必要に応じて、この接続文字列をスロット設定として設定することができます。

    この接続文字列は、`CUSTOMCONNSTR_<your-string-name>` という名前の環境変数としてアプリケーションにアクセスできます。 たとえば、先ほど作成した接続文字列の名前は `CUSTOMCONNSTR_exampledb` になります。

2. *application.properties* ファイルで、環境変数名を使用してこの接続文字列を参照します。 たとえば、例では次を使用します。

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

このトピックの詳細については、[データ アクセス](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html)と[外部化された構成](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)に関する Spring Boot の資料を参照してください。

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Tomcat を使用してセッション キャッシュとして Redis を使用する

[Azure Cache for Redis](../azure-cache-for-redis/index.yml) などの外部セッション ストアを使用するように Tomcat を構成できます。 これにより、自動スケール、再起動、フェールオーバーが発生した場合など、ユーザーがアプリの別のインスタンスに転送されるときに、ユーザーのセッション状態 (ショッピング カート データなど) を保持できます。

Redis で Tomcat を使用するには、[PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) の実装を使用するようにアプリを構成する必要があります。 次の手順では、例として [Pivotal Session Manager: redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) を使用してこのプロセスを説明します。

1. Bash ターミナルを開き、`<variable>=<value>` を使用して次の各環境変数を設定します。

    | 変数                 | 値                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | App Service インスタンスを含むリソース グループの名前。       |
    | WEBAPP_NAME              | App Service インスタンスの名前。                                     |
    | WEBAPP_PLAN_NAME         | App Service プランの名前。                                         |
    | リージョン                   | アプリがホストされているリージョンの名前。                           |
    | REDIS_CACHE_NAME         | Azure Cache for Redis インスタンスの名前。                           |
    | REDIS_PORT               | Redis キャッシュがリッスンする SSL ポート。                             |
    | REDIS_PASSWORD           | インスタンスのプライマリ アクセス キー。                                  |
    | REDIS_SESSION_KEY_PREFIX | アプリから取得されたセッション キーを識別するために指定する値。 |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=<App Service plan>
    REGION=<region>
    REDIS_CACHE_NAME=<cache>
    REDIS_PORT=<port>
    REDIS_PASSWORD=<access key>
    REDIS_SESSION_KEY_PREFIX=<prefix>
    ```

    Azure portal で サービス インスタンスの **[プロパティ]** セクションまたは **[アクセス キー]** セクションを探して、名前、ポート、およびアクセス キーの情報を確認できます。

2. 次の内容を使用して、アプリの *src/main/webapp/META-INF/context.xml* ファイルを作成または更新します。

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    このファイルで、アプリのセッション マネージャーの実装を指定して構成します。 前の手順で設定した環境変数を使用して、ソース ファイルからアカウント情報を保持します。

3. FTP を使用して、セッション マネージャーの JAR ファイルを App Service インスタンスにアップロードし、 */home/tomcat/lib* ディレクトリに配置します。 詳しくは、「[FTP/S を使用した Azure App Service へのアプリのデプロイ](./deploy-ftp.md)」をご覧ください。

4. App Service インスタンスの[セッション アフィニティ Cookie](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) を無効にします。 Azure portal からこれを実行するには、アプリに移動し、 **[構成] > [全般設定] > [ARR アフィニティ]** を **[オフ]** に設定します。 または、次のコマンドを使用できます。

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    App Service の既定では、セッション アフィニティ Cookie を使用して、既存のセッションによるクライアント要求がアプリケーションの同じインスタンスにルーティングされるようにしています。 この既定の動作の構成は必須ではありませんが、アプリ インスタンスが再起動されたときや、トラフィックが別のインスタンスに再ルーティングされたときに、ユーザーのセッション状態が保持されません。 [既存の ARR インスタンス アフィニティ構成を無効にして](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)セッション Cookie ベースのルーティングをオフにする場合は、操作なしで構成済みのセッション ストアが動作するようにします。

5. App Service インスタンスの **[プロパティ]** セクションに移動し、 **[追加の送信 IP アドレス]** を見つけます。 これらは、アプリに使用できるすべての送信 IP アドレスを表します。 次の手順で使用するためにコピーします。

6. 各 IP アドレスについて、Azure Cache for Redis インスタンスにファイアウォール規則を作成します。 この操作は、Azure portal の Redis インスタンスの **[ファイアウォール]** セクションから実行できます。 各規則に一意の名前を指定し、 **[開始 IP アドレス]** と **[終了 IP アドレス]** の値を同じ IP アドレスに設定します。

7. Redis インスタンスの **[詳細設定]** セクションに移動し、 **[SSL によるアクセスのみ許可する]** を **[いいえ]** に設定します。 これで、App Service インスタンスは Azure インフラストラクチャを介して Redis キャッシュと通信できるようになります。

8. Redis アカウント情報を参照するように、アプリの *pom.xml* ファイルの `azure-webapp-maven-plugin` の構成を更新します。 このファイルには、以前に設定した環境変数を使用して、ソース ファイルのアカウント情報を保持します。

    必要に応じて、`1.9.1` を [Azure App Service 用 Maven プラグイン](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)の最新バージョンに変更します。

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.1</version>
        <configuration>            
            <!-- Web App information -->
            <schemaVersion>v2</schemaVersion>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>            
            <runtime>
                <os>linux</os>
                <javaVersion>jre8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. アプリをリビルドして再デプロイします。

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

これで、アプリのセッション管理に Redis キャッシュが使用されるようになります。

これらの手順をテストするために使用できるサンプルについては、GitHub の [scaling-stateful-java-web-app-on-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) リポジトリを参照してください。

## <a name="docker-containers"></a>Docker コンテナー

Azure がサポートしている Zulu JDK をコンテナーで使用するには、[サポートされている Azul Zulu Enterprise for Azure のダウンロード ページ](https://www.azul.com/downloads/azure-only/zulu/)に記載されている事前構築済みのイメージをプルして使用するか、[Microsoft Java GitHub リポジトリ](https://github.com/Microsoft/java/tree/master/docker)にある `Dockerfile` の例を使用するようにします。

## <a name="statement-of-support"></a>サポートのステートメント

### <a name="runtime-availability"></a>ランタイムの可用性

App Service for Linux では、Java Web アプリケーションのマネージド ホスティング用に次の 2 つのランタイムがサポートされています。

- Web アーカイブ (WAR) ファイルとしてパッケージ化されたアプリケーションを実行するための [Tomcat サーブレット コンテナー](https://tomcat.apache.org/)。 サポートされているバージョンは 8.5 と 9.0 です。
- Java アーカイブ (JAR) ファイルとしてパッケージ化されたアプリケーションを実行するための Java SE ランタイム環境。 サポートされているバージョンは Java 8 と 11 です。

### <a name="jdk-versions-and-maintenance"></a>JDK のバージョンとメンテナンス

OpenJDK の Azul Zulu Enterprise ビルドは、Microsoft および Azul Systems でサポートされる、Azure と Azure Stack 用の OpenJDK の無料でマルチプラット フォーム対応の実稼働可能なディストリビューションです。 これらには、Java SE アプリケーションを構築および実行するためのすべてのコンポーネントが含まれています。 [Java JDK のインストール](https://aka.ms/azure-jdks)に関するページから JDK をインストールできます。

サポートされている JDK は、毎年 1 月、4 月、7 月、および 10 月の四半期ごとに自動的に適用されます。

### <a name="security-updates"></a>セキュリティ更新プログラム

重大なセキュリティの脆弱性のパッチおよび修正プログラムは、Azul Systems から利用可能になり次第リリースされます。 "重大な" 脆弱性は、[NIST Common Vulnerability Scoring System バージョン 2](https://nvd.nist.gov/cvss.cfm) で 9.0 以上の基本スコアにより定義されます。

### <a name="deprecation-and-retirement"></a>廃止と提供終了

サポートされている Java ランタイムが廃止予定の場合、影響を受けるランタイムを使用している Azure の開発者には、ランタイム終了の少なくとも 6 か月前までに廃止の通知が送信されます。

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>次のステップ

[Java 開発者向けの Azure](/java/azure/) センターにアクセスして、Azure クイック スタート、チュートリアル、および Java リファレンス ドキュメントを入手してください。

Java 開発に限られない、App Service for Linux の使用に関する一般的な質問については、[App Service Linux の FAQ](faq-app-service-linux.md) で回答されています。

::: zone-end