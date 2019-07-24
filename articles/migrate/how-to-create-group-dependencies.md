---
title: Azure Migrate のグループの依存関係マッピングを使用してアセスメント グループを絞り込む | Microsoft Docs
description: Azure Migrate サービスのグループの依存関係マッピングを使用して、アセスメントを絞り込む方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 3ee528cc68a2a5637e85dc1d5ef68203916138e7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786880"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>グループの依存関係マッピングを使用したグループの絞り込み

この記事では、グループ内のすべてのマシンの依存関係を視覚化して、グループを絞り込む方法を説明します。 アセスメントを実行する前に、グループの依存関係を照合して既存のグループのメンバーシップを調整する場合、通常はこの方法を使用します。 依存関係の視覚化を使用したグループの微調整により、Azure への移行を効率よく計画できます。 一緒に移行する必要がある、互いに依存するすべてのシステムを検出できます。 こうすることで、Azure に移行する際に、何も残すことがなく、突然障害が発生することもなくなります。


> [!NOTE]
> 依存関係を視覚化するグループには、10 個を超えるマシンを含めないでください。 グループ内に 10 個を超えるマシンがある場合、依存関係視覚化機能を利用するには小さいグループに分割することをお勧めします。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prepare-for-dependency-visualization"></a>依存関係視覚化を準備する
Azure Migrate では、マシンの依存関係を視覚化できるように Azure Monitor ログで Service Map ソリューションを活用します。

> [!NOTE]
> 依存関係可視化機能は、Azure Government では使用できません。

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
グループの依存関係を表示するには、グループに含まれるオンプレミスの各マシンにエージェントをダウンロードしてインストールする必要があります。 また、インターネットに接続されていないマシンの場合、[Log Analytics ゲートウェイ](../azure-monitor/platform/gateway.md)をダウンロードしてインストールする必要があります。

1. **[概要]** で **[管理]** > **[グループ]** をクリックし、必要なグループに移動します。
2. マシンの一覧の **[依存関係エージェント]** 列で **[インストールする必要があります]** をクリックし、エージェントのダウンロードおよびインストールの方法に関する手順を表示します。
3. **[依存関係]** ページで、グループに含まれる各 VM に Microsoft Monitoring Agent (MMA) と依存関係エージェントをダウンロードしてインストールします。
4. ワークスペース ID とキーをコピーします。 これらは、MMA をオンプレミスのマシンにインストールするときに必要になります。

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

#### <a name="install-the-agent-on-a-machine-monitored-by-system-center-operations-manager"></a>System Center Operations Manager によって監視されているマシンにエージェントをインストールする

Operations Manager 2012 R2 以降によって監視されているマシンの場合、MMA エージェントをインストールする必要はありません。 Service Map は、Operations Manager MMA を利用して必要な依存関係データを収集する Operations Manager と統合されています。 統合を有効にする方法については、[こちら](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)のガイダンスを参照してください。 ただし、このようなマシンには、依存関係エージェントをインストールする必要がある点に注意してください。

### <a name="install-the-dependency-agent"></a>依存関係エージェントをインストールする
1. Windows マシンに依存関係エージェントをインストールするには、セットアップ ファイルをダブルクリックし、ウィザードに従います。
2. Linux マシンに依存関係エージェントをインストールするには、次のコマンドを使用してルートとしてインストールします。

    ```sh InstallDependencyAgent-Linux64.bin```

[Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) および [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) オペレーティング システムの依存関係エージェントのサポートに関する詳細を確認してください。

スクリプトを使用して依存関係エージェントをインストールする方法については、[こちら](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)をご覧ください。

## <a name="refine-the-group-based-on-dependency-visualization"></a>依存関係視覚化に基づいてグループを絞り込む
グループのすべてのマシンにエージェントをインストールしたら、次の手順に従って、グループの依存関係を視覚化してグループを絞り込みます。

