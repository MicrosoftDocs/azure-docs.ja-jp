---
title: "Azure サブスクリプションの取り消し | Microsoft Docs"
description: "無料試用版サブスクリプションなど、Azure サブスクリプションを取り消す方法について説明します"
services: 
documentationcenter: 
author: genlin
manager: narmstr
editor: 
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 09c18ca6a967c2930ddd2b16d37f0bc606712ea1
ms.openlocfilehash: ccde3885f9412829cc6ff79b7efb4e3a2c099b5b


---
# <a name="cancel-your-azure-subscription"></a>Azure サブスクリプションの取り消し
アカウント管理者は Azure サブスクリプションを取り消すことができます。 サブスクリプションを取り消した後は、Azure のサービスとリソースにアクセスできなくなります。

サブスクリプションを取り消す前に、次の操作を行ってください。

* データをバックアップする。 たとえば、Azure Storage や SQL にデータを格納している場合は、コピーをダウンロードします。 仮想マシンを使用している場合は、そのイメージをローカルに保存します。
* サービスをシャットダウンする。 [管理ポータルのリソース ページ](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)に移動し、実行中の仮想マシン、アプリケーション、またはその他のサービスをすべて**停止**します。
* データの移行を検討する。 「[Move resources to new resource group or subscription (新しいリソース グループまたはサブスクリプションへのリソースの移動)](../azure-resource-manager/resource-group-move-resources.md)」を参照してください。

有料の [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を取り消しても、6 か月の期間の残りの期間について毎月請求されます。

## <a name="cancel-subscription-via-the-azure-portal"></a>Azure Portal を使用したサブスクリプションの取り消し
1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてログオンします。

   > [!NOTE]
   > サブスクリプションを取り消すことができるのは、アカウント管理者だけです。 サブスクリプションのアカウント管理者を確認する方法の詳細については、 [よく寄せられる質問](billing-subscription-transfer.md#faq)を参照してください。
   >
   >

2. ハブ メニューで、**[サブスクリプション]**を選択します。

    ![[サブスクリプション] ボタン](./media/billing-download-azure-invoice-daily-usage-date/submenu.png)

3. サブスクリプション ブレードで、取り消すサブスクリプションを選択し、**[Cancel Sub (サブスクリプションのキャンセル)]** をクリックして、[キャンセル] ブレードを開きます。

    ![cancel_ibiza](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
4. サブスクリプションの名前を入力し、取り消す理由を選択して、[キャンセル] ブレードの下部にある **[Cancel Sub (サブスクリプションのキャンセル)]** ボタンをクリックします。

> [!NOTE]
> 取り消しがポータルに反映されるまでに最大で 10 分かかりますが、課金は即座に停止されます。
>
>

## <a name="cancel-subscription-via-the-azure-account-center"></a>Azure アカウント センターを使用したサブスクリプションの取り消し
1. アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/subscriptions) にログオンします。
2. **[詳細および使用量を表示するにはサブスクリプションをクリックします]**で、取り消すサブスクリプションを選択します。

    ![selectsub](./media/billing-how-to-cancel-azure-subscription/Selectsub.png)
3. ページの右側にある **[サブスクリプションの取り消し]**を選択します。

    ![cancelsub](./media/billing-how-to-cancel-azure-subscription/cancelsub.png)
4. **[はい、サブスクリプションをキャンセルします。]**を選択します。

    ![cancelbox](./media/billing-how-to-cancel-azure-subscription/cancelbox.png)
5. Click ![checkbutton](./media/billing-how-to-cancel-azure-subscription/checkbutton.png) をクリックしてダイアログ ウィンドウを閉じ、サブスクリプション ページに戻ります。

Microsoft では、お客様によってサブスクリプションが取り消された後、お客様がデータにアクセスする必要がある場合やその決定を変更する場合に備えて、データを完全に削除する前に 90 日間保管します。 詳細については、[Microsoft Trust Center の Microsoft によるデータの管理方法](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)に関するページを参照してください。

## <a name="reactivate-subscription"></a>サブスクリプションの再アクティブ化
誤って従量課金制サブスクリプションを取り消した場合は、[アカウント センターで再アクティブ化](billing-subscription-become-disable.md)することができます。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。



<!--HONumber=Feb17_HO2-->


