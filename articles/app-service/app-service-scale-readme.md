---
title: "Azure App Service: App Service アプリケーションのスケーリング"
description: "App Service でのアプリケーションのスケーリングの詳細について説明します。"
keywords: "App Service, Azure App Service, スケール, スケーラブル, App Service プラン, App Service コスト"
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: f403c971-4450-432b-8cea-3eeb426c0147
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ec043a9e01aca2e843e50cef8b90328ba1939ea5
ms.lasthandoff: 11/17/2016


---
# <a name="azure-app-service-scaling-app-service-applications"></a>Azure App Service: App Service アプリケーションのスケーリング
Azure App Service でホストされているアプリケーションは、 [大規模なスケーリングを実現](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/)できますが、アプリケーションのスケーリングは、"どのような場合にも通用する" 解決策が存在しない複雑な問題です。
アプリケーションを正しくスケーリングするには、次の 3 つのことが重要です。 これらは、アプリケーションの成功に役立ちます。

1. アプリケーションのアーキテクチャとその短所を理解すること。
   * アプリケーションがステートフルか、 ステートレスか。
   * アプリケーションのすべてのコンポーネントが何であるか。
     * アプリケーションのボトルネックはどこにあるか。
   * アプリケーションに負荷がかかった場合、最初に何が起こるか。
2. 予想される負荷とパフォーマンス要件を理解すること。
   * アプリケーションを使用するユーザーの数が 1,000 人なのか、100 万人なのか。
   * トラフィックが 1 つの地域から発信されているか、世界中から発信されているか。
   * 季節による変動、トラフィックのピークが存在するかどうか。
   * どの程度のアプリの応答速度が必要か。 1 秒か、 それとも 1 ミリ秒か。
3. アプリをホストしているプラットフォームを理解して正しく利用すること。
   * 自分が目標とするスケールを実現するには、どの機能を利用する必要があるか。

このセクションは、すべての要素を理解し、目標とするスケーラビリティを実現するために必要な App Service の機能を活用する戦略を立てるのに役立ちます。

[!INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]


