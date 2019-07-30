---
title: Azure の Update Management ソリューション
description: この記事では、Azure Update Management ソリューションを使用して Windows および Linux コンピューターの更新プログラムを管理する方法について説明します。
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3bfec413430de588be6c4423702d41779a8426d0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477978"
---
# <a name="update-management-solution-in-azure"></a>Azure の Update Management ソリューション

Azure Automation の Update Management ソリューションを使用すると、Azure、オンプレミスの環境、またはその他のクラウド プロバイダーで、Windows コンピューターと Linux コンピューターに対してオペレーティング システムの更新プログラムを管理できます。 すべてのエージェント コンピューターで利用可能な更新プログラムの状態をすばやく評価し、サーバーに必要な更新プログラムをインストールするプロセスを管理できます。

仮想マシンの Update Management は、Azure Automation アカウントから直接有効にすることができます。 Automation アカウントから仮想マシンの Update Management を有効にする方法については、[複数の仮想マシンの更新管理](manage-update-multi.md)に関するページを参照してください。 また、Azure portal の仮想マシン ページから仮想マシンの Update Management を有効にすることもできます。 このシナリオは、[Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) および [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) の仮想マシンに対して使用できます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>ソリューションの概要

Update Management で管理されるコンピューターでは、評価と更新プログラムのデプロイに次の構成を使用します。

* Windows または Linux 用の Microsoft Monitoring Agent (MMA)
* PowerShell Desired State Configuration (DSC) (Linux の場合)
* Automation Hybrid Runbook Worker
* Microsoft Update または Windows Server Update Services (WSUS) (Windows コンピューターの場合)

次の図は、動作とデータ フローの概念図です。ワークスペース内の接続された Windows Server と Linux コンピューターすべてがこのソリューションによってどのように評価され、セキュリティ更新プログラムが適用されるかを示しています。

![Update Management プロセスのフロー](./media/automation-update-management/update-mgmt-updateworkflow.png)

Update Management を使用して、同じテナント内の複数のサブスクリプションにマシンをネイティブにオンボードできます。

パッケージがリリースされた後、Linux マシンの評価用に修正プログラムが表示されるまで 2、3 時間かかります。 Windows マシンの場合、リリースされてから評価用に修正プログラムが表示されるまで 12 ～ 15 時間かかります。

コンピューターが更新プログラムのコンプライアンスを確認するためにスキャンを完了した後、エージェントによって情報が Azure Monitor ログに一括転送されます。 Windows コンピューターでは、コンプライアンス スキャンは既定で 12 時間ごとに実行されます。

このスキャン スケジュールに加えて、MMA の再起動後 15 分以内、更新プログラムのインストール前、および更新プログラムのインストール後に、更新プログラムのコンプライアンスを確認するためのスキャンが開始されます。

Linux コンピューターでは、コンプライアンス スキャンは既定で 1 時間ごとに実行されます。 MMA エージェントを再起動した場合は、コンプライアンス スキャンは 15 分以内に開始されます。

このソリューションは、同期先として構成されたソースに基づいて、コンピューターがどの程度最新の状態であるかをレポートします。 Windows コンピューターが WSUS にレポートするよう構成されている場合、WSUS が Microsoft Update と最後に同期したタイミングによっては、その結果が Microsoft Updates の示す内容と一致しない場合があります。 この動作は、パブリック リポジトリではなくローカル リポジトリにレポートするよう構成されている Linux コンピューターも同様です。

