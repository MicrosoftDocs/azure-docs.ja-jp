---
title: "Azure Automation の Hybrid Runbook Worker | Microsoft Docs"
description: "この記事では、ローカル データ センター内のコンピューターで Runbook を実行できるようにする Azure Automation の機能である Hybrid Runbook Worker のインストールと使用について説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: ca145339ab14ef29851b53deca9be9ad137317c9
ms.openlocfilehash: 77d3a4114b23114c0b4bf97a461cee2356d66a4a
ms.lasthandoff: 03/01/2017

---

# <a name="automate-resources-in-your-data-center-with-hybrid-runbook-worker"></a>Hybrid Runbook Worker によるデータ センターのリソースの自動化
Azure Automation の Runbook は、Azure クラウドで実行されるため、ローカル データ センター内のリソースにはアクセスできません。  Azure Automation の Hybrid Runbook Worker 機能を使用すれば、ローカル リソースを管理するために、データ センターに配置されたマシンで Runbook を実行することができます。 Runbook は Azure Automation で格納および管理されてから、1 つ以上のオンプレミス マシンに配信されます。  

次の図にこの機能を示します。<br>  

![Hybrid Runbook Worker の概要](media/automation-hybrid-runbook-worker/automation.png)

Hybrid Runbook Worker として機能し、Azure Automation の Runbook を実行するように、データ センターの&1; つ以上のコンピューターを指定できます。  各 worker には、Microsoft Operations Management Suite および Azure Automation Runbook 環境に接続できる Microsoft 管理エージェントが必要です。  Operations Management Suite は、管理エージェントをインストールして管理し、worker の機能を監視するためにのみ使用されます。  Runbook の配信と Runbook の実行指示は、Azure Automation によって行われます。

