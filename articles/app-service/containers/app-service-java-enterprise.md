---
title: Linux での Java Enterprise のサポート - Azure App Service | Microsoft Docs
description: Azure App Service on Linux で Wildfly を使用して Java Enterprise アプリをデプロイするための開発者ガイド。
keywords: Azure App Service, Web アプリ, Linux, OSS, Java, Wildfly, Enterprise, java ee, jee, javaee
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 8db65fd9a1f271aea4ceb345f4d9dfbb6b9ff8a6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877382"
---
# <a name="java-enterprise-guide-for-app-service-on-linux"></a>App Service on Linux の Java Enterprise ガイド

> [!NOTE] 
> App Service Linux 上の Java Enterprise エディションは現在プレビュー段階です。 このスタックを運用環境向けの作業に使用することは**お勧めできません**。 Java SE および Tomcat スタックについては、[Java 開発者ガイド](app-service-linux-java.md)を参照してください。

Azure App Service on Linux を使用すると、Java 開発者は、完全に管理された Linux ベースのサービス上で Java Enterprise (Java EE) アプリケーションをビルド、デプロイ、およびスケーリングすることができます。  基になる Java Enterprise ランタイム環境は、オープン ソース [Wildfly](https://wildfly.org/) アプリケーション サーバーです。

このガイドでは、App Service for Linux で使用する Java Enterprise 開発者向けに主要な概念と手順を示します。 Azure App Service for Linux で Java アプリケーションをデプロイしたことがない場合は、最初に [Java クイック スタート](quickstart-java.md)を完了する必要があります。 Java Enterprise に限らない App Service for Linux に関する質問については、[Java 開発者ガイド](app-service-linux-java.md)および [App Service Linux の FAQ](app-service-linux-faq.md) で回答されています。

## <a name="scale-with-app-service"></a>App Service によるスケーリング 

App Service on Linux で実行されている WildFly アプリケーション サーバーは、ドメイン構成ではなく、スタンドアロン モードで実行されます。 App Service プランをスケールアウトすると、各 WildFly インスタンスがスタンドアロン サーバーとして構成されます。

 [スケール ルール](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started?toc=%2Fazure%2Fapp-service%2Fcontainers%2Ftoc.json)および[インスタンス数の増加](https://docs.microsoft.com/azure/app-service/web-sites-scale?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)によって、アプリケーションを垂直方向または水平方向にスケーリングします。 

## <a name="customize-application-server-configuration"></a>アプリケーション サーバー構成のカスタマイズ

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

App Service インスタンスの `/home/site/deployments/tools` にスタートアップ スクリプトをアップロードします。 FTP 資格情報の取得の手順については、[このドキュメント](/azure/app-service/deploy-configure-credentials#userscope)を参照してください。 

Azure portal の **[Startup Script]\(スタートアップ スクリプト\)** フィールドに、スタートアップ シェル スクリプトの場所 (`/home/site/deployments/tools/your-startup-script.sh` など) を設定します。

スクリプトで使用する環境変数を渡すため、アプリケーションの構成で[アプリケーション設定](/azure/app-service/web-sites-configure#application-settings)を指定します。 アプリケーション設定では、バージョン コントロールされていないアプリケーションを構成するために必要な接続文字列と他のシークレットを保持します。

## <a name="modules-and-dependencies"></a>モジュールと依存関係

JBoss CLI を使用して、Wildfly クラスパスにモジュールとそれらの依存関係をインストールするには、それぞれ独自のディレクトリに次のファイルを作成する必要があります。  一部のモジュールと依存関係には、JNDI の名前付けやその他の API 固有の構成などの追加の構成が必要になることがあるため、このリストは、ほとんどの場合で依存関係を構成するために必要な最小限のセットです。

- [XML モジュール記述子](https://jboss-modules.github.io/jboss-modules/manual/#descriptors)。 この XML ファイルには、名前、属性、およびモジュールの依存関係を定義します。 この [module.xml ファイルの例](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource)では、Postgres モジュール、その JAR ファイル JDBC 依存関係、およびその他の必要なモジュール依存関係を定義しています。
- モジュールに必要なすべての JAR ファイル依存関係。
- 新しいモジュールを構成するための JBoss CLI コマンドを含むスクリプト。 このファイルには、JBoss CLI によって実行され、依存関係を使用するようにサーバーを構成するためのコマンドが含まれます。 モジュール、データ ソース、およびメッセージング プロバイダーを追加するコマンドについては、[このドキュメント](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)を参照してください。
-  JBoss CLI を呼び出し、前の手順のスクリプトを実行する Bash スタートアップ スクリプト。 このファイルは、App Service インスタンスが再起動されるときか、またはスケールアウト時に新しいインスタンスがプロビジョニングされるときに実行されます。このスタートアップ スクリプトでは、JBoss コマンドが JBoss CLI に渡されるため、アプリケーションの他の任意の構成を実行できます。 少なくとも、このファイルには、JBoss CLI スクリプトコマンドを JBoss CLI に渡す 1 つのコマンドを指定できます。 
   
```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli` 
``` 

モジュールのファイルと内容を用意したら、次の手順に従って、Wildfly アプリケーション サーバーにモジュールを追加します。 

1. ファイルを App Service インスタンスの `/home/site/deployments/tools` に FTP で送信します。 FTP 資格情報の取得の手順については、このドキュメントを参照してください。 
2. Azure portal の [アプリケーション設定] ブレードで、[Startup Script]\(スタートアップ スクリプト\) フィールドにスタートアップ シェル スクリプトの場所 (例: `/home/site/deployments/tools/your-startup-script.sh`) を設定します。
3. portal の **[概要]** セクションの **[再起動]** ボタンを押すか、または Azure CLI を使用して、App Service インスタンスを再起動します。

## <a name="data-sources"></a>データ ソース

データ ソース接続用に Wildfly を構成するには、先述のモジュールのインストールと依存関係のセクションで説明した同じ手順に従います。 任意の Azure データベース サービスについても同じ手順に従うことができます。

1. データベースの種類に応じた JDBC ドライバーをダウンロードします。 便宜上、ここでは [Postgres](https://jdbc.postgresql.org/download.html) と [MySQL](https://dev.mysql.com/downloads/connector/j/) 用のドライバーとします。 ダウンロードをアンパックし、.Jar ファイルを取得します。
2. 「モジュールと依存関係」で説明した手順に従って、XML モジュール記述子、JBoss CLI スクリプト、スタートアップ スクリプト、および JDBC .jar 依存関係を作成してアップロードします。


[PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7)、[MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)、および [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) を使用した Wildfly の構成の詳細を参照できます。 先述の汎用化されたアプローチと共にカスタマイズされた手順を使用して、データ ソース定義をサーバーに追加します。

## <a name="messaging-providers"></a>メッセージング プロバイダー

メッセージング メカニズムとして Service Bus を使用してメッセージ ドリブン Bean を有効にするには:

1. [Apache QPId JMS メッセージング ライブラリ](https://qpid.apache.org/proton/index.html)を使用します。 アプリケーションの pom.xml (またはその他のビルド ファイル) にこの依存関係を含めます。

2.  [Service Bus リソース](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)を作成します。 Azure Service Bus 名前空間とその名前空間内のキューおよび送受信機能を含む共有アクセス ポリシーを作成します。

3. ポリシーの主キーを URL エンコードするか、または [Service Bus SDK を使用](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory)して、共有アクセス ポリシー キーをコードに渡します。

4. モジュール XML 記述子、.jar 依存関係、JBoss CLI コマンド、および JMS プロバイダーのスタートアップ スクリプトを使用して、モジュールと依存関係のインストールのセクションで説明されている手順に従います。 4 つのファイルに加えて、JMS キューおよびトピックの JNDI 名を定義する XML ファイルを作成する必要もあります。 参照構成ファイルについては、[このリポジトリ](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig)を参照してください。


## <a name="configure-session-management-caching"></a>セッション管理キャッシュの構成

既定で App Service on Linux では、セッション アフィニティ Cookie を使用して、既存のセッションによるクライアント要求がアプリケーションの同じインスタンスにルーティングされるようにしています。 この既定の動作には、構成は必要ありませんが、いくつかの制限があります。

- アプリケーション インスタンスが再起動されるか、またはスケール ダウンされると、アプリケーション サーバーのユーザー セッションの状態が失われます。
- アプリケーションに長いセッション タイムアウト設定または固定数のユーザーがある場合、新しいセッションだけが新しく起動されたインスタンスにルーティングされるため、自動スケーリングされた新しいインスタンスが、負荷を受け取るまでにしばらく時間がかかることがあります。

[Azure Cache for Redis](/azure/azure-cache-for-redis/) などの外部セッション ストアを使用するように Wildfly を構成できます。 [既存の ARR インスタンス アフィニティ構成を無効にして](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)、セッション Cookie ベースのルーティングをオフにし、構成済みの Wildfly セッション ストアが干渉せずに動作できるようにする必要があります。

## <a name="enable-web-sockets"></a>Web ソケットの有効化

既定で、Web ソケットは App Service で有効になります。 アプリケーションで Web ソケットの使用を開始するには、[このクイック スタート](https://github.com/wildfly/quickstart/tree/master/websocket-hello)を参照してください。

## <a name="logs-and-troubleshooting"></a>ログとトラブルシューティング

App Service には、アプリケーションの問題のトラブルシューティングに役立つツールが用意されています。

-   左側のナビゲーション ウィンドウの **[診断ログ]** をクリックして、ログを有効にします。 **[ファイル システム]** をクリックして、ストレージ クォータおよび保持期間を設定し、変更を保存します。 これらのログは、`/home/LogFiles/` にあります。
-   [SSH を使用して、アプリケーション インスタンスに接続し](app-service-linux-ssh-support.md)、実行中のアプリケーションのログを表示します。
-   portal の **[診断ログ]** パネルの診断ログまたは Azure CLI コマンド `az webapp log tail --name <your-app-name> --resource-group <your-apps-resource-group>` を使用して、診断ログを確認します。
