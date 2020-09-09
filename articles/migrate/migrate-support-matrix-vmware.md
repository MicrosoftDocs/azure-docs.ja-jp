---
title: Azure Migrate での VMware 評価サポート
description: Azure Migrate Server Assessment を使用した VMware VM の評価のサポートについて説明します。
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 9c9b1f7687d1ab1af36ac603501ecbaa7affd9b6
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387032"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 評価のサポートマトリックス 

この記事では、[Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) ツールを使用して、Azure に移行するために VMware VM を検出して評価する場合の前提条件とサポート要件について説明します。 VMware VM を評価するには、Azure Migrate プロジェクトを作成した後、そのプロジェクトに Server Assessment ツールを追加します。 ツールを追加した後、Azure Migrate アプライアンスをデプロイします。 アプライアンスは継続的にオンプレミスのマシンを検出し、マシンのメタデータとパフォーマンス データを Azure に送信します。 検出が完了したら、検出されたマシンをグループにまとめ、グループの評価を実行します。 

VMware VM を Azure に移行する場合は、[移行のサポート マトリックス](migrate-support-matrix-vmware-migration.md)を確認してください。



## <a name="limitations"></a>制限事項

**サポート** | **詳細**
--- | ---
**プロジェクトの制限** | 1 つの Azure サブスクリプションで複数のプロジェクトを作成できます。<br/><br/> 1 つの[プロジェクト](migrate-support-matrix.md#azure-migrate-projects)で最大 35,000 台の VMware VM を検出および評価できます。 また 1 つのプロジェクトには、各物理サーバーおよび Hyper-V VM の評価の上限に達するまで含めることができます。
**検出** | Azure Migrate アプライアンスで、vCenter Server 上の VMware VM を最大 10,000 台検出できます。
**評価** | 1 つのグループに最大 35,000 個のマシンを追加できます。<br/><br/> 1 回の評価で最大 35,000 個の VM を評価できます。

評価の詳細については[こちら](concepts-assessment-calculation.md)をご覧ください。


## <a name="vmware-requirements"></a>VMware の要件

**VMware** | **詳細**
--- | ---
**vCenter Server** | 検出および評価対象のマシンは、vCenter Server バージョン 5.5、6.0、6.5、または 6.7 で管理されている必要があります。
**アクセス許可** | Server Assessment には、検出および評価用に vCenter Server の読み取り専用アカウントが必要です。<br/><br/> アプリケーションの検出または依存関係の視覚化を行う場合、このアカウントには、 **[Virtual Machines]**  >  **[Guest Operations]\(ゲスト操作\)** に対して有効になっている特権が必要です。

## <a name="vm-requirements"></a>VM の要件
**VMware** | **詳細**
--- | ---
**VMware VM** | すべてのオペレーティング システムを、移行のために評価することができます。 


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate アプライアンスの要件

Azure Migrate では、[Azure Migrate アプライアンス](migrate-appliance.md)を使用して検出と評価を行います。 アプライアンスを VMWare VM としてデプロイするには、OVA テンプレートを使用するか、vCenter Server にインポートするか、[PowerShell スクリプト](deploy-appliance-script.md)を使用します。

- VMware の[アプライアンスの要件](migrate-appliance.md#appliance---vmware)を確認してください。
- Azure Government では、[スクリプトを使用して](deploy-appliance-script-government.md)アプライアンスをデプロイする必要があります。
- アプライアンスが[パブリック](migrate-appliance.md#public-cloud-urls)および [Government](migrate-appliance.md#government-cloud-urls) クラウドでアクセスする必要がある URL について確認します。


## <a name="port-access-requirements"></a>ポートのアクセス要件

**[デバイス]** | **接続**
--- | ---
**アプライアンス** | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br/><br/> ポート 44368 で、次の URL を使用してアプライアンス管理アプリにリモートでアクセスするためのインバウンド接続: ```https://<appliance-ip-or-name>:44368``` <br/><br/>ポート 443 (HTTPS) で検出とパフォーマンスのメタデータを Azure Migrate に送信するためのアウトバウンド接続。
**vCenter サーバー** | TCP ポート 443 で、アプライアンスが評価用に構成およびパフォーマンスのメタデータを収集できるようにするインバウンド接続。 <br/><br/> 既定では、アプライアンスはポート 443 で vCenter に接続します。 vCenter Server が別のポートでリッスンする場合、検出の設定時にポートを変更できます。
**ESXi ホスト** | [アプリ検出](how-to-discover-applications.md)または[エージェントレスの依存関係の分析](concepts-dependency-visualization.md#agentless-analysis)を行う場合、アプライアンスは、TCP ポート 443 上の ESXi ホストに接続されると、アプリケーションを検出し、VM でエージェントレスの依存関係の視覚化を実行します。

## <a name="application-discovery-requirements"></a>アプリケーションの検出の要件

マシンの検出に加えて、Server Assessment ではアプリ、ロール、機能を検出できます。 アプリ インベントリを検出することで、オンプレミスのワークロードに合わせて調整された移行パスを特定し、計画することができます。 

**サポート** | **詳細**
--- | ---
**サポートされているマシン** | 現在、アプリ検出は VMware VM でのみサポートされています。
**検出** | アプリ検出はエージェントレスです。 マシンのゲスト資格情報を使用し、WMI と SSH 呼び出しを使用してマシンにリモートでアクセスします。
**VM のサポート** | アプリ検出は、すべてのバージョンの Windows および Linux が実行されている VM でサポートされています。
**vCenter** | 評価に使用される vCenter Server の読み取り専用アカウントは、アプリケーション検出用の VM と対話するために、 **[Virtual Machines]**  >  **[Guest Operations]\(ゲスト操作\)** に対して有効になっている特権が必要です。
**VM アクセス** | アプリの検出では、VM 上にアプリケーション検出用のローカル ユーザー アカウントが必要です。<br/><br/> 現在、Azure Migrate ではすべての Windows サーバーに対して 1 つの資格情報と、すべての Linux サーバーに対して 1 つの資格情報を使用することがサポートされています。<br/><br/> Windows VM 用にゲスト ユーザー アカウントを作成し、すべての Linux VM 用に通常/標準ユーザー アカウント (非 sudo アクセス) を作成します。
**VMware ツール** | 検出する VM に VMware ツールがインストールされ、実行されている必要があります。 <br/><br/> VMware ツールのバージョンは、10.2.0 以降である必要があります。
**PowerShell** | VM には、PowerShell バージョン 2.0 以降がインストールされている必要があります。
**ポート アクセス** | 検出する VM を実行している ESXi ホストでは、Azure Migrate アプライアンスが TCP ポート 443 に接続できる必要があります。
**制限** | アプリ検出では、各 Azure Migrate アプライアンスで最大 1 万の VM を検出できます。


## <a name="dependency-analysis-requirements-agentless"></a>依存関係の分析の要件 (エージェントレス)

[依存関係の分析](concepts-dependency-visualization.md)を使用すると、評価して Azure に移行するオンプレミスのマシン間の依存関係を特定できます。 この表は、エージェントレスの依存関係の分析を設定するための要件をまとめたものです。

**要件** | **詳細**
--- | --- 
**デプロイ前** | Server Assessment ツールがプロジェクトに追加された状態で、Azure Migrate プロジェクトを準備する必要があります。<br/><br/>  オンプレミスの VMware マシンを検出するには、Azure Migrate アプライアンスをセットアップした後、依存関係の視覚化をデプロイします。<br/><br/> 初めてプロジェクトを作成する方法については[こちら](create-manage-projects.md)を参照してください。<br/> 既存のプロジェクトに評価ツールを追加方法については[こちら](how-to-assess.md)を参照してください。<br/> VMware VM の評価用に Azure Migrate アプライアンスを設定する方法を[参照](how-to-set-up-appliance-vmware.md)してください。
**サポートされているマシン** | 現在、VMware VM のみでサポートされています。
**Windows VM** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bit)。
**vCenter サーバーの資格情報** | 依存関係の可視化には、読み取り専用アクセス権を持ち、[仮想マシン] > [ゲスト操作] の権限が有効な vCenter Server アカウントが必要です。
**Windows VM のアクセス許可** |  依存関係の分析の場合、Windows VM にアクセスするには、Azure Migrate アプライアンスで使用できるドメイン管理者アカウントまたはローカル管理者アカウントが必要です。
**Linux VM** | Red Hat Enterprise Linux 7、6、5<br/> Ubuntu Linux 14.04、16.04<br/> Debian 7、8<br/> Oracle Linux 6、7<br/> CentOS 5、6、7。
**Linux アカウント** | 依存関係の分析の場合、Linux マシンの Azure Migrate アプライアンスには Root 特権を持つユーザー アカウントが必要です。<br/><br/> また、ユーザー アカウントには /bin/netstat および /bin/ls ファイルに対する次の権限が必要です。CAP_DAC_READ_SEARCH と CAP_SYS_PTRACE。
**必要なエージェント** | 分析するマシンにエージェントは必要ありません。
**VMware ツール** | 分析する各 VM に VMware ツール (10.2 以降) がインストールされ、実行されている必要があります。

**PowerShell** | Windows VM には、PowerShell バージョン 2.0 以降がインストールされている必要があります。
**ポート アクセス** | 分析する VM を実行している ESXi ホストでは、Azure Migrate アプライアンスが TCP ポート 443 に接続できる必要があります。


## <a name="dependency-analysis-requirements-agent-based"></a>依存関係の分析の要件 (エージェント ベース)

[依存関係の分析](concepts-dependency-visualization.md)を使用すると、評価して Azure に移行するオンプレミスのマシン間の依存関係を特定できます。 この表は、エージェント ベースの依存関係の分析を設定するための要件をまとめたものです。 

**要件** | **詳細** 
--- | --- 
**デプロイ前** | Azure Migrate を使用して、Azure Migrate プロジェクトを配置しておく必要があります。Server Assessment ツールプロジェクトに追加して、Azure Migrate プロジェクトを配置する必要があります。<br/><br/>  オンプレミスのマシンを検出するには、Azure Migrate アプライアンスをセットアップした後、依存関係の視覚化をデプロイします<br/><br/> 初めてプロジェクトを作成する方法については[こちら](create-manage-projects.md)を参照してください。<br/> 既存のプロジェクトに評価ツールを追加方法については[こちら](how-to-assess.md)を参照してください。<br/> [Hyper-V](how-to-set-up-appliance-hyper-v.md)、[VMware](how-to-set-up-appliance-vmware.md)、または物理サーバーの評価のために Azure Migrate アプライアンスを設定する方法について説明します。
**サポートされているマシン** | すべてのマシンでサポートされています。
**Azure Government** | 依存関係の視覚化は、Azure Government では使用できません。
**Log Analytics** | Azure Migrate は、依存関係の視覚化のために [Azure Monitor ログ](../azure-monitor/log-query/log-query-overview.md)の [Service Map](../azure-monitor/insights/service-map.md) ソリューションを使用します。<br/><br/> 新規または既存の Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けます。 Azure Migrate プロジェクトのワークスペースは、追加後に変更できません。 <br/><br/> このワークスペースは、Azure Migrate プロジェクトと同じサブスクリプションに含まれている必要があります。<br/><br/> ワークスペースは、米国東部リージョン、東南アジア リージョン、または西ヨーロッパ リージョンに存在する必要があります。 他のリージョンにあるワークスペースをプロジェクトに関連付けることはできません。<br/><br/> ワークスペースは、[Service Map がサポートされている](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)リージョンに存在する必要があります。<br/><br/> Log Analytics では、Azure Migrate に関連付けられたワークスペースは、移行プロジェクト キーとプロジェクト名のタグが付けられます。
**必要なエージェント** | 分析する各マシンに次のエージェントをインストールします。<br/><br/> [Microsoft Monitoring エージェント (MMA)](../azure-monitor/platform/agent-windows.md)。<br/> [依存関係エージェント](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> オンプレミス マシンがインターネットに接続されていない場合、Log Analytics ゲートウェイをダウンロードしてインストールする必要があります。<br/><br/> [依存関係エージェント](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)と [MMA](how-to-create-group-machine-dependencies.md#install-the-mma) のインストールの詳細を確認してください。
**Log Analytics ワークスペース** | このワークスペースは、Azure Migrate プロジェクトと同じサブスクリプションに含まれている必要があります。<br/><br/> Azure Migrate では、米国東部、東南アジア、および西ヨーロッパの各リージョンにあるワークスペースがサポートされます。<br/><br/>  ワークスペースは、[Service Map がサポートされている](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)リージョンに存在する必要があります。<br/><br/> Azure Migrate プロジェクトのワークスペースは、追加後に変更できません。
**コスト** | Service Map ソリューションでは、(Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けた日から) 最初の 180 日間は料金が発生しません。<br/><br/> 180 日が経過すると、Log Analytics の標準の料金が適用されます。<br/><br/> この関連付けられた Log Analytics ワークスペース内で Service Map 以外のソリューションを使用すると、Log Analytics の[標準の料金](https://azure.microsoft.com/pricing/details/log-analytics/)が発生します。<br/><br/> Azure Migrate プロジェクトが削除される際、それに伴ってワークスペースが削除されることはありません。 プロジェクトが削除された後は、Service Map を無料で使用することはできず、Log Analytics ワークスペースの有料階層に応じて各ノードの料金が請求されます。<br/><br/>Azure Migrate の一般提供 (GA - 2018 年 2 月 28 日) 前に作成したプロジェクトがある場合、Service Map の追加料金が発生する可能性があります。 GA の前の既存のワークスペースには引き続き課金されるため、180 日後にのみ支払いが発生するように、新しいプロジェクトを作成することをお勧めします。
**管理** | エージェントをワークスペースに登録する場合、Azure Migrate プロジェクトで提供される ID とキーを使用します。<br/><br/> Azure Migrate 以外で Log Analytics ワークスペースを使用できます。<br/><br/> 関連付けられた Azure Migrate プロジェクトを削除しても、ワークスペースは自動的に削除されません。 [手動で削除してください](../azure-monitor/platform/manage-access.md)。<br/><br/> Azure Migrate プロジェクトを削除しない場合は、Azure Migrate で作成されたワークスペースは削除しないでください。 削除した場合は、依存関係可視化機能は、期待どおりに機能しません。
**インターネット接続** | マシンがインターネットに接続されていない場合は、それらに Log Analytics ゲートウェイをインストールする必要があります。
**Azure Government** | エージェントベースの依存関係の分析はサポートされていません。


## <a name="next-steps"></a>次のステップ

- 評価作成のベストプラクティスを[確認](best-practices-assessment.md)します。
- VMware 評価の[準備](tutorial-prepare-vmware.md)
