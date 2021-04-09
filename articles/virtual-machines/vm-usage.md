---
title: Azure 仮想マシンの使用量について
description: 仮想マシンの使用量の詳細について
services: virtual-machines
documentationcenter: ''
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 07/28/2020
ms.openlocfilehash: ba973bd5609dacf05eca842025d4e828d8a9f841
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550949"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Azure 仮想マシンの使用量について
Azure の使用量のデータを分析すると、消費額に関して説得力のある裏付けを得ることができ、より効果的なコスト管理や組織全体への割り当てを実現できます。 このドキュメントでは、Azure Compute の消費額について詳しく見ていきます。 一般的な Azure 使用量の詳細については、[請求書の見方](../cost-management-billing/understand/review-individual-bill.md)に関するページをご覧ください。

## <a name="download-your-usage-details"></a>使用量の詳細のダウンロード
最初に、[使用量の詳細をダウンロード](../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal)します。 次の表は、Azure Resource Manager を使用してデプロイされた Virtual Machines の使用量の定義とサンプル値を示しています。 このドキュメントには、クラシック モデルを使用してデプロイされた VM に関する詳細情報は含まれません。


| フィールド | 意味 | サンプル値 | 
|---|---|---|
| 使用日 | リソースが使用された日付 | `11/23/2017` |
| 測定 ID | この使用量を占めるサービスのうち、最も上位のものを示します| `Virtual Machines`|
| 測定サブカテゴリ | 課金測定の識別子です。 <br><br> コンピューティング時間の使用量は、VM サイズ + OS (Windows、Windows 以外) + リージョンごとに測定されます。 <br><br> Premium ソフトウェアの使用量は、ソフトウェアの種類ごとに測定されます。 Premium ソフトウェア イメージのほとんどについて、コア サイズごとに異なる測定があります。 詳細については、[コンピューティング価格に関するページ](https://azure.microsoft.com/pricing/details/virtual-machines/)をご覧ください</li></ul>| `2005544f-659d-49c9-9094-8e0aea1be3a5`|
| 測定名| Azure の各サービスに固有です。 コンピューティングについては、常に "コンピューティング時間" です。| `Compute Hours`|
| 測定リージョン| データセンターの場所に基づいて価格が設定されるサービスについて、データセンターの場所を示します。|  `JA East`|
| ユニット| サービスが課金される単位を特定します。 コンピューティング リソースは時間単位で課金されます。| `Hours`|
| 使用量| その日に消費されたリソースの量。 コンピューティングについては、VM が実行されていた特定の時間に対して分単位で課金されます (最大 6 桁の精度)。| `1, 0.5`|
| リソースの場所  | リソースが実行されているデータ センターを特定します。| `JA East`|
| 使用サービス | 使用した Azure プラットフォーム サービス。| `Microsoft.Compute`|
| リソース グループ | デプロイされたリソースが実行されるリソース グループ。 詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)」を参照してください。|`MyRG`|
| インスタンス ID | リソースの識別子。 識別子には、リソースの作成時に指定した名前が含まれています。 VM については、インスタンス ID には、SubscriptionId、ResourceGroupName、および VMName (または、スケール セットの使用量についてはスケール セット名) が含まれます。| `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1`<br><br>or<br><br>`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1`|
| Tags| この列には、ユーザーが指定したリソース タグが含まれます。 タグは、課金記録のグループ化に使用できます。 [CLI](./tag-cli.md) または [PowerShell](./tag-portal.md) を使用して仮想マシンにタグを付ける方法について学習してください。これは、Resource Manager VM でのみ使用できます。| `{"myDepartment":"RD","myUser":"myName"}`|
| 追加情報 | サービス固有のメタデータ。 VM については、追加情報フィールドに次のデータが設定されます。 <br><br> イメージの種類: 実行した特定のイメージ。 以下の「イメージの種類」で、サポートされている文字列の完全な一覧を確認してください。<br><br> サービスの種類: デプロイしたサイズ。<br><br> VMName: VM の名前。 このフィールドは、スケール セット VM にのみ設定されます。 スケール セット VM の VM 名が必要な場合は、上記のインスタンス ID 文字列で確認できます。<br><br> UsageType: これが表す使用量の種類を指定します。<br><br> ComputeHR は、Standard_D1_v2 など、基になる VM のコンピューティング時間の使用量です。<br><br> ComputeHR_SW は、VM が Microsoft R Server などの Premium ソフトウェアを使用している場合の、Premium ソフトウェア料金です。 | Virtual Machines<br>`{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}`<br><br>Virtual Machine Scale Sets<br> `{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}`<br><br>Premium ソフトウェア<br> `{"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"}` |

