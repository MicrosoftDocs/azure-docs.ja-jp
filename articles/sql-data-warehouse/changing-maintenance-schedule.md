---
title: Azure メンテナンス スケジュール (プレビュー) |Microsoft Docs
description: メンテナンス スケジューリングを使用すると、Azure SQL Data Warehouse サービスで新機能のロールアウト、アップグレード、パッチを行うために必要な予定メンテナンス イベントを計画することができます。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/03/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 4bc1867f0dd18a101b2e764970de4e1904b960e0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790860"
---
# <a name="change-a-maintenance-schedule"></a>メンテナンス スケジュールの変更 

## <a name="portal"></a>ポータル
メンテナンス スケジュールをいつでも更新または、変更できます。 選択したインスタンスのアクティブなメンテナンス サイクルが経過している間、設定は保存されています。 指定した次のメンテナンス期間になると、設定がアクティブになります。 アクティブなメンテナンス イベント中にデータウェアハウスを監視する方法について[さらに理解する](https://docs.microsoft.com/azure/service-health/resource-health-overview)。 

メンテナンス スケジュールを使用するには、7 日の間に 2 つのメンテナンス ウィンドウを選択する必要があります。 メンテナンス ウィンドウはそれぞれ 3 ～ 8 時間です。 メンテナンスは、メンテナンス ウィンドウ内であればいつでも行われる可能性がありますが、ウィンドウ外については事前通知なしでは行われません。 また、サービスではデータ ウェアハウスに新しいコードが展開されるので、接続が一時的に失われます。 

## <a name="identifying-the-primary-and-secondary-windows"></a>プライマリ ウィンドウとセカンダリ ウィンドウの指定

プライマリ ウィンドウとセカンダリ ウィンドウは別の曜日範囲にする必要があります。 たとえば、プライマリ ウィンドウを火曜日から木曜日にしたら、土曜日から日曜日をセカンダリ ウィンドウにします。

データウェア ハウスに対するメンテナンス スケジュールを変更するには、次の手順のようにします。
1.  [Azure Portal](https://portal.azure.com/) にサインインします。
2.  更新するデータ ウェア ハウスを選択します。 [概要] ブレードで、ページが開きます。 
3.  [概要] ブレードの **[Maintenance Schedule (preview)]\(メンテナンス スケジュール (プレビュー)\)** 概要リンクを選択して、メンテナンス スケジュール設定用のページを開きます。 または、左側にあるリソース メニューの **[Maintenance Schedule]\(メンテナンス スケジュール\)** オプションを選択します。  

    ![概要ブレードのオプション](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. ページ上部のオプションを使用して、プライマリ メンテナンス ウィンドウの優先日の範囲を指定できます。 この選択は、平日または週末にプライマリ  ウィンドウが発生するかどうかを決定します。 選択した値に応じて、ドロップダウンの値が更新されます。 プレビュー中、一部のリージョンでは、利用可能な **[Day]\(日\)** オプションのフルセットがまだサポートされていない可能性があります。

   ![メンテナンスの設定ブレード](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. ドロップダウン リスト ボックスを使用して、優先されるプライマリとセカンダリのメンテナンス ウィンドウを選択します。
   - **[日]**:選択したウィンドウでメンテナンスを実行する優先日。
   - **[開始時刻]**:メンテナンス ウィンドウに対する優先開始時刻。
   - **[時間枠]**:時間枠の優先時間数。

   ブレードの下部にある **[スケジュールの概要]** 領域が、選択した値に基づいて更新されます。 
  
6. **[保存]** を選択します。 新しいスケジュールがアクティブになったことを確認するメッセージが表示されます。 

   メンテナンス スケジュールがサポートされていないリージョンにスケジュールを保存すると、次のメッセージが表示されます。 "Your settings are saved and become active when the feature becomes available in your selected region." (設定は保存されており、選択したリージョンで機能が使用可能になるとアクティブになります。)    

   ![リージョンの可用性に関するメッセージ](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>次の手順
- ログ アラート ルール用の Webhook アクションについて[詳しく知る](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)。
- Azure Service Health について[詳しく知る](https://docs.microsoft.com/azure/service-health/service-health-overview)。


