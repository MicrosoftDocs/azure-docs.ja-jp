---
title: Azure Migrate でマシンの依存関係を使用してマシンをグループ化する | Microsoft Docs
description: Azure Migrate サービスでマシンの依存関係を使用してアセスメントを作成する方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: af47678b19209936aed86c132a8a3f400c3a7e8f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795034"
---
# <a name="group-machines-using-machine-dependency-mapping"></a>マシンの依存関係マッピングを使用したマシンのグループ化

この記事では、[Azure Migrate](migrate-overview.md) でのアセスメントのために、マシンの依存関係を視覚化してマシンのグループを作成する方法について説明します。 アセスメントを実行する前に、マシンの依存関係を照合して信頼度レベルの高い VM のグループを評価する場合、通常はこの方法を使用します。 依存関係の視覚化により、Azure への移行を効率よく計画できます。 こうすることで、Azure に移行する際に、何も残すことがなく、突然障害が発生することもなくなります。 同時に移行する必要のある、相互依存しているシステムをすべて検出し、稼働中のシステムがまだユーザーにサービス提供しているのか、あるいは移行せず使用中止する対象となるのかを特定することができます。

> [!NOTE]
> 依存関係可視化機能は、Azure Government では使用できません。

## <a name="prepare-for-dependency-visualization"></a>依存関係視覚化を準備する
Azure Migrate では、マシンの依存関係を視覚化できるように Azure Monitor ログで Service Map ソリューションを活用します。

### <a name="associate-a-log-analytics-workspace"></a>Log Analytics ワークスペースを関連付ける
依存関係の視覚化を利用するために、新規または既存の Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付ける必要があります。 Migrate プロジェクトが作成されている同じサブスクリプションのみにワークステーションを作成する、またはアタッチすることができます。

- プロジェクトに Log Analytics ワークスペースをアタッチするには、**[概要]** からプロジェクトの **[基本]** セクションに移動し、**[構成が必要]** をクリックします

    ![Log Analytics ワークスペースを関連付ける](./media/concepts-dependency-visualization/associate-workspace.png)

