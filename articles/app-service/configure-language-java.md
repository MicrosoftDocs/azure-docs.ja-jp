---
title: Java アプリを構成する
description: Azure App Service で実行するように Java アプリを構成する方法について説明します。 この記事では、最も一般的な構成タスクを紹介しています。
keywords: Azure App Service, Web アプリ, Windows, OSS, Java, Tomcat, JBoss
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: 0334e259f75440cae25f1e165c0621c85f7c7705
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97804005"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Azure App Service 向けの Java アプリを構成する

Java 開発者は、Azure App Service を使用することで、フル マネージド サービス上で Java SE、Tomcat、JBoss EAP の Web アプリケーションを迅速にビルド、デプロイ、スケーリングすることができます。 アプリケーションのデプロイは、Maven プラグインを使用して、コマンド ラインから、または IntelliJ、Eclipse、Visual Studio Code などのエディターで行います。

このガイドでは、App Service を使用する Java 開発者向けに主要な概念と手順を示します。 Azure App Service を使用したことがない場合は、まず [Java クイック スタート](quickstart-java.md)をお読みください。 Java 開発に限られない、App Service の使用に関する一般的な質問については、[App Service の FAQ](faq-configuration-and-management.md) に関する記事で回答されています。

## <a name="deploying-your-app"></a>アプリのデプロイ

[Maven 用 Azure Web アプリ プラグイン](https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)を使用して、.war ファイルまたは .jar ファイルをデプロイできます。 一般的な IDE を使用したデプロイは、[Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) または [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse) でもサポートされています。

それ以外の場合、デプロイの方法はアーカイブの種類によって異なります。

### <a name="java-se"></a>Java SE

