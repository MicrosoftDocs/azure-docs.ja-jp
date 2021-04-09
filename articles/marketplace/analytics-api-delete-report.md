---
title: レポートの削除 API
description: この API を使用して、コマーシャル マーケットプレースの分析レポート用のレポートおよびレポート実行レコードをすべて削除します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7c39f8bc0db44f1d8aa885969ca09d90b0dcd332
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583625"
---
# <a name="delete-report-api"></a>レポートの削除 API

実行時には、この API によってすべてのレポートおよびレポート実行レコードが削除されます。

**要求の構文**

| Method | 要求 URI |
| ------------ | ------------- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

**要求ヘッダー**

| ヘッダー | 種類 | 説明 |
| ------------ | ------------- | ------------- |
| 承認 | string | 必須。 `Bearer <token>` という形式の Azure AD アクセス トークン |
| コンテンツの種類 | string | `Application/JSON` |
||||

**パス パラメーター**

なし

**クエリ パラメーター**

| パラメーター名 | 必須 | string | 説明 |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | はい | string | 変更されるレポートの ID |
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
| `ReportId` | 削除されたレポートの一意の UUID |
| `ReportName` | 作成時にレポートに指定された名前 |
| `Description` | レポートの作成時に指定された説明 |
| `QueryId` | レポートの作成時に渡されたクエリ ID |
| `Query` | このレポートに対して実行されるクエリ テキスト |
| `User` | レポートの作成に使用するユーザー ID |
| `CreatedTime` | レポートが作成された時刻。 時刻の形式は yyyy-MM-ddTHH:mm:ssZ です |
| `ModifiedTime` | レポートが最後に変更された時刻。 時刻の形式は yyyy-MM-ddTHH:mm:ssZ です |
| `StartTime` | レポートの実行が開始される時刻。 時刻の形式は yyyy-MM-ddTHH:mm:ssZ です |
| `ReportStatus` | レポート実行の状態。 有効な値は、Paused、Active、および Inactive です。 |
| `RecurrenceInterval` | レポートの作成中に指定された繰り返し間隔 |
| `RecurrenceCount` | レポートの作成中に指定された繰り返し回数 |
| `CallbackUrl` | 要求で指定されたコールバック URL |
| `Format` | レポート ファイルの形式 |
|||
