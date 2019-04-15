---
title: Linux で Java APM および監視ツールを構成する - Azure App Service
description: App Service Linux 上で実行される Java アプリケーションのログおよびメトリックを NewRelic ならびに App Dynamics APM プロバイダーへ送信する方法について説明します
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 03/21/2019
ms.author: astay;routlaw
ms.custom: seodec18
ms.openlocfilehash: e6a22258266bda18c9ff79590d88e70d512f6c77
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630153"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>方法: Azure App Service on Linux 上の Java アプリでのアプリケーション パフォーマンス監視ツール

このアーティクルでは、NewRelic と AppDynamics アプリケーション パフォーマンスの監視 (APM) プラットフォームで Linux 上の Azure App Service に展開された、Java アプリケーションを接続する方法を示します。

## <a name="configure-new-relic"></a>New Relic の構成

1. [NewRelic.com](https://newrelic.com/signup) で NewRelic アカウントを作成します
2. NewRelic より Java エージェントをダウンロードします。ファイル名はおよそ `newrelic-java-x.x.x.zip` のようになります。
3. ライセンス キーをコピーします。これは後ほどエージェントの構成を行う際に必要です。
4. [App Service インスタンスに SSH で接続し、](/azure/app-service/containers/app-service-linux-ssh-support)新しいディレクトリを作成`/home/site/wwwroot/apm` します。
5. `/home/site/wwwroot/apm` の下にあるディレクトリにアンパックされた NewRelic Java エージェント ファイルをアップロードします。 エージェント用のファイルは `/home/site/wwwroot/apm/newrelic` 内にある必要があります。
6. `/home/site/wwwroot/apm/newrelic/newrelic.yml` で YAML ファイルを変更し、プレース ホルダー ライセンスの値を独自のライセンス キーに置き換えます。
7. Azure portal で App Service でアプリケーションを参照し、新しいアプリケーション設定を作成します。
    - もしアプリが **Java SE** を使用している場合、`JAVA_OPTS` の名前および `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` の値を持つ環境変数を作成します。
    - **Tomcat** を使用している場合、`CATALINA_OPTS`の名前および `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` の値を持つ環境変数を作成します。
    - **WildFly** を使用している場合は、Java エージェントのインストールおよび JBoss 構成に関するガイダンスについて、[こちら](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java)の New Relic のドキュメントを参照してください。
    - `JAVA_OPTS` または `CATALINA_OPTS` 用の環境変数がすでにある場合、`javaagent` のオプションを現在値の最期に追加します。

## <a name="configure-appdynamics"></a>AppDynamics の構成

1. [AppDynamics.com](https://www.appdynamics.com/community/register/) で AppDynamics アカウントを作成します
1. AppDynamics の web サイトより Java エージェントをダウンロードします。ファイル名はおよそ `AppServerAgent-x.x.x.xxxxx.zip` のようになります
1. [App Service インスタンスに SSH で接続し、](/azure/app-service/containers/app-service-linux-ssh-support)新しいディレクトリを作成`/home/site/wwwroot/apm` します。
1. `/home/site/wwwroot/apm`の下にあるディレクトリに Java エージェント ファイルをアップロードします。 エージェント用のファイルは `/home/site/wwwroot/apm/appdynamics` 内にある必要があります。
1. Azure portal で App Service でアプリケーションを参照し、新しいアプリケーション設定を作成します。
    - **Java SE** を使用している場合、`JAVA_OPTS` の名前および `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` の値を持ち、`<YOUR_SITE_NAME>` の App Service 名を持つ環境変数を作成します。
    - **Tomcat** を使用している場合、`CATALINA_OPTS` の名前および `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` の値を持ち、`<YOUR_SITE_NAME>` の App Service 名を持つ環境変数を作成します。
    - **WildFly** を使用している場合は、Java エージェントのインストールおよび JBoss 構成に関するガイダンスについて、[こちら](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings)の AppDynamics のドキュメントを参照してください。
