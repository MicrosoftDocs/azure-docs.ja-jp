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
ms.openlocfilehash: 5f2b77c7d8e1a2da9517183043231b717b6cceab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734081"
---
### <a name="general-query-limits"></a>一般的なクエリの制限

| 制限 | 説明 |
|:---|:---|
| クエリ言語 | Azure Monitor では、Azure Data Explorer と同じ [Kusto クエリ言語](/azure/kusto/query/)が使用されます。 Azure Monitor でサポートされていない KQL 言語要素については、「[Azure Monitor ログ クエリ言語の違い](/azure/data-explorer/kusto/query/)」を参照してください。 |
| Azure Azure リージョン | データが複数の Azure リージョンの Log Analytics ワークスペースにまたがっている場合、ログ クエリで過剰なオーバーヘッドが発生する可能性があります。 詳細については、「[クエリの制限](../articles/azure-monitor/logs/scope.md#query-scope-limits)」をご覧ください。 |
| リソース間のクエリ | 1 回のクエリ内の Application Insights リソースと Log Analytics ワークスペースの数は 100 個に制限されています。<br>リソース間のクエリは、ビュー デザイナーでサポートされていません。<br>ログ アラートでのリソース間のクエリは、新しい scheduledQueryRules API でサポートされています。<br>詳細については、[リソース間のクエリの制限](../articles/azure-monitor/logs/cross-workspace-query.md#cross-resource-query-limits)に関する記事を参照してください。 |

### <a name="user-query-throttling"></a>ユーザー クエリの調整
Azure Monitor には、ユーザーが過剰な数のクエリを送信するのを防ぐためのいくつかの調整制限があります。 このような動作によってシステムのバックエンド リソースが過負荷になり、サービスの応答性が損なわれる可能性があります。 次の制限は、顧客を中断から保護し、一貫したサービス レベルを確保するために設計されています。 ユーザーの調整と制限は、極端な使用シナリオにのみ影響するように設計されており、一般的な使用には関係ありません。


| Measure | ユーザーあたりの制限 | 説明 |
|:---|:---|:---|
| 同時クエリ | 5 | ユーザーのために既に 5 つのクエリが実行されている場合、新しいクエリはユーザーごとのコンカレンシー キューに配置されます。 実行中のクエリの 1 つが終了すると、次のクエリがキューから取り出されて開始されます。 これには、アラート ルールからのクエリは含まれません。
| コンカレンシー キューの時間 | 3 分 | クエリが開始されずにキューに 3 分以上留まると、クエリはコード 429 の HTTP エラー応答で終了します。 |
| コンカレンシー キュー内の合計クエリ数 | 200 | キュー内のクエリの数が 200 に達すると、追加のクエリは HTTP エラー コード 429 で拒否されます。 この数は、同時に実行できる 5 つのクエリとは別にカウントされます。 |
| クエリ速度 | 30 秒あたり 200 クエリ | これは、1 人のユーザーがすべてのワークスペースにクエリを送信できる全体的な速度です。  この制限は、プログラムによるクエリや、Azure ダッシュボードや Log Analytics ワークスペースの概要ページなどの視覚化パーツによって開始されるクエリに適用されます。 |

- 「[Azure Monitor でログ クエリを最適化する](../articles/azure-monitor/logs/query-optimization.md)」の説明に従って、クエリを最適化します。
- ダッシュボードとワークブックでは 1 つのビューに複数のクエリを含めることができるため、読み込みまたは更新のたびにクエリのバーストが生成されることがあります。 1 つのビューを複数のビューに分割し、必要に応じてそれらを読み込むことを検討してください。 
- Power BI では、未加工のログではなく、集計された結果のみを抽出することを検討してください。