---
title: Azure EA VM 予約インスタンス
description: この記事では、VM 予約インスタンスに対する Azure 予約が、エンタープライズ登録にかかる金額の節約にどのように役立つかについて説明します。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 3c3d95feed4f7cf47448c4fe62d8368fa5498dd7
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841322"
---
# <a name="azure-ea-vm-reserved-instances"></a>Azure EA VM 予約インスタンス

この記事では、VM 予約インスタンスに対する Azure 予約が、エンタープライズ登録にかかる金額の節約にどのように役立つかについて説明します。 予約の詳細については、「[Azure の予約とは](billing-save-compute-costs-reservations.md)」を参照してください。

## <a name="reservation-exchanges-and-refunds"></a>予約の交換と返金

変化するニーズを満たすために、予約を同じ種類の別の予約と交換できます。 また、予約が不要になった場合は、最大で年間 50,000 米国ドルまでの払い戻しができます。 予約の交換または払い戻しを行うには、Azure portal を使用できます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](billing-azure-reservations-self-service-exchange-and-refund.md)」を参照してください。

## <a name="reservation-costs-and-usage"></a>予約のコストと使用状況

エンタープライズ契約のお客様は、Azure portal および REST API でコストと使用状況データを確認できます。 予約のコストと使用状況については、次のことができます。

- 予約購入データを取得する。
- どのサブスクリプション、リソース グループ、またはリソースによって予約が使用されたかを把握する。
- 予約の使用をチャージバックする。
- 予約による節約額を計算する。
- 使用率の低いデータの予約を取得する。
- 予約コストを償却する。

予約のコストと使用状況の詳細については、「[Enterprise Agreement の予約のコストと使用状況を取得する](billing-understand-reserved-instance-usage-ea.md)」を参照してください。

価格の詳細については、「[Linux Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)」または「[Windows Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)」を参照してください。

## <a name="reserved-instances-api-support"></a>予約インスタンス API サポート

Azure API を使用して、Azure サービスまたはソフトウェアの予約に関する組織の情報をプログラムで取得できます。 詳細については、「[APIs for Azure reservation automation](billing-reservation-apis.md)」(Azure の予約自動化の API) を参照してください。

## <a name="azure-reserved-virtual-machine-instances"></a>Azure 予約仮想マシン インスタンス

予約インスタンスにより、すべての VM で仮想マシンのコストを従量課金制の料金に比べて最大 72% 削減できます。また、予約インスタンスと Azure ハイブリッド特典を組み合わせると、最大 82% 節約できます。 1 年または 3 年分の前払いにより、ワークロード、予算、予測の優先順位をより適正に設定できます。 ビジネス ニーズの変化に応じて予約を交換またはキャンセルすることができます。

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>予約仮想マシン インスタンスを購入する方法

