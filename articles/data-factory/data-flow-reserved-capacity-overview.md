---
title: 予約容量を使用したコンピューティング コストの節約
description: Azure Data Factory データ フローの予約容量を購入してコンピューティング コストを節約する方法について説明します。
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.date: 02/05/2021
ms.openlocfilehash: 26a4692603d8e8a80a52ea77bdd56617131cea5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593902"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>予約容量を使用してリソースのコストを節約する - Azure Data Factory データ フロー

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

コンピューティング リソースの予約を確約することで、Azure Data Factory データ フローのコストを従量課金制の料金よりも節約できます。 予約容量では、ADF データ フローの 1 年間または 3 年間の使用を確約することで、コンピューティング コストの大幅な割引を受けることができます。 予約容量を購入するには、Azure リージョン、コンピューティングの種類、コアの数量、期間を指定する必要があります。

その予約を特定のファクトリまたは統合ランタイムに割り当てる必要はありません。 既存のファクトリや新しくデプロイされたファクトリには、この特典が自動的に適用されます。 予約を購入することで、データ フローのコンピューティング コストの 1 年間または 3 年間の使用を確約したことになります。 予約を購入するとすぐに、予約の属性に一致するコンピューティング料金は従量課金制で課金されなくなります。 

[予約数量](https://portal.azure.com)を購入する場合、[前払いまたは月払い](../cost-management-billing/reservations/prepare-buy-reservation.md)での予約を選択します。 予約容量を購入するには:

- 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制料金の個々のサブスクリプションで所有者ロールである必要があります。
- Enterprise サブスクリプションの場合、[EA ポータル](https://ea.azure.com)で **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。 予約容量。

エンタープライズおよび従量課金制のお客様に対する予約購入の課金方法の詳細については、「[エンタープライズ加入契約に適用される Azure の予約の使用状況について](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)」および「[従量課金制サブスクリプションに適用される Azure の予約の使用状況について](../cost-management-billing/reservations/understand-reserved-instance-usage.md)」をご覧ください。

> [!NOTE]
> 予約容量を購入しても、特定のインフラストラクチャ リソース (仮想マシンまたはクラスター) の使用が事前に割り当てされたり、予約されたりすることはありません。

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>購入前に必要とする適切な Azure IR のサイズを決定する

予約のサイズは、同じコンピューティング レベルを使用する既存の、またはすぐにデプロイされる予定のデータ フローによって使用されるコンピューティングの合計量に基づいて決定する必要があります。

たとえば、32 コアで最適化されたメモリを使用して、パイプラインを 1 時間に 1 回実行しているとします。 さらに、来月のうちに、汎用 64 コアを使用する追加のパイプラインをデプロイする予定があるとします。 また、少なくとも 1 年間はこれらのリソースが必要になることがわかっているとします。 この場合は、1 時間にそれぞれのコンピューティングの種類で必要なコア数を入力します。 Azure Portal で、[予約] を検索します。 [Data Factory] > [データ フロー] を選択し、メモリ最適化には「32」を、汎用には「64」を入力します。

## <a name="buy-reserved-capacity"></a>予約容量をご購入ください

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]**  >  **[予約]** を選択します。
3. **[追加]** を選択し、 **[購入予約]** ペインで **[ADF データ フロー]** を選択して、ADF データ フローの新しい予約を購入します。
4. 選択した必要なフィールドと属性に入力することで、予約容量の割引を受けることができます。 割引が適用されるデータ フローの実際の数は、選択されたスコープと数量によって異なります。
5. 予約容量の予約コストについては、「**コスト**」セクションを参照してください。
6. **[購入]** を選択します。
7. **[この予約を表示する]** を選択すると、購入の状態が表示されます。

## <a name="cancel-exchange-or-refund-reservations"></a>予約の取り消し、交換、または返金

一定の制限付きで、予約の取り消し、交換、または返金を行うことができます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)」を参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約割引を理解する](data-flow-understand-reservation-charges.md)
