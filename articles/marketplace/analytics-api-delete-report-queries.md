---
title: レポート クエリの削除 API
description: この API を使用して、コマーシャル マーケットプレース分析のユーザー定義クエリを削除します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4fc3479f1e35970a97684396a7a2e0c0c2582128
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583655"
---
# <a name="delete-report-queries-api"></a>レポート クエリの削除 API

この API ではユーザー定義クエリを削除します。

**要求の構文**

| **方法** | **要求 URI** |
| --- | --- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/{queryId}` |

**要求ヘッダー**

| **Header** | **Type** | **説明** |
| --- | --- | --- |
| 承認 | string | 必須。 `Bearer <token>` という形式の Azure Active Directory (Azure AD) アクセス トークン |
| Content-Type | string | `Application/JSON` |

**パス パラメーター**

| **パラメーター名** | **Type** | **説明** |
| --- | --- | --- |
| `queryId` | string | この引数で指定された ID を持つクエリのみの詳細を取得するフィルター |

**クエリ パラメーター**

なし

**要求ペイロード**

なし

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
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
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
| `QueryId` | 削除されたクエリの一意の UUID。 |
| `Name` | 削除されたクエリの名前 |
| `Description` | 削除されたクエリの説明 |
| `Query` | 削除されたクエリのレポート クエリ文字列 |
| `Type` | userDefined |
| `User` | クエリを作成したユーザーの ID |
| `CreatedTime` | クエリが作成された時刻 |
| `ModifiedTime` | [Null] |
| `TotalCount` | Value 配列内のデータセットの数 |
| `StatusCode` | 結果コード。 200、400、401、403、500 の値になる可能性があります |
