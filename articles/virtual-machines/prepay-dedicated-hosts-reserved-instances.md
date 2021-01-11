---
title: Azure Dedicated Host の前払いでコストを節約する
description: Azure Dedicated Hosts の予約インスタンスを購入してコンピューティング コストを削減する方法について説明します。
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 919ff7474dd64cfe3005975beb4cab6f77c177ce
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87825261"
---
# <a name="save-costs-with-azure-dedicated-host-reservations"></a>Azure Dedicated Host 予約によるコスト削減

Azure Dedicated Host の予約インスタンスにコミットすると、コストを削減することができます。 予約割引は、予約スコープと属性に一致する実行中の専用ホストの数に対して自動的に適用されます。 割引を取得するために、専用ホストに予約を割り当てる必要はありません。 予約インスタンスの購入では、使用量のコンピューティング部分のみが対象となり、ソフトウェア ライセンスのコストも含まれます。 [仮想マシン用 Azure 専用ホストに関する概要](./windows/dedicated-hosts.md)を参照してください。

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>購入前に適切な専用ホスト SKU を決定する


予約を購入する前に、必要な専用ホストを決定する必要があります。 SKU は専用ホストに対して定義されており、VM シリーズと種類を表します。 

まず、[Windows 仮想マシン](./sizes.md)または [Linux](./sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) のサポートされているサイズを調べて、VM シリーズを特定します。

