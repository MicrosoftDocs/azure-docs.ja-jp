---
title: Azure サブスクリプションの取り消し | Microsoft Docs
description: 無料試用版サブスクリプションなど、Azure サブスクリプションを取り消す方法について説明します
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/23/2019
ms.author: banders
ms.openlocfilehash: 374224663323e75a1d1413c47490bd3aacd00267
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57876818"
---
# <a name="cancel-your-subscription-for-azure"></a>Azure のサブスクリプションの取り消し

[アカウント管理者](billing-subscription-transfer.md#whoisaa)は Azure サブスクリプションを取り消すことができます。 サブスクリプションを取り消した後は、Azure のサービスとリソースにアクセスできなくなります。

サブスクリプションを取り消す前に、次の操作を行ってください。

* データをバックアップする。 たとえば、Azure Storage や SQL にデータを格納している場合は、コピーをダウンロードします。 仮想マシンを使用している場合は、そのイメージをローカルに保存します。
* サービスをシャットダウンする。 [管理ポータルのリソース ページ](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)に移動し、実行中の仮想マシン、アプリケーション、またはその他のサービスをすべて**停止**します。
* データの移行を検討する。 「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/resource-group-move-resources.md)」を参照してください。

有料の Azure サポート プランを取り消しても、サブスクリプション期間の残りの期間について請求されます。 詳細については、「[Azure のサポート プラン](https://azure.microsoft.com/support/plans/)」を参照してください。

## <a name="cancel-subscription-using-the-azure-portal"></a>Azure Portal を使用したサブスクリプションの取り消し

1. Azure ポータルの [[サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で、自分のサブスクリプションを選択します。
2. 取り消すサブスクリプションを選択します。
3. **[概要]** を選択し、**[サブスクリプションの取り消し]** を選択します。

    ![[キャンセル] ボタンを示すスクリーン ショット](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. 画面の指示に従って、取り消しを完了します。

## <a name="what-happens-after-i-cancel-my-subscription"></a>サブスクリプションの取り消し後の流れ

取り消し後は、課金がただちに停止されます。 ただし、取り消しがポータルに反映されるまでに最大で 10 分かかります。

その後、サービスが無効になります。 つまり、仮想マシンの割り当てが解除され、一時 IP アドレスが解放され、ストレージが読み取り専用になります。

請求期間の途中でキャンセルした場合、期間終了後の一般的な請求日に、最終的な課金内容が送信されます。 

Microsoft では、お客様がデータにアクセスする必要がある場合や、サブスクリプションの取り消しをキャンセルされた場合に備えて、データを完全に削除する前に 90 日間保管します。 データの保持に対しては課金されません。 詳しくは、[Microsoft Trust Center の Microsoft によるデータの管理方法](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)に関するページをご覧ください。

## <a name="reactivate-subscription"></a>サブスクリプションの再アクティブ化

従量課金制サブスクリプションを誤って取り消した場合は、[アカウント センターで再アクティブ化](billing-subscription-become-disable.md)することができます。

サブスクリプションが従量課金制でない場合は、取り消しから 90 日以内にサポートに問い合わせて、サブスクリプションの再アクティブ化を依頼してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
