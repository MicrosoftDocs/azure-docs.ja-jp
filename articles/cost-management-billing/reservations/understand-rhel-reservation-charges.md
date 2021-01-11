---
title: Red Hat 予約プラン割引 - Azure
description: Red Hat プランの割引が仮想マシン上の Red Hat ソフトウェアにどのように適用されるかを学習します。
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 4314a52488473bcacd4aa62a0e8f44d88d2b75f1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681602"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Red Hat Linux Enterprise ソフトウェアの予約プラン割引が Azure に適用されるしくみについて説明します

Red Hat Linux プランを購入すると、割引は、予約に一致するデプロイされた Red Hat 仮想マシン (VM) に自動的に適用されます。 Red Hat Linux プランでは、Azure VM 上で Red Hat ソフトウェアを実行するコストが対象になります。

適切な Red Hat Linux プランを購入するためには、実行する Red Hat VM と、その VM 上で実行する vCPU の数を理解する必要があります。 以降のセクションは、使用状況 CSV ファイルに基づいて購入するプランを識別するうえで役に立ちます。

## <a name="discount-applies-to-different-vm-sizes"></a>異なる VM サイズに対する割引の適用

予約 VM インスタンスと同様に、Red Hat プランの購入は、インスタンス サイズの柔軟性を提供します。 つまり、異なる vCPU 数の VM をデプロイする場合でも、割引が適用されます。 割引は、ソフトウェア プラン内の異なる VM サイズに適用されます。

割引額は、以降の表に記載されている比率によって異なります。 比率は、そのグループ内の各 VM サイズの相対的フットプリントを比較します。 比率は、VM の vCPU に依存します。 比率の値を使用すると、Red Hat Linux プランの割引が適用される VM インスタンスの数を計算できます。

たとえば、1 つから 4 つの vCPU を備える VM に対して Red Hat Linux Enterprise Server のプランを購入する場合、その予約の比率は 1 です。 次の Red Hat ソフトウェア コストが割引の対象になります。

- 1 つから 4 つの vCPU を使用する、1 つのデプロイされた VM。
- または、5 つ以上の vCPU を使用する VM の Red Hat Enterprise Linux コストの 0.46 (約 46%)。

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Azure portal マーケットプレース名:
- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 8.2

[プランの適用対象となる Red Hat Enterprise Linux のメーターをご確認ください。](https://isfratio.blob.core.windows.net/isfratio/RHELRatios.csv)

## <a name="next-steps"></a>次のステップ

予約について詳しくは、次の記事を参照してください。

- [Azure の予約とは](save-compute-costs-reservations.md)
- [Azure の予約による Red Hat ソフトウェア プランの前払い](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure の予約の管理](manage-reserved-vm-instance.md)
- [従量課金制サブスクリプションの予約使用量について](understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。
