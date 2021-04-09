---
title: Azure Spring Cloud のトラブルシューティング ガイド | Microsoft Docs
description: Azure Spring Cloud のトラブルシューティング ガイド
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 2a26a71e956a7e1313af9e99cc92232d0caf91e7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877649"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Azure Spring Cloud に関する一般的な問題のトラブルシューティング

この記事では、Azure Spring Cloud の開発に関する問題のトラブルシューティングについて説明します。 詳しくは、「[よく寄せられる質問](spring-cloud-faq.md)」をご覧ください。

## <a name="availability-performance-and-application-issues"></a>可用性、パフォーマンス、アプリケーションの問題

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>アプリケーションを起動できない (エンドポイントに接続できない、数回の再試行の後に 502 が返されるなど)

Azure Log Analytics にログをエクスポートしてください。 Spring アプリケーション ログのテーブルの名前は *AppPlatformLogsforSpring* です。 詳しくは、「[診断設定でログとメトリックを分析する](diagnostic-services.md)」をご覧ください。

ログに次のエラー メッセージが含まれることがあります。

> "org.springframework.context.ApplicationContextException: Unable to start web server" (Web サーバーを開始できません)

このメッセージは、次の 2 つの問題のいずれかを示しています。 
* いずれかの Bean またはその依存関係のいずれかが見つかりません。
* Bean のプロパティの 1 つが見つからないか、無効です。 この場合、"java.lang.IllegalArgumentException" が表示される可能性があります。

サービスのバインディングが原因で、アプリケーションの起動が失敗することもあります。 ログのクエリを実行するには、バインドされているサービスに関連するキーワードを使います。 たとえば、ローカルのシステム時刻に設定されている MySQL インスタンスを対象としたバインディングがアプリケーションにあるとします。 アプリケーションの起動が失敗する場合、次のエラー メッセージがログで見つかることがあります。

> "java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone." (サーバーのタイム ゾーンの値 '協定世界時' が認識されないか、複数のタイム ゾーンを表しています。)

このエラーを解決するには、MySQL インスタンスの `server parameters` に移動し、`time_zone` の値を *SYSTEM* から *+0:00* に変更します。


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>アプリケーションがクラッシュするか、予期しないエラーをスローする

アプリケーションのクラッシュをデバッグするときは、まずアプリケーションの実行状態と検出状態を確認します。 そのためには、Azure portal の _[アプリの管理]_ に移動し、すべてのアプリケーションの状態が _[実行中]_ かつ _[稼働中]_ であることを確認します。

