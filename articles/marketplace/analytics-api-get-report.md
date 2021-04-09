---
title: レポートの取得 API
description: この API を使用して、パートナー センターでスケジュールされている分析レポートを取得します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 3383af447f40ea984bce9cbc956f22ee6c5af200
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583594"
---
# <a name="get-report-api"></a>レポートの取得 API

この API では、スケジュールされているすべてのレポートを取得します。

**要求の構文**

| **方法** | **要求 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport?reportId={Report ID}&reportName={Report Name}&queryId={Query ID} ` |

**要求ヘッダー**

| **Header** | **Type** | **説明** |
| --- | --- | --- |
| 承認 | string | 必須。 `Bearer <token>` という形式の Azure Active Directory (Azure AD) アクセス トークン |
| Content-Type | string | `Application/JSON` |

**パス パラメーター**

なし

**クエリ パラメーター**

| **パラメーター名** | **必須** | **Type** | **説明** |
| --- | --- | --- | --- |
| `reportId` | いいえ | string | この引数で指定された `reportId` を持つレポートのみの詳細を取得するフィルター |
| `reportName` | いいえ | string | この引数で指定された `reportName` を持つレポートのみの詳細を取得するフィルター |
| `queryId` | いいえ | boolean | 定義済みのシステム クエリを応答に含めます |

**用語集**

None

**応答**

応答のペイロードは、JSON 形式で次のように構成されます。

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
      " RecurrenceCount": 0,
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

次の表では、応答内の要素の主な定義を示しています。

| **パラメーター** | **説明** |
| --- | --- |
| `ReportId` | 作成されたレポートの一意の UUID |
| `ReportName` | 要求ペイロードでレポートに指定された名前 |
| `Description` | レポートの作成時に指定された説明 |
| `QueryId` | レポートの作成時に渡されたクエリ ID |
| `Query` | このレポートに対して実行されるクエリ テキスト |
| `User` | レポートの作成に使用するユーザー ID |
| `CreatedTime` | レポートが作成された時刻。 時刻の形式は yyyy-MM-ddTHH:mm:ssZ です |
| `ModifiedTime` | レポートが最後に変更された時刻。 時刻の形式は yyyy-MM-ddTHH:mm:ssZ です |
| `StartTime` | 実行が開始される時刻。 時刻の形式は yyyy-MM-ddTHH:mm:ssZ です |
| `ReportStatus` | レポート実行の状態。 有効な値は、Paused、Active、および Inactive です。 |
| `RecurrenceInterval` | レポートの作成中に指定された繰り返し間隔 |
| `RecurrenceCount` | レポートの作成中に指定された繰り返し回数 |
| `CallbackUrl` | 要求で指定されたコールバック URL |
| `Format` | レポート ファイルの形式 |
