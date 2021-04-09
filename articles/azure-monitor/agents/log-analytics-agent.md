---
title: Log Analytics エージェントの概要
description: このトピックは、Azure、オンプレミス、または他のクラウド環境でホストされているコンピューターで、Log Analytics を使用してデータの収集と監視を行う方法を理解するために役立ちます。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/12/2021
ms.openlocfilehash: 8b3b1d39766d556b820f21c1775210611b995d92
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199898"
---
# <a name="log-analytics-agent-overview"></a>Log Analytics エージェントの概要

Azure Log Analytics エージェントによって、任意のクラウドの Windows および Linux 仮想マシン、オンプレミスのマシン、[System Center Operations Manager](/system-center/scom/) で監視しているマシンからテレメトリを収集し、Azure Monitor の Log Analytics ワークスペースに収集したデータを送信できます。 Log Analytics エージェントは、分析情報や、[VM insights](../vm/vminsights-enable-overview.md)、[Azure Security Center](../../security-center/index.yml)、[Azure Automation](../../automation/automation-intro.md) といった Azure Monitor のその他のサービスもサポートします。 この記事では、エージェント、システムとネットワークの要件、およびデプロイ方法の詳細な概要について説明します。

> [!NOTE]
> Log Analytics エージェントは、Microsoft Monitoring Agent (MMA) とも呼ばれます。

## <a name="comparison-to-azure-diagnostics-extension"></a>Azure Diagnostics 拡張機能との比較
Azure Monitor の[Azure Diagnostics 拡張機能](./diagnostics-extension-overview.md)は、Azure 仮想マシンのゲスト オペレーティング システムから監視データを収集することにも使用できます。 ご自分の要件に応じて、いずれかまたは両方を選択できます。 Azure Monitor エージェントの詳細な比較については、「[Azure Monitor エージェントの概要](../agents/agents-overview.md)」を参照してください。 

考慮すべき主な違いは次のとおりです。

