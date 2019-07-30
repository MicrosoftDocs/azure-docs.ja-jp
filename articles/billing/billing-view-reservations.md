---
title: Azure リソースの予約を表示する | Microsoft Docs
description: Azure portal で Azure の予約を表示する方法について説明します。
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 21bf96866c14615009a17279ff2fdd04bf4116ad
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490263"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Azure portal に Azure の予約を表示する

Azure に対する予約を表示する方法は、ご利用のサブスクリプションの種類とアクセス許可に応じて 2 つあります。

## <a name="view-purchased-reservations"></a>購入した予約を表示する

既定では、予約を購入すると、お客様とアカウント管理者は予約を表示できます。 お客様とアカウント管理者は、予約注文および予約に対する所有者ロールを自動的に取得します。 その他のユーザーが予約を表示できるようにするには、予約注文または予約に対してそのユーザーを**所有者**または**閲覧者**として追加する必要があります。

詳細については、「[予約を管理できるユーザーを追加または変更する](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)」を参照してください。

所有者または閲覧者として予約を表示するには

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **予約**を検索します。
    ![Azure portal の検索を表示しているスクリーンショット](./media/billing-view-reservation/portal-reservation-search.png)  
3. 一覧には、ご自分が所有者または閲覧者ロールになっている予約がすべて表示されます。 各予約には、最新の既知の使用率のパーセンテージが示されます。  
    ![予約の一覧を表示した例](./media/billing-view-reservation/view-reservations.png)
4. 1 つの予約を選択して、過去 5 日間の使用率の傾向を確認します。  
    ![予約の使用率の傾向を表示した例](./media/billing-view-reservation/reservation-utilization.png)
5. Reserved Instance 使用量 API と [Microsoft Azure Consumption Insights Power BI コンテンツ パック](/power-bi/service-connect-to-azure-consumption-insights)を使用して、[予約の使用率](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)を取得することもできます。

予約の範囲を変更する必要がある場合は、予約を分割するか、予約を管理できるユーザーを変更します。[Azure の予約の管理](billing-manage-reserved-vm-instance.md)に関するページを参照してください。

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>エンタープライズ登録に対する予約トランザクションを表示する

 エンタープライズ登録を先導するパートナーがいる場合は、EA ポータルの **[レポート]** に移動することで予約を表示します。 その他のエンタープライズ登録の場合は、EA ポータルおよび Azure portal で予約を表示できます。 予約トランザクションを表示するには、EA 管理者である必要があります。

Azure portal で予約トランザクションを表示するには

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. "**コスト管理 + 請求**" を検索します。

    ![Azure portal の検索を表示するスクリーンショット](./media/billing-view-reservation/portal-cm-billing-search.png)

1. **[予約トランザクション]** を選択します。
1. 結果をフィルター処理するには、 **[期間]** 、 **[種類]** または **[説明]** を選択します。
1. **[適用]** を選択します。

    ![予約トランザクションの結果を示すスクリーンショット](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

API を使用してデータを取得するには、「[Get Reserved Instance transaction charges for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)」 (エンタープライズ顧客向けの予約インスタンス トランザクションを取得する) を参照してください。

## <a name="next-steps"></a>次の手順

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](billing-save-compute-costs-reservations.md)
- [Azure の予約の管理](billing-manage-reserved-vm-instance.md)

サービス プランの購入:

- [Cosmos DB 予約容量の前払い](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Azure SQL Database の予約容量を使用した SQL Database 計算リソースの前払い](../sql-database/sql-database-reserved-capacity.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)

ソフトウェア プランの購入:

- [Azure の予約からの Red Hat ソフトウェア プランの前払い](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Azure の予約からの SUSE ソフトウェア プランの前払い](../virtual-machines/linux/prepay-suse-software-charges.md)

使用状況の把握:

- [従量課金制サブスクリプションの予約使用量について](billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)
- [CSP サブスクリプションの予約の使用状況について](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