## <a name="image-type"></a>イメージの種類
Azure ギャラリーの一部のイメージについては、イメージの種類が [追加情報] フィールドに設定されます。 これにより、ユーザーが仮想マシンにデプロイしたものを理解し、追跡できます。 デプロイしたイメージに基づいて、このフィールドに設定される値は次のとおりです。
- BitRock 
- Canonical FreeBSD 
- Open Logic 
- Oracle 
- SLES for SAP 
- Windows Server 2012 R2 プレビューの SQL Server 14 プレビュー 
- SUSE
- SUSE Premium
- StorSimple Cloud Appliance 
- Red Hat
- Red Hat for SAP Business Applications     
- Red Hat for SAP HANA 
- Windows クライアント BYOL 
- Windows Server BYOL 
- Windows Server プレビュー 

## <a name="service-type"></a>サービスの種類
[追加情報] フィールドのサービスの種類フィールドは、デプロイした VM サイズと正確に対応します。 Premium Storage VM (SSD ベース) と Premium Storage 以外の VM (HDD ベース) の価格は同じです。 Standard\_DS2\_v2 などの SSD ベースのサイズをデプロイすると、[測定サブカテゴリ] 列に SSD 以外のサイズ (`Standard\_D2\_v2 VM`) が表示され、[追加情報] フィールドに SSD サイズ (`Standard\_DS2\_v2`) が表示されます。

## <a name="region-names"></a>リージョン名
使用量の詳細の [リソースの場所] フィールドに設定されているリージョン名は、Azure Resource Manager で使用されているリージョン名とは異なります。 リージョンの値のマッピングを次に示します。

| **Resource Manager のリージョン名** | **使用量の詳細のリージョン名** |
|---|---|
| australiaeast |オーストラリア東部|
| australiasoutheast | オーストラリア南東部|
| brazilsouth | ブラジル南部|
| CanadaCentral | カナダ中部|
| CanadaEast | カナダ東部|
| CentralIndia | インド中部|
| centralus | 米国中部|
| chinaeast | 中国東部|
| chinanorth | 中国北部|
| eastasia | 東アジア|
| eastus | 米国東部|
| eastus2 | 米国東部 2|
| GermanyCentral | ドイツ中部|
| GermanyNortheast | ドイツ北東部|
| japaneast | 東日本|
| japanwest | 西日本|
| KoreaCentral | 韓国中部|
| KoreaSouth | 韓国南部|
| northcentralus | 米国中北部|
| northeurope | 北ヨーロッパ|
| southcentralus | 米国中南部|
| southeastasia | 東南アジア|
| SouthIndia | インド南部|
| UKNorth | 英国北部|
| uksouth | 英国南部|
| UKSouth2 | 英国南部 2|
| ukwest | 英国西部|
| USDoDCentral | US DoD Central|
| USDoDEast | US DoD East|
| USGovArizona | USGov アリゾナ|
| usgoviowa | USGov アイオワ州|
| USGovTexas | USGov テキサス|
| usgovvirginia | USGov バージニア州|
| westcentralus | 米国中西部|
| westeurope | 西ヨーロッパ|
| WestIndia | インド西部|
| westus | 米国西部|
| westus2 | 米国西部 2|


