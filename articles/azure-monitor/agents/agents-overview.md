---
title: Azure 監視エージェントの概要 | Microsoft Docs
description: この記事では、Azure またはハイブリッド環境にホストされている仮想マシンの監視をサポートする Azure エージェントについて概説します。
services: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/12/2021
ms.openlocfilehash: a2f6023b86b96266be8e625fd5b0d6625500e3fc
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551472"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure Monitor エージェントの概要

仮想マシンおよびその他のコンピューティング リソースでは、ゲスト オペレーティング システムとワークロードのパフォーマンスと可用性を測定するために必要な監視データを収集するエージェントが必要です。 この記事では、Azure Monitor で使用されるエージェントについて説明します。特定の環境の要件を満たす上で必要なエージェントを判断するのに役に立ちます。

> [!NOTE]
> Azure Monitor と Log Analytics が最近統合されたため、現在 Azure Monitor には複数のエージェントがあります。 機能に重複がある場合もありますが、それぞれに固有の機能があります。 要件によっては、マシンに 1 つ以上のエージェントが必要になる場合があります。 

特定のマシンに対して、1 つのエージェントでは完全に満たすことができない特定の要件セットがある場合があります。 たとえば、Azure Diagnostics 拡張機能を必要とするメトリック アラート以外に、Log Analytics エージェントと依存関係エージェントを必要とする VM insights の機能も利用したほうがよい場合があります。 このような場合、複数のエージェントを使用できます。これは、それぞれの機能を必要とする顧客を対象にした一般的なシナリオです。

## <a name="summary-of-agents"></a>エージェントの概要

次の表は、Windows と Linux の Azure Monitor エージェントを簡単に比較したものです。 それぞれの詳細については、以下のセクションで説明します。

> [!NOTE]
> Azure Monitor エージェントは、現在プレビューの段階にあり、機能が制限されています。 この表は更新される予定です 

### <a name="windows-agents"></a>Windows エージェント

| | Azure Monitor エージェント (プレビュー) | 診断<br>拡張機能 (WAD) | Log Analytics<br>エージェント | 依存関係<br>エージェント |
|:---|:---|:---|:---|:---|
| **サポートされている環境** | Azure<br>その他のクラウド (Azure Arc)<br>オンプレミス (Azure Arc)  | Azure | Azure<br>その他のクラウド<br>オンプレミス | Azure<br>その他のクラウド<br>オンプレミス | 
| **エージェントの要件**  | なし | なし | なし | Log Analytics エージェントが必要 |
| **収集されるデータ** | イベント ログ<br>パフォーマンス | イベント ログ<br>ETW イベント<br>パフォーマンス<br>ファイル ベース ログ<br>IIS ログ<br>.NET アプリ ログ<br>クラッシュ ダンプ<br>エージェント診断ログ | イベント ログ<br>パフォーマンス<br>ファイル ベース ログ<br>IIS ログ<br>分析情報とソリューション<br>その他のサービス | プロセスの依存関係<br>ネットワーク接続のメトリック |
| **送信されるデータ** | Azure Monitor ログ<br>Azure Monitor メトリック | Azure Storage<br>Azure Monitor メトリック<br>イベント ハブ | Azure Monitor ログ | Azure Monitor ログ<br>(Log Analytics エージェント経由) |
| **サービスと**<br>**features**<br>**サポート対象** | Log Analytics<br>メトリックス エクスプローラー | メトリックス エクスプローラー | VM insights<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | VM insights<br>サービス マップ |

### <a name="linux-agents"></a>Linux エージェント

| | Azure Monitor エージェント (プレビュー) | 診断<br>拡張機能 (LAD) | Telegraf<br>エージェント | Log Analytics<br>エージェント | 依存関係<br>エージェント |
|:---|:---|:---|:---|:---|:---|
| **サポートされている環境** | Azure<br>その他のクラウド (Azure Arc)<br>オンプレミス (Azure Arc) | Azure | Azure<br>その他のクラウド<br>オンプレミス | Azure<br>その他のクラウド<br>オンプレミス | Azure<br>その他のクラウド<br>オンプレミス |
| **エージェントの要件**  | なし | なし | なし | なし | Log Analytics エージェントが必要 |
| **収集されるデータ** | syslog<br>パフォーマンス | syslog<br>パフォーマンス | パフォーマンス | syslog<br>パフォーマンス| プロセスの依存関係<br>ネットワーク接続のメトリック |
| **送信されるデータ** | Azure Monitor ログ<br>Azure Monitor メトリック | Azure Storage<br>イベント ハブ | Azure Monitor メトリック | Azure Monitor ログ | Azure Monitor ログ<br>(Log Analytics エージェント経由) |
| **サービスと**<br>**features**<br>**サポート対象** | Log Analytics<br>メトリックス エクスプローラー | | メトリックス エクスプローラー | VM insights<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | VM insights<br>サービス マップ |


