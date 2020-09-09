---
title: Azure Automation Hybrid Runbook Worker の概要
description: この記事では、ローカル データ センターまたはクラウド プロバイダー内のコンピューターで Runbook を実行できるようにする Hybrid Runbook Worker の概要について説明します。
services: automation
ms.subservice: process-automation
ms.date: 07/16/2020
ms.topic: conceptual
ms.openlocfilehash: 4d29979e28140b728478d405db934cb41783f4b0
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448087"
---
# <a name="hybrid-runbook-worker-overview"></a>Hybrid Runbook Worker の概要

Azure Automation の Runbook は Azure クラウド プラットフォームで実行されるため、他のクラウドやオンプレミス環境のリソースにはアクセスできないことがあります。 Azure Automation の Hybrid Runbook Worker 機能を使うと、ロールをホスティングしているマシン上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 Runbook は Azure Automation で格納および管理された後、1 つ以上の割り当て済みマシンに配信されます。

次の図にこの機能を示します。

![Hybrid Runbook Worker の概要](media/automation-hybrid-runbook-worker/automation.png)

Hybrid Runbook Worker は、Windows と Linux のいずれのオペレーティング システム上でも実行できます。 Azure Monitor [Log Analytics ワークスペース](../azure-monitor/platform/design-logs-deployment.md)にレポートする [Log Analytics エージェント](../azure-monitor/platform/log-analytics-agent.md)に依存します。 ワークスペースは、サポートされているオペレーティング システムのマシンを監視するだけでなく、Hybrid Runbook Worker に必要なコンポーネントをダウンロードするためのものでもあります。

各 Hybrid Runbook Worker は、エージェントのインストール時に指定する Hybrid Runbook Worker グループのメンバーです。 グループには単一のエージェントを含めることができますが、高可用性グループに複数のエージェントをインストールすることができます。 それぞれのマシンでは、1 つのハイブリッド worker レポートを 1 つの Automation アカウントにホストできます。

Hybrid Runbook Worker で Runbook を開始する場合は、実行されるグループを指定します。 グループの各ワーカーは、実行可能なジョブがあるかどうかを確認するために Azure Automation をポーリングします。 ジョブが実行可能な場合、ジョブに最初に到達した worker がこれを実行します。 ジョブ キューの処理時間は、ハイブリッド worker のハードウェア プロファイルと負荷によって異なります。 特定の worker を指定することはできません。

