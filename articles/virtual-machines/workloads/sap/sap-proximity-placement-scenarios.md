---
title: SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ | Microsoft Docs
description: Azure 近接通信配置グループを使用した SAP のデプロイ シナリオについて説明します
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 02dcb7174dd9cb2926ef2fafda4b521b939ae68a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70077985"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ
SAP NetWeaver または SAP S/4HANA アーキテクチャを基盤とする SAP アプリケーションは、SAP アプリケーション層と SAP データベース層の間のネットワーク待ち時間の影響を受けます。 これらのアーキテクチャでこうした影響が生じる理由には、ほとんどのビジネス ロジックがアプリケーション レイヤーで実行されるという事実があります。 ビジネス ロジックの実行結果として、SAP アプリケーション レイヤーは、データベース層に対し、1 秒間に数千回や数万回という高い頻度でクエリを実行します。 これらのクエリの性質は単純であることがほとんどです。 しかも、データベース層で実行すれば、500 マイクロ秒未満、またはそれより短時間で済むことも少なくありません。 ネットワークでそのようなクエリをアプリケーション層からデータベース層に送信し、またデータベース層から結果セットを受け取るために費やされる時間が、ビジネス プロセスの実行にかかる時間に大きく影響しています。 このようにネットワーク待ち時間の影響を受けやすいことから、SAP デプロイ プロジェクトでは、しっかりと手間暇をかけて最適なネットワーク待ち時間を実現する必要があるのです。 [SAP Note #1100926 - FAQ: ネットワーク パフォーマンス](https://launchpad.support.sap.com/#/notes/1100926/E)に関するページに、ネットワーク待ち時間を分類する方法についてのいくつかのガイドラインが公開されています。

多くの Azure リージョンでは、データセンターの数が増加しており、その傾向は可用性ゾーンの導入によってさらに拍車がかかっています。 一方、顧客、特にハイエンド SAP システムのユーザーの間では、M シリーズ ファミリや HANA Large Instances の特殊な VM SKU が使用されてきました。 こうしたタイプの Azure 仮想マシンは、特定の Azure リージョンの一部のデータセンターには存在しません。 この 2 つの傾向により、ネットワーク待ち時間が最適な範囲になかったり、SAP システムのパフォーマンスが完全に生かし切れなかったりするケースが顧客側で生じました。

こうした問題を防ぐために、Azure では、[Azure 近接通信配置グループ](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)と呼ばれる構成を提供しています。 この新しい機能は、既にさまざまな SAP システムをデプロイする目的で使われています。 近接通信配置グループの制限については、言及されている記事を確認してください。 この記事では、Azure 近接通信配置グループを使用できる (または使用すべき) さまざまな SAP シナリオについて取り上げます。

## <a name="what-are-proximity-placement-groups"></a>近接通信配置グループとは 
Azure 近接通信配置グループは、定義の段階で Azure リージョンおよび Azure リソース グループにバインドされる論理構成です。 VM のデプロイ中、近接通信配置グループを参照するものは次のとおりです。

- データセンターに配置する、最初にデプロイされる Azure VM。 最初の仮想マシンは、(最終的に特定の Azure 可用性ゾーンのユーザー定義と組み合わされる) Azure の割り当てアルゴリズムに基づいてデータセンターにデプロイされるアンカー VM と考えることができます。
- その後にデプロイされる、その近接通信配置グループを参照するすべての VM。この近接通信配置グループは、最初の仮想マシンの配置先と同じデータセンターに続けてデプロイされるすべての Azure VM の配置先となります。

> [!NOTE]
> 最初の VM の配置先と同じデータセンターに、特定の VM タイプを実行できるホスト ハードウェアがデプロイされていない場合、要求されたタイプの VM のデプロイは成功せず、エラー メッセージが返されて終了します。 これらは、GPU を搭載した仮想マシンや HPC VM タイプなど、主流でない VM を軸として展開すべきケースと考えられます (M シリーズ VM を最初の VM タイプとしてデプロイするなど)。

1 つの [Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)には、複数の近接通信配置グループを割り当てることができます。 ただし、1 つの近接通信配置グループを複数の Azure リソース グループに割り当てることはできません。

近接通信配置グループを使用する際は、次の点に注意してください。

- SAP システムのパフォーマンスを最適化しようと、近接通信配置グループを使用してシステムの Azure データセンターを一本化した場合、そのような近接通信配置グループ内では組み合わせることのできない VM ファミリのタイプが生じることがあります。 ある種の VM を独占的に実行するために必要な特定のホスト ハードウェアが、配置グループの "アンカー VM" がデプロイされているデータセンターに存在しない可能性があるためです。
- そうした SAP システムのライフ サイクルでは、システムを別のデータセンターに移さざるを得ない可能性があります。 このような強制的な移行が生じるケースとしては、たとえば、スケールアウト HANA の DBMS レイヤーを 4 ノードから 16 ノードに移すことになった場合が考えられます。 既に使用しているタイプの VM をさらに 12 個確保できるだけのキャパシティが同じデータセンターには残っていない、というケースです。
- ハードウェアの使用停止により、Microsoft では、ご利用の VM タイプのキャパシティ増強を同じデータセンターではなく、別のデータセンターに対して行う可能性があります。 そのような事態が生じた場合、結果的にお客様は、近接通信配置グループのすべての VM を別のデータセンターに移動することになるでしょう。


## <a name="azure-proximity-placement-groups-with-sap-systems-using-azure-vms-exclusively"></a>Azure VM のみを使用した Azure 近接通信配置グループと SAP システム
SAP NetWeaver と S/4HANA システムを Azure にデプロイする場合、大多数のケースでは [HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) が使用されません。 そのようなシステムをデプロイする場合、SAP アプリケーション レイヤーと DBMS 層の間で最適なパフォーマンスを確保することが重要です。 そのためには、このシステムのためだけの Azure 近接通信配置グループを定義します。 カスタマー デプロイの大半では、SAP システム用に単一の [Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)を作成することをお客様は選択しています。 そのような場合、たとえば運用 ERP システムのリソース グループとその近接通信配置グループとの間には 1 対 1 の関係が存在することになります。 他方で、お客様が水平方向にそのリソース グループを整理し、すべての運用システムを 1 つのリソース グループにまとめてデプロイしているケースもありました。 そのような場合、運用 SAP システムのリソース グループと複数 (運用 SAP ERP、運用 SAP BW など) の近接通信配置グループの間には、1 対多の関係があることになります。運用システムであるか非運用システムであるかに関係なく、複数のまたは場合によってはすべての SAP システムを 1 つの近接通信配置グループに束ねることは避けてください。 例外的に、少数 (または 1 つ) の SAP システムといくつかの周辺アプリケーションとの間でやり取りされるネットワーク通信の待ち時間を短くしなければならないケースでは、それらのシステムを 1 つの近接通信配置グループに移すことも考えられます。 そのように推奨する理由は、近接通信配置グループに含めるシステムが多いほど、次の事態につながる可能性が高くなるためです。

- 近接通信配置グループの固定先となっている特定のデータセンターでは実行できないタイプの VM が必要になる。
- 既存の近接通信配置グループに新たにソフトウェアを追加していくとやがて、主流でない VM (M シリーズなど) のリソースは、増やす必要が生じても最終的に満たせなくなる。

以上のことを踏まえた理想的な使用形態は次のようになります。

![すべての Azure VM の近接通信配置グループ](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

このケースでは、それぞれ 1 つの近接通信配置グループを含んだ 1 つのリソース グループに、それぞれ 1 つの SAP システムがまとめられています。 HANA スケールアウト構成を使用するか DBMS スケールアップ構成を使用するかに関する依存関係はありません。


## <a name="azure-proximity-placement-groups-and-hana-large-instances"></a>Azure 近接通信配置グループと HANA Large Instances
ご利用の SAP システムがアプリケーション レイヤーとして [HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) に依存している場合、[リビジョン 4 の行またはスタンプ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)にデプロイされた HANA Large Instance ユニットを使用すると、HANA Large Instance ユニットと Azure VM との間のネットワーク待ち時間が大幅に改善されます。 改善点の 1 つとして、HANA Large Instances ユニットでは、そのデプロイ時に最初から、近接通信配置グループがデプロイされることが挙げられます。 その近接通信配置グループを使用して、アプリケーション レイヤーの VM をデプロイすることができます。 結果的にそれらの VM は、HANA Large Instance ユニットがホストされているのと同じデータセンターにデプロイされます。

ご利用の HANA Large Instance ユニットがリビジョン 4 のスタンプまたは行にデプロイされているかどうかを判別する方法については、「[Azure portal を介した Azure HANA L インスタンスの制御](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit)」の記事を参照してください。 HANA Large Instance ユニットの属性の概要で、HANA Large Instance ユニットのデプロイ時に作成された近接通信配置グループの名前も確認できます。 属性の概要に表示される名前が、アプリケーション レイヤーの VM のデプロイ先として使用すべき近接通信配置グループの名前です。

Azure 仮想マシンのみを使用する SAP システムとは対称的に、HANA Large Instances を使用する場合、[Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)をいくつ使用すべきかの判断はさほど重要ではありません。 [HANA Large Instances テナント](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms)の HANA Large Instance ユニットはすべて、1 つの Azure リソース グループにまとめられます ([こちら](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal)で説明されています)。 運用と非運用、特定のシステムなど、複数の異なるテナントにデプロイを分けたい場合を除き、すべての HANA Large Instance ユニットが 1 つの HANA Large Instances テナントにデプロイされ、また、この HANA Large Instances テナントもやはり、Azure リソース グループと 1 対 1 の関係を持ちます。 その一方で、個々のユニットにはいずれも、別個の近接通信配置グループが定義されることとなります。 

その結果、シングル テナントの Azure リソース グループと近接通信配置グループが次のように分類されます。

![すべての Azure VM の近接通信配置グループ](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)


## <a name="short-example-of-deploying-with-azure-proximity-placement-groups"></a>Azure 近接通信配置グループを使用したデプロイの簡単な例
ここでは、Azure 近接通信配置グループを使用した VM のデプロイ方法を具体的に紹介するために、一連の PowerShell コマンドを取り上げます。コマンドの使用法を通じて、Azure 近接通信配置グループの簡単な予行演習をしてみましょう。

[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) にログインしたらまず、デプロイに使用する適切な Azure サブスクリプションに自分がいるかどうかを次のコマンドで確認します。

<pre><code>
Get-AzureRmContext
</code></pre>

別のサブスクリプションに変更する必要がある場合は、次のコマンドを実行するとこの処理を実行できます。

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

3 つ目の手順として、次のコマンドを使用して新しい Azure リソース グループを作成します。

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

この時点で、次のように、新しい近接通信配置グループを作成できます。

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

これで、次のようなコマンドを使用して、この近接通信配置グループに対して最初の VM をデプロイできます。

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

上記のコマンドでは、Windows ベースの VM がデプロイされます。 この VM のデプロイが成功すると、Azure リージョン内で近接通信配置グループのデータセンターのスコープが定義されます。 最後のコマンドと同様に近接通信配置グループを参照する後続の VM はすべて、そのデータセンターに設置されたハードウェアでそのタイプの VM をホストでき、また、そのタイプの VM 用のキャパシティがあれば、すべて同じ Azure データセンターにデプロイされます。

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>可用性セットや可用性ゾーンと近接通信配置グループを組み合わせる 
SAP システムのデプロイに可用性ゾーンを使用した場合のデメリットの 1 つは、特定のゾーン内で可用性セットを使用し、SAP アプリケーション レイヤーを制御してデプロイすることができない点です。 やりたいことは、SAP アプリケーション レイヤーを DBMS レイヤーと同じゾーンにデプロイすることですが、単一の VM をデプロイするときに可用性ゾーンと可用性セットを参照することはサポートされていないため、必然的に、アプリケーション レイヤーはゾーンを参照してデプロイしなければならず、それによって、アプリケーション レイヤーの VM を確実に異なる更新ドメインと障害ドメインに分散させることが不可能になってしまうのです。 近接通信配置グループを利用すれば、この制限を克服することができます。 次のような順序でデプロイすることになります。

- 近接通信配置グループの作成
- 特定の Azure 可用性ゾーンを参照して "アンカー VM" (通常は DBMS サーバー) をデプロイする
- Azure 近接通信グループを参照する可用性セットを作成する (下記参照)
- 可用性セットと近接通信配置グループを参照してアプリケーション レイヤーの VM をデプロイする

VM をデプロイする際、先ほどのデモンストレーションのように最初の VM をデプロイする代わりに、Azure 可用性ゾーンと近接通信配置グループを参照します。

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

SAP システムのデータベース インスタンスを 1 つの Azure 可用性ゾーンでホストするこの仮想マシンのデプロイに成功すると、定義した可用性ゾーンを表すいずれかのデータセンターに、近接通信配置グループのスコープが固定されます。

セントラル サービス VM は、DBMS VM と同じ方法で、DBMS VM と同じゾーンおよび同じ近接通信配置グループを参照してデプロイすることを想定しています。 次の手順では、SAP システムのアプリケーション レイヤー用の可用性セットを作成する必要があります。
近接通信配置グループを定義して作成する必要があります。 可用性セットを作成するためのコマンドでは、別途、近接通信配置グループの ID (名前ではありません) を参照しなければなりません。 近接通信配置グループの ID は、次を使って取得できます。



<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

可用性セットを作成する際、マネージド ディスク (特に指定しない限り既定で使用される) と近接通信配置グループを使用する場合には、さらに他のパラメーターも考慮する必要があります。

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

理想的には、3 つの障害ドメインを使用します。 ただし、サポートされる障害ドメインの数は、リージョンによって異なる場合があります。 このケースでは、特定のリージョンで使用できる障害ドメインの最大数は 2 つでした。 アプリケーション レイヤーの VM をデプロイするためには、次に示したように、可用性セット名と近接通信配置グループ名への参照を追加する必要があります。

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

この順番で実行した結果、最終的には、SAP システムの DBMS レイヤーとセントラル サービスは特定の可用性ゾーンに配置され、SAP アプリケーション レイヤーは、DBMS VM のデプロイ先と同じ Azure データセンターの可用性セットを通じて配置されます。

> [!NOTE]
> 高可用性構成を作成するために、2 つの DBMS VM をそれぞれ異なるゾーンにデプロイする場合、ゾーンごとに異なる近接通信配置グループが必要となります。 同じことは、可用性セットを使用する場合にも当てはまります。

## <a name="get-an-existing-system-into-azure-proximity-placement-groups"></a>既存のシステムを Azure 近接通信配置グループに移動する
SAP システムのデプロイが済んだら、既にあるいくつかの重要なシステムのネットワーク待ち時間を最適化し、アプリケーション レイヤーと DBMS レイヤーを同じデータセンターに配置してみましょう。 近接通信配置グループ機能のパブリック プレビューの段階では、近接通信配置グループに対してそのような移動を行う場合、それらの VM を削除してから、新たに VM を作成する必要があります。 現段階の機能では、VM をシャットダウンするだけでは、このようにシャットダウンした仮想マシンを近接通信配置グループに割り当てることはできません。


## <a name="next-steps"></a>次の手順
以下のドキュメントをご覧ください。

- [Azure での SAP ワークロードの計画とデプロイに関するチェックリスト](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [プレビュー: Azure CLI を使用して近接通信配置グループに VM をデプロイする](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [プレビュー: PowerShell を使用して近接通信配置グループに VM をデプロイする](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

