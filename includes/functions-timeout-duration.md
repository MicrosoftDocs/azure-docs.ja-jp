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
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "77198328"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Function App タイムアウト期間 

関数アプリのタイムアウト期間は、[host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) プロジェクト ファイルの `functionTimeout` プロパティによって定義されます。 次の表は、両方のプランと各種ランタイム バージョンでの既定と最大値 (分単位) を示します。

| プラン | ランタイム バージョン | Default | 最大値 |
|------|---------|---------|---------|
| 従量課金 | 1.x | 5 | 10 |
| 従量課金 | 2.x | 5 | 10 |
| 従量課金 | 3.x | 5 | 10 |
| Premium | 1.x | 30 | 無制限 |
| Premium | 2.x | 30 | 無制限 |
| Premium | 3.x | 30 | 無制限 |
| App Service | 1.x | 無制限 | 無制限 |
| App Service | 2.x | 30 | 無制限 |
| App Service | 3.x | 30 | 無制限 |

> [!NOTE] 
> 関数アプリのタイムアウト設定に関係なく 230 秒は、HTTP トリガー関数が要求に応答できる最大時間です。 これは、[Azure Load Balancer の既定のアイドル タイムアウトによるものです](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)。 より長い処理時間では、[Durable Functions async pattern](../articles/azure-functions/durable/durable-functions-overview.md#async-http) の使用を検討するか、[実際の作業を遅らせて、即座に応答を返します](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)。
