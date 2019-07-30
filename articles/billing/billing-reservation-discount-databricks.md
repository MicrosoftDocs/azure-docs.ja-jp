---
title: Azure Databricks の事前購入割引が適用される方法
description: Azure Databricks の事前購入割引が使用状況に適用される方法について説明します。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 7c1855b587ab1d603e9c6ac24a67b0f50065361f
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827637"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Azure Databricks の事前購入割引が適用される方法

購入期間中はいつでも、事前購入済み Azure Databricks コミット ユニット (DBCU) を使用できます。 Azure Databricks の使用状況はすべて、自動的に事前購入済み DBCU から差し引かれます。

VM とは異なり、事前購入済みユニットは時間単位では期限切れになりません。 それらは、購入の期間中にいつでも使用できます。 事前購入割引を受けるために、事前購入済みプランを使用状況の Azure Databricks ワークスペースに再デプロイしたり、割り当てたりする必要はありません。

事前購入割引は、Azure Databricks ユニット (DBU) の使用状況にのみ適用されます。 コンピューティング、ストレージ、およびネットワーキングなどの他の料金は別途課金されます。

## <a name="pre-purchase-discount-application"></a>事前購入割引の適用

Databricks の事前購入は、すべての Databricks ワークロードおよびレベルに適用されます。 事前購入は、前払いの Databricks コミット ユニットのプールと考えることができます。 ワークロードまたはレベルには関係なく、使用状況がプールから差し引かれます。 使用状況は、次の比率で差し引かれます。

| **ワークロード** | **DBU の適用率 — Standard レベル** | **DBU の適用率 — Premium レベル** |
| --- | --- | --- |
| データ分析 | 0.4 | 0.55 |
| Data Engineering | 0.15 | 0.30 |
| Data Engineering Light | 0.07 | 0.22 |

たとえば、データ分析 — Standard レベルの数量が使用された場合、事前購入済み Databricks コミット ユニットは 0.4 ユニット差し引かれます。 Data Engineering Light — Standard レベルの数量が使用された場合、事前購入済み Databricks コミット ユニットは 0.07 ユニット差し引かれます。

## <a name="determine-plan-use"></a>プランの使用を確認する

DBCU プランの使用を確認するには、Azure portal > **[予約]** に移動し、購入された Databricks プランをクリックします。 現在までの使用率がすべての残りのユニットと共に表示されます。 予約の使用を確認する方法の詳細については、「[予約の使用状況を表示する](billing-reservation-apis.md#see-reservation-usage)」の記事を参照してください。

## <a name="how-discount-application-shows-in-usage-data"></a>割引の適用が使用状況データに表示される方法

事前購入割引が Databricks の使用状況に適用される場合、オンデマンド料金は使用状況データに 0 として表示されます。 予約のコストと使用状況の詳細については、「[Enterprise Agreement の予約のコストと使用状況を取得する](billing-understand-reserved-instance-usage-ea.md)」を参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次の手順

- 予約を管理する方法については、「[Azure の予約の管理](billing-manage-reserved-vm-instance.md)」をご覧ください。
- 費用を節約するための事前購入 Azure Databricks の詳細については、[事前購入を使用した Azure Databricks のコストの最適化](billing-prepay-databricks-reserved-capacity.md)に関するページを参照してください。
- Azure の予約の詳細については、次の記事を参照してください。
  - [Azure の予約とは](billing-save-compute-costs-reservations.md)
  - [Azure での予約の管理](billing-manage-reserved-vm-instance.md)
  - [従量課金制料金のサブスクリプションの予約の使用状況について](billing-understand-reserved-instance-usage.md)
  - [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)
