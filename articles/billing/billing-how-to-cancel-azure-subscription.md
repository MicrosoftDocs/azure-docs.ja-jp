---
title: Azure サブスクリプションの取り消し | Microsoft Docs
description: 無料試用版サブスクリプションなど、Azure サブスクリプションを取り消す方法について説明します
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: banders
ms.openlocfilehash: 7756174f01e3fede17bec3e2ac185e89caddc097
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "68666443"
---
# <a name="cancel-your-azure-subscription"></a>Azure サブスクリプションの取り消し

サブスクリプションが不要になった場合は、Azure portal で Azure サブスクリプションを取り消すことができます。 

サブスクリプションを取り消す前に、次の操作を行ってください。
* データをバックアップする。 たとえば、Azure Storage や SQL にデータを格納している場合は、コピーをダウンロードします。 仮想マシンを使用している場合は、そのイメージをローカルに保存します。
* サービスをシャットダウンする。 [管理ポータルのリソース ページ](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)に移動し、実行中の仮想マシン、アプリケーション、またはその他のサービスをすべて**停止**します。
* データの移行を検討する。 「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/resource-group-move-resources.md)」を参照してください。
* すべてのリソースとすべてのリソース グループを削除します。 サブスクリプションをキャンセルする前に、それらを削除する必要があります。 各リソース グループは、個別に削除する必要があります。 リソース グループの削除中に、リソース グループ名を入力して削除を確定する必要があります。
* `AssignableScopes` でこのサブスクリプションを参照しているカスタム ロールがある場合は、それらのカスタム ロールを更新して、このサブスクリプションを削除する必要があります。 サブスクリプションを取り消した後でカスタム ロールを更新しようとすると、エラーが発生する可能性があります。 詳細については、「[カスタム ロールに関する問題のトラブルシューティング](../role-based-access-control/troubleshooting.md#problems-with-custom-roles)」と、「[Azure リソースのカスタム ロール](../role-based-access-control/custom-roles.md)」を参照してください。

有料の Azure サポート プランを取り消しても、サブスクリプション期間の残りの期間について請求されます。 詳細については、「[Azure のサポート プラン](https://azure.microsoft.com/support/plans/)」を参照してください。

## <a name="cancel-subscription-in-the-azure-portal"></a>Azure portal でサブスクリプションを取り消す

1. [Azure portal の [サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で、自分のサブスクリプションを選択します。
2. 取り消すサブスクリプションを選択します。
3. **[概要]** を選択し、 **[サブスクリプションの取り消し]** を選択します。
    ![[キャンセル] ボタンを示すスクリーン ショット](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. 画面の指示に従って、取り消しを完了します。


## <a name="who-can-cancel-a-subscription"></a>サブスクリプションを取り消すことができるユーザー

次の表では、サブスクリプションを取り消すために必要なアクセス許可について説明します。

|サブスクリプションの種類     |取り消すことができるユーザー  |
|---------|---------|
|Azure Web サイトで Azure にサインアップするときに作成されたサブスクリプション。 たとえば、[Azure 無料アカウント](https://azure.microsoft.com/offers/ms-azr-0044p/)または[従量課金制料金のアカウント](https://azure.microsoft.com/offers/ms-azr-0003p/)にサインアップした場合や、[Visual Studio サブスクライバー](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)としてサインアップした場合です。 |  サブスクリプションのアカウント管理者、所有者、共同作成者  |
|[Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) と [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  サブスクリプションのアカウント所有者、所有者、共同作成者       |
|[Azure プラン](https://azure.microsoft.com/offers/ms-azr-0017g/)と [DevTest 用 Azure プラン](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  サブスクリプションの所有者、共同作成者      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>サブスクリプションの取り消し後の流れ

取り消し後は、課金がただちに停止されます。 ただし、取り消しがポータルに反映されるまでに最大で 10 分かかります。 請求期間の途中でキャンセルした場合、期間終了後の一般的な請求日に、最終的な課金内容が送信されます。

取り消し後、サービスが無効になります。 つまり、仮想マシンの割り当てが解除され、一時 IP アドレスが解放され、ストレージが読み取り専用になります。

Microsoft では、お客様がデータにアクセスする必要がある場合や、サブスクリプションの取り消しをキャンセルされた場合に備えて、データを完全に削除する前に 90 日間保管します。 データの保持に対しては課金されません。 詳しくは、[Microsoft Trust Center の Microsoft によるデータの管理方法](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)に関するページをご覧ください。

## <a name="reactivate-subscription"></a>サブスクリプションの再アクティブ化

従量課金制のサブスクリプションを誤って取り消した場合は、[アカウント センターで再アクティブ化](billing-subscription-become-disable.md)することができます。

サブスクリプションが従量課金制のサブスクリプションでない場合は、取り消しから 90 日以内にサポートに問い合わせて、サブスクリプションの再アクティブ化を依頼してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
