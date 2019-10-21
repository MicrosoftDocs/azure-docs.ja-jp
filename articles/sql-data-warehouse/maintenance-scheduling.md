---
title: Azure メンテナンス スケジュール (プレビュー) |Microsoft Docs
description: メンテナンス スケジューリングを使用すると、Azure SQL Data Warehouse サービスで新機能のロールアウト、アップグレード、パッチを行うために必要な予定メンテナンス イベントを計画することができます。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 07/16/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 0e5df583112bbb6db9651004e5deaceb6b5b9d12
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958892"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>メンテナンス スケジュールを使用してサービスの更新とメンテナンスを管理する

現在、メンテナンス スケジュールは Azure SQL Data Warehouse のすべてのリージョンで使用できるようになりました。 このメンテナンス スケジュール機能は、Service Health 計画メンテナンス通知、リソース ヘルス チェック モニター、Azure SQL Data Warehouse メンテナンス スケジューリング サービスを統合します。

メンテナンス スケジューリングを使用することで、新しい機能、アップグレード、パッチを受信するのに都合がいい時間枠をスケジュールできます。 お客様は、7 日間におけるプライマリおよびセカンダリのメンテナンス期間を選択します。 この機能を使用するには、別の期間にプライマリ ウィンドウとセカンダリ ウィンドウを指定する必要があります。

たとえば、土曜日の 22:00 から日曜日の 01:00 をプライマリ ウィンドウとしてスケジュールし、水曜日の 19:00 から 22:00 をセカンダリ ウィンドウとしてスケジュールできます。 プライマリ メンテナンス期間でメンテナンスを実行できなかった場合、SQL Data Warehouse は、セカンダリ メンテナンス期間で再度メンテナンスを試みます。 サービス メンテナンスは、プライマリ ウィンドウとセカンダリ ウィンドウの両方で発生する可能性があります。 すべてのメンテナンス操作を短時間で確実に完了するために、DW400(c) と下位層のデータ ウェアハウスでは、指定されたメンテナンス期間外にメンテナンスが完了する場合があります。

新しく作成されたすべてのAzure SQL Data Warehouse インスタンスには、デプロイ時にシステム定義のメンテナンス スケジュールが適用されます。 デプロイが完了するとすぐに、スケジュールを編集できます。

メンテナンス ウィンドウはそれぞれ 3 ～ 8 時間です。 その期間内は、任意のタイミングでメンテナンスが実行される可能性があります。 メンテナンスが開始されると、アクティブなセッションがすべて取り消され、コミットされていないトランザクションはロールバックされます。 このサービスがお使いのデータ ウェアハウスに新しいコードをデプロイする際に、接続が数回、一時的に失われることが予想されます。 データ ウェアハウスの保守が完了すると、すぐに通知が届きます。

 すべてのメンテナンス操作は、予定メンテナンス期間内に完了する必要があります。 指定されているメンテナンス ウィンドウの範囲外では、事前に通知することなく、メンテナンスは行われません。 スケジュールされたメンテナンス中にデータ ウェアハウスが一時停止されると、再開操作中に更新されます。 

## <a name="alerts-and-monitoring"></a>アラートと監視

Service Health 通知とリソース ヘルス チェック モニターとの統合により、お客様は差し迫ったメンテナンス アクティビティについての情報を得ることができます。 この新しいオートメーションには、Azure Monitor が活用されています。 差し迫ったメンテナンス イベントの通知方法を自分で決めることができます。 また、ダウンタイムを管理し、業務への影響を最小化することに寄与する自動化されたフローも自分で決めることができます。

すべてのメンテナンス イベントの 24 時間前に事前通知が発行されますが、DWC400c と下位層はその対象ではありません。 インスタンスのダウンタイムを最小化するために、選択したメンテナンス期間の前に、長時間実行されるトランザクションがデータ ウェアハウスにないことを確認してください。

> [!NOTE]
> 時間が重要な更新プログラムをデプロイする必要がある場合、高度な通知の時間が大幅に短縮されることがあります。

メンテナンスが行われるという事前通知を受けても、その間に SQL Data Warehouse がメンテナンスを実行できない場合は、キャンセル通知が届きます。 メンテナンスは、次の定期メンテナンス期間中に再開されます。

すべてのアクティブ メンテナンス イベントが **[Service Health - Planned Maintenance]\(Service Health - 計画メンテナンス\)** セクションに表示されます。 Service Health の履歴には、過去のイベントの記録がすべて含まれます。 アクティブなイベント中に Azure Service Health チェック ポータル ダッシュボードを使用してメンテナンスを監視できます。

### <a name="maintenance-schedule-availability"></a>メンテナンス スケジュールの提供状況

