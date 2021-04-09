---
title: Azure Monitor アラートのための移行について
description: アラート移行のしくみと問題のトラブルシューティングの方法について説明します。
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
author: yalavi
ms.openlocfilehash: 88b75c46898e1a74b33051d8996af96201f639c7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037712"
---
# <a name="understand-migration-options-to-newer-alerts"></a>新しいアラートへの移行オプションについて

パブリック クラウド ユーザー向けの従来のアラートは [廃止](./monitoring-classic-retirement.md)されていますが、**2021 年 5 月 31 日** までは引き続き利用できます。 Azure Government クラウドおよび Azure China 21Vianet 向けの従来のアラートは、**2024 年 2 月 29 日** に廃止されます。

この記事では、手動移行と自発的移行ツールがどのように機能するかについて説明します。このツールは、残りのアラート ルールを移行するために使用されます。 また、一般的な問題をいくつか取り上げて、その解決方法を紹介します。

> [!IMPORTANT]
> アクティビティ ログのアラート (サービス正常性のアラートを含む) とログ アラートは移行の影響を受けません。 移行は、[ここ](./monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)で説明されているクラシック アラート ルールにのみ適用されます。

> [!NOTE]
> クラシック アラート ルールが有効でない、つまり、[非推奨のメトリック](#classic-alert-rules-on-deprecated-metrics)や削除済みのリソースについてのルールである場合、それらは移行されず、サービスの廃止後は使用できなくなります。

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>従来のアラートの新しいアラートへの手動移行

残りのアラートを手動で移行することに関心のあるお客様は、以降のセクションを参考にして、それを既に実行することができます。 また、それには、廃止されているため直接移行できないメトリックも含まれます。

### <a name="guest-metrics-on-virtual-machines"></a>仮想マシンのゲスト メトリック

ゲスト メトリックに新しいメトリック アラートを作成するには、その前にゲスト メトリックを Azure Monitor ログ ストアに送信する必要があります。 アラートを作成するには、以下の手順のようにします。

- [Log Analytics へのゲストメトリック収集の有効化](../agents/agent-data-sources.md)
- [Azure Monitor でのログ アラートの作成](./alerts-log.md)

ゲスト メトリックとそれに対するアラートを収集するオプションは他にもあります。詳細については、[こちらを参照](../agents/agents-overview.md)してください。

### <a name="storage-and-classic-storage-account-metrics"></a>ストレージと従来のストレージのアカウントのメトリック

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
- 1 分あたりの最大 RUPM 消費量
- 1 秒あたりの最大 RU 数
- その他の Mongo 要求の料金
- その他の Mongo 要求のレート
- 測定された読み取り待機時間
- 測定された書き込み待機時間
- サービスの可用性
- ストレージの容量

1 秒あたりの平均要求数、整合性レベル、1 分あたりの最大 RUPM 消費量、1 秒あたりの最大 RU 数、測定された読み込み待機時間、測定された書き込み待機時間、ストレージ容量は現在、[新しいシステム](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts)で使用できません。

HTTP 2xx、HTTP 3xx、サービス可用性などの要求メトリックに関するアラートは、要求のカウント方法がクラシックのメトリックと新しいメトリックの間で異なるため、移行されません。 これらにメトリックに関するアラートは、しきい値を調整して手動で再作成する必要があります。

### <a name="classic-alert-rules-on-deprecated-metrics"></a>非推奨のメトリックに対するクラシック アラート ルール

以下は、以前はサポートされていましたが、結局非推奨とされたメトリックに対するクラシック アラート ルールです。 ごく一部のお客様は、このようなメトリックに対する無効なクラシック アラート ルールを持っている場合があります。 これらのアラート ルールは無効であるため、移行されません。

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

- **頻度**: クラシック アラート ルールまたは新しいアラート ルールが条件をチェックする頻度を定義します。 クラシック アラート ルールの `frequency` は、ユーザーは構成できず、すべてのリソースの種類で常に 5 分でした。 同等のルールの頻度も 5 分に設定されます。
- **集計の種類**: 関心のあるウィンドウ全体にわたるメトリックの集計方法を定義します。 `aggregationType` も、ほとんどのメトリックについて、クラシック アラートと新しいアラートの間に違いはありません。 場合によってはクラシック アラートと新しいアラートの間でメトリックが異なるため、そうしたメトリックに対して定義される同等の `aggregationType` または `primary Aggregation Type` が使用されます。
- **単位**: アラートが作成される対象のメトリックのプロパティ。 一部の同等メトリックの単位は異なっています。 しきい値は、必要に応じて適切に調整されます。 たとえば、元のメトリックが秒単位であるのに、同等の新しいメトリックがミリ秒単位である場合、確実に同じ動作となるように、元のしきい値には 1000 が掛けられます。
- **ウィンドウ サイズ**: しきい値と比較するため、メトリック データを集計するウィンドウを定義します。 5 分、15 分、30 分、1 時間、3 時間、6 時間、12 時間、1 日といった標準の `windowSize` 値では、同等の新しいアラート ルールのために行われた変更はありません。 その他の値の場合、最も近い `windowSize` が使用されます。 ほとんどのお客様にとって、この変更の影響はありません。 ごく一部のお客様については、まったく同じ動作を得るためにしきい値の調整が必要な可能性があります。

以降のセクションでは、新しいシステムで別個かつ同等のメトリックを持つメトリックについて詳しく説明します。 クラシック アラート ルールと新しいアラート ルールで同じままのメトリックはすべて、一覧に記載されていません。 新しいシステムでサポートされるメトリックの一覧は、[ここ](../essentials/metrics-supported.md)にあります。

### <a name="microsoftstoragestorageaccounts-and-microsoftclassicstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts と Microsoft.ClassicStorage/storageAccounts

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

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Cosmos DB の場合、同等のメトリックは以下に示すようになっています。

| クラシック アラートでのメトリック | 新しいアラートでの同等メトリック | 説明|
|--------------------------|---------------------------------|---------|
| AvailableStorage | AvailableStorage||
| データ サイズ | DataUsage| |
| ドキュメント数 | DocumentCount||
| インデックス サイズ | IndexUsage||
| サービス利用不可 | ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||
| 調整された要求数 | ディメンション "StatusCode" = "429" の TotalRequests| 集計の種類 "Average" は "Count" に修正されました|
| 内部サーバー エラー | ディメンション "StatusCode" = "500" の TotalRequests| 集計の種類 "Average" は "Count" に修正されました|
| HTTP 401 | ディメンション "StatusCode" = "401" の TotalRequests| 集計の種類 "Average" は "Count" に修正されました|
| HTTP 400 | ディメンション "StatusCode" = "400" の TotalRequests| 集計の種類 "Average" は "Count" に修正されました|
| 要求の合計数 | TotalRequests| 集計の種類 "Max" は "Count" に修正されました|
| Mongo Count 要求の料金| ディメンション "CommandName" = "count" のある MongoRequestCharge||
| Mongo Count 要求のレート | ディメンション "CommandName" = "count" のある MongoRequestsCount||
| Mongo Delete 要求の料金 | ディメンション "CommandName" = "delete" のある MongoRequestCharge||
| Mongo Delete 要求のレート | ディメンション "CommandName" = "delete" のある MongoRequestsCount||
| Mongo Insert 要求の料金 | ディメンション "CommandName" = "insert" のある MongoRequestCharge||
| Mongo Insert 要求のレート | ディメンション "CommandName" = "insert" のある MongoRequestsCount||
| Mongo Query 要求の料金 | ディメンション "CommandName" = "find" のある MongoRequestCharge||
| Mongo Query 要求のレート | ディメンション "CommandName" = "find" のある MongoRequestsCount||
| Mongo Update 要求の料金 | ディメンション "CommandName" = "update" のある MongoRequestCharge||
| 失敗した Mongo Insert 要求 | ディメンション "CommandName" = "insert" および "Status" = "failed" の MongoRequestCount| 集計の種類 "Average" は "Count" に修正されました|
| 失敗した Mongo Query 要求 | ディメンション "CommandName" = "query" および "Status" = "failed" の MongoRequestCount| 集計の種類 "Average" は "Count" に修正されました|
| 失敗した Mongo Count 要求 | ディメンション "CommandName" = "count" および "Status" = "failed" の MongoRequestCount| 集計の種類 "Average" は "Count" に修正されました|
| 失敗した Mongo Update 要求 | ディメンション "CommandName" = "update" および "Status" = "failed" の MongoRequestCount| 集計の種類 "Average" は "Count" に修正されました|
| 失敗したその他の Mongo 要求 | ディメンション "CommandName" = "other" および "Status" = "failed" の MongoRequestCount| 集計の種類 "Average" は "Count" に修正されました|
| 失敗した Mongo Delete 要求 | ディメンション "CommandName" = "delete" および "Status" = "failed" の MongoRequestCount| 集計の種類 "Average" は "Count" に修正されました|

### <a name="how-equivalent-action-groups-are-created"></a>同等のアクション グループはどのように作成されるか

クラシック アラート ルールには、アラート ルール自体に関連付けられているメール、Webhook、ロジック アプリ、Runbook のアクションがあります。 新しいアラート ルールでは、複数のアラート ルールにわたって再利用できるアクション グループが使用されます。 そのアクションをいくつのアラート ルールが使用しているかに関わらず、移行ツールにより同じアクションに対して 1 つのアクション グループが作成されます。 移行ツールによって作成されたアクション グループは、"Migrated_AG*" という名前付け形式を使用します。

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

移行の一環として、新しいメトリック アラートと新しいアクション グループが作成され、クラシック アラート ルールが削除されますが、 スコープ ロックによって、リソースの作成または削除が妨げられる可能性があります。 スコープ ロックによっては、一部またはすべてのルールを移行できない可能性があります。 この問題は、[移行ツール](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)に関するページに一覧表示されている、サブスクリプション、リソース グループ、またはリソースのスコープ ロックを削除し、移行を再度トリガーすることで解決できます。 スコープ ロックを無効にすることはできず、移行プロセスの間は削除する必要があります。 [スコープ ロックの管理の詳細はこちら](../../azure-resource-manager/management/lock-resources.md#portal)。

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>"Deny" 効果が使用されたポリシーによってルールの移行が妨げられる

移行の一環として、新しいメトリック アラートと新しいアクション グループが作成され、クラシック アラート ルールが削除されますが、 [Azure ポリシー](../../governance/policy/index.yml)の割り当てによって、リソースの作成が妨げられる可能性があります。 ポリシーの割り当てによっては、一部またはすべてのルールを移行できない可能性があります。 プロセスをブロックしているポリシー割り当ては、[移行ツール](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)に関するページに一覧表示されます。 この問題は、次のいずれかの方法で解決できます。

- 移行プロセスの間に、ポリシー割り当てからサブスクリプション、リソース グループまたは個別のリソースを除外します。 [ポリシーの除外スコープの管理の詳細](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion)。
- ポリシー割り当てに対して "強制モード" を **Disabled (無効)** に設定します。 [ポリシー割り当ての enforcementMode プロパティの詳細](../../governance/policy/concepts/assignment-structure.md#enforcement-mode)
- サブスクリプション、リソースグループ、または個々のリソースの Azure Policy 除外 (プレビュー) をポリシー割り当てに設定します。 [Azure Policy の除外構造の詳細](../../governance/policy/concepts/exemption-structure.md)
- 効果を削除するか、「無効」、「監査」、「追加」または「修正」 に変更する (これにより、たとえば、タグの欠落に関連する問題を解決できます)。 [ポリシー効果の管理い関する詳細](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>次のステップ

- [移行ツールの使用方法](alerts-using-migration-tool.md)
- [移行を準備する](alerts-prepare-migration.md)