---
title: Azure Reservation の購入と払戻のトランザクションを表示する
description: Azure Reservation の購入と払戻のトランザクションを表示する方法について学習します。
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: d9e5269468f7cd4571e7ae686af7f1ef159b4ef3
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681704"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>予約購入と払戻のトランザクションの表示

予約購入と払戻のトランザクションを表示するには、いくつかの方法があります。 Azure portal、Power BI、REST API を使用できます。

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Azure portal で予約トランザクションを表示する

エンタープライズ登録または Microsoft 顧客契約の課金管理者は、Cost Management と課金で予約トランザクションを表示できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. "**コスト管理 + 請求**" を検索します。
1. **[予約トランザクション]** を選択します。
1. 結果をフィルター処理するには、 **[期間]** 、 **[種類]** または **[説明]** を選択します。
1. **[適用]** を選択します。

[![Azure portal の予約トランザクションを示すスクリーンショット](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Power BI で予約トランザクションを表示する

エンタープライズ登録または Microsoft 顧客契約の課金管理者は、Cost Management Power BI アプリで予約トランザクションを表示できます。

1. [Cost Management Power BI アプリ](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)を取得します。
1. RI の購入レポートに移動します。

[![予約トランザクションを示す例](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

詳細については、[Enterprise Agreement 用の Azure Cost Management Power BI アプリ](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)に関するページを参照してください。

## <a name="use-apis-to-get-reservation-transactions"></a>API を使用して予約トランザクションを取得する

マイクロソフト エンタープライズ契約 (EA) と Microsoft 顧客契約のユーザーは、[予約トランザクション - List API](https://docs.microsoft.com/rest/api/consumption/reservationtransactions/list) を使用して、予約トランザクション データを取得できます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ

- 予約を管理する方法については、「[Azure の予約の管理](manage-reserved-vm-instance.md)」をご覧ください。
- Azure の予約の詳細については、次の記事を参照してください。
  - [Azure の予約とは](save-compute-costs-reservations.md)
  - [Azure での予約の管理](manage-reserved-vm-instance.md)
