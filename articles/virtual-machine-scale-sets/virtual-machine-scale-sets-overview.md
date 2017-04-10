---
title: "Azure Virtual Machine Scale Sets の概要 | Microsoft Docs"
description: "Azure Virtual Machine Scale Sets について説明します。"
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
ms.date: 03/10/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 2071debe24ef2705b2ee05ce1210a813234cf277
ms.lasthandoff: 03/10/2017


---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>Azure の仮想マシン スケール セットとは
仮想マシン スケール セットは、同一の VM のセットをデプロイおよび管理するための Azure コンピューティング リソースです。 すべての VM が同じ構成になっており、VM を事前にプロビジョニングする必要がない、真の自動スケールをサポートするように設計されています。そのため、ビッグ コンピューティング、ビッグ データ、コンテナー化されたワークロードなどを対象にした大規模サービスを簡単に構築できます。

コンピューティング リソースをスケール アウトしたりスケール インしたりする必要のあるアプリケーションでは、複数の障害ドメインと更新ドメインに対してスケール操作が暗黙的にバランシングされます。 スケール セットの概要については、[Azure ブログでの発表](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/)を参照してください。

以下のビデオで、スケール セットが詳しく解説されています。

* [Mark Russinovich が語る Azure スケール セット](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [仮想マシン スケール セットを Guy Bowerman が解説](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-scale-sets"></a>スケール セットの作成と管理
[Azure Portal](https://portal.azure.com) でスケール セットを作成するには、*[新規]* を選択して、検索バーに「スケール」と入力します。 検索結果に、"仮想マシン スケール セット" が表示されます。 そこから、必要なフィールドを入力し、スケール セットをカスタマイズしてデプロイします。 また、ポータルには、CPU 使用率に基づいて基本的な自動スケール規則を設定するオプションも用意されています。

VM スケール セットは、個々の Azure Resource Manager VM と同様に、JSON テンプレートと [REST API](https://msdn.microsoft.com/library/mt589023.aspx) を使用して、定義およびデプロイすることもできます。 そのため、Azure リソース マネージャーの標準的なデプロイ方法を利用することができます。 テンプレートの詳細については、「 [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」をご覧ください。

仮想マシン スケール セットのサンプル テンプレートは、[こちら](https://github.com/Azure/azure-quickstart-templates)の Azure Quickstart templates GitHub リポジトリから入手できます (タイトルに *vmss* が付いているテンプレートを探してください)。

これらのテンプレートの詳細ページには、ポータルのデプロイ機能にリンクしているボタンあります。 スケール セットをデプロイするには、ボタンをクリックし、ポータルで必要なすべてのパラメーターを指定します。 リソースで大文字や大文字と小文字の混在がサポートされているかどうかが不明な場合は、常に小文字と数字のパラメーター値を使用する方が安全です。 以下のサイトで、スケール セット テンプレートについてやさしく解説したビデオをご覧いただけます。

[VM スケール セット テンプレートの解説](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-scale-set-out-and-in"></a>スケール セットのスケール アウトとスケール イン
Azure Portal でスケール セットの容量を変更するには、_[設定]_ で _[スケーリング]_ セクションをクリックします。 

コマンド ラインでスケール セットの容量を変更するには、[Azure CLI](https://github.com/Azure/azure-cli) で _scale_ コマンドを使用します。 たとえば、スケール セットの容量を VM 10 個に設定するには、次のコマンドを実行します。

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

PowerShell を使用してスケール セット内の VM の数を設定するには、_Update-AzureRmVmss_ コマンドを使用します。

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

Azure Resource Manage テンプレートを使用してスケール セット内の仮想マシンの数を増減するには、*capacity* プロパティを変更し、テンプレートを再デプロイします。 このように操作は単純なので、Azure 自動スケールを使用して複数のスケール セットも簡単に統合できます。Azure 自動スケールでサポートされていないカスタム スケール イベントを定義する必要がある場合は、独自のカスタム スケール層を簡単に記述できます。 

容量を変更するために Azure Resource Manager テンプレートを再デプロイする場合は、"SKU" プロパティ パケットと更新された容量だけが含まれた、非常に小さいテンプレートを定義できます。 その例は、[こちら](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)にあります。

## <a name="autoscale"></a>Autoscale

Azure Portal でスケール セットを作成する場合、オプションで自動スケール設定を構成できます。自動スケール設定を構成すると、平均 CPU 使用率に基づいて VM の数を増減できます。 [Azure のクイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates)にあるスケール セット テンプレートの多くでは、自動スケール設定が定義されています。 既存のスケール セットに自動スケール設定を追加することもできます。 たとえば、以下は、CPU ベースの自動スケールをスケール セットに追加するための Azure PowerShell スクリプトです。

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

 「[Azure Monitor のサポートされるメトリック](../monitoring-and-diagnostics/monitoring-supported-metrics.md)」の「_Microsoft.Compute/virtualMachineScaleSets_」には、スケールのための有効なメトリックの一覧があります。 スケジュールに基づく自動スケールや、webhook を使用したアラート システムとの統合など、高度な自動スケールのオプションも用意されています。

## <a name="monitoring-your-scale-set"></a>スケール セットの監視
[Azure Portal](https://portal.azure.com) には、スケール セットがプロパティと共に一覧表示されます。 Azure Portal は管理操作もサポートしており、スケール セットとスケール セット内の個々の VM の両方を管理できます。 また、カスタマイズ可能なリソース使用量グラフも表示されます。 Azure リソースの基盤となっている JSON 定義を表示または編集する必要がある場合は、[Azure リソース エクスプローラー](https://resources.azure.com)を使用することもできます。 スケール セットは、Microsoft.Compute Azure リソース プロバイダーの下にあるリソースなので、このサイトから以下のリンクを展開すると表示できます。

**[Subscriptions] -> 自分のサブスクリプション -> [resourceGroups] -> [providers] -> [Microsoft.Compute] -> [virtualMachineScaleSets] -> 自分のスケール セット-> etc.**

## <a name="scale-set-scenarios"></a>スケール セットのシナリオ
このセクションでは、標準的なスケール セットのシナリオをいくつか紹介します。 一部の高レベルの Azure サービス (Batch、Service Fabric、Azure Container Service など) も、これらのシナリオを使用します。

* **スケール セット インスタンスへの RDP/SSH** - スケール セットは VNET の中に作成され、スケール セット内の個々の VM にはパブリック IP アドレスが割り当てられていません。 このポリシーによって、コンピューティング グリッド内のすべてのノードに個別にパブリック IP アドレスを割り当てる費用や管理オーバーヘッドを回避できます。 これらの VM には、ロード バランサーやスタンドアロンの仮想マシンなど、VNET 内の他のリソースから接続できます。このようなリソースには、パブリック IP アドレスを割り当てることができます。
* **NAT 規則を使用した VM への接続** - パブリック IP アドレスを作成し、ロード バランサーに割り当てることができます。そのうえで、IP アドレス上のポートをスケール セット内の VM 上のポートにマッピングする受信 NAT プールを定義できます。 次に例を示します。
  
  | から | 発信元ポート | 変換先 | 宛先ポート |
  | --- | --- | --- | --- |
  |  パブリック IP |ポート 50000 |vmss\_0 |ポート 22 |
  |  パブリック IP |ポート 50001 |vmss\_1 |ポート 22 |
  |  パブリック IP |ポート 50002 |vmss\_2 |ポート 22 |
  
   次のリンク先にあるサンプルでは、1 つのパブリック IP を使用してスケール セット内のすべての VM に対する SSH 接続を許可するように NAT 規則が定義されています: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   同じことを RDP と Windows で行う例は、 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **"Jumpbox" を使用して VM に接続** - スケール セットとスタンドアロン VM を同じ VNET 内に作成する場合、スタンドアロン VM とスケール セット VM は、VNET/サブネットによって定義された内部 IP アドレスを使用して、互いに接続することができます。 パブリック IP アドレスを作成し、スタンドアロン VM に割り当てる場合は、スタンドアロン VM に RDP または SSH 接続して、そのコンピューターからスケール セット インスタンスに接続できます。 これでお気付きかもしれませんが、単純なスケール セットは、本質的に、既定の構成のパブリック IP アドレスを持つ単純なスタンドアロン VM よりも安全です。
  
   たとえば、次のテンプレートでは、スタンドアロン VM を使用した単純なスケール セットがデプロイされます。[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)
* **スケール セット インスタンスに対する負荷分散** - "ラウンド ロビン" 方式を使用して VM のコンピューティング クラスターに作業を割り当てる場合は、それに応じたレイヤー&4; の負荷分散規則で Azure Load Balancer を構成できます。 指定されたプロトコル、間隔、および要求パスでポートを ping して、アプリケーションが実行されていることを確認するためのプローブを定義することもできます。 また Azure [Application Gateway](https://azure.microsoft.com/services/application-gateway/) はスケール セットをサポートし、レイヤー&7; のより洗練された負荷分散シナリオに対応します。
  
   次のリンク先にあるサンプルでは、Apache Web サーバーが実行されるスケール セットを作成し、ロード バランサーを使用して各 VM が受ける負荷を分散します: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) (Microsoft.Network/loadBalancers というリソースの種類と、virtualMachineScaleSet の networkProfile および extensionProfile を確認してください)

   次のサンプルでは Application Gateway を使用しています。 Linux:  [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway). Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway) 

* **PaaS クラスター マネージャーでのコンピューティング クラスターとしてのスケール セットのデプロイ** - スケール セットは、次世代の worker ロールとして説明されることがあります。 有効な説明ですが、スケール セットの機能を Azure Cloud Services の機能と混同させる危険性があります。 ある意味で、スケール セットは、真の "worker ロール" を提供します。つまり、プラットフォーム/ランタイムに依存せず、カスタマイズ可能で、Azure Resource Manager IaaS に統合された、汎用のコンピューティング リソースである worker リソースを提供します。
  
   Cloud Services worker ロールは、プラットフォーム/ランタイムのサポートの面では制限がありますが (Windows プラットフォーム イメージのみ)、VIP スワップ、構成可能なアップグレード設定、ランタイム/アプリ デプロイ固有の設定などのサービスも提供しています。これらのサービスは、スケール セットでは "*まだ*" 利用できないか、Service Fabric などの他の高レベル PaaS サービスによって提供されます。 スケール セットは PaaS をサポートするインフラストラクチャであると見なすことができます。 [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) のような PaaS ソリューションは、このインフラストラクチャの上に構築されます。
  
   このアプローチの一例として、次のテンプレートでは、[Azure Container Service](https://azure.microsoft.com/services/container-service/) によって、コンテナー オーケストレーターを使用するスケール セットに基づいてクラスターがデプロイされます: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)

## <a name="scale-set-performance-and-scale-guidance"></a>スケール セットのパフォーマンスとスケールのガイダンス
* スケール セットでは、1 つのスケール セットに最大 1,000 個の VM を設定できます。 独自のカスタム VM イメージを作成してアップロードする場合は、上限が 100 個になります。 大規模なスケール セットを使用する際の考慮事項については、「[大規模な仮想マシン スケール セットの使用](virtual-machine-scale-sets-placement-groups.md)」をご覧ください。
* スケール セットを使用するために Azure ストレージ アカウントを事前作成する必要はありません。 スケール セットでは Azure Managed Disks がサポートされているため、ストレージ アカウントあたりのディスク数に関してパフォーマンスを懸念しなくて済みます。 詳細については、「[Azure VM スケール セットと管理ディスク](virtual-machine-scale-sets-managed-disks.md)」をご覧ください。
* VM のプロビジョニング時間を短縮して予測できるものにし、IO パフォーマンスを向上させるために、Azure Standard Storage ではなく Azure Premium Storage を使用することを検討してください。
* 作成できる VM の数は、デプロイ先のリージョンのコア クォータによって制限されます。 計算クォータの制限を緩和するには、現時点で Azure Cloud Services で使用するためのコアの上限が高い場合でも、カスタマー サポートへの連絡が必要である場合があります。 クォータを照会するには、Azure CLI コマンドの場合は `azure vm list-usage`、PowerShell コマンドの場合は `Get-AzureRmVMUsage` (1.0 以前のバージョンの PowerShell を使用している場合は `Get-AzureVMUsage`) を実行します。

## <a name="scale-set-frequently-asked-questions"></a>スケール セットに関してよく寄せられる質問
**Q.** スケール セットには何個の VM を設定できますか?

**A.** スケール セットには、プラットフォーム イメージに基づいて 0 ～ 1,000 個の VM、またはカスタム イメージに基づいて 0 ～ 100 個の VM を含めることができます。 

**Q.** スケール セット内でデータ ディスクはサポートされていますか?

**A.** はい。 スケール セットでは、セット内のすべての VM に適用される、接続されたデータ ドライブの構成を定義できます。 詳細については、(Azure スケール セットと接続されたデータ ディスク)[virtual-machine-scale-sets-attached-disks.md]に関するページをご覧ください。 データを格納するための他のオプションを次に示します。

* Azure ファイル (SMB 共有ドライブ)
* OS ドライブ
* 一時ドライブ (ローカルで、Azure Storage にサポートされない)
* Azure データ サービス (Azure テーブル、Azure BLOB など)
* 外部データ サービス (リモート DB など)

**Q.** スケール セットは、どの Azure リージョンでサポートされていますか?

**A.** すべてのリージョンでスケール セットがサポートされています。

**Q.** カスタム イメージを使用してスケール セットを作成するにはどうすればよいですか?

**A.** カスタム イメージ VHD に基づいて管理ディスクを作成し、スケール セット テンプレートで参照します。 次のリンク先にサンプルがあります: [https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os)

**Q.** スケール セット容量を 20 から 15 に減らすと、どの VM が削除されますか?

**A.** 可用性を最大限に高めるために仮想マシンは、すべてのアップグレード ドメインと障害ドメインのスケール セットから均等に削除されます。 ID が最大の VM が最初に削除されます。

**Q.** その後、容量を 15 から 18 に増やす場合はどうなるでしょうか?

**A.** 容量を 18 に増やすと、3 つの新しい VM が作成されます。 作成されるたびに、VM インスタンス ID は前の最大値に増分された値となります (例: 20、21、22) 。 VM は FD と UD 間に分散されます。

**Q.** スケール セットで複数の拡張機能を使用する場合、実行順序を強制できますか?

**A.** 直接的にではありませんが、customScript 拡張機能の場合、スクリプトで他の拡張機能が完了するまで待機できます ([たとえば、拡張機能ログを監視することによって](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)) を実行します。 拡張機能の実行順序についての詳しいガイダンスについては、ブログ記事「 [Extension Sequencing in Azure VM Scale Sets (Azure VM スケール セットにおける拡張機能の実行順序)](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)」を参照してください。

**Q.** スケール セットは、Azure 可用性セットと連携できますか?

**A.** はい。 スケール セットは、5 つの FD と 5 つの UD を持つ、暗黙的な可用性セットです。 100 を超える VM を備えたスケール セットは、複数の可用性セットに相当する複数の "配置グループ" にまたがります。 配置グループの詳細については、「[大規模な仮想マシン スケール セットの使用](virtual-machine-scale-sets-placement-groups.md)」をご覧ください。 VM の可用性セットは、VM のスケール セットと同じ VNET に存在できます。 一般的な構成では、(多くの場合、可用性セットに固有の構成を必要とする) 制御ノード VM とデータ ノードをスケール セットに配置します。

ここに記載されていない、スケール セットに関してよく寄せられる質問については、「[Azure Virtual Machine Scale Sets FAQ (Azure 仮想マシン スケール セットに関する FAQ)](virtual-machine-scale-sets-faq.md)」をご覧ください。

