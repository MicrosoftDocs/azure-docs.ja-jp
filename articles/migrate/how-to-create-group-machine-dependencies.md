---
title: Azure Migrate Server Assessment でエージェントベースの依存関係の分析を設定する
description: この記事では、Azure Migrate Server Assessment でエージェントベースの依存関係の分析を設定する方法について説明します。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 772602a11878276da3b81b84e7f4016997375077
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183065"
---
# <a name="set-up-dependency-visualization"></a>依存関係の視覚化を設定する

この記事では、Azure Migrate: Server Assessment でエージェントベースの依存関係の分析を設定する方法について説明します。 [依存関係の分析](concepts-dependency-visualization.md)は、評価や Azure への移行を行うマシン間の依存関係を特定し、理解するために役立ちます。

## <a name="before-you-start"></a>開始する前に

- 次のエージェントベースの依存関係分析のサポートとデプロイの要件を確認します。
    - [VMware VM](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [物理サーバー](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Hyper-V VM](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- 以下を実行します。
    - Azure Migrate プロジェクトがあること。 ない場合は、今すぐ[作成](./create-manage-projects.md)します。
    - Azure Migrate: Server Assessment ツールがプロジェクトに[追加](how-to-assess.md)されていることを確認します。
    - オンプレミスのマシンを検出するための [Azure Migrate アプライアンス](migrate-appliance.md)を設定すること。 アプライアンスでオンプレミスのマシンが検出されて、メタデータとパフォーマンス データが Azure Migrate: Server Assessment を使用して作成する方法について説明します。 次に対してアプライアンスを設定します。
        - [VMware](how-to-set-up-appliance-vmware.md) VM
        - [Hyper-V VM](how-to-set-up-appliance-hyper-v.md)
        - [物理サーバー](how-to-set-up-appliance-physical.md)
- 依存関係の視覚化を利用するには、[Log Analytics ワークスペース](../azure-monitor/logs/manage-access.md)を Azure Migrate プロジェクトに関連付けます。
    - Azure Migrate アプライアンスを設定して Azure Migrate プロジェクトでマシンが検出された後にのみ、ワークスペースをアタッチできます。
    - ワークスペースが、Azure Migrate プロジェクトを含むサブスクリプション内にあることを確認します。
    - ワークスペースは、米国東部リージョン、東南アジア リージョン、または西ヨーロッパ リージョンに存在する必要があります。 他のリージョンにあるワークスペースをプロジェクトに関連付けることはできません。
    - ワークスペースは、[Service Map がサポートされている](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)リージョンに存在する必要があります。
    - 新規または既存の Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けることができます。
    - ワークスペースのアタッチは、マシンの依存関係の視覚化を初めて設定したときに行います。 Azure Migrate プロジェクトのワークスペースは、追加後に変更できません。
    - Log Analytics では、Azure Migrate に関連付けられたワークスペースは、移行プロジェクト キーとプロジェクト名のタグが付けられます。

## <a name="associate-a-workspace"></a>ワークスペースを関連付ける

1. 評価対象のマシンを検出したら、 **[サーバー]**  > **Azure Migrate:Server Assessment** で、 **[概要]** をクリックします。  
2. **Azure Migrate:Server Assessment** で、 **[Essentials]** をクリックします。
3. **[OMS ワークスペース]** で、 **[構成が必要]** をクリックします。

     ![Log Analytics ワークスペースを構成する](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. **[OMS ワークスペースの構成]** で、新しいワークスペースを作成するか、または既存のものを使用するかを指定します。
    - 移行プロジェクト サブスクリプションのすべてのワークスペースから、既存のワークスペースを選択できます。
    - ワークスペースを関連付けるには、ワークスペースに対する閲覧者アクセス権が必要です。
5. 新しいワークスペースを作成する場合は、その場所を選択します。

    ![新しいワークスペースの追加](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>VM エージェントをダウンロードしてインストールする

分析する各マシンにエージェントをインストールします。

> [!NOTE]
> System Center Operations Manager 2012 R2 以降によって監視されているマシンの場合、MMA エージェントをインストールする必要はありません。 Service Map は Operations Manager と統合されます。 統合ガイダンスに[従ってください](../azure-monitor/vm/service-map-scom.md#prerequisites)。

1. **Azure Migrate:Server Assessment** で、 **[Discovered servers]\(検出済みサーバー\)** をクリックします。
2. 依存関係の視覚化を利用して分析する各マシンについて、 **[依存関係]** 列の **[エージェントをインストールする必要があります]** をクリックします。
3. **[依存関係]** ページで、Windows 用または Linux 用の MMA と依存関係エージェントをダウンロードします。
4. **[MMA エージェントの構成]\(Configure MMA agent\)** で、ワークスペース ID とキーをコピーします。 これらは、MMA エージェントをインストールするときに必要です。

    ![エージェントをインストールする](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>MMA をインストールする

分析する Windows または Linux の各マシンに MMA をインストールします。

### <a name="install-mma-on-a-windows-machine"></a>Windows マシンに MMA をインストールする

Windows マシンにエージェントをインストールするには、次の手順に従います。

1. ダウンロードしたエージェントをダブルクリックします。
2. **[ようこそ]** ページで **[次へ]** をクリックします。 **[ライセンス条項]** ページで、 **[同意する]** をクリックしてライセンスに同意します。
3. **[インストール先のフォルダー]** で、既定のインストール フォルダーをそのまま使用するか変更し、 **[次へ]** をクリックします。
4. **[エージェントのセットアップ オプション]** で、 **[Azure Log Analytics]**  >  **[次へ]** の順にクリックします。
5. **[追加]** をクリックして、新しい Log Analytics ワークスペースを追加します。 ポータルからコピーしたワークスペース ID とキーを貼り付けます。 **[次へ]** をクリックします。

エージェントは、コマンド ラインからインストールするか、Configuration Manager または [Intigua](https://www.intigua.com/intigua-for-azure-migration) などの自動化された方法を使用してインストールすることができます。
- このような方法を使用して MMA エージェントをインストールする方法については、[詳細](../azure-monitor/agents/log-analytics-agent.md#installation-options)のページを参照してください。
- この[スクリプト](https://github.com/brianbar-MSFT/Install-MMA)を使用して、MMA エージェントをインストールすることもできます。
- MMA でサポートされる Windows オペレーティング システムの詳細については、[こちら](../azure-monitor/agents/agents-overview.md#supported-operating-systems)をご覧ください。

### <a name="install-mma-on-a-linux-machine"></a>Linux マシンに MMA をインストールする

Linux マシンに MMA をインストールするには、以下を実行します。

1. 該当するバンドル (x86 または x64) を、scp/sftp を使用して Linux コンピューターに転送します。
2. --install 引数を使用してバンドルをインストールします。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

MMA でサポートされる Linux オペレーティング システムの一覧は、[ここ](../azure-monitor/agents/agents-overview.md#supported-operating-systems)をご覧ください。 

## <a name="install-the-dependency-agent"></a>依存関係エージェントをインストールする

1. Windows マシンに依存関係エージェントをインストールするには、セットアップ ファイルをダブルクリックし、ウィザードに従います。
2. Linux マシンに依存関係エージェントをインストールするには、次のコマンドを使用してルートとしてインストールします。

    ```sh InstallDependencyAgent-Linux64.bin```

- スクリプトを使用して依存関係エージェントをインストールする方法については、[こちら](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent)をご覧ください。
- 依存関係エージェントでサポートされるオペレーティング システムの詳細については、[こちら](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems)をご覧ください。


## <a name="create-a-group-using-dependency-visualization"></a>依存関係の視覚化を使用してグループを作成する

ここで評価用のグループを作成します。 


> [!NOTE]
> 依存関係を視覚化するグループには、10 個を超えるマシンを含めないでください。 マシンが 10 台を超える場合は、小さいグループに分割します。

1. **Azure Migrate:Server Assessment** で、 **[Discovered servers]\(検出済みサーバー\)** をクリックします。
2. **[依存関係]** 列で、確認するマシンごとに **[依存関係の表示]** をクリックします。
3. 依存関係マップでは、以下を確認できます。
    - マシンとの間の受信 (クライアント) と送信 (サーバー) の TCP 接続。
    - 依存関係エージェントがインストールされていない依存するマシンは、ポート番号でグループ化されます。
    - 依存関係エージェントがインストールされている依存するマシンは、別のボックスとして表示されます。
    - マシン内で実行されているプロセス。 各マシンのボックスを展開すると、プロセスが表示されます。
    - マシンのプロパティ (FQDN、オペレーティング システム、MAC アドレスなど)。 各マシンのボックスをクリックすると、詳細が表示されます。

4. 時間範囲のラベルで期間をクリックすると、さまざまな期間の依存関係を表示できます。
    - 既定では、範囲は 1 時間です。 
    - 時間の範囲を変更することも、開始日と終了日および期間を指定することもできます。
    - 時間の範囲は最大 1 時間です。 それより長い範囲が必要な場合は、Azure Monitor を使って、長い期間に対して依存データのクエリを実行します。

5. 一緒にグループ化する依存するマシンを特定したら、マップ上で Ctrl キーを押しながらクリックして複数のマシンを選択し、 **[Group machines]\(マシンのグループ化\)** をクリックします。
6. グループ名を指定します。
7. 依存するマシンが Azure Migrate によって検出されていることを確認します。

    - 依存するマシンが Azure Migrate:Server Assessment によって検出されない場合は、それをグループに追加することはできません。
    - マシンを追加するには、検出をもう一度実行して、マシンが検出されることを確認します。

8. このグループの評価を作成する場合は、グループの新しい評価を作成するためのチェックボックスをオンにします。
8. **[OK]** をクリックしてグループを保存します。

グループを作成した後は、グループ内のすべてのマシンにエージェントをインストールし、グループ全体の依存関係を視覚化することをお勧めします。

## <a name="query-dependency-data-in-azure-monitor"></a>Azure Monitor で依存関係データのクエリを実行する

Azure Migrate プロジェクトに関連付けられた Log Analytics ワークスペースで、Service Map によってキャプチャされた依存関係データのクエリを実行できます。 Log Analytics は、Azure Monitor ログ クエリの記述と実行に使用されます。

- Log Analytics で Service Map データを検索する[方法の詳細情報](../azure-monitor/vm/service-map.md#log-analytics-records)を確認します。
- [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) でのクエリの記述の[概要を確認](../azure-monitor/logs/get-started-queries.md)します。

次のようにして依存関係データのクエリを実行します。

1. エージェントをインストールしたら、ポータルに移動し、 **[概要]** をクリックします。
2. **Azure Migrate:Server Assessment** で、 **[概要]** をクリックします。 下矢印をクリックして **[要点]\(Essentials\)** を展開します。
3. **[OMS ワークスペース]** で、ワークスペース名をクリックします。
3. Log Analytics ワークスペース ページで、 **[全般]** 、 **[ログ]** の順にクリックします。
4. クエリを作成し、 **[実行]** をクリックします。

### <a name="sample-queries"></a>サンプル クエリ

以下に、依存関係データを抽出するために使用できるサンプル クエリをいくつか示します。

- クエリを変更して、目的のデータ ポイントを抽出できます。
- 依存関係データ レコードの完全な一覧を[確認](../azure-monitor/vm/service-map.md#log-analytics-records)します。
- 追加のサンプル クエリを[確認](../azure-monitor/vm/service-map.md#sample-log-searches)します。

#### <a name="sample-review-inbound-connections"></a>サンプル:受信接続を確認する

一連の VM の受信接続を確認します。

- 接続メトリック用のテーブル (VMConnection) 内にあるレコードは、個々の物理ネットワーク接続を表すものではありません。
- 複数の物理ネットワーク接続は、論理接続にグループ化されます。
- 物理ネットワーク接続データが VMConnection 内でどのように集約されるかについての[詳細情報](../azure-monitor/vm/service-map.md#connections)を確認してください。

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>サンプル:送受信したデータを集計する

このサンプルは、一連のマシン間での受信接続で送受信されたデータの量を集計します。

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>次のステップ

グループの[評価を作成](how-to-create-assessment.md)します。
