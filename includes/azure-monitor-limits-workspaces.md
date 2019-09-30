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
ms.openlocfilehash: 5d0c43fbcc1c59c3281f412aad96a3942a5c79b1
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "70392914"
---
**データの収集量と保持期間** 

| レベル | 1 日あたりの制限 | データの保持 | Comment (コメント) |
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

| Category | 制限 | 説明 |
|:---|:---|:---|
| ログ クエリによって返されるレコードの最大数 | 10,000 | クエリでクエリ スコープ、時間範囲、およびフィルターを使用して、結果を減らします。 |


**データ コレクター API**

| Category | 制限 | 説明 |
|:---|:---|:---|
| 1 回の投稿の最大サイズ | 30 MB | 大量の場合は複数の投稿に分割します。 |
| フィールド値の最大サイズ  | 32 KB | 32 KB を超えるフィールドは切り詰められます。 |

**Search API**

| Category | 制限 | 説明 |
|:---|:---|:---|
| 1 つのクエリで返されるレコードの最大数 | 500,000 | |
| 返されるデータの最大サイズ | 64,000,000 バイト (~61 MiB)| |
| クエリの最大実行時間 | 10 分 | 詳細については、[タイムアウト](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts)に関するページをご覧ください。  |
| 最大要求レート | AAD ユーザーまたはクライアントの IP アドレスごとに、30 秒あたり 200 件の要求 | 詳細については、[レート制限](https://dev.loganalytics.io/documentation/Using-the-API/Limits)に関するページをご覧ください。 |

**一般的なワークスペースの制限**

| Category | 制限 | 説明 |
|:---|:---|:---|
| テーブルの最大列数         | 500 | |
| 列名の最大文字数 | 500 | |
| 容量の上限に達しているリージョン | 米国中西部 | このリージョンは一時的な容量の上限に達しているため、現在新しいワークスペースを作成できません。 この制限は、2019 年 10 月末までに対処される予定です。 |
| データのエクスポート | 現在、利用できません | データの集計とエクスポートには Azure Function または Logic App を使用してください。 | 

>[!NOTE]
>Log Analytics を使用してきた期間の長さによっては、レガシ価格レベルにアクセスできることがあります。 詳細については、[Log Analytics のレガシ価格レベル](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)に関するページを参照してください。 