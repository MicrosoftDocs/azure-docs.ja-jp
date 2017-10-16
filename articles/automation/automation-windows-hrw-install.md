---
title: "Azure Automation の Windows Hybrid Runbook Worker | Microsoft Docs"
description: "この記事では、お使いのローカル データ センターまたはクラウド 環境内の Windows ベースのコンピューターで Runbook を実行できるようにする Azure Automation Hybrid Runbook Worker のインストールについて説明します。"
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
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: d889fda72f54af3a5f6db24f7ee6632f72eacb88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-a-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker のデプロイ方法

Azure Automation の Runbook は Azure クラウドで実行されるため、他のクラウドやオンプレミス環境のリソースにはアクセスできません。  Azure Automation の Hybrid Runbook Worker 機能を使用すると、ロールをホスティングしているコンピューター上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 Runbook は Azure Automation で格納および管理された後、1 つ以上の指定されたコンピューターに配信されます。  

次の図にこの機能を示します。<br>

![Hybrid Runbook Worker の概要](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Hybrid Runbook Worker ロールの技術的な概要とデプロイの考慮事項については、「[Automation アーキテクチャの概要](automation-offering-get-started.md#automation-architecture-overview)」を参照してください。

## <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker のグループ

各 Hybrid Runbook Worker は、エージェントのインストール時に指定する Hybrid Runbook Worker グループのメンバーです。  グループには単一のエージェントを含めることができますが、高可用性グループに複数のエージェントをインストールすることができます。

Hybrid Runbook Worker で Runbook を開始する場合は、実行されるグループを指定します。  要求を処理するワーカーは、グループのメンバーが決定します。  特定のワーカーを指定することはできません。

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker のインストール 

Windows Hybrid Runbook Worker は、以下の 2 つの方法でインストール、構成できます。  Automation Runbook を使用して、Windows コンピューターの構成に必要なプロセスを完全に自動化する方法をお勧めします。  2 番目の方法は、ロールを手動でインストールして構成する手順を段階的に追うものです。  

> [!NOTE]
> Hybrid Runbook Worker ロールをサポートするサーバーの構成を Desired State Configuration (DSC) を使用して管理するには、サーバーを DSC ノードとして追加する必要があります。  DSC による管理のためのサーバーのオンボードの詳細については、「[Azure Automation DSC による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」を参照してください。        
[更新管理ソリューション](../operations-management-suite/oms-solution-update-management.md)を有効にすると、このソリューションに含まれる Runbook をサポートするために、OMS ワークスペースに接続された Windows コンピューターは Hybrid Runbook Worker として自動的に構成されます。  ただし、このコンピューターは、Automation アカウントで既に定義した可能性のあるハイブリッド worker グループには登録されません。  このソリューションと Hybrid Runbook Worker グループ メンバーシップの両方に同じアカウントを使用していれば、このコンピューターを Automation アカウントの Hybrid Runbook Worker に追加して Automation Runbook をサポートできます。  この機能は、Hybrid Runbook Worker のバージョン 7.2.12024.0 に追加されました。  

Hybrid Runbook Worker のデプロイを開始する前に、[ハードウェアとソフトウェアの要件](automation-offering-get-started.md#hybrid-runbook-worker)および[ネットワークを準備するための情報](automation-offering-get-started.md#network-planning)に関する情報を見直します。  Runbook Worker が正常にデプロイされたら、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。  
 
### <a name="automated-deployment"></a>自動化されたデプロイ

Windows Hybrid Worker ロールのインストールと構成を自動化するには、次の手順を実行します。  

1. [PowerShell ギャラリー](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript) の *New-OnPremiseHybridWorker.ps1* スクリプトを Hybrid Runbook Worker ロールを実行しているコンピューターから、または環境内の別のコンピューターから直接ダウンロードし、worker にコピーします。  

    *New-OnPremiseHybridWorker.ps1* スクリプトは、実行中に次のパラメーターを必要とします。

  * *AutomationAccountName* (必須) - Automation アカウントの名前。  
  * *ResourceGroupName* (必須) - Automation アカウントと関連付けられたリソース グループの名前。  
  * *HybridGroupName* (必須) - このシナリオをサポートしている Runbook のターゲットとして指定する Hybrid Runbook Worker グループの名前。 
  *  *SubscriptionID* (必須) - Automation アカウントがある Azure サブスクリプション ID。
  *  *WorkspaceName* (省略可能) - OMS ワークスペースの名前。  OMS ワークスペースがない場合は、スクリプトがこれを作成して構成します。  

     > [!NOTE]
     > OMS と Automation の統合は、現時点では**オーストラリア南東部**、**米国東部 2**、**東南アジア**、および**西ヨーロッパ**の地域でサポートされています。  Automation アカウントがそれらの地域のいずれかにない場合、スクリプトによって OMS ワークスペースが作成されますが、Automation アカウントと OMS ワークスペースのリンクができないと警告されます。
     >
2. コンピューターの**スタート**画面から、管理者モードで **Windows PowerShell** を起動します。  
3. PowerShell コマンド ライン シェルから、ダウンロードしたスクリプトが含まれているフォルダーに移動し、スクリプトを実行します。その際、*-AutomationAccountName*、*-ResourceGroupName*、*-HybridGroupName*、*-SubscriptionId*、*-WorkspaceName* の各パラメーターの値を変更します。

     > [!NOTE] 
     > スクリプトの実行後、Azure での認証が求められます。  サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用してサインインする**必要があります**。  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. **NuGet** のインストールに同意するように求められ、Azure 資格情報で認証するように求められます。<br><br> ![New-OnPremiseHybridWorker スクリプトの実行](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. スクリプトが完了したら、[ハイブリッド worker グループ] ブレードには新しいグループとメンバーの数が表示されますが、既存のグループの場合はメンバーの数がインクリメントされます。  **[ハイブリッド worker グループ]** ブレード上にあるリストからグループを選択し、**[ハイブリッド worker]** タイルを選択できます。  **[ハイブリッド worker]** ブレードで、グループの各メンバーが一覧表示されます。  

### <a name="manual-deployment"></a>手動デプロイ 

最初の 2 つのステップは Automation 環境に対して 1 回だけ実行し、残りのステップは worker コンピューターごとに繰り返します。

#### <a name="1-create-operations-management-suite-workspace"></a>1.Operations Management Suite のワークスペースを作成する

Operations Management Suite のワークスペースがまだない場合は、[ワークスペースの管理](../log-analytics/log-analytics-manage-access.md)に関するページの手順に従って作成します。 既存のワークスペースがある場合は、それを使用できます。

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>手順 2.Operations Management Suite ワークスペースに Automation ソリューションを追加します。

ソリューションにより、Operations Management Suite に機能が追加されます。  Automation ソリューションは、Hybrid Runbook Worker のサポートなど、Azure Automation 用の機能を追加します。  ソリューションをワークスペースに追加すると、次の手順でインストールする worker コンポーネントがエージェント コンピューターに自動的にプッシュダウンされます。

「 [To add a solution using the Solutions Gallery (ソリューション ギャラリーを使用してソリューションを追加するには)](../log-analytics/log-analytics-add-solutions.md) 」の説明に従って、Operations Management Suite のワークスペースに **Automation** ソリューションを追加します。

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3.Microsoft Monitoring Agent をインストールする

Microsoft Monitoring Agent は Operations Management Suite にコンピューターを接続します。  エージェントをオンプレミスのコンピューターにインストールし、ワークスペースに接続すると、Hybrid Runbook Worker に必要なコンポーネントが自動的にダウンロードされます。

「[Windows コンピューターを Log Analytics に接続する](../log-analytics/log-analytics-windows-agents.md)」の手順に従って、オンプレミスのコンピューターにエージェントをインストールします。  コンピューターごとにこのプロセスを繰り返して、複数の worker を環境に追加できます。

エージェントが Operations Management Suite に正常に接続すると、Operations Management Suite の **[設定]** ウィンドウの **[接続されているソース]** タブにエージェントが表示されます。  C:\Program Files\Microsoft Monitoring Agent\Agent に **AzureAutomationFiles** という名前のフォルダーが作成されていることを調べて、エージェントが Automation ソリューションを正常にダウンロードしたことを確認できます。  Hybrid Runbook Worker のバージョンを確認するには、C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ に移動し、\\*バージョン* サブフォルダーをメモします。   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4.Runbook 環境をインストールして、Azure Automation に接続する

エージェントを Operations Management Suite に追加すると、Automation ソリューションは、**Add-HybridRunbookWorker** コマンドレットを含む **HybridRegistration** PowerShell モジュールをプッシュダウンします。  コンピューターに Runbook 環境をインストールして、Azure Automation に登録する場合は、このコマンドレットを使用します。

管理者モードで PowerShell セッションを開き、次のコマンドを実行してモジュールをインポートします。

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

次に、以下の構文を使用して、 **Add-HybridRunbookWorker** コマンドレットを実行します。

    Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>

このコマンドレットに必要な情報は、Azure ポータルの **[キーの管理]** ブレードから取得できます。  このブレードを開くには、Automation アカウントの **[設定]** ブレードで **[キー]** オプションを選択します。

![Hybrid Runbook Worker の概要](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** は、Hybrid Runbook Worker グループの名前です。 Automation アカウントにこのグループが既に存在する場合は、現在のコンピューターがそれに追加されます。  まだ存在しない場合は、追加されます。
* **EndPoint** は、**[キーの管理]** ブレードの **[URL]** フィールドです。
* **Token** は、**[キーの管理]** ブレードの **[プライマリ アクセス キー]** です。  

インストールに関する詳細な情報を受け取るには、**Add-HybridRunbookWorker** で **-Verbose** スイッチを使用します。

#### <a name="5-install-powershell-modules"></a>5.PowerShell モジュールをインストールする

Runbook は、Azure Automation 環境にインストールされているモジュールで定義されているアクティビティとコマンドレットをすべて使用できます。  これらのモジュールはオンプレミス コンピューターに自動的に配置されないため、手動でインストールする必要があります。  ただし、Azure モジュールは既定でインストールされ、すべての Azure サービスのコマンドレットと Azure Automation のアクティビティにアクセスできます。

Hybrid Runbook Worker 機能の主な目的はローカル リソースを管理することであるため、ほとんどの場合、これらのリソースをサポートするモジュールをインストールする必要があります。  Windows PowerShell モジュールのインストールについては、「 [モジュールのインストール](http://msdn.microsoft.com/library/dd878350.aspx) 」を参照してください。  インストールされるモジュールは PSModulePath 環境変数によって参照されている場所に置き、ハイブリッド worker によって自動的にインポートされるようにする必要があります。  詳細については、「[Modifying the PSModulePath Installation Path (PSModulePath インストール パスの変更)](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx)」を参照してください。 

## <a name="troubleshooting"></a>トラブルシューティング 

Hybrid Runbook Worker は、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、および状態の報告を行うために Microsoft Monitoring Agent に依存しています。 ワーカーの登録に失敗した場合に考えられるエラーの原因を次に示します。  

1. Hybrid Worker がプロキシまたはファイアウォールの内側にある。  
    コンピューターがポート 443 で *.azure-automation.net に発信アクセスできることを確認します。  

2. Hybrid Worker を実行しているコンピューターが、ハードウェアの最小[要件](automation-offering-get-started.md#hybrid-runbook-worker)を満たしていない。  
    Hybrid Runbook Worker を実行するコンピューターは、この機能をホストするよう指定する前に、ハードウェアの最小要件を満たしている必要があります。 満たしていない場合、他のバックグラウンド プロセスのリソース使用状況および実行中の Runbook による競合によっては、コンピューターが過負荷になり、Runbook ジョブが遅延またはタイムアウトします。
   Hybrid Runbook Worker の機能を実行するように指定されているコンピューターがハードウェアの最小要件を満たしていることを確認します。  満たしている場合は、CPU とメモリの使用状況を監視して、Hybrid Runbook Worker プロセスのパフォーマンスと Windows の間の相関関係を調べます。  メモリまたは CPU に負荷がかかる場合は、リソースのボトルネックを解消してエラーを解決するには、アップグレード、プロセッサの追加、またはメモリの増設が必要である可能性があります。 または、最小要件を満たす異なるコンピューティング リソースを選択し、ワークロードがさらに多くのリソースを必要とすることを示したときは拡張します。
    
3. Microsoft Monitoring Agent サービスが実行されていない。  
    Microsoft Monitoring Agent の Windows サービスが実行されていない場合、Hybrid Runbook Worker は Azure Automation と通信できません。  PowerShell で次のコマンドを入力して、このエージェントが実行されていることを確認します: `get-service healthservice`。  サービスが停止している場合は、PowerShell で次のコマンドを入力してサービスを開始します: `start-service healthservice`。  

4. **アプリケーションとサービス ログ\Operations Manager** イベント ログに、**Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** と次の説明を含むイベント 4502 と EventMessage が表示される。"*service <wsid>.oms.opinsights.azure.com によって提示された証明書は Microsoft サービスで使用される証明機関によって発行されていません。TLS/SSL 通信を遮断するプロキシが実行されているかどうかをネットワーク管理者に問い合わせてください。記事 KB3126513 に、接続の問題に関するトラブルシューティング情報が記載されています。*"
    これは、プロキシまたはネットワークのファイアウォールが Microsoft Azure への通信をブロックしていることが原因で発生する可能性があります。  コンピューターがポート 443 で *.azure-automation.net に発信アクセスできることを確認します。

ログは、各ハイブリッド worker の C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes にローカルに格納されます。  Azure Automation へのロールのオンボードに影響する接続などの問題や、通常の動作の実行中に発生した問題を示唆する警告やエラー イベントが、**アプリケーションとサービス ログ\Microsoft-SMA\Operations** と**アプリケーションとサービス ログ\Operations Manager** イベント ログに書き込まれているかどうかをチェックできます。  

## <a name="next-steps"></a>次のステップ

* 「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。
* Hybrid Runbook Worker を削除する方法については、「[Remove Azure Automation Hybrid Runbook Workers (Azure Automation の Hybrid Runbook Worker の削除)](automation-remove-hrw.md)」をご覧ください。