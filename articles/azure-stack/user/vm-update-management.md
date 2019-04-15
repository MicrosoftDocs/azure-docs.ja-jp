---
title: Azure Stack での VM の更新と管理 | Microsoft Docs
description: Azure Automation の Azure Monitor for VMs、Update Management、Change Tracking、および Inventory ソリューションを使用して、Azure Stack にデプロイされている Windows および Linux の VM を管理する方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: cb8258c0f837d0e70ba87a26246f055b0efe5c00
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316145"
---
# <a name="azure-stack-vm-update-and-management"></a>Azure Stack の VM の更新と管理
以下の Azure Automation ソリューション機能を使用すると、Azure Stack を使用してデプロイされている Windows および Linux の VM を管理できます。

- **[Update Management](https://docs.microsoft.com/azure/automation/automation-update-management)**. Update Management ソリューションでは、すべてのエージェント コンピューターで利用可能な更新プログラムの状態をすばやく評価し、Windows および Linux の VM に必要な更新プログラムをインストールするプロセスを管理できます。

- **[Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking)**. 監視対象サーバーにインストールされているソフトウェア、Windows サービス、Windows レジストリとファイル、および Linux デーモンの変更が、クラウドの Azure Monitor サービスに送信され、処理されます。 受信したデータにロジックが適用され、クラウド サービスによってそのデータが記録されます。 [変更の追跡] ダッシュボードの情報を使用して、サーバー インフラストラクチャで行われた変更を簡単に確認できます。

- **[Inventory](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**. Azure Stack の仮想マシンを追跡する Inventory では、インベントリ コレクションを設定して構成するためのブラウザー ベースのユーザー インターフェイスが提供されます。

- **[Azure Monitor for VMs](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)**。 Azure Monitor for VMs では、Azure と Azure Stack の仮想マシン (VM) および仮想マシン スケール セットの大規模な監視が行われます。 これにより、ご利用の Windows VM および Linux VM のプロセスや、その他のリソースおよび外部プロセスとの依存関係を監視することにより、それらの VM のパフォーマンスおよび正常性が分析されます。 

> [!IMPORTANT]
> これらのソリューションは、Azure VM の管理に使用されるものと同じです。 Azure と Azure Stack の VM はどちらも、同じインターフェイスから同じツールを使用して同じ方法で管理されます。 また、Azure Stack で Update Management、Change Tracking、Inventory、Azure Monitor Virtual Machines ソリューションを使用すると、Azure Stack VM の課金も Azure VM と同じです。

## <a name="prerequisites"></a>前提条件
これらの機能を使用して Azure Stack の VM を更新および管理するには、事前にいくつかの前提条件を満たす必要があります。 これらには、Azure portal および Azure Stack 管理ポータルで実行する必要のある手順が含まれます。

### <a name="in-the-azure-portal"></a>Azure ポータルで次の操作を行います。
Azure Stack の VM に対して Azure Monitor for VMs、Inventory、Change Tracking、Update Management の Azure Automation 機能を使用するには、まず、Azure でこれらのソリューションを有効にする必要があります。

> [!TIP]
> Azure VM に対してこれらの機能を既に有効にしてある場合は、Log Analytics ワークスペースの既存の資格情報を使用できます。 使用する Log Analytics のワークスペース ID とプライマリ キーが既にある場合は、[次のセクション](./vm-update-management.md#in-the-azure-stack-administration-portal)に進んでください。 それ以外の場合は、引き続きこのセクションで新しい Log Analytics ワークスペースと Automation アカウントを作成します。

これらのソリューションを有効にする最初のステップは、Azure サブスクリプションで [Log Analytics ワークスペースを作成する](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)ことです。 Log Analytics ワークスペースは、独自のデータ リポジトリ、データ ソース、ソリューションを備えた固有の Azure Monitor ログ環境です。 ワークスペースを作成した後、ワークスペース ID とキーを書き留めておいてください。 この情報は、ワークスペース ブレードに移動し、**[詳細設定]** をクリックして、**[ワークスペース ID]** と **[プライマリ キー]** で確認できます。 

次に、[Automation アカウントを作成する](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)必要があります。 Automation アカウントは、Azure Automation リソースのコンテナーです。 お使いの環境を分離したり、Automation のワークフローとリソースをさらに整理したりする手段を提供します。 Automation アカウントを作成した後は、Inventory、Change Tracking、Update Management の各機能を有効にする必要があります。 そのためには、以下の手順のようにします。

1. Azure portal で、使用する Automation アカウントに移動します。

2. 有効にするソリューションを選択します (**[インベントリ]**、**[変更の追跡]**、または **[更新の管理]**)。

3. **[ワークスペースの選択]** ドロップダウン リストで、使用する Log Analytics ワークスペースを選択します。

4. 残りのすべての情報が正しいことを確認し、**[有効化]** をクリックしてソリューションを有効にします。

5. 手順 2 ～ 4 を繰り返して、3 つのソリューションをすべて有効にします。 

   [![](media/vm-update-management/1-sm.PNG "Automation アカウントの機能を有効にする")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="enable-azure-monitor-for-vms"></a>Azure Monitor for VMs の有効化

Azure Monitor for VMs では、ご利用の Azure 仮想マシン (VM) および仮想マシン スケール セットの大規模な監視が行われます。 これにより、ご利用の Windows VM および Linux VM のプロセスや、その他のリソースおよび外部プロセスとの依存関係を監視することにより、それらの VM のパフォーマンスおよび正常性が分析されます。

ソリューションとして、Azure Monitor for VMs では、オンプレミスまたは別のクラウド プロバイダーでホストされている VM を対象にしたパフォーマンスおよびアプリケーションの依存関係の監視がサポートされています。 次の 3 つの主要な機能により、詳細な分析情報が提供されます。

1. Windows および Linux が稼働している Azure VM の論理コンポーネント:事前に構成された正常性基準と照らし合わせて測定され、評価条件が満たされると通知が届きます。 

2. 事前に定義された、傾向を示すパフォーマンス グラフ:ゲスト VM オペレーティング システムから主要なパフォーマンス メトリックが表示されます。

3. 依存関係マップ:さまざまなリソース グループおよびサブスクリプションから、該当する VM と相互接続されているコンポーネントが表示されます。

Log Analytics ワークスペースが作成されたら、Linux および Windows VM 上での収集のためにワークスペース内のパフォーマンス カウンターを有効にし、ワークスペースに ServiceMap および InfrastructureInsights ソリューションをインストールして有効にする必要があります。 このプロセスについて詳しくは、「[Azure Monitor for VMs をデプロイする](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-onboard#deploy-azure-monitor-for-vms)」をご覧ください。

### <a name="in-the-azure-stack-administration-portal"></a>Azure Stack 管理者ポータルで
Azure portal で Azure Automation のソリューションを有効にしたら、次に、クラウド管理者として Azure Stack 管理ポータルにサインインし、**Azure Monitor、更新および構成管理**および **Linux 用 Azure Monitor、更新および構成管理**という拡張機能の Azure Stack マーケットプレース項目をダウンロードします。 

   ![[Azure Monitor, Update and Configuration Management]\(Azure Monitor、更新および構成管理\) 拡張機能マーケットプレース項目](media/vm-update-management/2.PNG) 

Azure Monitor for VMs マップ ソリューションを有効にして、ネットワークの依存関係の分析情報を取得するには、**Azure Monitor Dependency Agent** もダウンロードする必要があります。

   ![Azure Monitor Dependency Agent](media/vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>Update Management を Azure Stack 仮想マシンに対して有効にする
以下の手順のようにして、Azure Stack VM の更新管理を有効にします。

1. Azure Stack ユーザー ポータルにログインします。

2. Azure Stack ユーザー ポータルで、ソリューションを有効にする仮想マシンの [拡張機能] ブレードに移動して、**[+ 追加]** をクリックし、**[Azure Update and Configuration Management]\(Azure 更新および構成管理\)** 拡張機能を選択して、**[作成]** をクリックします。

   [![](media/vm-update-management/3-sm.PNG "VM 拡張機能ブレード")](media/vm-update-management/3-lg.PNG#lightbox)

3. 前に作成したワークスペース ID とプライマリ キーを入力して、エージェントと Log Analytics ワークスペースをリンクし、**[OK]** をクリックして拡張機能をデプロイします。

   [![](media/vm-update-management/4-sm.PNG "ワークスペース ID とキーの指定")](media/vm-update-management/4-lg.PNG#lightbox) 

4. [Automation Update Management のドキュメント](https://docs.microsoft.com/azure/automation/automation-update-management)の説明に従って、管理する各 VM に対して Update Management ソリューションを有効にする必要があります。 ワークスペースに報告するすべての VM でソリューションを有効にするには、**[更新の管理]** を選択し、**[マシンの管理]** をクリックして、**[使用可能なマシンと今後のマシンすべてで有効にします]** オプションを選択します。

   [![](media/vm-update-management/5-sm.PNG "ワークスペース ID とキーの指定")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > この手順を繰り返して、ワークスペースに報告する Azure Stack の VM で各ソリューションを有効にします。 
  
Azure Update and Configuration Management (Azure 更新および構成管理) 拡張機能が有効になると、管理対象の各 VM で 1 日に 2 回スキャンが実行されます。 15 分ごとに API が呼び出され、最後の更新時間を照会することで、状態が変化したかどうかが確認されます。 状態が変更された場合、コンプライアンス スキャンが開始されます。

スキャンされた VM は、Azure Automation アカウントの Update Management ソリューションに表示されます。 

   [![](media/vm-update-management/6-sm.PNG "ワークスペース ID とキーの指定")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> 管理対象のコンピューターの更新されたデータがダッシュボードに表示されるまでに、30 分～ 6 時間かかる場合があります。

Azure Stack の VM は、Azure VM と共に、スケジュールされた更新プログラムのデプロイに含めることができるようになります。

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack"></a>Azure Stack で実行している Azure Monitor for VMs を有効化する
**Azure Monitor、更新、および構成管理**および**Azure Monitor Dependency Agent** の拡張機能が VM にインストールされると、[Azure Monitor for VMs](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) ソリューション内のデータのレポートが開始されます。 

> [!TIP]
> **Azure Monitor Dependency Agent** 拡張機能にはパラメーターは必要ありません。 Azure Monitor for VMs マップの Dependency Agent でデータ自体が送信されることはないため、ファイアウォールやポートを変更する必要はありません。 マップ データは、Log Analytics エージェントによって常に Azure Monitor サービスに直接送信されます。または、ご利用の IT セキュリティ ポリシーでネットワーク上のコンピューターがインターネットに接続することが許可されていない場合には、[OMS ゲートウェイ](https://docs.microsoft.com/azure/azure-monitor/platform/gateway)を経由して送信されます。

VM 用 Azure Monitor には、仮想マシンがどの程度効果的に実行されているかを判定するのに役立ついくつかの主要業績評価指標 (KPI) を対象とする一連のパフォーマンス グラフが含まれています。 グラフには一定期間にわたるリソース使用率が表示されるので、ボトルネックまたは異常を識別することができます。あるいは、各マシンを一覧表示するパースペクティブに切り替えて、選択したメトリックに基づくリソース使用率を表示することもできます。 パフォーマンスを扱うときに考慮すべき要素は数多くありますが、Azure Monitor for VMs では、プロセッサ、メモリ、ネットワーク アダプター、およびディスクの使用率に関連するオペレーティング システムの主要業績評価指標を監視します。 パフォーマンスによって正常性の監視機能が補完されます。さらに、システム コンポーネントに障害がある可能性を示す問題を明らかにしたり、効率化のためのチューニングや最適化をサポートしたり、容量計画をサポートしたりするのにパフォーマンスは役立ちます。

   ![Azure Monitor [パフォーマンス] タブ](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

Azure Stack で実行されている Windows および Linux 仮想マシン上で検出されたアプリケーション コンポーネントの表示は、Azure Monitor for VMs を使用して 2 つの方法で観察できます。仮想マシンから直接行う方法と、Azure Monitor で仮想マシンのグループ全体を対象とする方法です。
「[Azure Monitor for VMs (プレビュー) のマップを使用したアプリケーション コンポーネントの理解](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-maps)」の記事は、2 つの観点のエクスペリエンスとマップ機能の使用方法を理解するために役立ちます。

   ![Azure Monitor [パフォーマンス] タブ](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)


## <a name="enable-update-management-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Update Management を有効にする
Azure Stack の VM の数が多い場合は、[この Azure Resource Manager テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)を使用して、さらに簡単に VM にソリューションをデプロイできます。 このテンプレートは、Microsoft Monitoring Agent 拡張機能を既存の VM にデプロイし、既存の Azure LogAnalytics ワークスペースにそれを追加します。
 
## <a name="next-steps"></a>次の手順
[SQL Server VM のパフォーマンスを最適化する](azure-stack-sql-server-vm-considerations.md)




