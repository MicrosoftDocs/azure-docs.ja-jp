---
title: SAP アプリケーションの Azure 近接通信配置グループ | Microsoft Docs
description: Azure 近接通信配置グループを使用する SAP のデプロイ シナリオについて説明します
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 924fdef475c43023c69c3006db19cd9a5aa15349
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669663"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>SAP アプリケーションで最適なネットワーク待ち時間を実現するための Azure 近接通信配置グループ
SAP NetWeaver または SAP S/4HANA アーキテクチャを基盤とする SAP アプリケーションは、SAP アプリケーション層と SAP データベース層の間のネットワーク待ち時間の影響を受けます。 この影響は、アプリケーション レイヤーで実行されているほとんどのビジネス ロジックの結果です。 SAP アプリケーション レイヤーはビジネス ロジックを実行するため、データベース層に対して、1 秒あたり何千回または何万回という高い頻度でクエリを実行します。 これらのクエリの性質は、ほとんどの場合単純です。 多くの場合、データベース層で 500 マイクロ秒以内で実行できます。

そのようなクエリをアプリケーション層からデータベース層に送信し、その結果セットを受け取るためにネットワークで費やされる時間は、ビジネス プロセスの実行にかかる時間に大きく影響します。 ネットワーク待ち時間によるこの影響が理由で、SAP デプロイ プロジェクトでは特定の最大ネットワーク待ち時間を実現しなければならないことがあります。 ネットワーク待ち時間を分類する方法のガイドラインについては、[SAP Note #1100926 - FAQ:ネットワーク パフォーマンス](https://launchpad.support.sap.com/#/notes/1100926/E) に関する記事を参照してください。

多くの Azure リージョンで、データセンターの数が増加しています。 同時に、(特にハイエンド SAP システムの) お客様は、M および Mv2 ファミリまたは HANA Large Instances のより特別な VM SKU を使用しています。 これらの Azure 仮想マシンの種類は、Azure リージョンを補完するすべてのデータセンターで利用できるわけではありません。 これらの事実によって、SAP アプリケーション層と SAP DBMS 層の間のネットワーク待ち時間を最適化する機会が生じる場合があります。

ネットワーク待ち時間を最適化できるように、Azure では[近接配置グループ](../../co-location.md)が提供されています。 近接配置グループを使用すると、さまざまな種類の VM を 1 つの Azure データセンターに強制的にグループ化して、このさまざまな種類の VM 間のネットワーク待ち時間を可能な限り最適化できます。 このような近接配置グループに最初の VM をデプロイするプロセスでは、その VM が特定のデータセンターに関連付けられます。 この見通しは魅力的に聞こえますが、この構成の使用には次のようないくつかの制限もあります。

- すべての Azure VM の種類をすべての Azure データセンターで利用できるとは限りません。 そのため、1 つの近接配置グループ内で異なる VM の種類を組み合わせることは制限される可能性があります。 これらの制限が発生するのは、特定の種類の VM を実行するために必要なホスト ハードウェアが、配置グループがデプロイされたデータセンターに存在しない可能性があるためです。
- 1 つの近接配置グループ内の VM の一部をサイズ変更する際、あらゆる場合に、近接配置グループに含まれる他の VM と同じデータセンターで新しい VM の種類を使用できると無意識に想定することはできません。
- Azure がハードウェアを使用停止すると、近接配置グループの特定の VM が別の Azure データセンターに強制移動されることがあります。 このケースの詳細については、[待ち時間短縮のためのリソースの併置](../../co-location.md#planned-maintenance-and-proximity-placement-groups)に関するドキュメントを参照してください。  

> [!IMPORTANT]
> 考えられる制限を受けて、近接配置グループは次のように使用する必要があります。
>
> - 必要な場合のみ
> - システム全体のランドスケープまたは完全な SAP ランドスケープではなく、単一の SAP システムという粒度でのみ
> - 近接配置グループ内の各種 VM の種類と VM の数を最小限に抑えるように

前提として、可用性ゾーンを指定して VM をデプロイし、同じ可用性ゾーンを選択する場合、これらの VM 間のネットワーク待ち時間は、SAP NetWeaver および S/4HANA システムを満足のいくパフォーマンスとスループットで運用するのに十分なものである必要があります。 この前提は、特定のゾーンが 1 つのデータセンターで構成されているか、複数のデータセンターで構成されているかには関係ありません。 ゾーンでのデプロイに近接配置グループを使用する唯一の理由は、Azure 可用性セットを使用してデプロイされた VM を、ゾーンにデプロイされた VM と一緒に割り当てる必要があるケースであるということです。


## <a name="what-are-proximity-placement-groups"></a>近接通信配置グループとは 
Azure 近接通信配置グループとは、論理構造です。 近接配置グループは定義されると、Azure リージョンと Azure リソース グループに関連付けられます。 VM がデプロイされると、近接通信配置グループは次によって参照されます。

- データセンターにデプロイされた最初の Azure VM。 最初の仮想マシンは、最終的に特定の可用性ゾーンのユーザー定義と組み合わされる Azure 割り当てアルゴリズムに基づいてデータセンターにデプロイされる "スコープ VM" と考えることができます。
- 以降にデプロイされるすべての VM。近接通信配置グループを参照して、デプロイされる後続のすべての Azure VM を最初の仮想マシンと同じデータセンターに配置します。

> [!NOTE]
> 最初の VM が配置されたデータセンターに、特定の VM の種類を実行できるホスト ハードウェアがデプロイされていない場合、要求された VM の種類のデプロイは成功しません。 エラー メッセージが表示されます。

1 つの [Azure リソース グループ](../../../azure-resource-manager/management/manage-resources-portal.md)には、複数の近接通信配置グループを割り当てることができます。 しかし、1 つの近接通信配置グループは、1 つの Azure リソース グループにしか割り当てることができません。


## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Azure VM のみを使用する SAP システムの近接通信配置グループ
Azure 上のほとんどの SAP NetWeaver および S/4HANA システム デプロイでは、[HANA Large Instances](./hana-overview-architecture.md) は使用されません。 HANA Large Instances を使用しないデプロイでは、SAP アプリケーション レイヤーと DBMS 層の間で最適なパフォーマンスを提供することが重要です。 これを行うには、当該システムのためだけの Azure 近接通信配置グループを定義します。

ほとんどのお客様のデプロイでは、お客様は、SAP システム用に 1 つの [Azure リソース グループ](../../../azure-resource-manager/management/manage-resources-portal.md)を作成します。 その場合は、たとえば、運用 ERP システム リソース グループとその近接通信配置グループの間に一対一のリレーションシップがあります。 また、お客様がリソース グループを水平方向に編成し、すべての運用システムを 1 つのリソース グループにまとめる場合もあります。 この場合は、運用 SAP システム用のリソース グループと、運用 SAP ERP、SAP BW などの複数の近接通信配置グループとの間に、一対多のリレーションシップがあります。

複数の SAP 運用システムまたは非運用システムを 1 つの近接通信配置グループにまとめることは避けてください。 少数の SAP システム、または 1 つの SAP システムといくつかの周辺アプリケーションで、低待機時間のネットワーク通信が必要な場合は、これらのシステムを 1 つの近接通信配置グループに移動することを検討してください。 1 つの近接配置グループ内にグループ化するシステムの数が増えるほど、次のようなことが起こる可能性が高くなるため、システムのバンドルは避けてください。

- その近接通信配置グループのスコープが設定された特定のデータセンターで実行できない VM の種類が必要になる。
- M シリーズの VM のような、メインストリームでない VM のリソースは、時間の経過と共に近接通信配置グループにソフトウェアが追加されることで追加が必要になっても、最終的に利用できない可能性がある。

先に説明した理想的な構成は次のようになります。

![Azure VM のみを含む近接通信配置グループ](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

この場合、単一の SAP システムはそれぞれ 1 つのリソース グループにグループ化され、それぞれに 1 つの近接通信配置グループがあります。 HANA スケールアウト構成と DBMS スケールアップ構成のどちらを使用するかに依存することはありません。

## <a name="proximity-placement-groups-and-hana-large-instances"></a>近接通信配置グループと HANA Large Instances
一部の SAP システムがアプリケーション レイヤーに関して [HANA Large Instances](./hana-overview-architecture.md) に依存している場合、[リビジョン 4 の行またはスタンプ](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)にデプロイされた HANA Large Instance ユニットを使用しているときに、HANA Large Instance ユニットと Azure VM との間のネットワーク待ち時間が大幅に改善されます。 改善点の 1 つは、HANA Large Instances ユニットはデプロイされるときに、近接通信配置グループと共にデプロイされることです。 その近接通信配置グループを使用して、アプリケーション レイヤーの VM をデプロイすることができます。 その結果、それらの VM は、HANA Large Instances ユニットをホストする同じデータセンターにデプロイされます。

HANA Large Instances ユニットがリビジョン 4 のスタンプまたは行にデプロイされているかどうかを確認するには、「[Azure portal を介した Azure HANA Large Instances の制御](./hana-li-portal.md#look-at-attributes-of-single-hli-unit)」の記事を確認してください。 HANA Large Instance ユニットの属性の概要では、近接通信配置グループの名前も確認できます。これは、HANA Large Instance ユニットがデプロイされたときに作成されたためです。 属性の概要に表示される名前は、アプリケーション レイヤーの VM のデプロイ先である近接通信配置グループの名前です。

Azure 仮想マシンのみを使用する SAP システムと比べると、HANA Large Instances を使用する場合、使用する [Azure リソース グループ](../../../azure-resource-manager/management/manage-resources-portal.md)の数を決定する際の柔軟性は低くなります。 [HANA Large Instances テナント](./hana-know-terms.md)のすべての HANA Large Instance ユニットは、1 つのリソース グループにグループ化されます ([こちらの記事](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal)を参照)。 運用システムと非運用システム、または他のシステムなどを分離するために別々のテナントにデプロイする場合を除き、すべての HANA Large Instances ユニットは 1 つの HANA Large Instances テナントにデプロイされます。 このテナントには、リソース グループとの一対一のリレーションシップがあります。 ただし、単一のユニットごとに、別個の近接通信配置グループが定義されます。

その結果、1 つのテナントの Azure リソース グループと近接通信配置グループ間のリレーションシップは次のようになります。

![近接通信配置グループと HANA Large Instances](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>近接通信配置グループを使用したデプロイの例
次に、Azure 近接通信配置グループを使用して VM をデプロイするために使用できる PowerShell コマンドをいくつか紹介します。

[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) にサインインした後の最初の手順では、デプロイに使用する Azure サブスクリプションを使用しているかどうかを確認します。

<pre><code>
Get-AzureRmContext
</code></pre>

別のサブスクリプションに変更する必要がある場合は、次のコマンドを実行します。

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

次のコマンドを実行して、新しい Azure リソース グループを作成します。

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

次のコマンドを実行して、新しい近接通信配置グループを作成します。

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

次のようなコマンドを使用して、最初の VM を近接通信配置グループにデプロイします。

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

上記のコマンドは、Windows ベースの VM をデプロイします。 この VM のデプロイが成功すると、近接通信配置グループのデータセンターのスコープが Azure リージョン内で定義されます。 前のコマンドで示されているように、近接配置グループを参照する後続のすべての VM デプロイは、同じ Azure データセンターにデプロイされます。これは、そのデータセンターに配置されているハードウェアでその VM の種類をホストでき、その VM の種類用の容量が使用可能であることが条件になります。

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>可用性セットと Availability Zones を近接通信配置グループに結合する
SAP システムのデプロイに Availability Zones を使用する場合の欠点の 1 つは、特定のゾーン内の可用性セットを使用して SAP アプリケーション レイヤーをデプロイできないことです。 SAP アプリケーション レイヤーを DBMS レイヤーと同じゾーンにデプロイする必要があります。 1 つの VM をデプロイするときに可用性ゾーンと可用性セットを参照することはサポートされていません。 そのため、以前は、ゾーンを参照することによってアプリケーション レイヤーをデプロイする必要がありました。 アプリケーション レイヤー VM がさまざまな更新ドメインと障害ドメインに分散されていることを確認する機能は失われました。

近接通信配置グループを使用すると、この制限を回避できます。 デプロイ シーケンスを次に示します。

- 近接通信配置グループを作成します。
- 可用性ゾーンを参照することによって、アンカー VM (通常は DBMS サーバー) をデプロイします。
- Azure 近接通信グループを参照する可用性セットを作成します。 (この記事で後述するコマンドを参照してください。)
- 可用性セットと近接通信配置グループを参照してアプリケーション レイヤーの VM をデプロイします。

前のセクションで説明したように、最初の VM をデプロイする代わりに、VM をデプロイするときに可用性ゾーンと近接通信配置グループを参照します。

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

この仮想マシンを正常にデプロイすると、SAP システムのデータベース インスタンスが 1 つの可用性ゾーンでホストされます。 近接通信配置グループのスコープは、定義した可用性ゾーンを表すデータセンターのいずれかに固定されます。

同じゾーン (単数または複数) と同じ近接通信配置グループを参照して、DBMS VM と同じようにセントラル サービス VM をデプロイするとします。 次の手順では、SAP システムのアプリケーション レイヤーに使用する可用性セットを作成する必要があります。

近接配置グループを定義して作成します。 可用性セットを作成するためのコマンドでは、別途、近接通信配置グループの ID (名前ではありません) を参照しなければなりません。 近接通信配置グループの ID を取得するには、次のコマンドを使用します。

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

可用性セットを作成する際、マネージド ディスク (特に指定されていない場合は既定) と近接通信配置グループを使用する場合は追加のパラメーターを考慮する必要があります。

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

理想的には、3 つの障害ドメインを使用します。 ただし、サポートされる障害ドメインの数はリージョンによって異なる場合があります。 この場合、特定のリージョンに対して可能な障害ドメインの最大数は 2 です。 アプリケーション レイヤーの VM をデプロイするには、次に示すように、可用性セット名と近接通信配置グループ名への参照を追加する必要があります。

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

このデプロイの結果は次のとおりです。
- 特定の可用性ゾーン (単数または複数) に配置されている SAP システムの DBMS レイヤーとセントラル サービス。
- DBMS VM (単数または複数) と同じ Azure データセンター内の可用性セットを介して配置された SAP アプリケーションレイヤー。

> [!NOTE]
> 1 つの DBMS VM をあるゾーンにデプロイし、2 つ目の DBMS VM を別のゾーンにデプロイして高可用性構成を作成するため、ゾーンごとに異なる近接通信配置グループが必要になります。 使用するどの可用性セットでも同様です。

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>既存のシステムを近接通信配置グループに移動する
既に SAP システムをデプロイしている場合は、重要なシステムの一部のネットワーク待ち時間を最適化し、アプリケーション レイヤーと DBMS レイヤーを同じデータセンターに配置することができます。 完全な Azure 可用性セットの VM を、既にスコープが設定されている既存の近接配置グループに移動するには、Azure portal、PowerShell、または CLI を使用して、可用性セットの VM をすべてシャットダウンし、可用性セットを既存の近接配置グループに割り当てる必要があります。 可用性セットに含まれていない VM を既存の近接配置グループに移動する場合は、その VM をシャットダウンして、既存の近接配置グループに割り当てるだけです。 


## <a name="next-steps"></a>次のステップ
次のドキュメントを参照してください。

- [Azure での SAP ワークロードの計画とデプロイに関するチェックリスト](./sap-deployment-checklist.md)
- [プレビュー:Azure CLI を使用して近接通信配置グループに VM をデプロイする](../../linux/proximity-placement-groups.md)
- [プレビュー:PowerShell を使用して近接通信配置グループに VM をデプロイする](../../windows/proximity-placement-groups.md)
- [SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](./dbms_guide_general.md)