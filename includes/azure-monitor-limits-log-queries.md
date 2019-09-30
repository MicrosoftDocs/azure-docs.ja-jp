---
title: インクルード ファイル
description: インクルード ファイル
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f007cf0d46d6cbee39a950b9784bbc9bde702ff5
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "69657812"
---
| 制限 | 説明 |
|:---|:---|
| クエリ言語 | Azure Monitor では、Azure Data Explorer と同じ [Kusto クエリ言語](/azure/kusto/query/)が使用されます。 Azure Monitor でサポートされていない KQL 言語要素については、「[Azure Monitor ログ クエリ言語の違い](../articles/azure-monitor/log-query/data-explorer-difference.md)」を参照してください。 |
| Azure Azure リージョン | データが複数の Azure リージョンの Log Analytics ワークスペースにまたがっている場合、ログ クエリで過剰なオーバーヘッドが発生する可能性があります。 詳細については、「[クエリの制限](../articles/azure-monitor/log-query/scope.md#query-limits)」をご覧ください。 |
| リソース間のクエリ | 1 回のクエリ内の Application Insights リソースと Log Analytics ワークスペースの数は 100 個に制限されています。<br>リソース間のクエリは、ビュー デザイナーでサポートされていません。<br>ログ アラートでのリソース間のクエリは、新しい scheduledQueryRules API でサポートされています。<br>詳細については、[リソース間のクエリの制限](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits)に関する記事を参照してください。 |