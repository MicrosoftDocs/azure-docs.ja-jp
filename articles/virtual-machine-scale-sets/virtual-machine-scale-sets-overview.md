---
title: "Azure 仮想マシン スケール セットの概要 | Microsoft Docs"
description: "Azure 仮想マシン スケール セットについて説明します"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: bdd0fd0d1919f61fe586f495adadaf4eabde2dae
ms.contentlocale: ja-jp
ms.lasthandoff: 09/02/2017

---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>Azure Virtual Machine Scale Sets とは
Virtual Machine Scale Sets は、同一の VM のセットをデプロイおよび管理するための Azure コンピューティング リソースです。 すべての VM が同じ構成になっており、VM を事前にプロビジョニングする必要がない、真の自動スケールをサポートするように設計されています。 そのため、ビッグ コンピューティング、ビッグ データ、コンテナー化されたワークロードを対象にした大規模サービスを簡単に構築できます。

コンピューティング リソースをスケール アウトしたりスケール インしたりする必要のあるアプリケーションでは、複数の障害ドメインと更新ドメインに対してスケール操作が暗黙的にバランシングされます。 スケール セットの概要についてさらに確認したい場合は、[Azure ブログでの発表](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/)を参照してください。

スケール セットの詳細については、次の動画をご覧ください。

