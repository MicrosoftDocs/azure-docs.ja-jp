---
title: Azure Monitor for VMs (プレビュー) を使用して仮想マシンの正常性を監視する | Microsoft Docs
description: この記事では、VM の Azure Monitor を使用して仮想マシンと基になるオペレーティング システムの正常性を理解する方法について説明します。
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
ms.date: 04/12/2019
ms.author: magoedte
ms.openlocfilehash: f2a0d64da5a88e82c0ae1fd893af52f2070268f8
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549867"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Azure 仮想マシンの正常性を把握する

Azure には監視領域において特定の役割やタスクを個別に実行する複数のサービスが含まれますが、Azure 仮想マシン上でホストされているオペレーティング システムの詳細な正常性情報を提供することはできませんでした。 Azure Monitor を使用してさまざまな条件を監視することができたものの、仮想マシンのコア コンポーネントの正常性または全体的な正常性をモデル化して表現するようには設計されていませんでした。 VM の Azure Monitor 正常性機能では、主要なコンポーネントとその関係、それらのコンポーネントの正常性を測定する方法を指定する基準、および異常な状態が検出されたときのアラートを表すモデルを使用して、Windows または Linux ゲスト OS の可用性とパフォーマンスが事前に監視されます。  

Azure VM と基になるオペレーティング システムの全体的な正常性状態は、VM の Azure Monitorm の正常性では、仮想マシンからの直接的な観点と、Azure Monitor からのリソース グループ内の全 VM に対する観点という 2 つの観点で観察できます。

この記事は、検出された正常性の問題をすばやく評価、調査、および解決する方法を理解するのに役立ちます。

Azure Monitor for VMs の構成については、[Azure Monitor for VMs の有効化](vminsights-onboard.md)に関する記事をご覧ください。

## <a name="monitoring-configuration-details"></a>監視の構成の詳細

このセクションでは、Azure の Windows および Linux 仮想マシンを監視するために定義されている既定の正常性基準について説明します。 すべての正常性条件は、非正常状態になったら通知するように事前に構成されています。 

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

