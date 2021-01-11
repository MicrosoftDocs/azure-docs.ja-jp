---
title: Azure Migrate での Hyper-V の評価のサポート
description: Azure Migrate Server Assessment を使用した Hyper-V の評価のサポートについて説明します。
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: 3d55d71fc4e9551fa19afe8c3f37d31107e4d2e7
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641825"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Hyper-V の評価のサポート マトリックス

この記事では、[Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) ツールを使用して、Azure に移行するために Hyper-V VM を評価する場合の前提条件とサポート要件について説明します。 Hyper-V VM を Azure に移行する場合は、[移行のサポート マトリックス](migrate-support-matrix-hyper-v-migration.md)を確認してください。

Hyper-V VM の評価を設定するには、Azure Migrate プロジェクトを作成し、そのプロジェクトに Server Assessment ツールを追加します。 ツールを追加した後、[Azure Migrate アプライアンス](migrate-appliance.md)をデプロイします。 アプライアンスは継続的にオンプレミスのマシンを検出し、マシンのメタデータとパフォーマンス データを Azure に送信します。 検出が完了したら、検出されたマシンをグループにまとめ、グループの評価を実行します。


## <a name="limitations"></a>制限事項

**サポート** | **詳細**
--- | ---
**評価の上限** | 1 つの [Azure Migrate プロジェクト](migrate-support-matrix.md#azure-migrate-projects)で最大 35,000 の Hyper-V VM を検出し、評価することができます。
**プロジェクトの制限** | 1 つの Azure サブスクリプションで複数のプロジェクトを作成できます。 Hyper-V VM に加えて、それぞれの評価の上限に達するまで、1 つのプロジェクトに VMware VM と物理サーバーを含めることができます。
**検出** | Azure Migrate アプライアンスでは、最大 5,000 台の Hyper-V VM を検出できます。<br/><br/> アプライアンスは、最大 300 台の Hyper-V ホストに接続できます。
**評価** | 1 つのグループに最大 35,000 個のマシンを追加できます。<br/><br/> グループごとに、1 回の評価で最大 35,000 個の VM を評価できます。

評価の詳細については[こちら](concepts-assessment-calculation.md)をご覧ください。



## <a name="hyper-v-host-requirements"></a>Hyper-V ホストの要件

| **サポート**                | **詳細**               
| :-------------------       | :------------------- |
| **Hyper-V ホスト**       | Hyper-V ホストは、スタンドアロンにすることも、クラスターにデプロイすることもできます。<br/><br/> Hyper-V ホストは、Windows Server 2019、Windows Server 2016、または Windows Server 2012 R2 を実行できます。 これらのオペレーティング システムの Server コア インストールもサポートされています。 <br/>Windows Server 2012 を実行している Hyper-V ホスト上にある VM を評価することはできません。
| **アクセス許可**           | Hyper-V ホストに対する管理者のアクセス許可が必要です。 <br/> 管理者のアクセス許可を割り当てたくない場合には、代わりにローカルまたはドメインのユーザー アカウントを作成し、そのユーザー アカウントを Remote Management Users、Hyper-V Administrators、Performance Monitor Users のグループに追加します。 |
| **PowerShell リモート処理**   | [PowerShell リモート処理](/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7)は、各 Hyper-V ホストで有効にする必要があります。 |
| **Hyper-V レプリカ**       | Hyper-V レプリカを使用する (または、同じ VM 識別子を持つ複数の VM がある) 場合、Azure Migrate を使用して元の VM とレプリケートされた VM の両方を検出すると、Azure Migrate によって生成される評価が正確ではない可能性があります。 |


## <a name="vm-requirements"></a>VM 要件

| **サポート**                  | **詳細**               
| :----------------------------- | :------------------- |
| **オペレーティング システム** | すべてのオペレーティング システムを、移行のために評価することができます。  |
| **統合サービス**       | オペレーティング システム情報をキャプチャするには、評価する VM で [Hyper-V 統合サービス](/virtualization/hyper-v-on-windows/reference/integration-services)が実行されている必要があります。 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate アプライアンスの要件

Azure Migrate では、[Azure Migrate アプライアンス](migrate-appliance.md)を使用して検出と評価を行います。 ポータルからダウンロードした圧縮された Hyper-V VHD、または [PowerShell スクリプト](deploy-appliance-script.md)を使用して、アプライアンスをデプロイできます。

- Hyper-V の[アプライアンスの要件](migrate-appliance.md#appliance---hyper-v)を確認する。
- アプライアンスが[パブリック](migrate-appliance.md#public-cloud-urls)および [Government](migrate-appliance.md#government-cloud-urls) クラウドでアクセスする必要がある URL について確認します。
- Azure Government では、[スクリプトを使用して](deploy-appliance-script-government.md)アプライアンスをデプロイする必要があります。

## <a name="port-access"></a>ポート アクセス

次の表は、評価のためのポート要件をまとめたものです。

**[デバイス]** | **接続**
--- | ---
**アプライアンス** | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br/><br/> ポート 44368 で、次の URL を使用してアプライアンス管理アプリにリモートでアクセスするためのインバウンド接続: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> ポート 443 (HTTPS) で検出とパフォーマンスのメタデータを Azure Migrate に送信するためのアウトバウンド接続。
**Hyper-V ホスト/クラスター** | Common Information Model (CIM) セッションを使用し、WinRM ポート 5985 (HTTP) で、Hyper-V VM のメタデータとパフォーマンスのデータをプルするための受信接続。

## <a name="agent-based-dependency-analysis-requirements"></a>エージェント ベースの依存関係の分析の要件

[依存関係の分析](concepts-dependency-visualization.md)を使用すると、評価して Azure に移行するオンプレミスのマシン間の依存関係を特定できます。 この表は、エージェント ベースの依存関係の分析を設定するための要件をまとめたものです。 Hyper-V では現在、エージェント ベースの依存関係の視覚化のみがサポートされています。 

**要件** | **詳細** 
--- | --- 
**デプロイ前** | Server Assessment ツールがプロジェクトに追加された状態で、Azure Migrate プロジェクトを準備する必要があります。<br/><br/>  オンプレミスのマシンを検出するには、Azure Migrate アプライアンスをセットアップした後、依存関係の視覚化をデプロイします<br/><br/> 初めてプロジェクトを作成する方法については[こちら](create-manage-projects.md)を参照してください。<br/> 既存のプロジェクトに評価ツールを追加方法については[こちら](how-to-assess.md)を参照してください。<br/> [Hyper-V VM](how-to-set-up-appliance-hyper-v.md) の評価用に Azure Migrate アプライアンスを設定する方法を参照してください。
**Azure Government** | 依存関係の視覚化は、Azure Government では使用できません。
**Log Analytics** | Azure Migrate は、依存関係の視覚化のために [Azure Monitor ログ](../azure-monitor/log-query/log-query-overview.md)の [Service Map](../azure-monitor/insights/service-map.md) ソリューションを使用します。<br/><br/> 新規または既存の Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けます。 Azure Migrate プロジェクトのワークスペースは、追加後に変更できません。 <br/><br/> このワークスペースは、Azure Migrate プロジェクトと同じサブスクリプションに含まれている必要があります。<br/><br/> ワークスペースは、米国東部リージョン、東南アジア リージョン、または西ヨーロッパ リージョンに存在する必要があります。 他のリージョンにあるワークスペースをプロジェクトに関連付けることはできません。<br/><br/> ワークスペースは、[Service Map がサポートされている](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)リージョンに存在する必要があります。<br/><br/> Log Analytics では、Azure Migrate に関連付けられたワークスペースは、移行プロジェクト キーとプロジェクト名のタグが付けられます。
**必要なエージェント** | 分析する各マシンに次のエージェントをインストールします。<br/><br/> [Microsoft Monitoring エージェント (MMA)](../azure-monitor/platform/agent-windows.md)。<br/> [依存関係エージェント](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> オンプレミス マシンがインターネットに接続されていない場合、Log Analytics ゲートウェイをダウンロードしてインストールする必要があります。<br/><br/> [依存関係エージェント](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)と [MMA](how-to-create-group-machine-dependencies.md#install-the-mma) のインストールの詳細を確認してください。
**Log Analytics ワークスペース** | このワークスペースは、Azure Migrate プロジェクトと同じサブスクリプションに含まれている必要があります。<br/><br/> Azure Migrate では、米国東部、東南アジア、および西ヨーロッパの各リージョンにあるワークスペースがサポートされます。<br/><br/>  ワークスペースは、[Service Map がサポートされている](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)リージョンに存在する必要があります。<br/><br/> Azure Migrate プロジェクトのワークスペースは、追加後に変更できません。
**コスト** | Service Map ソリューションでは、(Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けた日から) 最初の 180 日間は料金が発生しません。<br/><br/> 180 日が経過すると、Log Analytics の標準の料金が適用されます。<br/><br/> この関連付けられた Log Analytics ワークスペース内で Service Map 以外のソリューションを使用すると、Log Analytics の[標準の料金](https://azure.microsoft.com/pricing/details/log-analytics/)が発生します。<br/><br/> Azure Migrate プロジェクトが削除される際、それに伴ってワークスペースが削除されることはありません。 プロジェクトが削除された後は、Service Map を無料で使用することはできず、Log Analytics ワークスペースの有料階層に応じて各ノードの料金が請求されます。<br/><br/>Azure Migrate の一般提供 (GA - 2018 年 2 月 28 日) 前に作成したプロジェクトがある場合、Service Map の追加料金が発生する可能性があります。 GA の前の既存のワークスペースには引き続き課金されるため、180 日後にのみ支払いが発生するように、新しいプロジェクトを作成することをお勧めします。
**管理** | エージェントをワークスペースに登録する場合、Azure Migrate プロジェクトで提供される ID とキーを使用します。<br/><br/> Azure Migrate 以外で Log Analytics ワークスペースを使用できます。<br/><br/> 関連付けられた Azure Migrate プロジェクトを削除しても、ワークスペースは自動的に削除されません。 [手動で削除してください](../azure-monitor/platform/manage-access.md)。<br/><br/> Azure Migrate プロジェクトを削除しない場合は、Azure Migrate で作成されたワークスペースは削除しないでください。 削除した場合は、依存関係可視化機能は、期待どおりに機能しません。
**インターネット接続** | マシンがインターネットに接続されていない場合は、それらに Log Analytics ゲートウェイをインストールする必要があります。
**Azure Government** | エージェントベースの依存関係の分析はサポートされていません。

## <a name="next-steps"></a>次のステップ

[Hyper-V VM の評価を準備する](tutorial-prepare-hyper-v.md)
