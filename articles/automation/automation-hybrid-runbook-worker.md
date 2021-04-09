---
title: Azure Automation Hybrid Runbook Worker の概要
description: この記事では、ローカル データ センターまたはクラウド プロバイダー内のコンピューターで Runbook を実行できるようにする Hybrid Runbook Worker の概要について説明します。
services: automation
ms.subservice: process-automation
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: c95ccb5ea1a23e8173d58bd3a18490e9b8e630e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581271"
---
# <a name="hybrid-runbook-worker-overview"></a>Hybrid Runbook Worker の概要

Azure Automation の Runbook は Azure クラウド プラットフォームで実行されるため、他のクラウドやオンプレミス環境のリソースにはアクセスできないことがあります。 Azure Automation の Hybrid Runbook Worker 機能を使うと、ロールをホスティングしているマシン上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 Runbook は Azure Automation で格納および管理された後、1 つ以上の割り当て済みマシンに配信されます。

## <a name="runbook-worker-types"></a>Runbook Worker の種類

Runbook Worker には、システムとユーザーの 2 種類があります。 次の表は、それらの違いについて説明しています。

|Type | [説明] |
|-----|-------------|
|**システム** |Windows および Linux マシンにユーザー指定の更新プログラムをインストールするために設計された Update Management 機能によって使用される、非表示の一連の Runbook がサポートされます。<br> この種類の Hybrid Runbook Worker は Hybrid Runbook Worker グループのメンバーではないため、Runbook Worker グループを対象とする Runbook は実行されません。 |
|**User** |1 つまたは複数の Runbook Worker グループのメンバーである Windows および Linux マシン上で直接実行することを目的としたユーザー定義の Runbook がサポートされます。 |

Hybrid Runbook Worker は、Windows または Linux のいずれかのオペレーティング システムで実行できます。このロールは、Azure Monitor [Log Analytics ワークスペース](../azure-monitor/logs/design-logs-deployment.md)へレポートする [Log Analytics エージェント](../azure-monitor/agents/log-analytics-agent.md)に依存しています。 ワークスペースは、サポートされているオペレーティング システムのマシンを監視するだけでなく、Hybrid Runbook Worker のインストールに必要なコンポーネントをダウンロードするためのものでもあります。

Azure Automation [Update Management](./update-management/overview.md) を有効にすると、Log Analytics ワークスペースに接続されたマシンはすべてシステム Hybrid Runbook Worker として自動的に構成されます。 ユーザー Windows Hybrid Runbook Worker として構成する場合は、「[Windows Hybrid Runbook Worker をデプロイする](automation-windows-hrw-install.md)」を参照してください。Linux の場合は、「[Linux Hybrid Runbook Worker を展開する](automation-linux-hrw-install.md)」を参照してください。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

![Hybrid Runbook Worker の概要](media/automation-hybrid-runbook-worker/automation.png)

各ユーザー Hybrid Runbook Worker は、Worker のインストール時に指定する Hybrid Runbook Worker グループのメンバーです。 グループに 1 の worker を含めることは可能ですが、高可用性を実現するために、複数の worker をグループに含めることができます。 各マシンは、1 つの Automation アカウントにレポートする 1 つの Hybrid Runbook Worker をホストできますが、複数の Automation アカウントに対してハイブリッド worker を登録することはできません。 ハイブリッド worker は、単一の Automation アカウントからしかジョブをリッスンできません。 Update Management によって管理されているシステム Hybrid Runbook Worker をホストしているマシンの場合は、Hybrid Runbook Worker グループに追加できます。 しかし、Update Management と Hybrid Runbook Worker グループ メンバーシップの両方に同じ Automation アカウントを使用する必要があります。

ユーザー Hybrid Runbook Worker で Runbook を開始する場合は、実行されるグループを指定します。 グループの各ワーカーは、実行可能なジョブがあるかどうかを確認するために Azure Automation をポーリングします。 ジョブが実行可能な場合、ジョブに最初に到達した worker がこれを実行します。 ジョブ キューの処理時間は、ハイブリッド worker のハードウェア プロファイルと負荷によって異なります。 特定の worker を指定することはできません。 ハイブリッド worker は、ポーリング機構 (30 秒ごと) で動作し、先着順で処理を行います。 ジョブがプッシュされたタイミングに応じて、Automation サービスへの ping が実行されたハイブリッド worker によってジョブが取得されます。 通常、1 つのハイブリッド worker で取得できるジョブは、ping ごと (つまり、30秒ごと) に 4 つです。 ジョブのプッシュ速度が 30 秒あたり 4 つを超える場合は、Hybrid Runbook Worker グループ内の別のハイブリッド worker によってジョブが取得された可能性が高くなります。