1. Azure Migrate プロジェクトで、**[管理]** の  **[グループ]** をクリックし、グループを選択します。
2. グループ ページで  **[依存関係の表示]** をクリックして、グループの依存関係マップを開きます。
3. グループの依存関係マップには次の詳細情報が表示されます。
   - グループに含まれるすべてのマシンに対する受信 (クライアント) と送信 (サーバー) の TCP 接続
       - MMA と依存関係エージェントがインストールされていない依存マシンは、ポート番号によってグループ化されます。
       - MMA と依存関係エージェントがインストールされている依存マシンは、別のボックスに表示されます。
   - マシンで実行しているプロセス。各マシンのボックスを展開してプロセスを表示できます。
   - 各マシンの完全修飾ドメイン名、オペレーティング システム、MAC アドレスなどのプロパティ。各マシンのボックスをクリックして、これらの詳細を表示できます。

     ![グループの依存関係の表示](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. さらに細かい依存関係を表示するには、時間の範囲をクリックして変更します。 既定では、範囲は 1 時間です。 時間の範囲を変更することも、開始日と終了日および期間を指定することもできます。

   > [!NOTE]
   >    現在、依存関係の視覚化 UI は、1 時間を超える時間の範囲の選択をサポートしていません。 長期間にわたって[依存関係データのクエリを実行する](https://docs.microsoft.com/azure/migrate/how-to-create-a-group)には、Azure Monitor ログを使用してください。

4. 依存マシンと、各マシン内で実行しているプロセスを確認し、グループに対して追加または削除する必要があるマシンを特定します。
5. グループにマシンを追加またはグループから削除するには、Ctrl キーを押しながらクリックしてマップ上でマシンを選択します。
    - 追加できるのは、検出済みのマシンだけです。
    - グループのマシンを追加または削除すると、そのグループの過去のアセスメントが無効になります。
    - 必要に応じて、グループを変更したときに新しいアセスメントを作成することもできます。
5. **[OK]** をクリックしてグループを保存します。

    ![マシンの追加または削除](./media/how-to-create-group-dependencies/add-remove.png)

グループの依存関係マップに表示されている特定のマシンの依存関係を確認する場合は、[マシンの依存関係マッピング](how-to-create-group-machine-dependencies.md)を設定します。

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Azure Monitor ログから依存関係データにクエリを実行する

Service Map によってキャプチャされた依存関係データは、Azure Migrate プロジェクトに関連付けられている Log Analytics ワークスペースでのクエリに使用できます。 Azure Monitor ログ内でクエリを実行する Service Map データ テーブルの[詳細を参照してください](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)。 

Kusto クエリを実行するには:

1. エージェントをインストールしたら、ポータルに移動し、**[概要]** をクリックします。
2. **[概要]** で、プロジェクトの **[基本]** セクションに移動し、**[OMS ワークスペース]** の横にあるワークスペース名をクリックします。
3. Log Analytics ワークスペース ページで **[全般]** > 、**[ログ]** の順にクリックします。
4. Azure Monitor ログを使用して依存関係データを収集するためのクエリを作成します。 次のセクションでサンプル クエリを見つけます。
5. [実行] をクリックしてクエリを実行します。 

Kusto クエリの作成方法の[詳細を参照してください](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。 

## <a name="sample-azure-monitor-logs-queries"></a>Azure Monitor ログのサンプル クエリ

依存関係データを抽出するために使用できるサンプル クエリは次のとおりです。 クエリを変更して、目的のデータ ポイントを抽出できます。 依存関係データ レコード内のフィールドの完全なリストは、[こちら](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)で入手できます。 その他のサンプル クエリについては、[こちら](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)を参照してください。

### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>一連のマシンの受信接続を集計する

接続メトリック用のテーブル内のレコード (VMConnection) は、個々の物理ネットワーク接続を表していないことに注意してください。 複数の物理ネットワーク接続は、論理接続にグループ化されます。 物理ネットワーク接続データがどのように VMConnection 内の単一の論理レコードに集約されるかについては、[詳細を確認](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections)してください。 

```
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
- 評価の計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
