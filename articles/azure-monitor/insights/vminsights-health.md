---
title: Azure 仮想マシンの正常性を把握する | Microsoft Docs
description: この記事では、Azure Monitor for VMs を使用して、仮想マシンと基になるオペレーティング システムの正常性を把握する方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 2bf891f8cfecbb9e78e511dcee7ed1c61c170016
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340136"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Azure 仮想マシンの正常性を把握する

Azure には、監視領域内の特定のロールまたはタスクのためのサービスが含まれていますが、Azure 仮想マシン (VM) でホストされているオペレーティング システム (OS) の詳細な正常性情報は提供されません。 Azure Monitor は、さまざまな状態に対して使用できますが、コア コンポーネントの正常性や VM の全体的な正常性をモデル化して表すようには設計されていません。

Azure Monitor for VMs の正常性を使用すると、Windows または Linux ゲスト OS の可用性とパフォーマンスをアクティブに監視できます。 この正常性機能は、主要なコンポーネントとそれらの関係を表すモデルを使用し、コンポーネントの正常性を測定する方法を指定する基準を提供し、さらに異常な状態を検出した場合はアラートを送信します。

Azure VM と基になる OS の全体的な正常性状態の表示は、VM から直接か、または Azure Monitor からリソース グループ内のすべての VM に対しての 2 つの観点から観察できます。

この記事では、Azure Monitor for VMs の正常性機能によって検出された正常性の問題をすばやく評価、調査、および解決する方法について説明します。

Azure Monitor for VMs の構成については、[Azure Monitor for VMs の有効化](vminsights-enable-overview.md)に関する記事をご覧ください。

## <a name="monitoring-configuration-details"></a>監視の構成の詳細

このセクションでは、Azure の Windows および Linux VM を監視するための既定の正常性基準の概要について説明します。 すべての正常性基準は、異常な状態を検出した場合はアラートを送信するように事前に構成されています。

### <a name="windows-vms"></a>Windows VM

- Available Megabytes of Memory (メモリの使用可能なメガバイト数)
- Average Disk Seconds Per Write (Logical Disk) (ディスクの平均書き込み秒数 (論理ディスク))
- Average Disk Seconds Per Write (Disk) (ディスクの平均書き込み秒数 (ディスク))
- Average Logical Disk Seconds Per Read (論理ディスクの平均読み取り秒数)
- Average Logical Disk Seconds Per Transfer (論理ディスクの平均転送秒数)
- Average Disk Seconds Per Read (ディスクの平均読み取り秒数)
- Average Disk Seconds Per Transfer (ディスクの平均転送秒数)
- Current Disk Queue Length (Logical Disk) (現在のディスク キューの長さ (論理ディスク))
- Current Disk Queue Length (Disk) (現在のディスク キューの長さ (ディスク))
- Disk Percent Idle Time (ディスク アイドル時間の割合)
- File system error or corruption (ファイル システム エラーまたは破損)
- Logical Disk Free Space (%) Low (論理ディスクの空き領域 (%) 低)
- Logical Disk Free Space (MB) Low (論理ディスクの空き領域 (MB) 低)
- Logical Disk Percent Idle Time (論理ディスク アイドル時間の割合)
- Memory Pages Per Second (1 秒あたりのメモリ ページ数)
- Percent Bandwidth Used Read (使用された帯域幅の割合 (読み取り))
- Percent Bandwidth Used Total (使用された帯域幅の割合 (合計))
- Percent Bandwidth Used Write (使用された帯域幅の割合 (書き込み))
- Percentage of Committed Memory in Use (使用されているコミット済みメモリの割合)
- Disk Percent Idle Time (ディスク アイドル時間の割合)
- DHCP Client Service Health (DHCP クライアント サービスの正常性)
- DNS Client Service Health (DNS クライアント サービスの正常性)
- RPC Service Health (RPC サービスの正常性)
- Server Service Health (サーバー サービスの正常性)
- Total CPU Utilization Percentage (合計 CPU 使用率の割合)
- Windows Event Log Service Health (Windows イベント ログ サービスの正常性)
- Windows Firewall Service Health (Windows ファイアウォール サービスの正常性)
- Windows Remote Management Service Health (Windows リモート管理サービスの正常性)

