---
title: Azure Migrate でマシンの依存関係を使用してマシンをグループ化する | Microsoft Docs
description: Azure Migrate サービスでマシンの依存関係を使用してアセスメントを作成する方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: c48323bb4c8798a0f36d3fda99a4c659187e0e81
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906387"
---
# <a name="set-up-dependency-visualization-for-assessment"></a>評価のために依存関係の視覚化を設定する

この記事では、依存関係マッピングを Azure Migrate:Server Assessment を使用して作成する方法について説明します。

依存関係マッピングは、評価および移行するマシン間の依存関係を視覚化するのに役立ちます。

- Azure Migrate:Server Assessment では、評価のためにマシンを集めます。 通常、一緒に移行するマシンです。
- 一般的に依存関係マッピングは、より高い信頼度でグループを評価したいときに使用します。
- 依存関係マッピングは、評価と移行を実行する前に、マシンの依存関係を照合するのに役立ちます。
- 依存関係のマッピングと視覚化は、Azure への移行を効果的に計画するのに役立ちます。 これにより、やり残しがないことを確実にして、移行中の突然の停止を回避することができます。
- マッピングを使用することで、一緒に移行する必要がある相互に依存するシステムを検出できます。 また、実行中のシステムがまだユーザーにサービスを提供しているか、または移行ではなく使用停止の対象であるかを特定できます。

