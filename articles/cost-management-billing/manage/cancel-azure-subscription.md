---
title: Azure サブスクリプションの取り消し
description: 無料試用版サブスクリプションなど、Azure サブスクリプションを取り消す方法について説明します
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: banders
ms.openlocfilehash: 0d0dd5342cfc57da776e38ddef3df3b9c2199ac2
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735777"
---
# <a name="cancel-your-azure-subscription"></a>Azure サブスクリプションの取り消し

不要になった場合は、Azure portal で Azure サブスクリプションを取り消すことができます。

必須ではありませんが、Microsoft は、サブスクリプションをキャンセルする前に次の処置を講じることを "*推奨*" しています。

* データをバックアップする。 たとえば、Azure Storage や SQL にデータを格納している場合は、コピーをダウンロードします。 仮想マシンを使用している場合は、そのイメージをローカルに保存します。
* サービスをシャットダウンする。 [管理ポータルのリソース ページ](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)に移動し、実行中の仮想マシン、アプリケーション、またはその他のサービスをすべて **停止** します。
* データの移行を検討する。 「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。
* すべてのリソースとすべてのリソース グループを削除します。
* `AssignableScopes` でこのサブスクリプションを参照しているカスタム ロールがある場合は、それらのカスタム ロールを更新して、このサブスクリプションを削除する必要があります。 サブスクリプションを取り消した後でカスタム ロールを更新しようとすると、エラーが発生する可能性があります。 詳細については、[カスタム ロールに関する問題のトラブルシューティング](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles)に関するセクションと、「[Azure カスタム ロール](../../role-based-access-control/custom-roles.md)」を参照してください。