## <a name="azure-monitor-agent-preview"></a>Azure Monitor エージェント (プレビュー)

[Azure Monitor エージェント](azure-monitor-agent-overview.md)は、現在プレビューの段階にあり、Windows と Linux の両方のマシンで、Log Analytics エージェントおよび Telegraf エージェントを置き換えます。 Azure Monitor ログと Azure Monitor のメトリックの両方にデータを送信し、[データ収集ルール (DCR)](data-collection-rule-overview.md) を使用します。これにより、各エージェントのデータ コレクションと変換先をよりスケーラブルに構成する方法が提供されます。

次のことを行う必要がある場合は、Azure Monitor エージェントを使用します。

- Azure、その他のクラウド、またはオンプレミスの任意のマシンからゲスト ログとメトリックを収集する。 (Azure の外部のマシンには [Azure Arc 対応サーバー](../../azure-arc/servers/overview.md)が必要です。) 
- Azure Monitor での分析のために、Azure Monitor ログと Azure Monitor メトリックにデータを送信する。 
- アーカイブのためにデータを Azure Storage に送信する。
- [Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md) を使用して、データをサードパーティ製のツールに送信する。
- [Azure Security Center](../../security-center/security-center-introduction.md) または [Azure Sentinel](../../sentinel/overview.md) を使用して、ご利用のマシンのセキュリティを管理する。 (プレビューでは使用できません。)

Azure Monitor エージェントの制限事項は次のとおりです。

