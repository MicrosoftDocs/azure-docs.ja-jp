---
title: Azure における VM の分離
description: Azure における VM の分離機能について学習します。
author: ayshakeen
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/18/2019
ms.author: ayshak
ms.openlocfilehash: 58b3f9c3655a2e15842f7fa28ad3e6921395beba
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831449"
---
# <a name="virtual-machine-isolation-in-azure"></a>Azure における仮想マシンの分離性

Azure Compute では、特定のハードウェアの種類に分離される、単一顧客専用の仮想マシン サイズを提供します。 分離されたサイズは、特定のハードウェア世代上に存続して動作し、そのハードウェア世代が廃止されると非推奨となります。

分離された仮想マシン サイズは、コンプライアンスや規制上の要件を満たすなどの理由で、他の顧客のワークロードからの高いレベルの分離を必要とするワークロードに最適です。  分離されたサイズを利用することで、お使いの仮想マシンがその特定のサーバー インスタンス上で実行されている唯一のマシンであることが保証されます。 


お客様は、[入れ子になった仮想マシンの Azure サポート](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)を使用して、これらの VM のリソースを再分割することも選択できます。

現在の分離された仮想マシンのプランには、以下が含まれます。
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_F72s_v2

> [!NOTE]
> 分離された VM サイズには、ハードウェアによって限定される有効期間があります。 詳細については以下を参照してください

## <a name="deprecation-of-isolated-vm-sizes"></a>分離された VM サイズの非推奨化
分離された VM サイズはハードウェアにしばられたサイズであるため、サイズが正式に非推奨となる 12 か月前に、Azure から通知が送付されます。  Azure では、次のハードウェア バージョンでも、お客様がワークロードを移動することを検討できる分離されたサイズの更新されたものが提供されます。

| サイズ | 分離の廃止日 | 
| --- | --- |
| Standard_DS15_v2<sup>1</sup> | 2020 年 5 月 15 日 |
| Standard_D15_v2<sup>1</sup>  | 2020 年 5 月 15 日 |

<sup>1</sup>  Standard_DS15_v2 と Standard_D15_v2 の分離の廃止予定の詳細については、「よく寄せられる質問」を参照してください


## <a name="faq"></a>よく寄せられる質問
### <a name="q-is-the-size-going-to-get-retired-or-only-isolation-feature-is"></a>Q:廃止される予定なのはサイズですか、それとも "分離" 機能のみですか?
**A**: 仮想マシン サイズに添字の "i" がない場合は、"分離" 機能のみが廃止されます。 分離が不要な場合、する必要のあるアクションはなく、VM は予期したとおり機能し続けます。 例として、Standard_DS15_v2、Standard_D15_v2、Standard_M128ms などが挙げられます。仮想マシン サイズに添字の "i" が含まれる場合、そのサイズは廃止されます。

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>Q:分離されていないハードウェアに VM がある場合、ダウンタイムは発生しますか?
**A**: 分離が不要な場合、必要なアクションはなく、ダウンタイムは生じません。

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>Q:分離されていない仮想マシンに移行する場合、コスト差分がありますか?
**A**: いいえ

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>Q:他の分離されたサイズはいつ廃止される予定ですか?
**A**: 分離されたサイズが正式に非推奨となる 12 か月前に通知が送付されます。

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Q:シルバーまたはゴールドの持続性層を利用している Azure Service Fabric ユーザーです。 この変更の影響はありますか?
**A**: いいえ。 Service Fabric の[持続性層](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)で提供されている保証は、この変更の後も引き続き機能します。 その他の理由で物理的なハードウェアの分離が必要な場合、上記のいずれかのアクションを実行する必要があります。 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>Q:D15_v2 や DS15_v2 の分離に関する廃止のマイルストーンはどのようになっていますか? 
**A**: 
 
| Date | アクション |
|---|---| 
| 2019 年 11 月 18 日 | D/DS15i_v2 (従量課金制、1 年間の予約インスタンス) を入手可能です | 
| 2020 年 5 月 14 日 | D/DS15i_v2 (1 年間の予約インスタンス) を購入できる最終日です | 
| 2020 年 5 月 15 日 | D/DS15_v2 分離保証が削除されます | 
| 2021 年 5 月 15 日 | D/DS15i_v2 が廃止されます (2019 年 11 月 18 日より前に D/DS15_v2 の 3 年間の予約インスタンスを購入したお客様を除くすべてのお客様)| 
| 2022 年 11 月 17 日 | 3 年間の予約インスタンスの完了時に D/DS15i_v2 が廃止されます (2019 年 11 月 18 日より前に D/DS15_v2 の 3 年間の予約インスタンスを購入したお客様の場合) |

## <a name="next-steps"></a>次のステップ

お客様は、[入れ子になった仮想マシンの Azure サポート](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)を使用して、これらの分離された仮想マシンのリソースをさらに分割することもできます。