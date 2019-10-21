---
title: Azure Spring Cloud のトラブルシューティング ガイド | Microsoft Docs
description: Azure Spring Cloud のトラブルシューティング ガイド
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: ebb960085691206b096090813636ef56366e6536
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038361"
---
# <a name="troubleshooting-guide-for-common-problems"></a>よくある問題に関するトラブルシューティング ガイド

この記事では、Azure Spring Cloud 内で作業する開発者を対象に、よくある問題とトラブルシューティングの手順を詳しく説明します。 このほかに、Microsoft の [FAQ の記事](spring-cloud-faq.md)を読むこともお勧めします。

## <a name="availability-performance-and-application-issues"></a>可用性、パフォーマンス、アプリケーションの問題

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>アプリケーションを起動できない (エンドポイントに接続できない、数回の再試行の後にエンドポイントから 502 が返されるなど)

_Azure Log Analytics_ にログをエクスポートしてください。 Spring アプリケーション ログのテーブルの名前は `AppPlatformLogsforSpring` です。 詳細については、「[Analyze logs and metrics with Diagnostic settings](diagnostic-services.md)」(診断の設定でログとメトリックを分析する) を参照してください。

ログに次のエラーが見つかった場合には、次の 2 つの問題のいずれかが発生していることが考えられます。

`org.springframework.context.ApplicationContextException: Unable to start web server`

* いずれかの Bean またはその依存関係のいずれかが見つかりません。
* Bean のプロパティの 1 つが見つからないか、無効です。 この場合は、`java.lang.IllegalArgumentException` が発生していることも少なくありません。

このほか、サービスのバインディングが原因となってアプリケーションの起動に失敗することもあります。 バインドされているサービスに関連するキーワードを使用して、ログのクエリを実行します。  たとえば、ローカルのシステム時刻に設定されている MySQL インスタンスを対象としたバインディングがアプリケーションにあるとします。 このアプリケーションの起動に失敗した場合、ログで次のエラーが見つかることがあります。

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

このエラーを解決するには、MySQL インスタンスの `server parameters` にアクセスして、`time_zone` を `SYSTEM` から `+0:00` に変更します。


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>アプリケーションがクラッシュするか、予期しないエラーをスローする

デバッグ中のアプリケーションがクラッシュしたときは、まずアプリケーションの実行状態と検出状態を確認してみることをお勧めします。 Azure portal の _[アプリの管理]_ にアクセスして、アプリケーションがいずれも _[実行中]_ かつ _[稼働中]_ であることを確認します。

