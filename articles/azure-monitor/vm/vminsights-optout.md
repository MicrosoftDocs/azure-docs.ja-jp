---
title: VM insights での監視を無効にする
description: この記事では、VM insights で仮想マシンの監視を停止する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 2de0dcd52745ebadb02ab8dbb563e28abf2822dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046484"
---
# <a name="disable-monitoring-of-your-vms-in-vm-insights"></a>VM insights での VM の監視を無効にする

仮想マシン (VM) の監視を有効にした後、後から VM insights での監視を無効にすることを選択できます。 この記事では、1 つまたは複数の VM の監視を無効にする方法を示します。  

現時点では、VM insights で VM の監視を選択的に無効にすることはできません。 お使いの Log Analytics ワークスペースが、VM insights とその他のソリューションをサポートしている場合があります。 他の監視データも収集する可能性があります。 お使いの Log Analytics ワークスペースがこれらのサービスを提供する場合は、開始する前に、監視を無効にすることの影響とその方法を理解する必要があります。

VM insights はそのエクスペリエンスを実現するために、以下のコンポーネントを使用します。

* Log Analytics ワークスペース。VM および他のソースからの監視データを保管します。
* ワークスペースで構成されているパフォーマンス カウンターのコレクション。 このコレクションは、ワークスペースに接続しているすべての VM での監視構成を更新します。
* `VMInsights`。これは、ワークスペースで構成されている監視ソリューションです。 このソリューションは、ワークスペースに接続しているすべての VM での監視構成を更新します。
* `MicrosoftMonitoringAgent` および `DependencyAgent`。AzureVM の拡張機能です。 これらの拡張機能はデータを収集してワークスペースに送信します。

VM の監視を無効にする準備をするときは、これらの考慮事項に留意してください。

* 1 つの VM を評価し、事前に選択された既定の Log Analytics ワークスペースを使用した場合は、Dependency エージェントを VM からアンインストールして Log Analytics エージェントをこのワークスペースから切断することで監視を無効にできます。 このアプローチは、VM を他の目的に使用し、その後で別のワークスペースに再接続する場合に適しています。
* 他の監視ソリューションや、他のソースからのデータ収集をサポートする既存の Log Analytics ワークスペースを選択した場合は、ソリューション コンポーネントをワークスペースから削除できます。ワークスペースが中断したり影響を受けることもありません。  

>[!NOTE]
> ワークスペースからソリューション コンポーネントを削除した後に、Azure VM のパフォーマンスとマップのデータが引き続き表示される場合があります。 データは最終的に、 **[パフォーマンス]** ビューと **[マップ]** ビューに表示されなくなります。 選択した Azure VM から **[Enable]\(有効にする\)** オプションが選択可能になります。これにより、将来、監視を再び有効にすることができます。  

## <a name="remove-vm-insights-completely"></a>VM insights を完全に削除する

まだ Log Analytics ワークスペースが必要な場合は、次の手順に従って VM insights を完全に削除してください。 `VMInsights` ソリューションをワークスペースから削除します。  

1. [Azure portal](https://portal.azure.com) にサインインします。
2. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて一覧から候補が絞り込まれます。 **[Log Analytics]** を選択します。
3. Log Analytics ワークスペースの一覧で、VM insights を有効化するときに選択したワークスペースを選択します。
4. 左側で、 **[ソリューション]** を選択します。  
5. ソリューションの一覧から、 **[VMInsights(ワークスペース名)]** を選択します。 ソリューションの **[概要]** ページで、 **[削除]** を選択します。 確認メッセージが表示されたら、 **[はい]** を選択します。

## <a name="disable-monitoring-and-keep-the-workspace"></a>監視を無効にしてワークスペースを保持する  

お使いの Log Analytics ワークスペースで、他のソースからの監視をまだサポートする必要がある場合は、次の手順に従って、VM insights を評価するために使用していた VM に対する監視を無効にしてください。 Azure VM の場合は、Windows または Linux 用の依存関係エージェント VM 拡張機能と Log Analytics エージェント VM 拡張機能を、直接 VM から削除することになります。 

>[!NOTE]
>以下の場合は、Log Analytics エージェントを削除しないでください。 
>
> * プロセスを調整したり、構成または更新プログラムを管理するために、Azure Automation で VM を管理している。 
> * セキュリティと脅威検出のために Azure Security Center で VM を管理している。 
>
> Log Analytics エージェントを削除する場合、これらのサービスとソリューションが VM を積極的に管理できないようにすることになります。 

1. [Azure portal](https://portal.azure.com) にサインインします。 
2. Azure Portal で、 **[仮想マシン]** を選択します。 
3. 一覧から VM を選択します。 
4. 左側で、 **[拡張機能]** を選択します。 **[拡張機能]** ページで、**DependencyAgent** を選択します。
5. 拡張機能のプロパティ ページで、 **[アンインストール]** を選択します。
6. **[拡張機能]** ページで、**MicrosoftMonitoringAgent** を選択します。 拡張機能のプロパティ ページで、 **[アンインストール]** を選択します。  
