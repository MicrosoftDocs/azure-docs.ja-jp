---
title: Azure Reservation コストのチャージ バック
description: Azure Reservation コストのチャージバックを表示する方法について学習します。
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 4387a1aa5b22c9808f36364b71f89a71736958b8
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689475"
---
# <a name="charge-back-azure-reservation-costs"></a>Azure Reservation コストのチャージ バック

マイクロソフト エンタープライズ契約および Microsoft 顧客契約の課金閲覧者は、予約の分散コスト データを表示できます。 コスト データを使用して、サブスクリプション、リソース グループ、リソース、またはタグの金額をパートナーにチャージ バックできます。 償却されたデータにおいて実行価格は、比例配分された時間単位の予約コストです。 コストは、その日のリソースごとの予約の使用状況における合計コストです。

個々のサブスクリプションを持つユーザーは、使用状況ファイルから分散コスト データを取得できます。 リソースの予約割引を受けると、使用状況ファイルの *AdditionalInfo* セクションに予約の詳細が含まれます。 詳細については、[Azure portal からの利用状況のダウンロード](https://docs.microsoft.com/azure/cost-management-billing/understand/download-azure-daily-usage#download-usage-from-the-azure-portal-csv)に関するページを参照してください。

## <a name="get-reservation-charge-back-data-for-chargeback"></a>チャージバック用の予約チャージ バック データを取得する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. **[実際のコスト]** で、 **[分散コスト]** メトリックを選択します。
1. 予約によって使用されたリソースを確認するには、 **[予約]** にフィルターを適用し、予約を選択します。
1. **[細分性]** を **[Monthly]\(毎月\)** または **[Daily]\(毎日\)** に設定します。
1. グラフの種類を **[テーブル]** に設定します。
1. **[リソース]** に **[グループ別]** オプションを設定します。

[![チャージバックに使用できる予約リソース コストを示す例](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Azure portal で予約使用率のコストを表示する方法を示すビデオを以下に示します。

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ

- 予約を管理する方法については、「[Azure の予約の管理](manage-reserved-vm-instance.md)」をご覧ください。
- Azure の予約の詳細については、次の記事を参照してください。
  - [Azure の予約とは](save-compute-costs-reservations.md)
  - [Azure での予約の管理](manage-reserved-vm-instance.md)
