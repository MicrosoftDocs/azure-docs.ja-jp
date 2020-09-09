---
title: Azure Cosmos DB クエリ言語の日付と時刻関数
description: DateTime および timestamp の操作を実行する Azure Cosmos DB の日付と時刻の SQL システム関数について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1ea838224f9d91b000100d5fa9308289619fd963
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605184"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>日付と時刻関数 (Azure Cosmos DB)

日付と時刻関数を使用すると、Azure Cosmos DB で DateTime と timestamp の操作を実行できます。

## <a name="functions-to-obtain-the-date-and-time"></a>日付と時刻を取得する関数

次のスカラー関数を使用すると、現在の UTC の日時を、ISO 8601 形式に準拠した文字列、UNIX エポックから経過したミリ秒数のタイムスタンプの数値、または UNIX エポックから経過した 100 ナノ秒単位のティック数を示すティックの数値、の 3 つの形式で取得できます。

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>DateTime 値を操作する関数

次の関数を使用すると、DateTime、timestamp、およびティックの値を簡単に操作できます。

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)
* [DateTimeToTimestamp](sql-query-datetimetotimestamp.md)
* [TicksToDateTime](sql-query-tickstodatetime.md)
* [TimestampToDateTime](sql-query-timestamptodatetime.md)

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
- [ユーザー定義関数](sql-query-udfs.md)
- [集計](sql-query-aggregates.md)
