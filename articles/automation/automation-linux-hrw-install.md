---
title: "Azure Automation の Linux Hybrid Runbook Worker | Microsoft Docs"
description: "この記事では、Azure Automation Hybrid Runbook Worker をインストールして、ローカル データ センターやクラウド環境にある Linux ベースのコンピューターで Runbook を実行できるようにする方法について説明します。"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 88c052c3a22611b796559d4dd62c763445aa6210
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker のデプロイ方法
Azure Automation の Runbook は Azure クラウドで実行されるため、他のクラウドやオンプレミス環境のリソースにはアクセスできません。  Azure Automation の Hybrid Runbook Worker 機能を使用すると、ロールをホスティングしているコンピューター上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 Runbook は Azure Automation で格納および管理された後、1 つ以上の指定されたコンピューターに配信されます。  

次の図にこの機能を示します。<br>  

![Hybrid Runbook Worker の概要](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Hybrid Runbook Worker ロールの技術的な概要については、「[Automation アーキテクチャの概要](automation-offering-get-started.md#automation-architecture-overview)」を参照してください。 Hybrid Runbook Worker のデプロイを開始する前に、[ハードウェアとソフトウェアの要件](automation-offering-get-started.md#hybrid-runbook-worker)および[ネットワークを準備するための情報](automation-offering-get-started.md#network-planning)に関する情報を見直します。  Runbook Worker が正常にデプロイされたら、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。     

## <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker のグループ
各 Hybrid Runbook Worker は、エージェントのインストール時に指定する Hybrid Runbook Worker グループのメンバーです。  グループには単一のエージェントを含めることができますが、高可用性グループに複数のエージェントをインストールすることができます。

Hybrid Runbook Worker で Runbook を開始する場合は、実行されるグループを指定します。  要求を処理するワーカーは、グループのメンバーが決定します。  特定のワーカーを指定することはできません。

## <a name="installing-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker のインストール
Linux コンピューターに Hybrid Runbook Worker をインストールして構成するには、非常に簡単な手順に従って手動でインストールし、ロールを構成します。   **Automation Hybrid Worker** ソリューションを OMS ワークスペースで有効にする必要があります。その後、一連のコマンドを実行してコンピューターを worker として登録し、新規または既存のグループに追加します。 

作業を進める前に、Automation アカウントがリンクされている Log Analytics ワークスペースと、Automation アカウントの主キーに注意する必要があります。  ポータルから、Automation アカウント、ワークスペース ID の**ワークスペース**、および主キーの**キー**を選択することで両方を検索できます。  

1.  OMS で "Automation Hybrid Worker" ソリューションを有効にします。 これは以下のいずれかの方法で実行できます。

   1. [OMS ポータル](https://mms.microsoft.com)のソリューション ギャラリーから、**Automation Hybrid Worker** ソリューションを有効にします。
   2. 次のコマンドレットを実行します。

        ```powershell
         $null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

2.  Linux コンピューターで、パラメーター *-w* と *-k* の値を変更する次のコマンドを実行します。
    
    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <AutomationSharedKey> --groupname <hybridgroupname> -e <automationendpoint>
    ```
3. コマンドが完了すると、Azure Portal の [ハイブリッド worker グループ] ブレードに新しいグループとメンバー数が表示されますが、既存のグループの場合はメンバー数が追加されます。  **[ハイブリッド worker グループ]** ブレード上にあるリストからグループを選択し、**[ハイブリッド worker]** タイルを選択できます。  **[ハイブリッド worker]** ブレードで、グループの各メンバーが一覧表示されます。  

## <a name="next-steps"></a>次のステップ

* 「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。
* Hybrid Runbook Worker を削除する方法については、「[Remove Azure Automation Hybrid Runbook Workers (Azure Automation の Hybrid Runbook Worker の削除)](automation-remove-hrw.md)」をご覧ください。