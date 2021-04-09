---
title: Azure VMware Solution の予約インスタンスを使用してコストを節約する
description: Azure VMware Solution の予約インスタンスを購入する方法について説明します。
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 227ea9274f70cc58468e7e0b3ddcf8239f5b7f5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550977"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Azure VMware Solution でコストを節約する

[Azure VMware Solution ](introduction.md)の予約インスタンスにコミットすると、コストの削減になります。  予約割引は、予約スコープと属性に一致する実行中の Azure VMware Solution ホストに対して自動的に適用されます。 予約インスタンスの購入では、使用量のコンピューティング部分のみが対象となり、ソフトウェア ライセンスのコストが含まれます。 

## <a name="purchase-restriction-considerations"></a>購入の制限に関する考慮事項

予約インスタンスの利用についてはいくつかの例外があります。

-   **クラウド** - 予約は、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware)」ページの一覧にあるリージョンでのみ使用できます。

-   **クォータの不足**- 単一サブスクリプションまたは共有サブスクリプションをスコープとする予約の場合、新しい予約インスタンスで利用可能なホスト クォータがそのサブスクリプションに存在している必要があります。 [クォータを増やす要求を作成](enable-azure-vmware-solution.md)して、この問題を解決できます。

-   **プランの適格性** - Microsoft との [Azure Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md) が必要です。

-   **容量制限**- まれに、リージョンの容量が低下していることが原因で、Azure VMware Solution ホスト SKU に対する新しい予約の購入が、Azure によって制限されることがあります。

## <a name="buy-a-reservation"></a>予約の購入

Azure VMware Solution ホスト インスタンスの予約インスタンスは、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D) で購入できます。

予約の支払いは、[前払いまたは月払い](../cost-management-billing/reservations/prepare-buy-reservation.md)で行うことができます。

予約専用ホスト インスタンスの購入には、次の要件が適用されます。

-   ご自分が少なくとも 1 つの EA サブスクリプションまたは従量課金制料金のサブスクリプションの "*所有者*" ロールに属している必要があります。

-   EA サブスクリプションの場合、[EA ポータル](https://ea.azure.com/)で **[予約インスタンスを追加します]** を有効にする必要があります。 無効になっている場合、サブスクリプションを有効にするには、その EA 管理者である必要があります。

-   クラウド ソリューション プロバイダー (CSP) Azure プランのサブスクリプションの場合、パートナーは顧客に代わり Azure portal で予約インスタンスを購入する必要があります。 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>EA サブスクリプションの予約インスタンスを購入する

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. **[すべてのサービス]**  >  **[予約]** を選択します。

3. **[今すぐ購入]** を選択してから、 **[Azure VMware ソリューション]** を選択します。

4. 必須フィールドを入力します。 選択した属性が実行中の Azure VMware Solution ホストに一致する場合は、予約割引を受ける資格があります。  属性の例としては、SKU、リージョン (該当する場合)、スコープなどがあります。 予約の割引が適用される範囲は、予約のスコープによって選択されます。

   EA 契約を結んでいる場合、 **[Add more option]\(さらにオプションを追加\)** を使用してインスタンスをすばやく追加することができます。 このオプションは、他のサブスクリプションの種類では使用できません。

   | フィールド        |  説明 |
   | ------------ | ------------ |
   | サブスクリプション | 予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、予約のコストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号: MS-AZR-0017P または MS-AZR-0148P)、Microsoft 顧客契約、または従量課金制の個々のサブスクリプション (プラン番号: MS-AZR-0003P または MS-AZR-0023P)。 Azure 前払い (旧称: 年額コミットメント) の残高から料金が差し引かれるか (使用可能な場合)、超過料金として課金されます。 従量課金制料金のサブスクリプションの場合、サブスクリプションのクレジット カードまたは請求書に記載されている支払方法に料金が課金されます。 |
   | Scope        | 1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を予約のスコープにすることができます。 以下を選択した場合:<br><ul><li><b>単一のリソース グループ スコープ</b> - 選択されたリソース グループ内の一致するリソースにのみ、予約割引を適用します。</li><li><b>単一サブスクリプション スコープ</b> - 選択されたサブスクリプションの一致するリソースに予約割引を適用します。</li><li><b>共有スコープ</b> - 課金コンテキスト内にある有効なサブスクリプションの一致するリソースに予約割引を適用します。 EA の顧客の場合、課金コンテキストは課金です。 従量課金制料金の個々のサブスクリプションの場合、課金スコープはアカウント管理者によって作成されるすべての有効なサブスクリプションです。</li></ul>       |
   | リージョン       | 予約の対象となる Azure リージョン。   |
   | ホストのサイズ    | AV36    |
   | 期間         | 1 年間または 3 年間。  |
   | Quantity     | 予約内で購入するインスタンスの数。 この数量は、課金の割引を受けられる実行中の Azure VMware Solution ホストの数です。    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>CSP サブスクリプションの予約インスタンスを購入する

