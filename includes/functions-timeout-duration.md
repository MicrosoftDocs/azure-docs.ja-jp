---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: 0bd66699142e9e03f4a344d499624fe207cb9a45
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963582"
---
## <a name="timeout"></a>Function App タイムアウト期間 

Function App のタイムアウト期間は、[host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) プロジェクト ファイルの functionTimeout プロパティによって定義されます。 次の表は、両方のプランと両方のランタイム バージョンでの既定と最大値 (分単位) を示します。

| プラン | ランタイム バージョン | Default | 最大値 |
|------|---------|---------|---------|
| 消費 | 1.x | 5 | 10 |
| 消費 | 2.x | 5 | 10 |
| App Service | 1.x | 無制限 | 無制限 |
| App Service | 2.x | 30 | 無制限 |

> [!NOTE] 
> 関数アプリのタイムアウト設定に関係なく 230 秒は、HTTP トリガー関数が要求に応答できる最大時間です。 これは、[Azure Load Balancer の既定のアイドル タイムアウトによるものです](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)。 より長い処理時間では、[Durable Functions async pattern](../articles/azure-functions/durable/durable-functions-overview.md#async-http) の使用を検討するか、[実際の作業を遅らせて、即座に応答を返します](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)。
