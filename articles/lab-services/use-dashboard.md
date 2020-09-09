---
title: Azure Lab Services でクラスルーム ラボ用のダッシュボードを使用する | Microsoft Docs
description: Azure Lab Services でクラスルーム ラボ用のダッシュボードを使用する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4e59917aa385a344d79a62441e208df970f42285
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85444320"
---
# <a name="dashboard-for-classroom-labs"></a>クラスルーム ラボ用のダッシュボード
この記事では、Azure Lab Services でのクラスルーム ラボのダッシュボード ビューについて説明します。 

![ダッシュボード](./media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>コストと課金のタイル
このタイルには、次のコスト見積もりの詳細が表示されます。

| 設定 | 値 | 
| ------- | ----- | 
| [Quota hours]\(クォータ時間\) | ユーザーがスケジュールされた時間外に VM を使用できる最大時間数。 |
| [Scheduled hours]\(スケジュールされた時間\) | ラボで設定されたスケジュールに基づいて発生する時間。 この値は、すべてのスケジュール イベントに対して開始比または終了日が設定されている場合にのみ使用できます。 |
| [Hours/user]\(時間/ユーザー\) | クォータ時間とスケジュールされた時間の合計。 |
| [Maximum users]\(最大ユーザー数\) | 要求されるすべての仮想マシンに基づくラボ内のユーザーの最大数。 |
| [Hours x users]\(時間 x ユーザー\) | 時間/ユーザーにユーザー数を掛けた値。 |
| [Adjusted quota]\(調整されたクォータ\) | 特定のユーザーに追加されたクォータ時間の合計。 |
| [Total hours * $/hour]\(合計時間数 * $/時間\) | 選択された VM サイズに基づく 1 時間あたりのコスト。 これは、通常の従量課金制の価格に基づいています。 |
| [Total estimated cost]\(見積もりコストの合計\) | これは、現在の設定に基づくこのラボの最大の価格です。 |

## <a name="template-tile"></a>テンプレート タイル
このタイルには、次の情報が表示されます。

- テンプレートが作成された日付。 
- テンプレートが最後に公開された日付。 

また、 **[テンプレート]** ページに移動するためのリンクもあります。このページでは、クラスの[テンプレート VM を管理](how-to-create-manage-template.md)できます。 

## <a name="virtual-machine-pool-tile"></a>仮想マシン プールのタイル

このタイルには、次の情報が表示されます。

- 学生 (ユーザー) に割り当てられている仮想マシンの数
- 学生に未割り当ての仮想マシンの数

また、 **[Virtual machine pool]\(仮想マシン プール\)** ページに移動するためのリンクもあります。このページでは、ラボで[仮想マシンのプールを管理](how-to-set-virtual-machine-passwords.md)できます。 

## <a name="users-tile"></a>ユーザー タイル

このタイルには、次の情報が表示されます。

- クラスに登録されているユーザーの数
- ラボに追加されていてもクラスに登録されていないユーザーの数 

また、 **[ユーザー]** ページに移動するためのリンクもあります。このページでは、ラボの[ユーザーを管理](how-to-configure-student-usage.md)できます。 

## <a name="schedules-tile"></a>スケジュール タイル
ラボの現在スケジュール済みのイベントがタイルに表示されます。 また、 **[Schedule]\(スケジュール\)** ページに移動するためのリンクもあります。このページでは、[スケジュールを作成および管理](how-to-create-schedules.md)できます。 タイルには、スケジュールされた 2 つのイベントの詳細と、そのラボの残りのスケジュールされたイベントの数が表示されます。 

![スケジュールされたイベント](./media/use-dashboard/scheduled-events.png)