- Azure Diagnostics 拡張機能は、Azure の仮想マシンでのみ使用できます。 Log Analytics エージェントは、Azure、他のクラウド、およびオンプレミスの仮想マシンで使用できます。
- Azure Diagnostics 拡張機能では、Azure Storage、[Azure Monitor メトリック](../essentials/data-platform-metrics.md) (Windows のみ)、および Event Hubs にデータが送信されます。 Log Analytics エージェントでは、[Azure Monitor ログ](../logs/data-platform-logs.md)にデータが送信されます。
- Log Analytics エージェントは、[ソリューション](../monitor-reference.md#insights-and-core-solutions)、[VM insights](../vm/vminsights-overview.md)、および [Azure Security Center](../../security-center/index.yml) などのその他のサービスに必要です。

## <a name="costs"></a>コスト

Log Analytics エージェントには料金はかかりませんが、取り込まれたデータの料金が発生する場合があります。 Log Analytics ワークスペースで収集されたデータの価格設定の詳細については、[Azure Monitor ログで使用量とコストを管理する](../logs/manage-cost-storage.md) を確認してください。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

 Log Analytics エージェントでサポートされている Windows および Linux オペレーティング システムのバージョンの一覧については、「[サポートされるオペレーティング システム](../agents/agents-overview.md#supported-operating-systems)」をご覧ください。 

## <a name="data-collected"></a>収集されるデータ

次の表に、接続されているすべてのエージェントから収集するために Log Analytics ワークスペースを構成できるデータの種類を示します。 Log Analytics エージェントを使用して他の種類のデータを収集する分析情報、ソリューション、およびその他のソリューションのリストについては、 「[Azure Monitor で何が監視されていますか？](../monitor-reference.md)」 を参照してください。

| Data Source | 説明 |
| --- | --- |
| [Windows イベント ログ](../agents/data-sources-windows-events.md) | Windows イベント ログ システムに送信された情報。 |
| [Syslog](../agents/data-sources-syslog.md)                     | Linux イベント ログ システムに送信される情報。 |
| [パフォーマンス](../agents/data-sources-performance-counters.md)  | オペレーティング システムとワークロードのさまざまな側面のパフォーマンスを測定する数値。 |
| [IIS ログ](../agents/data-sources-iis-logs.md)                 | ゲスト オペレーティング システムで実行されている IIS Web サイトの使用状況に関する情報。 |
| [カスタム ログ](../agents/data-sources-custom-logs.md)           | Windows コンピューターと Linux コンピューターの両方のテキスト ファイルからのイベント。 |

## <a name="data-destinations"></a>データの保存先

Log Analytics エージェントは、データを Azure Monitor の Log Analytics ワークスペースに送信します。 Windows エージェントをマルチホーム化して、複数のワークスペースおよび System Center Operations Manager 管理グループにデータを送信できます。 Linux エージェントでは、ワークスペース、管理グループのどちらか 1 つの宛先にのみ送信できます。

## <a name="other-services"></a>その他のサービス

Linux および Windows 用のエージェントは、Azure Monitor に接続するためだけのものではありません。 Azure Security Center や Azure Sentinel などの他のサービスは、エージェントと、その接続されている Log Analytics ワークスペースに依存します。 エージェントでは Azure Automation もサポートされており、Hybrid Runbook Worker ロールや、[Change Tracking](../../automation/change-tracking/overview.md)、[Update Management](../../automation/update-management/overview.md)、[Azure Security Center](../../security-center/security-center-introduction.md) などの他のサービスがホストされます。 Hybrid Runbook Worker ロールの詳細については、[Azure Automation の Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) に関する記事を参照してください。  

## <a name="workspace-and-management-group-limitations"></a>ワークスペースと管理グループの制限事項

エージェントを Operations Manager 管理グループに接続する方法の詳細については、「[Operations Manager 管理グループに報告するようにエージェントを構成する](../agents/agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)」をご覧ください。

* Windows エージェントでは、System Center Operations Manager 管理グループに接続されている場合でも、最大 4 つのワークスペースに接続できます。
* Linux エージェントではマルチホームがサポートされず、1 つのワークスペースまたは管理グループにのみ接続できます。

## <a name="security-limitations"></a>セキュリティの制限事項

* Windows および Linux エージェントでは [FIPS 140 標準](/windows/security/threat-protection/fips-140-validation)がサポートされていますが、[他の種類のセキュリティ強化はサポートされていない可能性があります](../agents/agent-linux.md#supported-linux-hardening)。

## <a name="installation-options"></a>インストール オプション

Log Analytics エージェントをインストールしてマシンをAzure Monitor に接続するには、要件に応じて複数の方法があります。 以下のセクションでは、さまざまな種類の仮想マシンで使用できる方法を示します。

> [!NOTE]
> Log Analytics エージェントが既に構成されているマシンのクローンはサポートされていません。 エージェントが既にワークスペースに関連付けられている場合、これは "ゴールデンイメージ" に対しては機能しません。

### <a name="azure-virtual-machine"></a>Azure 仮想マシン

- [VM insights](../vm/vminsights-enable-overview.md) には、エージェントを大規模に有効にする複数の方法が用意されています。 これには、Log Analytics エージェントと Dependency Agent のインストールが含まれます。 
- [Azure Security Center では、Log Analytics エージェントをプロビジョニングできます](../../security-center/security-center-enable-data-collection.md)。セキュリティの脆弱性と脅威を監視するために有効にすれば、サポートされているすべての Azure VM と作成された新しいものを対象にできます。
- [Windows](../../virtual-machines/extensions/oms-windows.md) または [Linux](../../virtual-machines/extensions/oms-linux.md) 用の Log Analytics VM 拡張機能は、Azure portal、Azure CLI、Azure PowerShell、または Azure Resource Manager テンプレートを使用してインストールできます。
- 個別の Azure 仮想マシン用に、[Azure portal から手動で](../vm/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)インストールします。

### <a name="windows-virtual-machine-on-premises-or-in-another-cloud"></a>オンプレミスまたは別のクラウド内の Windows 仮想マシン

- [Azure Arc 対応サーバー](../../azure-arc/servers/overview.md)を使用し、Log Analytics VM 拡張機能をデプロイし、管理します。
- エージェントをコマンド ラインから[手動でインストールします](../agents/agent-windows.md)。
- [Azure Automation DSC](../agents/agent-windows.md#install-agent-using-dsc-in-azure-automation) を使用してインストールを自動化します。
- [Azure Stack での Resource Manager テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)を使用します

### <a name="linux-virtual-machine-on-premises-or-in-another-cloud"></a>オンプレミスまたは別のクラウド内の Linux 仮想マシン

- [Azure Arc 対応サーバー](../../azure-arc/servers/overview.md)を使用し、Log Analytics VM 拡張機能をデプロイし、管理します。
- GitHub でホストされているラッパー スクリプトを呼び出してエージェントを[手動でインストールします](../vm/quick-collect-linux-computer.md)。
- 収集したデータを管理グループ直属の Windows コンピューターから転送する目的で、[System Center Operations Manager](./om-agents.md) と Azure Monitor を統合します。

## <a name="workspace-id-and-key"></a>ワークスペース ID とキー

使用するインストール方法に関係なく、エージェントが接続する Log Analytics ワークスペースのワークスペース ID とキーが必要になります。 Azure portal の **[Log Analytics ワークスペース]** メニューからワークスペースを選択します。 次に、 **[設定]** セクションで **[エージェント管理]** を選択します。 

[![ワークスペースの詳細](media/log-analytics-agent/workspace-details.png)](media/log-analytics-agent/workspace-details.png#lightbox)

## <a name="tls-12-protocol"></a>TLS 1.2 プロトコル

Azure Monitor ログに転送中のデータのセキュリティを確保するには、エージェントを、少なくともトランスポート層セキュリティ (TLS) 1.2 を使用するように構成することを強くお勧めします。 以前のバージョンの TLS/SSL (Secure Sockets Layer) は脆弱であることが確認されています。現在、これらは下位互換性を維持するために使用可能ですが、**推奨されていません**。  詳細については、「[TLS 1.2 を使用して安全にデータを送信する](../logs/data-security.md#sending-data-securely-using-tls-12)」を参照してください。 

## <a name="network-requirements"></a>ネットワークの要件

Linux および Windows 用のエージェントでは、TCP ポート 443 を介して Azure Monitor サービスへのアウトバウンド通信を行います。 インターネット経由で通信するためにマシンがファイアウォールやプロキシ サーバーを介して接続する場合、以下の要件を確認して必要なネットワーク構成を把握してください。 インターネットに接続するネットワーク上のコンピューターが IT セキュリティ ポリシーで許可されていない場合、[Log Analytics ゲートウェイ](gateway.md)を設定し、エージェントをゲートウェイ経由で Azure Monitor に接続するように構成できます。 これで、エージェントによって構成情報を受信し、収集したデータを送信できます。

![Log Analytics エージェントの通信ダイアグラム](./media/log-analytics-agent/log-analytics-agent-01.png)

以下の表は、Linux および Windows エージェントが Azure Monitor ログと通信するために必要なプロキシとファイアウォール構成情報の一覧です。

### <a name="firewall-requirements"></a>ファイアウォールの要件

|エージェントのリソース|Port |Direction |バイパス HTTPS 検査|
|------|---------|--------|--------|
|*.ods.opinsights.azure.com |ポート 443 |送信|はい |  
|*.oms.opinsights.azure.com |ポート 443 |送信|はい |  
|*.blob.core.windows.net |ポート 443 |送信|はい |
|*.azure-automation.net |ポート 443 |送信|はい |

Azure Government に必要なファイアウォールの情報については、[Azure Government の管理](../../azure-government/compare-azure-government-global-azure.md#azure-monitor)に関するトピックを参照してください。 

Azure Automation Hybrid Runbook Worker を使用して Automation サービスに接続および登録し、お使いの環境で Runbook または管理機能を使用することを計画している場合、[Hybrid Runbook Worker 用のネットワークの構成](../../automation/automation-hybrid-runbook-worker.md#network-planning)に関する記事に説明されているポート番号と URL にアクセスできる必要があります。

### <a name="proxy-configuration"></a>[プロキシ構成]

Windows および Linux エージェントは、HTTPS プロトコルを使用し、プロキシ サーバーまたは Log Analytics ゲートウェイのいずれかを経由して、Azure Monitor との通信をサポートします。  匿名認証と基本認証 (ユーザー名/パスワード) の両方がサポートされます。  サービスに直接接続される Windows エージェントの場合、プロキシの構成は、インストール時や、[デプロイ後](../agents/agent-manage.md#update-proxy-settings)にコントロール パネルまたは PowerShell を使って、指定されます。  

Linux エージェントの場合、プロキシ サーバーは、インストール時や、[インストール後](../agents/agent-manage.md#update-proxy-settings)に proxy.conf 構成ファイルを修正することによって、指定されます。 Linux エージェント プロキシ構成の値には次の構文があります。

`[protocol://][user:password@]proxyhost[:port]`

|プロパティ| 説明 |
|--------|-------------|
|Protocol | https |
|user | プロキシ認証のオプションのユーザー名 |
|password | プロキシ認証のオプションのパスワード |
|proxyhost | プロキシ サーバー/Log Analytics ゲートウェイのアドレスまたは FQDN |
|port | プロキシ サーバー/Log Analytics ゲートウェイのオプションのポート番号 |

例: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> パスワードに "\@" などの特殊文字を使用した場合、値が正しく解析されないためにプロキシ接続エラーが発生します。  この問題を回避するには、[URLDecode](https://www.urldecoder.org/) などのツールを使用して、URL 内にパスワードをエンコードします。  

## <a name="next-steps"></a>次のステップ

* [データ ソース](../agents/agent-data-sources.md)を見直して、Windows または Linux コンピューターからデータを収集するために使用できるデータ ソースを理解します。 
* [ログ クエリ](../logs/log-query-overview.md)について学習し、データ ソースとソリューションから収集されたデータを分析します。 
* Azure Monitor に機能を追加し、Log Analytics ワークスペース内にデータを収集する[監視ソリューション](../insights/solutions.md)について学習します。
