---
title: Azure Active Directory レポートの待機時間 | Microsoft ドキュメント
description: Azure Portal にレポート イベントが表示されるのにかかる時間について学習します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64bd2247a3437a2cc960da1820d9be417eedff8e
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438651"
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory レポートの待機時間

待機時間とは、[Azure portal](https://portal.azure.com) に Azure Active Directory (Azure AD) レポート データが表示されるのにかかる時間です。 この記事では、さまざまな種類のレポートで想定される待機時間を示します。 

## <a name="activity-reports"></a>アクティビティ レポート

アクティビティ レポートには次の 2 つの種類があります。

- [サインイン](concept-sign-ins.md) – マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。
- [監査ログ](concept-audit-logs.md) - ユーザーとグループや、マネージド アプリケーションとディレクトリのアクティビティに関するシステム アクティビティ情報を提示します。

次の表に、アクティビティ レポートの待機時間情報を一覧表示します。 

> [!NOTE]
> **待機時間 (95 パーセンタイル)** は、ログの 95% が報告されるまでにかかる時間を示し、**待機時間 (99 パーセンタイル)** は、ログの 99% が報告されるまでにかかる時間を示します。 
>

| レポート | 待機時間 (95 パーセンタイル) |待機時間 (99 パーセンタイル)|ログがレポートされる時間の範囲|
| :-- | --- | --- | --- |
| 監査ログ | 2 分  | 5 分  | 2 分から 60 分 |
| サインイン | 2 分  | 5 分 | 2 分から 120 分 |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Premium ライセンスの取得後、アクティビティ データが表示されるまでにかかる時間

無料ライセンスでのアクティビティ データが既にある場合、アップグレード時にはすぐにそのデータを表示できます。 データが何もない場合は、Premium ライセンスへのアップグレード後、レポートにデータが表示されるまで 1 日から 2 日かかります。

## <a name="security-reports"></a>セキュリティ レポート

セキュリティ レポートには次の 2 種類があります。

- [リスクの高いサインイン](concept-risky-sign-ins.md) - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。 
- [リスクのフラグ付きユーザー](concept-user-at-risk.md) - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。 

次の表に、セキュリティ レポートの待機時間情報を一覧表示します。

| レポート | 最小値 | 平均 | 最大値 |
| :-- | --- | --- | --- |
| リスクのあるユーザー          | 5 分   | 約 15 分  | 2 時間  |
| リスクの高いサインイン         | 5 分   | 約 15 分  | 2 時間  |

## <a name="risk-events"></a>リスク イベント

Azure AD では、アダプティブ機械学習アルゴリズムとヒューリスティックを使用して、ユーザー アカウントに関連する疑わしいアクションを検出します。 検出された疑わしいアクションはそれぞれ、**リスク イベント**と呼ばれるレコードに格納されます。

次の表に、リスク イベントの待機時間情報を一覧表示します。

| レポート | 最小値 | 平均 | 最大値 |
| :-- | --- | --- | --- |
| 匿名の IP アドレスからのサインイン |5 分 |約 15 分 |2 時間 |
| 未知の場所からのサインイン |5 分 |約 15 分 |2 時間 |
| 資格情報が漏洩したユーザー |2 時間 |4 時間 |8 時間 |
| 特殊な場所へのあり得ない移動 |5 分 |1 時間 |8 時間  |
| 感染しているデバイスからのサインイン |2 時間 |4 時間 |8 時間  |
| 不審なアクティビティのある IP アドレスからのサインイン |2 時間 |4 時間 |8 時間  |


## <a name="next-steps"></a>次の手順

* [Azure AD レポートの概要](overview-reports.md)
* [Azure AD レポートへのプログラムによるアクセス](concept-reporting-api.md)
* [Azure Active Directory リスク イベント](concept-risk-events.md)
