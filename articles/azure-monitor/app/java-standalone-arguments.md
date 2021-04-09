---
title: JVM 引数の追加 - Azure Monitor Application Insights for Java
description: Azure Monitor Application Insights for Java を有効にする JVM 引数を追加する方法
ms.topic: conceptual
ms.date: 04/16/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: bd6ed2e5c848b39ad946209d5ea92fcf6ce3b438
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625366"
---
# <a name="adding-the-jvm-arg---azure-monitor-application-insights-for-java"></a>JVM 引数の追加 - Azure Monitor Application Insights for Java



## <a name="azure-environments"></a>Azure 環境

[App Services](../../app-service/configure-language-java.md#set-java-runtime-options) を構成します。

## <a name="spring-boot"></a>Spring Boot

JVM 引数 `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` を `-jar` より前の任意の位置に追加します。次に例を示します。

```
java -javaagent:path/to/applicationinsights-agent-3.0.2.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Docker エントリ ポイントを使用した Spring Boot

*exec* フォームを使用している場合は、パラメーター `"-javaagent:path/to/applicationinsights-agent-3.0.2.jar"` を、パラメーターリストの `"-jar"` パラメーターより前の位置に追加します。次に例を示します。

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.2.jar", "-jar", "<myapp.jar>"]
```

*shell* フォームを使用している場合は、JVM 引数 `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` を `-jar` より前の位置に追加します。次に例を示します。

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.2.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat を `apt-get` または`yum` を使用してインストールした場合

`apt-get` または `yum` を使用して Tomcat をインストールした場合は、ファイル `/etc/tomcat8/tomcat8.conf` が必要です。  そのファイルの末尾に次の行を追加します。

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.2.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat をダウンロードし、解凍してインストールした場合

[https://tomcat.apache.org](https://tomcat.apache.org) から Tomcat をダウンロードし、解凍してインストールした場合は、ファイル `<tomcat>/bin/catalina.sh` が必要です。  `<tomcat>/bin/setenv.sh` という名前で、同じディレクトリに新しいファイルを作成して、次の内容を設定します。

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.2.jar"
```

ファイル `<tomcat>/bin/setenv.sh` が既に存在する場合は、そのファイルを変更し、`CATALINA_OPTS` に `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` を追加します。


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Tomcat をコマンド ラインから実行する

`<tomcat>/bin/catalina.bat` ファイルを見つけます。  `<tomcat>/bin/setenv.bat` という名前で、同じディレクトリに新しいファイルを作成して、次の内容を設定します。

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.2.jar
```

引用符は必須ではありませんが、含める場合の適切な位置は次のようになります。

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.2.jar"
```

ファイル `<tomcat>/bin/setenv.bat` が既に存在する場合は、そのファイルを変更し、`CATALINA_OPTS` に `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` を追加するだけです。

### <a name="running-tomcat-as-a-windows-service"></a>Tomcat を Windows サービスとして実行する

`<tomcat>/bin/tomcat8w.exe` ファイルを見つけます。  その実行可能ファイルを実行し、[`Java`] タブの [`Java Options`] に `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` を追加します。


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>スタンドアロン サーバー

ファイル `JBOSS_HOME/bin/standalone.conf` (Linux) または `JBOSS_HOME/bin/standalone.conf.bat` (Windows) の既存の `JAVA_OPTS` 環境変数に `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` を追加します。

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.2.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>ドメイン サーバー

`JBOSS_HOME/domain/configuration/host.xml` の既存の `jvm-options` に `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` を追加します。

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.2.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

複数のマネージド サーバーを 1 つのホストで実行している場合は、各 `server` の `system-properties` に `applicationinsights.agent.id` を追加する必要があります。

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

指定された `applicationinsights.agent.id` 値は一意である必要があります。 各 JVM プロセスには独自のローカルの applicationinsights 構成およびローカルの applicationinsights ログ ファイルが必要であるため、applicationinsights ディレクトリの下にサブディレクトリを作成するのに使用されます。 また、中央のコレクターにレポートする場合、`applicationinsights.properties` ファイルは複数のマネージド サーバーによって共有されるため、指定された `applicationinsights.agent.id` は、その共有ファイルの `agent.id` 設定をオーバーライドするために必要です。 `applicationinsights.agent.rollup.id` は、マネージド サーバーごとに `agent.rollup.id` 設定をオーバーライドする必要がある場合に、サーバーの `system-properties` でも同様に指定できます。


## <a name="jetty-9"></a>Jetty 9

次の行を `start.ini` に追加します

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.2.jar
```


## <a name="payara-5"></a>Payara 5

`glassfish/domains/domain1/config/domain.xml` の既存の `jvm-options` に `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` を追加します。

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.2.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

管理コンソールを開き、 **[サーバー] > [WebSphere アプリケーション サーバー] > [アプリケーションサーバー]** に移動し、適切なアプリケーション サーバーを選択して以下をクリックします。 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
"汎用 JVM 引数" に、以下を追加します。
```
-javaagent:path/to/applicationinsights-agent-3.0.2.jar
```
その後、アプリケーション サーバーを保存して再起動します。


## <a name="openliberty-18"></a>OpenLiberty 18

サーバー ディレクトリに新しいファイル `jvm.options` (例: `<openliberty>/usr/servers/defaultServer`) を作成し、次の行を追加します。
```
-javaagent:path/to/applicationinsights-agent-3.0.2.jar
```
