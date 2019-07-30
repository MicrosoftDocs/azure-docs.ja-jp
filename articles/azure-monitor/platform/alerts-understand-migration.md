---
title: Azure Monitor アラートに対する自主的移行ツールのしくみを理解する
description: アラート移行ツールのしくみと問題のトラブルシューティングの方法について説明します。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: f981c14e26c51c427dab6b418cab8df46b1bb026
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302257"
---
# <a name="understand-how-the-migration-tool-works"></a>移行ツールの動作の理解

[以前発表された](monitoring-classic-retirement.md)ように、Azure Monitor のクラシック アラートは 2019 年 8 月 31 日までに廃止される予定です (当初は 2019 年 6 月 30 日でした)。 Azure portal には、クラシック アラート ルールを使用しているお客様と移行を自分でトリガーしたいと思っているお客様を対象に、移行ツールが用意されています。

この記事では、自主的移行ツールのしくみについて説明します。 また、一般的な問題をいくつか取り上げて、その解決方法を紹介します。

> [!NOTE]
> 移行ツールの展開が遅れたことで、クラシック アラート移行の提供終了日が、最初に発表された 2019 年 6 月 30 日から [2019 年 8 月 31 日に延長](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)されました。

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>移行されないクラシック アラート ルール

> [!IMPORTANT]
> アクティビティ ログのアラート (サービス正常性のアラートを含む) とログ アラートは移行の影響を受けません。 移行は、[ここ](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)で説明されているクラシック アラート ルールにのみ適用されます。

ほぼすべての[クラシック アラート ルール](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)をツールで移行できますが、例外がいくつかあります。 以下のアラート ルールは、ツールを使用して (または 2019 年 9 月 の自動移行開始時に) 移行されません。

- 仮想マシンのゲスト メトリックに対するクラシック アラート ルール (Windows と Linux の両方)。 この記事の後半の[新しいメトリック アラートでこれらのアラート ルールを再作成するためのガイダンス](#guest-metrics-on-virtual-machines)を参照してください。
- クラシック ストレージ メトリックに対するクラシック アラート ルール。 [お使いのクラシック ストレージ アカウントの監視に関するガイダンス](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)を参照してください。
- 一部のストレージ アカウント メトリックに対するクラシック アラート ルール。 この記事の後半で[詳細](#storage-account-metrics)をご確認ください。
- 一部の Cosmos DB メトリックに対するクラシック アラート ルール。 この記事の後半で[詳細](#cosmos-db-metrics)をご確認ください。
- すべてのクラシック仮想マシンおよびクラウド サービス メトリックに対するクラシック アラート ルール (Microsoft.ClassicCompute/virtualMachines および Microsoft.ClassicCompute/domainNames/slots/roles)。 この記事の後半で[詳細](#classic-compute-metrics)をご確認ください。

お使いのサブスクリプションにこれらのクラシック ルールが含まれている場合、そのルールは手動で移行する必要があります。 自動移行を提供することができないため、これらの種類の既存のクラシック メトリック アラートは 2020年 6 月まで動作し続ける予定です。 この拡張機能により、新しいアラートに移行する時間が確保されます。 ただし、2019 年 8 月を過ぎると、新しいクラシック アラートを作成することはできません。

> [!NOTE]
> 上に一覧で示した例外のほかに、クラシック アラート ルールが有効でない場合、つまり[非推奨のメトリック](#classic-alert-rules-on-deprecated-metrics)や削除済みのリソースに対するルールである場合、それらは自主的移行時に移行されません。 そのような無効なクラシック アラート ルールは、自動移行が行われるときにすべて削除されます。

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

パーセント メトリックに対するクラシック アラート ルールは、[従来のストレージ メトリックと新しいストレージ メトリック間のマッピング](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)に基づいて移行する必要があります。 使用可能な新しいメトリックは絶対値であるため、しきい値を適切に変更する必要があります。

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

クラシック コンピューティング リソースは新しいアラートでまだサポートされていないため、クラシック コンピューティング メトリックに関するアラートは移行ツールを使用しても移行されません。 これらのリソースの種類に対する新しいアラートのサポートは、今後追加される予定です。 追加されたら、お客様は 2020 年 6 月より前のクラシック アラート ルールに基づいて、新しい同等のアラート ルールを作成し直す必要があります。

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

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>ポリシーまたはスコープ ロックによりルールを移行できません

移行の一環として、新しいメトリック アラートと新しいアクション グループが作成され、クラシック アラート ルールが削除されますが、 ポリシーまたはスコープ ロックがあるため、リソースを作成できません。 ポリシーまたはスコープ ロックによって、一部またはすべてのルールを移行できませんでした。 この問題を解決するには、スコープ ロックまたはポリシーを一時的に削除して、移行を再度トリガーします。

## <a name="next-steps"></a>次の手順

- [移行ツールの使用方法](alerts-using-migration-tool.md)
- [移行を準備する](alerts-prepare-migration.md)