Azure 予約仮想マシン インスタンスを購入するには、エンタープライズ Azure 登録管理者が [Azure EA Portal](https://ea.azure.com/) の _[加入契約]_ タブにある _[加入契約の詳細]_ セクションで _[予約インスタンス]_ 購入オプションを有効にする必要があります。

EA 加入契約を有効にして、予約インスタンスを追加すると、EA 加入契約に関連付けられたアクティブなサブスクリプションを持つすべてのアカウント所有者は、[Azure portal](https://aka.ms/reservations) で予約仮想マシン インスタンスを購入できます。 詳細については、[予約仮想マシン インスタンスによる仮想マシンの使用料の前払いとコスト削減](https://go.microsoft.com/fwlink/?linkid=861721)に関するページを参照してください。

### <a name="how-to-view-reserved-instance-purchase-details"></a>予約インスタンスの購入の詳細を表示する方法

[Azure portal](https://aka.ms/reservations) の左側にある _[加入契約]_ メニューを使用するか、または [Azure EA Portal](https://ea.azure.com/) を使用して、予約インスタンスの購入の詳細を表示することができます。 左側のメニューで **[レポート]** を選択し、 _[使用状況の概要]_ タブで _[Charges by Services]\(サービス別料金\)_ セクションまで下にスクロールします。このセクションの一番下までスクロールすると、一覧の最後に、予約インスタンスの購入と使用状況が表示されます。予約インスタンスは、サービス名の横に '1 year' または '3 years' が示されます。たとえば、Standard_DS1_v2 eastus 1 year または Standard_D2s_v3 eastus2 3 years と表示されます。

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>予約インスタンスと関連付けられたサブスクリプションを変更するか、予約インスタンス特典を同じアカウントのサブスクリプションに譲渡するにはどうすればよいですか?

どのような場合でも、予約インスタンス特典を受けることができるサブスクリプションは 1 つのみです。 予約インスタンス特典を受けるサブスクリプションを変更するには、次の手順を行います。

- [Azure portal](https://aka.ms/reservations)にログインします。
- 同じアカウントの別のサブスクリプションを関連付けて、提供されるサブスクリプションのスコープを更新します。

### <a name="how-to-view-reserved-instance-usage-details"></a>予約インスタンスの使用量の詳細を表示する方法

予約インスタンスの使用量の詳細は、[Azure portal](https://aka.ms/reservations) に表示することができます。または、[Azure EA portal](https://ea.azure.com/) で、 _[レポート]_  >  _[使用状況の概要]_  > 、 _[Charges by Services]\(サービス別料金\)_ の順に移動して表示することもできます (課金情報を表示するアクセス権限を持つ EA のお客様の場合)。 予約インスタンスは、'Reservation' を含むサービス名で識別できます。たとえば、Reservation-Base VM または Virtual Machines Reservation-Windows Svr (1 Core) と表示されます。

使用状況の詳細と詳細レポートのダウンロード用 CSV には、予約インスタンスの使用量に関する追加情報が含まれます。 _[追加情報]_ フィールドは、予約インスタンスの使用量を確認するのに役立ちます。

Azure ハイブリッド特典を使用せずに Azure 予約 VM インスタンスを購入した場合、予約インスタンスによって 2 つのメーター (ハードウェアとソフトウェア) が生成されます。 Azure ハイブリッド特典を使用して予約インスタンスを購入した場合、予約インスタンスの使用量の詳細にソフトウェア メーターは表示されません。

### <a name="reserved-instance-billing"></a>予約インスタンスの課金

エンタープライズ カスタマーの場合は、年額コミットメントを利用して Azure 予約 VM インスタンスを購入します。 加入契約の年額コミットメントに、予約インスタンスを購入できる十分な残高が残っている場合、予約インスタンスの金額は年額コミットメントの残高から差し引かれ、購入分の請求書は送付されません。

Azure EA のお客様がお持ちの年額コミットメントが全額使用されている場合でも、予約インスタンスを購入することができます。この購入に対しては、次回の超過分請求で請求されます。 予約インスタンスの超過分がある場合は、通常の超過分請求に含まれます。

### <a name="reserved-instance-expiration"></a>予約インスタンスの有効期限

予約の 30 日前および有効期限の 30 日前にメールで通知が送付されます。 予約の期限が切れても、デプロイされている VM は稼働し続け、従量課金制で課金されます。 詳細については、[予約仮想マシン インスタンス プラン](https://azure.microsoft.com/pricing/reserved-vm-instances/)に関するページを参照してください。

## <a name="next-steps"></a>次の手順
- Azure の予約の詳細については、「[Azure の予約とは](billing-save-compute-costs-reservations.md)」を参照してください。
- エンタープライズ予約のコストと使用状況の詳細については、「[Enterprise Agreement の予約のコストと使用状況を取得する](billing-understand-reserved-instance-usage-ea.md)」を参照してください。
- 価格の詳細については、「[Linux Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)」または「[Windows Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)」を参照してください。
