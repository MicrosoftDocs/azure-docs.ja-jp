---
title: Azure リソースの予約を表示する | Microsoft Docs
description: Azure portal で Azure の予約を表示する方法について説明します。 API、PowerShell、CLI、Power BI を使用して予約と使用率を参照してください。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: ec91d0997717d536c3b47ce7b276f75b21d1baa1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681653"
---
# <a name="view-azure-reservations"></a>Azure の予約を表示する

購入した予約は、Azure portal で表示して管理することができます。

## <a name="permissions-to-view-a-reservation"></a>予約を表示するためのアクセス許可

予約を表示または管理するには、その閲覧者または所有者のアクセス許可が必要です。 既定では、予約を購入したユーザーとアカウント管理者には、予約注文と予約とに対する所有者ロールが自動的に割り当てられます。 その他のユーザーが予約を表示できるようにするには、予約注文または予約に対してそのユーザーを**所有者**または**閲覧者**として追加する必要があります。 予約の請求用サブスクリプションにユーザーを追加しても、それらのユーザーが自動的に予約に追加されるわけではありません。 

詳細については、「[予約を管理できるユーザーを追加または変更する](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)」を参照してください。

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Azure portal で予約と使用率を表示する

所有者または閲覧者として予約を表示するには

1. [Azure portal](https://portal.azure.com) にサインインします。
2. [[予約]](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) に移動します。
3. 一覧には、ご自分が所有者または閲覧者ロールになっている予約がすべて表示されます。 各予約には、最新の既知の使用率のパーセンテージが示されます。
4. 使用率をクリックすると、使用率の履歴と詳細が表示されます。 詳細については、以下のビデオをご覧ください。
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-cli"></a>API、PowerShell、CLI を使用して予約と使用率を取得する

予約の全一覧は、次のリソースを使用して取得します。
- [API: 予約注文 - 一覧](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: 予約注文 - 一覧](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI: 予約注文 - 一覧](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

Reserved Instance 使用量 API を使用して、[予約の使用率](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)を取得することもできます。 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Power BI で予約と使用率を確認する

Power BI ユーザーには、次の 2 とおりの方法があります。
- コンテンツパック: [Consumption Insights Power BI コンテンツ パック](/power-bi/desktop-connect-azure-cost-management)で、予約の購入と使用率のデータを入手できます。 このコンテンツ パックを使用して、目的のレポートを作成します。 
- コスト管理アプリ: 事前に作成されたレポートを [Cost Management アプリ](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)を使用して入手できます。このレポートをさらにカスタマイズすることもできます。

## <a name="next-steps"></a>次のステップ

- [Azure の予約を管理する](manage-reserved-vm-instance.md)。
- [従量課金制サブスクリプションの予約使用量について](understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)
- [CSP サブスクリプションの予約の使用状況について](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
