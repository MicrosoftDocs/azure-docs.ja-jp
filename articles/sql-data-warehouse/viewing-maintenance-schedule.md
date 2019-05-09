---
title: Azure メンテナンス スケジュール (プレビュー) |Microsoft Docs
description: メンテナンス スケジューリングを使用すると、Azure SQL Data Warehouse サービスで新機能のロールアウト、アップグレード、パッチを行うために必要な予定メンテナンス イベントを計画することができます。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 61537a4cb8ca1e6e98f2fa04959861eb03480fe9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247257"
---
# <a name="view-a-maintenance-schedule"></a>メンテナンス スケジュールを表示する 

## <a name="portal"></a>ポータル

既定では、新しく作成されるすべての Azure SQL Data Warehouse インスタンスに対し、デプロイの間に、8 時間のプライマリおよびセカンダリ メンテナンス ウィンドウが適用されます。 ウィンドウは、デプロイが完了するとすぐに変更できます。 指定されているメンテナンス ウィンドウの範囲外では、事前に通知することなく、メンテナンスは行われません。

データ ウェアハウスに適用されたメンテナンス スケジュールを表示するには、次の手順のようにします。

1.  [Azure Portal](https://portal.azure.com/) にサインインします。
2.  表示するデータ ウェア ハウスを選択します。 
3.  選択したデータ ウェアハウスが、[概要] ブレードで開きます。 データ ウェアハウスに適用されているメンテナンス スケジュールが、**[Maintenance schedule (preview)]\(メンテナンス スケジュール (プレビュー)\)** の下に表示されます。

![[概要] ブレード](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>次の手順
- Azure Monitor を使用してアラートを作成、表示、管理する方法について[詳しく知る](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage)。
- ログ アラート ルール用の Webhook アクションについて[詳しく知る](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)。
- アクション グループの作成と管理について[詳しく知る](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)。
- Azure Service Health について[詳しく知る](https://docs.microsoft.com/azure/service-health/service-health-overview)。


