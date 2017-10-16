---
title: "Azure Active Directory レポートの待機時間 | Microsoft ドキュメント"
description: "Azure Portal にレポート イベントが表示されるのにかかる時間について学習します"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi;dhanyahk
ms.reviewer: dhanyahk
ms.openlocfilehash: 93cb0baeab8f13f81257ed1bd32ed08561c54b72
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory レポートの待機時間

Azure Active Directory の[レポート](active-directory-preview-explainer.md)では、環境の動作状況を判断するために必要な情報をすべて取得できます。 Azure Portal にレポート データが表示されるのにかかる時間は、待機時間とも呼ばれます。 

このトピックでは、Azure Portal 内のすべてのレポート用のカテゴリの待機時間情報を一覧します。 


## <a name="activity-reports"></a>アクティビティ レポート

アクティビティ レポートには次の 2 つの領域があります。

- **サインイン アクティビティ** – マネージ アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報
- **監査ログ** - ユーザーとグループの管理や、マネージ アプリケーションとディレクトリのアクティビティに関するシステム アクティビティ情報

次の表に、アクティビティ レポートの待機時間情報を一覧表示します。

| レポート | 最小値 | 平均 | 最大値 |
| :-- | --- | --- | --- |
| 監査ログ             | 30 分  | 45 分 | 1 時間     |
| サインイン               | 約 15 分  | 約 15 分 | 2 時間*   |

>[!NOTE]
> 従来の企業のアプリケーションからのサインイン アクティビティ データの場合、レポート データの表示に 8 時間を要します。 


## <a name="security-reports"></a>セキュリティ レポート

セキュリティ レポートには次の 2 つの領域があります。

- **リスクの高いサインイン** - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。 
- **リスクのフラグ付きユーザー** - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。 

次の表に、セキュリティ レポートの待機時間情報を一覧表示します。

| レポート | 最小値 | 平均 | 最大値 |
| :-- | --- | --- | --- |
| リスクのあるユーザー          | 5 分   | 約 15 分  | 2 時間  |
| リスクの高いサインイン         | 5 分   | 約 15 分  | 2 時間  |

## <a name="risk-events"></a>リスク イベント

Azure Active Directory では、アダプティブ機械学習アルゴリズムとヒューリスティックを使用して、ユーザー アカウントに関連する疑わしいアクションを検出します。 検出された疑わしいアクションはそれぞれ、リスク イベントと呼ばれるレコードに格納されます。

次の表に、リスク イベントの待機時間情報を一覧表示します。

| レポート | 最小値 | 平均 | 最大値 |
| :-- | --- | --- | --- |
| 匿名の IP アドレスからのサインイン |5 分 |約 15 分 |2 時間 |
| 未知の場所からのサインイン |5 分 |約 15 分 |2 時間 |
| 資格情報が漏洩したユーザー |2 時間 |4 時間 |8 時間 |
| 特殊な場所へのあり得ない移動 |5 分 |1 時間 |8 時間  |
| 感染しているデバイスからのサインイン |2 時間 |4 時間 |8 時間  |
| 不審なアクティビティのある IP アドレスからのサインイン |2 時間 |4 時間 |8 時間  |



## <a name="next-steps"></a>次のステップ

Azure Portal のアクティビティ レポートに関する詳細については、次を参照してください。

- [Azure Active Directory ポータルのサインイン アクティビティ レポート](active-directory-reporting-activity-sign-ins.md)
- [Azure Active Directory ポータルの監査アクティビティ レポート](active-directory-reporting-activity-audit-logs.md)

Azure Portal のセキュリティ レポートに関する詳細については、次を参照してください。

- [Azure Active Directory ポータルの危険な状態のユーザー セキュリティ レポート](active-directory-reporting-security-user-at-risk.md)
- [Azure Active Directory ポータルのリスクの高いサインイン レポート](active-directory-reporting-security-risky-sign-ins.md)

リスク イベントの詳細について理解するには、「[Azure Active Directory のリスク イベント](active-directory-reporting-risk-events.md)」を参照してください。