次に、Azure Dedicated Host でそれがサポートされているかどうかを確認します。 [Azure Dedicated Host の価格](https://aka.ms/ADHPricing)ページには、専用ホスト SKU の完全な一覧、CPU 情報、およびさまざまな価格オプション (予約インスタンスを含む) があります。

VM シリーズをサポートする複数の SKU (異なる種類) が見つかる場合があります。 ホストの容量 (vCPU の数) を比較して、最適な SKU を特定します。 予約は、同じ VM シリーズ (DSv3_Type1 と DSv3_Type2 など) をサポートする複数の専用ホスト SKU に適用できますが、異なる VM シリーズ (DSv3 と ESv3 など) には適用できないことに注意してください。



## <a name="purchase-restriction-considerations"></a>購入の制限に関する考慮事項

予約インスタンスは、ほとんどの専用ホストのサイズで使用できますが、一部例外があります。

次の場合、予約割引は適用されません。

- **クラウド** - ドイツまたは中国の各リージョンでは、予約購入を利用できません。

- **クォータの不足** - 単一サブスクリプションをスコープとする予約の場合、新しい予約インスタンスに割り当てることのできる vCPU クォータがそのサブスクリプションに存在していることが必要です。 たとえば、ターゲット サブスクリプションに、DSv3 シリーズに対して 10 vCPU のクォータ制限がある場合、このシリーズをサポートする予約専用ホストを購入することはできません。 予約のクォータ チェックには、既にサブスクリプションにデプロイされている VM と専用のホストが含まれます。  [クォータを増やす要求を作成](../azure-portal/supportability/resource-manager-core-quotas-request.md) して、この問題を解決できます。

- **容量制限**- まれに、リージョンの容量が低下していることが原因で、専用ホスト SKU のサブセットに対する新しい予約の購入が Azure によって制限されることがあります。

## <a name="buy-a-reservation"></a>予約の購入

Azure Dedicated Host インスタンスの予約インスタンスは、 [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D) で購入できます。

予約の支払いは、 [前払いまたは月払い](../cost-management-billing/reservations/prepare-buy-reservation.md)で行います。 Dedicated Host インスタンスの購入には、次の要件が適用されます。

- 少なくとも 1 つの EA サブスクリプションまたは従量課金制料金のサブスクリプションの所有者ロールである必要があります。

- EA サブスクリプションの場合、 [EA ポータル](https://ea.azure.com/)で  **[予約インスタンスを追加します]**  オプションを有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。

- クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理エージェントまたはセールス エージェントのみが予約購入できます。

インスタンスを購入する場合、次のことが必要です。

1.  [Azure portal](https://portal.azure.com/) にサインインします。

2.  **[すべてのサービス]**  \> **[予約]** を選択します。

3.  **[追加]**  を選択して新しい予約を購入してから、 **[Dedicated Host]** をクリックします。

4. 必須フィールドに入力します。 選択した属性と一致する Dedicated Host インスタンスを実行することで、予約割引を受けることができます。 割引を受ける Dedicated Host インスタンスの実際の数は、選択したスコープと数量によって変わります。

EA 契約を結んでいる場合、 **[Add more option]\(さらにオプションを追加\)**  を使用してインスタンスをすばやく追加することができます。 このオプションは、他のサブスクリプションの種類では使用できません。

| **フィールド**           | **説明**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| サブスクリプション        | 予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、予約のコストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号:MS-AZR-0017P または MS-AZR-0148P) または Microsoft Customer Agreement または従来課金制の個々のサブスクリプション (プラン番号:MS-AZR-0003P または MS-AZR-0023P)。 年額コミットメント残高から料金が差し引かれるか(使用可能な場合)、超過料金として課金されます。 従量課金制料金のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。 |
| Scope               | 1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を予約のスコープにすることができます。 以下を選択した場合:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| リージョン              | 予約の対象となる Azure リージョン。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Dedicated Host のサイズ | Dedicated Host インスタンスのサイズ。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| 期間                | 1 年間または 3 年間。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Quantity            | 予約内で購入しているインスタンス数。 この数量は、課金の割引を受けられる実行中の Dedicated Host インスタンスの数です。                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **単一のリソース グループのスコープ** — 選択されたリソース グループ内の一致するリソースにのみ、予約割引を適用します。

- **単一サブスクリプション** — 選択されたサブスクリプションの一致するリソースに予約割引を適用します。

- **共有スコープ** — 課金コンテキスト内にある有効なサブスクリプションの一致するリソースに予約割引を適用します。 EA の顧客の場合、課金コンテキストは課金です。 従量課金制料金の個々のサブスクリプションの場合、課金スコープはアカウント管理者によって作成されるすべての有効なサブスクリプションです。

## <a name="usage-data-and-reservation-utilization"></a>使用状況データと予約の使用率

使用状況データには、予約割引が適用される使用量に対する 0 の実効価格があります。 予約ごとに、予約割引を受けた VM インスタンスを確認できます。

使用状況データに予約割引を表示する方法の詳細については、EA を使用している場合は、 [エンタープライズ加入契約に適用される Azure の予約の使用状況](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)に関するページを参照してください。 個別サブスクリプションを使用している場合は、 [従量課金制サブスクリプションに適用される Azure の予約の使用状況](../cost-management-billing/reservations/understand-reserved-instance-usage.md)に関するページを参照してください。

## <a name="change-a-reservation-after-purchase"></a>購入後に予約を変更する

購入後に予約に次の種類の変更を加えることができます。

- 予約スコープの更新

- インスタンス サイズの柔軟性 (該当する場合)

- 所有権

また、予約をより小さいチャンクに分割したり、既に分割あれている予約を統合したりすることもできます。 いかなる変更であっても、新しい商用のトランザクションを発生させたり、予約の終了日が変更されたりすることはありません。

購入後に直接、次の種類の変更を加えることはできません。

- 既存の予約のリージョン

- SKU

- Quantity

- Duration

ただし、変更を加える必要がある場合、予約を " *交換*" することができます。

## <a name="cancel-exchange-or-refund-reservations"></a>予約の取り消し、交換、または返金

一定の制限付きで、予約の取り消し、交換、または返金を行うことができます。 詳しくは、「 [Azure の予約のセルフサービスによる交換と払戻](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)」を参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、 [サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ

予約を管理する方法については、 [Azure の予約の管理](../cost-management-billing/reservations/manage-reserved-vm-instance.md)に関するページをご覧ください。

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](../cost-management-billing/reservations/save-compute-costs-reservations.md)

- [Azure Dedicated Host の使用](./windows/dedicated-hosts.md)

- [Dedicated Host の価格](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Azure での予約の管理](../cost-management-billing/reservations/manage-reserved-vm-instance.md)

- [予約割引の適用方法について](../cost-management-billing/manage/understand-vm-reservation-charges.md)

- [従量課金制料金のサブスクリプションの予約の使用状況について](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

- [エンタープライズ加入契約の予約使用量について](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

- [予約に含まれない Windows ソフトウェアのコスト](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

- [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](/partner-center/azure-reservations)