[Azure サンドボックス](automation-runbook-execution.md#runbook-execution-environment)の代わりに Hybrid Runbook Worker を 使用します。これには、ディスク領域、メモリ、またはネットワーク ソケットに関するサンドボックスの多くの[制限](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)がないためです。 ハイブリッド worker の制限は、ワーカーそのもののリソースにのみ関連します。

> [!NOTE]
> Hybrid Runbook Worker には、Azure サンドボックスに存在するような[フェア シェア](automation-runbook-execution.md#fair-share)による時間制限はありません。

## <a name="hybrid-runbook-worker-installation"></a>Hybrid Runbook Worker のインストール

Hybrid Runbook Worker をインストールするプロセスは、オペレーティング システムによって異なります。 次の表に、デプロイのタイプを定義します。

|オペレーティング システム  |デプロイのタイプ  |
|---------|---------|
|Windows     | [自動](automation-windows-hrw-install.md#automated-deployment)<br>[手動](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Azure Automation Runbook を使用して、Windows マシンを構成するプロセスを完全に自動化するインストール方法をお勧めします。 それができない場合は、ステップごとの手順に従って、ロールを手動でインストールして構成します。 Linux マシンでは、コンピューターにエージェントをインストールする Python スクリプトを実行します。

## <a name="network-planning"></a><a name="network-planning"></a>ネットワークの計画

Hybrid Runbook Worker が Azure Automation に接続して登録するには、このセクションで説明されているポート番号と URL へのアクセスが必要です。 worker は、Azure Monitor Log Analytics ワークスペースに接続するために [Log Analytics エージェントに必要なポートと URL](../azure-monitor/platform/agent-windows.md) へのアクセスも必要です。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Hybrid Runbook Worker には、次のポートと URL が必要です。

* ポート: 送信インターネット アクセスには TCP 443 のみが必要です
* グローバル URL: `*.azure-automation.net`
* US Gov バージニアのグローバル URL: `*.azure-automation.us`
* エージェント サービス: `https://<workspaceId>.agentsvc.azure-automation.net`

特定のリージョンに対して定義された Automation アカウントがある場合は、そのリージョン データセンターへの Hybrid Runbook Worker の通信を制限できます。 必要な DNS レコードについては、[Azure Automation によって使用される DNS レコード](how-to/automation-region-dns-records.md)に関するページを参照してください。

### <a name="proxy-server-use"></a>プロキシ サーバーの使用

Azure Automation と、Log Analytics エージェントを実行しているマシンとの間の通信にプロキシ サーバーを使用する場合は、適切なリソースにアクセスできることを確認してください。 Hybrid Runbook Worker および Automation サービスからの要求のタイムアウトは 30 秒です。 3 回試行した後で、要求は失敗します。

### <a name="firewall-use"></a>ファイアウォールの使用

ファイアウォールを使用してインターネットへのアクセスを制限する場合は、アクセスを許可するようにファイアウォールを構成する必要があります。 Log Analytics ゲートウェイをプロキシとして使用した場合、Hybrid Runbook Worker 用に構成されていることを確認してください。 [Automation Hybrid Worker に向けた Log Analytics ゲートウェイの構成](../azure-monitor/platform/gateway.md)に関するセクションを参照してください。

### <a name="service-tags"></a>サービス タグ

Azure Automation では、サービス タグ [GuestAndHybridManagement](../virtual-network/service-tags-overview.md) で始まる Azure 仮想ネットワーク サービス タグがサポートされています。 サービス タグを使用して、[ネットワーク セキュリティ グループ](../virtual-network/security-overview.md#security-rules)または [Azure Firewall](../firewall/service-tags.md) でのネットワーク アクセス制御を定義できます。 セキュリティ規則を作成するときに、特定の IP アドレスの代わりにサービス タグを使用できます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 **GuestAndHybridManagement** を指定することで、Automation サービスのトラフィックを許可または拒否できます。 このサービス タグでは、IP 範囲を特定のリージョンに制限することによる、より詳細な制御はサポートされていません。

Azure Automation サービスのサービス タグでは、以下のシナリオで使用される IP のみが提供されます。

* 仮想ネットワーク内から Webhook をトリガーする
* VNet 上の Hybrid Runbook Worker または State Configuration エージェントに Automation サービスとの通信を許可する

>[!NOTE]
>現時点で、サービス タグ **GuestAndHybridManagement** は、Azure サンドボックスでの Runbook ジョブの実行をサポートしていません。Hybrid Runbook Worker での直接の実行のみがサポートされています。

## <a name="support-for-impact-level-5-il5"></a>影響レベル 5 (IL5) のサポート

Azure Government で Azure Automation Hybrid Runbook Worker を使用すると、次の 2 つの構成のいずれかで、影響レベル 5 のワークロードをサポートできます。

* [分離された仮想マシン](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines)。 デプロイすると、IL5 ワークロードをサポートするために必要な分離レベルを提供する、その VM の物理ホスト全体が消費されます。

* [Azure Dedicated Hosts](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-hosts) は、1 つの Azure サブスクリプションに対して専用の、1 つ以上の仮想マシンをホストできる物理サーバーを提供します。

>[!NOTE]
>Hybrid Runbook Worker ロールを使用したコンピューティングの分離は、Azure 商用と米国政府のクラウドで利用できます。 

## <a name="update-management-on-hybrid-runbook-worker"></a>Hybrid Runbook Worker の Update Management

Azure Automation [Update Management](update-management/update-mgmt-overview.md) を有効にすると、Log Analytics ワークスペースに接続されたマシンはすべて Hybrid Runbook Worker として自動的に構成されます。 各 worker は、対象の Runbook を更新の管理でサポートできます。

このように構成されたマシンは、Automation アカウントで既に定義された Hybrid Runbook Worker グループには登録されません。 このマシンを Hybrid Runbook Worker グループに追加できますが、Update Management と Hybrid Runbook Worker グループ メンバーシップの両方に同じアカウントを使用する必要があります。 この機能は、Hybrid Runbook Worker のバージョン 7.2.12024.0 に追加されました。

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Hybrid Runbook Worker の Update Management アドレス

Hybrid Runbook Worker に必要な標準のアドレスとポートに加えて、Update Management には、「[ネットワークの計画](update-management/update-mgmt-overview.md#ports)」セクションで説明されている追加のネットワーク構成要件があります。

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker での Azure Automation State Configuration

Hybrid Runbook Worker で [Azure Automation State Configuration](automation-dsc-overview.md) を実行できます。 Hybrid Runbook Worker をサポートするサーバーの構成を管理するには、サーバーを DSC ノードとして追加する必要があります。 [Azure Automation State Configuration による管理のためのマシンの有効化](automation-dsc-onboarding.md)に関するページを参照してください。

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker での Runbook

ローカル マシン上のリソースを管理したり、Hybrid Runbook Worker が展開されているローカル環境のリソースに対して実行したりする Runbook があるとします。 この場合は、Automation アカウントではなく、ハイブリッド worker で Runbook を実行できます。 Hybrid Runbook Worker で実行される Runbook は、Automation アカウントで実行する Runbook と同じ構造になります。 詳細については、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照してください。

### <a name="hybrid-runbook-worker-jobs"></a>Hybrid Runbook Worker ジョブ

Hybrid Runbook Worker ジョブは、Windows ではローカルの**システム** アカウントで実行され、Linux では [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) アカウントで実行されます。 Azure Automation による Hybrid Runbook Worker でのジョブの処理は、Azure サンドボックスで実行されるジョブとは若干異なります。 「[Runbook の実行環境](automation-runbook-execution.md#runbook-execution-environment)」を参照してください。

Hybrid Runbook Worker のホスト コンピューターが再起動された場合、実行中の Runbook ジョブは最初から再起動されるか、PowerShell ワークフロー Runbook の最後のチェックポイントから再起動されます。 Runbook ジョブの再起動が 3 回を超えると、そのジョブは中断されます。

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker に対する Runbook のアクセス許可

Hybrid Runbook Worker で実行される Runbook は Azure 以外のリソースにアクセスするため、Azure リソースへの認証に Runbook で通常使用される認証メカニズムを使用できません。 Runbook では、ローカル リソースに対して独自の認証を提供するか、または [Azure リソース用のマネージド ID](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) を使用して認証を構成します。 また、すべての Runbook にユーザー コンテキストを提供する実行アカウントを指定することもできます。

## <a name="next-steps"></a>次のステップ

* オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を学習するには、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」をご覧ください。

* Hybrid Runbook Worker のトラブルシューティングを行う方法については、「[Hybrid Runbook Worker のトラブルシューティング](troubleshoot/hybrid-runbook-worker.md#general)」を参照してください。