Java SE に .jar ファイルをデプロイするには、Kudu サイトの `/api/zipdeploy/` エンドポイントを使用します。 この API の詳細については、[このドキュメント](./deploy-zip.md#rest)を参照してください。

### <a name="tomcat"></a>Tomcat

.war ファイルを Tomcat にデプロイするには、`/api/wardeploy/` エンドポイントを使用してアーカイブ ファイルを POST します。 この API の詳細については、[このドキュメント](./deploy-zip.md#deploy-war-file)を参照してください。

::: zone pivot="platform-linux"

### <a name="jboss-eap"></a>JBoss EAP

.war ファイルを JBoss にデプロイするには、`/api/wardeploy/` エンドポイントを使用してアーカイブ ファイルを POST します。 この API の詳細については、[このドキュメント](./deploy-zip.md#deploy-war-file)を参照してください。

.ear ファイルをデプロイするには、[FTP を使用](deploy-ftp.md)します。

::: zone-end

FTP を使用して .war や .jar をデプロイしないでください。 FTP ツールは、スタートアップ スクリプト、依存関係、またはその他のランタイム ファイルをアップロードするために設計されています。 これは、Web アプリをデプロイするための最適な選択肢ではありません。

## <a name="logging-and-debugging-apps"></a>アプリのログ記録とデバッグ

パフォーマンス レポート、トラフィックの視覚エフェクト、および正常性検査は、Azure portal を介して各アプリに対して使用できます。 詳細については、「[Azure App Service 診断の概要](overview-diagnostics.md)」を参照してください。

### <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

詳細については、[Cloud Shell でのログのストリーミング](troubleshoot-diagnostic-logs.md#in-cloud-shell)に関する記事をご覧ください。

::: zone pivot="platform-linux"

### <a name="ssh-console-access"></a>SSH コンソール アクセス

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="troubleshooting-tools"></a>トラブルシューティング ツール

組み込みの Java イメージは [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) オペレーティング システムに基づいています。 `apk` パッケージ マネージャーを使用し、トラブルシューティングのツールやコマンドをインストールします。

::: zone-end

### <a name="flight-recorder"></a>Flight Recorder

Azul JVM を使用する App Service のすべての Java ランタイムには、Zulu Flight Recorder が付属しています。 これを使用すると、JVM、システム、アプリケーションのイベントを記録し、Java アプリケーションでの問題のトラブルシューティングを行うことができます。

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>時間指定の記録

時間を指定して記録を行うには、Java アプリケーションの PID (プロセス ID) が必要です。 PID を検索するには、ブラウザーで Web アプリの SCM サイト (https://<your-site-name>.scm.azurewebsites.net/ProcessExplorer/) を開きます。 このページには、Web アプリで実行中のプロセスが表示されます。 テーブル内で "java" という名前のプロセスを探し、対応する PID (プロセス ID) をコピーします。

次に、SCM サイト上部のツール バーにある **デバッグ コンソール** を開き、次のコマンドを実行します。 `<pid>` を、先ほどコピーしたプロセス ID に置き換えます。 このコマンドにより、Java アプリケーションのプロファイラーによる 30 秒の記録が開始され、`D:\home` ディレクトリに `timed_recording_example.jfr` という名前のファイルが生成されます。

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

対象の App Service に SSH で接続し、`jcmd` コマンドを実行して、実行されているすべての Java プロセスの一覧を表示します。 jcmd 自体に加えて、実行されているご自分の Java アプリケーションとプロセス ID 番号 (pid) が表示されます。

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

::: zone-end

#### <a name="analyze-jfr-files"></a>`.jfr` ファイルの分析

JFR ファイルをご自分のローカル コンピューターにダウンロードするには、[FTPS](deploy-ftp.md) を使います。 JFR ファイルを分析するには、[Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/) をダウンロードしてインストールします。 Zulu Mission Control については、[Azul のドキュメント](https://docs.azul.com/zmc/)と[インストール手順](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)をご覧ください。

### <a name="app-logging"></a>アプリのログ記録

::: zone pivot="platform-windows"

Azure portal または [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) を使用して[アプリケーションのログ記録](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)を有効にし、アプリケーションの標準コンソール出力および標準コンソール エラー ストリームをローカル ファイル システムまたは Azure BLOB ストレージに書き込むよう App Service を構成します。 App Service のローカル ファイル システム インスタンスへのログ記録は、構成されてから 12 時間後に無効になります。 リテンション期間を長くする必要がある場合は、BLOB ストレージ コンテナーに出力を書き込むようアプリケーションを構成します。 Java と Tomcat のアプリ ログは */home/LogFiles/Application/* ディレクトリにあります。

::: zone-end
::: zone pivot="platform-linux"

Azure portal または [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) を使用して[アプリケーションのログ記録](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer)を有効にし、アプリケーションの標準コンソール出力および標準コンソール エラー ストリームをローカル ファイル システムまたは Azure BLOB ストレージに書き込むよう App Service を構成します。 リテンション期間を長くする必要がある場合は、BLOB ストレージ コンテナーに出力を書き込むようアプリケーションを構成します。 Java と Tomcat のアプリ ログは */home/LogFiles/Application/* ディレクトリにあります。

Linux ベースの App Services の Azure Blob Storage のログ記録は [Azure Monitor (プレビュー) を使用してのみ構成できます](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) 

::: zone-end

アプリケーションで [Logback](https://logback.qos.ch/) または [Log4j](https://logging.apache.org/log4j) をトレースに使用している場合は、「[Application Insights を使用した Java トレース ログの探索](../azure-monitor/app/java-trace-logs.md)」にあるログ記録フレームワークの構成手順に従って、これらのトレースを確認のために Azure Application Insights に転送することができます。

## <a name="customization-and-tuning"></a>カスタマイズとチューニング

Azure App Service for Linux では、Azure portal および CLI を使用したチューニングとカスタマイズが追加設定なしでサポートされています。 Java 以外の特定の Web アプリの構成については、次の記事を確認してください。

- [アプリケーションの設定の構成](configure-common.md#configure-app-settings)
- [カスタム ドメインの設定](app-service-web-tutorial-custom-domain.md)
- [SSL バインドの構成](configure-ssl-bindings.md)
- [CDN の追加](../cdn/cdn-add-to-web-app.md)
- [Kudu サイトを構成する](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)


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

#### <a name="java-se"></a>Java SE

Spring Boot 開発者は、[Azure Active Directory Spring Boot スターター](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory)を使用して、使い慣れた Spring Security の注釈と API を使用してアプリケーションをセキュリティで保護することができます。 *application.properties* ファイルで最大ヘッダー サイズを大きくしてください。 `16384` の値をお勧めします。

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

「[Azure App Service で SSL バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)」の手順に従って、既存の SSL 証明書をアップロードし、アプリケーションのドメイン名にバインドします。 既定では、アプリケーションで引き続き HTTP 接続が許可されます。チュートリアルの特定の手順に従って、SSL と TLS を適用します。

### <a name="use-keyvault-references"></a>KeyVault 参照を使用する

[Azure Key Vault](../key-vault/general/overview.md) では、アクセス ポリシーと監査履歴を使用した一元的なシークレット管理を提供しています。 シークレット (パスワードや接続文字列など) を KeyVault に格納し、環境変数を使用してアプリケーション内でこれらのシークレットにアクセスすることができます。

最初に、[Key Vault へのアクセス権をアプリに付与](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)したり、[アプリケーション設定で自分のシークレットに対する KeyVault 参照を設定](app-service-key-vault-references.md#reference-syntax)したりするための手順に従います。 App Service のターミナルにリモートでアクセスしている間に環境変数を出力することで、シークレットへの参照が解決されることを確認できます。

Spring または Tomcat 構成ファイルにこれらのシークレットを挿入するには、環境変数の挿入構文 (`${MY_ENV_VAR}`) を使用します。 Spring の構成ファイルについては、[外部化された構成](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)に関するこちらのドキュメントを参照してください。

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>Java キー ストアを使用する

既定では、[App Service Linux にアップロードされた](configure-ssl-certificate.md)パブリック証明書またはプライベート証明書は、コンテナーの起動時にそれぞれの Java キー ストアに読み込まれます。 証明書のアップロード後、証明書が Java キー ストアに読み込まれるようにするために、App Service を再起動する必要があります。 パブリック証明書は `$JAVA_HOME/jre/lib/security/cacerts` のキー ストアに読み込まれ、プライベート証明書は `$JAVA_HOME/lib/security/client.jks` に格納されます。

Java キー ストアの証明書を使用して JDBC 接続を暗号化するために、追加の構成が必要になる場合があります。 選択した JDBC ドライバーのドキュメントを参照してください。

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>Java キー ストアを初期化する

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

::: zone-end

## <a name="configure-apm-platforms"></a>APM プラットフォームを構成する

このセクションでは、NewRelic と AppDynamics アプリケーション パフォーマンスの監視 (APM) プラットフォームで、Linux 上の Azure App Service にデプロイされた Java アプリケーションを接続する方法を示します。

### <a name="configure-new-relic"></a>New Relic の構成

::: zone pivot="platform-windows"

1. [NewRelic.com](https://newrelic.com/signup) で NewRelic アカウントを作成します
2. NewRelic から Java エージェントをダウンロードします。ファイル名は *newrelic-java-x.x.x.zip* のようになります。
3. ライセンス キーをコピーします。これは後ほどエージェントの構成を行う際に必要です。
4. [App Service インスタンスに SSH で接続し](configure-linux-open-ssh-session.md)、新しいディレクトリ */home/site/wwwroot/apm* を作成します。
5. */home/site/wwwroot/apm* の下にあるディレクトリにアンパックされた NewRelic Java エージェント ファイルをアップロードします。 エージェント用のファイルは */home/site/wwwroot/apm/newrelic* 内にある必要があります。
6. */home/site/wwwroot/apm/newrelic/newrelic.yml* で YAML ファイルを変更し、プレース ホルダー ライセンスの値を独自のライセンス キーに置き換えます。
7. Azure portal で App Service でアプリケーションを参照し、新しいアプリケーション設定を作成します。

    - **Java SE** アプリの場合は、名前が `JAVA_OPTS` で値が `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` の環境変数を作成します。
    - **Tomcat** の場合は、名前が `CATALINA_OPTS` で値が `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` の環境変数を作成します。

::: zone-end
::: zone pivot="platform-linux"

1. [NewRelic.com](https://newrelic.com/signup) で NewRelic アカウントを作成します
2. NewRelic から Java エージェントをダウンロードします。ファイル名は *newrelic-java-x.x.x.zip* のようになります。
3. ライセンス キーをコピーします。これは後ほどエージェントの構成を行う際に必要です。
4. [App Service インスタンスに SSH で接続し](configure-linux-open-ssh-session.md)、新しいディレクトリ */home/site/wwwroot/apm* を作成します。
5. */home/site/wwwroot/apm* の下にあるディレクトリにアンパックされた NewRelic Java エージェント ファイルをアップロードします。 エージェント用のファイルは */home/site/wwwroot/apm/newrelic* 内にある必要があります。
6. */home/site/wwwroot/apm/newrelic/newrelic.yml* で YAML ファイルを変更し、プレース ホルダー ライセンスの値を独自のライセンス キーに置き換えます。
7. Azure portal で App Service でアプリケーションを参照し、新しいアプリケーション設定を作成します。
   
    - **Java SE** アプリの場合は、名前が `JAVA_OPTS` で値が `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` の環境変数を作成します。
    - **Tomcat** の場合は、名前が `CATALINA_OPTS` で値が `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` の環境変数を作成します。

::: zone-end

>  `JAVA_OPTS` または `CATALINA_OPTS` 用の環境変数がすでにある場合、`-javaagent:/...` のオプションを現在値の最期に追加します。

### <a name="configure-appdynamics"></a>AppDynamics の構成

::: zone pivot="platform-windows"

1. [AppDynamics.com](https://www.appdynamics.com/community/register/) で AppDynamics アカウントを作成します
2. AppDynamics の Web サイトから Java エージェントをダウンロードします。ファイル名は *AppServerAgent-x.x.x.xxxxx.zip* のようになります
3. [Kudu コンソール](https://github.com/projectkudu/kudu/wiki/Kudu-console)を使用して、新しいディレクトリ */home/site/wwwroot/apm* を作成します。
4. */home/site/wwwroot/apm* の下にあるディレクトリに Java エージェント ファイルをアップロードします。 エージェント用のファイルは */home/site/wwwroot/apm/appdynamics* 内にある必要があります。
5. Azure portal で App Service でアプリケーションを参照し、新しいアプリケーション設定を作成します。

   - **Java SE** アプリの場合は、名前が `JAVA_OPTS` で値が `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` の環境変数を作成します。`<app-name>` は App Service の名前です。
   - **Tomcat** アプリの場合は、名前が `CATALINA_OPTS` で値が `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` の環境変数を作成します。`<app-name>` は App Service の名前です。

::: zone-end
::: zone pivot="platform-linux"

1. [AppDynamics.com](https://www.appdynamics.com/community/register/) で AppDynamics アカウントを作成します
2. AppDynamics の Web サイトから Java エージェントをダウンロードします。ファイル名は *AppServerAgent-x.x.x.xxxxx.zip* のようになります
3. [App Service インスタンスに SSH で接続し](configure-linux-open-ssh-session.md)、新しいディレクトリ */home/site/wwwroot/apm* を作成します。
4. */home/site/wwwroot/apm* の下にあるディレクトリに Java エージェント ファイルをアップロードします。 エージェント用のファイルは */home/site/wwwroot/apm/appdynamics* 内にある必要があります。
5. Azure portal で App Service でアプリケーションを参照し、新しいアプリケーション設定を作成します。

   - **Java SE** アプリの場合は、名前が `JAVA_OPTS` で値が `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` の環境変数を作成します。`<app-name>` は App Service の名前です。
   - **Tomcat** アプリの場合は、名前が `CATALINA_OPTS` で値が `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` の環境変数を作成します。`<app-name>` は App Service の名前です。

::: zone-end

> [!NOTE]
>  `JAVA_OPTS` または `CATALINA_OPTS` 用の環境変数がすでにある場合、`-javaagent:/...` のオプションを現在値の最期に追加します。

## <a name="configure-data-sources"></a>データ ソースの構成

### <a name="java-se"></a>Java SE

Spring Boot アプリケーション内のデータ ソースに接続するには、接続文字列を作成し、それらを *application.properties* ファイルに挿入することをお勧めします。

1. App Service ページの [構成] セクションに文字列の名前を設定し、JDBC 接続文字列を値フィールドに貼り付けて、タイプを [カスタム] に設定します。 必要に応じて、この接続文字列をスロット設定として設定することができます。

    この接続文字列は、`CUSTOMCONNSTR_<your-string-name>` という名前の環境変数としてアプリケーションにアクセスできます。 たとえば、先ほど作成した接続文字列の名前は `CUSTOMCONNSTR_exampledb` になります。

2. *application.properties* ファイルで、環境変数名を使用してこの接続文字列を参照します。 たとえば、例では次を使用します。

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

このトピックの詳細については、[データ アクセス](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html)と[外部化された構成](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)に関する Spring Boot の資料を参照してください。

::: zone pivot="platform-windows"

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

---

::: zone-end
::: zone pivot="platform-linux"

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
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
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

### <a name="jboss-eap"></a>JBoss EAP

[JBoss EAP を使用してデータ ソースを登録する](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management)場合の 3 つの主要なステップは、JDBC ドライバーのアップロード、モジュールとしての JDBC ドライバーの追加、モジュールの登録です。 App Service はステートレスのホスティング サービスであるため、データ ソース モジュールを追加および登録するための構成コマンドは、スクリプト化して、コンテナーの起動時に適用する必要があります。

1. データベースの JDBC ドライバーを入手します。 
2. JDBC ドライバー用の XML モジュール定義ファイルを作成します。 PostgreSQL のモジュール定義の例を次に示します。

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.postgres">
        <resources>
        <!-- ***** IMPORTANT : REPLACE THIS PLACEHOLDER *******-->
        <resource-root path="/home/site/deployments/tools/postgresql-42.2.12.jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. JBoss CLI コマンドが含まれる `jboss-cli-commands.cli` という名前のファイルを作成します。 JBoss コマンドを使用して、モジュールを追加し、データ ソースとして登録する必要があります。 次の例では、PostgreSQL 用の JBoss CLI コマンドを示します。

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. JBoss CLI コマンドを呼び出すスタートアップ スクリプト `startup_script.sh` を作成します。 次の例は、`jboss-cli-commands.cli` を呼び出す方法を示したものです。 後で、コンテナーの起動時にこのスクリプトを実行するように App Service を構成します。 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. 任意の FTP クライアントを使用して、JDBC ドライバー、`jboss-cli-commands.cli`、`startup_script.sh`、およびモジュール定義を `/site/deployments/tools/` にアップロードします。
2. コンテナーの起動時に `startup_script.sh` を実行するようにサイトを構成します。 Azure portal で、 **[構成]**  >  **[一般設定]**  >  **[スタートアップ コマンド]** に移動します。 スタートアップ コマンド フィールドを `/home/site/deployments/tools/startup_script.sh` に設定します。 変更内容を **保存** します。

データソースが JBoss サーバーに追加されたことを確認するには、webapp に SSH で接続して、`$JBOSS_HOME/bin/jboss-cli.sh --connect` を実行します。 JBoss に接続したら、`/subsystem=datasources:read-resource` を実行してデータ ソースの一覧を表示します。

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>Java ランタイム バージョンの選択

App Service を使用すると、ユーザーは JVM のメジャー バージョン (Java 8 や Java 11 など) だけでなく、マイナー バージョン (1.8.0_232 や 11.0.5 など) も選択できます。 新しいマイナー バージョンが利用可能になったらマイナー バージョンを自動的に更新するように選択することもできます。 ほとんどの場合、運用サイトにおいては、固定されたマイナー JVM バージョンを使用する必要があります。 これにより、マイナー バージョンの自動更新の間に、予期しない停止が発生するのを防ぐことができます。

マイナー バージョンの固定を選択した場合は、サイトの JVM のマイナー バージョンを定期的に更新する必要があります。 アプリケーションが新しいマイナー バージョンで確実に実行されるようにするには、ステージング スロットを作成し、ステージング サイトでマイナー バージョンをインクリメントします。 新しいマイナー バージョンでアプリケーションが正しく実行されることを確認したら、ステージング スロットと運用スロットを入れ替えることができます。

## <a name="jboss-eap-hardware-options"></a>JBoss EAP のハードウェア オプション

JBoss EAP は、Premium および Isolated のハードウェア オプションでのみ使用できます。 パブリック プレビュー中に Free、Shared、Basic、または Standard レベルで JBoss EAP サイトを作成したお客様は、予期しない動作を避けるために、Premium または Isolated のハードウェア レベルにスケールアップする必要があります。

## <a name="java-runtime-statement-of-support"></a>Java ランタイムのサポート ステートメント

### <a name="jdk-versions-and-maintenance"></a>JDK のバージョンとメンテナンス

Azure でサポートされている Java Development Kit (JDK) は、[Azul Systems](https://www.azul.com/) で提供されている [Zulu](https://www.azul.com/downloads/azure-only/zulu/) です。 OpenJDK の Azul Zulu Enterprise ビルドは、Microsoft および Azul Systems でサポートされる、Azure と Azure Stack 用の OpenJDK の無料でマルチプラット フォーム対応の実稼働可能なディストリビューションです。 これらには、Java SE アプリケーションを構築および実行するためのすべてのコンポーネントが含まれています。 [Java JDK のインストール](/azure/developer/java/fundamentals/java-jdk-long-term-support)に関するページから JDK をインストールできます。

メジャー バージョンの更新プログラムは、Azure App Service の新しいランタイム オプションによって提供されます。 お客様は、App Service デプロイを構成することで Java のこれらの新しいバージョンに更新します。また、主要な更新プログラムをテストし、ニーズを満たしていることを確認する必要があります。

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

## <a name="next-steps"></a>次のステップ

[Java 開発者向けの Azure](/java/azure/) センターにアクセスして、Azure クイック スタート、チュートリアル、および Java リファレンス ドキュメントを入手してください。

Java 開発に限られない、App Service for Linux の使用に関する一般的な質問については、[App Service Linux の FAQ](faq-app-service-linux.md) で回答されています。
