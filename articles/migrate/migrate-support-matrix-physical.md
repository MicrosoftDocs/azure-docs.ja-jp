---
title: Azure Migrate での物理サーバーの評価のサポート
description: Azure Migrate Server Assessment での物理サーバーの評価のサポートについて説明します。
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 2b96bff7468f0705f2b80f60dcd5248960495f16
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640125"
---
# <a name="support-matrix-for-physical-server-assessment"></a>物理サーバーの評価のサポート マトリックス 

この記事では、[Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) ツールを使用して、Azure に移行するために物理サーバーを評価する場合の前提条件とサポート要件について説明します。 物理サーバーを Azure に移行する場合は、[移行のサポート マトリックス](migrate-support-matrix-physical-migration.md)を確認してください。


物理サーバーを評価するには、Azure Migrate プロジェクトを作成し、そのプロジェクトに Server Assessment ツールを追加します。 ツールを追加した後、[Azure Migrate アプライアンス](migrate-appliance.md)をデプロイします。 アプライアンスは継続的にオンプレミスのマシンを検出し、マシンのメタデータとパフォーマンス データを Azure に送信します。 検出が完了したら、検出されたマシンをグループにまとめ、グループの評価を実行します。


## <a name="limitations"></a>制限事項

**サポート** | **詳細**
--- | ---
**評価の上限** | 1 つの [Azure Migrate プロジェクト](migrate-support-matrix.md#azure-migrate-projects)で最大 35,000 の物理サーバーを検出し、評価することができます。
**プロジェクトの制限** | 1 つの Azure サブスクリプションで複数のプロジェクトを作成できます。 物理サーバーに加えて、それぞれの評価の上限に達するまで、1 つのプロジェクトに VMware VM と Hyper-V VM を含めることができます。
**検出** | Azure Migrate アプライアンスでは、最大 1000 台の物理サーバーを検出できます。
**評価** | 1 つのグループに最大 35,000 個のマシンを追加できます。<br/><br/> 1 回の評価で最大 35,000 個のマシンを評価できます。

評価の詳細については[こちら](concepts-assessment-calculation.md)をご覧ください。

## <a name="physical-server-requirements"></a>物理サーバーの要件

| **サポート**                | **詳細**               
| :-------------------       | :------------------- |
| **物理サーバーの展開**       | 物理サーバーは、スタンドアロンにすることも、クラスターにデプロイすることもできます。 |
| **アクセス許可**           | **Windows:** ドメインに参加しているマシンにはドメイン アカウントを使用し、ドメインに参加していないマシンにはローカル アカウントを使用します。 次のグループにユーザー アカウントを追加する必要があります:リモート管理ユーザー、パフォーマンス モニター ユーザー、パフォーマンス ログ ユーザー。 <br/><br/> **Linux:** 検出する Linux サーバーのルート アカウントが必要です。 <br/> または、次のコマンドを使用して必要な機能が設定されていることを確認します。 <br/> setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br/> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk (/usr/sbin/fdisk が存在しない場合) <br/> setcap "cap_dac_override、cap_dac_read_search、cap_fowner、cap_fsetid、cap_setuid、cap_setpcap、cap_net_bind_service、cap_net_admin、cap_sys_chroot、cap_sys_admin、cap_sys_resource、cap_audit_control、cap_setfcap=+eip" /sbin/lvm <br/> setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode <br/> chmod a+r /sys/class/dmi/id/product_uuid
| **オペレーティング システム** | Windows Server 2003 および SUSE Linux を除くすべてのオペレーティング システムは、移行について評価することができます。 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate アプライアンスの要件

Azure Migrate では、[Azure Migrate アプライアンス](migrate-appliance.md)を使用して検出と評価を行います。 物理サーバーのアプライアンスは、VM または物理マシン上で実行できます。 

- 物理サーバーの[アプライアンスの要件](migrate-appliance.md#appliance---physical)を確認します。
- アプライアンスが[パブリック](migrate-appliance.md#public-cloud-urls)および [Government](migrate-appliance.md#government-cloud-urls) クラウドでアクセスする必要がある URL について確認します。
- アプライアンスは、Azure portal からダウンロードした [PowerShell スクリプト](how-to-set-up-appliance-physical.md)を使用して設定します。
Azure Government で、[このスクリプトを使用して](deploy-appliance-script-government.md)アプライアンスをデプロイします。

## <a name="port-access"></a>ポート アクセス

次の表は、評価のためのポート要件をまとめたものです。

**[デバイス]** | **接続**
--- | ---
**アプライアンス** | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br/><br/> ポート 44368 で、次の URL を使用してアプライアンス管理アプリにリモートでアクセスするためのインバウンド接続: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> ポート 443 (HTTPS) で検出とパフォーマンスのメタデータを Azure Migrate に送信するためのアウトバウンド接続。
**物理サーバー** | **Windows:** WinRM ポート 5985 (HTTP) で Windows サーバーから構成とパフォーマンスのメタデータをプルするためのインバウンド接続。 <br/><br/> **Linux:** ポート 22 (TCP) で Linux サーバーから構成とパフォーマンスのメタデータをプルするための受信接続。 |

## <a name="agent-based-dependency-analysis-requirements"></a>エージェント ベースの依存関係の分析の要件

[依存関係の分析](concepts-dependency-visualization.md)を使用すると、評価して Azure に移行するオンプレミスのマシン間の依存関係を特定できます。 この表は、エージェント ベースの依存関係の分析を設定するための要件をまとめたものです。 現在、物理サーバーではエージェント ベースの依存関係の分析のみがサポートされています。

**要件** | **詳細** 
--- | --- 
**デプロイ前** | Server Assessment ツールがプロジェクトに追加された状態で、Azure Migrate プロジェクトを準備する必要があります。<br/><br/>  オンプレミスのマシンを検出するには、Azure Migrate アプライアンスをセットアップした後、依存関係の視覚化をデプロイします<br/><br/> 初めてプロジェクトを作成する方法については[こちら](create-manage-projects.md)を参照してください。<br/> 既存のプロジェクトに評価ツールを追加方法については[こちら](how-to-assess.md)を参照してください。<br/> [Hyper-V](how-to-set-up-appliance-hyper-v.md)、[VMware](how-to-set-up-appliance-vmware.md)、または物理サーバーの評価のために Azure Migrate アプライアンスを設定する方法について説明します。
**Azure Government** | 依存関係の視覚化は、Azure Government では使用できません。
**Log Analytics** | Azure Migrate は、依存関係の視覚化のために [Azure Monitor ログ](../azure-monitor/log-query/log-query-overview.md)の [Service Map](../azure-monitor/insights/service-map.md) ソリューションを使用します。<br/><br/> 新規または既存の Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けます。 Azure Migrate プロジェクトのワークスペースは、追加後に変更できません。 <br/><br/> このワークスペースは、Azure Migrate プロジェクトと同じサブスクリプションに含まれている必要があります。<br/><br/> ワークスペースは、米国東部リージョン、東南アジア リージョン、または西ヨーロッパ リージョンに存在する必要があります。 他のリージョンにあるワークスペースをプロジェクトに関連付けることはできません。<br/><br/> ワークスペースは、[Service Map がサポートされている](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)リージョンに存在する必要があります。<br/><br/> Log Analytics では、Azure Migrate に関連付けられたワークスペースは、移行プロジェクト キーとプロジェクト名のタグが付けられます。
**必要なエージェント** | 分析する各マシンに次のエージェントをインストールします。<br/><br/> [Microsoft Monitoring エージェント (MMA)](../azure-monitor/platform/agent-windows.md)。<br/> [依存関係エージェント](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> オンプレミス マシンがインターネットに接続されていない場合、Log Analytics ゲートウェイをダウンロードしてインストールする必要があります。<br/><br/> [依存関係エージェント](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)と [MMA](how-to-create-group-machine-dependencies.md#install-the-mma) のインストールの詳細を確認してください。
**Log Analytics ワークスペース** | このワークスペースは、Azure Migrate プロジェクトと同じサブスクリプションに含まれている必要があります。<br/><br/> Azure Migrate では、米国東部、東南アジア、および西ヨーロッパの各リージョンにあるワークスペースがサポートされます。<br/><br/>  ワークスペースは、[Service Map がサポートされている](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)リージョンに存在する必要があります。<br/><br/> Azure Migrate プロジェクトのワークスペースは、追加後に変更できません。
**コスト** | Service Map ソリューションでは、(Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けた日から) 最初の 180 日間は料金が発生しません。<br/><br/> 180 日が経過すると、Log Analytics の標準の料金が適用されます。<br/><br/> この関連付けられた Log Analytics ワークスペース内で Service Map 以外のソリューションを使用すると、Log Analytics の[標準の料金](https://azure.microsoft.com/pricing/details/log-analytics/)が発生します。<br/><br/> Azure Migrate プロジェクトが削除される際、それに伴ってワークスペースが削除されることはありません。 プロジェクトが削除された後は、Service Map を無料で使用することはできず、Log Analytics ワークスペースの有料階層に応じて各ノードの料金が請求されます。<br/><br/>Azure Migrate の一般提供 (GA - 2018 年 2 月 28 日) 前に作成したプロジェクトがある場合、Service Map の追加料金が発生する可能性があります。 GA の前の既存のワークスペースには引き続き課金されるため、180 日後にのみ支払いが発生するように、新しいプロジェクトを作成することをお勧めします。
**管理** | エージェントをワークスペースに登録する場合、Azure Migrate プロジェクトで提供される ID とキーを使用します。<br/><br/> Azure Migrate 以外で Log Analytics ワークスペースを使用できます。<br/><br/> 関連付けられた Azure Migrate プロジェクトを削除しても、ワークスペースは自動的に削除されません。 [手動で削除してください](../azure-monitor/platform/manage-access.md)。<br/><br/> Azure Migrate プロジェクトを削除しない場合は、Azure Migrate で作成されたワークスペースは削除しないでください。 削除した場合は、依存関係可視化機能は、期待どおりに機能しません。
**インターネット接続** | マシンがインターネットに接続されていない場合は、それらに Log Analytics ゲートウェイをインストールする必要があります。
**Azure Government** | エージェントベースの依存関係の分析はサポートされていません。

## <a name="next-steps"></a>次のステップ

[物理サーバーの評価を準備します](tutorial-prepare-physical.md)。
