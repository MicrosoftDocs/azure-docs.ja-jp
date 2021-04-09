---
title: プログラムによるアクセスのパラダイム
description: プログラムによる分析のための API 呼び出しパターンについて、大まかな流れを説明します。 Microsoft コマーシャル マーケットプレースの分析レポートにアクセスするための API についても説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8e0b94a46e96dd8ba16040e16b421520eb67de19
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583612"
---
# <a name="programmatic-access-paradigm"></a>プログラムによるアクセスのパラダイム

この図は、新しいレポート テンプレートを作成し、カスタム レポートのスケジュールを設定して、エラー データを取得するために使用される API 呼び出しパターンを示しています。

[![新しいレポート テンプレートを作成し、カスタム レポートのスケジュールを設定し、エラー データを取得するために使用される API 呼び出しパターンを示しています。](./media/analytics-programmatic-access/analytics-high-level-flow.png)](./media/analytics-programmatic-access/analytics-high-level-flow.png#lightbox)
***図 1: API 呼び出しパターンの大まかな流れ***

この一覧で、図 1 の詳細を説明します。

1. クライアント アプリケーションでは、[レポート クエリの作成 API](#create-report-query-api) を呼び出すことによって、カスタム レポート スキーマ/テンプレートを定義できます。 または、[システム クエリの一覧](analytics-system-queries.md)からレポート テンプレート (`QueryId`) を使用することもできます。
1. 成功した場合、レポート テンプレートの作成 API から `QueryId` が返されます。
1. その後、クライアント アプリケーションでは、`QueryID` と共にレポートの開始日、繰り返し間隔、繰り返し回数、およびオプションのコールバック URI を使用して、[レポートの作成 API](#create-report-api) を呼び出します。
1. 成功した場合、[レポートの作成 API](#create-report-api) から `ReportID` が返されます。
1. クライアント アプリケーションでは、レポート データのダウンロード準備が完了するとすぐに、コールバック URI で通知を受け取ります。
1. その後、クライアント アプリケーションで [レポート実行状態の取得 API](#get-report-executions-api) を使用し、`Report ID` と日付範囲を指定してレポートの状態のクエリを実行します。
1. 成功すると、レポートのダウンロード リンクが返され、アプリケーションでデータのダウンロードを開始できます。

## <a name="report-query-language-specification"></a>レポート クエリ言語の仕様

レポートの作成に使用できる[システム クエリ](analytics-system-queries.md)が用意されていますが、ビジネス ニーズに基づいて独自のクエリを作成することもできます。 カスタム クエリの詳細については、「[カスタム クエリの仕様](analytics-custom-query-specification.md)」を参照してください。

## <a name="create-report-query-api"></a>レポート クエリの作成 API

この API では、列およびメトリックのエクスポート元となるデータセットを定義するカスタム クエリを作成できます。 この API は、ビジネス ニーズに基づいて新しいレポート テンプレートを作成するための柔軟性を提供します。

また、用意されている[システム クエリ](analytics-system-queries.md)を使用することもできます。 カスタム レポート テンプレートが不要な場合は、用意されているシステム クエリの [QueryId](analytics-system-queries.md) を使用して、[レポートの作成 API](#create-report-api) を直接呼び出すことができます。

次の例では、過去 1 か月間の [ISVUsage](analytics-make-your-first-api-call.md#programmatic-api-call) データセットから _有料 SKU の正規化された使用と推定料金_ を取得するカスタム クエリを作成する方法を示します。

*要求の構文*

| Method | 要求 URI |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries` |
|||

*要求ヘッダー*

| ヘッダー | 種類 | 説明 |
| ------------- | ------------- | ------------- |
| 承認 | string | 必須。 Azure Active Directory (Azure AD) アクセス トークン。 形式は `Bearer <token>` です。 |
| Content-Type | `string` | `application/JSON` |
||||

*パス パラメーター*

なし

*Query parameter (クエリ パラメーター)*

なし

*要求ペイロードの例*

```json
{
    "Name": "ISVUsageQuery",
    "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
    "Query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"
}
```

*用語集*

次の表では、要求ペイロードの要素の主な定義を示しています。

| パラメーター | 必須 | 説明 | 使用できる値 |
| ------------ | ------------- | ------------- | ------------- |
| `Name` | はい | クエリのフレンドリ名 | string |
| `Description` | いいえ | クエリが返すものの説明 | string |
| `Query` | はい | レポート クエリ文字列 | データ型: 文字列<br>ビジネス ニーズに基づく[カスタム クエリ](analytics-sample-queries.md) |
|||||

> [!NOTE]
> カスタム クエリのサンプルについては、[サンプル クエリの例](analytics-sample-queries.md)に関するページを参照してください。

*応答のサンプル*

応答ペイロードは、次のように構成されます。

応答コード: 200、400、401、403、500

応答ペイロードの例:

```json
{
  "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": " ISVUsageQuery",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs”,
            "query": " SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34Z"
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

*用語集*

次の表では、応答の要素の主な定義を示しています。

| パラメーター | 説明 |
| ------------ | ------------- |
| `QueryId` | 作成したクエリの汎用一意識別子 (UUID) |
| `Name` | 要求ペイロード内のクエリに指定されたフレンドリ名 |
| `Description` | クエリの作成時に指定された説明 |
| `Query` | クエリの作成中に入力として渡されたレポート クエリ |
| `Type` | `userDefined` |
| `User` | クエリの作成に使用するユーザー ID |
| `CreatedTime` | クエリが作成された UTC 時刻で、形式は yyyy-MM-ddTHH:mm:ssZ |
| `TotalCount` | Value 配列内のデータセットの数 |
| `StatusCode` | 結果コード<br>200、400、401、403、500 の値になる可能性があります。 |
| `message` | API の実行からのステータス メッセージ |
|||

## <a name="create-report-api"></a>レポートの作成 API

カスタム レポート テンプレートを正常に作成し、`QueryID` を [レポート クエリの作成](#create-report-query-api)応答の一部として受け取ると、この API を呼び出して一定の間隔でクエリを実行するようにスケジュールを設定できます。 配信するレポートの頻度とスケジュールを設定できます。 用意されているシステム クエリの場合は、[QueryId](analytics-sample-queries.md) を使用してレポートの作成 API を呼び出すこともできます。

*要求の構文*

| Method | 要求 URI |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport` |
|||

*要求ヘッダー*

| ヘッダー | 種類 | 説明 |
| ------ | ---- | ----------- |
| 承認 | string | 必須。 Azure Active Directory (Azure AD) アクセス トークン。 形式は `Bearer <token>` です。 |
| コンテンツの種類 | string | `application/JSON` |
||||

*パス パラメーター*

なし

*Query parameter (クエリ パラメーター)*

なし

*要求ペイロードの例*

```json
{
  "ReportName": "ISVUsageReport",
  "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
  "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c ",
  "StartTime": "2021-01-06T19:00:00Z ",
  "RecurrenceInterval": 48,
  "RecurrenceCount": 20,
  "Format": "csv",
  "CallbackUrl": "https://<SampleCallbackUrl>"
}
```

*用語集*

次の表では、要求ペイロードの要素の主な定義を示しています。

| パラメーター | 必須 | 説明 | 使用できる値 |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | はい | レポートに割り当てられた名前 | string |
| `Description` | いいえ | 作成されたレポートの説明 | string |
| `QueryId` | はい | レポート クエリ ID | string |
| `StartTime` | はい | レポート生成が開始される UTC タイムスタンプ。<br>yyyy-MM-ddTHH:mm:ssZ の形式にする必要があります | string |
| `RecurrenceInterval` | はい | レポートが生成される頻度 (時間単位)。<br>最小値は 4、最大値は 90 です。 | 整数 (integer) |
| `RecurrenceCount` | いいえ | 生成されるレポートの数。 | 整数 (integer) |
| `Format` | いいえ | エクスポートされるファイルのファイル形式。<br>既定の形式は .CSV です。 | CSV/TSV |
| `CallbackUrl` | いいえ | オプションでコールバック先として構成できる、パブリックに到達可能な URL。 | 文字列 (http URL) |
| `ExecuteNow` | いいえ | このパラメーターは、1 回だけ実行されるレポートを作成するために使用される必要があります。 これが `true` に設定されている場合、`StartTime`、`RecurrenceInterval`、および `RecurrenceCount` は無視されます。 レポートは、非同期に即時実行されます。 | true/false |
| `QueryStartTime` | いいえ | 必要に応じて、データを抽出するクエリの開始時刻を指定します。 このパラメーターは、`ExecuteNow` が `true` に設定されている 1 回限りの実行レポートにのみ適用されます。 yyyy-MM-ddTHH:mm:ssZ の形式にする必要があります | 文字列としてのタイムスタンプ |
| `QueryEndTime` | いいえ | 必要に応じて、データを抽出するクエリの終了時刻を指定します。 このパラメーターは、`ExecuteNow` が `true` に設定されている 1 回限りの実行レポートにのみ適用されます。 yyyy-MM-ddTHH:mm:ssZ の形式にする必要があります | 文字列としてのタイムスタンプ |
|||||

*応答のサンプル*

応答ペイロードは、次のように構成されます。

応答コード: 200、400、401、403、404、500

応答ペイロード:

```json
{
  "Value": [
    {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVUsageReport",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": "https://<SampleCallbackUrl>",
            "format": "csv"    
    }
  ],
  "TotalCount": 1,
  "Message": "Report created successfully",
  "StatusCode": 200
}
```

*用語集*

次の表では、応答の要素の主な定義を示しています。

| パラメーター | 説明 |
| ------------ | ------------- |
| `ReportId` | 作成したレポートの汎用一意識別子 (UUID) |
| `ReportName` | 要求ペイロードでレポートに指定された名前 |
| `Description` | レポートの作成時に指定された説明 |
| `QueryId` | レポートの作成時に渡されたクエリ ID |
| `Query` | このレポートに対して実行されるクエリ テキスト |
| `User` | レポートの作成に使用するユーザー ID |
| `CreatedTime` | レポートが作成された UTC 時刻で、形式は yyyy-MM-ddTHH:mm:ssZ |
| `ModifiedTime` | レポートが最終更新された UTC 時刻で、形式は yyyy-MM-ddTHH:mm:ssZ |
| `StartTime` | レポート実行が開始する UTC 時刻で、形式は yyyy-MM-ddTHH:mm:ssZ |
| `ReportStatus` | レポート実行の状態。 有効な値は、**Paused**、**Active**、および **Inactive** です。 |
| `RecurrenceInterval` | レポートの作成中に指定された繰り返し間隔 |
| `RecurrenceCount` | レポートの作成中に指定された繰り返し回数 |
| `CallbackUrl` | 要求で指定されたコールバック URL |
| `Format` | レポート ファイルの形式。 有効な値は CSV または TSV です。 |
| `TotalCount` | Value 配列内のデータセットの数 |
| `StatusCode` | 結果コード<br>200、400、401、403、500 の値になる可能性があります。 |
| `message` | API の実行からのステータス メッセージ |
|||

## <a name="get-report-executions-api"></a>レポート実行の取得 API

このメソッドを使用すると、[レポートの作成 API](#create-report-api) から受信した `ReportId` を使用して、レポート実行状態のクエリを実行できます。 レポートをダウンロードする準備ができている場合、このメソッドでレポートのダウンロード リンクが返されます。 それ以外の場合は、メソッドから状態が返されます。 また、この API を使用して、特定のレポートで発生したすべての実行を取得することもできます。

> [!IMPORTANT]
> この API には、既定のクエリ パラメーターとして `executionStatus=Completed` および `getLatestExecution=true` が設定されています。 このため、レポート実行が最初に成功する前にこの API を呼び出すと、404 が返されます。 保留中の実行は、`executionStatus=Pending` を設定することによって取得できます。

*要求の構文*

| Method | 要求 URI |
| ------------ | ------------- |
| 取得 | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/{reportId}?executionId={executionId}&executionStatus={executionStatus}&getLatestExecution={getLatestExecution}` |
|||

*要求ヘッダー*

| ヘッダー | 種類 | 説明 |
| ------ | ------ | ------ |
| 承認 | string | 必須。 Azure Active Directory (Azure AD) アクセス トークン。 形式は `Bearer <token>` です。 |
| Content type | string | `application/json` |
||||

*パス パラメーター*

なし

*Query parameter (クエリ パラメーター)*

| パラメーター名 | 必須 | 種類 | 説明 |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | はい | string | この引数で指定された `reportId` を持つレポートのみの実行の詳細を取得するフィルター。 複数の `reportIds` を指定する場合は、セミコロン ";" で区切ります。 |
| `executionId` | いいえ | string | この引数で指定された `executionId` を持つレポートのみの詳細を取得するフィルター。 複数の `executionIds` を指定する場合は、セミコロン ";" で区切ります。 |
| `executionStatus` | いいえ | string/enum | この引数で指定された `executionStatus` を持つレポートのみの詳細を取得するフィルター。<br>有効な値は、`Pending`、`Running`、`Paused`、`Completed` です <br>既定値は `Completed` です。 複数の状態を指定する場合は、セミコロン ";" で区切ります。 |
| `getLatestExecution` | いいえ | boolean | この API から最新のレポート実行の詳細が返されます。<br>既定では、このパラメーターは `true` に設定されます。 このパラメーターの値を `false` として渡すことを選択した場合、この API から最後の 90 日間の実行インスタンスが返されます。 |
|||||

*要求ペイロード*

なし

*応答のサンプル*

応答ペイロードは、次のように構成されます。

応答コード: 200、400、401、403、404、500

応答ペイロードの例:

```json
{
    "value": [
        {
            "executionId": "a0bd78ad-1a05-40fa-8847-8968b718d00f",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 4,
            "recurrenceCount": 10,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Completed",
            "reportAccessSecureLink": "https://<path to report for download>",
            "reportExpiryTime": null,
            "reportGeneratedTime": "2021-01-13T14:40:46Z"
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

レポートの実行が完了すると、実行状態 `Completed` が表示されます。 `reportAccessSecureLink` で URL を選択して、レポートをダウンロードできます。

*用語集*

応答内の要素の主な定義。

| パラメーター | 説明 |
| ------------ | ------------- |
| `ExecutionId` | 実行インスタンスの汎用一意識別子 (UUID) |
| `ReportId` | 実行インスタンスに関連付けられているレポート ID |
| `RecurrenceInterval` | レポートの作成中に指定された繰り返し間隔 |
| `RecurrenceCount` | レポートの作成中に指定された繰り返し回数 |
| `CallbackUrl` | 実行インスタンスに関連付けられているコールバック URL |
| `Format` | 実行の終了時に生成されるファイルの形式 |
| `ExecutionStatus` | レポート実行インスタンスの状態。<br>有効な値は、`Pending`、`Running`、`Paused`、`Completed` です |
| `ReportAccessSecureLink` | レポートに安全にアクセスできるリンク |
| `ReportExpiryTime` | レポート リンクの有効期限が切れる UTC 時刻で、形式は yyyy-MM-ddTHH:mm:ssZ |
| `ReportGeneratedTime` | レポートが生成された UTC 時刻で、形式は yyyy-MM-ddTHH:mm:ssZ |
| `TotalCount` | Value 配列内のデータセットの数 |
| `StatusCode` | 結果コード <br>200、400、401、403、404、500 の値になる可能性があります |
| `message` | API の実行からのステータス メッセージ |
|||

## <a name="next-steps"></a>次のステップ
- [Swagger API URL](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html) を使用して API を試すことができます
- 分析データへのプログラムによるアクセスの概要