依存関係の視覚化の[詳細を確認](concepts-dependency-visualization.md#how-does-it-work)します。

## <a name="before-you-start"></a>開始する前に

- Azure Migrate プロジェクトを[作成](how-to-add-tool-first-time.md)していることを確認します。
- プロジェクトを既に作成してある場合は、次のツールを[追加済み](how-to-assess.md)であることを確認します。Azure Migrate: Server Assessment ツールを追加済みであることを確認してください。
- マシンが Azure Migrate で検出されていることを確認します。これは、[VMware](how-to-set-up-appliance-vmware.md) または [Hyper-V](how-to-set-up-appliance-hyper-v.md) 用に Azure Migrate アプライアンスを設定することで実行できます。 アプライアンスでオンプレミスのマシンが検出されて、メタデータとパフォーマンス データが Azure Migrate: Server Assessment にメタデータとパフォーマンス データを送信します。 [詳細情報](migrate-appliance.md)。


**機能** | **注**
--- | ---
可用性 | 依存関係の視覚化は、Azure Government では使用できません。
サービス マップ | 依存関係の視覚化では、Azure Monitor で Service Map ソリューションが使用されます。 [Service Map](../azure-monitor/insights/service-map.md) は、サーバー間の接続を自動的に検出して表示します。
エージェント | 依存関係の視覚化を使用するには、マップするマシン上に次のエージェントをインストールします。<br/> - [Log Analytics エージェント](../azure-monitor/platform/log-analytics-agent.md) (以前は Microsoft Monitoring Agent (MMA) と呼ばれていました)。<br/> - [Service Map の Dependency Agent](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent)。<br/><br/> エージェントのインストールを自動化するには、デプロイ ツール (System Center Configuration Manager など) を使用するか、または Azure Migrate 用のエージェント デプロイ ソリューションを備えたパートナー ツール ([Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) など) を使用します。
依存関係エージェント | Windows および Linux での [Dependency Agent](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent) のサポートを確認します。<br/><br/> スクリプトを使用した依存関係エージェントのインストールの[詳細情報](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)を確認します。
Log Analytics エージェント (MMA) | MMA のインストール方法の[詳細情報](../azure-monitor/platform/log-analytics-agent.md#install-and-configure-agent)を確認します。<br/><br/> System Center Operations Manager 2012 R2 以降によって監視されているマシンの場合、MMA エージェントをインストールする必要はありません。 Service Map は Operations Manager と統合されます。 統合を有効にする方法については、[こちら](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)のガイダンスを参照してください。 ただし、このようなマシンには、Dependency Agent をインストールする必要がある点に注意してください。<br/><br/> Log Analytics エージェントでサポートされている Linux オペレーティング システムを[確認](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)します。
評価グループ | 依存関係を視覚化するグループには、10 個を超えるマシンを含めないでください。 マシンが 10 台を超える場合は、小さいグループに分割して依存関係を視覚化してください。

## <a name="associate-a-log-analytics-workspace"></a>Log Analytics ワークスペースを関連付ける

依存関係の視覚化を利用するには、[Log Analytics ワークスペース](../azure-monitor/platform/manage-access.md)を Azure Migrate プロジェクトに関連付ける必要があります。

- ワークスペースのアタッチは、Azure Migrate プロジェクトのサブスクリプションでのみ可能です。
- 既存のワークスペースをアタッチするか、新しいワークスペースを作成できます。
- ワークスペースのアタッチは、マシンの依存関係の視覚化を初めて設定したときに行います。
- ワークスペースをアタッチできるのは、Azure Migrate プロジェクトでマシンが検出された後のみになります。 これは、Azure Migrate アプライアンスを [VMware](how-to-set-up-appliance-vmware.md) または [Hyper-V](how-to-set-up-appliance-hyper-v.md) 用に設定することで実行できます。 アプライアンスでオンプレミスのマシンが検出されて、メタデータとパフォーマンス データが Azure Migrate: Server Assessment にメタデータとパフォーマンス データを送信します。 [詳細情報](migrate-appliance.md)。

次の手順に従ってワークスペースをアタッチします。

1. **Azure Migrate:Server Assessment** で、 **[概要]** をクリックします。 Server Assessment ツールをまだ追加していない場合は、[最初にそれを実行します](how-to-assess.md)。
2. **[概要]** で下矢印をクリックして、 **[要点]\(Essentials\)** を展開します。
3. **[OMS ワークスペース]** で、 **[構成が必要]** をクリックします。
4. **[ワークスペースの構成]** で、新しいワークスペースを作成するか、または既存のものを使用するかを指定します。

    ![ワークスペースの追加](./media/how-to-create-group-machine-dependencies/workspace.png)

    - 新しいワークスペースの名前を指定すると、Azure Migrate プロジェクトと同じ場所に作成されます。
    - 既存のワークスペースをアタッチするときは、移行プロジェクトと同じサブスクリプション内の使用可能なすべてのワークスペースから選択できます。
    - ワークスペースをアタッチするには、閲覧者アクセス権が必要です。
    - プロジェクトに関連付けられたワークスペースは、アタッチ後は変更できません。

## <a name="download-and-install-the-vm-agents"></a>VM エージェントをダウンロードしてインストールする

依存関係マッピングを使用して視覚化するオンプレミスの各マシンにエージェントをダウンロードしてインストールします。

1. **Azure Migrate:Server Assessment** で、 **[Discovered servers]\(検出済みサーバー\)** をクリックします。
2. 依存関係の視覚化を使用するマシンごとに、 **[エージェントをインストールする必要があります]** をクリックします。
3. マシンの **[依存関係]** ページで **MMA をダウンロードしてインストール**し、適切なエージェントをダウンロードし、以下の説明に従ってインストールします。
4. **[Download and install Dependency agent]\(依存関係エージェントのダウンロードとインストール\)** で、適切なエージェントをダウンロードし、以下の説明に従ってインストールします。
5. **[MMA エージェントの構成]\(Configure MMA agent\)** で、ワークスペース ID とキーをコピーします。 これらは、MMA エージェントをインストールするときに必要です。

### <a name="install-the-mma"></a>MMA をインストールする

#### <a name="install-the-agent-on-a-windows-machine"></a>Windows マシンにエージェントをインストールする

Windows マシンにエージェントをインストールするには、次の手順に従います。

1. ダウンロードしたエージェントをダブルクリックします。
2. **[ようこそ]** ページで **[次へ]** をクリックします。 **[ライセンス条項]** ページで、 **[同意する]** をクリックしてライセンスに同意します。
3. **[インストール先のフォルダー]** で、既定のインストール フォルダーをそのまま使用するか変更し、 **[次へ]** をクリックします。
4. **[エージェントのセットアップ オプション]** で、 **[Azure Log Analytics]**  >  **[次へ]** の順にクリックします。
5. **[追加]** をクリックして、新しい Log Analytics ワークスペースを追加します。 ポータルからコピーしたワークスペース ID とキーを貼り付けます。 **[次へ]** をクリックします。

#### <a name="install-the-agent-on-a-linux-machine"></a>Linux マシンにエージェントをインストールする

Linux マシンにエージェントをインストールするには、次の手順に従います。

1. 該当するバンドル (x86 または x64) を、scp/sftp を使用して Linux コンピューターに転送します。
2. --install 引数を使用してバンドルをインストールします。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

### <a name="install-the-dependency-agent"></a>依存関係エージェントをインストールする
1. Windows マシンに依存関係エージェントをインストールするには、セットアップ ファイルをダブルクリックし、ウィザードに従います。
2. Linux マシンに依存関係エージェントをインストールするには、次のコマンドを使用してルートとしてインストールします。

    ```sh InstallDependencyAgent-Linux64.bin```

## <a name="create-a-group-using-dependency-visualization"></a>依存関係の視覚化を使用してグループを作成する

1. **Azure Migrate:Server Assessment** で、 **[Discovered servers]\(検出済みサーバー\)** をクリックします。
2. **[依存関係]** 列で、確認するマシンごとに **[依存関係の表示]** をクリックします。
3. 依存関係マップでは、以下を確認できます。
    - マシンとの間の受信 (クライアント) と送信 (サーバー) の TCP 接続。
    - 依存関係エージェントがインストールされていない依存するマシンは、ポート番号でグループ化されます。
    - 依存関係エージェントがインストールされている依存するマシンは、別のボックスとして表示されます。
    - マシン内で実行されているプロセス。 各マシンのボックスを展開すると、プロセスが表示されます。
    - マシンのプロパティ (FQDN、オペレーティング システム、MAC アドレスなど)。 各マシンのボックスをクリックすると、詳細が表示されます。

4. 時間範囲のラベルで期間をクリックすると、さまざまな期間の依存関係を表示できます。 既定では、範囲は 1 時間です。 時間の範囲を変更することも、開始日と終了日および期間を指定することもできます。

    > [!NOTE]
    > 時間の範囲は最大 1 時間です。 それより長い範囲が必要な場合は、Azure Monitor を使って、長い期間に対して依存データのクエリを実行します。

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

- Log Analytics で Service Map データを検索する[方法の詳細情報](../azure-monitor/insights/service-map.md#log-analytics-records)を確認します。
- [Log Analytics](../azure-monitor/log-query/get-started-portal.md) でのクエリの記述の[概要を確認](../azure-monitor/log-query/get-started-queries.md)します。

次のようにして依存関係データのクエリを実行します。

1. エージェントをインストールしたら、ポータルに移動し、 **[概要]** をクリックします。
2. **Azure Migrate:Server Assessment** で、 **[概要]** をクリックします。 下矢印をクリックして **[要点]\(Essentials\)** を展開します。
3. **[OMS ワークスペース]** で、ワークスペース名をクリックします。
3. Log Analytics ワークスペース ページで、 **[全般]** 、 **[ログ]** の順にクリックします。
4. クエリを作成し、 **[実行]** をクリックします。

### <a name="sample-queries"></a>サンプル クエリ

依存関係データを抽出するために使用できる多くのサンプル クエリが用意されています。

- クエリを変更して、目的のデータ ポイントを抽出できます。
- 依存関係データ レコードの完全な一覧を[確認](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)します。
- 追加のサンプル クエリを[確認](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)します。

#### <a name="sample-review-inbound-connections"></a>サンプル:受信接続を確認する

一連の VM の受信接続を確認します。

- 接続メトリック用のテーブル (VMConnection) 内にあるレコードは、個々の物理ネットワーク接続を表すものではありません。
- 複数の物理ネットワーク接続は、論理接続にグループ化されます。
- 物理ネットワーク接続データが VMConnection 内でどのように集約されるかについての[詳細情報](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections)を確認してください。

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

## <a name="next-steps"></a>次の手順

グループの[評価を作成](how-to-create-assessment.md)します。