## <a name="virtual-machine-usage-faq"></a>仮想マシンの使用量に関する FAQ
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>VM をデプロイする場合、どのようなリソースに課金されますか。    
VM では、VM 自体、VM で実行されている Premium ソフトウェア、VM に関連付けられているストレージ アカウント/管理ディスク、および VM からのネットワーク帯域幅転送に対してコストがかかります。
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>VM が Azure ハイブリッド特典を使用しているかどうかは、使用量 CSV でどのように判断できますか。
[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を使用してデプロイする場合は、クラウドにご自身のライセンスを持ち込むため、Windows 以外の VM に料金が請求されます。 請求書で、[ImageType] 列に "Windows\_Server BYOL" または "Windows\_Client BYOL" のいずれかが示されているため、どの Resource Manager VM が Azure ハイブリッド特典を実行しているかを特定できます。
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Basic VM とStandard VM は、使用量 CSV ではどのように区別されますか。
Basic VM と Standard A シリーズ VM の両方が提供されます。 Basic VM をデプロイすると、測定サブカテゴリに "Basic" という文字列が示されます。 Standard A シリーズ VM をデプロイすると、Standard が既定であるため、VM サイズは "A1 VM" として表示されます。 Basic と Standard の違いの詳細については、[価格ページ](https://azure.microsoft.com/pricing/details/virtual-machines/)をご覧ください。
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>ExtraSmall、Small、Medium、Large、および ExtraLarge サイズとは何ですか。
ExtraSmall ～ ExtraLarge は、Standard\_A0 ～ Standard\_A4 のレガシ名です。 クラシック VM 使用量レコードでこのようなサイズをデプロイした場合は、この表記が表示される場合があります。
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>"測定範囲" と "リソースの場所" の違いは何ですか。
測定範囲は、測定に関連付けられています。 すべてのリージョンに対して 1 つの価格を使用している Azure サービスの場合、[測定範囲] フィールドが空白になることがあります。 ただし、VM では、Virtual Machines のリージョンごとに専用の価格が設定されているため、このフィールドには値が入力されます。 同様に、Virtual Machines のリソースの場所は、VM がデプロイされている場所です。 両方のフィールドの Azure リージョンは同じですが、リージョン名の文字列表記法が異なる場合があります。
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>[詳細情報] フィールドの ImageType 値は、なぜ空白なのですか。
[ImageType] フィールドは、イメージのサブセットに対してのみ設定されます。 上記のいずれかのイメージをデプロイしなかった場合、ImageType は空白になります。
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>[詳細情報] の VMName は、なぜ空白なのですか。
VMName は、スケールセットの VM に対してのみ、[追加情報] フィールドに設定されます。 [InstanceID] フィールドには、スケール セット以外の VM の VM 名が含まれています。
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>[追加情報] の [UsageType] フィールドの ComputeHR とは何ですか。
ComputeHR は、基になるインフラストラクチャ コストの使用状況イベントを表すコンピューティング時間を意味します。 UsageType が ComputeHR\_SW の場合、使用状況イベントは、VM の Premium ソフトウェア料金を表します。
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Premium ソフトウェアに料金が発生してるかどうかを確認するには、どうすればよいですか。
ニーズに最適な VM イメージを確認するときは、必ず [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute) をよく調べてください。 イメージには、ソフトウェア プラン料金があります。 "Free" と表示されている場合、ソフトウェアに対して追加コストは発生しません。 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>使用サービスにおける Microsoft.ClassicCompute と Microsoft.Compute の違いは何ですか。
Microsoft.ClassicCompute は、Azure Service Manager を使ってデプロイしたクラシック リソースを表します。 Resource Manager でデプロイする場合、使用サービスには Microsoft.Compute が設定されます。 詳細については、[Azure のデプロイメント モデル](../azure-resource-manager/management/deployment-models.md)に関するページをご覧ください。
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>仮想マシンの使用量に対して、[InstanceID] フィールドが空白になるのはなぜですか。
クラシック デプロイ モデルでデプロイする場合、InstanceID 文字列は使用できません。
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>VM のタグが使用量の詳細に表示されないのはなぜですか。
タグは、Resource Manager VM の使用量 CSV にのみ表示されます。 クラシック リソース タグは、使用量の詳細では使用できません。
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>どのような場合に使用量が 1 日につき 24 時間を超えますか。
クラシック モデルでは、リソースに対する請求は、クラウド サービス レベルで集計されます。 同じ請求測定が使用されているクラウド サービスに、複数の VM がある場合、使用量は一緒に集計されます。 Resource Manager でデプロイされた VM は、VM レベルで請求されるため、この集計は適用されません。
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>DS/FS/GS/LS サイズに対する価格が、価格ページにないのはなぜですか。
Premium Storage 対応の VM の料金は、Premium Storage 非対応の VM と同じです。 異なるのはストレージ コストのみです。 詳細については、[ストレージの価格ページ](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/)をご覧ください。

## <a name="next-steps"></a>次のステップ
使用量の詳細については、「[Understand your bill for Microsoft Azure (Microsoft Azure の課金について)](../cost-management-billing/understand/review-individual-bill.md)」を参照してください。