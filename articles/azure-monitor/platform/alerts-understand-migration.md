---
title: Azure Monitor アラートのための移行について
description: アラート移行のしくみと問題のトラブルシューティングの方法について説明します。
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 52a74593fcfbdc2c1e464077e4ae460f6a5a9c39
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852397"
---
# <a name="understand-migration-options-to-newer-alerts"></a>新しいアラートへの移行オプションについて

従来のアラートは[廃止](./monitoring-classic-retirement.md)されますが、新しいアラートをまだサポートしていないリソースのために、引き続き限定的に使用されます。 残りのアラート移行である [Azure Government クラウド](../../azure-government/documentation-government-welcome.md)と [Azure China 21Vianet](https://docs.azure.cn/) については、近日中に新しい日付が発表されます。

この記事では、手動移行と自発的移行ツールがどのように機能するかについて説明します。このツールは、残りのアラート ルールを移行するために使用されます。 また、一般的な問題をいくつか取り上げて、その解決方法を紹介します。

> [!IMPORTANT]
> アクティビティ ログのアラート (サービス正常性のアラートを含む) とログ アラートは移行の影響を受けません。 移行は、[ここ](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)で説明されているクラシック アラート ルールにのみ適用されます。

> [!NOTE]
> クラシック アラート ルールが有効でない、つまり、[非推奨のメトリック](#classic-alert-rules-on-deprecated-metrics)や削除済みのリソースについてのルールである場合、それらは移行されず、サービスの廃止後は使用できなくなります。

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>従来のアラートの新しいアラートへの手動移行

残りのアラートを手動で移行することに関心のあるお客様は、以降のセクションを参考にして、それを既に実行することができます。 これらのセクションでは、リソース プロバイダーによって廃止されていて、現在は直接移行できないメトリックも定義しています。

### <a name="guest-metrics-on-virtual-machines"></a>仮想マシンのゲスト メトリック

ゲスト メトリックに新しいメトリック アラートを作成するには、その前にゲスト メトリックを Azure Monitor カスタム メトリック ストアに送信する必要があります。 次の手順に従って、診断設定で Azure Monitor シンクを有効にします。

- [Windows VM のゲスト メトリックの有効化](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Linux VM のゲスト メトリックの有効化](collect-custom-metrics-linux-telegraf.md)

これらの手順が完了したら、ゲスト メトリックで新しいメトリック アラートを作成できます。 新しいメトリック アラートを再作成したら、クラシック アラートを削除できます。

### <a name="storage-account-metrics"></a>ストレージ アカウントのメトリック

これらのメトリックに対するアラートを除いて、ストレージ アカウントに対するすべてのクラシック アラートを移行できます。

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

パーセント メトリックに対するクラシック アラート ルールは、[従来のストレージ メトリックと新しいストレージ メトリック間のマッピング](../../storage/common/storage-metrics-migration.md#metrics-mapping-between-old-metrics-and-new-metrics)に基づいて移行する必要があります。 使用可能な新しいメトリックは絶対値であるため、しきい値を適切に変更する必要があります。

AnonymousThrottlingError、SASThrottlingError、および ThrottlingError に関するクラシック アラート ルールについては、同じ機能を提供する一体型のメトリックがないため、2 つの新しいアラートに分割する必要があります。 しきい値を適切に調整する必要があります。

### <a name="cosmos-db-metrics"></a>Cosmos DB のメトリック

これらのメトリックに対するアラートを除いて、Cosmos DB メトリックに対するすべてのクラシック アラートを移行できます。

- 1 秒あたりの平均要求数
- 整合性レベル
- HTTP 2xx
- HTTP 3xx
- HTTP 400
- HTTP 401
- 内部サーバー エラー
- 1 分あたりの最大 RUPM 消費量
- 1 秒あたりの最大 RU 数
- 失敗した Mongo Count 要求
- 失敗した Mongo Delete 要求
- 失敗した Mongo Insert 要求
- 失敗したその他の Mongo 要求
- その他の Mongo 要求の料金
- その他の Mongo 要求のレート
- 失敗した Mongo Query 要求
- 失敗した Mongo Update 要求
- 測定された読み取り待機時間
- 測定された書き込み待機時間
- サービスの可用性
- ストレージの容量
- 調整された要求数
- 要求の合計数

1 秒あたりの平均要求数、整合性レベル、1 分あたりの最大 RUPM 消費量、1 秒あたりの最大 RU 数、測定された読み込み待機時間、測定された書き込み待機時間、ストレージ容量は現在、[新しいシステム](metrics-supported.md#microsoftdocumentdbdatabaseaccounts)で使用できません。

HTTP 2xx、HTTP 3xx、HTTP 400、HTTP 401、内部サーバー エラー、サービス可用性、スロットルされた要求数、合計要求数などの要求メトリックに関するアラートは、要求のカウント方法がクラシックのメトリックと新しいメトリックの間で異なるため、移行されません。 これらに関するアラートは、しきい値を調整して手動で再作成する必要があります。

失敗した Mongo 要求のメトリックに関するアラートは、同じ機能を提供する一体型のメトリックがないため、複数のアラートに分割する必要があります。 しきい値を適切に調整する必要があります。

### <a name="classic-compute-metrics"></a>従来のコンピューティング メトリック

クラシック コンピューティング リソースは新しいアラートでまだサポートされていないため、クラシック コンピューティング メトリックに関するアラートは移行ツールを使用しても移行されません。 これらのリソースの種類に対する新しいアラートのサポートは現在パブリック プレビュー段階であり、お客様は従来のアラート ルールに基づいて新しい同等のアラート ルールを再作成できます。

### <a name="classic-alert-rules-on-deprecated-metrics"></a>非推奨のメトリックに対するクラシック アラート ルール

これらは、以前はサポートされていましたが、結局非推奨とされたメトリックに対するクラシック アラート ルールです。 ごく一部のお客様は、このようなメトリックに対する無効なクラシック アラート ルールを持っている場合があります。 これらのアラート ルールは無効であるため、移行されません。

| リソースの種類| 非推奨のメトリック |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, throttling, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>同等の新しいアラート ルールとアクション グループはどのように作成されるか

移行ツールにより、クラシック アラート ルールは同等の新しいアラート ルールとアクション グループに変換されます。 ほとんどのクラシック アラート ルールでは、同等の新しいアラート ルールは、`windowSize` や `aggregationType` など、同じプロパティを持つ同じメトリックに対するものとなります。 ただし、一部のクラシック アラート ルールは、新しいシステムの、別個かつ同等のメトリックに対するものとなります。 次の原則は、後のセクションで指定されていなければクラシック アラートの移行に適用されます。

- **頻度**: クラシック アラート ルールまたは新しいアラート ルールが条件をチェックする頻度を定義します。 クラシック アラート ルールの `frequency` は、ユーザーが構成可能ではなく、Application Insights コンポーネント (1 分間隔) を除くすべてのリソースの種類に対して常に 5 分間隔でした。そのため、同等のルールの頻度も、それぞれ 5 分と 1 分に設定されます。
- **集計の種類**: 関心のあるウィンドウ全体にわたるメトリックの集計方法を定義します。 `aggregationType` も、ほとんどのメトリックについて、クラシック アラートと新しいアラートの間に違いはありません。 場合によってはクラシック アラートと新しいアラートの間でメトリックが異なるため、そうしたメトリックに対して定義される同等の `aggregationType` または `primary Aggregation Type` が使用されます。
- **単位**: アラートが作成される対象のメトリックのプロパティ。 一部の同等メトリックの単位は異なっています。 しきい値は、必要に応じて適切に調整されます。 たとえば、元のメトリックが秒単位であるのに、同等の新しいメトリックがミリ秒単位である場合、確実に同じ動作となるように、元のしきい値には 1000 が掛けられます。
- **ウィンドウ サイズ**: しきい値と比較するため、メトリック データを集計するウィンドウを定義します。 5 分、15 分、30 分、1 時間、3 時間、6 時間、12 時間、1 日といった標準の `windowSize` 値では、同等の新しいアラート ルールのために加えられた変更はありません。 その他の値の場合、最も近い `windowSize` が選択されて使用されます。 ほとんどのお客様にとって、この変更の影響はありません。 ごく一部のお客様については、まったく同じ動作を得るためにしきい値の調整が必要な可能性があります。

以降のセクションでは、新しいシステムで別個かつ同等のメトリックを持つメトリックについて詳しく説明します。 クラシック アラート ルールと新しいアラート ルールで同じままのメトリックはすべて、一覧に記載されていません。 新しいシステムでサポートされるメトリックの一覧は、[ここ](metrics-supported.md)にあります。

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

BLOB、テーブル、ファイル、キューなどのストレージ アカウント サービスの場合、以下のメトリックは次に示す同等のメトリックにマッピングされています。

| クラシック アラートでのメトリック | 新しいアラートでの同等メトリック | 説明|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| ディメンション "ResponseType"="AuthorizationError" および "Authentication" = "Anonymous" のあるトランザクション メトリック| |
| AnonymousClientOtherError | ディメンション "ResponseType"="ClientOtherError" および "Authentication" = "Anonymous" のあるトランザクション メトリック | |
| AnonymousClientTimeOutError| ディメンション "ResponseType"="ClientTimeOutError" および "Authentication" = "Anonymous" のあるトランザクション メトリック | |
| AnonymousNetworkError | ディメンション "ResponseType"="NetworkError" および "Authentication" = "Anonymous" のあるトランザクション メトリック | |
| AnonymousServerOtherError | ディメンション "ResponseType"="ServerOtherError" および "Authentication" = "Anonymous" のあるトランザクション メトリック | |
| AnonymousServerTimeOutError | ディメンション "ResponseType"="ServerTimeOutError" および "Authentication" = "Anonymous" のあるトランザクション メトリック | |
| AnonymousSuccess | ディメンション "ResponseType"="Success" および "Authentication" = "Anonymous" のあるトランザクション メトリック | |
| AuthorizationError | ディメンション "ResponseType"="AuthorizationError" のあるトランザクション メトリック | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| 容量 | BlobCapacity | "last" ではなく `aggregationType` "average" を使用します。 メトリックは Blob サービスにのみ適用されます |
| ClientOtherError | ディメンション "ResponseType"="ClientOtherError" のあるトランザクション メトリック  | |
| ClientTimeoutError | ディメンション "ResponseType"="ClientTimeOutError" のあるトランザクション メトリック | |
| ContainerCount | ContainerCount | "last" ではなく `aggregationType` "average" を使用します。 メトリックは Blob サービスにのみ適用されます |
| NetworkError | ディメンション "ResponseType"="NetworkError" のあるトランザクション メトリック | |
| ObjectCount | BlobCount| "last" ではなく `aggregationType` "average" を使用します。 メトリックは Blob サービスにのみ適用されます |
| SASAuthorizationError | ディメンション "ResponseType"="AuthorizationError" および "Authentication" = "SAS" のあるトランザクション メトリック | |
| SASClientOtherError | ディメンション "ResponseType"="ClientOtherError" および "Authentication" = "SAS" のあるトランザクション メトリック | |
| SASClientTimeOutError | ディメンション "ResponseType"="ClientTimeOutError" および "Authentication" = "SAS" のあるトランザクション メトリック | |
| SASNetworkError | ディメンション "ResponseType"="NetworkError" および "Authentication" = "SAS" のあるトランザクション メトリック | |
| SASServerOtherError | ディメンション "ResponseType"="ServerOtherError" および "Authentication" = "SAS" のあるトランザクション メトリック | |
| SASServerTimeOutError | ディメンション "ResponseType"="ServerTimeOutError" および "Authentication" = "SAS" のあるトランザクション メトリック | |
| SASSuccess | ディメンション "ResponseType"="Success" および "Authentication" = "SAS" のあるトランザクション メトリック | |
| ServerOtherError | ディメンション "ResponseType"="ServerOtherError" のあるトランザクション メトリック | |
| ServerTimeOutError | ディメンション "ResponseType"="ServerTimeOutError" のあるトランザクション メトリック  | |
| Success | ディメンション "ResponseType"="Success" のあるトランザクション メトリック | |
| TotalBillableRequests| トランザクション | |
| TotalEgress | エグレス | |
| TotalIngress | イングレス | |
| TotalRequests | トランザクション | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

Application Insights の場合、同等のメトリックは以下に示すようになっています。

| クラシック アラートでのメトリック | 新しいアラートでの同等メトリック | 説明|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/duration| 元のしきい値は 1,000 倍にされます。クラシック メトリックは秒単位で、新しいメトリックではミリ秒単位であるためです。  |
| basicExceptionBrowser.count | exceptions/browser|  "sum" ではなく `aggregationType` "count" を使用します。 |
| basicExceptionServer.count | exceptions/server| "sum" ではなく `aggregationType` "count" を使用します。  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| 元のしきい値は 1,000 倍にされます。クラシック メトリックは秒単位で、新しいメトリックではミリ秒単位であるためです。  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  元のしきい値は 1,000 倍にされます。クラシック メトリックは秒単位で、新しいメトリックではミリ秒単位であるためです。 |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| 元のしきい値は 1,000 倍にされます。クラシック メトリックは秒単位で、新しいメトリックではミリ秒単位であるためです。  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| 元のしきい値は 1,000 倍にされます。クラシック メトリックは秒単位で、新しいメトリックではミリ秒単位であるためです。  |
| clientPerformance.total.value | browserTimings/totalDuration| 元のしきい値は 1,000 倍にされます。クラシック メトリックは秒単位で、新しいメトリックではミリ秒単位であるためです。  |
| performanceCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| 元のメトリックはすべてのコアにわたっていて、新しいメトリックは 1 つのコアに正規化されるため、しきい値は適切に修正する必要があります。 移行ツールではしきい値は変更されません。  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | requests/duration| 元のしきい値は 1,000 倍にされます。クラシック メトリックは秒単位で、新しいメトリックではミリ秒単位であるためです。  |
| request.rate | 要求/率|   |
| requestFailed.count | requests/failed| "sum" ではなく `aggregationType` "count" を使用します。   |
| view.count | pageViews/count| "sum" ではなく `aggregationType` "count" を使用します。   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Cosmos DB の場合、同等のメトリックは以下に示すようになっています。

| クラシック アラートでのメトリック | 新しいアラートでの同等メトリック | 説明|
|--------------------------|---------------------------------|---------|
| AvailableStorage     |AvailableStorage|   |
| データ サイズ | DataUsage| |
| ドキュメント数 | DocumentCount||
| インデックス サイズ | IndexUsage||
| Mongo Count 要求の料金| ディメンション "CommandName" = "count" のある MongoRequestCharge||
| Mongo Count 要求のレート | ディメンション "CommandName" = "count" のある MongoRequestsCount||
| Mongo Delete 要求の料金 | ディメンション "CommandName" = "delete" のある MongoRequestCharge||
| Mongo Delete 要求のレート | ディメンション "CommandName" = "delete" のある MongoRequestsCount||
| Mongo Insert 要求の料金 | ディメンション "CommandName" = "insert" のある MongoRequestCharge||
| Mongo Insert 要求のレート | ディメンション "CommandName" = "insert" のある MongoRequestsCount||
| Mongo Query 要求の料金 | ディメンション "CommandName" = "find" のある MongoRequestCharge||
| Mongo Query 要求のレート | ディメンション "CommandName" = "find" のある MongoRequestsCount||
| Mongo Update 要求の料金 | ディメンション "CommandName" = "update" のある MongoRequestCharge||
| サービス利用不可| ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>同等のアクション グループはどのように作成されるか

クラシック アラート ルールには、アラート ルール自体に関連付けられているメール、Webhook、ロジック アプリ、Runbook のアクションがあります。 新しいアラート ルールでは、複数のアラート ルールにわたって再利用できるアクション グループが使用されます。 移行ツールは、そのアクションをいくつのアラート ルールが使用しているかに関わらず、同じアクションに対して 1 つのアクション グループを作成します。 移行ツールによって作成されたアクション グループは、"Migrated_AG*" という名前付け形式を使用します。

> [!NOTE]
> 従来の管理者ロールへの通知に使用したときのクラシック アラートでは、従来の管理者のロケールに基づいて、ローカライズした電子メールを送信していました。 新しいアラート メールは、アクション グループを介して英語のみで送信されます。

## <a name="rollout-phases"></a>展開の段階

移行ツールは、クラシック アラート ルールを使用しているすべてのお客様に対して段階的に展開中です。 ツールを使用してサブスクリプションを移行する準備ができたら、サブスクリプションの所有者に電子メールが届きます。

> [!NOTE]
> ツールは段階的に展開されているため、初期段階では、一部のサブスクリプションがまだ移行できる状態ではない可能性があります。

ほとんどのサブスクリプションが、現在は移行準備完了としてマークされます。 まだ移行準備ができていないのは、以下のリソースの種類に対するクラシック アラート ルールがあるサブスクリプションのみです。

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>移行をトリガーできるユーザー

サブスクリプション レベルで、組み込みロールの監視共同作業者を割り当てられたすべてのユーザーが移行をトリガーできます。 また、次の権限を持つカスタム ロールが割り当てられたユーザーも移行をトリガーできます。

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> 上記のアクセス許可があることに加えてて、サブスクリプションはさらに、Microsoft.AlertsManagement リソース プロバイダーに登録されている必要があります。 これは、Application Insights に対する障害異常アラートを正常に移行するために必要です。 

## <a name="common-problems-and-remedies"></a>一般的な問題と解決方法

[移行をトリガー](alerts-using-migration-tool.md)したら、移行が完了したこと、または何らかのアクションが必要なことを通知するメールが、指定したアドレスに届きます。 このセクションでは、一般的な問題とその対処方法について説明します。

### <a name="validation-failed"></a>検証に失敗しました

サブスクリプション内のクラシック アラートに対する最近のいくつかの変更が原因で、サブスクリプションを移行できません。 これは一時的な問題です。 移行の状態が **[Ready for migration]\(移行準備完了\)** に戻ったら、移行を再開できます。

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>スコープ ロックによりルールを移行できない

移行の一環として、新しいメトリック アラートと新しいアクション グループが作成され、クラシック アラート ルールが削除されますが、 スコープ ロックによって、リソースの作成または削除が妨げられる可能性があります。 スコープ ロックによっては、一部またはすべてのルールを移行できない可能性があります。 この問題は、[移行ツール](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)に関するページに一覧表示されている、サブスクリプション、リソース グループ、またはリソースのスコープ ロックを削除し、移行を再度トリガーすることで解決できます。 スコープ ロックを無効にすることはできません。また、移行プロセスの間は削除する必要があります。 [スコープ ロックの管理の詳細はこちら](../../azure-resource-manager/management/lock-resources.md#portal)。

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>"Deny" 効果が使用されたポリシーによってルールの移行が妨げられる

移行の一環として、新しいメトリック アラートと新しいアクション グループが作成され、クラシック アラート ルールが削除されますが、 ポリシーによって、リソースの作成が妨げられる可能性があります。 ポリシーによっては、一部またはすべてのルールを移行できない可能性があります。 プロセスをブロックしているポリシーは、[移行ツール](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)に関するページに一覧表示されます。 この問題は、次のいずれかの方法で解決できます。

- 移行プロセスの間に、ポリシー割り当てからサブスクリプションまたはリソース グループを除外する。 [ポリシーの除外スコープの管理の詳細はこちら](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion)。
- 効果を削除するか、"audit" または "append" に変更する (これにより、たとえば、タグの欠落に関連する問題を解決できます)。 [ポリシーの効果の詳細はこちら](../../governance/policy/concepts/definition-structure.md#policy-rule)。

## <a name="next-steps"></a>次のステップ

- [移行ツールの使用方法](alerts-using-migration-tool.md)
- [移行を準備する](alerts-prepare-migration.md)
