---
title: "Azure サブスクリプションの取り消し | Microsoft Docs"
description: "無料試用版サブスクリプションなど、Azure サブスクリプションを取り消す方法について説明します"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c415fada30aa0b0bd9b9d1e416bc37ef30653f68
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017


---
# <a name="cancel-your-subscription-for-azure"></a>Azure のサブスクリプションの取り消し

[アカウント管理者](billing-subscription-transfer.md#whoisaa)は Azure サブスクリプションを取り消すことができます。 サブスクリプションを取り消した後は、Azure のサービスとリソースにアクセスできなくなります。

サブスクリプションを取り消す前に、次の操作を行ってください。

* データをバックアップする。 たとえば、Azure Storage や SQL にデータを格納している場合は、コピーをダウンロードします。 仮想マシンを使用している場合は、そのイメージをローカルに保存します。
* サービスをシャットダウンする。 [管理ポータルのリソース ページ](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)に移動し、実行中の仮想マシン、アプリケーション、またはその他のサービスをすべて**停止**します。
* データの移行を検討する。 「[Move resources to new resource group or subscription (新しいリソース グループまたはサブスクリプションへのリソースの移動)](../azure-resource-manager/resource-group-move-resources.md)」を参照してください。

有料の [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を取り消しても、6 か月の期間の残りの期間について毎月請求されます。

## <a name="cancel-subscription-using-the-azure-portal"></a>Azure Portal を使用したサブスクリプションの取り消し

1. [[サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で、自分のサブスクリプションを選択します。

1. 取り消すサブスクリプションを選択し、**[サブスクリプションの取り消し]** をクリックします。

    ![[キャンセル] ボタンを示すスクリーン ショット](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)

1. 画面の指示に従って、取り消しを完了します。

## <a name="cancel-subscription-using-the-azure-account-center"></a>Azure アカウント センターを使用したサブスクリプションの取り消し

1. アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/subscriptions)にサインインします。

1. **[詳細および使用量を表示するにはサブスクリプションをクリックします]**で、取り消すサブスクリプションを選択します。

    ![選択されたサブスクリプション例を示すスクリーンショット](./media/billing-how-to-cancel-azure-subscription/Selectsub.png)

1. ページの右側にある **[サブスクリプションの取り消し]**を選択します。

    ![[サブスクリプションの取り消し] ボタンを示すスクリーンショット](./media/billing-how-to-cancel-azure-subscription/cancelsub.png)

1. **[はい、サブスクリプションをキャンセルします。]**を選択します。

    ![[キャンセル] ダイアログを示すスクリーン ショット](./media/billing-how-to-cancel-azure-subscription/cancelbox.png)

1. Click ![チェック マーク ボタン](./media/billing-how-to-cancel-azure-subscription/checkbutton.png) をクリックしてダイアログ ウィンドウを閉じ、サブスクリプション ページに戻ります。

## <a name="what-happens-after-i-cancel-my-subscription"></a>サブスクリプションの取り消し後の流れ

取り消し後は、課金がただちに停止されます。 ただし、取り消しがポータルに反映されるまでに最大で 10 分かかります。

その後、サービスが無効になります。 つまり、仮想マシンの割り当てが解除され、一時 IP アドレスが解放され、ストレージが読み取り専用になります。

無料試用版を使用している場合や、利用可能なクレジットがある場合を除き、最後の課金サイクルと取り消し日の間に発生した未払いの使用料金があれば課金されます。 課金サイクルの終わりに、最後の請求書が送信されます。

Microsoft では、お客様によってサブスクリプションが取り消されたあと、お客様がデータにアクセスする必要がある場合やその決定を変更する場合に備えて、データを完全に削除する前に 90 日間保管します。 データの保持に対しては課金されません。 詳しくは、[Microsoft Trust Center の Microsoft によるデータの管理方法](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)に関するページをご覧ください。

## <a name="reactivate-subscription"></a>サブスクリプションの再アクティブ化

従量課金制サブスクリプションを誤って取り消した場合は、[アカウント センターで再アクティブ化](billing-subscription-become-disable.md)することができます。

サブスクリプションが従量課金制でない場合は、取り消しから 90 日以内にサポートに問い合わせて、サブスクリプションの再アクティブ化を依頼してください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

まだ他に質問がある場合は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