Hybrid Runbook Worker をサポートするための受信ファイアウォールの要件はありません。 ローカル コンピューター上のエージェントは、クラウドでの Azure Automation とのすべての通信を開始します。 Runbook が開始されると、Azure Automation はエージェントによって取得される指示を作成します。 次に、エージェントは、それを実行する前に、Runbook と任意のパラメーターをプル ダウンします。  Azure Automation の Runbook で使用される [資産](http://msdn.microsoft.com/library/dn939988.aspx) も取得します。

> [!NOTE]
> Hybrid Runbook Worker ロールをサポートするサーバーの構成を Desired State Configuration (DSC) を使用して管理するには、サーバーを DSC ノードとして追加する必要があります。  DSC による管理のためのサーバーのオンボードの詳細については、「[Azure Automation DSC による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」を参照してください。           
><br>
>[更新管理ソリューション](../operations-management-suite/oms-solution-update-management.md)を有効にすると、このソリューションに含まれる Runbook をサポートするために、OMS ワークスペースに接続された Windows コンピューターは Hybrid Runbook Worker として自動的に構成されます。  ただし、このコンピューターは、Automation アカウントで既に定義されている可能性のあるハイブリッド worker グループには登録されません。  このソリューションと Hybrid Runbook Worker グループ メンバーシップの両方に同じアカウントを使用していれば、このコンピューターを Automation アカウントの Hybrid Runbook Worker に追加して Automation Runbook をサポートできます。  この機能は、Hybrid Runbook Worker のバージョン 7.2.12024.0 に追加されました。  


## <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker のグループ
各 Hybrid Runbook Worker は、エージェントのインストール時に指定する Hybrid Runbook Worker グループのメンバーです。  グループには単一のエージェントを含めることができますが、高可用性グループに複数のエージェントをインストールすることができます。

Hybrid Runbook Worker で Runbook を開始する場合は、実行されるグループを指定します。  要求を処理するワーカーは、グループのメンバーが決定します。  特定のワーカーを指定することはできません。

## <a name="hybrid-runbook-worker-requirements"></a>Hybrid Runbook Worker の要件
Hybrid Runbook のジョブを実行するには、少なくとも&1; 台のオンプレミス コンピューターを指定する必要があります。  このコンピューターには次のものが必要です。

* Windows Server 2012 またはそれ以降
* Windows PowerShell 4.0 またはそれ以降。  信頼性向上のため、コンピューターに Windows PowerShell 5.0 をインストールすることをお勧めします。 新しいバージョンは、[Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=50395) からダウンロードできます。
* 少なくとも 2 つのコアと 4 GB の RAM

Hybrid Worker では次の推奨事項を考慮してください。

* 高可用性のため、グループごとに複数の Hybrid Worker を指定します。  
* Hybrid Worker は、Service Management Automation または System Center Orchestrator の Runbook サーバーと共存できます。
* ジョブが完了すると Azure Automation にジョブのデータが返送されるため、Automation アカウントのリージョン内またはその近くに物理的に存在するコンピューターを使用することを検討します。

### <a name="configure-proxy-and-firewall-settings"></a>プロキシとファイアウォール設定の構成
オンプレミスの Hybrid Runbook Worker を Microsoft Operations Management Suite (OMS) サービスに接続して登録するには、その Hybrid Runbook Worker は、以下で説明するポート番号と URL にアクセスできる必要があります。  OMS に接続するには、[Microsoft Monitoring Agent に必要なポートと URL](../log-analytics/log-analytics-proxy-firewall.md#configure-settings-with-the-microsoft-monitoring-agent) のほかに、このポートと URL が必要です。 エージェントと OMS サービス間の通信にプロキシ サーバーを使用する場合、適切なリソースにアクセスできることを確認する必要があります。 ファイアウォールを使用してインターネットへのアクセスを制限している場合は、アクセスを許可するようにファイアウォールを構成する必要があります。

Automation と通信するには、次の一覧に示すポートと URL が Hybrid Runbook Worker に必要です。

* ポート: 発信インターネット アクセスには TCP 443 のみが必要です
* グローバル URL: *.azure-automation.net

Automation アカウントが特定のリージョンに対して定義されており、そのリージョンのデータセンターとの通信を制限する必要がある場合は、次の表に示す各リージョンの DNS レコードを使用します。

| **[リージョン]** | **DNS レコード** |
| --- | --- |
| 米国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 米国東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 西ヨーロッパ |we-jobruntimedata-prod-su1.azure-automation.net |
| 北ヨーロッパ |ne-jobruntimedata-prod-su1.azure-automation.net |
| カナダ中部 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 東南アジア |sea-jobruntimedata-prod-su1.azure-automation.net |
| インド中部 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 東日本 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| オーストラリア東南部 |ase-jobruntimedata-prod-su1.azure-automation.net |

## <a name="installing-hybrid-runbook-worker"></a>Hybrid Runbook Worker のインストール

Hybrid Runbook Worker をインストールして構成する手順には、次に示す&2; つの方法があります。  最初の方法は、Windows コンピューターを構成するために必要なすべての手順を自動化する PowerShell スクリプトであり、これは展開プロセス全体を合理化する場合に推奨される方法です。  2 番目の方法は、ロールを手動でインストールして構成する手順を段階的に追うものです。   

### <a name="automated-deployment"></a>自動化されたデプロイ

ハイブリッド Worker ロールのインストールと構成を自動化するには、次の手順を実行します。  

1. [PowerShell ギャラリー](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript) の *New-OnPremiseHybridWorker.ps1* スクリプトを Hybrid Runbook Worker ロールを実行しているコンピューターから、または環境内の別のコンピューターから直接ダウンロードし、worker にコピーします。  

    *New-OnPremiseHybridWorker.ps1* スクリプトは、実行中に次のパラメーターを必要とします。

  * *AutomationAccountName* (必須) - Automation アカウントの名前。  
  * *ResourceGroupName* (必須) - Automation アカウントと関連付けられたリソース グループの名前。  
  * *HybridGroupName* (必須) - このシナリオをサポートしている Runbook のターゲットとして指定する Hybrid Runbook Worker グループの名前 
  *  *SubscriptionID* (必須) - Automation アカウントがある Azure サブスクリプション ID。
  *  *WorkspaceName* (省略可能) - OMS ワークスペースの名前。  OMS ワークスペースがない場合は、スクリプトがこれを作成して構成します。

    > [!NOTE]
    > OMS と Automation の統合は、現時点では**オーストラリア南東部**、**米国東部 2**、**東南アジア**、および**西ヨーロッパ**の地域でサポートされています。  Automation アカウントがそれらの地域のいずれかにない場合、スクリプトは OMS ワークスペースを作成しますが、Automation アカウントと OMS ワークスペースのリンクができないと警告されます。  

2. コンピューターの**スタート**画面から、管理者モードで **Windows PowerShell** を起動します。  
3. PowerShell コマンド ライン シェルから、ダウンロードしたスクリプトが含まれているフォルダーに移動し、スクリプトを実行します。その際、*-AutomationAccountName*、*-ResourceGroupName*、*-HybridGroupName*、*-SubscriptionId*、*-WorkspaceName* の各パラメーターの値を変更します。

    > [!NOTE] 
    > スクリプトの実行後、Azure での認証が求められます。  サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用してログインする **必要があります** 。   
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. **NuGet** のインストールに同意するように求められ、Azure 資格情報で認証するように求められます。<br><br> ![New-OnPremiseHybridWorker スクリプトの実行](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. スクリプトが完了したら、[Hybrid Worker Groups (Hybrid Worker グループ)] ブレードは新しいグループとメンバーの数を表示しますが、既存のグループの場合はメンバーの数が適宜インクリメントされます。  **[Hybrid Worker Groups (Hybrid Worker グループ)]** ブレード上にあるリストからグループを選択し、**[Hybrid Workers]** タイルを選択できます。  **[Hybrid Workers]** ブレードで、グループの各メンバーが一覧表示されます。  

### <a name="manual-deployment"></a>手動デプロイ 
最初の&2; つのステップは Automation 環境に対して&1; 回だけ実行し、残りのステップは worker コンピューターごとに繰り返します。

#### <a name="1-create-operations-management-suite-workspace"></a>1.Operations Management Suite のワークスペースを作成する
Operations Management Suite のワークスペースがまだない場合は、[ワークスペースのセットアップ](https://technet.microsoft.com/library/mt484119.aspx)に関するページの手順に従って作成します。 既存のワークスペースがある場合は、それを使用できます。

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>手順&2;.Operations Management Suite ワークスペースに Automation ソリューションを追加します。
ソリューションにより、Operations Management Suite に機能が追加されます。  Automation ソリューションは、Hybrid Runbook Worker のサポートなど、Azure Automation 用の機能を追加します。  ソリューションをワークスペースに追加すると、次の手順でインストールする worker コンポーネントがエージェント コンピューターに自動的にプッシュダウンされます。

「 [To add a solution using the Solutions Gallery (ソリューション ギャラリーを使用してソリューションを追加するには)](../log-analytics/log-analytics-add-solutions.md) 」の説明に従って、Operations Management Suite のワークスペースに **Automation** ソリューションを追加します。

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3.Microsoft Monitoring Agent をインストールする
Microsoft Monitoring Agent は Operations Management Suite にコンピューターを接続します。  エージェントをオンプレミスのコンピューターにインストールし、ワークスペースに接続すると、Hybrid Runbook Worker に必要なコンポーネントが自動的にダウンロードされます。

「[Windows コンピューターを Log Analytics に接続する](../log-analytics/log-analytics-windows-agents.md)」の手順に従って、オンプレミスのコンピューターにエージェントをインストールします。  コンピューターごとにこのプロセスを繰り返して、複数の worker を環境に追加できます。

エージェントが Operations Management Suite に正常に接続すると、Operations Management Suite の **[設定]** ウィンドウの **[接続されているソース]** タブにエージェントが表示されます。  C:\Program Files\Microsoft Monitoring Agent\Agent に **AzureAutomationFiles** という名前のフォルダーが作成されていることを調べて、エージェントが Automation ソリューションを正常にダウンロードしたことを確認できます。  Hybrid Runbook Worker のバージョンを確認するには、C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ and note the \\*バージョン* サブフォルダーに移動します。   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4.Runbook 環境をインストールして、Azure Automation に接続する
エージェントを Operations Management Suite に追加すると、Automation ソリューションは、**Add-HybridRunbookWorker** コマンドレットを含む **HybridRegistration** PowerShell モジュールをプッシュダウンします。  コンピューターに Runbook 環境をインストールして、Azure Automation に登録する場合は、このコマンドレットを使用します。

管理者モードで PowerShell セッションを開き、次のコマンドを実行してモジュールをインポートします。

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

次に、以下の構文を使用して、 **Add-HybridRunbookWorker** コマンドレットを実行します。

    Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

このコマンドレットに必要な情報は、Azure ポータルの **[キーの管理]** ブレードから取得できます。  このブレードを開くには、Automation アカウントの **[設定]** ブレードから **[キー]** オプションを選択します。

![Hybrid Runbook Worker の概要](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Name** は、Hybrid Runbook Worker グループの名前です。 Automation アカウントにこのグループが既に存在する場合は、現在のコンピューターがそれに追加されます。  まだ存在しない場合は、追加されます。
* **EndPoint** は、**[キーの管理]** ブレードの **[URL]** フィールドです。
* **Token** は、**[キーの管理]** ブレードの **[プライマリ アクセス キー]** です。  

インストールに関する詳細な情報を受け取るには、**Add-HybridRunbookWorker** で **-Verbose** スイッチを使用します。

#### <a name="5-install-powershell-modules"></a>5.PowerShell モジュールをインストールする
Runbook は、Azure Automation 環境にインストールされているモジュールで定義されているアクティビティとコマンドレットをすべて使用できます。  これらのモジュールはオンプレミス コンピューターに自動的に配置されないため、手動でインストールする必要があります。  ただし、Azure モジュールは既定でインストールされ、すべての Azure サービスのコマンドレットと Azure Automation のアクティビティにアクセスできます。

Hybrid Runbook Worker 機能の主な目的はローカル リソースを管理することであるため、ほとんどの場合、これらのリソースをサポートするモジュールをインストールする必要があります。  Windows PowerShell モジュールのインストールについては、「[モジュールのインストール](http://msdn.microsoft.com/library/dd878350.aspx)」を参照してください。

## <a name="removing-hybrid-runbook-worker"></a>Hybrid Runbook Worker の削除 
要件に応じて、グループから&1; つ以上の Hybrid Runbook Worker を削除したり、グループを削除することができます。  オンプレミス コンピューターから Hybrid Runbook Worker を削除するには、次の手順を実行します。

1. Azure Portal で、Automation アカウントに移動します。  
2. **[設定]** ブレードから、**[キー]** を選択し、フィールドの **[URL]** と **[プライマリ アクセス キー]** の値をメモします。  この情報は、次の手順に必要です。
3. 管理者モードで PowerShell セッションを開き、次のコマンド - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>` を実行します。  削除処理の詳細なログを取得するには、 **-Verbose** スイッチを使用します。

> [!NOTE]
> これによってコンピューターから Microsoft Monitoring Agent が削除されることはありません。Hybrid Runbook Worker ロールの機能と構成のみが削除されます。  

## <a name="remove-hybrid-worker-groups"></a>Hybrid Worker グループを削除する
グループを削除するには、まず、先ほどの手順を使用して、グループのメンバーであるすべてのコンピューターから Hybrid Runbook Worker を削除する必要があります。その後、次の手順を実行してグループを削除します。  

1. Azure ポータルで Automation アカウントを開きます。
2. **[ハイブリッド Worker グループ]** タイルを選択し、**[ハイブリッド Worker グループ]** ブレードで削除するグループを選択します。  特定のグループを選択すると、**[ハイブリッド Worker グループ]** プロパティ ブレードが表示されます。<br> ![Hybrid Runbook Worker グループ ブレード](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. 選択したグループのプロパティ ブレードで、**[削除]** をクリックします。  このアクションの確認を求めるメッセージが表示されるので、続行する場合は **[はい]** をクリックします。<br> ![グループ削除の確認ダイアログ ボックス](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> このプロセスは、完了までに数秒かかる場合があります。進行状況は、メニューの **[通知]** で確認してください。  

## <a name="starting-runbooks-on-hybrid-runbook-worker"></a>Hybrid Runbook Worker での Runbook の開始
[Azure Automation での Runbook の開始](automation-starting-a-runbook.md) 」では、Runbook を開始するためのさまざまな方法を説明しています。  Hybrid Runbook Worker は、Hybrid Runbook Worker グループの名前を指定できる **RunOn** オプションを追加します。  グループが指定されている場合は、Runbook が取得され、そのグループ内のワーカーによって実行されます。  このオプションが指定されていない場合は、Azure Automation で通常どおり実行されます。

Azure ポータルで Runbook を開始する際に、**Azure** または**ハイブリッド worker** を選択できる **[実行場所を選択して実行]** オプションが表示されます。  **Hybrid Worker**を選択した場合は、ドロップダウン リストからグループを選択できます。

**RunOn** パラメーターを使用します。次のコマンドを使用し、Windows PowerShell を使用して MyHybridGroup という名前の Hybrid Runbook Worker グループで Test-Runbook という名前の Runbook を開始できます。

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> The **RunOn** parameter was added to the **Start-AzureAutomationRunbook** cmdlet in version 0.9.1 of Microsoft Azure PowerShell.  以前のバージョンがインストールされている場合は、 [最新バージョンをダウンロード](https://azure.microsoft.com/downloads/) する必要があります。  Windows PowerShell から Runbook を開始するワークステーションでは、このバージョンをインストールするだけです。  ワーカー コンピューターから Runbook を開始する場合を除き、そのコンピューターにインストールする必要はありません。  Automation アカウントに最新バージョンの Azure Powershell をインストールする必要があるため、現時点ではある Runbook を別の Runbook の Hybrid Runbook Worker で開始することはできません。  最新バージョンは Azure Automation で自動的に更新され、自動的にワーカーにすぐにプッシュダウンされます。
>
>

## <a name="runbook-permissions"></a>Runbook のアクセス許可
Hybrid Runbook Worker で実行されている Runbook は Azure 外部のリソースにアクセスするため、Azure のリソースへの認証に Runbook で通常使用される方法と同じものを使用することはできません。  Runbook にローカル リソースに対して独自の認証機能を用意するか、すべての Runbook にユーザー コンテキストを提供する RunAs アカウントを指定することができます。

### <a name="runbook-authentication"></a>Runbook の認証
既定で、Runbook はオンプレミス コンピューターのローカル システム アカウントのコンテキストで実行されるため、アクセスするリソースを独自に認証する必要があります。  

資格情報を指定できるコマンドレットで Runbook の[資格情報](http://msdn.microsoft.com/library/dn940015.aspx)資産と[証明書](http://msdn.microsoft.com/library/dn940013.aspx)資産を使用することで、さまざまなリソースへの認証が可能になります。  次の例は、コンピューターを再起動する Runbook の一部を示しています。  資格情報資産から資格情報を取得し、変数資産からはコンピューター名を取得して、Restart-Computer コマンドレットでこれらの値を使用します。

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

また、[PSCredential 共通パラメーター](http://technet.microsoft.com/library/jj129719.aspx)で指定された資格情報で別のコンピューター上でコード ブロックを実行できるようにする [InlineScript](automation-powershell-workflow.md#inlinescript) を利用することもできます。

### <a name="runas-account"></a>RunAs アカウント
Runbook でローカル リソースへの独自の認証機能を用意するのではなく、ハイブリッド worker グループの **RunAs** アカウントを指定することができます。  ローカル リソースに対するアクセス権を持つ [資格情報資産](automation-credentials.md) を指定すると、グループ内の Hybrid Runbook Worker で Runbook を実行するときに、その資格情報ですべての Runbook が実行されます。  

資格情報のユーザー名は、次の形式にする必要があります。

* ドメイン\ユーザー名
* username@domain
* ユーザー名 (オンプレミス コンピューターのローカルのアカウントの場合)

ハイブリッド worker グループの RunAs アカウントを指定するには、以下の手順を実行します。

1. ローカル リソースに対するアクセス権を持つ [資格情報資産](automation-credentials.md) を作成します。
2. Azure ポータルで Automation アカウントを開きます。
3. **[ハイブリッド Worker グループ]** タイルを選択し、グループを選択します。
4. **[すべての設定]**、**[ハイブリッド Worker グループの設定]** の順に選択します。
5. **[実行者]** を **[既定]** から **[カスタム]** に変更します。
6. 資格情報を選択し、 **[保存]**をクリックします。

## <a name="creating-runbooks-for-hybrid-runbook-worker"></a>Hybrid Runbook Worker の Runbook の作成
Azure Automation で実行される Runbook と、Hybrid Runbook Worker で実行される Runbook の構造に違いはありません。 ただし、通常、Hybrid Runbook Worker の Runbook はデータ センター内のローカル リソースを管理しますが、Azure Automation の Runbook は Azure クラウド内のリソースを管理するため、ほとんどの場合、それぞれで使用する Runbook は大きく異なります。

Azure Automation で Hybrid Runbook Worker 用に Runbook を編集することはできますが、エディターで Runbook のテストを試行する場合は困難な場合があります。  ローカル リソースにアクセスする PowerShell モジュールを Azure Automation 環境にインストールできない場合があります。その場合、テストは失敗します。  必要なモジュールをインストールすれば、Runbook は実行されますが、ローカル リソースにアクセスして完全なテストを行うことはできません。

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Hybrid Runbook Worker での Runbook のトラブルシューティング
[Runbook の出力とメッセージ](automation-runbook-output-and-messages.md) は Hybrid Worker から Azure Automation に送られます。  他の Runbook と同じ方法で、Verbose および Progress ストリームも有効にできます。  

ログは、各ハイブリッド worker の C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes にローカルに格納されます。

Runbook が正常に完了せず、ジョブの概要で状態が **中断**になっている場合は、トラブルシューティングの記事「 [Hybrid Runbook Worker: Runbook ジョブが中断状態で終了する](automation-troubleshooting-hrw-runbook-terminates-suspended.md)」を参照してください。   

## <a name="relationship-to-service-management-automation"></a>Service Management Automation との関係
[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) では、ローカル データ センターの Azure Automation でサポートされているものと同じ Runbook を実行することができます。 Windows Azure Pack には SMA 管理用のグラフィカル インターフェイスが含まれているため、通常 SMA は、Windows Azure パックと一緒にデプロイされます。 Azure Automation とは異なり、SMA には API をホストする Web サーバー、Runbook と SMA の構成を保持するためのデータベース、およびRunbook ジョブを実行する Runbook Worker を含むローカル インストールが必要です。 Azure Automation ではクラウドでこれらのサービスが提供されるため、必要なのはローカル環境での Hybrid Runbook Worker の維持のみです。

既存の SMA ユーザーの場合は、何も変更しなくても Hybrid Runbook Worker で使用する Azure Automation に Runbook を移動できます。ただし、「[Hybrid Runbook Worker の Runbook の作成](#creating-runbooks-for-hybrid-runbook-worker)」の説明に従って、リソースを独自に認証することが前提となります。  SMA の Runbook は、Runbook の認証を提供できるワーカー サーバーのサービス アカウントのコンテキストで実行されます。

Hybrid Runbook Worker 機能を持つ Azure Automation と Service Management Automation のどちらがより要件に合っているかを判断するために、次の条件を使用できます。

* グラフィカル管理インターフェイスが必要な場合、SMA には Windows Azure パックに接続されている、基になるコンポーネントのローカル インストールが必要です。 ローカル Runbook ワーカーにインストールされているエージェントのみを必要とする Azure Automation よりも、ローカル リソースとメンテナンス コストがかかります。 エージェントは Operations Management Suite によって管理され、メンテナンス コストがより削減されます。
* Azure Automation はその Runbook をクラウド内に格納し、オンプレミスの Hybrid Runbook Worker に配信します。 セキュリティ ポリシーでこの動作が許可されていない場合は、SMA を使用する必要があります。
* SMA は System Center に付属するため、System Center 2012 R2 のライセンスが必要です。 Azure Automation は、階層化されたサブスクリプション モデルに基づきます。
* Azure Automation には、SMA で使用できないグラフィカル Runbook などの拡張機能があります。

## <a name="next-steps"></a>次のステップ
* Runbook を開始するために使用できるさまざまな方法の詳細については、「[Azure Automation での Runbook の開始](automation-starting-a-runbook.md)」を参照して下さい。  
* テキスト エディターを使用して、Azure Automation で PowerShell Runbook と PowerShell ワークフロー Runbook を操作するためのさまざまな手順については、[Azure Automation での Runbook の編集](automation-edit-textual-runbook.md)を参照してください。

