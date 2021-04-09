---
title: レポート実行の再開 API
description: この API を使用して、一時停止されているコマーシャル マーケットプレース分析レポートのスケジュール実行を再開します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4a11783b28352cb62c5a3c0d38e45dcdc47a8d86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583551"
---
# <a name="resume-report-executions-api"></a>レポート実行の再開 API

この API を実行すると、一時停止されているコマーシャル マーケットプレース分析レポートのスケジュール実行が再開されます。

**要求の構文**

| Method | 要求 URI |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/resume/{reportId}` |
|||

**要求ヘッダー**

| ヘッダー | Type | 説明 |
| ------------ | ------------- | ------------- |
| 承認 | string | 必須。 `Bearer <token>` という形式の Azure Active Directory (Azure AD) アクセス トークン |
| Content-Type | string | `Application/JSON` |
||||

**パス パラメーター**

なし

**クエリ パラメーター**

| パラメーター名 | 必須 | Type | 説明 |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | はい | string | 変更されるレポートの ID |
|||||

**用語集**

None

**応答**

応答ペイロードは、JSON 形式で次のように構成されます。

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
| `ReportId` | 再開されたレポートの汎用一意識別子 (UUID) |
| `ReportName` | 作成時にレポートに指定された名前 |
| `Description` | レポートの作成時に指定された説明 |
| `QueryId` | レポートの作成時に渡されたクエリ ID |
| `Query` | このレポートに対して実行されるクエリ テキスト |
| `User` | レポートの作成に使用されたユーザー ID |
| `CreatedTime` | レポートが作成された時刻。 時刻の形式は yyyy-MM-ddTHH:mm:ssZ です |
| `ModifiedTime` | レポートが最後に変更された時刻。 時刻の形式は yyyy-MM-ddTHH:mm:ssZ です |
| `StartTime` | レポートの実行が開始される時刻。 時刻の形式は yyyy-MM-ddTHH:mm:ssZ です |
| `ReportStatus` | レポート実行のステータス。 有効な値は、一時停止、アクティブ、および非アクティブです。 |
| `RecurrenceInterval` | レポートの作成中に指定された繰り返し間隔 |
| `RecurrenceCount` | レポートの作成中に指定された繰り返し回数 |
| `CallbackUrl` | 要求で指定されたコールバック URL |
| `Format` | レポート ファイルの形式 |
|||
