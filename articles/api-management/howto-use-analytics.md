---
title: Azure API Management で API 分析を使用する | Microsoft Docs
description: Azure API Management で分析を使用して、API の使用状況と API のパフォーマンスを理解し、分類することができます。
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: apimpm
ms.openlocfilehash: ca7bd70bbf99a6d0079717a7a02328b11528d2e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96839702"
---
# <a name="get-api-analytics-in-azure-api-management"></a>Azure API Management で API 分析を取得する

Azure API Management には、API の組み込み分析が用意されています。 次のような複数のディメンションにわたる、API Management インスタンスでの API の使用状況とパフォーマンスを分析します。

* Time
* [地理的な場所]
* API
* API 操作
* 製品
* サブスクリプション
* ユーザー
* Requests

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="ポータルでのタイムライン分析":::

API の高レベルの監視とトラブルシューティングを行うために分析を使用します。 診断と監査のためのほぼリアルタイムのメトリックとリソース ログを含む、追加の監視機能については、[チュートリアル: 公開された API の監視](api-management-howto-use-azure-monitor.md)に関するページを参照してください。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>分析 - ポータル

Azure portal を使用して、API Management インスタンスの分析データを一目で確認します。

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。 
1. 左側のメニューで、 **[監視]** の下から **[分析]** を選択します。

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="ポータルで API Management インスタンスに対して分析を選択する":::  
1. データの時間範囲を選択するか、カスタムの時間範囲を入力します。
1. **[タイムライン]** 、 **[地理的な場所]** など、分析データのレポート カテゴリを選択します。
1. 必要に応じて、1つまたは複数の追加のカテゴリでレポートをフィルター処理します。

## <a name="analytics---rest-api"></a>分析 - REST API

API Management REST API の[レポート](/rest/api/apimanagement/2019-12-01/reports)操作を使用して、API Management インスタンスの分析データを取得し、フィルター処理します。

使用可能な操作は、API、地理的な場所、API 操作、製品、要求、サブスクリプション、時間、またはユーザーごとにレポート レコードを返します。

## <a name="next-steps"></a>次のステップ

* API Management の Azure Monitor 機能の概要については、[チュートリアル: 公開された API の監視](api-management-howto-use-azure-monitor.md)に関する記事を参照してください。
* 詳細な HTTP のログと監視については、[Azure API Management、イベント ハブ、および Moesif を使用した API の監視](api-management-log-to-eventhub-sample.md)に関する記事を参照してください。
* [Azure API Management と Azure Application Insights](api-management-howto-app-insights.md)の統合について説明します。