Hybrid Runbook Worker には、ディスク領域、メモリ、またはネットワーク ソケットに関する [Azure サンドボックス](automation-runbook-execution.md#runbook-execution-environment) リソースの[制限](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)の多くがありません。 ハイブリッド worker に対する制限は、worker のリソースのみに関連し、Azure サンドボックスの[フェア シェア](automation-runbook-execution.md#fair-share)の時間制限には制限されません。

Hybrid Runbook Worker での Runbook のディストリビューション、およびジョブがトリガーされるタイミングや方法を制御するには、Automation アカウント内のさまざまな Hybrid Runbook Worker グループに対してハイブリッド worker を登録します。 独自の実行の配置に合うよう、ジョブの対象を特定の 1 つのグループや複数のグループにします。

## <a name="hybrid-runbook-worker-installation"></a>Hybrid Runbook Worker のインストール

ユーザー Hybrid Runbook Worker をインストールするプロセスは、オペレーティング システムによって異なります。 次の表に、デプロイのタイプを定義します。

|オペレーティング システム  |デプロイのタイプ  |
|---------|---------|
|Windows     | [自動](automation-windows-hrw-install.md#automated-deployment)<br>[手動](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [[手動]](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Windows マシンにおけるお勧めのインストール方法は、Azure Automation Runbook を使用して、構成するプロセスを完全に自動化する方法です。 それができない場合は、ステップごとの手順に従って、ロールを手動でインストールして構成します。 Linux マシンでは、コンピューターにエージェントをインストールする Python スクリプトを実行します。

## <a name="network-planning"></a><a name="network-planning"></a>ネットワークの計画

Hybrid Runbook Worker に必要なポート、URL、およびその他のネットワークの詳細については、[Azure Automation のネットワーク構成](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker)を確認してください。

### <a name="proxy-server-use"></a>プロキシ サーバーの使用

Azure Automation と、Log Analytics エージェントを実行しているマシンとの間の通信にプロキシ サーバーを使用する場合は、適切なリソースにアクセスできることを確認してください。 Hybrid Runbook Worker および Automation サービスからの要求のタイムアウトは 30 秒です。 3 回試行した後で、要求は失敗します。

### <a name="firewall-use"></a>ファイアウォールの使用

ファイアウォールを使用してインターネットへのアクセスを制限する場合は、アクセスを許可するようにファイアウォールを構成する必要があります。 Log Analytics ゲートウェイをプロキシとして使用した場合、Hybrid Runbook Worker 用に構成されていることを確認してください。 [Automation Hybrid Runbook Worker 用の Log Analytics ゲートウェイの構成](../azure-monitor/agents/gateway.md)に関するページをご覧ください。

### <a name="service-tags"></a>サービス タグ

Azure Automation では、サービス タグ [GuestAndHybridManagement](../virtual-network/service-tags-overview.md) で始まる Azure 仮想ネットワーク サービス タグがサポートされています。 サービス タグを使用して、[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md#security-rules)または [Azure Firewall](../firewall/service-tags.md) でのネットワーク アクセス制御を定義できます。 セキュリティ規則を作成するときに、特定の IP アドレスの代わりにサービス タグを使用できます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 **GuestAndHybridManagement** を指定することで、Automation サービスのトラフィックを許可または拒否できます。 このサービス タグでは、IP 範囲を特定のリージョンに制限することによる、より詳細な制御はサポートされていません。

Azure Automation サービスのサービス タグでは、以下のシナリオで使用される IP のみが提供されます。

* 仮想ネットワーク内から Webhook をトリガーする
* VNet 上の Hybrid Runbook Worker または State Configuration エージェントに Automation サービスとの通信を許可する

>[!NOTE]
>現時点で、サービス タグ **GuestAndHybridManagement** は、Azure サンドボックスでの Runbook ジョブの実行をサポートしていません。Hybrid Runbook Worker での直接の実行のみがサポートされています。

## <a name="support-for-impact-level-5-il5"></a>影響レベル 5 (IL5) のサポート

Azure Government で Azure Automation Hybrid Runbook Worker を使用すると、次の 2 つの構成のいずれかで、影響レベル 5 のワークロードをサポートできます。

* [分離された仮想マシン](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines)。 デプロイすると、IL5 ワークロードをサポートするために必要な分離レベルを提供する、そのマシンの物理ホスト全体が消費されます。

* [Azure Dedicated Hosts](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host) は、1 つの Azure サブスクリプションに対して専用の、1 つ以上の仮想マシンをホストできる物理サーバーを提供します。

>[!NOTE]
>Hybrid Runbook Worker ロールを使用したコンピューティングの分離は、Azure 商用と米国政府のクラウドで利用できます。

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Hybrid Runbook Worker の Update Management アドレス

Hybrid Runbook Worker に必要な標準のアドレスとポートに加えて、Update Management には、「[ネットワークの計画](./update-management/overview.md#ports)」セクションで説明されている他のネットワーク構成要件があります。

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker での Azure Automation State Configuration

Hybrid Runbook Worker で [Azure Automation State Configuration](automation-dsc-overview.md) を実行できます。 Hybrid Runbook Worker をサポートするサーバーの構成を管理するには、サーバーを DSC ノードとして追加する必要があります。 [Azure Automation State Configuration による管理のためのマシンの有効化](automation-dsc-onboarding.md)に関するページを参照してください。

## <a name="runbook-worker-limits"></a>Runbook Worker の制限

Automation アカウントあたりのハイブリッド Worker グループの最大数は 4,000 であり、これはシステムとユーザー ハイブリッド worker の両方に適用されます。 管理するマシンが 4,000 台を超える場合は、別の Automation アカウントを作成することをお勧めします。

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker での Runbook

ローカル マシン上のリソースを管理したり、ユーザー Hybrid Runbook Worker が展開されているローカル環境のリソースに対して実行したりする Runbook があるとします。 この場合は、Automation アカウントではなく、ハイブリッド worker で Runbook を実行できます。 Hybrid Runbook Worker で実行される Runbook は、Automation アカウントで実行する Runbook と同じ構造になります。 詳細については、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照してください。

### <a name="hybrid-runbook-worker-jobs"></a>Hybrid Runbook Worker ジョブ

Hybrid Runbook Worker ジョブは、Windows ではローカルの **システム** アカウントで実行され、Linux では [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) アカウントで実行されます。 Azure Automation による Hybrid Runbook Worker でのジョブの処理は、Azure サンドボックスで実行されるジョブとは異なります。 「[Runbook の実行環境](automation-runbook-execution.md#runbook-execution-environment)」を参照してください。

Hybrid Runbook Worker のホスト コンピューターが再起動された場合、実行中の Runbook ジョブは最初から再起動されるか、PowerShell ワークフロー Runbook の最後のチェックポイントから再起動されます。 Runbook ジョブの再起動が 3 回を超えると、そのジョブは中断されます。

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker に対する Runbook のアクセス許可

ユーザー Hybrid Runbook Worker で実行される Runbook は Azure 以外のリソースにアクセスするため、Azure リソースへの認証に Runbook で通常使用される認証メカニズムを使用できません。 Runbook では、ローカル リソースに対して独自の認証を提供するか、または [Azure リソース用のマネージド ID](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) を使用して認証を構成します。 また、すべての Runbook にユーザー コンテキストを提供する実行アカウントを指定することもできます。

## <a name="view-system-hybrid-runbook-workers"></a>システム Hybrid Runbook Worker を表示する

Windows または Linux マシンで Update Management 機能を有効にしたら、Azure portal 内のシステム Hybrid Runbook Worker グループ一覧のインベントリを作成できます。 選択した Automation アカウントの左側のペインにある **[Hybrid worker groups]\(ハイブリッド worker グループ\)** オプションから **[System hybrid worker groups]\(システム ハイブリッド worker グループ\)** タブを選択することにより、ポータル内で最大 2,000 の worker を表示できます。

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Automation アカウントの [System hybrid worker groups]\(システム ハイブリッド worker グループ\) ページ" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

ハイブリッド worker の数が 2,000 を超えている場合、そのすべての一覧を取得するには、次の PowerShell スクリプトを実行します。

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>次のステップ

* オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を学習するには、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」をご覧ください。

* Hybrid Runbook Worker のトラブルシューティングを行う方法については、「[Hybrid Runbook Worker のトラブルシューティング](troubleshoot/hybrid-runbook-worker.md#general)」を参照してください。
