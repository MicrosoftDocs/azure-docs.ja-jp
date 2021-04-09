---
title: メンテナンス通知にポータルを使用する
description: ポータルを使用して Azure で実行されている仮想マシンのメンテナンス通知を表示し、セルフサービス メンテナンスを開始します。
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: a13d79a28ac07d736b1eaf0d0e6b7f7b1ba4a4ec
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557711"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>ポータルを使用した計画済みメンテナンスの通知の処理

**この記事は、Linux と Windows の両方を実行する仮想マシンに適用されます。**

[計画メンテナンス](maintenance-notifications.md) ウェーブをスケジュールすると、影響を受ける仮想マシンの一覧を確認できます。 

Azure Portal を使用して、VM のメンテナンス スケジュールを検索できます。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 左側のナビゲーションにある **[仮想マシン]** をクリックします。

3. [仮想マシン] ウィンドウの **[列の編集]** ボタンをクリックして使用できる列の一覧を表示します。

4. 次の列を選択して追加します。

   **メンテナンスの状態**:VM のメンテナンス状態を表示します。 以下のような値が表示されます。
      
    | 値 | 説明 |
    |-------|-------------|
    | 今すぐ開始 | VM はセルフサービス メンテナンス期間内であり、メンテナンスを手動で開始できます。 VM でメンテナンスを開始する方法については、以下を参照してください。 | 
    | スケジュール | VM は、メンテナンスを開始するオプションが指定されない状態でメンテナンスがスケジュールされています。 このビューで [Maintenance - Scheduled]\(メンテナンス - スケジュール済み\) 期間を選択するか、VM でクリックすることで、メンテナンス期間を知ることができます。 | 
    | 更新済み | VM が既に更新され、この時点で必要な操作はありません。 | 
    | 後で再試行してください | メンテナンスを開始し、成功しませんでした。 セルフサービス メンテナンス オプションを後で使用できます。 | 
    | 今すぐやり直す | 以前失敗した手動開始メンテナンスを再試行することができます。 | 
    | - | VM は、計画済みメンテナンス ウェーブの一部ではありません。 |

   **Maintenance - Self-service (メンテナンス - セルフサービス) 期間**: VM 上でメンテナンスを自動開始できる期間を表示します。
   
   **Maintenance - Scheduled (メンテナンス - スケジュール済み) 期間**: メンテナンスを完了するために Azure が VM をメンテナンスする期間を表示します。 



## <a name="notification-and-alerts-in-the-portal"></a>ポータルの通知とアラート

Azure は、サブスクリプション所有者と共同所有者グループに電子メールを送信することで、計画メンテナンスのスケジュールを伝えます。 Azure アクティビティ ログ アラートを作成して、この通信にその他の受信者とチャネルを追加することができます。 詳細については、「[サービス通知のアクティビティ ログ アラートを作成する](../service-health/alerts-activity-log-service-notifications-portal.md)」を参照してください。

**[イベントの種類]** が **[計画済みメンテナンス]** に設定され、 **[サービス]** が **[Virtual Machine Scale Sets]** または **[Virtual Machines]** に設定されていることを確認します

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>ポータルから VM に対するメンテナンスを開始する

VM の詳細情報を見ているときに、メンテナンスに関連する詳細情報をさらに表示することができます。  
VM が計画メンテナンス ウェーブに含まれる場合、VM の詳細ビューの上部には、新しい通知リボンが追加されます。 また、可能な場合にメンテナンスを開始する新しいオプションが追加されます。 


メンテナンス通知をクリックすると、計画メンテナンスの詳細情報が記載されたメンテナンス ページが表示されます。 そこから、VM で **メンテナンスを開始** できます。

メンテナンスを開始すると、仮想マシンがメンテナンスされ、数分以内に結果を反映してメンテナンス状態が更新されます。

セルフサービス ウィンドウを閉じてしまった場合でも、Azure で VM をメンテナンスするときにウィンドウを表示することができます。 


## <a name="next-steps"></a>次のステップ

また、[Azure CLI](maintenance-notifications-cli.md) または [PowerShell](maintenance-notifications-powershell.md)を使用して、計画メンテナンスを処理することもできます。