顧客に代わって予約インスタンスを購入する CSP は、[パートナー センターのドキュメント](/partner-center/azure-plan-manage)からの **代理管理者** (AOBO) の手順を使用する必要があります。 詳細については、[代理管理者 (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) に関する動画をご覧ください。

1. [パートナー センター](https://partner.microsoft.com)にサインインします。

2. **[CSP]** を選択して、 **[顧客]** 領域にアクセスします。

3. 顧客の詳細を展開し、 **[Microsoft Azure の管理ポータル]** を選択します。 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Microsoft パートナー センターの [顧客] 領域" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. Azure portal で、 **[すべてのサービス]**  >  **[予約]** の順に選択します。

5. **[今すぐ購入]** を選択してから、 **[Azure VMware ソリューション]** を選択します。

   :::image type="content" source="media/reserved-instances/csp-buy-ri-azure-portal.png" alt-text="Microsoft Azure portal での予約" lightbox="media/reserved-instances/csp-buy-ri-azure-portal.png":::

6. 必須フィールドを入力します。 選択した属性が実行中の Azure VMware Solution ホストに一致する場合は、予約割引を受ける資格があります。  属性の例としては、SKU、リージョン (該当する場合)、スコープなどがあります。 予約の割引が適用される範囲は、予約のスコープによって選択されます。

   | フィールド        |  説明 |
   | ------------ | ------------ |
   | サブスクリプション | 予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、予約のコストが課金されます。 サブスクリプションの種類は、有効なものとする必要があります。この場合、CSP サブスクリプションです。|
   | Scope        | 1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を予約のスコープにすることができます。 以下を選択した場合:<br><ul><li><b>単一のリソース グループ スコープ</b> - 選択されたリソース グループ内の一致するリソースにのみ、予約割引を適用します。</li><li><b>単一サブスクリプション スコープ</b> - 選択されたサブスクリプションの一致するリソースに予約割引を適用します。</li><li><b>共有スコープ</b> - 課金コンテキスト内にある有効なサブスクリプションの一致するリソースに予約割引を適用します。 EA の顧客の場合、課金コンテキストは課金です。 従量課金制料金の個々のサブスクリプションの場合、課金スコープはアカウント管理者によって作成されるすべての有効なサブスクリプションです。</li></ul>       |
   | リージョン       | 予約の対象となる Azure リージョン。   |
   | ホストのサイズ    | AV36    |
   | 期間         | 1 年間または 3 年間。  |
   | Quantity     | 予約内で購入するインスタンスの数。 この数量は、課金の割引を受けられる実行中の Azure VMware Solution ホストの数です。     |

顧客に代わり購入した予約を表示する方法の詳細については、「[クラウド ソリューション プロバイダー (CSP) として Azure の予約を表示する](../cost-management-billing/reservations/how-to-view-csp-reservations.md)」の記事をご覧ください。

## <a name="usage-data-and-reservation-usage"></a>使用状況データと予約の使用量

予約割引が適用される使用量には、0 の実効価格があります。 予約ごとに、予約割引を受けた Azure VMware Solution インスタンスを確認できます。

使用状況データでの予約割引の表示方法ついては詳しくは、次をご覧ください。

- EA のお客様は、「[エンタープライズ加入契約に適用される Azure の予約の使用状況について](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)」を参照してください。
- 個別サブスクリプションの場合は、「[従量課金制サブスクリプションに適用される Azure の予約の使用状況について](../cost-management-billing/reservations/understand-reserved-instance-usage.md)」を参照してください。

## <a name="change-a-reservation-after-purchase"></a>購入後に予約を変更する

購入後に予約に対して次の変更を加えることができます。

-   予約スコープの更新

-   インスタンス サイズの柔軟性 (該当する場合)

-   所有権

また、予約をより小さいチャンクに分割したり、予約を統合したりすることもできます。 いかなる変更であっても、新しい商用のトランザクションを発生させたり、予約の終了日が変更されたりすることはありません。

CSP マネージド予約の詳細については、「[パートナー センター、Azure portal、および API を使用して Microsoft Azure の予約を顧客に販売する](/partner-center/azure-reservations)」を参照してください。



>[!NOTE]
>予約を購入したら、次の種類の変更を直接行うことはできません。
>
> - 既存の予約のリージョン
> - SKU
> - Quantity
> - Duration
>
>ただし、変更を加える必要がある場合、予約を *交換* することができます。

## <a name="cancel-exchange-or-refund-reservations"></a>予約の取り消し、交換、または返金

一定の制限付きで、予約の取り消し、交換、または返金を行うことができます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)」を参照してください。

CSP は、一定の制限付きで、顧客に代わって購入する予約の取り消し、交換、または返金を行うことができます。 詳細については、「[顧客の Microsoft Azure の予約を管理、取り消し、交換、または返金する](/partner-center/azure-reservations-manage)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure VMware Solution の予約インスタンスの購入について確認したので、次の事項の学習に進むことができます。

- [Azure VMware Solution 評価の作成](../migrate/how-to-create-azure-vmware-solution-assessment.md)。
- [Azure VMware Solution 用の DHCP の管理](manage-dhcp.md)。
- [Azure VMware Solution VM のライフサイクル管理](lifecycle-management-of-azure-vmware-solution-vms.md)。
 