---
title: 旧バージョンの Azure Migrate を使用する | Microsoft Docs
description: 旧バージョンの Azure Migrate の使用方法に関する概要を示します
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7cf87c3a146f51666a2c24c7cd0d6e9425159225
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228406"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>旧バージョンの Azure Migrate を使用する

この記事では、旧バージョンの Azure Migrate の使用方法に関する情報を提供します。


Azure Migrate サービスには次の 2 つのバージョンがあります。

- **最新バージョン**:Azure Migrate プロジェクトの作成、オンプレミス マシンの検出、評価と移行の調整を行う場合はこちらのバージョンを使用します。 このバージョンの新機能については[こちらをご覧ください](whats-new.md)。
- **旧バージョン**:旧バージョンの Azure Migrate (オンプレミスの VMware VM の評価のみサポート) を使用している場合、今後は最新バージョンを使用してください。 旧バージョンで作成した Azure Migrate プロジェクトを今後も使用する必要がある場合にできることとできないことは、以下のとおりです。
    - 移行プロジェクトは作成できなくなります。
    - 新しく検出を行わないことをお勧めします。
    - 既存のプロジェクトには引き続きアクセスできます。
    - 評価は引き続き実行できます。
    

## <a name="upgrade-between-versions"></a>バージョン間でアップグレードする

旧バージョンのプロジェクトやコンポーネントを新バージョンにアップグレードすることはできません。 [新しい Azure Migrate プロジェクトを作成](how-to-add-tool-first-time.md)して、それに評価と移行ツールを追加する必要があります。

## <a name="find-projects-from-previous-version"></a>旧バージョンのプロジェクトを探す

以下に従って、旧バージョンのプロジェクトを探します。

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索して選択します。 
2. Azure Migrate ダッシュボードに、通知と、過去の Azure Migrate プロジェクトにアクセスするためのリンクが表示されます。
3. リンクをクリックして、v1 のプロジェクトを開きます。


## <a name="create-an-assessment"></a>評価を作成する

ポータルで VM が検出された後、それらをグループ化して、評価を作成します。

- オンプレミス評価は、VM がポータルで検出された直後に作成できます。
- パフォーマンスベースの評価については、信頼できるサイズの推奨を得るために、パフォーマンスベースの評価を作成する前に少なくとも 1 日待つことをお勧めします。

評価の作成手順は以下のとおりです。

1. プロジェクトの **[概要]** ページで、 **[+ 評価の作成]** をクリックします。
2. **[すべて表示]** をクリックして、評価のプロパティを確認します。
3. グループを作成し、グループ名を指定します。
4. グループに追加するマシンを選びます。
5. **[評価の作成]** をクリックして、グループと評価を作成します。
6. 評価が作成された後、 **[概要]**  >  **[ダッシュボード]** でそれを表示します。
7. **[評価のエクスポート]** をクリックし、Excel ファイルとしてダウンロードします。

既存の評価を最新のパフォーマンス データで更新する場合は、その評価に対して **[Recalculate]\(再計算)** コマンドを使用して、評価を更新することができます。

## <a name="review-an-assessment"></a>評価を確認する 

評価には、3 つの段階があります。

- 評価ではまず、マシンに Azure での互換性があるかどうかを調べる適合性分析が行われます。
- サイズの見積もり。
- 月間コストの見積もり。

前の段階で合格したマシンだけが次の段階に進みます。 たとえば、マシンが適合性チェックで不合格になった場合、Azure に不適合とマークされ、サイズとコストの見積もりは行われません。


### <a name="review-azure-readiness"></a>Azure 対応性を確認する

評価の Azure 対応性ビューには、各 VM の対応状態が表示されます。

