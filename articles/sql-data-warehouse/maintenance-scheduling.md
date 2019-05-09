---
title: Azure メンテナンス スケジュール (プレビュー) |Microsoft Docs
description: メンテナンス スケジューリングを使用すると、Azure SQL Data Warehouse サービスで新機能のロールアウト、アップグレード、パッチを行うために必要な予定メンテナンス イベントを計画することができます。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 03/13/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: b97e27b86ecad1f7f87a6de4d43b09d69c167c6f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792036"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>メンテナンス スケジュールを使用してサービスの更新とメンテナンスを管理する

現在、メンテナンス スケジュールは Azure SQL Data Warehouse のすべてのリージョンで使用できるようになりました。 この機能は、Service Health 計画メンテナンス通知、リソース ヘルス チェック モニター、Azure SQL Data Warehouse メンテナンス スケジューリング サービスを統合します。

メンテナンス スケジューリングを使用することで、新しい機能、アップグレード、パッチを受信するのに都合がいい時間枠をスケジュールできます。 お客様は、7 日間におけるプライマリおよびセカンダリのメンテナンス期間を選択します。 たとえば、土曜日の 22:00 から日曜日の 01:00 をプライマリ ウィンドウとし、水曜日の 19:00 から 22:00 をセカンダリ ウィンドウとします。 プライマリ メンテナンス期間でメンテナンスを実行できなかった場合、SQL Data Warehouse は、セカンダリ メンテナンス期間で再度メンテナンスを試みます。 サービス メンテナンスは、プライマリ ウィンドウとセカンダリ ウィンドウの両方で発生する可能性があります。 すべてのメンテナンス操作を短時間で確実に完了するために、DW400(c) と下位層のデータ ウェアハウスでは、指定されたメンテナンス期間外にメンテナンスが完了する場合があります。

新しく作成されたすべてのAzure SQL Data Warehouse インスタンスには、デプロイ時にシステム定義のメンテナンス スケジュールが適用されます。 デプロイが完了するとすぐに、スケジュールを編集できます。

メンテナンス ウィンドウはそれぞれ 3 ～ 8 時間です。 その期間内は、任意のタイミングでメンテナンスが実行される可能性があります。 このサービスがお使いのデータ ウェアハウスに新しいコードをデプロイする際に、接続が一時的に失われることが予想されます。

この機能を使用するには、別の期間にプライマリ ウィンドウとセカンダリ ウィンドウを指定する必要があります。 すべてのメンテナンス操作は、予定メンテナンス期間内に完了する必要があります。 指定されているメンテナンス ウィンドウの範囲外では、事前に通知することなく、メンテナンスは行われません。 スケジュールされたメンテナンス中にデータ ウェアハウスが一時停止されると、再開操作中に更新されます。  

## <a name="alerts-and-monitoring"></a>アラートと監視

Service Health 通知とリソース ヘルス チェック モニターとの統合により、お客様は差し迫ったメンテナンス アクティビティについての情報を得ることができます。 この新しいオートメーションには、Azure Monitor が活用されています。 差し迫ったメンテナンス イベントの通知方法を自分で決めることができます。 また、ダウンタイムを管理し、業務への影響を最小化することに寄与する自動化されたフローも自分で決めることができます。

すべてのメンテナンス イベントの 24 時間前に事前通知が発行されます。現時点では DW400c と下位層は、その対象ではありません。 インスタンスのダウンタイムを最小化するために、選択したメンテナンス期間の前に、長時間実行されるトランザクションがデータ ウェアハウスにないことを確認してください。 メンテナンスが開始されると、すべてのアクティブなセッションがキャンセルされます。 コミットされていないトランザクションはロールバックされ、データ ウェアハウスの接続が短時間失われます。 データ ウェアハウスの保守が完了すると、すぐに通知が届きます。

メンテナンスが行われるという事前通知を受けても、その間に SQL Data Warehouse がメンテナンスを実行できない場合は、キャンセル通知が届きます。 メンテナンスは、次の定期メンテナンス期間中に再開されます。

すべてのアクティブ メンテナンス イベントが **[Service Health - Planned Maintenance]\(Service Health - 計画メンテナンス\)** セクションに表示されます。 Service Health の履歴には、過去のイベントの記録がすべて含まれます。 アクティブなイベント中に Azure Service Health チェック ポータル ダッシュボードを使用してメンテナンスを監視できます。

### <a name="maintenance-schedule-availability"></a>メンテナンス スケジュールの提供状況

選択したリージョンでメンテナンス スケジューリングが利用できない場合でも、メンテナンス スケジュールはいつでも表示および編集できます。 ご利用のリージョンでメンテナンス スケジューリングが利用可能になると、特定されたスケジュールはすぐにお使いのデータ ウェアハウスでアクティブになります。

## <a name="next-steps"></a>次の手順

- メンテナンス スケジュールを表示する方法について[詳しく知る](viewing-maintenance-schedule.md)。
- メンテナンス スケジュールを変更する方法について[詳しく知る](changing-maintenance-schedule.md)。
- Azure Monitor を使用してアラートを作成、表示、管理する方法について[詳しく知る](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage)。
- ログ アラート ルール用の Webhook アクションについて[詳しく知る](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)。
- アクション グループの作成と管理について[詳しく知る](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)。
- Azure Service Health について[詳しく知る](https://docs.microsoft.com/azure/service-health/service-health-overview)。