* 状態が _[実行中]_ であっても、検出状態が _[稼働中]_ でない場合には、「[アプリケーションを登録できない](#my-application-cannot-be-registered)」を参照してください。

* 検出状態が _[稼働中]_ になっている場合は、 _[メトリック]_ に移動して、アプリケーションの正常性を確認します。 確認するメトリックは次のとおりです。


  - `TomcatErrorCount` (_tomcat.global.error_):Spring アプリケーションのすべての例外がここでカウントされます。 この値が大きい場合は、_Azure Log Analytics_ に移動して、アプリケーション ログを調べてみてください。

  - `AppMemoryMax` (_jvm.memory.max_):アプリケーションで利用可能なメモリの最大量です。 これが定義されていないか、定義されていても時間が経って変わっている可能性があります。 最大値が定義されている場合、使用済みおよびコミット済みのメモリの量は常にそれ以下になります。 ただし、使用済みメモリ量が最大メモリ量以下であっても、コミット済みより使用済みが大きくなるように使用済みメモリ量を増やそうとすると、メモリ割り当てが `OutOfMemoryError` で失敗する可能性があります。 このような状況では、`-Xmx` パラメーターを使用して最大ヒープ サイズを増やしてみてください。

  - `AppMemoryUsed` (_jvm.memory.used_):アプリケーションが現在使用しているメモリの量 (バイト単位) です。 通常の負荷の Java アプリケーションでは、このメトリック シリーズは "鋸歯" パターンになります。この場合、メモリの使用量は、少しずつ増加して突然大きく減少するという、安定した増減パターンを繰り返します。 これは、Java 仮想マシン内のガベージ コレクションによるものであり、"鋸歯" 上で大きな減少を示したところが、コレクション アクションを表しています。
    このメトリックは、* メモリ エクスプロージョンの最初期、* 特定のロジック パスに対する大量のメモリ割り当て、* 段階的なメモリ リークなど、メモリの問題を識別するために重要です。

  詳細については、[メトリック](spring-cloud-concept-metrics.md)に関するページを参照してください。

_Azure Log Analytics_ の使用に関しては、[こちらの入門記事](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)を参照してください。

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>アプリケーションで高い CPU 使用率またはメモリ使用率が発生する

アプリケーションで CPU またはメモリの使用率が高くなった場合には、次の 2 つの状況のいずれかが発生しています。
* すべてのアプリ インスタンスで CPU またはメモリの使用率が高い。
* 一部のアプリ インスタンスで CPU またはメモリの使用率が高い。

どちらの状況であるかを確認するには、次のようにします。

1. _[メトリック]_ に移動し、[`Service CPU Usage Percentage`]\(サービス CPU 使用率\) または [`Service Memory Used`]\(使用済みサービス メモリ\) を選択します。
2. 監視するアプリケーションを指定するには、`App=` フィルターを追加します。
3. `Instance` でメトリックを分割します。

すべてのインスタンスで CPU またはメモリの使用率が高くなっている場合には、アプリケーションをスケールアウトするか、CPU またはメモリをスケールアップする必要があります。 詳細については、[アプリケーションのスケーリング](spring-cloud-tutorial-scale-manual.md)に関するページを参照してください。

一部のインスタンスで CPU またはメモリの使用率が高くなっている場合には、インスタンスの状態とその検出状態を確認します。

詳細については、[メトリック](spring-cloud-concept-metrics.md)に関するページを参照してください。

すべてのインスタンスが稼働している場合は、_Azure Log Analytics_ に移動して、アプリケーション ログのクエリを実行し、コードのロジックを調べて、いずれかがスケールのパーティション分割に影響している可能性があるかどうかを確認してください。 詳細については、「[Analyze logs and metrics with Diagnostic settings](diagnostic-services.md)」(診断の設定でログとメトリックを分析する) を参照してください。

_Azure Log Analytics_ の使用に関しては、[こちらの入門記事](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)を参照してください。 ログのクエリを実行する際は、[Kusto クエリ言語](https://docs.microsoft.com/azure/kusto/query/)を使用してください。

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>Spring アプリケーションを Azure Spring Cloud にオンボードする前のチェックリスト

* 指定した Java ランタイム バージョンのローカル環境で、アプリケーションを実行できる。
* 環境の構成 (CPU、RAM、およびインスタンス) が、アプリケーション プロバイダーが定めた最小要件を満たしている。
* 構成項目に適切な値が設定されている。 詳細については、[構成サーバー](spring-cloud-tutorial-config-server.md)に関するページを参照してください。
* 環境変数に適切な値が設定されている。
* JVM パラメーターに適切な値が設定されている。
* 埋め込まれている "_構成サーバー_" および "_Spring サービス レジストリ_" サービスを無効にするか、アプリケーション パッケージから削除することをお勧めします。
* いずれかの Azure リソースを "_サービス バインド_" によってバインドする場合は、ターゲット リソースが稼働していることを確認します。

## <a name="configuration-and-management"></a>構成と管理

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>Azure Spring Cloud サービス インスタンスの作成で問題が発生した

ポータルを使用して _Azure Spring Cloud_ サービス インスタンスをプロビジョニングしようとすると、Azure Spring Cloud による検証が自動的に行われます。

これに対して、[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) または [Resource Manager テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/)を使用して _Azure Spring Cloud_ サービス インスタンスをプロビジョニングする場合には、次のことを確認してください。

* サブスクリプションがアクティブである。
* 場所が _Azure Spring Cloud_ で[サポートされている](spring-cloud-faq.md)。
* インスタンスのリソース グループが既に作成されている。
* リソース名が、名前付け規則に準拠している (使用できる文字は小文字、数字、ハイフンのみです。 先頭の文字は英字にする必要があります。 末尾の文字は、文字または数字にする必要があります。 値の長さは 2 から 32 文字にする必要があります)。

Resource Manager テンプレートを使用して _Azure Spring Cloud_ サービス インスタンスをプロビジョニングする場合には、 https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates でテンプレートの構文を確認してください。

_Azure Spring Cloud_ サービス インスタンスの名前は、`azureapps.io` 以下のサブドメイン名を要求するために使用されるため、名前が既存のものと競合する場合、プロビジョニングに失敗します。 アクティビティ ログで詳細を確認できます。

### <a name="i-cannot-deploy-a-jar-package"></a>JAR パッケージをデプロイできない

ポータルまたは Resource Manager テンプレートを使用して JAR やソース パッケージをアップロードすることはできません。

[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) を使用してアプリケーション パッケージをデプロイすると、デプロイの進行状況が定期的にポーリングされ、最後にデプロイの結果が表示されます。

ポーリングが中断された場合でも、次のコマンドを使用してデプロイ ログを取得できます。

`az spring-cloud app show-deploy-log -n <app-name>`

アプリケーションが正しい[実行可能 jar 形式](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)でパッケージ化されていることを確認してください。 そうでない場合は、次のようなエラーが表示されます。

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>ソース パッケージをデプロイできない

ポータルまたは Resource Manager テンプレートを使用して JAR やソース パッケージをアップロードすることはできません。

[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) を使用してアプリケーション パッケージをデプロイすると、デプロイの進行状況が定期的にポーリングされ、最後にデプロイの結果が表示されます。

ポーリングが中断された場合でも、次のコマンドを使用してビルドとデプロイのログを取得できます。

`az spring-cloud app show-deploy-log -n <app-name>`

ただし、1 つの _Azure Spring Cloud_ サービス インスタンスから一度にトリガーできるビルド ジョブは、ソース パッケージ 1 つにつき 1 件のみであることに注意してください。 詳細については、[アプリケーションのデプロイ](spring-cloud-quickstart-launch-app-portal.md)に関するページおよび[ステージング環境に関するガイド](spring-cloud-howto-staging-environment.md)のページを参照してください。

### <a name="my-application-cannot-be-registered"></a>アプリケーションを登録できない

この問題が発生するのは、必要とされる依存関係またはサービス検出が POM ファイル内で正しく構成されていない場合がほとんどです。 構成が終わったら、組み込みのサービス レジストリ サーバー エンドポイントが、アプリケーションに環境変数として挿入されます。 その後はアプリケーションがサービス レジストリ サーバーに登録され、他の依存マイクロサービスを検出していきます。

新しく登録されたインスタンスがトラフィックの受信を始めるまで、少なくとも 2 分待ってください。

既存の Spring Cloud ベースのソリューションを Azure に移行する場合には、_Azure Spring Cloud_ によって提供されるマネージド インスタンスとの競合を避けるため、アドホックの "_サービス レジストリ_" インスタンスと "_構成サーバー_" インスタンスを削除 (または無効化) してあることを確認してください。

また、_Azure Log Analytics_ で "_サービス レジストリ_" のクライアント ログを確認することもできます。 詳細については、「[Analyze logs and metrics with Diagnostic settings](diagnostic-services.md)」(診断の設定でログとメトリックを分析する) を参照してください。

_Azure Log Analytics_ の使用に関しては、[こちらの入門記事](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)を参照してください。 ログのクエリを実行する際は、[Kusto クエリ言語](https://docs.microsoft.com/azure/kusto/query/)を使用してください。

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>アプリケーションのメトリックまたはログが見つからない

_[アプリの管理]_ に移動して、アプリケーションが _[実行中]_ かつ _[稼働中]_ であることを確認します。

_JVM_ のメトリックが表示されるものの、_Tomcat_ のメトリックが表示されない場合は、アプリケーション パッケージで `spring-boot-actuator` 依存関係が有効になっており、正常に起動しているかどうかを確認します。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

アプリケーション ログをストレージ アカウントにアーカイブすることはできても、_Azure Log Analytics_ に送信できない場合は、[ワークスペースを正しく設定している](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)かどうかを確認してください。 Free レベルの _Azure Log Analytics_ を使用している場合は、[Free レベルでは SLA が提供されない](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)点にご注意ください。