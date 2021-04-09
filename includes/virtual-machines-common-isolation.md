---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: rishabv90
ms.service: virtual-machines
ms.topic: include
ms.date: 11/05/2020
ms.author: risverma
ms.custom: include file
ms.openlocfilehash: fc61c111291f9862f71f9a81828fa0fa828ab2ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102511049"
---
Azure Compute では、特定のハードウェアの種類に分離される、単一顧客専用の仮想マシン サイズを提供します。 分離されたサイズは、特定のハードウェア世代上に存続して動作し、そのハードウェア世代が廃止されると非推奨となります。

分離された仮想マシン サイズは、コンプライアンスや規制上の要件を満たすなどの理由で、他の顧客のワークロードからの高いレベルの分離を必要とするワークロードに最適です。  分離されたサイズを利用することで、お使いの仮想マシンがその特定のサーバー インスタンス上で実行されている唯一のマシンであることが保証されます。 


お客様は、[入れ子になった仮想マシンの Azure サポート](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)を使用して、これらの VM のリソースを再分割することも選択できます。

現在の分離された仮想マシンのプランには、以下が含まれます。
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_F72s_v2
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5


> [!NOTE]
> 分離された VM サイズには、ハードウェアによって限定される有効期間があります。 詳細については以下を参照してください

## <a name="deprecation-of-isolated-vm-sizes"></a>分離された VM サイズの非推奨化

分離された VM サイズには、ハードウェアによって限定される有効期間があります。 そのサイズの正式な廃止日の 12 か月前に Azure からリマインダーを出し、お客様の検討用に、更新された分離オファリングを提供します。

| サイズ | 分離の廃止日 | 
| --- | --- |
| Standard_DS15_v2 | 2021 年 5 月 15 日 |
| Standard_D15_v2  | 2021 年 5 月 15 日 |
| Standard_G5  | 2022 年 2 月 15 日 |
| Standard_GS5  | 2022 年 2 月 15 日 |
| Standard_E64i_v3  | 2022 年 2 月 15 日 |
| Standard_E64is_v3  | 2022 年 2 月 15 日 |


## <a name="faq"></a>よく寄せられる質問
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>Q:廃止される予定なのはサイズですか、それともその "分離" 機能のみですか?
**A**: 現時点では、廃止されるのは VM サイズの分離機能のみです。 非推奨の分離サイズは、分離されていない状態で引き続き存在します。 分離が不要な場合、する必要のあるアクションはなく、VM は予期したとおり機能し続けます。

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>Q:分離されていないハードウェアに VM がある場合、ダウンタイムは発生しますか?
**A**: 分離が不要な場合、必要なアクションはなく、ダウンタイムは生じません。 分離が必要な場合とは対照的に、発表には推奨される代替サイズが含まれます。 代替サイズを選択すると、お客様による VM のサイズの変更が必要になります。  

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>Q:分離されていない仮想マシンに移行する場合、コスト差分がありますか?
**A**: いいえ

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>Q:他の分離されたサイズはいつ廃止される予定ですか?
**A**: 分離されたサイズが正式に非推奨となる 12 か月前に通知が送付されます。 最新の発表には、Standard_G5、Standard_GS5、Standard_E64i_v3、および Standard_E64i_v3 の分離機能の提供が含まれています。  

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Q:シルバーまたはゴールドの持続性層を利用している Azure Service Fabric ユーザーです。 この変更の影響はありますか?
**A**: いいえ。 Service Fabric の[持続性層](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)で提供されている保証は、この変更の後も引き続き機能します。 その他の理由で物理的なハードウェアの分離が必要な場合、上記のいずれかのアクションを実行する必要があります。 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>Q:D15_v2 や DS15_v2 の分離に関する廃止のマイルストーンはどのようになっていますか? 
**A**: 
 
| Date | アクション |
|---|---| 
| 2020 年 5 月 15 日<sup>1</sup> | D/DS15_v2 分離の提供終了に関するお知らせ| 
| 2021 年 5 月 15 日 | D/DS15_v2 分離保証が削除されます| 

<sup>1</sup> これらのサイズを使用している既存のお客様には、次の手順に関する詳細な手順が記載されたお知らせのメールが届きます。  

### <a name="q-what-are-the-milestones-for-g5-gs5-e64i_v3-and-e64is_v3-isolation-retirement"></a>Q: G5、Gs5、E64i_v3 および E64is_v3 分離の廃止のマイルストーンはどのようなものですか? 
**A**: 
 
| Date | アクション |
|---|---|
| 2021 年 2 月 15 日<sup>1</sup> | G5/GS5/E64i_v3/E64is_v3 分離の提供終了に関するお知らせ |
| 2022 年 2 月 15 日 | G5/GS5/E64i_v3/E64is_v3 分離保証が削除されます |

<sup>1</sup> これらのサイズを使用している既存のお客様には、次の手順に関する詳細な手順が記載されたお知らせのメールが届きます。  

## <a name="next-steps"></a>次のステップ

お客様は、[入れ子になった仮想マシンの Azure サポート](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)を使用して、これらの分離された仮想マシンのリソースをさらに分割することもできます。