> [!NOTE]
> サービスに正しく報告するためには、Update Management で、特定の URL とポートを有効にする必要があります。 これらの要件の詳細については、[Hybrid Worker のためのネットワーク計画](automation-hybrid-runbook-worker.md#network-planning)に関する記事を参照してください。

更新が必要なコンピューターへのソフトウェア更新プログラムのデプロイとインストールに、スケジュールされたデプロイを使用できます。 Windows コンピューターの場合、"*オプション*" に分類されている更新プログラムはデプロイの範囲に含まれません。 デプロイの範囲には、必須の更新プログラムのみが含まれています。

スケジュールされたデプロイでは、適用可能な更新プログラムを受け取る対象コンピューターを定義する際に、コンピューターを明示的に指定するか、特定のコンピューター セットのログ検索、または指定した条件に基づいて動的に Azure VM を選択する [Azure クエリ](#azure-machines)に基づいた[コンピューター グループ](../azure-monitor/platform/computer-groups.md)を選択します。 これらのグループは[スコープ構成](../azure-monitor/insights/solution-targeting.md)とは異なります。これは、ソリューションを有効にする管理パックを取得するマシンを決定するためにのみ使用されます。 

また、スケジュールを指定するときは、更新プログラムのインストールを許可する期間を承認し、設定します。 この期間は、メンテナンス期間と呼ばれます。 再起動が必要な場合、適切な再起動オプションを選択していれば、再起動のために 10 分間のメンテナンス期間が予約されます。 パッチ適用に予想よりも時間がかかり、メンテナンス期間の残りが 10 分を切った場合、再起動は行われません。

更新プログラムは、Azure Automation の Runbook によってインストールされます。 これらの Runbook は表示できません。また、これらは構成不要です。 更新プログラムのデプロイを作成すると、対象に含めたコンピューターに対して、指定した時間にマスター更新 Runbook を開始するスケジュールが作成されます。 このマスター Runbook は、必須の更新プログラムをインストールする子 Runbook を各エージェントで開始します。

更新プログラムのデプロイで指定した日時に、対象のコンピューターでデプロイが並列で実行されます。 まず、スキャンが実行され、その更新プログラムが依然として必須であることが確認されてからインストールされます。 WSUS クライアント コンピューターの場合、更新プログラムが WSUS で承認されていないと更新プログラムのデプロイは失敗します。

1 つのマシンを複数の Log Analytics ワークスペースで Update Management 用に登録すること (マルチホーム) は、サポートされていません。

## <a name="clients"></a>クライアント

### <a name="supported-client-types"></a>サポートされているクライアントの種類

次の表は、サポートされているオペレーティング システムの一覧です。

|オペレーティング システム  |メモ  |
|---------|---------|
|Windows Server 2008、Windows Server 2008 R2 RTM    | 更新プログラムの評価のみをサポートします。         |
|Windows Server 2008 R2 SP1 以降 (Windows Server 2012 および 2016 を含む)    |.NET Framework 4.5.1 以降が必要です。 ([.NET Framework のダウンロード](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 以降が必要です。 ([WMF 4.0 のダウンロード](https://www.microsoft.com/download/details.aspx?id=40855))。<br/> より高い信頼性を確保するには Windows PowerShell 5.1 を使用することをお勧めします  ([WMF 5.1 のダウンロード](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) および 7 (x64)      | Linux エージェントは、更新リポジトリへのアクセスが必要です。 分類に基づく修正プログラムでは、CentOS に既定では設定されていない、セキュリティ データを返すための "yum" が必須です。 分類に基づく CentOS への修正プログラムの適用の詳細については、[Linux での分類の更新](#linux-2)に関するページを参照してください。          |
|Red Hat Enterprise 6 (x86/x64) および 7 (x64)     | Linux エージェントは、更新リポジトリへのアクセスが必要です。        |
|SUSE Linux Enterprise Server 11 (x86/x64) および 12 (x64)     | Linux エージェントは、更新リポジトリへのアクセスが必要です。        |
|Ubuntu 14.04 LTS、16.04 LTS、18.04 (x86/x64)      |Linux エージェントは、更新リポジトリへのアクセスが必要です。         |

### <a name="unsupported-client-types"></a>サポートされていないクライアントの種類

次の表は、サポートされていないオペレーティング システムの一覧です。

|オペレーティング システム  |メモ  |
|---------|---------|
|Windows クライアント     | クライアント オペレーティング システム (Windows 7 や Windows 10 など) はサポートされません。        |
|Windows Server 2016 Nano Server     | サポートされていません。       |

### <a name="client-requirements"></a>クライアントの要件

#### <a name="windows"></a>Windows

Windows エージェントは、WSUS サーバーと通信するように構成するか、Microsoft Update にアクセスできる必要があります。 System Center Configuration Manager と Update Management を統合して使用できます。 統合シナリオの詳細については、「[System Center Configuration Manager と Update Management の統合](oms-solution-updatemgmt-sccmintegration.md#configuration)」を参照してください。 [Windows エージェント](../azure-monitor/platform/agent-windows.md)が必要です。 Azure 仮想マシンのオンボードを実行すると、このエージェントは自動的にインストールされます。

#### <a name="linux"></a>Linux

Linux コンピューターには、更新リポジトリへのアクセスが必要です。 プライベートまたはパブリックの更新リポジトリが使用できます。 Update Management と対話するには、TLS 1.1 または TLS 1.2 が必要です。 このソリューションでは、Linux 用 Log Analytics エージェントが複数の Azure Log Analytics ワークスペースにレポートする構成はサポートされていません。

Linux 用 Log Analytics エージェントをインストールして最新バージョンをダウンロードする方法の詳細については、[Linux 用 Log Analytics エージェント](https://github.com/microsoft/oms-agent-for-linux)に関するページを参照してください。 Windows 用 Log Analytics エージェントをインストールする方法の詳細については、[Microsoft Monitoring Agent for Windows](../log-analytics/log-analytics-windows-agent.md) に関するページを参照してください。

## <a name="permissions"></a>アクセス許可

更新プログラムのデプロイを作成および管理するには、特定のアクセス許可が必要です。 これらのアクセス許可の詳細については、[Update Management へのロールベースのアクセス](automation-role-based-access-control.md#update-management)に関するページをご覧ください。

## <a name="solution-components"></a>ソリューションのコンポーネント

このソリューションは、次のリソースで構成されています。 リソースは、Automation アカウントに追加されます。 これらは、直接接続しているエージェントであるか、または Operations Manager に接続された管理グループの中に存在します。

### <a name="hybrid-worker-groups"></a>ハイブリッド worker グループ

このソリューションを有効にすると、ソリューションに含まれている Runbook をサポートするために、Log Analytics ワークスペースに直接接続された Windows コンピューターが自動的に Hybrid Runbook Worker として構成されます。

ソリューションで管理されている各 Windows コンピューターは、Automation アカウントの **[システム ハイブリッド worker グループ]** として、 **[ハイブリッド Worker グループ]** ページに表示されます。 ソリューションは、*Hostname FQDN_GUID* の名前付け規則を使用します。 アカウントの Runbook でこれらのグループを対象として指定することはできません。 指定すると失敗します。 これらのグループは、管理ソリューションをサポートすることのみを目的としています。

このソリューションと Hybrid Runbook Worker グループ メンバーシップの両方に同じアカウントを使用すると、Windows コンピューターを Automation アカウントの Hybrid Runbook Worker グループに追加して Automation Runbook をサポートすることができます。 この機能は、Hybrid Runbook Worker のバージョン 7.2.12024.0 で追加されました。

### <a name="management-packs"></a>管理パック

System Center Operations Manager 管理グループが Log Analytics ワークスペースに接続されている場合は、以下の管理パックが Operations Manager にインストールされます。 これらの管理パックは、このソリューションを追加した後、直接接続された Windows コンピューターにもインストールされます。 これらの管理パックを構成または管理する必要はありません。

* Microsoft System Center Advisor 更新プログラム評価インテリジェンス パック (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新プログラムの展開 MP

> [!NOTE]
> ワークスペースに関連付けられるように管理グループ レベルでエージェントが構成されている Operations Manager 1807 管理グループがある場合、それらを表示するための現在の回避策としては、**Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** ルールで **IsAutoRegistrationEnabled** を **True** にオーバーライドします。

ソリューション管理パックの更新方法の詳細については、[Azure Monitor ログへの Operations Manager の接続](../azure-monitor/platform/om-agents.md)に関するページを参照してください。

> [!NOTE]
> Operations Manger エージェントを使用しているシステムを Update Management によって完全に管理できるようにするには、エージェントを Microsoft Monitoring Agent に更新する必要があります。 エージェントを更新する方法については、[Operations Manager エージェントのアップグレード方法](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)に関する記事を参照してください。 Operations Manager を使用する環境では、System Center Operations Manager 2012 R2 UR 14 以降を実行している必要があります。

## <a name="onboard"></a>Update Management の有効化

システムへの修正プログラムの適用を開始するには、Update Management ソリューションを有効にする必要があります。 Update Management にマシンをオンボードする方法は多数あります。 以下に、推奨およびサポートされている、ソリューションにオンボードする方法を示します。

* [仮想マシンから](automation-onboard-solutions-from-vm.md)
* [複数のマシンを参照することから](automation-onboard-solutions-from-browse.md)
* [お使いの Automation アカウントから](automation-onboard-solutions-from-automation-account.md)
* [Azure Automation Runbook によって](automation-onboard-solutions.md)
  
### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Azure 以外のマシンが配布準備済みであることを確認する

直接接続されたマシンが Azure Monitor ログと通信していることを確認するには、数分経ってから、次のログ検索を実行します。

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Windows コンピューターでは、次の情報を調べて、Azure Monitor ログとエージェントの接続を確認できます。

1. [コントロール パネル] から **[Microsoft Monitoring Agent]** を開きます。 **[Azure Log Analytics]** タブで、エージェントに"**Microsoft Monitoring Agent は Log Analytics に正常に接続しました**" というメッセージが表示されます。
2. Windows イベント ログを開きます。 **アプリケーションとサービス ログ\Operations Manager** に移動して、ソースの **[サービス コネクタ]** でイベント ID 3000 および 5002 を検索します。 これらのイベントは、コンピューターが Log Analytics ワークスペースに登録され、構成を受信していることを示しています。

エージェントが Azure Monitor ログと通信できず、ファイアウォールまたはプロキシ サーバーを介してインターネットと通信するよう構成されている場合は、ファイアウォールまたはプロキシ サーバーが正しく構成されていることを確認します。 ファイアウォールまたはプロキシ サーバーが正しく構成されていることを確認する方法については、[Windows エージェントのネットワーク構成](../azure-monitor/platform/agent-windows.md)または [Linux エージェントのネットワーク構成](../log-analytics/log-analytics-agent-linux.md)に関する記事を参照してください。

> [!NOTE]
> Linux システムがプロキシまたは Log Analytics ゲートウェイと通信するよう構成されており、このソリューションの配布準備を行っている場合は、次のコマンドを実行し、ファイルに対する読み取り権限を omiuser グループに付与するよう、*proxy.conf* のアクセス許可を更新します。
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

新しく追加された Linux エージェントは、評価が完了した後、状態が "**更新済み**" と表示されます。 このプロセスには最大で 6 時間かかります。

Operations Manager 管理グループが Azure Monitor ログと通信していることを確認する方法については、[Operations Manager と Azure Monitor ログの統合の検証](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)に関するページを参照してください。

## <a name="data-collection"></a>データ収集

### <a name="supported-agents"></a>サポートされているエージェント

次の表では、このソリューションでサポートされている接続先ソースについて説明します。

| 接続先ソース | サポートされています | 説明 |
| --- | --- | --- |
| Windows エージェント |はい |ソリューションは、Windows エージェントからシステムの更新プログラムに関する情報を収集し、必要な更新プログラムのインストールを開始します。 |
| Linux エージェント |はい |ソリューションは、Linux エージェントからシステムの更新プログラムに関する情報を収集し、サポート対象のディストリビューションに対して必要な更新プログラムのインストールを開始します。 |
| Operations Manager 管理グループ |はい |ソリューションは、接続された管理グループ内のエージェントからシステムの更新プログラムに関する情報を収集します。<br/>Operations Manager エージェントから Azure Monitor ログへの直接接続は必要ありません。 データは管理グループから Log Analytics ワークスペースに転送されます。 |

### <a name="collection-frequency"></a>収集の頻度

管理対象の各 Windows コンピューターでは、1 日 2 回スキャンが実行されます。 15 分ごとに Windows API が呼び出され、最後の更新時間を照会することで、状態が変化したかどうかが確認されます。 状態が変更された場合、コンプライアンス スキャンが開始されます。

各マネージド Linux コンピューターでは、1 時間ごとにスキャンが実行されます。

管理対象のコンピューターの更新されたデータがダッシュボードに表示されるまでに、30 分～ 6 時間かかる場合があります。

Update Management を使用しているマシンでの Azure Monitor ログの平均データ使用量は、1 か月あたり約 25 MB です。 この値は概数にすぎず、環境によって異なる可能性があります。 お使いの環境を監視し、実際に必要な正確な使用量を確認することをお勧めします。

## <a name="viewing-update-assessments"></a>更新の評価を表示する

Automation アカウントで **[Update Management]** をクリックすると、使用しているマシンの状態が表示されます。

このビューには、ご利用のマシン、不足している更新プログラム、更新プログラムのデプロイ、およびスケジュールされている更新プログラムのデプロイに関する情報が表示されます。 **[対応]** 列では、マシンが評価された最終時刻を確認できます。 **[エージェントの更新の準備]** 列では、更新エージェントの正常性を確認できます。 問題がある場合は、問題を解決するために必要な手順が記載されたトラブルシューティング ドキュメントへのリンクを選択してください。

マシン、更新プログラム、またはデプロイに関する情報を返すログ検索を実行するには、一覧から項目を選択します。 これにより、項目のクエリが選択された状態で **[ログ検索]** ページが開きます。

![Update Management の既定のビュー](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>更新プログラムをインストールする

ご利用のワークスペースにある Linux コンピューターと Windows コンピューターのすべてで更新プログラムが評価されたら、"*更新プログラムのデプロイ*" を作成して、必要な更新プログラムをインストールできます。 更新プログラムのデプロイを作成するには、Automation アカウントに対する書き込みアクセス権と、デプロイで対象とされる Azure VM に対する書き込みアクセス権が必要です。 更新プログラムのデプロイとは、1 台以上のコンピューターに対して、必要な更新プログラムをスケジュールに従ってインストールすることです。 デプロイの範囲に含めるコンピューターまたはコンピューター グループと、デプロイの日時を指定します。 コンピューター グループの詳細については、[Azure Monitor ログのコンピューター グループ](../azure-monitor/platform/computer-groups.md)に関するページを参照してください。

更新プログラムのデプロイにコンピューター グループを含めると、スケジュールの作成時にグループ メンバーシップが 1 回だけ評価されます。 その後で加えられたグループへの変更は反映されません。 このような[動的グループ](#using-dynamic-groups)の使用を回避するため、これらのグループはデプロイ時に解決され、Azure VM に対するクエリまたは Azure 以外の VM に関する保存された検索によって定義されます。

> [!NOTE]
> Azure Marketplace からデプロイされた Windows 仮想マシンは、既定で Windows Update Service から自動更新を受信するように設定されています。 このソリューションまたは Windows 仮想マシンをワークスペースに追加しても、この動作は変わりません。 このソリューションで更新プログラムを能動的に管理しない場合は、既定の動作 (更新プログラムが自動的に適用される) が適用されます。

Ubuntu でメンテナンス期間外に更新プログラムが適用されないようにするには、無人アップグレード パッケージを再構成して自動更新を無効にします。 パッケージの構成方法については、[Ubuntu サーバー ガイドの自動更新に関するトピック](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)をご覧ください。

Azure Marketplace から利用できるオンデマンドの Red Hat Enterprise Linux (RHEL) イメージから作成した仮想マシンは、Azure にデプロイされた [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) にアクセスするよう登録されています。 その他の Linux ディストリビューションは、サポートされている方法に従って、ディストリビューション オンライン ファイル リポジトリから更新する必要があります。

新しい更新プログラムのデプロイを作成するには、 **[更新プログラムの展開のスケジュール]** を選択します。 **[新しい更新プログラムの展開]** ページが開きます。 次の表で説明されているプロパティの値を入力し、 **[作成]** をクリックします。

| プロパティ | Description |
| --- | --- |
| Name |更新プログラムの展開を識別する一意の名前。 |
|オペレーティング システム| Linux または Windows|
| 更新するグループ |Azure マシンの場合、サブスクリプション、リソース グループ、場所、およびタグの組み合わせに基づいてクエリを定義し、デプロイに含める Azure VM の動的グループを構築します。 </br></br>Azure 以外のマシンの場合、既存の保存された検索を選択して、デプロイに含める Azure 以外のマシンのグループを選択します。 </br></br>詳しくは、[動的グループ](automation-update-management.md#using-dynamic-groups)に関するページをご覧ください。|
| 更新するマシン |保存した検索条件、インポートしたグループを選択するか、ドロップダウンから [マシン] を選択し、個別のマシンを選択します。 **[マシン]** を選択すると、マシンの準備状況が **[エージェントの更新の準備]** 列に示されます。</br> Azure Monitor ログでコンピューター グループを作成するさまざまな方法については、[Azure Monitor ログのコンピューター グループ](../azure-monitor/platform/computer-groups.md)に関するページを参照してください |
|更新プログラムの分類|必要な更新プログラムの分類すべてを選択します|
|更新プログラムの包含/除外|**[包含/除外]** ページが開きます。 含めるまたは除外する更新プログラムは別のタブに表示されます。 包含を処理する方法について詳しくは、[包含の動作](automation-update-management.md#inclusion-behavior)に関するページをご覧ください。 |
|スケジュール設定|開始する時刻を選択し、繰り返しの設定として、[1 回] または [定期的] のいずれかを選択します|
| 事前スクリプトと事後スクリプト|デプロイの前後に実行するスクリプトを選択します|
| メンテナンス期間 |更新プログラムに対して設定された分数です。 30 分未満の値を指定することはできません。また、6 時間を超えることはできません |
| 再起動制御| 再起動の処理方法を決定します。 使用できるオプションは次のとおりです。</br>必要に応じて再起動 (既定値)</br>常に再起動</br>再起動しない</br>Only reboot - will not install updates (再起動のみ - 更新プログラムをインストールしない)|

更新プログラムのデプロイはプログラムで作成することもできます。 REST API を使用して更新プログラムのデプロイを作成する方法については、「[Software Update Configurations - Create](/rest/api/automation/softwareupdateconfigurations/create)」(ソフトウェア更新プログラムの構成 - 作成) をご覧ください。 週単位の更新プログラムのデプロイを作成するために使用できるサンプル Runbook もあります。 この Runbook について詳しくは、「[Create a weekly update deployment for one or more VMs in a resource group](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)」(リソース グループ内の VM に対して週単位の更新プログラムのデプロイを作成する) をご覧ください。

### <a name="multi-tenant"></a>テナント間の更新プログラムのデプロイ

修正プログラムを適用する必要がある Update Management に報告する別の Azure テナントにマシンが存在する場合は、次の対処法を使用して、スケジュールを設定する必要があります。 スケジュールは、[New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) コマンドレットと `-ForUpdate` スイッチを使用して作成できます。[New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) コマンドレットを使用する際、`-NonAzureComputer` パラメーターに他のテナントのマシンを渡すことができます。 以下の例は、その方法を示しています。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="view-missing-updates"></a>不足している更新プログラムを表示する

**[不足している更新プログラム]** をクリックすると、ご利用のマシンで不足している更新プログラムの一覧が表示されます。 各更新プログラムが表示され、選択することができます。 更新プログラムを必要とするマシンの数やオペレーティング システムに関する情報、および詳細情報にアクセスするためのリンクが表示されます。 **[ログ検索]** ウィンドウには更新プログラムに関する詳細情報が表示されます。

## <a name="view-update-deployments"></a>更新プログラムのデプロイを表示する

**[更新プログラムの展開]** タブをクリックすると、既存の更新プログラムのデプロイの一覧が表示されます。 表で更新プログラムのデプロイのいずれかをクリックすると、その更新プログラムのデプロイの **[更新プログラムの展開の実行]** ページが開きます。 ジョブのログは、最大 30 日間保存されます。

![更新プログラムのデプロイ結果の概要](./media/automation-update-management/update-deployment-run.png)

REST API から更新プログラムのデプロイを表示する方法については、「[Software Update Configuration Runs](/rest/api/automation/softwareupdateconfigurationruns)」(ソフトウェア更新プログラムの構成の実行) をご覧ください。

## <a name="update-classifications"></a>更新プログラムの分類

次の表は、Update Management の更新プログラムの分類と、各分類の定義を示します。

### <a name="windows"></a>Windows

|分類  |説明  |
|---------|---------|
|緊急更新プログラム     | セキュリティに関連しない重大なバグを修正する、特定の問題に対する更新プログラムです。        |
|セキュリティ更新プログラム     | 製品固有のセキュリティに関連する問題に対する更新プログラムです。        |
|更新プログラムのロールアップ     | 容易なデプロイのためにパッケージにまとめられた修正プログラムの累積セットです。        |
|Feature Pack     | 製品リリース外で配布される製品の新機能です。        |
|Service Pack     | アプリケーションに適用される修正プログラムの累積セットです。        |
|定義の更新     | ウイルスまたは他の定義ファイルに対する更新プログラムです。        |
|ツール     | 1 つまたは複数のタスクを完了するのに役立つユーティリティまたは機能です。        |
|更新プログラム     | 現在インストールされているアプリケーションまたはファイルに対する更新プログラムです。        |

### <a name="linux"></a>Linux

|分類  |説明  |
|---------|---------|
|緊急更新プログラムとセキュリティ更新プログラム     | 特定の問題または製品固有のセキュリティに関連する問題に対する更新プログラムです。         |
|他の更新プログラム     | 本質的に重要ではない、またはセキュリティ更新プログラムではない、他のすべての更新プログラムです。        |

Linux の場合、クラウドでのデータ エンリッチメントにより、Update Management は、評価データを表示しながら、重要な更新プログラムとセキュリティ更新プログラムを識別できます。 修正プログラムの場合、Update Management はマシン上にある分類データを使用します。 他のディストリビューションとは異なり CentOS では、既定ではこの情報は使用できません。 CentOS マシンで、次のコマンドに対しセキュリティ データを返すように構成されている場合、Update Management は分類に基づいて修正プログラムを適用できます。

```bash
sudo yum -q --security check-update
```

CentOS 上でネイティブ分類データを使用できるようにするためのサポートされている方法はありません。 現時点では、独自の方法で分類データを使用するお客様には、できる範囲内のサポートのみを提供しています。

## <a name="firstparty-predownload"></a>詳細設定

Update Management は、Windows の更新プログラムのダウンロードとインストールに Windows Update を使用しています。 そのため、Windows Update で使用される多くの設定を尊重しています。 Windows 以外の更新プログラムを有効にする設定を使用している場合、Update Management では、それらの更新プログラムも管理されます。 更新プログラムの展開が行われる前の更新プログラムのダウンロードを有効にすると、更新プログラムの展開が速くなり、メンテナンス期間を超過する可能性が低くなります。

### <a name="pre-download-updates"></a>更新プログラムの事前ダウンロード

グループ ポリシーで更新プログラムを自動的にダウンロードするように構成するには、[[自動更新を構成する]](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) 設定を **[3]** に設定します。 これで、必要な更新プログラムはバックグラウンドでダウンロードされますが、インストールされません。 そのため、Update Management のスケジュールを管理しながら、更新プログラムの管理のメンテナンス期間以外に更新プログラムをダウンロードできます。 この方法で Update Management の "**メンテナンス期間を超過しました**" エラーを防ぐことができます。

この設定は PowerShell で行うこともできます。更新プログラムを自動ダウンロードするシステム上で、次の PowerShell を実行してください。

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

### <a name="disable-automatic-installation"></a>自動インストールを無効化する

Azure VM では、更新プログラムの自動インストールが既定で有効になっています。 そのため、更新プログラムは、Update Management によってインストールされるようにユーザーがスケジュールする前に、インストールされる可能性があります。 この動作は、`NoAutoUpdate` レジストリ キーを `1` に設定すると無効化できます。 次の PowerShell スニペットは、この操作を行う 1 つの方法を示しています。

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

### <a name="enable-updates-for-other-microsoft-products"></a>他の Microsoft 製品の更新プログラムを有効にする

既定では、Windows Update は Windows の更新プログラムのみを提供します。 **[Windows の更新時に他の Microsoft 製品の更新プログラムも入手します]** をオンにすると、SQL Server や他のファースト パーティ ソフトウェアのセキュリティ修正プログラムなど、他の製品の更新プログラムも提供されます。 このオプションをグループ ポリシーで構成することはできません。 他のファースト パーティの修正プログラムを有効にするシステム上で次の PowerShell を実行すると、Update Management はその設定に従います。

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="third-party"></a>Windows でのサード パーティの修正プログラム

Update Management は、サポート対象の Windows システムへの修正プログラムの適用を、ローカルに構成された更新リポジトリに依存しています。 これは、WSUS または Windows Update のいずれかです。 [System Center Updates Publisher](/sccm/sum/tools/updates-publisher
) (Updates Publisher) などのツールを使用すれば、カスタム更新プログラムを WSUS に公開できます。 このシナリオでは、サード パーティ製ソフトウェアで System Center Configuration Manager を更新リポジトリとして使用するマシンに、Update Management で修正プログラムを適用できます。 Updates Publisher を構成する方法については、「[Install Updates Publisher](/sccm/sum/tools/install-updates-publisher)」(Updates Publisher のインストール) を参照してください。

## <a name="ports"></a>ネットワークの計画

Update Management には次のアドレスが明示的に必要です。 このアドレスへの通信は、ポート 443 を使用して行われます。

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

Hybrid Runbook Worker で必要なポートの詳細については、[ハイブリッド worker ロールのポート](automation-hybrid-runbook-worker.md#hybrid-worker-role)に関するページをご覧ください。

例外を定義するときは、一覧に示されているアドレスを使用することをお勧めします。 [Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)の IP アドレスをダウンロードできます。 このファイルは毎週更新され、現在デプロイされている範囲と今後変更される IP 範囲が反映されます。

## <a name="search-logs"></a>検索ログ

Azure Portal で提供されている詳細情報に加え、ログに対する検索を実行できます。 ソリューション ページで **[Log Analytics]** を選択します。 **[ログ検索]** ウィンドウが開きます。

クエリをカスタマイズする方法や、さまざまなクライアントから使用する方法などについては、[Log Analytics の検索 API のドキュメント](
https://dev.loganalytics.io/)を参照してください。

### <a name="sample-queries"></a>サンプル クエリ

以下のセクションは、このソリューションによって収集された更新レコードのログ クエリの例です。

#### <a name="single-azure-vm-assessment-queries-windows"></a>単一の Azure VM 評価クエリ (Windows)

VMUUID 値を、クエリの対象である仮想マシンの VM GUID に置き換えます。 Azure Monitor ログで次のクエリを実行することで、使用する必要がある VMUUID を確認できます。`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>不足している更新プログラムの概要

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>不足している更新プログラム一覧

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>単一の Azure VM 評価クエリ (Linux)

Linux のディストリビューションによっては、Azure Resource Manager に由来する VMUUID 値と、Azure Monitor ログに格納されている値との間で[エンディアン](https://en.wikipedia.org/wiki/Endianness)が一致しない場合があります。 次のクエリは、いずれかのエンディアンでの一致をチェックします。 結果を適切に返すために、VMUUID 値を GUID のビッグエンディアン形式とリトルエンディアン形式に置き換えます。 Azure Monitor ログで次のクエリを実行することで、使用する必要がある VMUUID を確認できます。`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>不足している更新プログラムの概要

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>不足している更新プログラム一覧

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>マルチ VM の評価クエリ

##### <a name="computers-summary"></a>コンピューターの概要

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>不足している更新プログラムの概要

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="computers-list"></a>コンピューター一覧

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>不足している更新プログラム一覧

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="using-dynamic-groups"></a>動的グループの使用

Update Management では、Azure または Azure 以外の VM の動的グループを更新プログラムのデプロイの対象にする機能が提供されています。 これらのグループはデプロイ時に評価されるため、マシンを追加するためにデプロイを編集する必要はありません。

> [!NOTE]
> 更新プログラムのデプロイを作成するには、適切なアクセス許可が必要です。 詳細については、「[更新プログラムをインストールする](#install-updates)」を参照してください。

### <a name="azure-machines"></a>Azure マシン

これらのグループはクエリによって定義され、更新プログラムのデプロイが開始するときに、そのグループのメンバーが評価されます。 動的なグループは、クラシック VM では動作しません。 クエリを定義するときに、次の項目をまとめて使用して動的グループを設定できます。

* サブスクリプション
* リソース グループ
* Locations
* Tags

![グループを選択する](./media/automation-update-management/select-groups.png)

動的グループの結果をプレビューするには、 **[プレビュー]** ボタンをクリックします。 このプレビューでは、その時点でのグループのメンバーシップが表示されます。この例では、タグ **Role** が **BackendServer** に等しいマシンを検索しています。 このタグを持つマシンがさらに追加された場合、そのグループに対する将来のデプロイに追加されます。

![グループをプレビューする](./media/automation-update-management/preview-groups.png)

### <a name="non-azure-machines"></a>Azure 以外のマシン

Azure 以外のマシンの場合、動的グループの作成にコンピューター グループが使用されるため、保存された検索も参照されます。 保存された検索の作成方法については、「[コンピューター グループの作成](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)」を参照してください。 グループを作成した後は、保存された検索の一覧からそのグループを選択できます。 **[プレビュー]** をクリックすると、保存された検索でのその時点でのコンピューターをプレビューできます。

![グループを選択する](./media/automation-update-management/select-groups-2.png)

## <a name="integrate-with-system-center-configuration-manager"></a>System Center Configuration Manager との統合

PC、サーバー、モバイル デバイスを管理するために System Center Configuration Manager に投資してきたお客様は、Configuration Manager の強みと成熟度を活用してソフトウェア更新プログラムを管理できます。 Configuration Manager は、そのソフトウェア更新プログラムの管理 (SUM) サイクルの一部です。

管理ソリューションを System Center Configuration Manager と統合する方法については、「[System Center Configuration Manager と Update Management の統合](oms-solution-updatemgmt-sccmintegration.md)」を参照してください。

## <a name="inclusion-behavior"></a>包含の動作

更新プログラムの包含では、適用する特定の更新プログラムを指定できます。 包含されている修正プログラムまたはパッケージがインストールされます。 修正プログラムまたはパッケージが包含されていて、分類も選択されていると、包含されるアイテムと分類に一致するアイテムの両方がインストールされます。

包含より除外の方が優先されることを知っておく必要があります。 たとえば、除外ルール `*` を定義した場合、修正プログラムまたはパッケージはすべて除外されるためインストールされません。 除外されたパッチは、マシンに不足しているものとして表示されます。 Linux マシンでは、包含されているパッケージに除外された依存パッケージがある場合、パッケージはインストールされません。

## <a name="patch-linux-machines"></a>Linux マシンのパッチ

次のセクションでは、Linux のパッチ適用に関する潜在的な問題について説明します。

### <a name="unexpected-os-level-upgrades"></a>予期しない OS レベルのアップグレード

Red Hat Enterprise Linux など、Linux バリエーションの中には、パッケージによって OS レベルのアップグレードが発生することがあります。 これにより、OS バージョン番号の変更を伴う Update Management が実行される可能性があります。 Update Management がパッケージを更新するときに使用する方法は、管理者がローカルで Linux コンピューターに使用する方法と同じであるため、この動作は意図的なものです。

Update Management の実行による OS バージョンの更新を回避するには、**除外**機能を使用します。

Red Hat Enterprise Linux で、除外するパッケージ名は redhat-release-server.x86_64 です

![Linux の除外するパッケージ](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>重要なパッチまたはセキュリティ パッチが適用されない

更新プログラムを Linux マシンにデプロイする場合、更新プログラムの分類を選択できます。 これにより、更新プログラムは、指定した条件を満たすマシンにのみ適用されるようにフィルター処理されます。 このフィルターは、更新プログラムが展開されるときに、マシンに対してローカルに適用されます。

Update Management は更新プログラムの強化をクラウドで実行するため、Update Management では、一部の更新プログラムに "セキュリティへの影響あり" のフラグが設定されることがありますが、ローカル マシンにはその情報がありません。 このため、重大な更新プログラムを Linux マシンに適用する場合、そのマシンの "セキュリティへの影響あり" とマークされていない更新プログラムが存在する可能性があり、これらは適用されません。

ただし、Update Management には関連更新プログラムに関する追加情報があるため、そのマシンは引き続き "非対応" として報告されることがあります。

更新プログラムの分類ごとの更新プログラムのデプロイは CentOS では既定では動作しません。 CentOS 用の更新プログラムを正しく展開するには、更新プログラムが確実に適用されるように、すべての分類を選択します。 SUSE で '他の更新プログラム' *のみ*を分類として選択すると、zypper (パッケージ マネージャー) に関連するセキュリティ更新プログラムもインストールされるか、またはその依存関係がまず必要になる場合があります。 この動作は、zypper の制限です。 場合によっては、更新プログラムのデプロイを再実行する必要があります。 確認するには、更新プログラムのログを調べてください。

## <a name="remove-a-vm-from-update-management"></a>Update Management から VM を削除する

Update Management から VM を削除するには:

* Log Analytics ワークスペースで、スコープ構成 `MicrosoftDefaultScopeConfig-Updates` の保存された検索条件から VM を削除します。 保存された検索条件は、ワークスペース内の **[全般]** にあります。
* [Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) または [Linux 用 Log Analytics エージェント](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)を削除します。

## <a name="next-steps"></a>次の手順

使用している Windows 仮想マシンの更新プログラムの管理方法を学習するためのチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Windows VM の更新プログラムとパッチの管理](automation-tutorial-update-management.md)

* [Azure Monitor ログ](../log-analytics/log-analytics-log-searches.md)のログ検索を使用して、詳細な更新データを確認します。
* 更新プログラムのデプロイの状態に関する[アラートを作成](automation-tutorial-update-management.md#configure-alerts)します。

* REST API を使用して Update Management を操作する方法については、「[Software Update Configurations](/rest/api/automation/softwareupdateconfigurations)」(ソフトウェア更新プログラムの構成) をご覧ください。
* Update Management のトラブルシューティング方法については、[Update Management のトラブルシューティング](troubleshoot/update-management.md)に関するページを参照してください。
