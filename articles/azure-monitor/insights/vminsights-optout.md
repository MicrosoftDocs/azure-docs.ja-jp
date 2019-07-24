---
title: Azure Monitor for VMs (プレビュー) を使用した監視を無効にする方法 | Microsoft Docs
description: この記事では、Azure Monitor for VMs を使用した仮想マシンの監視を中断する方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: c6a8322998b391b9021292a3a7213ded56b67feb
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263358"
---
# <a name="how-to-disable-monitoring-of-your-virtual-machines-with-azure-monitor-for-vms-preview"></a>Azure Monitor for VMs (プレビュー) を使用した仮想マシンの監視を無効にする方法

仮想マシンの監視を有効にした後で Azure Monitor for VMs を使用した監視が必要でないと決めた場合は、監視を無効にできます。 この記事では、これを 1 つまたは複数の VM で実現する方法を説明します。  

現時点では、 Azure Monitor for VMs で VM の監視を選択的に無効にすることはできません。 Log Analytics ワークスペースがこのソリューションと他のソリューションをサポートするだけでなく、他の監視データを収集するよう構成されている場合は、以下で説明する影響と方法を理解した後で先に進むことが重要です。

Azure Monitor for VMs はそのエクスペリエンスを実現するために、以下のコンポーネントを使用します。

* Log Analytics ワークスペース。VM および他のソースから収集された監視データを保管します。
* ワークスペースで構成されているパフォーマンス カウンターのコレクション。ワークスペースに接続されているすべての VM の監視構成を更新します。
* ワークスペースで構成されている 2 つの監視ソリューション (**InfrastructureInsights** と **ServiceMap**) です。ワークスペースに接続しているすべての VM についての監視構成を更新します。
* 2 つの Azure 仮想マシン拡張機能 (**MicrosoftMonitoringAgent** と **DependencyAgent**) です。データを収集してワークスペースへ送信します。

Azure Monitor for VMs を使用した仮想マシンの監視の無効化を準備するときは、以下について考慮してください。

* 1 つの VM を評価しており事前に選択されたデフォルトの Log Analytics ワークスペースを承諾した場合は、Dependency エージェントを VM からアンインストールして Log Analytics エージェントをこのワークスペースから切断することで監視を無効にできます。 このアプローチは、VM を他の目的に使用し、その後で別のワークスペースに再接続する場合に適しています。
* 他の監視ソリューションや他のソースからのデータ コレクションをサポートするために Log Analytics ワークスペースを使用している場合は、Azure Monitor for VMs ソリューション コンポーネントをこのワークスペースから削除できます。ワークスペースが中断したり影響を受けることもありません。  

>[!NOTE]
> ソリューション コンポーネントをワークスペースから削除した後で、引き続きヘルス状態を Azure VM から見ることができます。たとえばパフォーマンス データやマップ データは、ポータルでそれぞれのビューに移動すると見ることができます。 データはしばらくするといずれはパフォーマンスおよびマップのビューに表示されなくなります。しかし、ヘルス ビューには引き続き VM のヘルス状態が表示されます。 **[今すぐ試す]** オプションを選択した Azure VM で選択して、監視を後で再度有効にできるようになります。  

## <a name="complete-removal-of-azure-monitor-for-vms"></a>Azure Monitor for VMs の完全削除

次の手順では、Log Analytics ワークスペースをまだ必要とする場合にAzure Monitor for VM を完全に削除する方法について説明します。 **InfrastructureInsights** および **ServiceMap** ソリューションをワークスペースから削除しようとしています。  

>[!NOTE]
>Azure Monitor for VMs を有効にする前に Service Map 監視ソリューションを使用していた場合でまだこれを使用する場合は、このソリューションを下の手順 6 で説明するように削除しないでください。  
>

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。
2. Azure Portal で、**[すべてのサービス]** をクリックします。 リソースの一覧で、「Log Analytics」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。
3. Log Analytics ワークスペースの一覧で、Azure Monitor for VMs の搭載時に選択したワークスペースを選択します。
4. 左側のウィンドウで、 **[ソリューション]** を選択します。  
5. ソリューションの一覧で、**InfrastructureInsights (ワークスペースの名前)** を選択してから、ソリューションの **[概要]** ページで **[削除]** をクリックします。  確認メッセージが表示されたら、 **[はい]** をクリックします。  
6. ソリューションの一覧で、**ServiceMap (ワークスペースの名前)** を選択してから、ソリューションの **[概要]** ページで **[削除]** をクリックします。  確認メッセージが表示されたら、 **[はい]** をクリックします。  

Azure Monitor for VMs を搭載する前にワークスペースで Windows または Linux ベースの VM で[有効にしていたパフォーマンス カウンターを収集](vminsights-onboard.md?toc=/azure/azure-monitor/toc.json#performance-counters-enabled)していなかった場合は、Windows および Linux に対してこれらのルールを[ここ](../../azure-monitor/platform/data-sources-performance-counters.md?toc=/azure/azure-monitor/toc.json#configuring-performance-counters)で説明する手順に従って無効にする必要があります。

## <a name="disable-monitoring-for-an-azure-vm-and-retain-workspace"></a>Azure VM の監視を無効にしてワークスペースを維持する  

次の手順では、Azure Monitor for VMs を評価するため有効にしていた仮想マシンの監視を無効にするが、Log Analytics ワークスペースは他のソースからの監視をサポートするためまだ必要とする場合の方法について説明します。 これが Azure VM の場合は、Windows/Linux の Dependency エージェント VM 拡張機能と Log Analytics エージェント VM 拡張機能を直接 VM から削除することになります。 

>[!NOTE]
>仮想マシンを Azure Automation が管理してプロセスを調整し、構成を管理し、あるいは更新を管理する場合、あるいは Azure Security Center がセキュリティ管理や脅威検出のために管理する場合は、Log Analytics エージェントを削除しないでください。 それ以外の場合は、これらのサービスとソリューションが VM を積極的に管理できないようにすることになります。 

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。 
2. Azure Portal で、**[仮想マシン]** を選択します。 
3. 一覧から VM を選択します。 
4. 左側のウィンドウで **[拡張機能]** を選択して、**[拡張機能]** ページで **DependencyAgent** を選択します。
5. 拡張機能のプロパティ ページで、**[アンインストール]** をクリックします。
6. **[拡張機能]** ページで、**MicrosoftMonitoringAgent** を選択して拡張機能のプロパティ ページで **[アンインストール]** をクリックします。  
