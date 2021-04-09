---
title: レポートの更新 API
description: この API を使用して、コマーシャル マーケットプレース分析レポートのパラメーターをレポートします。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 38680eb291417ded4c2f93539e8d1ae091b1d560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583547"
---
# <a name="update-report-api"></a>レポートの更新 API

この API を使用すると、レポート パラメーターを変更できます。

**要求の構文**

| Method | 要求 URI |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

**要求ヘッダー**

| ヘッダー | Type | 説明 |
| ------------ | ------------- | ------------- |
| 承認 | string | 必須。 `Bearer <token>` という形式の Azure Active Directory (Azure AD) アクセス トークン |
| Content-Type | string | `Application/JSON` |
||||

**パス パラメーター**

なし

**Query parameter (クエリ パラメーター)**

| パラメーター名 | 必須 | Type | 説明 |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | はい | string | 変更されるレポートの ID |
|||||

**要求ペイロード**

```json
{
  "ReportName": "string",
  "Description": "string",
  "StartTime": "string",
  "RecurrenceInterval": 0,
  "RecurrenceCount": 0,
  "Format": "string",
  "CallbackUrl": "string"
}
```

**用語集**

次の表では、要求ペイロードの要素の主な定義を示しています。

| パラメーター | 必須 | 説明 | 使用できる値 |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | はい | レポートに割り当てられる名前 | string |
| `Description` | いいえ | 作成されたレポートの説明 | string |
| `StartTime` | はい | レポート生成が開始されるまでのタイムスタンプ | string |
| `RecurrenceInterval` | いいえ | レポートが生成される頻度 (時間単位)。 最小値は 4 です | 整数 (integer) |
| `RecurrenceCount` | いいえ | 生成されるレポートの数。 既定値は不定数です | 整数 (integer) |
| `Format` | はい | エクスポートされるファイルのファイル形式。 既定値は CSV です。 | CSV/TSV |
| `CallbackUrl` | はい | レポートの生成時に呼び出される https コールバック URL | string |
|||||

**用語集**

None

**応答**

応答ペイロードは、次のように構成されます。

応答コード: 200、400、401、403、404、500

応答ペイロード:

```json
{
  "Value": [
    {
      "ReportId": "string",
      "ReportName": "string",
      "Description": "string",
      "QueryId": "string",
      "Query": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string",
      "StartTime": "string",
      "ReportStatus": "string",
      "RecurrenceInterval": 0,
      "RecurrenceCount": 0,
      "CallbackUrl": "string",
      "Format": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**用語集**

| パラメーター | 説明 |
| ------------ | ------------- |
| `ReportId` | 更新されるレポートのユニバーサル一意識別子 (UUID) |
| `ReportName` | 要求ペイロードでレポートに指定された名前 |
| `Description` | レポートの作成時に指定された説明 |
| `QueryId` | レポートの作成時に渡されたクエリ ID |
| `Query` | このレポートに対して実行されるクエリ テキスト |
| `User` | レポートの作成に使用するユーザー ID |
| `CreatedTime` | レポートが作成された時刻。 時刻の形式は yyyy-MM-ddTHH:mm:ssZ です |
| `ModifiedTime` | レポートが最後に変更された時刻。 時刻の形式は yyyy-MM-ddTHH:mm:ssZ です |
| `StartTime` | レポートの実行が開始される時刻。 時刻の形式は yyyy-MM-ddTHH:mm:ssZ です |
| `ReportStatus` | レポート実行のステータス。 有効な値は、一時停止、アクティブ、および非アクティブです。 |
| `RecurrenceInterval` | レポートの作成中に指定された繰り返し間隔 |
| `RecurrenceCount` | レポートの作成中に指定された繰り返し回数 |
| `CallbackUrl` | 要求で指定されたコールバック URL |
| `Format` | レポート ファイルの形式 |
|||