* [Mark Russinovich が語る Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Virtual Machine Scale Sets を Guy Bowerman が解説](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-scale-sets"></a>Scale Sets の作成と管理
[Azure Portal](https://portal.azure.com) で Scale Sets を作成するには、**[新規]** を選択して、検索バーに「**scale**」と入力します。 検索結果に、**仮想マシン スケール セット**が表示されます。 そこから、必要なフィールドを入力し、Scale Sets をカスタマイズしてデプロイします。 また、ポータルには、CPU 使用率に基づいて基本的な自動スケール規則を設定するオプションも用意されています。

スケール セットの定義とデプロイは、個々の Azure Resource Manager VM と同様に JSON テンプレートと [REST API](https://msdn.microsoft.com/library/mt589023.aspx) を使用して行えます。 そのため、Azure Resource Manager の標準的なデプロイ方法を利用することができます。 テンプレートの詳細については、「 [Azure Resource Manager テンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」に関する記事をご覧ください。

仮想マシン スケール セットの一連のサンプル テンプレートは、[Azure クイックスタート テンプレートの GitHub リポジトリ](https://github.com/Azure/azure-quickstart-templates)から入手できます  (タイトルに **vmss** が付いているテンプレートを探してください)。

クイックスタートのサンプル テンプレートについては、各テンプレートの Readme にある "Azure へのデプロイ" ボタンが、ポータルのデプロイ機能にリンクされています。 Scale Sets をデプロイするには、ボタンをクリックし、ポータルで必要なすべてのパラメーターを指定します。 

## <a name="scaling-a-scale-set-out-and-in"></a>Scale Sets のスケール アウトとスケール イン
Azure Portal で Scale Sets の容量を変更するには、**[設定]** で **[スケーリング]** セクションをクリックします。 

コマンド ラインでスケール セットの容量を変更するには、[Azure CLI](https://github.com/Azure/azure-cli) で **scale** コマンドを使用します。 たとえば、Scale Sets の容量を VM 10 個に設定するには、次のコマンドを実行します。

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

PowerShell を使用して Scale Sets 内の VM の数を設定するには、**Update-AzureRmVmss** コマンドを使用します。

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

Azure Resource Manager テンプレートを使用してスケール セット内の仮想マシンの数を増やしたり減らしたりするには、**capacity** プロパティを変更し、テンプレートを再デプロイします。 このように操作は単純なので、Azure 自動スケールを使用して複数のスケール セットを簡単に統合できます。Azure 自動スケールでサポートされていないカスタム スケール イベントを定義する必要がある場合は、独自のカスタム スケール層を簡単に記述できます。 

容量を変更するために Azure Resource Manager テンプレートを再デプロイする場合は、**SKU** プロパティ パケットと更新された容量のみが含まれた、非常に小さいテンプレートを定義できます。 [こちら](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)でサンプルをご覧ください。

## <a name="autoscale"></a>自動スケール

Azure Portal で Scale Sets を作成する場合、オプションで自動スケール設定を構成できます。 これを構成すると、平均 CPU 使用率に基づいて VM の数を増減できます。 

[Azure のクイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates)にある Scale Sets テンプレートの多くでは、自動スケール設定が定義されています。 既存の Scale Sets に自動スケール設定を追加することもできます。 たとえば、次の Azure PowerShell スクリプトを使用すると、CPU ベースの自動スケールがスケール セットに追加されます。

```PowerShell

$subid = "yoursubscriptionid"
$rgname = "yourresourcegroup"
$vmssname = "yourscalesetname"
$location = "yourlocation" # e.g. southcentralus

$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionValue 1
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator LessThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Decrease -ScaleActionValue 1
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "autoprofile1"
Add-AzureRmAutoscaleSetting -Location $location -Name "autosetting1" -ResourceGroup $rgname -TargetResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -AutoscaleProfiles $profile1
```

「[Azure Monitor のサポートされるメトリック](../monitoring-and-diagnostics/monitoring-supported-metrics.md)」の「Microsoft.Compute/virtualMachineScaleSets」には、スケールのための有効なメトリックの一覧があります。 スケジュールに基づく自動スケールや、webhook を使用したアラート システムとの統合など、高度な自動スケールのオプションも用意されています。

## <a name="monitoring-your-scale-set"></a>Scale Sets の監視
[Azure Portal](https://portal.azure.com) には、スケール セットがそのプロパティと共に一覧表示されます。 Azure Portal では管理操作もサポートされており、 スケール セットとスケール セット内の個々の VM の両方に対して、管理操作を実行できます。 また、カスタマイズ可能なリソース使用量グラフも表示されます。 

Azure リソースの基盤となっている JSON 定義を表示または編集する必要がある場合は、[Azure リソース エクスプローラー](https://resources.azure.com)を使用することもできます。 Scale Sets は、Microsoft.Compute Azure リソース プロバイダーの下にあるリソースなので、このサイトから以下のリンクを展開すると表示できます。 このサイトから次のリンクを展開すると表示できます。

**subscriptions** > **自分のサブスクリプション** > **resourceGroups** > **providers** > **Microsoft.Compute** > **virtualMachineScaleSets** > **自分の Scale Sets** > その他

## <a name="scale-set-scenarios"></a>Scale Sets のシナリオ
このセクションでは、標準的な Scale Sets のシナリオをいくつか紹介します。 一部の高レベルの Azure サービス (Batch、Service Fabric、Container Service など) で、これらのシナリオが使用されます。

* **RDP または SSH でスケール セット インスタンスに接続する**: スケール セットは仮想ネットワーク内に作成され、既定ではスケール セット内の個々の VM にパブリック IP アドレスが割り当てられていません。 このポリシーによって、コンピューティング グリッド内のすべてのノードに個別にパブリック IP アドレスを割り当てる費用や管理オーバーヘッドを回避できます。 スケール セット VM への直接外部接続がどうしても必要な場合は、新しい VM に自動的にパブリック IP アドレスを割り当てるようにスケール セットを構成することができます。 VM には、ロード バランサーやスタンドアロンの仮想マシンなど、パブリック IP アドレスを割り当てることができる、仮想ネットワーク内の他のリソースから接続することもできます。 
* **NAT 規則を使用して VM に接続する**: パブリック IP アドレスを作成し、それをロード バランサーに割り当てて、受信 NAT プールを定義できます。 これらの操作を行うと、IP アドレス上のポートがスケール セット内の VM 上のポートにマッピングされます。 For example:
  
  | ソース | ソース ポート | 宛先 | 宛先ポート |
  | --- | --- | --- | --- |
  |  パブリック IP |ポート 50000 |vmss\_0 |ポート 22 |
  |  パブリック IP |ポート 50001 |vmss\_1 |ポート 22 |
  |  パブリック IP |ポート 50002 |vmss\_2 |ポート 22 |
  
   [こちらのサンプル](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)では、1 つのパブリック IP アドレスを使用してスケール セット内のすべての VM に対する SSH 接続を許可するように NAT 規則が定義されています。
  
   [こちらのサンプル](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)では、RDP と Windows を使用して同様の NAT 規則が定義されています。
* **"踏み台" を使用して VM に接続する**: スケール セットとスタンドアロン VM を同じ VNET 内に作成する場合、スタンドアロン VM と Scale Sets VM は、VNET/サブネットによって定義された内部 IP アドレスを使用して、互いに接続することができます。 パブリック IP アドレスを作成してスタンドアロン VM に割り当てたら、RDP または SSH を使用してスタンドアロン VM に接続できます。 その後、そのコンピューターからスケール セット インスタンスに接続できます。 これでお気付きかもしれませんが、単純なスケール セットは、本質的に、既定の構成のパブリック IP アドレスが割り当てられた単純なスタンドアロン VM よりも安全です。
  
   たとえば、[こちらのテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)では、単純なスケール セットがスタンドアロン VM と共にデプロイされます。 
* **スケール セット インスタンスに対して負荷分散する**: ラウンド ロビン方式を使用して VM のコンピューティング クラスターに作業を割り当てる場合は、それに応じたレイヤー 4 の負荷分散規則で Azure ロード バランサーを構成できます。 指定されたプロトコル、間隔、要求パスでポートに ping して、アプリケーションが実行されていることを確認するためのプローブを定義することもできます。 [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) もスケール セットをサポートし、レイヤー 7 のより洗練された負荷分散シナリオに対応します。
  
   [こちらのサンプル](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl)では、Apache Web サーバーを実行するスケール セットが作成され、各 VM が受け取る負荷がロード バランサーによって分散されます  (Microsoft.Network/loadBalancers というリソースの種類のほか、virtualMachineScaleSet の networkProfile と extensionProfile をご覧ください)。

   [こちらの Linux のサンプル](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway)と[こちらの Windows のサンプル](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway)では、Application Gateway が使用されます。  

* **PaaS クラスター マネージャーでスケール セットをコンピューティング クラスターとしてデプロイする**: スケール セットは、次世代の worker ロールとして説明されることがあります。 有効な説明ですが、スケール セットの機能を Azure Cloud Services の機能と混同させる危険性があります。 ある意味で、スケール セットは真の worker ロールまたは worker リソースを提供します。 スケール セットは、プラットフォーム/ランタイムに依存せず、カスタマイズ可能で、Azure Resource Manager IaaS に統合された、汎用のコンピューティング リソースです。
  
   Cloud Services worker ロールは、プラットフォーム/ランタイムのサポートの点で制限がありますが (Windows プラットフォーム イメージのみ)、 VIP スワップ、構成可能なアップグレード設定、ランタイム/アプリ デプロイ固有の設定などのサービスも提供しています。 これらのサービスは、スケール セットでは "*まだ*" 利用できないか、Azure Service Fabric などの他の高レベル PaaS サービスによって提供されます。 Scale Sets は PaaS をサポートするインフラストラクチャであると見なすことができます。 [Service Fabric](https://azure.microsoft.com/services/service-fabric/) のような PaaS ソリューションは、このインフラストラクチャの上に構築されます。
  
   そのようなアプローチの[こちらの例](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)では、[Azure Container Service](https://azure.microsoft.com/services/container-service/) によって、コンテナー オーケストレーターを使用するスケール セットに基づいてクラスターがデプロイされます。

## <a name="scale-set-performance-and-scale-guidance"></a>Scale Sets のパフォーマンスとスケールのガイダンス
* スケール セットでは、最大 1,000 個の VM がサポートされます。 独自のカスタム VM イメージを作成してアップロードする場合は、上限が 300 個になります。 大規模なスケール セットを使用する際の考慮事項については、「[大規模な Virtual Machine Scale Sets の使用](virtual-machine-scale-sets-placement-groups.md)」をご覧ください。
* Scale Sets を使用するために Azure ストレージ アカウントを事前作成する必要はありません。 スケール セットでは Azure 管理ディスクがサポートされているため、ストレージ アカウントあたりのディスク数に関してパフォーマンスを懸念しなくて済みます。 詳細については、「[Azure VM Scale Sets と管理ディスク](virtual-machine-scale-sets-managed-disks.md)」をご覧ください。
* VM のプロビジョニング時間を短縮して予測できるものにし、I/O パフォーマンスを向上させるために、Azure Storage ではなく Azure Premium Storage を使用することを検討してください。
* 作成できる VM の数は、デプロイ先のリージョンのコア クォータによって制限されます。 コンピューティング クォータの制限を緩和するには、現時点で Azure Cloud Services で使用するためのコアの上限が高い場合でも、カスタマー サポートへの連絡が必要である場合があります。 クォータを照会するには、Azure CLI コマンド `azure vm list-usage` を実行します。 または、PowerShell コマンド `Get-AzureRmVMUsage` を実行します。

## <a name="frequently-asked-questions-for-scale-sets"></a>Scale Sets に関してよく寄せられる質問
**Q.** Scale Sets には何個の VM を設定できますか?

**A.** スケール セットには、プラットフォーム イメージに基づいて 0 ～ 1,000 個の VM、またはカスタム イメージに基づいて 0 ～ 300 個の VM を含めることができます。 

**Q.** Scale Sets 内でデータ ディスクはサポートされていますか?

**A.** はい。 スケール セットでは、セット内のすべての VM に適用される、接続されたデータ ディスクの構成を定義できます。 詳細については、[Azure Scale Sets と接続されたデータ ディスク](virtual-machine-scale-sets-attached-disks.md)に関するページをご覧ください。 データを格納するための他のオプションを次に示します。

* Azure ファイル (SMB 共有ドライブ)
* OS ドライブ
* 一時ドライブ (ローカル、Azure Storage に保存されない)
* Azure データ サービス (Azure テーブル、Azure BLOB など)
* 外部データ サービス (リモート データベースなど)

**Q.** Scale Sets は、どの Azure リージョンでサポートされていますか?

**A.** すべてのリージョンで Scale Sets がサポートされています。

**Q.** カスタム イメージを使用して Scale Sets を作成するにはどうすればよいですか?

**A.** カスタム イメージ VHD に基づいて管理ディスクを作成し、Scale Sets テンプレートで参照します。 [こちら](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os)でサンプルをご覧ください。

**Q.** Scale Sets 容量を 20 から 15 に減らすと、どの VM が削除されますか?

**A.** 可用性を最大限に高めるために、仮想マシンは、すべての更新ドメインと障害ドメインのスケール セットから均等に削除されます。 ID が最大の VM が最初に削除されます。

**Q.** その後、容量を 15 から 18 に増やすとどうなりますか。

**A.** 容量を 18 に増やすと、3 つの新しい VM が作成されます。 VM が作成されるたびに、VM インスタンス ID は前の最大値に増分された値となります (例: 20、21、22)。 VM は障害ドメインと更新ドメインに分散されます。

**Q.** Scale Sets で複数の拡張機能を使用する場合、実行順序を強制できますか?

**A.** 直接的にではありませんが、カスタム スクリプト拡張機能の場合、スクリプトで他の拡張機能が完了するまで待機できます 。 拡張機能の実行順序についての詳しいガイダンスについては、ブログ記事「[Extension Sequencing in Azure VM Scale Sets (Azure VM Scale Sets における拡張機能の実行順序)](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)」をご覧ください。

**Q.** Scale Sets は、Azure 可用性セットと連携できますか?

**A.** はい。 スケール セットは、5 つの障害ドメインと 5 つの更新ドメインを備えた、暗黙的な可用性セットです。 100 個を超える VM を備えたスケール セットは、複数の可用性セットに相当する複数の "*配置グループ*" にまたがります。 配置グループの詳細については、「[大規模な Virtual Machine Scale Sets の使用](virtual-machine-scale-sets-placement-groups.md)」をご覧ください。 VM の可用性セットは、VM Scale Sets と同じ VNET に存在できます。 一般的な構成では、(多くの場合、可用性セットに固有の構成を必要とする) 制御ノード VM とデータ ノードを Scale Sets に配置します。

スケール セットに関するその他の質問については、「[Azure Virtual Machine Scale Sets FAQ (Azure Virtual Machine Scale Sets に関する FAQ)](virtual-machine-scale-sets-faq.md)」をご覧ください。