[Azure Portal](https://portal.azure.com) にサインインします。 

## <a name="introduction-to-health-experience"></a>正常性エクスペリエンスの概要

単一の仮想マシンまたは仮想マシンのグループの正常性機能の使用について説明する前に、情報の表示方法および視覚エフェクトで表現されているものの概要を説明します。  

## <a name="view-health-directly-from-a-virtual-machine"></a>仮想マシンから正常性を直接表示する 

Azure VM の正常性を表示するには、仮想マシンの左側のウィンドウから **[Insights (プレビュー)]** を選択します。 VM の分析情報のページでは、**[Health]\(正常性\)** が既定で開いて、VM の正常性ビューが示されます。  

![選択した Azure 仮想マシン正常性の概要が表示されている VM の Azure Monitor](./media/vminsights-health/vminsights-directvm-health.png)

**[Health]\(正常性\)** タブの **[ゲスト VM の正常性]** セクションのテーブルには、仮想マシンの現在の正常性状態と、異常なコンポーネントによって生成された VM 正常性アラートの合計数が示されます。 詳細については、アラート エクスペリエンスのその他の詳細に関する「[アラート](#alerts)」を参照してください。  

VM に対して定義されている正常性状態の説明をまとめたものが次の表です。 

|アイコン |正常性の状態 |意味 |
|-----|-------------|------------|
| |Healthy |定義されている正常性基準の範囲内にある場合、正常性の状態は正常であり、その VM に関して問題は検出されておらず、要求されたとおりに機能していることを示します。 親ロールアップ モニターの場合は、正常性はロールアップされて、子の最善または最悪の状態を反映します。|
| |重大 |定義されている正常性基準の範囲内にない場合、正常性の状態は重大です。これは、1 つまたは複数の重大な問題が検出されており、正常な機能に戻すには処置が必要であることを示します。 親ロールアップ モニターでは、正常性はロールアップされて、子の最善または最悪の状態を反映します。|
| |警告 |定義されている正常性基準の 2 つのしきい値 (1 つは "*警告*" 状態を示し、もう 1 つは "*重大*" 状態を示します) の間にある場合、正常性の状態は警告です (正常性状態の 3 つのしきい値を構成できます)。また、重大な問題の原因となる可能性がある重大ではない問題が検出された場合も、正常性の状態は警告になります (その問題が解決されていない場合)。 親ロールアップ モニターでは、1 つ以上の子が警告状態の場合、親は "*警告*" 状態を反映します。 "*重大*" 状態の子と "*警告*" 状態の子がある場合は、親のロールアップの正常性状態は "*重大*" を示します。|
| |Unknown |データを収集できない、サービスが初期化されていないなど、何らかの理由により正常性の状態を計算できない場合、正常性状態は "*不明*" 状態になります。この正常性状態は構成できません。| 

**[正常性の診断を表示]** を選択すると、VM のすべてのコンポーネント、関連付けられている正常性基準、状態の変化、および VM に関連したコンポーネントの監視によって発生したその他の重要な問題を示すページが開きます。 詳細については、「[正常性の診断](#health-diagnostics)」を参照してください。 

**[コンポーネントの正常性]** セクションの表には、**CPU**、**メモリ**、**ディスク**、**ネットワーク** の各領域について正常性基準によって監視されている主要なパフォーマンス カテゴリの正常性ロールアップ状態が示されます。  コンポーネントのいずれかを選択すると開くページには、そのコンポーネントのすべての個別正常性条件監視側面と、それぞれの正常性状態が一覧表示されます。  

Windows オペレーティング システムを実行する Azure VM から正常性にアクセスすると、上位 5 つのコア Windows サービスの正常性状態が **[コア サービスの正常性]** セクションに表示されます。  いずれかのサービスを選択すると、そのコンポーネントとその正常性状態を監視する正常性基準が一覧表示されます。  正常性基準の名前をクリックするとプロパティ ウィンドウを開き、そこからは、正常性基準に対応する Azure Monitor アラートが定義されているかどうかなど、構成の詳細を確認できます。 詳細については、[正常性の診断と正常性基準の使用](#health-diagnostics)に関するページを参照してください。  

## <a name="aggregate-virtual-machine-perspective"></a>仮想マシンの観点を集約する

リソース グループ内のすべての仮想マシンの正常性のコレクションを表示するには、ポータルのナビゲーション リストから、**[モニター]** を選択し、**[Virtual Machines (プレビュー)]** を選択します。  

![Azure Monitor からの VM 分析情報監視ビュー](./media/vminsights-health/vminsights-aggregate-health.png)

報告された正常性状態を表示するには、**[サブスクリプション]** および **[リソース グループ]** ドロップダウン リストから、グループに関連する VM を含む適切なリソース グループを選択します。  この選択が適用されるのは、正常性機能のみで、パフォーマンスやマップには引き継がれません。

**[状態]** タブでは次のことがわかります。

* 重大または異常な状態の VM の数と、正常な VM またはデータを送信していない VM (不明状態と呼ばれます) の数。
* オペレーティング システム (OS) 別の、異常状態を報告している VM とその数。
* プロセッサ、ディスク、メモリ、ネットワーク アダプターで検出された問題のために異常な VM の数 (正常性状態によって分類)。  
* コア オペレーティング システム サービスで検出された問題のために異常な VM の数 (正常性状態によって分類)。

ここでは、予防的に VM を監視している正常性基準によって検出された上位の重大な問題をすばやく識別し、VM の正常性アラートの詳細および問題の診断と修復に役立つ関連ナレッジ記事を確認できます。  任意の重大度を選択すると、その重大度でフィルター処理された [[すべてのアラート]](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) ページが開きます。

**[オペレーティング システムごとの VM の分布]** の一覧には、Windows のエディションまたは Linux のディストリビューションおよびバージョンごとに VM が一覧表示されます。 オペレーティング システムの各カテゴリでは、VM はさらに VM の正常性に基づいて分類されます。 

![VM の分析情報: 仮想マシンの分布観点](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

**[VM 数]**、**[重大]**、**[警告]**、**[正常]**、**[不明]** の各列をクリックして **[仮想マシン]** ページにドリルダウンし、選択した列に一致するフィルター選択された結果の一覧を見ることができます。 たとえば、**Red Hat Enterprise Linux リリース 7.5** を実行しているすべての VM を確認したい場合は、その OS の **[VM 数]** の値をクリックすると、次のページが開き、そのフィルターに一致する仮想マシンおよび現在の既知の正常性状態が一覧表示されます。  

![Red Hat Linux VM のロールアップの例](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
**[仮想マシン]** ページで、**[VM 名]** 列の VM の名前を選択すると、VM インスタンスのページに移動し、選択した VM に影響する識別されたアラートと正常性基準の問題の詳細が表示されます。  ここから、ページの左上隅にある **[正常性の状態]** アイコンをクリックすることによって正常性の状態の詳細をフィルター処理してどのコンポーネントが異常であるかを確認したり、異常なコンポーネントによって生成された VM 正常性アラートをアラートの重大度で分類して表示したりできます。    

VM リスト ビューで VM の名前をクリックすると、VM から直接 **[Insights (プレビュー)]** を選択した場合と同じように、選択した VM の **[正常性]** ページが開きます。

![選択した Azure 仮想マシンの VM 分析情報](./media/vminsights-health/vminsights-directvm-health.png)

ここでは、仮想マシンの**正常性状態**のロールアップと重大度別の**アラート**が表示されます。これは、正常性基準の正常性状態が正常から異常に変化したときに生成される VM 正常性アラートを表します。  **[VMs in critical condition]\(重大状態の VM\)** を選択すると、重大な正常性状態になっている VM の一覧のページが開きます。  リスト内の VM のいずれかの正常性状態をクリックすると、VM の **[正常性の診断]** ビューが表示されます。  ここでは、正常性状態の問題を反映している正常性の基準を確認できます。 **[正常性の診断]** ページが開くと、VM のすべてのコンポーネントおよびそれらに関連付けられている正常性基準と現在の正常性状態が示されます。 詳細については、「[正常性の診断](#health-diagnostics)」を参照してください。  

**[正常性のすべての条件を表示]** を選択すると、この機能で使用可能なすべての正常性基準の一覧を表示するページが開きます。  次のオプションに基づいて情報をさらにフィルター処理できます。

* **[種類]** – 監視対象の VM の状態を評価して全体的な正常性状態をロールアップするため、正常性基準には 3 つの種類があります。  
    a. **[Unit]\(ユニット\)** – 仮想マシンのいくつかの側面を測定します。 この正常性基準の種類は、パフォーマンス カウンターを調べてコンポーネントのパフォーマンスを確認したり、スクリプトを実行して代理トランザクションを実行したり、エラーを示すイベントを監視したりします。  既定では、フィルターはユニットに設定されます。  
    b. **[依存関係]** - さまざまなエンティティ間の正常性のロールアップを提供します。 この正常性基準では、エンティティの正常性を、その正常な動作が依存している別の種類のエンティティの正常性に依存させることができます。  
    c. **[集計]** - 似た正常性基準の正常性状態の組み合わせを提供します。 ユニットと依存関係の正常性基準は、通常、集計正常性基準の下で構成されます。 あるエンティティを対象とする多くの異なる正常性基準のよりよい一般的編成を提供するだけでなく、集計正常性基準はエンティティのカテゴリごとに一意の正常性状態を提供します。

* **[カテゴリ]** - レポートの目的で同じような種類の基準をグループ化するために使用する正常性基準の種類です。  **[可用性]** と **[パフォーマンス]** があります。

**[正常でないコンポーネント]** 列の値をクリックすることにより、さらにドリルダウンして、異常なインスタンスを確認できます。  そのページのテーブルには、重大の正常性状態にあるコンポーネントが一覧表示されます。    

## <a name="health-diagnostics"></a>正常性の診断

**[正常性の診断]** ページでは、VM のすべてのコンポーネント、関連する正常性基準、状態の変化、および監視コンポーネントによって検出された VM に関連するその他の重要な問題を一覧表示することで、VM の正常性モデルを可視化できます。

![VM の [正常性の診断] ページの例](./media/vminsights-health/health-diagnostics-page-01.png)

正常性の診断は次の方法で開始できます。

* Azure Monitor の集計 VM 観点からのすべての VM に対するロールアップ正常性状態で。  **[正常性]** ページで、**[ゲスト VM の正常性]** セクションの下の **[重大]**、**[警告]**、**[正常]**、**[不明]** アイコンをクリックして、そのフィルター処理されたカテゴリと一致するすべての VM が一覧表示されるページにドリルダウンします。  **[正常性の状態]** 列の値をクリックすると、その特定の VM を対象とする正常性の診断が開きます。      

* Azure Monitor の集計 VM 観点からのオペレーティング システムで。 **[VM の分布]** でいずれかの列の値を選択すると、**[仮想マシン]** ページが開き、フィルター処理されたカテゴリに一致する一覧がテーブルに表示されます。  **[正常性の状態]** 列の値をクリックすると、選択した VM に対する正常性の診断が開きます。    
 
* Azure Monitor で VM の **[正常性]** タブに対するゲスト VM から、**[正常性の診断を表示]** を選択して 

正常性の診断では、正常性の情報が次のカテゴリにまとめられています。 

* 可用性
* パフォーマンス
 
論理ディスク、CPU などの特定のコンポーネントに定義されたすべての正常性基準は、ページ上で **[可用性]** または **[パフォーマンス]** オプションを選択するときに、2 つのカテゴリ (つまり、すべての基準をまとめたビュー) でフィルター処理せずに表示するか、いずれかのカテゴリで結果をフィルター処理することができます。 また、**[正常性の基準]** 列の基準の横で基準のカテゴリを確認できます。 基準が選択したカテゴリと一致しない場合は、**[正常性の基準]** 列に "**選択したカテゴリに使用できる正常性基準がありません**" というメッセージが表示されます。  

正常性基準の状態は、4 つの状態 (*[重大]*、*[警告]*、*[正常]*、および *[不明]*) のいずれかで定義されます。 最初の 3 つは構成可能です。つまり、正常性基準の構成ウィンドウから、または Azure Monitor REST API の[update monitor 操作](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)を使用して、モニターのしきい値を直接変更することができます。 *[不明]* は構成不可であり、特定のシナリオのために予約されています。  

[正常性の診断] ページには、次の 3 つの主要なセクションがあります。

* Component Model (コンポーネント モデル) 
* 正常性の基準
* State Changes (状態の変化) 

![[正常性の診断] ページのセクション](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>コンポーネント モデル

[正常性の診断] ページの一番左の列は、コンポーネント モデルです。 VM に関連付けられているすべてのコンポーネントが、それらの現在の正常性状態とともに、この列に表示されます。 

次の例では、検出されたコンポーネントはディスク、論理ディスク、プロセッサ、メモリ、およびオペレーティング システムです。 これらのコンポーネントの複数のインスタンスが検出され、この列に表示されます。 たとえば、次の図では、VM に論理ディスクの 2 つのインスタンス、C: と D: があり、どちらも正常状態です。  

![[正常性の診断] に表示されたコンポーネント モデルの例](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>正常性の基準

[正常性の診断] ページの中央の列は、**[正常性の基準]** 列です。 VM に対して定義されている正常性モデルが、階層ツリーに表示されます。 VM の正常性モデルは、ユニットおよび集計の正常性基準で構成されます。  

![[正常性の診断] に表示された正常性の基準の例](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

正常性基準により、何らかの基準 (しきい値、エンティティの状態など) で監視対象インスタンスの正常性が測定されます。前述したように、正常性基準には、2 つまたは 3 つの構成可能な正常性状態のしきい値があります。 特定の時点において、正常性の基準は取り得る状態のいずれか 1 つだけになることができます。 

ターゲットの全体的な正常性は、正常性モデルで定義されている各正常性基準の正常性によって決定されます。 これは、ターゲットを直接対象とする正常性基準と、集約正常性基準によってターゲットにロールアップするコンポーネントで対象になっている正常性基準を、組み合わせたものです。 この階層は、[正常性の診断] ページの **[正常性の基準]** セクションで示されます。 正常性ロールアップ ポリシーは、集約正常性基準の構成の一部です (既定では *[Worst-of]\(最低\)* に設定されています)。 「[監視の構成の詳細](#monitoring-configuration-details)」セクションでこの機能の一部として実行されている既定の正常性基準セットの一覧を取得できます。さらに、Azure Monitor REST API の [monitor instances - list by resource 操作](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource)を使用すると、Azure VM リソースに対して実行されている正常性基準とその詳細な構成のすべての一覧を取得できます。  

正常性基準の種類 "**ユニット**" は、右端の省略記号リンクをクリックして **[詳細の表示]** を選択して構成ウィンドウを開くことにより、構成を変更できます。 

![正常性基準の構成の例](./media/vminsights-health/health-diagnostics-vm-example-02.png)

選択した正常性基準の構成ウィンドウで、例 **[Average Disk Seconds Per Write]/(ディスクの平均書き込み秒数/)** を使用して、そのしきい値として異なる数値を構成できます。 これは 2 状態の監視です。つまり、正常から警告への変化のみ監視されます。 他の正常性基準は 3 状態の場合があり、警告と重大の正常性状態のしきい値の値を構成できます。 Azure Monitor REST API の [update monitor 操作](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)を使用して、しきい値を変更することもできます。

>[!NOTE]
>1 つのインスタンスに正常性基準の構成の変更を適用すると、すべての監視対象インスタンスに適用されます。  たとえば、**[Disk -1 D:]/(ディスク - 1 D:/)** を選択し、**[Average Disk Seconds Per Write]/(ディスクの平均書き込み秒数/)** のしきい値を変更すると、この変更は、そのインスタンスに適用されるだけでなく、VM で検出されて監視されている他のすべてのディスク インスタンスにも適用されます。
>

![ユニット モニターの正常性基準の構成の例](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

正常性インジケーターの詳細が必要な場合は、問題、原因、および解決策を識別するために役立つナレッジ記事が含まれています。 このページにある **[View information] (情報の表示)** リンクをクリックすると、特定のナレッジ記事を示す新しいタブがブラウザーで開きます。 VM 用 Azure Monitor の正常性機能に含まれている正常性基準に関するナレッジ記事はすべて、[ここ](https://docs.microsoft.com/azure/monitoring/infrastructure-health/)でいつでも確認できます。
  
### <a name="state-changes"></a>状態の変化

[正常性の診断] ページの右端の列は **[State Changes]\(状態の変化\)** です。 この列には、**[正常性の基準]** セクションで選択されている正常性基準に関連付けられているすべての状態変化、またはテーブルの **[Component Model]\(コンポーネント モデル\)** 列または **[正常性の基準]** 列で VM が選択されている場合は VM の状態変化が、一覧表示されます。 

![[正常性の診断] に表示された状態の変化の例](./media/vminsights-health/health-diagnostics-page-statechanges.png)

このセクションは、正常性基準の状態とそれに関連する時刻で構成され、時刻の降順に並んでいます。   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>コンポーネント モデル、正常性基準、状態変化の列の関連 

3 つの列は相互に関連しています。 **[コンポーネント モデル]** セクションで検出されたインスタンスを選択すると、**[正常性の基準]** セクションはそのコンポーネント ビューでフィルター処理され、それに対応して **[State Changes]\(状態の変化\)** セクションも、選択された正常性基準に基づいて更新されます。 

![監視対象のインスタンスと結果の選択の例](./media/vminsights-health/health-diagnostics-vm-example-01.png)

上の例で、**[Disk - 1 D:]/(ディスク - 1 D:/)** を選択すると、[正常性基準] ツリーが **[Disk - 1 D:]/(ディスク - 1 D:/)** でフィルター処理されます。 **[State Changes]\(状態の変化\)** 列には、**[Disk - 1 D:]/(ディスク - 1 D:/)** の可用性に基づく状態変化が示されます。 

更新された正常性状態を表示するには、**[最新の情報に更新]** リンクをクリックして [正常性の診断] ページを更新します。  定義済みのポーリング間隔に基づいて、正常性基準の正常性状態に更新がある場合、このタスクにより待機を避けて最新の正常性状態を反映できます。  **[正常性基準の状態]** はフィルターであり、選択されている正常性の状態 (*[正常]*、*[警告]*、*[重大]*、*[不明]*、および *[すべて]*) に基づいて結果を絞り込むことができます。  右上隅の **[最終更新日時]** は、[正常性の診断] ページが最後に更新された日時を表します。  

## <a name="alerts"></a>アラート

VM の Azure Monitor 正常性機能は [Azure アラート](../../azure-monitor/platform/alerts-overview.md)と統合し、状態が検出されて定義済みの正常性基準が正常から異常状態に変化すると、アラートを生成します。 アラートは重大度 0 ～ 4 で分類され、重要度 0 が最も高い重大度レベルを表します。 

アラートはアクション グループに関連付けられていないので、アラートがトリガーされても通知は届きません。 サブスクリプションの所有者は、[このセクションで後述する](#configure-alerts)手順に従って通知を構成する必要があります。   

重大度によって分類された VM 正常性アラートの合計数は、**[アラート]** セクションの **[正常性]** ダッシュボードで確認できます。 アラートの合計数、または重大度レベルに対応する数のいずれかを選択すると、**[アラート]** ページが開き、選択に一致するすべてのアラートが一覧表示されます。  たとえば、**重大度レベル 1** に対応する行を選択した場合、次のようなビューが表示されます。

![重大度レベル 1 のすべてのアラートの例](./media/vminsights-health/vminsights-sev1-alerts-01.png)

**[アラート]** ページでは、選択内容に一致するアラートを表示するようにスコープされるだけではなく、仮想マシン リソースによって生成された正常性アラートだけを表示するように **[リソースの種類]** によってもフィルターされます。  これは、**[ターゲット リソース]** 列の下のアラートの一覧に反映されます。そして、特定の正常性基準の異常条件が満たされたときにアラートが生成された Azure VM が表示されます。  

他のリソースの種類またはサービスからのアラートは、このビューに表示されることは意図されていません。たとえば、ログ クエリに基づくログ アラートや、通常は既定の Azure Monitor の [[すべてのアラート]](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) ページに表示されるメトリック アラートなどです。 

ページの上部にあるドロップダウン メニューで値を選択することにより、このビューをフィルター処理できます。

|列 |説明 | 
|-------|------------| 
|サブスクリプション |Azure サブスクリプションを選択します。 このビューには、選択したサブスクリプション内のアラートのみが含まれます。 | 
|リソース グループ |1 つのリソース グループを選択します。 このビューには、選択されたリソース グループ内のターゲットを含むアラートのみが含まれます。 | 
|リソースの種類 |1 つ以上のリソースの種類を選択します。 既定では、ターゲットが**仮想マシン**のアラートのみが選択されて、このビューに表示されます。 この列は、リソース グループを指定した後でのみ使用できます。 | 
|Resource |リソースを選択します。 このビューには、そのリソースをターゲットとして含むアラートのみが含まれます。 この列は、リソースの種類を指定した後でのみ使用できます。 | 
|Severity |アラートの重大度を選択するか、または *[すべて]* を選択してすべての重大度のアラートを含めます。 | 
|[Monitor Condition] (監視条件) |アラートをフィルター処理する監視状態として、システムによるアラートの "*生成*" または状態がアクティブではなくなった場合のシステムによる "*解決*" を選択します。 または、"*すべて*" を選択してすべての状態のアラートを含めます。 | 
|アラートの状態 |アラートの状態を *[新規]*、*[確認済み]*、*[解決済み]* から選択するか、または *[すべて]* を選択してすべての状態のアラートを含めます。 | 
|サービスの監視 |サービスを選択するか、または *[すべて]* を選択してすべてのサービスを含めます。 この機能では *VM Insights* からのアラートのみがサポートされます。| 
|時間範囲| このビューには、選択された時間枠内に発生したアラートのみが含まれます。 サポートされる値は、過去 1 時間、過去 24 時間、過去 7 日間、および過去 30 日間です。 | 

アラートを選択すると **[アラートの詳細]** ページが表示され、アラートの詳細が提供されて、その状態を変更することができます。 アラートの管理の詳細については、「[Azure Monitor を使用してアラートを作成、表示、管理する](../../azure-monitor/platform/alerts-metric.md)」をご覧ください。  

>[!NOTE]
>現時点では、ポータルからの、正常性基準に基づく新しいアラートの作成、または Azure Monitor の既存の正常性アラート ルールの変更は、サポートされていません。  
>

![選択したアラートの [アラートの詳細] ウィンドウ](./media/vminsights-health/alert-details-pane-01.png)

**[すべてのアラート]** ページでアラートを選択してから左上隅の **[状態の変更]** を選択することで、1 つまたは複数のアラートの状態を変更することもできます。 **[アラートの状態の変更]** ページで状態のいずれかを選択して、**[コメント]** フィールドに変更の説明を追加し、**[OK]** をクリックして変更をコミットします。 情報が検証され、変更が適用されている間、メニューの **[通知]** でその進行状況を追跡することができます。  

### <a name="configure-alerts"></a>アラートを構成する
一部のアラート管理タスクは Azure portal から管理できないため、[Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components) を使用して実行する必要があります。 具体的には次の処理が行われます。

- 正常性基準のアラートを有効または無効にする 
- 正常性基準アラートの通知を設定する 

各例で使用されているアプローチは、お使いの Windows マシンで [ARMClient](https://github.com/projectkudu/armclient) を使用しています。 この方法に慣れていない場合は、「[ARMClient の使用](../platform/rest-api-walkthrough.md#use-armclient)」を参照してください。  

#### <a name="enable-or-disable-alert-rule"></a>アラート ルールを有効または無効にする

特定の正常性基準のアラートを有効または無効にするには、正常性基準のプロパティ *alertGeneration* を **Disabled** または **Enabled** のいずれかの値に変更する必要があります。 特定の正常性基準の *monitorId* を識別するために、次の例では、基準の **LogicalDisk\Avg Disk Seconds Per Transfer** に対してその値をクエリする方法を示します。

1. ターミナル ウィンドウで、「 **armclient.exe login**」と入力します。 これにより Azure にサインインするよう求められます。

2. 次のコマンドを入力して特定の仮想マシン上でアクティブなすべての正常性基準を取得し、*monitorId* プロパティの値を識別します。 

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    次の例は、そのコマンドの出力を示します。 *MonitorId* の値をメモします。 この値は、次の手順で必要になります。次の手順では、正常性基準の ID を指定し、そのプロパティを変更してアラートを作成する必要があります。

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

4. 手順 2 で使用した GET コマンドを入力して、プロパティの値が **Disabled** に設定されていることを確認します。  

#### <a name="associate-action-group-with-health-criteria"></a>アクション グループを正常性基準と関連付ける

Azure Monitor for VMs Health は、正常性基準が正常ではない状態になってアラートが生成されるときの SMS とメール通知をサポートします。 通知を構成するには、SMS またはメール通知を送信するように構成されているアクション グループの名前をメモする必要があります。 

>[!NOTE]
>このアクションは、通知を受信する監視対象の各 VM に対して実行する必要があります。

1. ターミナル ウィンドウで、「 **armclient.exe login**」と入力します。 これにより Azure にサインインするよう求められます。

2. 次のコマンドを入力して、アクション グループをアラート ルールに関連付けます。
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}" 
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. プロパティ **actionGroupResourceIds** の値が正常に更新されたことを確認するには、次のコマンドを入力します。

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    出力は次のようになります。
    
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

VM のパフォーマンスのボトルネックや全体的な使用率を識別するには、[Azure VM のパフォーマンスの表示](vminsights-performance.md)に関するページを参照してください。または、検出されたアプリケーションの依存関係を表示するには、[VM 用 Azure Monitor のマップの表示](vminsights-maps.md)に関するページを参照してください。 