**対応性** | **State** | **詳細**
--- | --- | ---
Azure に対応 | 互換性の問題はありません。 マシンは現状のまま Azure に移行でき、完全な Azure サポートがある状態で起動します。 | 準備ができている VM の場合、Azure Migrate は Azure での VM のサイズを推奨します。
Azure に条件付きで対応 | マシンは Azure で起動する可能性はありますが、Azure サポートの一部を受けられないことがあります。 たとえば、Azure でサポートされない過去のバージョンの Windows Server OS を搭載したマシンなどが該当します。 | Azure Migrate に対応性の問題の説明と修復の手順が表示されます。
Azure に未対応 |  この VM は Azure では起動しません。 たとえば、VM に 4 TB を超えるディスクが搭載されている場合、Azure でホストすることはできません。 | Azure Migrate に対応性の問題の説明と修復の手順が表示されます。
対応不明 | Azure Migrate で Azure 対応性を識別できませんでした。よくある原因は、データを利用できないことです。


#### <a name="azure-vm-properties"></a>Azure VM プロパティ
対応性では、多くの VM プロパティを考慮して、Azure で VM を実行可能かどうかを特定します。


**プロパティ** | **詳細** | **対応性**
--- | --- | ---
**ブートの種類** | BIOS がサポートされています。 UEFI はサポートされていません。 | ブートの種類が UEFI の場合は条件付きで対応します。
**コア** | マシンのコア数が、Azure VM でサポートされる最大コア数 (128) 以下。<br/><br/> パフォーマンス履歴が使用可能な場合、Azure Migrate では、使用されているコアの数が考慮されます。<br/>評価 <br/>設定で快適性係数が指定されている場合、使用されているコアの数に快適性係数が乗算されます。<br/><br/> パフォーマンス履歴がない場合は、快適性係数を適用せずに、割り当てられているコアの数が使用されます。 | 制限以下の場合は対応します。
**メモリ** | マシンのメモリ サイズが、Azure VM の最大メモリ (Azure M シリーズ Standard_M128m&nbsp;<sup>2</sup> で 3,892 GB) 以下。 [詳細情報](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。<br/><br/> パフォーマンス履歴が使用可能な場合、Azure Migrate では、使用されているメモリの数が考慮されます。<br/><br/>快適性係数が指定されている場合、使用されているメモリに快適性係数が乗算されます。<br/><br/> 履歴がない場合は、快適性係数は適用されず、割り当てられているメモリが使用されます。<br/><br/> | 制限内であれば対応します。
**ストレージ ディスク** | ディスクの割り当てサイズは、4 TB (4,096 GB) 以下である必要があります。<br/><br/> マシンに接続されているディスクの数は、OS ディスクを含めて 65 個以下である必要があります。 | 制限内であれば対応します。
**ネットワーク** | マシンに接続されている NIC の数は、32 個以下である必要があります。 | 制限内であれば対応します。

#### <a name="guest-operating-system"></a>ゲスト オペレーティング システム

Azure Migrate では、VM プロパティと合わせてオンプレミス VM のゲスト OS も調べて、VM を Azure で実行できるかどうかを確認します。

- Azure Migrate では、vCenter Server に指定されている OS が考慮されます。
- Azure Migrate による検出はアプライアンスに基づいているため、VM 内で実行している OS が vCenter Server に指定された OS と同じかどうかを確かめることができません。

以下のロジックが使用されます。

**オペレーティング システム** | **詳細** | **対応性**
--- | --- | ---
Windows Server 2016 および全 SP | Azure は完全サポートを提供します。 | Azure に対応
Windows Server 2012 R2 および全 SP | Azure は完全サポートを提供します。 | Azure に対応
Windows Server 2012 および全 SP | Azure は完全サポートを提供します。 | Azure に対応
Windows Server 2008 R2 および全 SP | Azure は完全サポートを提供します。| Azure に対応
Windows Server 2008 (32 ビットおよび 64 ビット) | Azure は完全サポートを提供します。 | Azure に対応
Windows Server 2003、2003 R2 | サポート対象外。Azure でサポートを受けるには[カスタム サポート契約 (CSA)](https://aka.ms/WSosstatement) が必要になります。 | Azure 条件付きで対応。Azure に移行する前に OS のアップグレードを検討してください。
Windows 2000、98、95、NT、3.1、MS-DOS | サポート対象外。 マシンが Azure で起動する可能性はありますが、Azure による OS サポートは提供されません。 | Azure に条件付きで対応。Azure に移行する前に OS のアップグレードをお勧めします。
Windows Client 7、8、10 | Azure は、[Visual Studio サブスクリプション](https://docs.microsoft.com/azure/virtual-machines/windows/client-images)にのみサポートを提供します。 | Azure に条件付きで対応
Windows 10 Pro デスクトップ | Azure は、[マルチ テナント ホスティング権限](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)にサポートを提供します。 | Azure に条件付きで対応
Windows Vista、XP Professional | サポート対象外。 マシンが Azure で起動する可能性はありますが、Azure による OS サポートは提供されません。 | Azure に条件付きで対応。Azure に移行する前に OS のアップグレードをお勧めします。
Linux | Azure はこれらの [Linux オペレーティング システム](../virtual-machines/linux/endorsed-distros.md)をサポートしています。 他の Linux オペレーティング システムも Azure で起動する可能性はありますが、Azure に移行する前に、サポート対象バージョンへの OS のアップグレードをお勧めします。 | Azure に対応 (バージョンがサポートされている場合)。<br/><br/>条件付きで対応 (バージョンがサポートされていない場合)。
他のオペレーティング システム<br/><br/> 例: Oracle Solaris、Apple Mac OS、FreeBSD など | Azure では、これらのオペレーティング システムはサポートされません。 マシンは Azure で起動できますが、Azure による OS サポートは提供されません。 | Azure に条件付きで対応。Azure に移行する前にサポート対象の OS のインストールをお勧めします。  
vCenter Server で **[Other]\(その他\)** と指定された OS | この場合、Azure Migrate は OS を識別できません。 | 対応不明。 VM で実行している OS が Azure でサポートされることを確認します。
32 ビット オペレーティング システム | マシンは Azure で起動できますが、Azure がフル サポートを提供しない可能性があります。 | 条件付きで Azure に対応。Azure に移行する前に、マシンの OS を 32 ビット OS から 64 ビット OS にアップグレードすることをご検討ください。


### <a name="review-sizing"></a>サイズ見積もりを確認する

 Azure Migrate での推奨サイズは、評価プロパティで指定されたサイズの見積もり基準に依存します。

- サイズ見積もりがパフォーマンスベースである場合、推奨サイズでは、VM (CPU とメモリ) およびディスク (IOPS とスループット) のパフォーマンス履歴が考慮されます。
- サイズの見積もり基準が "オンプレミス" の場合、Azure での推奨サイズは、オンプレミスの VM のサイズに基づいたものとなります。 ディスクのサイズ見積もりは、評価プロパティで指定されたストレージの種類に基づいて行われます (既定値は Premium ディスクです)。 Azure Migrate では、VM とディスクのパフォーマンス データは考慮されません。

### <a name="review-cost-estimates"></a>コスト見積もりを確認する

コスト見積もりでは、Azure で VM を実行する合計コンピューティング コストとストレージ コストのほか、各マシンの詳細が示されます。

- コストの見積もりは、VM マシンとそのディスクの推奨サイズのほか、評価プロパティを使って計算されます。
- コンピューティングとストレージの見積もり月額コストが、グループ内の全 VM について集計されます。
- コスト見積もりは、オンプレミスの VM をサービスとしての Azure インフラストラクチャ (IaaS) VM として実行した場合のものです。 Azure Migrate では、サービスとしてのプラットホーム (PaaS) のコストも、サービスとしてのソフトウェア (SaaS) のコストも考慮されません。

### <a name="review-confidence-rating-performance-based-assessment"></a>信頼度レーティングを確認する (パフォーマンスベースの評価)

パフォーマンスベースの評価にはそれぞれ、信頼度レーティングが付けられています。

- 信頼度レーティングは 1 つ星から 5 つ星までです (1 つ星が最低、5 つ星が最高です)。
- 信頼度レーティングは、評価の計算に必要なデータ ポイントの可用性に基づいて、評価に割り当てられます。
- 評価の信頼度レーティングは、Azure Migrate による推奨サイズの信頼性を判断する目安となります。
- 信頼度レーティングは、"そのままの" オンプレミス評価では使用できません。

パフォーマンスベースのサイズ見積もりを行うには、Azure Migrate に次の情報が必要です。
- CPU の使用状況データ。
- VM のメモリ データ。
- VM に接続されている各ディスクについて、ディスクの IOPS とスループットに関するデータが必要です。
- VM に接続されている各ネットワーク アダプターについて、ネットワークの入出力が必要です。
- 上記のいずれも利用できない場合、推奨サイズの信頼性が (したがって信頼度レーティングの信頼性も) 損なわれることがあります。


表に、利用可能なデータ ポイントの割合別に信頼度レーティングをまとめます。

**データ ポイントの可用性** | **信頼度レーティング**
--- | ---
0% - 20% | 1 つ星
21% - 40% | 2 つ星
41% - 60% | 3 つ星
61% - 80% | 4 つ星
81% - 100% | 5 つ星


#### <a name="assessment-issues-affecting-confidence-ratings"></a>信頼度レーティングに影響する評価の問題

さまざまな理由により、評価で一部のデータ ポイントを使用できない場合があります。

- 評価期間中に使用環境のプロファイリングが行われていない。 たとえば、パフォーマンス期間を 1 日に設定した評価を作成した場合は、検出の開始後少なくとも 1 日、またはすべてのデータ ポイントが収集されるまで待つ必要があります。
- 評価の計算対象期間中にシャットダウンされた VM がある。 対象期間の一部で VM の電源がオフになっていた場合、Azure Migrate ではその期間におけるパフォーマンス データは収集できません。
- 評価の計算対象期間中に作成された VM がある。 たとえば、前月のパフォーマンス履歴を使用して評価を作成するときに、1 週間前にその環境で多数の VM を作成している場合、新しい VM のパフォーマンス履歴は対象期間全体のものにはなりません。

> [!NOTE]
> 評価の信頼度レーティングが 5 つ星を下回る場合、環境のプロファイリングが終わるまで少なくとも 1 日待ってから、評価を再計算します。 このようにしない場合、パフォーマンスベースのサイズ見積もりの信頼性が損なわれる可能性があります。 再計算をしない場合には、評価プロパティを変更して、オンプレミスどおりのサイズ見積もりに切り替えることをお勧めします。



## <a name="create-groups-using-dependency-visualization"></a>依存関係の視覚化を使用してグループを作成する

グループは手動で作成するだけでなく、依存関係の視覚化を使用して作成することもできます。
- 評価実行前に、マシンの依存関係を照合してより信頼度の高いグループを見極める場合、通常はこの方法を使用します。
- 依存関係の視覚化により、Azure への移行を効率よく計画できます。 こうすることで、Azure に移行する際に、後に何も残されず、突然障害が発生することもなくなります。
- 同時に移行する必要のある、相互依存しているシステムをすべて検出し、稼働中のシステムがまだユーザーにサービス提供しているのか、あるいは移行せず使用中止する対象となるのかを特定することができます。
- Azure Migrate では、依存関係の視覚化を実現するために Azure Monitor の Service Map ソリューションを使用します。

> [!NOTE]
> 依存関係の視覚化は、Azure Government では使用できません。

依存関係の視覚化を設定するには、Log Analytics ワークスペースを Azure Migrate プロジェクトと関連付け、依存関係を視覚化するマシンにエージェントをインストールしてから、依存関係の情報を使用してグループを作成します。 



### <a name="associate-a-log-analytics-workspace"></a>Log Analytics ワークスペースを関連付ける

依存関係の視覚化を利用するには、Log Analytics ワークスペースを移行プロジェクトと関連付けます。 Migrate プロジェクトが作成されている同じサブスクリプションのみにワークステーションを作成する、またはアタッチすることができます。

1. プロジェクトに Log Analytics ワークスペースをアタッチするには、 **[概要]** の **[基本]** に移動し、 **[構成が必要]** をクリックします。
2. 新しいワークスペースを作成することも、既存のワークスペースをアタッチすることもできます。
  - 新しいワークスペースを作成する場合は、名前を指定します。 移行プロジェクトと同じ [Azure 地理的環境](https://azure.microsoft.com/global-infrastructure/geographies/)内にあるリージョンにワークスペースが作成されます。
  - 既存のワークスペースをアタッチするときは、移行プロジェクトと同じサブスクリプション内の使用可能なすべてのワークスペースから選択できます。 表示されるワークスペースは、[サポートされている Service Map リージョン](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions)内で作成されたもののみです。 ワークスペースをアタッチするには、ワークスペースへの "閲覧者" アクセスがあることを確認してください。

> [!NOTE]
> 移行プロジェクトに関連付けられているワークスペースは変更できません。

### <a name="download-and-install-vm-agents"></a>VM エージェントをダウンロードしてインストールする

ワークスペースの構成後、評価対象の各オンプレミス マシンにエージェントをダウンロードし、インストールします。 また、インターネットに接続されていないマシンの場合、[Log Analytics ゲートウェイ](../azure-monitor/platform/gateway.md)をダウンロードしてインストールする必要があります。

1. **[概要]** で **[管理]**  >  **[マシン]** をクリックし、必要なマシンを選択します。
2. **[依存関係]** 列で、 **[エージェントのインストール]** をクリックします。
3. **[依存関係]** ページで、評価する各 VM に Microsoft Monitoring Agent (MMA) と依存関係エージェントをダウンロードし、インストールします。
4. ワークスペース ID とキーをコピーします。 これらは、MMA をオンプレミスのマシンにインストールするときに必要になります。

> [!NOTE]
> エージェントのインストールを自動化するには、System Center Configuration Manager などのデプロイ ツールを使用するか、Azure Migrate 用エージェント デプロイ ソリューションを備えたパートナー ツール ([Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) など) を使用します。


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Windows マシンに MMA エージェントをインストールする

Windows マシンにエージェントをインストールするには、次の手順に従います。

1. ダウンロードしたエージェントをダブルクリックします。
2. **[ようこそ]** ページで **[次へ]** をクリックします。 **[ライセンス条項]** ページで、 **[同意する]** をクリックしてライセンスに同意します。
3. **[インストール先のフォルダー]** で、既定のインストール フォルダーをそのまま使用するか変更し、 **[次へ]** をクリックします。
4. **[エージェントのセットアップ オプション]** で、 **[Azure Log Analytics]**  >  **[次へ]** の順にクリックします。
5. **[追加]** をクリックして、新しい Log Analytics ワークスペースを追加します。 ポータルからコピーしたワークスペース ID とキーを貼り付けます。 **[次へ]** をクリックします。

エージェントは、コマンド ラインからインストールするか、System Center Configuration Manager などの自動化された方法を使用してインストールすることができます。 このような方法を使用して MMA エージェントをインストールする方法については、[詳細](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)のページを参照してください。

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Linux マシンに MMA エージェントをインストールする

Linux マシンにエージェントをインストールするには、次の手順に従います。

1. 該当するバンドル (x86 または x64) を、scp/sftp を使用して Linux コンピューターに転送します。
2. --install 引数を使用してバンドルをインストールします。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

MMA でサポートされる Linux オペレーティング システムの一覧は、[ここ](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)をご覧ください。

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Operations Manager による監視対象マシンに MMA エージェントをインストールする

System Center Operations Manager 2012 R2 以降によって監視されているマシンの場合、MMA エージェントをインストールする必要はありません。 必要な依存関係データを収集するために、Service Map が Operations Manager の MMA と統合されています。 [詳細情報](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)。 依存関係エージェントはインストールする必要があります。


### <a name="install-the-dependency-agent"></a>依存関係エージェントをインストールする

1. Windows マシンに依存関係エージェントをインストールするには、セットアップ ファイルをダブルクリックし、ウィザードに従います。
2. Linux マシンに依存関係エージェントをインストールするには、次のコマンドを使用してルートとしてインストールします。

    ```sh InstallDependencyAgent-Linux64.bin```

- [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) および [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) オペレーティング システムの依存関係エージェントのサポートに関する詳細を確認してください。
- スクリプトを使用して依存関係エージェントをインストールする方法については、[こちら](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)をご覧ください。


### <a name="create-a-group-with-dependency-mapping"></a>依存関係マッピングを使用してグループを作成する

1. エージェントをインストールしたら、ポータルに移動し、 **[管理]**  >  **[マシン]** をクリックします。
2. エージェントをインストールしたマシンを検索します。
3. マシンの **[依存関係]** 列に **[依存関係の表示]** と表示されます。 この列をクリックすると、マシンの依存関係が表示されます。
4. マシンの依存関係マップには次の詳細情報が表示されます。
    - マシンに対する受信 (クライアント) と送信 (サーバー) の TCP 接続
        - MMA または依存関係エージェントがインストールされていない依存マシンは、ポート番号によってグループ化されます。
        - MMA と依存関係エージェントがインストールされている依存マシンは、別のボックスに表示されます。
    - マシンで実行しているプロセス。各マシンのボックスを展開してプロセスを表示できます。
    - FQDN、オペレーティング システム、MAC アドレスなどのマシンのプロパティが表示されます。 各マシンのボックスをクリックすると、詳細を確認できます。

4. 時間の範囲のラベルで期間をクリックすると、さまざまな期間の依存関係を確認できます。 既定では、範囲は 1 時間です。 時間の範囲を変更することも、開始日と終了日および期間を指定することもできます。

   > [!NOTE]
   >    最大で 1 時間の時間の範囲がサポートされています。 長期間にわたって[依存関係データを照会する](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)には、Azure Monitor ログを使用してください。

5. グループ化する依存関係のあるマシンを特定したら、マップ上で Ctrl キーを押しながらクリックして複数のマシンを選択し、 **[グループ マシン]** をクリックします。
6. グループ名を指定します。 依存するマシンが Azure Migrate によって検出されていることを確認します。

    > [!NOTE]
    > 依存するマシンが Azure Migrate によって検出されない場合は、マシンをグループに追加できません。 そのようなマシンをグループに追加するには、vCenter Server で正しい範囲に対して検出プロセスを再び実行して、マシンが Azure Migrate によって検出されることを確認する必要があります。  

7. このグループの評価を作成する場合は、グループの新しい評価を作成するためのチェックボックスをオンにします。
8. **[OK]** をクリックしてグループを保存します。

グループが作成されたら、グループのすべてのマシンにエージェントをインストールし、グループ全体の依存関係を視覚化してグループを絞り込むことをお勧めします。

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Azure Monitor ログから依存関係データにクエリを実行する

Service Map によってキャプチャされた依存関係データは、Azure Migrate プロジェクトに関連付けられている Log Analytics ワークスペースでのクエリに使用できます。 Azure Monitor ログ内でクエリを実行する Service Map データ テーブルの[詳細を参照してください](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)。 

Kusto クエリを実行するには:

1. エージェントをインストールしたら、ポータルに移動し、 **[概要]** をクリックします。
2. **[概要]** で、プロジェクトの **[基本]** セクションに移動し、 **[OMS ワークスペース]** の横にあるワークスペース名をクリックします。
3. Log Analytics ワークスペース ページで **[全般]**  > 、 **[ログ]** の順にクリックします。
4. Azure Monitor ログを使用して依存関係データを収集するためのクエリを作成します。 次のセクションでサンプル クエリを見つけます。
5. [実行] をクリックしてクエリを実行します。 

Kusto クエリの作成方法の[詳細を参照してください](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。 

### <a name="sample-azure-monitor-logs-queries"></a>Azure Monitor ログのサンプル クエリ

依存関係データを抽出するために使用できるサンプル クエリは次のとおりです。 クエリを変更して、目的のデータ ポイントを抽出できます。 依存関係データ レコード内のフィールドの完全なリストは、[こちら](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)で入手できます。 その他のサンプル クエリについては、[こちら](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)を参照してください。

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>一連のマシンの受信接続を集計する

接続メトリック用のテーブル (VMConnection) 内にあるレコードは、個々の物理ネットワーク接続を表すものではありません。 複数の物理ネットワーク接続は、論理接続にグループ化されます。 物理ネットワーク接続データがどのように VMConnection 内の単一の論理レコードに集約されるかについては、[詳細を確認](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections)してください。 

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
最新バージョンの Azure Migrate の[詳細を確認してください](migrate-services-overview.md)。
