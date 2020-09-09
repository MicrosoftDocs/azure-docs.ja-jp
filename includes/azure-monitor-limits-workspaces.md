---
title: インクルード ファイル
description: インクルード ファイル
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: c8868cd6f5c50b84f263155518ee553145afcfa9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602419"
---
**データの収集量と保持期間** 

| レベル | 1 日あたりの制限 | データの保持 | 解説 |
|:---|:---|:---|:---|
| 現在の GB あたりの価格レベル<br>(2018 年 4 月に導入) | 制限なし | 30 日 - 730 日 | 31 日を超えるデータ保持期間は追加料金で使用できます。 Azure Monitor の価格を確認してください。 |
| 従来の Free レベル<br>(2016 年 4 月に導入) | 500 MB | 7 日 | ワークスペースが 1 日あたり 500 MB の制限に達した場合、データ インジェストが停止し、翌日の始めに再開されます。 1 日は UTC に基づきます。 Azure Security Center によって収集されるデータは、この 1 日あたり 500 MB の制限には含まれず、この制限を超えても引き続き収集されます。  |
| 従来の GB あたりの Standalone レベル<br>(2016 年 4 月に導入) | 制限なし | 30 日 - 730 日 | 31 日を超えるデータ保持期間は追加料金で使用できます。 Azure Monitor の価格を確認してください。 |
| 従来のノードあたり (OMS)<br>(2016 年 4 月に導入) | 制限なし | 30 日 - 730 日 | 31 日を超えるデータ保持期間は追加料金で使用できます。 Azure Monitor の価格を確認してください。 |
| 従来の Standard レベル | 制限なし | 30 日  | 保持期間を調整することはできません。 |
| 従来の Premium レベル | 制限なし | 365 日  | 保持期間を調整することはできません。 |

**サブスクリプションあたりのワークスペースの数。**

| Pricing tier    | ワークスペースの制限 | 説明
|:---|:---|:---|
| Free レベル  | 10 | この制限を増やすことはできません。 |
| その他のすべてのレベル | 制限なし | リソース グループ内のリソースの数とサブスクリプションあたりのリソース グループの数によって制限されます。 |

**Azure Portal**

| カテゴリ | 制限 | 説明 |
|:---|:---|:---|
| ログ クエリによって返されるレコードの最大数 | 10,000 | クエリでクエリ スコープ、時間範囲、およびフィルターを使用して、結果を減らします。 |


**データ コレクター API**

| カテゴリ | 制限 | 説明 |
|:---|:---|:---|
| 1 回の投稿の最大サイズ | 30 MB | 大量の場合は複数の投稿に分割します。 |
| フィールド値の最大サイズ  | 32 KB | 32 KB を超えるフィールドは切り詰められます。 |

**Search API**

| カテゴリ | 制限 | 説明 |
|:---|:---|:---|
| 1 つのクエリで返されるレコードの最大数 | 500,000 | |
| 返されるデータの最大サイズ | 64,000,000 バイト (~61 MiB)| |
| クエリの最大実行時間 | 10 分 | 詳細については、[タイムアウト](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts)に関するページをご覧ください。  |
| 最大要求レート | Azure AD ユーザーまたはクライアントの IP アドレスごとに、30 秒あたり 200 件の要求 | 詳細については、[レート制限](https://dev.loganalytics.io/documentation/Using-the-API/Limits)に関するページをご覧ください。 |

**一般的なワークスペースの制限**

| カテゴリ | 制限 | 説明 |
|:---|:---|:---|
| テーブルの最大列数         | 500 | |
| 列名の最大文字数 | 500 | |
| データのエクスポート | 現在、利用できません | データの集計とエクスポートには Azure Function または Logic App を使用してください。 | 

**<a name="data-ingestion-volume-rate">データ インジェストのボリューム レート</a>**

Azure Monitor とは、毎月増加するテラバイト単位のデータを送信する何千もの顧客にサービスを提供する高スケールのデータ サービスです。 ボリューム レート制限は、マルチテナント環境における突然のインジェスト スパイクから Azure Monitor の顧客を隔離するためのものです。 ワークスペースでは、既定のインジェスト ボリューム レートのしきい値である 500 MB (圧縮) が定義されており、これは約 **6 GB/分** (非圧縮) に変換されます。実際のサイズは、ログの長さとその圧縮率に左右され、データの種類によって異なることがあります。 ボリューム レート制限は、 [診断設定](../articles/azure-monitor/platform/diagnostic-settings.md)、 [Data Collector API](../articles/azure-monitor/platform/data-collector-api.md) またはエージェントを使用して Azure リソースから送信されたかどうかにかかわらず、すべてのインジェスト データに適用されます。

ワークスペースに構成されているしきい値の 80% を超えるボリューム レートでワークスペースにデータを送信すると、しきい値を超え続けている間、6 時間ごとにワークスペースの "*操作*" テーブルにイベントが送信されます。 インジェスト ボリューム レートがしきい値を超えると、一部のデータが削除され、しきい値を超え続けている間、6 時間ごとにワークスペースの "*操作*" テーブルにイベントが送信されます。 インジェスト ボリューム レートがしきい値を超え続けている場合、または間もなくそれに達すると予測される場合は、サポート リクエストを開いて、その引き上げをリクエストできます。 

ワークスペースでインジェスト ボリューム レート制限に近づいたときまたは達したときに通知を受けるには、ゼロより大きい結果数、5 分の評価期間、5 分の頻度のアラート ロジック ベースを使った次のクエリを使用して、[ログ アラート ルール](../articles/azure-monitor/platform/alerts-log.md)を作成します。

インジェストボリューム レートがしきい値の 80% に到達:
```Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The data ingestion volume rate crossed 80% of the threshold"
```

インジェストボリューム レートがしきい値に到達:
```Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The data ingestion volume rate crossed the threshold"
```

>[!NOTE]
>Log Analytics を使用してきた期間の長さによっては、レガシ価格レベルにアクセスできることがあります。 詳細については、[Log Analytics のレガシ価格レベル](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)に関するページを参照してください。 