- ワークスペースを関連付けるときに、新しいワークスペースを作成するか、既存のワークスペースをアタッチするかを選択できます。
  - 新しいワークスペースを作成する場合は、ワークスペースの名前を指定する必要があります。 移行プロジェクトと同様の [Azure 地理的環境](https://azure.microsoft.com/global-infrastructure/geographies/)のリージョンでワークスペースが作成されます。
  - 既存のワークスペースをアタッチするときは、移行プロジェクトと同じサブスクリプション内の使用可能なすべてのワークスペースから選択できます。 表示されるのは、[Service Map がサポートされている](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions)リージョンで作成されたワークスペースのみであることに注意してください。 ワークスペースをアタッチできるようにするには、ワークスペースへの "読み取り" アクセスがあることを確認してください。

> [!NOTE]
> Migration プロジェクトに関連付けられているワークスペースは変更できません。

### <a name="download-and-install-the-vm-agents"></a>VM エージェントをダウンロードしてインストールする
ワークステーションを構成したら、評価するオンプレミスの各マシンにエージェントをダウンロードしてインストールする必要があります。 また、インターネットに接続されていないマシンの場合、[Log Analytics ゲートウェイ](../azure-monitor/platform/gateway.md)をダウンロードしてインストールする必要があります。

1. **[概要]** で **[管理]** > **[マシン]** をクリックし、必要なマシンを選択します。
2. **[依存関係]** 列で、**[エージェントのインストール]** をクリックします。
3. **[依存関係]** ページで、評価する各 VM に Microsoft Monitoring Agent (MMA) と依存関係エージェントをダウンロードしてインストールします。
4. ワークスペース ID とキーをコピーします。 これらは、MMA をオンプレミスのマシンにインストールするときに必要になります。

> [!NOTE]
> エージェントのインストールを自動化するには、System Center Configuration Manager などの任意のデプロイ ツールを使用するか、または Azure Migrate 用のエージェント デプロイ ソリューションを持つマイクロソフト パートナー ツールの [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) を使用します。

### <a name="install-the-mma"></a>MMA をインストールする

#### <a name="install-the-agent-on-a-windows-machine"></a>Windows マシンにエージェントをインストールする

Windows マシンにエージェントをインストールするには、次の手順に従います。

1. ダウンロードしたエージェントをダブルクリックします。
2. **[ようこそ]** ページで **[次へ]** をクリックします。 **[ライセンス条項]** ページで、**[同意する]** をクリックしてライセンスに同意します。
3. **[インストール先のフォルダー]** で、既定のインストール フォルダーをそのまま使用するか変更し、**[次へ]** をクリックします。
4. **[エージェントのセットアップ オプション]** で、**[Azure Log Analytics]** > **[次へ]** の順にクリックします。
5. **[追加]** をクリックして、新しい Log Analytics ワークスペースを追加します。 ポータルからコピーしたワークスペース ID とキーを貼り付けます。 **[次へ]** をクリックします。

エージェントは、コマンド ラインからインストールするか、System Center Configuration Manager などの自動化された方法を使用してインストールすることができます。 このような方法を使用して MMA エージェントをインストールする方法については、[詳細](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)のページを参照してください。

#### <a name="install-the-agent-on-a-linux-machine"></a>Linux マシンにエージェントをインストールする

Linux マシンにエージェントをインストールするには、次の手順に従います。

1. 該当するバンドル (x86 または x64) を、scp/sftp を使用して Linux コンピューターに転送します。
2. --install 引数を使用してバンドルをインストールします。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

MMA でサポートされる Linux オペレーティング システムの一覧は、[ここ](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)をご覧ください。

#### <a name="install-the-agent-on-a-machine-monitored-by-scom"></a>SCOM によって監視されているマシンにエージェントをインストールする

System Center Operations Manager 2012 R2 以降によって監視されているマシンの場合、MMA エージェントをインストールする必要はありません。 Service Map は、SCOM MMA を利用して必要な依存関係データを収集する SCOM と統合されています。 統合を有効にする方法については、[こちら](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)のガイダンスを参照してください。 ただし、このようなマシンには、依存関係エージェントをインストールする必要がある点に注意してください。


### <a name="install-the-dependency-agent"></a>依存関係エージェントをインストールする
1. Windows マシンに依存関係エージェントをインストールするには、セットアップ ファイルをダブルクリックし、ウィザードに従います。
2. Linux マシンに依存関係エージェントをインストールするには、次のコマンドを使用してルートとしてインストールします。

    ```sh InstallDependencyAgent-Linux64.bin```

[Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) および [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) オペレーティング システムの依存関係エージェントのサポートに関する詳細を確認してください。

スクリプトを使用して依存関係エージェントをインストールする方法については、[こちら](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)をご覧ください。


## <a name="create-a-group"></a>グループの作成

1. エージェントをインストールしたら、ポータルに移動し、**[管理]** > **[マシン]** をクリックします。
2. エージェントをインストールしたマシンを検索します。
3. マシンの **[依存関係]** 列に **[依存関係の表示]** と表示されます。 この列をクリックすると、マシンの依存関係が表示されます。
4. マシンの依存関係マップには次の詳細情報が表示されます。
    - マシンに対する受信 (クライアント) と送信 (サーバー) の TCP 接続
        - MMA と依存関係エージェントがインストールされていない依存マシンは、ポート番号によってグループ化されます。
        - MMA と依存関係エージェントがインストールされている依存マシンは、別のボックスに表示されます。
    - マシンで実行しているプロセス。各マシンのボックスを展開してプロセスを表示できます。
    - 各マシンの完全修飾ドメイン名、オペレーティング システム、MAC アドレスなどのプロパティ。各マシンのボックスをクリックして、これらの詳細を表示できます。

      ![マシンの依存関係の表示](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. 時間範囲のラベルで期間をクリックすると、さまざまな期間の依存関係を表示できます。 既定では、範囲は 1 時間です。 時間の範囲を変更することも、開始日と終了日および期間を指定することもできます。

   > [!NOTE]
   >    現在、依存関係の視覚化 UI は、1 時間を超える時間の範囲の選択をサポートしていません。 長期間にわたって[依存関係データのクエリを実行する](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)には、Azure Monitor ログを使用してください。

5. グループ化する依存関係のあるマシンを特定したら、マップ上で Ctrl キーを押しながらクリックして複数のマシンを選択し、**[グループ マシン]** をクリックします。
6. グループ名を指定します。 依存するマシンが Azure Migrate によって検出されていることを確認します。

    > [!NOTE]
    > 依存するマシンが Azure Migrate によって検出されない場合は、マシンをグループに追加できません。 そのようなマシンをグループに追加するには、vCenter Server で正しい範囲に対して検出プロセスを再び実行して、マシンが Azure Migrate によって検出されることを確認する必要があります。  

7. このグループの評価を作成する場合は、グループの新しい評価を作成するためのチェックボックスをオンにします。
8. **[OK]** をクリックしてグループを保存します。

グループが作成されたら、グループのすべてのマシンにエージェントをインストールし、グループ全体の依存関係を視覚化してグループを絞り込むことをお勧めします。

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Azure Monitor ログから依存関係データにクエリを実行する

Service Map によってキャプチャされた依存関係データは、Azure Migrate プロジェクトに関連付けられている Log Analytics ワークスペースでのクエリに使用できます。 Azure Monitor ログ内でクエリを実行する Service Map データ テーブルの[詳細を参照してください](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)。 

Kusto クエリを実行するには:

1. エージェントをインストールしたら、ポータルに移動し、**[概要]** をクリックします。
2. **[概要]** で、プロジェクトの **[基本]** セクションに移動し、**[OMS ワークスペース]** の横にあるワークスペース名をクリックします。
3. Log Analytics ワークスペース ページで **[全般]** > 、**[ログ]** の順にクリックします。
4. Azure Monitor ログを使用して依存関係データを収集するためのクエリを作成します。 次のセクションでサンプル クエリを見つけます。
5. [実行] をクリックしてクエリを実行します。 

Kusto クエリの作成方法の[詳細を参照してください](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。 

### <a name="sample-azure-monitor-logs-queries"></a>Azure Monitor ログのサンプル クエリ

依存関係データを抽出するために使用できるサンプル クエリは次のとおりです。 クエリを変更して、目的のデータ ポイントを抽出できます。 依存関係データ レコード内のフィールドの完全なリストは、[こちら](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)で入手できます。 その他のサンプル クエリについては、[こちら](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)を参照してください。

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>一連のマシンの受信接続を集計する

接続メトリック用のテーブル内のレコード (VMConnection) は、個々の物理ネットワーク接続を表していないことに注意してください。 複数の物理ネットワーク接続は、論理接続にグループ化されます。 物理ネットワーク接続データがどのように VMConnection 内の単一の論理レコードに集約されるかについては、[詳細を確認](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections)してください。 

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

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>一連のマシン間での受信接続で送受信されたデータの量を集計する

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

- [依存関係の可視化の詳細については](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization)、よく寄せられる質問を確認します。
- グループの依存関係を視覚化してグループを絞り込む[方法を確認](how-to-create-group-dependencies.md)をします。
- 評価の計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
