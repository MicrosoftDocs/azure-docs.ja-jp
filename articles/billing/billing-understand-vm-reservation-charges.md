---
title: Azure Reserved VM Instances の割引について | Microsoft Docs
description: Azure Reserved VM Instance の割引が、実行中の仮想マシンにどのように適用されるのかを説明します。
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: banders
ms.openlocfilehash: c8994041d525186dd55bb4f4475f7e68036454f6
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649831"
---
# <a name="understand-how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>仮想マシンに対する Azure 予約割引の適用方法

Azure 予約仮想マシン インスタンスを購入すると、予約の属性や数量に合致する仮想マシンに対して予約割引が自動的に適用されます。 予約購入分は、ご利用の仮想マシンの計算コストに充当されます。

SQL Database の予約容量については、「[Understand Azure Reserved Instances discount](billing-understand-reservation-charges.md)」(Azure 予約インスタンスの割引について) を参照してください。

以下の表は、予約 VM インスタンスを購入した後の仮想マシンのコストを示しています。 ストレージとネットワークについては、すべての場合において通常料金が適用されます。

| 仮想マシンのタイプ  | 予約 VM インスタンスの料金 |
|-----------------------|--------------------------------------------|
|Linux VM (追加ソフトウェアなし) | 予約購入分は、ご利用の VM インフラストラクチャ コストに充当されます。|
|Linux VM + ソフトウェア料金 (例: Red Hat) | 予約購入分は、インフラストラクチャ コストに充当されます。 追加ソフトウェアについては課金の対象となります。|
|Windows VM (追加ソフトウェアなし) |予約購入分は、インフラストラクチャ コストに充当されます。 Windows ソフトウェアについては課金の対象となります。|
|Windows VM + 追加ソフトウェア (例: SQL Server) | 予約購入分は、インフラストラクチャ コストに充当されます。 Windows ソフトウェアや追加ソフトウェアについては課金の対象となります。|
|Windows VM + [Azure ハイブリッド特典](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | 予約購入分は、インフラストラクチャ コストに充当されます。 Windows ソフトウェアの料金には、Azure ハイブリッド特典が適用されます。 その他のソフトウェアについては、別途課金の対象となります。| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>非 Windows VM への予約割引の適用

 Azure 予約割引は、実行中の VM インスタンスに 1 時間単位で適用されます。 購入済みの予約は、実行中の VM によって生成された使用量と照合され、予約割引が適用されます。 実行時間が 1 時間に満たない可能性のある VM の場合、予約は、同時に実行されている VM を含め、予約を使用していない他の VM から満たされます。 1 時間の最後には、その時間の VM の予約の適用がロックされます。 VM の実行が 1 時間に満たない場合、またはその時間内の同時実行 VM が予約の時間を満たさない場合、その時間の予約は十分に活用されていません。 以下のグラフは、課金対象の VM 使用量に予約を適用した例を示しています。 この例は、単一の予約購入で、合致する VM インスタンスが 2 つあることを前提としています。

![適用済みの 1 つの予約と合致する 2 つの VM インスタンスのスクリーンショット](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. 予約のラインを超える使用量には、通常の従量課金制の料金が適用されます。 予約のラインを下回る使用量については、予約購入分として前払い済みであるため課金されません。
2. Hour 1 では、インスタンス 1 の実行時間が 0.75 時間、インスタンス 2 の実行時間が 0.5 時間です。 Hour 1 の合計使用量は 1.25 時間となります。 残りの 0.25 時間については従量課金制の料金が発生します。
3. Hour 2 と Hour 3 では、どちらのインスタンスも実行時間はそれぞれ 1 時間です。 一方のインスタンスには予約購入分が充当されますが、もう一方には従量課金制の料金が発生します。
4. Hour 4 では、インスタンス 1 の実行時間が 0.5 時間、インスタンス 2 の実行時間が 1 時間です。 インスタンス 1 は予約購入分で全額充当されます。またインスタンス 2 の 0.5 時間も充当されます。 残りの 0.5 時間については従量課金制の料金が発生します。

Azure の予約の適用状況を把握し、課金の使用状況レポートで確認する方法については、[予約の使用状況](https://go.microsoft.com/fwlink/?linkid=862757)に関するページを参照してください。

## <a name="application-of-reservation-discount-to-windows-vms"></a>Windows VM への予約割引の適用

Windows VM インスタンスの実行中は、インフラストラクチャ コストに予約が適用されて充当されます。 VM インフラストラクチャ コストに対する予約の適用は、Windows VM の場合も 非 Windows VM の場合も変わりません。 Windows ソフトウェアには別途、vCPU 単位の料金が発生します。 [予約に伴う Windows ソフトウェアのコスト](https://go.microsoft.com/fwlink/?linkid=862756)に関するページを参照してください。 Windows のライセンス コストは [Windows Server 向け Azure ハイブリッド特典](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)で充当できます。

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>さまざまなサイズに適用され、インスタンス サイズの柔軟性を提供する割引

予約 VM インスタンスを購入するときに、**[最適化の対象**: **インスタンス サイズの柔軟性]** を選択した場合、割引範囲は選択する仮想マシンのサイズによって決まります。 予約は同じサイズの系列グループの仮想マシン (VM) のサイズに適用できます。 詳細については、「[Reserved VM Instances での仮想マシン サイズの柔軟性](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)」を参照してください。

## <a name="next-steps"></a>次の手順

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](billing-save-compute-costs-reservations.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database の容量が予約された SQL Database 計算リソースの前払い](../sql-database/sql-database-reserved-capacity.md)
- [Azure の予約の管理](billing-manage-reserved-vm-instance.md)
- [従量課金制サブスクリプションの予約使用量について](billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)
- [CSP サブスクリプションの予約の使用状況について](https://docs.microsoft.com/partner-center/azure-reservations)
- [予約に含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
