---
title: "Log Analytics でのデータ使用状況の分析 | Microsoft Docs"
description: "OMS サービスに送信されているデータの量は、Log Analytics の使用量ダッシュボードで確認できます。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 7e3d4b83fefdc70f292cf85b682cf8ed756bf4c5
ms.openlocfilehash: e7f04df679604f274c8ad9bf4daddc63c8b5418a
ms.lasthandoff: 02/22/2017


---
# <a name="analyze-data-usage-in-log-analytics"></a>Log Analytics でのデータ使用状況の分析
Log Analytics は、データを収集して OMS サービスに定期的に送信します。  OMS サービスに送信されているデータの量は、**[Log Analytics Usage (Log Analytics の使用量)]** ダッシュボードで確認できます。 このダッシュボードには、ソリューションから送信されているデータの量や、サーバーからデータが送信される頻度も表示されます。

> [!NOTE]
> 無料アカウントをご利用の場合、OMS サービスに送信できるデータの上限は、1 日あたり 500 MB です。 1 日の上限に達した場合、データの分析は停止し、翌日の初めに再開されます。 その場合、OMS によって受け付けられなかったデータや処理されなかったデータは再送する必要があります。

1 日あたりの使用量の上限を超えたか、上限に近づいている場合、必要に応じてソリューションを削除し、OMS サービスに送信されるデータの量を減らすことができます。 ソリューションの削除の詳細については、「 [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md)」 (ソリューション ギャラリーから Log Analytics ソリューションを追加する) を参照してください。

![[使用量] ダッシュボード](./media/log-analytics-usage/usage-dashboard01.png)

**[Log Analytics Usage (Log Analytics の使用量)]** ダッシュボードには、次の情報が表示されます。

- データ ボリューム
    - (現在の時間範囲に基づく) 経時のデータ ボリューム
    - ソリューション別のデータ ボリューム
    - コンピューターに関連付けられていないデータ
- [Computers (コンピューター)]
    - データを送信しているコンピューター
    - 過去 24 時間のデータがないコンピューター
- プラン
    - Insight と Analytics のノード
    - Automation と Control のノード
    - セキュリティ ノード
- パフォーマンス
    - データの収集とインデックス作成に要した時間
- クエリのリスト

## <a name="understanding-nodes-for-oms-offers"></a>OMS プランのノードについて

"*ノード単位 (OMS)*" の価格レベルを使用している場合は、有効にしたノードとソリューションの数に基づいて料金が発生します。 使用されている各プランのノード数は、使用量ダッシュボードの "*オファリング*" セクションで確認できます。

![[使用量] ダッシュボード](./media/log-analytics-usage/log-analytics-usage-offerings.png)

## <a name="to-work-with-usage-data"></a>使用量データを扱うには
1. まだサインインしていない場合は、Azure サブスクリプションを使用して [Azure Portal](https://portal.azure.com) にサインインします。
2. **ハブ** メニューで **[その他のサービス]** をクリックし、リソースの一覧で「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** をクリックします。  
    ![Azure のハブ](./media/log-analytics-usage/hub.png)
3. **[Log Analytics]** ダッシュボードにワークスペースの一覧が表示されます。 ワークスペースを選択します。
4. *[ワークスペース]* ダッシュボードで、**[Log Analytics Usage (Log Analytics の使用量)]** をクリックします。
5. **[Log Analytics Usage (Log Analytics の使用量)]** ダッシュボードで、**[時間: 過去 24 時間]** をクリックして期間を変更します。  
    ![期間](./media/log-analytics-usage/time.png)
6. 確認したい領域が示されている使用量カテゴリ ブレードを確認します。 ブレードを選択し、その項目をクリックして、[[ログ検索]](log-analytics-log-searches.md) に詳細を表示します。  
    ![データ使用量ブレードの例](./media/log-analytics-usage/blade.png)
7. [ログ検索] ダッシュボードで、検索によって返された結果を確認します。  
    ![使用量のログの検索の例](./media/log-analytics-usage/usage-log-search.png)


## <a name="next-steps"></a>次のステップ
* 「[Log Analytics におけるログの検索](log-analytics-log-searches.md)」を参照し、各種機能およびソリューションによって収集され、OMS 似送信された詳細情報を確認します。

