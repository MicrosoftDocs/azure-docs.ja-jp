---
title: Azure Monitor for VMs (プレビュー) での監視を無効にする | Microsoft Docs
description: この記事では、Azure Monitor for VMs で仮想マシンの監視を停止する方法について説明します。
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
ms.openlocfilehash: eb667486a6e3279cb78fefe02723f14d9f7c9b4f
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155692"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>Azure Monitor for VMs (プレビュー) で VM の監視を無効にする

仮想マシン (VM) の監視を有効にした後、後から Azure Monitor for VMs での監視を無効にすることを選択できます。 この記事では、1 つまたは複数の VM の監視を無効にする方法を示します。  

現時点では、Azure Monitor for VMs で VM の監視を選択的に無効にすることはできません。 お使いの Log Analytics ワークスペースが、Azure Monitor for VMs とその他のソリューションをサポートしている場合があります。 他の監視データも収集する可能性があります。 お使いの Log Analytics ワークスペースがこれらのサービスを提供する場合は、開始する前に、監視を無効にすることの影響とその方法を理解する必要があります。

Azure Monitor for VMs はそのエクスペリエンスを実現するために、以下のコンポーネントを使用します。

* Log Analytics ワークスペース。VM および他のソースからの監視データを保管します。
* ワークスペースで構成されているパフォーマンス カウンターのコレクション。 このコレクションは、ワークスペースに接続しているすべての VM での監視構成を更新します。
* `InfrastructureInsights` および `ServiceMap`。これらは、ワークスペースで構成されている監視ソリューションです。 これらのコレクションは、ワークスペースに接続しているすべての VM での監視構成を更新します。
* `MicrosoftMonitoringAgent` および `DependencyAgent`。AzureVM の拡張機能です。 これらの拡張機能はデータを収集してワークスペースに送信します。

VM の監視を無効にする準備をするときは、これらの考慮事項に留意してください。

* 1 つの VM を評価し、事前に選択された既定の Log Analytics ワークスペースを使用した場合は、Dependency エージェントを VM からアンインストールして Log Analytics エージェントをこのワークスペースから切断することで監視を無効にできます。 このアプローチは、VM を他の目的に使用し、その後で別のワークスペースに再接続する場合に適しています。
* 他の監視ソリューションや、他のソースからのデータ収集をサポートする既存の Log Analytics ワークスペースを選択した場合は、ソリューション コンポーネントをワークスペースから削除できます。ワークスペースが中断したり影響を受けることもありません。  

>[!NOTE]
> ソリューション コンポーネントをワークスペースから削除した後で、引き続き正常性状態を Azure VM から見ることができます。たとえばパフォーマンス データやマップ データは、ポータルでそれぞれのビューに移動すると見ることができます。 データは最終的に、 **[パフォーマンス]** ビューと **[マップ]** ビューに表示されなくなります。 ただし、 **[Health]\(正常性\)** ビューには引き続き、VM の正常性状態が表示されます。 選択した Azure VM から **[今すぐ試す]** オプションを選択して、監視を後で再度有効にすることができます。  

## <a name="remove-azure-monitor-for-vms-completely"></a>Azure Monitor for VMs を完全に削除する

まだ Log Analytics ワークスペースが必要な場合は、次の手順に従って Azure Monitor for VMs を完全に削除してください。 `InfrastructureInsights` および `ServiceMap` ソリューションをワークスペースから削除します。  

>[!NOTE]
>Azure Monitor for VMs を有効にする前に Service Map 監視ソリューションを使用していて、このソリューションにまだ依存している場合は、このソリューションを削除しないでください (次の説明の最後の手順は実行しないでください)。  
>

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて一覧から候補が絞り込まれます。 **[Log Analytics]** を選択します。
3. Log Analytics ワークスペースの一覧で、Azure Monitor for VMs を有効化するときに選択したワークスペースを選択します。
4. 左側で、 **[ソリューション]** を選択します。  
5. ソリューションの一覧で、**InfrastructureInsights(ワークスペース名)** を選択します。 ソリューションの **[概要]** ページで、 **[削除]** を選択します。 確認メッセージが表示されたら、 **[はい]** を選択します。  
6. ソリューションの一覧で、**ServiceMap(ワークスペース名)** を選択します。 ソリューションの **[概要]** ページで、 **[削除]** を選択します。 確認メッセージが表示されたら、 **[はい]** を選択します。  

Azure Monitor for VMs を有効にする前に、ワークスペースで Windows ベースまたは Linux ベースの VM の[パフォーマンス カウンターを収集](vminsights-enable-overview.md#performance-counters-enabled)していなかった場合は、Windows 用と Linux 用の[規則を無効にして](../platform/data-sources-performance-counters.md#configuring-performance-counters)ください。

## <a name="disable-monitoring-and-keep-the-workspace"></a>監視を無効にしてワークスペースを保持する  

お使いの Log Analytics ワークスペースで、他のソースからの監視をまだサポートする必要がある場合は、次の手順に従って、Azure Monitor for VMs を評価するために使用していた VM に対する監視を無効にしてください。 Azure VM の場合は、Windows または Linux 用の依存関係エージェント VM 拡張機能と Log Analytics エージェント VM 拡張機能を、直接 VM から削除することになります。 

>[!NOTE]
>以下の場合は、Log Analytics エージェントを削除しないでください。 
>
> * プロセスを調整したり、構成または更新プログラムを管理するために、Azure Automation で VM を管理している。 
> * セキュリティと脅威検出のために Azure Security Center で VM を管理している。 
>
> Log Analytics エージェントを削除する場合、これらのサービスとソリューションが VM を積極的に管理できないようにすることになります。 

1. [Azure Portal](https://portal.azure.com) にサインインします。 
2. Azure Portal で、 **[仮想マシン]** を選択します。 
3. 一覧から VM を選択します。 
4. 左側で、 **[拡張機能]** を選択します。 **[拡張機能]** ページで、**DependencyAgent** を選択します。
5. 拡張機能のプロパティ ページで、 **[アンインストール]** を選択します。
6. **[拡張機能]** ページで、**MicrosoftMonitoringAgent** を選択します。 拡張機能のプロパティ ページで、 **[アンインストール]** を選択します。  