* 状態が _[実行中]_ であっても、検出状態が _[稼働中]_ でない場合は、「[アプリケーションを登録できない](#my-application-cant-be-registered)」セクションをご覧ください。

* 検出状態が _[稼働中]_ になっている場合は、[メトリック] に移動して、アプリケーションの正常性を確認します。 確認するメトリックは次のとおりです。


  - `TomcatErrorCount` (_tomcat.global.error_):Spring アプリケーションのすべての例外がここにカウントされます。 この値が大きい場合は、Azure Log Analytics に移動して、アプリケーション ログを調べます。

  - `AppMemoryMax` (_jvm.memory.max_):アプリケーションで利用可能なメモリの最大量です。 量が定義されていないか、定義されていても時間が経って変更されている可能性があります。 定義されている場合、使用済みおよびコミット済みのメモリの量は常に最大量以下になります。ただし、"*使用済みメモリ量が最大メモリ量以下*" であっても、割り当てにおいて "*コミット済みより使用済みが大きくなる*" ように使用済みメモリの増量が試みられると、メモリ割り当てが `OutOfMemoryError` メッセージで失敗する可能性があります。 このような状況では、`-Xmx` パラメーターを使用して最大ヒープ サイズを増やしてみてください。

  - `AppMemoryUsed` (_jvm.memory.used_):アプリケーションで現在使用されているメモリの量 (バイト単位) です。 通常の負荷の Java アプリケーションでは、このメトリック シリーズは "*鋸歯*" パターンになります。この場合、メモリの使用量は、少しずつ増加して突然大きく減少するという、安定した増減パターンを繰り返します。 このメトリック シリーズは、Java 仮想マシン内のガベージ コレクションによって発生し、鋸歯パターンで大きく低下したところが、コレクション アクションを表しています。
    
    このメトリックは、次のようなメモリの問題を特定するのに役立ちます。
    * 最初のメモリの爆発的な増加。
    * 特定の論理パスに対するメモリ割り当ての急増。
    * 段階的なメモリ リーク。
  詳しくは、[メトリック](spring-cloud-concept-metrics.md)に関する記事をご覧ください。
  
* アプリケーションの起動に失敗した場合は、アプリケーションに有効な jvm パラメーターがあることを確認します。 jvm メモリの設定が高すぎる場合、ログに次のエラー メッセージが含まれている可能性があります。

  >"required memory 2728741K is greater than 2000M available for allocation" (必要なメモリ 2728741K は割り当てに使用できる 2000M を超えています)



Azure Log Analytics について詳しくは、「[Azure Monitor で Log Analytics の使用を開始する](../azure-monitor/logs/log-analytics-tutorial.md)」をご覧ください。

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>アプリケーションで高い CPU 使用率またはメモリ使用率が発生する

アプリケーションで CPU またはメモリの使用率が高くなった場合には、次の 2 つの状況のいずれかが発生しています。
* すべてのアプリ インスタンスで CPU またはメモリの使用率が高い。
* 一部のアプリ インスタンスで CPU またはメモリの使用率が高い。

どちらの状況が当てはまるかを確認するには、次のようにします。

1. **[メトリック]** に移動し、 **[サービス CPU 使用率]** または **[Service Memory Used]\(サービス メモリ使用量\)** を選択します。
2. 監視するアプリケーションを指定するには、**App=** フィルターを追加します。
3. **インスタンス** ごとにメトリックを分割します。

"*すべてのインスタンス*" で CPU またはメモリの使用率が高くなっている場合は、アプリケーションをスケールアウトするか、CPU またはメモリの使用量をスケールアップする必要があります。 詳細については、[Azure Spring Cloud でアプリケーションをスケーリングする](spring-cloud-howto-scale-manual.md)」をご覧ください。

"*一部のインスタンス*" で CPU またはメモリの使用率が高くなっている場合は、インスタンスの状態とその検出状態を確認します。

詳しくは、「[Azure Spring Cloud のメトリック](spring-cloud-concept-metrics.md)」をご覧ください。

すべてのインスタンスが稼働している場合は、Azure Log Analytics に移動して、アプリケーション ログのクエリを実行し、コードのロジックを調べます。 これは、いずれかがスケールのパーティション分割に影響している可能性があるかどうかを確認するのに役立ちます。 詳しくは、「[診断設定でログとメトリックを分析する](diagnostic-services.md)」をご覧ください。

Azure Log Analytics について詳しくは、「[Azure Monitor で Log Analytics の使用を開始する](../azure-monitor/logs/log-analytics-tutorial.md)」をご覧ください。 [Kusto クエリ言語](/azure/kusto/query/)を使用して、ログのクエリを実行します。

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Spring アプリケーションを Azure Spring Cloud にデプロイするためのチェックリスト

アプリケーションをオンボードする前に、次の条件を満たしていることを確認します。

* 指定した Java ランタイム バージョンのローカル環境で、アプリケーションを実行できる。
* 環境の構成 (CPU、RAM、およびインスタンス) が、アプリケーション プロバイダーが定めた最小要件を満たしている。
* 構成項目に適切な値が設定されている。 詳細については、[構成サーバー](spring-cloud-howto-config-server.md)に関するページを参照してください。
* 環境変数に適切な値が設定されている。
* JVM パラメーターに適切な値が設定されている。
* 埋め込まれている "_構成サーバー_" および "_Spring サービス レジストリ_" サービスを無効にするか、アプリケーション パッケージから削除することをお勧めします。
* いずれかの Azure リソースを "_サービス バインド_" によってバインドする場合は、ターゲット リソースが稼働していることを確認します。

## <a name="configuration-and-management"></a>構成と管理

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Azure Spring Cloud サービス インスタンスの作成で問題が発生した

Azure portal を使用して Azure Spring Cloud サービス インスタンスを設定すると、Azure Spring Cloud によって自動的に検証が行われます。

一方、[Azure CLI](/cli/azure/get-started-with-azure-cli) または [Azure Resource Manager テンプレート](../azure-resource-manager/index.yml)を使用して Azure Spring Cloud サービス インスタンスを設定する場合には、次のことを確認してください。

* サブスクリプションがアクティブである。
* 場所が Azure Spring Cloud で[サポートされている](spring-cloud-faq.md)。
* インスタンスのリソース グループが既に作成されている。
* リソース名が、名前付け規則に準拠している 使用できる文字は小文字、数字、およびハイフンのみです。 先頭の文字は英字にする必要があります。 末尾の文字は、文字または数字にする必要があります。 値は 2 文字以上 32 文字以下にする必要があります。

Resource Manager テンプレートを使用して Azure Spring Cloud サービス インスタンスを設定する場合は、最初に「[Azure Resource Manager テンプレートの構造と構文の詳細](../azure-resource-manager/templates/template-syntax.md)」を参照してください。

Azure Spring Cloud サービス インスタンスの名前が `azureapps.io` の下のサブドメイン名を要求するために使用されるため、名前が既存のものと競合する場合、設定は失敗します。 アクティビティ ログで詳細を確認できる場合があります。

### <a name="i-cant-deploy-a-net-core-app"></a>.NET Core アプリをデプロイできない

Azure portal または Resource Manager テンプレートを使用して .NET Core Steeltoe アプリの *.zip* ファイルをアップロードすることはできません。

[Azure CLI](/cli/azure/get-started-with-azure-cli) を使用してアプリケーション パッケージをデプロイすると、Azure CLI によってデプロイの進行状況が定期的にポーリングされ、最後にデプロイの結果が表示されます。

アプリケーションが正しい *.zip* ファイル形式でパッケージ化されていることを確認してください。 正しくパッケージ化されていないと、プロセスの応答が停止するか、エラー メッセージが表示されます。

### <a name="i-cant-deploy-a-jar-package"></a>JAR パッケージをデプロイできない

Azure portal または Resource Manager テンプレートを使用して、Java アーカイブ ファイル (JAR) やソース パッケージをアップロードすることはできません。

[Azure CLI](/cli/azure/get-started-with-azure-cli) を使用してアプリケーション パッケージをデプロイすると、Azure CLI によってデプロイの進行状況が定期的にポーリングされ、最後にデプロイの結果が表示されます。

ポーリングが中断された場合でも、次のコマンドを使用してデプロイ ログを取得できます。

`az spring-cloud app show-deploy-log -n <app-name>`

アプリケーションが適切な[実行可能 JAR 形式](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)でパッケージ化されていることを確認します。 正しくパッケージ化されていない場合は、次のようなエラーメッセージが表示されます。

> "エラー: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714" (JAR ファイル無効または破損しています)

### <a name="i-cant-deploy-a-source-package"></a>ソース パッケージをデプロイできない

Azure portal または Resource Manager テンプレートを使用して、JAR やソース パッケージをアップロードすることはできません。

[Azure CLI](/cli/azure/get-started-with-azure-cli) を使用してアプリケーション パッケージをデプロイすると、Azure CLI によってデプロイの進行状況が定期的にポーリングされ、最後にデプロイの結果が表示されます。

ポーリングが中断された場合でも、次のコマンドを使用してビルドとデプロイのログを取得できます。

`az spring-cloud app show-deploy-log -n <app-name>`

ただし、1 つの Azure Spring Cloud サービス インスタンスから一度にトリガーできるビルド ジョブは、ソース パッケージ 1 つにつき 1 件のみであることに注意してください。 詳しくは、「[アプリケーションをデプロイする](spring-cloud-quickstart.md)」および「[Azure Spring Cloud でステージング環境を設定する](spring-cloud-howto-staging-environment.md)」をご覧ください。

### <a name="my-application-cant-be-registered"></a>アプリケーションを登録できない

ほとんどの場合、このような状況が発生するのは、"*必要な依存関係*" および "*サービス検出*" が、プロジェクト オブジェクト モデル (POM) ファイルで正しく構成されていない場合です。 構成が終わると、組み込みのサービス レジストリ サーバー エンドポイントが、アプリケーションに環境変数として挿入されます。 その後、アプリケーションによって、アプリケーション自体がサービス レジストリ サーバーに登録され、他の依存マイクロサービスが検出されます。

新しく登録されたインスタンスでトラフィックの受信が開始されるまで、少なくとも 2 分待ってください。

既存の Spring Cloud ベースのソリューションを Azure に移行する場合は、Azure Spring Cloud によって提供されるマネージド インスタンスとの競合を避けるため、アドホックの "_サービス レジストリ_" インスタンスと "_構成サーバー_" インスタンスを削除 (または無効化) してあることを確認してください。

また、Azure Log Analytics で "_サービス レジストリ_" のクライアント ログを確認することもできます。 詳しくは、「[診断設定でログとメトリックを分析する](diagnostic-services.md)」をご覧ください

Azure Log Analytics について詳しくは、「[Azure Monitor で Log Analytics の使用を開始する](../azure-monitor/logs/log-analytics-tutorial.md)」をご覧ください。 [Kusto クエリ言語](/azure/kusto/query/)を使用して、ログのクエリを実行します。

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>アプリケーションの環境変数を調べたい

環境変数によって Azure Spring Cloud フレームワークに伝えられる情報により、アプリケーションを構成するサービスの場所と構成方法が Azure で認識されます。 潜在的な問題をトラブルシューティングするためにまず必要な手順は、環境変数が正しいことの確認です。  環境変数は、Spring Boot Actuator エンドポイントを使用して確認できます。  

> [!WARNING]
> この手順では、テスト エンドポイントを使用して環境変数が公開されます。  テスト エンドポイントがパブリックにアクセスできる場合や、アプリケーションにドメイン名が割り当てられている場合は、この先の手順を行わないようにしてください。

1. `https://<your application test endpoint>/actuator/health` にアクセスします。  
    - `{"status":"UP"}` のような応答は、エンドポイントが有効になっていることを示します。
    - 応答が否定的である場合は、*POM.xml* ファイルに次の依存関係を追加します。

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Spring Boot Actuator エンドポイントが有効になったら、Azure portal に移動してアプリケーションの構成ページを見つけます。  名前は `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`、値は `*` として、環境変数を追加します。 

1. アプリケーションを再起動します。

1. `https://<your application test endpoint>/actuator/env` に移動して、応答を調べます。  次のようになります。

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

`systemEnvironment` という名前の子ノードを検索します。  このノードにアプリケーションの環境変数が格納されています。

> [!IMPORTANT]
> アプリケーションをパブリックにアクセスできるようにする前に、公開した環境変数は必ず元に戻してください。  Azure portal に移動してアプリケーションの構成ページを探し、環境変数 `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` を削除します。

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>アプリケーションのメトリックまたはログが見つからない

**[アプリの管理]** に移動し、アプリケーションの状態が _[実行中]_ かつ _[稼働中]_ であることを確認します。

アプリケーション パッケージで _JMX_ が有効になっているか確認します。 この機能は構成プロパティ `spring.jmx.enabled=true` で有効にできます。  

アプリケーション パッケージで `spring-boot-actuator` 依存関係が有効になっており、正常に起動しているかどうかを確認します。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

アプリケーション ログをストレージ アカウントにアーカイブすることはできても、Azure Log Analytics に送信できない場合は、[ワークスペースが正しく設定されている](../azure-monitor/logs/quick-create-workspace.md)かどうかを確認します。 Free レベルの Azure Log Analytics を使用している場合は、[Free レベルではサービス レベル アグリーメント (SLA) が提供されない](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)ことに注意してください。

## <a name="next-steps"></a>次の手順

* [Azure Spring Cloud での問題を自己診断して解決する方法](spring-cloud-howto-self-diagnose-solve.md)