有料の Azure サポート プランを取り消しても、サブスクリプション期間の残りの期間について請求されます。 詳細については、「[Azure のサポート プラン](https://azure.microsoft.com/support/plans/)」を参照してください。

## <a name="who-can-cancel-a-subscription"></a>サブスクリプションを取り消すことができるユーザー

次の表では、サブスクリプションを取り消すために必要なアクセス許可について説明します。

|サブスクリプションの種類     |取り消すことができるユーザー  |
|---------|---------|
|Azure Web サイトで Azure にサインアップするときに作成されたサブスクリプション。 たとえば、[Azure 無料アカウント](https://azure.microsoft.com/offers/ms-azr-0044p/)または[従量課金制料金のアカウント](https://azure.microsoft.com/offers/ms-azr-0003p/)にサインアップした場合や、[Visual Studio サブスクライバー](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)としてサインアップした場合です。 |  サブスクリプションの所有者とアカウント管理者  |
|[Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) と [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  サブスクリプションの所有者とアカウント所有者       |
|[Azure プラン](https://azure.microsoft.com/offers/ms-azr-0017g/)と [DevTest 用 Azure プラン](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  サブスクリプションの所有者      |


## <a name="cancel-subscription-in-the-azure-portal"></a>Azure portal でサブスクリプションを取り消す

1. [Azure portal の [サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で、自分のサブスクリプションを選択します。
1. 取り消すサブスクリプションを選択します。
1. **[概要]** を選択し、 **[サブスクリプションの取り消し]** を選択します。
    ![[キャンセル] ボタンを示すスクリーン ショット](./media/cancel-azure-subscription/cancel_ibiza.png)
1. 画面の指示に従って、取り消しを完了します。

> [!NOTE]
> パートナーは、顧客から要求された場合、または未払いや不正行為があった場合に、サブスクリプションを中断または取り消すことができます。 詳細については、「[サブスクリプションを中断または取り消す](/partner-center/create-a-new-subscription#suspend-or-cancel-a-subscription)」セクションを参照してください。

## <a name="cancel-a-support-plan"></a>サポート プランを取り消す

Azure Web サイトか Azure portal からサポート プランを購入した場合、または Microsoft 顧客契約の下でサポート プランをご利用の場合、サポート プランを取り消すことができます。 Microsoft の代理店またはパートナーからサポート プランを購入した場合は、代理店またはパートナーにお問い合わせください。 

1. Azure portal で **[コストの管理と請求]** に移動します。
1. **[課金]** の **[当月サービス利用料金]** を選択します。
1. サポート プラン明細項目の右側にある省略記号 ( **...** ) を選択し、 **[Turn off auto-renewal]\(自動更新をオフにします\)** を選択します。

## <a name="what-happens-after-subscription-cancellation"></a>サブスクリプションの取り消し後は、どうなりますか?

取り消し後は、課金がただちに停止されます。 ただし、取り消しがポータルに反映されるまでに最大で 10 分かかります。 請求期間の途中でキャンセルした場合、期間終了後の一般的な請求日に、最終的な課金内容が送信されます。

取り消し後、サービスが無効になります。 つまり、仮想マシンの割り当てが解除され、一時 IP アドレスが解放され、ストレージが読み取り専用になります。

Microsoft では、サブスクリプションが取り消された後、お客様がデータにアクセスする必要がある場合や気が変わった場合に備えて、データを完全に削除するまで 30 から 90 日待ちます。 データの保持に対しては課金されません。 詳しくは、[Microsoft Trust Center の Microsoft によるデータの管理方法](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)に関するページをご覧ください。

## <a name="delete-free-trial-subscription"></a>無料試用版サブスクリプションの削除

無料試用版サブスクリプションをお持ちの場合は、サブスクリプションが自動的に削除されるまで 30 日待つ必要はありません。 サブスクリプションは、取り消してから "*3 日*" 後に削除できます。 **[サブスクリプションの削除]** オプションは、サブスクリプションを取り消してから 3 日後まで使用できません。

1. サブスクリプションを取り消した日から 3 日待ちます。
1. Azure portal の [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ページでサブスクリプションを選択します。
1. 削除するサブスクリプションを選択します。
1. **[概要]** を選択し、 **[サブスクリプションの削除]** を選択します。

## <a name="reactivate-a-subscription"></a>サブスクリプションの再アクティブ化

従量課金制のサブスクリプションを誤って取り消した場合は、[Azure portal で再度有効](subscription-disabled.md)にすることができます。

サブスクリプションが従量課金制のサブスクリプションでない場合は、取り消しから 90 日以内にサポートに問い合わせて、サブスクリプションの再アクティブ化を依頼してください。

## <a name="why-dont-i-see-the-cancel-subscription-option-on-the-azure-portal"></a>Azure portal にサブスクリプション取り消しオプションが表示されないのはなぜですか? 

サブスクリプションを取り消すために必要なアクセス許可がない可能性があります。 さまざまな種類のサブスクリプションを取り消すことができるユーザーについては、「[サブスクリプションを取り消すことができるユーザー](#who-can-cancel-a-subscription)」を参照してください。

## <a name="how-do-i-delete-my-azure-account"></a>Azure アカウントを削除するにはどうすればよいですか?

*自分のすべての個人情報を含め、自分のアカウントを削除する必要があります。アクティブな (無料試用版) サブスクリプションは既に取り消しました。アクティブなサブスクリプションはなく、アカウントを完全に削除したいと考えています。*

* 組織を通じて Azure Active Directory アカウントを持っている場合は、Azure AD 管理者がアカウントを削除できます。 その後、サービスが無効になります。 つまり、仮想マシンの割り当てが解除され、一時 IP アドレスが解放され、ストレージが読み取り専用になります。 要約すると、取り消しが完了すると、すぐに課金が停止されます。

* 組織を通じた Azure AD アカウントではない場合は、Azure サブスクリプションを取り消して削除した後、アカウントからクレジット カードを削除することができます。 このアクションによってアカウントは削除されませんが、操作できなくなります。 関連付けられている Microsoft アカウントが他の目的に使用されていない場合は、さらに進んで、それを削除することもできます。

## <a name="how-do-i-cancel-a-visual-studio-professional-account"></a>Visual Studio Professional アカウントを取り消すにはどうすればよいですか?

「[更新とキャンセル](/visualstudio/subscriptions/faq/admin/renewal-cancellation)」の記事を参照してください。 Visual Studio Azure サブスクリプションを持っている場合は、それも取り消して削除する必要があります。

## <a name="next-steps"></a>次のステップ

- 必要に応じて、従量課金制サブスクリプションを [Azure portal](subscription-disabled.md) で再度有効にすることができます。