選択したリージョンでメンテナンス スケジューリングが利用できない場合でも、メンテナンス スケジュールはいつでも表示および編集できます。 ご利用のリージョンでメンテナンス スケジューリングが利用可能になると、特定されたスケジュールはすぐにお使いのデータ ウェアハウスでアクティブになります。

## <a name="view-a-maintenance-schedule"></a>メンテナンス スケジュールを表示する 

### <a name="portal"></a>ポータル

既定では、新しく作成されるすべての Azure SQL Data Warehouse インスタンスに対し、デプロイの間に、8 時間のプライマリおよびセカンダリ メンテナンス ウィンドウが適用されます。 上記の通り、ウィンドウはデプロイが完了するとすぐに変更できます。 指定されているメンテナンス ウィンドウの範囲外では、事前に通知することなく、メンテナンスは行われません。

データ ウェアハウスに適用されたメンテナンス スケジュールを表示するには、次の手順のようにします。

1.  [Azure Portal](https://portal.azure.com/) にサインインします。
2.  表示するデータ ウェア ハウスを選択します。 
3.  選択したデータ ウェアハウスが、[概要] ブレードで開きます。 データ ウェアハウスに適用されているメンテナンス スケジュールが、 **[Maintenance schedule]\(メンテナンス スケジュール\)** の下に表示されます。

![[概要] ブレード](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>メンテナンス スケジュールの変更 

### <a name="portal"></a>ポータル
メンテナンス スケジュールをいつでも更新または、変更できます。 選択したインスタンスのアクティブなメンテナンス サイクルが経過している間、設定は保存されています。 指定した次のメンテナンス期間になると、設定がアクティブになります。 アクティブなメンテナンス イベント中にデータウェアハウスを監視する方法について[さらに理解する](https://docs.microsoft.com/azure/service-health/resource-health-overview)。 

### <a name="identifying-the-primary-and-secondary-windows"></a>プライマリ ウィンドウとセカンダリ ウィンドウの指定

プライマリ ウィンドウとセカンダリ ウィンドウは別の曜日範囲にする必要があります。 たとえば、プライマリ ウィンドウを火曜日から木曜日にしたら、土曜日から日曜日をセカンダリ ウィンドウにします。

データウェア ハウスに対するメンテナンス スケジュールを変更するには、次の手順のようにします。
1.  [Azure Portal](https://portal.azure.com/) にサインインします。
2.  更新するデータ ウェア ハウスを選択します。 [概要] ブレードで、ページが開きます。 
3.  [概要] ブレードの **[Maintenance Schedule (preview)]\(メンテナンス スケジュール (プレビュー)\)** 概要リンクを選択して、メンテナンス スケジュール設定用のページを開きます。 または、左側にあるリソース メニューの **[Maintenance Schedule]\(メンテナンス スケジュール\)** オプションを選択します。  

    ![概要ブレードのオプション](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. ページ上部のオプションを使用して、プライマリ メンテナンス ウィンドウの優先日の範囲を指定できます。 この選択は、平日または週末にプライマリ  ウィンドウが発生するかどうかを決定します。 選択した値に応じて、ドロップダウンの値が更新されます。 プレビュー中、一部のリージョンでは、利用可能な **[Day]\(日\)** オプションのフルセットがまだサポートされていない可能性があります。

   ![メンテナンスの設定ブレード](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. ドロップダウン リスト ボックスを使用して、優先されるプライマリとセカンダリのメンテナンス ウィンドウを選択します。
   - **[日]** :選択したウィンドウでメンテナンスを実行する優先日。
   - **[開始時刻]** :メンテナンス ウィンドウに対する優先開始時刻。
   - **[時間枠]** :時間枠の優先時間数。

   ブレードの下部にある **[スケジュールの概要]** 領域が、選択した値に基づいて更新されます。 
  
6. **[保存]** を選択します。 新しいスケジュールがアクティブになったことを確認するメッセージが表示されます。 

   メンテナンス スケジュールがサポートされていないリージョンにスケジュールを保存すると、次のメッセージが表示されます。 "Your settings are saved and become active when the feature becomes available in your selected region." (設定は保存されており、選択したリージョンで機能が使用可能になるとアクティブになります。)    

   ![リージョンの可用性に関するメッセージ](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>次の手順
- Azure Monitor を使用してアラートを作成、表示、管理する方法について[詳しく知る](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage)。
- ログ アラート ルール用の Webhook アクションについて[詳しく知る](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)。
- アクション グループの作成と管理について[詳しく知る](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)。
- Azure Service Health について[詳しく知る](https://docs.microsoft.com/azure/service-health/service-health-overview)。