- 現在、パブリック プレビュー段階にあります。 パブリック プレビュー段階での制限事項の一覧については、[現在の制限事項](azure-monitor-agent-overview.md#current-limitations)に関する記事を参照してください。

## <a name="log-analytics-agent"></a>Log Analytics エージェント

[Log Analytics エージェント](./log-analytics-agent.md)は、Azure、他のクラウド プロバイダー、オンプレミス マシンの仮想マシンのゲスト オペレーティング システムとワークロードから、監視データを収集します。 データは、Log Analytics ワークスペースに送信されます。 Log Analytics エージェントは、System Center Operations Manager によって使用されるものと同じであり、エージェント コンピューターをマルチホーム化して管理グループおよび Azure Monitor との通信を同時に行います。 このエージェントは、Azure Monitor および Azure の他のサービスの特定の分析情報にも必要です。

> [!NOTE]
> Windows 用 Log Analytics エージェントは、多くの場合、Microsoft Monitoring Agent (MMA) と呼ばれます。 Linux 用 Log Analytics エージェントは、多くの場合、OMS エージェントと呼ばれます。

Log Analytics エージェントは、次のような場合に使用します。

* Azure の外部でホストされている Azure 仮想マシンまたはハイブリッド マシンから、ログとパフォーマンス データを収集する。
* データを Log Analytics ワークスペースに送信して、[ログ クエリ](../logs/log-query-overview.md)など、[Azure Monitor ログ](../logs/data-platform-logs.md)でサポートされている機能を活用する。
* マシンを大規模に監視し、そのプロセスや他のリソースおよび外部プロセスに対する依存関係を監視できる、[VM insights](../vm/vminsights-overview.md) を使用する。  
* [Azure Security Center](../../security-center/security-center-introduction.md) または [Azure Sentinel](../../sentinel/overview.md) を使用して、ご利用のマシンのセキュリティを管理する。
* [Azure Automation Update Management](../../automation/update-management/overview.md)、[Azure Automation State Configuration](../../automation/automation-dsc-overview.md)、または [Azure Automation Change Tracking と Inventory](../../automation/change-tracking/overview.md) を使用して、Azure および非 Azure マシンを包括的に管理する。
* さまざまな[ソリューション](../monitor-reference.md#insights-and-core-solutions)を使用して、特定のサービスまたはアプリケーションを監視する。

Log Analytics エージェントの制限事項は次のとおりです。

- Azure Monitor メトリック、Azure Storage、または Azure Event Hubs にデータを送信することはできません。
- 個々のエージェントに対して一意の監視定義を構成することは困難です。
- 各仮想マシンに固有の構成があるため、大規模な管理は困難です。

## <a name="azure-diagnostics-extension"></a>Azure Diagnostics 拡張機能

[Azure Diagnostics 拡張機能](./diagnostics-extension-overview.md)によって、Azure 仮想マシンおよびその他のコンピューティング リソースのゲスト オペレーティング システムとワークロードから監視データが収集されます。 データは主に Azure Storage に収集されますが、データ シンクを定義して、Azure Monitor メトリックや Azure Event Hubs などの他の送信先にデータを送信することもできます。

Azure Diagnostics 拡張機能は、次のような場合に使用します。

- アーカイブのため、または [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) などのツールで分析したりするために Azure Storage にデータを送信する。
- [Azure Monitor メトリック](../essentials/data-platform-metrics.md)にデータを送信して、[メトリックス エクスプローラー](../essentials/metrics-getting-started.md)で分析したり、凖リアルタイムの[メトリック アラート](../alerts/alerts-metric-overview.md)や[自動スケーリング](../autoscale/autoscale-overview.md) (Windows のみ) などの機能を活用したりする。
- [Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md) を使用して、データをサードパーティ製のツールに送信する。
- [ブート診断](../../virtual-machines/troubleshooting/boot-diagnostics.md)を収集して VM ブートの問題を調査する。

Azure Diagnostics 拡張機能の制限事項は次のとおりです。

- Azure リソースでのみ使用できます。
- Azure Monitor ログにデータを送信する機能が制限されています。

## <a name="telegraf-agent"></a>Telegraf エージェント

[InfluxData Telegraf エージェント](../essentials/collect-custom-metrics-linux-telegraf.md)は、Linux コンピューターから Azure Monitor メトリックにパフォーマンス データを収集するために使用されます。

Telegraf エージェントは、次のような場合に使用します。

* [Azure Monitor メトリック](../essentials/data-platform-metrics.md)にデータを送信して、[メトリックス エクスプローラー](../essentials/metrics-getting-started.md)で分析したり、凖リアルタイムの[メトリック アラート](../alerts/alerts-metric-overview.md)や[自動スケーリング](../autoscale/autoscale-overview.md) (Linux のみ) などの機能を活用したりする。

## <a name="dependency-agent"></a>依存関係エージェント

依存関係エージェントは、マシンで実行されているプロセスおよび外部プロセスの依存関係に関する検出データを収集します。 

依存関係エージェントは、次のような場合に使用します。

* マップ機能の [VM insights](../vm/vminsights-overview.md) または [Service Map](../vm/service-map.md) ソリューションを使用する。

依存関係エージェントを使用する場合は、次の点を考慮してください。

- 依存関係エージェントを使用するには、Log Analytics エージェントを同じマシンにインストールする必要があります。
- Linux コンピューターの場合は、Azure 診断の拡張機能の前に Log Analytics エージェントをインストールする必要があります。

## <a name="virtual-machine-extensions"></a>仮想マシン拡張機能

[Windows](../../virtual-machines/extensions/oms-windows.md) および [Linux](../../virtual-machines/extensions/oms-linux.md) 用の Log Analytics 拡張機能は、Azure 仮想マシンに Log Analytics エージェントをインストールします。 [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) および [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) 用の Azure Monitor 依存関係拡張機能は、Azure 仮想マシンに依存関係エージェントをインストールします。 これらは上記と同じエージェントですが、これらは[仮想マシン拡張機能](../../virtual-machines/extensions/overview.md)を使用して管理できます。 可能な場合は、拡張機能を使用してエージェントをインストールおよび管理する必要があります。

ハイブリッド マシンでは、[Azure Arc 対応サーバー](../../azure-arc/servers/manage-vm-extensions.md)を使用して、Log Analytics および Azure Monitor Dependency VM 拡張機能をデプロイします。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

次の表は、Azure Monitor エージェントでサポートされているオペレーティング システムの一覧です。 エージェント固有の考慮事項とインストール プロセスについては、各エージェントのドキュメントをご覧ください。 Telegraf でサポートされているオペレーティング システムについては、Telegraf のドキュメントをご覧ください。 オペレーティング システムはすべて x64 と見なされます。 x86 はどのオペレーティング システムでもサポートされていません。

### <a name="windows"></a>Windows

| オペレーティング システム | Azure Monitor エージェント | Log Analytics エージェント | 依存関係エージェント | 診断拡張機能 | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2019                                      | X | X | X | X |
| Windows Server 2016                                      | X | X | X | X |
| Windows Server 2016 Core                                 |   |   |   | X |
| Windows Server 2012 R2                                   | X | X | X | X |
| Windows Server 2012                                      | X | X | X | X |
| Windows Server 2008 R2 SP1                               | X | X | X | X |
| Windows Server 2008 R2                                   |   | X | X | X |
| Windows 10 Enterprise<br>(マルチセッションを含む) および Pro<br>(サーバー シナリオのみ)  | X | X | X | X |
| Windows 8 Enterprise および Pro<br>(サーバー シナリオのみ)  |   | X | X |   |
| Windows 7 SP1<br>(サーバー シナリオのみ)                 |   | X | X |   |

### <a name="linux"></a>Linux

| オペレーティング システム | Azure Monitor エージェント <sup>1</sup> | Log Analytics エージェント <sup>1</sup> | 依存関係エージェント | 診断拡張機能 <sup>2</sup>| 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017.09                                        |   | X |   |   |
| CentOS Linux 8                                              | X <sup>3</sup> | X | X |   |
| CentOS Linux 7                                              | X | X | X | X |
| CentOS Linux 6                                              |   | X |   |   |
| CentOS Linux 6.5 以降                                           |   | X | X | X |
| Debian 10 <sup>1</sup>                                      | X |   |   |   |
| Debian 9                                                    | X | X | x | X |
| Debian 8                                                    |   | X | X |   |
| Debian 7                                                    |   |   |   | X |
| OpenSUSE 13.1+                                              |   |   |   | X |
| Oracle Linux 8                                              | X <sup>3</sup> | X |   |   |
| Oracle Linux 7                                              | X | X |   | X |
| Oracle Linux 6                                              |   | X |   |   |
| Oracle Linux 6.4+                                           |   | X |   | X |
| Red Hat Enterprise Linux Server 8                           | X <sup>3</sup> | X | X |   |
| Red Hat Enterprise Linux Server 7                           | X | X | X | X |
| Red Hat Enterprise Linux Server 6                           |   | X | X |   |
| Red Hat Enterprise Linux Server 6.7 以降                        |   | X | X | X |
| SUSE Linux Enterprise Server 15.2                           | X <sup>3</sup> |   |   |   |
| SUSE Linux Enterprise Server 15.1                           | X <sup>3</sup> | X |   |   |
| SUSE Linux Enterprise Server 15                             | X | X | X |   |
| SUSE Linux Enterprise Server 12                             | X | X | X | X |
| Ubuntu 20.04 LTS                                            | X | X | X |   |
| Ubuntu 18.04 LTS                                            | X | X | X | X |
| Ubuntu 16.04 LTS                                            | X | X | X | X |
| Ubuntu 14.04 LTS                                            |   | X |   | X |

<sup>1</sup> マシンに Python (2 または 3) がインストールされている必要があります。

<sup>2</sup> マシンに Python 2 がインストールされている必要があります。

<sup>3</sup> Syslog イベントの収集に関する既知の問題。 現在、パフォーマンス データのみがサポートされています。
#### <a name="dependency-agent-linux-kernel-support"></a>依存関係エージェントの Linux カーネル サポート

依存関係エージェントはカーネル レベルで動作するため、サポートもカーネルのバージョンに依存します。 次の表は、Linux OS のメジャーおよびマイナー リリースと依存関係エージェントでサポートされているカーネル バージョンの一覧です。

| Distribution | OS バージョン | カーネル バージョン |
|:---|:---|:---|
|  Red Hat Linux 8   | 8.2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80.\*el8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
|  Red Hat Linux 7   | 7.9     | 3.10.0-1160 |
|                    | 7.8     | 3.10.0-1136 |
|                    | 7.7     | 3.10.0-1062 |
|                    | 7.6     | 3.10.0-957  |
|                    | 7.5     | 3.10.0-862  |
|                    | 7.4     | 3.10.0-693  |
| Red Hat Linux 6    | 6.10    | 2.6.32-754 |
|                    | 6.9     | 2.6.32-696  |
| CentOS Linux 8     | 8.2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80.\*el8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
| CentOS Linux 7     | 7.9     | 3.10.0-1160 |
|                    | 7.8     | 3.10.0-1136 |
|                    | 7.7     | 3.10.0-1062 |
| CentOS Linux 6     | 6.10    | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
|                    | 6.9     | 2.6.32-696.30.1<br>2.6.32-696.18.7 |
| Ubuntu Server      | 20.04   | 5.4\* |
|                    | 18.04   | 5.3.0-1020<br>5.0 (Azure で調整されたカーネルを含む)<br>4.18 *<br>4.15* |
|                    | 16.04.3 | 4.15.\* |
|                    | 16.04   | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |
| SUSE Linux 12 Enterprise Server | 15     | 4.12.14-150\*
|                                 | 12 SP4 | 4.12。* (Azure で調整されたカーネルを含む) |
|                                 | 12 SP3 | 4.4.* |
|                                 | 12 SP2 | 4.4.* |
| Debian                          | 9      | 4.9  | 

## <a name="next-steps"></a>次のステップ

各エージェントの詳細については、以下を参照してください。

- [Log Analytics エージェントの概要](./log-analytics-agent.md)
- [Azure Diagnostics 拡張機能の概要](./diagnostics-extension-overview.md)
- [Linux VM のカスタム メトリックを InfluxData Telegraf エージェントを使用して収集する](../essentials/collect-custom-metrics-linux-telegraf.md)
