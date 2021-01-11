---
title: Azure Monitor Application Insights のワークスペース ベースのリソース
description: Azure Monitor Application Insights ワークスペース ベース リソースの新しいテーブルの構造とスキーマについて説明します。
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/09/2020
ms.openlocfilehash: 1d7275c928b4d25e200a3a8d3d690c7575c056e7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323181"
---
# <a name="workspace-based-resource-changes-preview"></a>ワークスペースベース リソースの変更 (プレビュー)

[ワークスペースベース Application Insights リソース](create-workspace-resource.md)を導入する前、Application Insights データは Azure Monitor の他のログ データとは別に保存されていました。 どちらも Azure Data Explorer に基づいており、同じ Kusto Query Language (KQL) が使用されています。 これに関する説明は「[Azure Monitor のログ](../platform/data-platform-logs.md)」にあります。

ワークスペースベースの Application Insights では、リソース データが他の監視データやアプリケーション データと共に Log Analytics ワークスペースに格納されます。 複数のソリューションをまたいでデータを分析することが簡単になり、ワークスペースの機能を活用できるため、構成が簡単になります。

## <a name="table-structure"></a>テーブル構造

| レガシ テーブル名 | 新しいテーブル名 | 説明 |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  可用性テストの要約データ。|
| browserTimings | AppBrowserTimings | 受信データの処理にかかった時間などのクライアントのパフォーマンスに関するデータ。|
| dependencies | AppDependencies | TrackDependency () を使用して記録された他のコンポーネント (外部コンポーネントを含む) へのアプリケーションからの呼び出し (REST API、データベース、またはファイル システムへの呼び出しなど)。  |
| customEvents | AppEvents | アプリケーションで作成されたカスタム イベント。 |
| customMetrics | AppMetrics | アプリケーションで作成されたカスタム メトリック。 |
| pageViews | AppPageViews| 各 Web サイトのビューに関するデータとブラウザー情報。 |
| performanceCounters | AppPerformanceCounters | Windows のパフォーマンス カウンターなど、アプリケーションをサポートするコンピューティング リソースのパフォーマンス測定結果。 |
| requests | AppRequests | お使いのアプリケーションで受信された要求。 たとえば、お使いの Web アプリが受信する HTTP 要求ごとに、個別の要求レコードがログ記録されます。  |
| exceptions | AppSystemEvents | アプリケーションのランタイムからスローされた例外。サーバー側とクライアント側 (ブラウザー) の両方の例外がキャプチャされます。 |
| traces | AppTraces | TrackTrace () を使用して記録された、アプリケーション コード/ログ記録フレームワークによって出力された詳細なログ (トレース)。 |

## <a name="table-schemas"></a>テーブル スキーマ

後続のセクションは、従来のプロパティ名とワークスペースベース Application Insights の新しいプロパティ名の対照表になっています。  レガシ テーブルを使用してクエリを変換するとき、この情報を使用します。

ほとんどの例には、大文字と小文字が異なる同じ名前が含まれています。 KQL では大文字と小文字が区別されるため、既存クエリのテーブル名に合わせて列名をそれぞれ変更する必要があります。 大文字と小文字表記に加え、変更がある列は強調表示されています。 ワークスペースベースのリソースの場合でも、Application Insights リソースの **[ログ]** ペイン内では従来の Application Insights クエリを引き続き使用できます。 Log Analytics ワークスペースベース エクスペリエンスのコンテキスト内からクエリするとき、新しいプロパティ名が必要になります。

### <a name="appavailabilityresults"></a>AppAvailabilityResults

レガシ テーブル: 可用性

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|動的|Properties|動的|
|customMeasurements|動的|測定|動的|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|String|
|location|string|場所|string|
|message|string|Message|string|
|name|string|名前|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|size|real|サイズ|real|
|success|string|Success|Bool|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appbrowsertimings"></a>AppBrowserTimings

レガシ テーブル: browserTimings

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|動的|Properties|動的|
|customMeasurements|動的|測定|動的|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|名前|DATETIME|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|sdkVersion|string|SdkVersion|string|
|sendDuration|real|SendDurationMs|real|
|session_Id|string|SessionId|string|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|totalDuration|real|TotalDurationMs|real|
|url|string|url|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appdependencies"></a>AppDependencies

レガシ テーブル: dependencies

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|動的|Properties|動的|
|customMeasurements|動的|測定|動的|
|data|string|Data|string|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|String|
|name|string|名前|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|resultCode|string|ResultCode|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|success|string|Success|Bool|
|ターゲット (target)|string|移行先|string|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|type|string|DependencyType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appevents"></a>AppEvents

レガシ テーブル: customEvents

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|動的|Properties|動的|
|customMeasurements|動的|測定|動的|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|名前|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appmetrics"></a>AppMetrics

レガシ テーブル: customMetrics

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|動的|Properties|動的|
|iKey|string|IKey|string|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|名前|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|
|value|real|(削除済み)||
|valueCount|INT|ValueCount|INT|
|valueMax|real|ValueMax|real|
|valueMin|real|ValueMin|real|
|valueStdDev|real|ValueStdDev|real|
|valueSum|real|ValueSum|real|

### <a name="apppageviews"></a>AppPageViews

レガシ テーブル: pageViews

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|動的|Properties|動的|
|customMeasurements|動的|測定|動的|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|String|
|name|string|名前|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|url|string|url|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appperformancecounters"></a>AppPerformanceCounters

レガシ テーブル: performanceCounters

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|category|string|カテゴリ|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|counter|string|(削除済み)||
|customDimensions|動的|Properties|動的|
|iKey|string|IKey|string|
|instance|string|インスタンス|string|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|名前|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|
|value|real|値|real|

### <a name="apprequests"></a>AppRequests

レガシ テーブル: requests

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|動的|Properties|動的|
|customMeasurements|動的|測定|動的|
|duration|real|DurationMs|Real|
|`id`|string|`Id`|String|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|String|
|name|string|名前|String|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|String|
|resultCode|string|ResultCode|String|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|source|string|source|String|
|success|string|Success|Bool|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|url|string|url|String|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appsystemevents"></a>AppSystemEvents

レガシ テーブル: exceptions

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|アセンブリ (assembly)|string|アセンブリ|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|動的|Properties|動的|
|customMeasurements|動的|測定|動的|
|details|動的|詳細|動的|
|handledAt|string|HandledAt|string|
|iKey|string|IKey|string|
|innermostAssembly|string|InnermostAssembly|string|
|innermostMessage|string|InnermostMessage|string|
|innermostMethod|string|InnermostMethod|string|
|innermostType|string|InnermostType|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|message|string|Message|string|
|method|string|Method|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|outerAssembly|string|OuterAssembly|string|
|outerMessage|string|OuterMessage|string|
|outerMethod|string|OuterMethod|string|
|outerType|string|OuterType|string|
|problemId|string|ProblemId|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|severityLevel|INT|SeverityLevel|INT|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|type|string|ExceptionType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="apptraces"></a>AppTraces

レガシ テーブル: traces

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|動的|Properties|動的|
|customMeasurements|動的|測定|動的|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|message|string|Message|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|severityLevel|INT|SeverityLevel|INT|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

## <a name="next-steps"></a>次のステップ

* [メトリックを探索する](../platform/metrics-charts.md)
* [Analytics クエリを作成する](../log-query/log-query-overview.md)

