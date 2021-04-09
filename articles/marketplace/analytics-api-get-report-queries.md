---
title: レポート クエリの取得 API
description: この API を使用して、コマーシャル マーケットプレースの分析レポートで使用できるすべてのクエリを取得します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: e2be43e8402e5179fb62d810fe7b9f41e704c49d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583613"
---
# <a name="get-report-queries-api"></a>レポート クエリの取得 API

レポート クエリの取得 API では、レポートで使用できるすべてのクエリが取得されます。 既定では、すべてのシステムおよびユーザー定義クエリが取得されます。

**要求の構文**

| **方法** | **要求 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries?queryId={QueryID}&queryName={QueryName}&includeSystemQueries={include_system_queries}&includeOnlySystemQueries={include_only_system_queries}` |
|||

**要求ヘッダー**

| **Header** | **Type** | **説明** |
| --- | --- | --- |
| 承認 | string | 必須。 `Bearer <token>` という形式の Azure Active Directory (Azure AD) アクセス トークン |
| Content-Type | string | `Application/JSON` |
||||

**パス パラメーター**

なし

**クエリ パラメーター**

| **パラメーター名** | **Type** | **必須** | **説明** |
| --- | --- | --- | --- |
| `queryId` | string | いいえ | 引数で指定された ID を持つクエリのみの詳細を取得するフィルター |
| `queryName` | string | いいえ | 引数で指定された名前を持つクエリのみの詳細を取得するフィルター |
| `IncludeSystemQueries` | boolean | いいえ | 定義済みのシステム クエリを応答に含めます |
| `IncludeOnlySystemQueries` | boolean | いいえ | システム クエリのみを応答に含めます |
|||||

**要求ペイロード**

なし

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
| `QueryId` | クエリの一意の UUID |
| `Name` | クエリの作成時にクエリに指定された名前 |
| `Description` | クエリの作成時に指定された説明 |
| `Query` | レポート クエリ文字列 |
| `Type` | ユーザーが作成したクエリの場合は `userDefined` に、定義済みのシステム クエリの場合は `system` に設定します |
| `User` | クエリを作成したユーザーの ID |
| `CreatedTime` | クエリの作成時刻 |
| `TotalCount` | Value 配列内のデータセットの数 |
| `StatusCode` | 結果コード。 200、400、401、403、500 の値になる可能性があります。 |
|||