### <a name="linux-vms"></a>Linux VM

- Disk Avg.Disk sec/Transfer
- Disk Avg.Disk sec/Read
- Disk Avg.Disk sec/Write
- Disk Health (ディスクの正常性)
- Logical Disk Free Space (論理ディスクの空き領域)
- Logical Disk % Free Space (論理ディスクの空き領域 (%))
- Logical Disk % Free Inodes (論理ディスクの空き inode (%))
- Network Adapter Health (ネットワーク アダプターの正常性)
- Total Percent Processor Time (合計プロセッサ時間 (%))
- Operating System Available Megabytes of Memory (オペレーティング システムの使用可能なメモリ メガバイト数)

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

サインインするには、[Azure portal](https://portal.azure.com) に移動します。

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Azure Monitor for VMs の正常性の概要

1 つの VM または VM のグループに対して正常性機能を使用する前に、これらの情報がどのように提供され、視覚化により何が表されているかを理解することが重要です。

### <a name="view-health-directly-from-a-vm"></a>正常性を VM から直接表示する

Azure VM の正常性を表示するには、VM の左側のウィンドウにある **[Insights (プレビュー)]** を選択します。 VM Insights ページでは、 **[正常性]** タブが既定で開き、VM の正常性ビューが表示されます。

![選択した Azure 仮想マシン正常性の概要が表示されている VM の Azure Monitor](./media/vminsights-health/vminsights-directvm-health.png)

**[正常性]** タブの **[ゲスト VM の正常性]** では、VM の正常性状態と異常なコンポーネントによって生成された VM 正常性アラートの総数がテーブルに表示されます。

詳細については、「[アラート](#alerts)」を参照してください。

VM に対して定義されている正常性状態の説明をまとめたものが次の表です。

|アイコン |正常性の状態 |意味 |
|-----|-------------|---------------|
| |Healthy |VM は、定義された正常性状態の範囲内にあります。 この状態は、問題が検出されておらず、VM が正常に機能していることを示します。 親ロールアップ モニターでは、正常性がロールアップされ、子の最高の条件または最悪の条件の状態が反映されます。|
| |重大 |この状態は、定義された正常性状態の範囲内にはなく、1 つ以上の重大な問題が検出されたことを示します。 正常な機能を復元するには、これらの問題に対処する必要があります。 親ロールアップ モニターでは、正常性状態がロールアップされ、子の最高の条件または最悪の条件の状態が反映されます。|
| |警告 |この状態は、定義された正常性状態の 2 つのしきい値の間にあります。ここで、1 つは警告状態を示し、もう 1 つは重大な状態を示します (正常性状態のしきい値は 3 つ構成できます)。または、重大ではない問題が未解決のときに、それにより重大な問題が発生する可能性がある場合です。 親ロールアップ モニターでは、1 つ以上の子が警告状態にある場合、親には警告状態が反映されます。 1 つの子が重大な状態にあり、もう 1 つの子が警告状態にある場合、親ロールアップには正常性状態が重大として表示されます。|
| |Unknown |いくつかの理由で、状態を計算できません。 次のセクションでは、追加の詳細と考えられる解決策について説明します。 |

[不明] の正常性状態は、次の問題によって発生することがあります。

- エージェントが再構成され、Azure Monitor for VMs を有効にしたときに指定されたワークスペースに報告しなくなった。 ワークスペースに報告するようにエージェントを構成するには、「[ワークスペースの追加または削除](../platform/agent-manage.md#adding-or-removing-a-workspace)」を参照してください。
- VM が削除された。
- Azure Monitor for VMs に関連付けられたワークスペースが削除された。 Premier サポートの特典がある場合は、そのワークスペースを復旧できます。 [Premier](https://premier.microsoft.com/) に移動し、サポート要求を開きます。
- ソリューションの依存関係が削除された。 Log Analytics ワークスペースで ServiceMap および InfrastructureInsights ソリューションを再び有効にするには、[Azure Resource Manager テンプレート](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions)を使用して、これらのソリューションを再インストールします。 または、[作業の開始] タブにある [ワークスペースの構成] オプションを使用します。
- VM がシャットダウンされた。
- Azure VM サービスが使用できないか、またはメンテナンスが実行されている。
- ワークスペースの [1 日のデータまたは保持期間の制限](../platform/manage-cost-storage.md)に達した。

**[正常性の診断を表示]** を選択すると、VM のすべてのコンポーネント、関連付けられている正常性基準、状態の変化、およびその VM に関連したコンポーネントを監視することによって検出されたその他の問題を示すページが開きます。

詳細については、「[正常性の診断](#health-diagnostics)」を参照してください。

**[正常性]** セクションでは、正常性基準によって監視されているパフォーマンス コンポーネントの正常性ロールアップがテーブルに表示されます。 これらのコンポーネントには、**CPU**、**メモリ**、**ディスク**、および**ネットワーク**が含まれます。 コンポーネントを選択すると、そのコンポーネントのすべての監視基準と正常性状態が一覧表示されたページが開きます。

Windows を実行する Azure VM から正常性にアクセスすると、上位 5 つのコア Windows サービスの正常性状態が **[コア サービスの正常性]** に表示されます。 これらのサービスのいずれかを選択すると、そのコンポーネントを監視している正常性基準とその正常性状態が一覧表示されたページが開きます。

正常性基準の名前を選択すると、プロパティ ウィンドウが開きます。 このウィンドウでは、構成の詳細 (その正常性基準に、対応する Azure Monitor アラートが割り当てられているかどうかを含む) を確認できます。

詳細については、[正常性の診断および正常性基準の操作](#health-diagnostics)に関する説明を参照してください。

### <a name="aggregate-vm-perspective"></a>集計 VM 観点

リソース グループ内のすべての VM の正常性コレクションを表示するには、ポータルのナビゲーション リストから **[Azure Monitor]** を選択し、 **[Virtual Machines (プレビュー)]** を選択します。

![Azure Monitor からの VM 分析情報監視ビュー](./media/vminsights-health/vminsights-aggregate-health.png)

**[サブスクリプション]** および **[リソース グループ]** ドロップダウン リストで、そのグループに関連した VM を含む適切なリソース グループを選択して、報告された正常性状態を表示します。 その選択は正常性機能にのみ適用され、 **[パフォーマンス]** または **[マップ]** タブには引き継がれません。

**[正常性]** タブには、次の情報が表示されます。

* 重大または異常な状態にある VM の数に対して、正常な VM の数、またはデータを送信していない VM ([不明] 状態と呼ばれます) の数。
* 異常な状態を報告している OS ごとの VM の種類とその数。
* 正常性状態で分類された、プロセッサ、ディスク、メモリ、またはネットワーク アダプターで検出された問題のために異常な VM の数。
* 正常性状態で分類された、コア OS サービスで検出された問題のために異常な VM の数。

**[正常性]** タブでは、VM を監視している正常性基準によって検出された重大な問題を識別し、アラートの詳細とそれに関連付けられたナレッジ記事を確認することができます。 これらの記事は、問題の診断や修復に役立ちます。 任意の重大度を選択すると、その重大度でフィルター処理された [[すべてのアラート]](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) ページが開きます。

**[オペレーティング システムごとの VM の分布]** の一覧には、Windows のエディションまたは Linux のディストリビューションおよびバージョンごとに VM が一覧表示されます。 各 OS カテゴリでは、VM の正常性に基づいて VM がさらに分類されます。

![VM の分析情報: 仮想マシンの分布観点](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

**[VM 数]** 、 **[重大]** 、 **[警告]** 、 **[正常]** 、または **[不明]** を含むいずれかの列を選択します。 選択された列に一致する **[Virtual Machines]** ページで、フィルター処理された結果の一覧を表示します。

たとえば、Red Hat Enterprise Linux リリース 7.5 を実行するすべての VM を確認するには、その OS に対する **[VM 数]** 値を選択すると、そのフィルターに一致する VM とそれらの現在の正常性状態が一覧表示されます。

![Red Hat Linux VM のロールアップの例](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

**[Virtual Machines]** ページで、 **[VM 名]** 列の VM の名前を選択すると、 **[VM instance] (VM インスタンス)** ページが表示されます。 このページには、選択された VM に影響を与えているアラートと正常性基準の問題の詳細が表示されます。 どのコンポーネントが異常かを確認するには、ページの左上隅にある **[正常性の状態]** アイコンを選択して正常性状態の詳細をフィルター処理します。 また、異常なコンポーネントによって生成された VM 正常性アラートを、アラートの重大度で分類して表示することもできます。

**[VM list] (VM リスト)** ビューから VM の名前を選択すると、VM から直接 **[Insights (プレビュー)]** を選択した場合と同じように、その VM の **[正常性]** ページが開きます。

![選択した Azure 仮想マシンの VM 分析情報](./media/vminsights-health/vminsights-directvm-health.png)

**[Insights (プレビュー)]** ページには、VM とアラートのロールアップ正常性状態が表示されます。 この正常性状態は、重大度で分類されます。これは、基準に基づいて、正常性状態が正常から異常に変化したときに生成された VM 正常性アラートを表します。 **[VMs in critical condition] (重大な状態にある VM)** を選択すると、重大な正常性状態にある 1 つ以上の VM の一覧を含むページが開きます。

いずれかの VM の正常性状態を選択すると、その VM の **[正常性の診断]** ビューが表示されます。 このビューでは、正常性状態の問題がどの正常性基準に反映されているかを確認できます。 **[正常性の診断]** ページが開くと、すべての VM コンポーネントとそれらに関連付けられている正常性基準が、現在の正常性状態と共に表示されます。

詳細については、「[正常性の診断](#health-diagnostics)」を参照してください。

**[正常性のすべての条件を表示]** を選択すると、この機能で使用可能なすべての正常性基準の一覧を表示するページが開きます。 次のオプションに基づいて情報をさらにフィルター処理できます。

* **[種類]** 。 監視対象の VM の状態を評価し、全体的な正常性状態をロールアップするための正常性基準には次の 3 つの種類があります。
    - **[ユニット]** 。 VM のいくつかの側面を測定します。 この正常性基準の種類では、コンポーネントのパフォーマンスを判定するためにパフォーマンス カウンターを確認したり、代理トランザクションを実行するスクリプトを実行したり、エラーを示すイベントを監視したりする可能性があります。 フィルターは、既定ではユニットに設定されます。
    - **[依存関係]** 。 さまざまなエンティティ間の正常性ロールアップを提供します。 この正常性基準では、あるエンティティの正常性を、それが正常に動作するために依存している別の種類のエンティティの正常性に依存させることができます。
    - **[集計]** 。 同様の正常性基準の組み合わされた正常性状態を提供します。 ユニットと依存関係の正常性基準は通常、集計正常性基準のもとに構成されます。 あるエンティティを対象とする多くの異なる正常性基準のよりよい一般的編成を提供するだけでなく、集計正常性基準はエンティティのカテゴリごとに一意の正常性状態を提供します。

* **[カテゴリ]** 。 レポートの目的で同様の基準をグループ化するために使用される正常性基準の種類。 これらのカテゴリには、 **[可用性]** と **[パフォーマンス]** があります。

どのインスタンスが異常かを確認するには、 **[正常でないコンポーネント]** 列の値を選択します。 このページでは、重大な正常性状態にあるコンポーネントがテーブルに一覧表示されます。

## <a name="health-diagnostics"></a>正常性の診断

**[正常性の診断]** ページでは、VM の正常性モデルを視覚化できます。 このページには、すべての VM コンポーネント、関連付けられている正常性基準、状態の変化、およびその VM に関連した監視対象のコンポーネントによって識別されたその他の重要な問題が一覧表示されます。

![VM の [正常性の診断] ページの例](./media/vminsights-health/health-diagnostics-page-01.png)

正常性の診断は、次の方法を使用して開始されます。

* Azure Monitor の集計 VM 観点から、すべての VM のロールアップ正常性状態により。

    1. **[正常性]** ページで、 **[ゲスト VM の正常性]** セクションの **[重大]** 、 **[警告]** 、 **[正常]** 、または **[不明]** の正常性状態のアイコンを選択します。
    2. そのフィルター処理されたカテゴリに一致するすべての VM が一覧表示されたページに移動します。
    3. **[正常性の状態]** 列の値を選択すると、スコープがその VM に設定された正常性の診断が開きます。

* Azure Monitor の集計 VM 観点から OS により。 **[VM の分布]** でいずれかの列の値を選択すると、 **[仮想マシン]** ページが開き、フィルター処理されたカテゴリに一致する一覧がテーブルに表示されます。 **[正常性の状態]** 列の値を選択すると、選択された VM に対する正常性の診断が開きます。
 
* Azure Monitor for VMs の **[正常性]** タブでゲスト VM から **[正常性の診断を表示]** の選択により。

正常性の診断では、正常性情報が可用性とパフォーマンスの 2 つのカテゴリに整理されます。
 
あるコンポーネント (論理ディスクや CPU など) に対して定義されたすべての正常性基準を、この 2 つのカテゴリでフィルター処理することなく表示できます。 これらのビューは、すべての基準が含まれたビューであっても、 **[可用性]** または **[パフォーマンス]** を選択した場合のどちらかのカテゴリで結果をフィルター処理したものであってもかまいません。

また、基準のカテゴリを **[正常性の基準]** 列の横に表示することもできます。 基準が選択されたカテゴリに一致していない場合は、"**選択したカテゴリに使用できる正常性基準がありません**" というメッセージが **[正常性の基準]** 列に表示されます。

正常性基準の状態は、次の 4 つの種類のいずれかによって定義されます。 **[重大]** 、 **[警告]** 、 **[正常]** 、および **[不明]** 。 最初の 3 つは構成できます。つまり、 **[正常性の基準]** 構成ウィンドウで直接モニターのしきい値を変更できます。 また、Azure Monitor REST API の[モニターの更新操作](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)を使用することもできます。 **[不明]** は構成できず、特定のシナリオのために予約されています。

**[正常性の診断]** ページには、次の 3 つの主なセクションがあります。

* Component Model (コンポーネント モデル)
* 正常性の基準
* State Changes (状態の変化)

![[正常性の診断] ページのセクション](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>コンポーネント モデル

**[正常性の診断]** ページの左端の列は **[コンポーネント モデル]** です。 VM に関連付けられているすべてのコンポーネントが、その現在の正常性状態と共にこの列に表示されます。

次の例では、検出されたコンポーネントは**ディスク**、**論理ディスク**、**プロセッサ**、**メモリ**、および**オペレーティング システム**です。 これらのコンポーネントの複数のインスタンスが検出され、この列に表示されます。

たとえば、次の図は、VM に論理ディスクの 2 つのインスタンス **C:** と **D:** があり、それらが正常な状態にあることを示しています。

![[正常性の診断] に表示されたコンポーネント モデルの例](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>正常性の基準

[正常性の診断] ページの中央の列は **[正常性の基準]** です。 VM に対して定義されている正常性モデルが、階層ツリーに表示されます。 VM の正常性モデルは、ユニットおよび集計の正常性基準で構成されます。

![[正常性の診断] に表示された正常性の基準の例](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

正常性基準では、監視対象のインスタンスの正常性 (しきい値、エンティティの状態など) を測定します。 先に説明したように、正常性基準には、2 つまたは 3 つの構成可能な正常性状態のしきい値があります。 どの時点でも、正常性基準が取り得る状態はそれらのうちの 1 つだけです。

正常性モデルでは、ターゲット全体とターゲットの各コンポーネントの正常性を判定する基準を定義します。 基準の階層は、 **[正常性の診断]** ページの **[正常性の基準]** セクションに表示されます。

正常性ロールアップ ポリシーは、集計正常性基準の構成の一部です (既定値は **[worst-of] (最悪)** に設定されています)。 この機能の一部として実行されている既定の一連の正常性基準は、この記事の「[監視の構成の詳細](#monitoring-configuration-details)」セクションで見つけることができます。

また、Azure Monitor REST API の[リソースごとのモニター インスタンスの一覧](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource)を使用して、すべての正常性基準の一覧を取得することもできます。 この基準には、Azure VM リソースに対して実行されている構成の詳細が含まれます。

**[ユニット]** の正常性基準の種類では、右側にある省略記号のリンクを選択することによってその構成を変更できます。 **[詳細の表示]** を選択すると、構成ウィンドウが開きます。

![正常性基準の構成の例](./media/vminsights-health/health-diagnostics-vm-example-02.png)

選択された正常性基準の構成ウィンドウで、 **[ディスクの平均書き込み秒数]** の例を使用すると、しきい値を異なる数値で構成できます。 これは 2 つの状態のモニターです。つまり、 **[正常]** から **[警告]** にしか変化できません。

他の正常性基準では、3 つの状態を使用する場合があります。その場合は、警告と重大の正常性状態のしきい値の値を構成できます。 また、Azure Monitor REST API の[モニターの構成](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)を使用してしきい値を変更することもできます。

>[!NOTE]
>正常性基準の構成変更を 1 つのインスタンスに適用すると、それらは監視対象のすべてのインスタンスに適用されます。 たとえば、 **[Disk -1 D:] (ディスク - 1 D:)** を選択してから **[ディスクの平均書き込み秒数]** のしきい値を変更すると、その変更は VM 上で検出および監視されているすべてのインスタンスに適用されます。


![ユニット モニターの正常性基準の構成の例](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

正常性基準の詳細について学習したい場合は、問題、原因、および解決策を識別するために役立つナレッジ記事が用意されています。 関連するナレッジ記事を表示するには、このページの **[詳細の表示]** を選択します。

Azure Monitor for VMs の正常性に含まれているすべてのナレッジ記事を確認するには、[Azure Monitor の正常性に関する知識のドキュメント](https://docs.microsoft.com/azure/monitoring/infrastructure-health/)を参照してください。

### <a name="state-changes"></a>状態の変化

**[正常性の診断]** ページの右端の列は **[状態の変化]** です。 この列には、 **[正常性の基準]** セクションで選択された正常性基準に関連付けられているすべての状態の変化、またはテーブルの **[コンポーネント モデル]** または **[正常性の基準]** 列から VM が選択された場合はその VM の状態の変化が一覧表示されます。

![[正常性の診断] に表示された状態の変化の例](./media/vminsights-health/health-diagnostics-page-statechanges.png)

次のセクションでは、正常性基準の状態とそれに関連付けられた時間を示します。 これらの情報は、列の一番上にある最新の状態を示します。

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>[コンポーネント モデル]、[正常性の基準]、および [状態の変化] 列の関連付け

3 つの列は相互に関連しています。 **[コンポーネント モデル]** 列でインスタンスを選択すると、 **[正常性の基準]** 列がそのコンポーネント ビューにフィルター処理されます。 それに対応して、 **[状態の変化]** 列が、選択された正常性基準に基づいて更新されます。

![監視対象のインスタンスと結果の選択の例](./media/vminsights-health/health-diagnostics-vm-example-01.png)

たとえば、 **[コンポーネント モデル]** の一覧から *[Disk - 1 D:] (ディスク - 1 D:)* を選択すると、 **[正常性の基準]** が *[Disk - 1D:] (ディスク - 1D:)* にフィルター処理され、 **[状態の変化]** に *[Disk - 1 D:] (ディスク - 1 D:)* の可用性に基づいた状態の変化が表示されます。

更新された正常性状態を確認するには、 **[更新]** リンクを選択することによって [正常性の診断] ページを更新できます。 定義済みのポーリング間隔に基づいて、正常性基準の正常性状態に更新がある場合、このタスクにより待機を避けて最新の正常性状態を反映できます。 **[正常性基準の状態]** は、[正常]、[警告]、[重大]、[不明]、および [すべて] の選択された正常性状態に基づいて結果のスコープを設定できるフィルターです。 右上隅にある **[最終更新日時]** の時間は、[正常性の診断] ページが最後に更新された時間を表します。

## <a name="alerts"></a>アラート

Azure Monitor for VMs の正常性は、[Azure アラート](../../azure-monitor/platform/alerts-overview.md)と統合されています。 定義済みの基準に達した場合や、正常な状態から異常な状態への変化が検出された場合にアラートを発生させます。 アラートは、重大度 0 から重大度 4 までの重大度で分類され、重大度 0 が最も高いレベルです。

アラートは、トリガーされたらユーザーに通知するためのアクション グループには関連付けられていません。 サブスクリプション所有者は、「[アラートを構成する](#configure-alerts)」のセクションにある手順に従って通知を構成する必要があります。

重大度で分類された VM 正常性アラートの総数は、 **[アラート]** セクションの **[正常性]** ダッシュボードで表示できます。 アラートの合計数、または重大度レベルに対応する数のいずれかを選択すると、 **[アラート]** ページが開き、選択に一致するすべてのアラートが一覧表示されます。

たとえば、**重大度レベル 1** に対応する行を選択すると、次のビューが表示されます。

![重大度レベル 1 のすべてのアラートの例](./media/vminsights-health/vminsights-sev1-alerts-01.png)

**[すべてのアラート]** ページのスコープは、ユーザーの選択に一致するアラートだけを表示するようには設定されていません。 また、VM リソースによって生成された正常性アラートだけを表示するように **[リソースの種類]** でフィルター処理されます。 この形式は、 **[ターゲット リソース]** 列のアラートの一覧に反映されています。そこには、異常な状態が検出されたときにアラートが生成された Azure VM が表示されます。

他のリソースの種類またはサービスからのアラートは、このビューに含まれるようには考慮されていません。 これらのアラートには、通常は Azure Monitor の既定の [[すべてのアラート]](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) ページから表示するログ クエリまたはメトリック アラートに基づくログ アラートが含まれます。

ページの上部にあるドロップダウン メニューで値を選択することによって、このビューをフィルター処理できます。

|列 |説明 |
|-------|------------|
|サブスクリプション |Azure サブスクリプションを選択します。 このビューには、選択したサブスクリプション内のアラートのみが含まれます。 |
|リソース グループ |1 つのリソース グループを選択します。 このビューには、選択されたリソース グループ内のターゲットを含むアラートのみが含まれます。 |
|リソースの種類 |1 つ以上のリソースの種類を選択します。 既定では、ターゲットが**仮想マシン**のアラートのみが選択されて、このビューに表示されます。 この列は、リソース グループを指定した後でのみ使用できます。 |
|Resource |リソースを選択します。 このビューには、そのリソースをターゲットとして含むアラートのみが含まれます。 この列は、リソースの種類が指定された後でのみ使用できます。 |
|Severity |アラートの重大度を選択するか、または **[すべて]** を選択してすべての重大度のアラートを含めます。 |
|[Monitor Condition] (監視条件) |アラートをフィルター処理するための監視条件として、アラートが発生したかどうか、または状態がアクティブでなくなった場合はシステムによって解決されたかどうかを選択します。 または、 **[すべて]** を選択してすべての状態のアラートを含めます。 |
|アラートの状態 |アラートの状態として、 **[新規]** 、 **[確認済み]** 、 **[解決済み]** を選択するか、または **[すべて]** を選択してすべての状態のアラートを含めます。 |
|サービスの監視 |サービスを選択するか、または **[すべて]** を選択してすべてのサービスを含めます。 この機能では、VM Insights からのアラートのみがサポートされます。|
|時間範囲| このビューには、選択された時間枠内に発生したアラートのみが含まれます。 サポートされる値は、過去 1 時間、過去 24 時間、過去 7 日間、および過去 30 日間です。 |

アラートを選択すると、 **[アラートの詳細]** ページが表示されます。 このページにはアラートの詳細が表示され、その状態を変更できるようになります。

アラートの管理の詳細については、「[Azure Monitor を使用してアラートを作成、表示、管理する](../../azure-monitor/platform/alerts-metric.md)」をご覧ください。

>[!NOTE]
>ポータルからの正常性基準に基づいた新しいアラートの作成、または Azure Monitor の既存の正常性アラート ルールの変更は現在サポートされていません。


![選択したアラートの [アラートの詳細] ウィンドウ](./media/vminsights-health/alert-details-pane-01.png)

1 つまたは複数のアラートで、アラートの状態を変更するには、 **[すべてのアラート]** ページでそれらを選択してから、左上隅にある **[状態の変更]** を選択します。 **[アラートの状態の変更]** ウィンドウでいずれかの状態を選択し、 **[コメント]** フィールドに変更の説明を追加してから、 **[OK]** を選択して変更内容をコミットします。 情報が検証され、変更が適用されたら、メニューの **[通知]** で進行状況を追跡します。

### <a name="configure-alerts"></a>アラートを構成する
Azure portal から特定のアラート管理タスクを管理することはできません。 これらのタスクは、[Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components) を使用して実行する必要があります。 具体的には次の処理が行われます。

- 正常性基準のアラートを有効または無効にする
- 正常性基準アラートのための通知を設定する

各例では、Windows コンピューター上の [ARMClient](https://github.com/projectkudu/armclient) を使用します。 この方法に慣れていない場合は、「[ARMClient の使用](../platform/rest-api-walkthrough.md#use-armclient)」を参照してください。

#### <a name="enable-or-disable-an-alert-rule"></a>アラート ルールを有効または無効にする

特定の正常性基準のアラートを有効または無効にするには、プロパティ **alertGeneration** を **Disabled** または **Enabled** のどちらかの値に変更する必要があります。

特定の正常性基準の *monitorId* を識別するために、次の例は、基準 **LogicalDisk\Avg Disk Seconds Per Transfer** のその値にクエリを実行する方法を示しています。

1. ターミナル ウィンドウで、「 **armclient.exe login**」と入力します。 これにより Azure にサインインするよう求められます。

2. 次のコマンドを入力して、特定の VM 上でアクティブなすべての正常性基準を取得し、*monitorId* プロパティの値を識別します。

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    次の例は、*armclient GET* コマンドの出力を示しています。 *MonitorId* の値をメモします。 この値は次の手順に必要です。そこでは、正常性基準の ID を指定し、アラートを作成するためにそのプロパティを変更する必要があります。

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. 次のコマンドを入力して *alertGeneration* プロパティを変更します。

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. 手順 2. で使用された GET コマンドを入力して、このプロパティ値が **Disabled** に設定されていることを確認します。

#### <a name="associate-an-action-group-with-health-criteria"></a>アクション グループを正常性基準に関連付ける

Azure Monitor for VMs の正常性では、異常な正常性基準からアラートが生成されたときの SMS および電子メール通知がサポートされています。 通知を構成するには、SMS または電子メール通知を送信するように構成されたアクション グループの名前をメモする必要があります。

>[!NOTE]
>このアクションは、通知を受信するようにしたい監視対象の各 VM に対して実行する必要があります。 これは、リソース グループ内のすべての VM には適用されません。

1. ターミナル ウィンドウで、「*armclient.exe login*」と入力します。 これにより Azure にサインインするよう求められます。

2. 次のコマンドを入力して、アクション グループをアラート ルールに関連付けます。
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. プロパティ **actionGroupResourceIds** の値が正常に更新されたことを確認するには、次のコマンドを入力します。

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    出力は次の基準のようになります。
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>次の手順

- 制限および全体的な VM のパフォーマンスを識別するには、[Azure VM のパフォーマンスの表示](vminsights-performance.md)に関するページを参照してください。
- 検出されたアプリケーションの依存関係の詳細については、[Azure Monitor for VMs のマップの表示](vminsights-maps.md)に関するページを参照してください。
