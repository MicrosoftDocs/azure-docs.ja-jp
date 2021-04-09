---
title: Azure での Service Map ソリューションの使用 | Microsoft Docs
description: Service Map は、Windows および Linux システム上のアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップする、Azure のソリューションです。 この記事では、サービス マップを環境に展開して、さまざまなシナリオで使用する場合の詳細について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2019
ms.openlocfilehash: ad3f8821189e6e7aabb5653e5f938bb73f57a34b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047009"
---
# <a name="using-service-map-solution-in-azure"></a>Azure での Service Map ソリューションの使用

Service Map は自動的に Windows および Linux のシステム上のアプリケーション コンポーネントを検出し、サービス間の通信をマップします。 Service Map を使用すると、サーバーを重要なサービスを提供する相互接続されたシステムとして表示することができます。 Service Map は、TCP 接続アーキテクチャ全体におけるサーバー、プロセス、受信接続と送信接続の待機時間、ポートの間の接続を表示します。エージェントのインストール以外の構成は必要ありません。

この記事では、オンボードと、Service Map の使い方を詳しく説明します。 ソリューションの前提条件は次のとおりです。

* [サポートされているリージョン](vminsights-configure-workspace.md#supported-regions)内の Log Analytics ワークスペース。

* ソリューションを有効にした同じワークスペースに接続された Windows コンピューターまたは Linux サーバーにインストールされている [Log Analytics エージェント](vminsights-enable-overview.md#agents)。

* Windows コンピューターまたは Linux サーバーにインストールされている [Dependency Agent](vminsights-enable-overview.md#agents)。

>[!NOTE]
>Service Map を既にデプロイ済みの場合は、VM insights でマップを表示することもできます。これには、VM の正常性とパフォーマンスを監視する追加機能が含まれます。 詳細については、[VM insights の概要](../vm/vminsights-overview.md)に関するページをご覧ください。 Service Map ソリューションと VM insights のマップ機能の違いについては、次の [FAQ](../faq.md#vm-insights) をご覧ください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="enable-service-map"></a>Service Map を有効にする

1. Service Map ソリューションを [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceMapOMS?tab=Overview) から有効にします。または、[Solutions Gallery からの監視ソリューションの追加](../insights/solutions.md)に関するページで説明されている手順に従って有効にします。
1. データを取得する各コンピューターの [Windows に Dependency Agent をインストールするか](../vm/vminsights-enable-hybrid.md#install-the-dependency-agent-on-windows)、[Linux に Dependency Agent をインストールします](../vm/vminsights-enable-hybrid.md#install-the-dependency-agent-on-linux)。 Dependency Agent は近隣のコンピューターへの接続を監視することができるため、すべてのコンピューターにエージェントが必要とは限りません。

Log Analytics ワークスペースから Azure Portal 内の Service Map にアクセスして、左側のウィンドウから **[ソリューション]** オプションを選択します。<br><br> ![ワークスペースで [ソリューション] オプションを選択します](./media/service-map/select-solution-from-workspace.png)。<br> ソリューションの一覧から **[ServiceMap(workspaceName)]** を選択し、Service Map ソリューションの概要ページで、Service Map の概要タイルをクリックします。<br><br> ![Service Map の概要タイル](./media/service-map/service-map-summary-tile.png)。

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>使用例:IT プロセスの依存関係を明確にする

### <a name="discovery"></a>探索

サービス マップは、サーバー、プロセス、およびサード パーティのサービス間での依存関係について、共通の参照マップを自動的に作成します。 すべての TCP 依存関係を検出してマップし、予想外の接続、依存しているリモートのサードパーティ システム、Active Directory などの従来型のネットワークの暗領域の依存関係を特定します。 Service Map は、管理システムが試みたネットワーク接続の失敗を検出し、サーバー構成に関する潜在的な問題、サービスの停止、ネットワークの問題を特定する手助けをします。

### <a name="incident-management"></a>インシデント管理

サービス マップでは、システムがどのように接続され互いに影響しているかを示すので、当て推量で問題を特定することが少なくなります。 失敗した接続を特定するだけでなく、ロード バランサー構成の問題、重要なサービスへの予想外または過剰な負荷、許可されていないクライアント (実稼働システムと通信する開発者マシンなど) を特定するのに役立ちます。 また、Change Tracking と統合されたワークフローを使用すれば、バックエンド マシンの変更イベントとサービスのどちらがインシデントの根本原因であるのかがわかります。

### <a name="migration-assurance"></a>確実な移行

Service Map を使用すると、Azure の移行を効果的に計画、迅速化、検証することができるため、必要な作業を網羅して予想外の障害が起きないようにすることができます。 同時に移行する必要のある、相互依存しているシステムをすべて検出し、システム構成と容量を評価し、稼働中のシステムがまだユーザーにサービス提供しているのか、あるいは移行せず使用中止する対象となるのかを特定することができます。 移行完了後は、クライアントの負荷と ID をチェックして、テスト システムと顧客が接続していることを確認できます。 サブネットの計画とファイアウォールの定義に問題がある場合は、サービス マップにある失敗した接続を見れば、接続が必要なシステムがわかります。

### <a name="business-continuity"></a>ビジネス継続性

Azure Site Recovery を使用していて、お使いのアプリケーション環境における復元シーケンスの定義が必要な場合、サービス マップは、どのようにシステムが相互依存しているかを自動的に表示して、信頼性の高い復旧計画を立てる手助けをします。 重要なサーバーまたはグループを選択してそのクライアントを表示することで、そのサーバーが復元され使用可能になった後に復旧するフロントエンド システムを特定できます。 逆に、重要なサーバーのバックエンドの依存関係を調べることで、焦点となるシステムが復元される前に復旧するシステムを特定できます。

### <a name="patch-management"></a>更新プログラムの管理

Service Map は、システム更新評価の使用を強化します。他のどのチームとサーバーがサービスに依存しているかが示されるため、更新プログラム適用のためにシステムを停止する前に事前通知することができます。 また、Service Map は、更新プログラムを適用して再起動した後にそのサービスが使用可能で正常に接続されているかを示すことにより、更新プログラム管理を強化します。

## <a name="mapping-overview"></a>マッピングの概要

Service Map エージェントは、それらがインストールされているサーバー上のすべての TCP 接続プロセスに関する情報に加え、各プロセスの受信および送信接続の詳細も収集します。

左側のウィンドウの一覧から、Service Map エージェントがインストールされているマシンまたはグループを選択し、指定の時間範囲でその依存関係を視覚化できます。 マシンの依存関係マップは、特定のマシンに焦点を当て、そのマシンの直接 TCP クライアントまたはサーバーであるすべてのマシンを表示します。  マシン グループのマップでは、一連のサーバーとその依存関係が示されます。

![サービス マップの概要](media/service-map/service-map-overview.png)

マシンはマップ上で展開表示することができ、選択した時間の範囲内にアクティブなネットワーク接続で実行しているプロセス グループとプロセスを表示することができます。 Service Map エージェントがインストールされているリモート マシンを展開してプロセスの詳細を表示すると、対象のマシンと通信するプロセスのみが表示されます。 対象のマシンに接続するエージェントなしのフロントエンド マシンの数が、その接続先となるプロセスの左側に示されます。 対象のマシンがエージェントのないバックエンド マシンに接続している場合、そのバックエンド サーバーは、同じポート番号への他の接続と共に、サーバー ポート グループに含められます。

既定では、サービス マップは、直近 30 分間の依存関係情報を表示します。 左上にある時間コントロールを使用すると、過去の時間範囲 (最長 1 時間まで) のマップを照会し、過去における依存関係を表示することができます (例: インシデントの発生中や変更が発生する前)。 サービス マップのデータは、有料のワークスペースで 30 日間、無料のワークスペースでは 7 日間のあいだ保存されます。

## <a name="status-badges-and-border-coloring"></a>状態バッジと枠線の色分け

マップ内の各サーバーの下部には、サーバーの状態情報を示す状態バッジの一覧が表示されます。 このバッジは、ソリューション統合のいずれかに、サーバー関連情報がいくつかあることを示します。 バッジをクリックすると、状態に関する詳細情報が右側のウィンドウに直接表示されます。 現在使用できる状態バッジには、アラート、サービス デスク、変更、セキュリティ、更新があります。

状態バッジの重大度に応じて、マシン ノードの枠線を赤 (重大)、黄 (警告)、青 (情報) に色分けすることができます。 この色は、状態バッジの中で最も重大な状態を表します。 灰色の枠線は、ノードに状態インジケーターがないことを示します。

![状態バッジ](media/service-map/status-badges.png)

## <a name="process-groups"></a>プロセス グループ

プロセス グループを使用すると、共通の製品またはサービスに関連付けられているプロセスをプロセス グループに結合できます。  マシン ノードを展開すると、プロセス グループと共にスタンドアロン プロセスが表示されます。  プロセス グループ内のプロセスへの受信接続と送信接続が失敗した場合、その接続はプロセス グループ全体に対して失敗として表示されます。

## <a name="machine-groups"></a>マシン グループ

マシン グループを使用すると、単独のサーバーではなく一連のサーバーを中心としたマップを表示できます。これにより、多層アプリケーションまたはサーバー クラスターのすべてのメンバーを 1 つのマップに表示できます。

ユーザーは、同じグループに属するサーバーを選択し、そのグループに名前を付けます。  その後、グループと共にそのプロセスと接続をすべて表示するか、そのグループの他のメンバーに直接関連するプロセスと接続のみを表示するかを選択できます。

![マシン グループ](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>マシン グループの作成

グループを作成するには、[マシン] の一覧で 1 つまたは複数のマシンを選択し、 **[グループに追加]** をクリックします。

![グループの作成](media/service-map/machine-groups-create.png)

ここで、 **[新規作成]** を選択すると、グループに名前を付けることができます。

![グループの名前付け](media/service-map/machine-groups-name.png)

>[!NOTE]
>マシン グループは、10 台のサーバーに制限されます。

### <a name="viewing-a-group"></a>グループの表示

グループを作成した後、[グループ] タブを選択すると、それらが表示されます。

![[グループ] タブ](media/service-map/machine-groups-tab.png)

次に、グループ名を選択して、そのマシン グループのマップを表示します。
![マシン グループ](media/service-map/machine-group.png) グループに属するマシンは、マップ内で白い枠線に囲まれています。

グループを展開すると、そのマシン グループを構成するマシンが一覧表示されます。

![マシン グループ内のマシン](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>プロセスによるフィルター

マップ ビューは、グループ内のプロセスと接続をすべて表示するか、マシン グループに直接関連するプロセスと接続のみを表示するかを切り替えることができます。  既定のビューでは、すべてのプロセスが表示されます。  ビューを変更するには、マップの上部にあるフィルター アイコンをクリックします。

![グループのフィルター](media/service-map/machine-groups-filter.png)

**[すべてのプロセス]** を選択すると、グループ内の各マシンのプロセスと接続がすべてマップに表示されます。

![マシン グループのすべてのプロセス](media/service-map/machine-groups-all.png)

**グループ関連プロセス** のみが表示されるようビューを変更すると、マップの表示が、グループの他のマシンに直接関連するプロセスと接続のみに絞られ、簡略化されたビューになります。

![マシン グループのフィルター処理されたプロセス](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>グループへのマシンの追加

既存のグループにマシンを追加するには、追加したいマシンの横にあるチェック ボックスをオンにし、 **[グループに追加]** をクリックします。  次に、マシンの追加先となるグループを選択します。
 
### <a name="removing-machines-from-a-group"></a>グループからのマシンの削除

[グループ] の一覧でグループ名を展開し、マシン グループ内のマシンを一覧表示します。  次に、削除したいマシンの横にある省略記号メニューをクリックし、 **[削除]** を選択します。

![グループからのマシンの削除](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>グループの削除または名前の変更

グループの一覧にあるグループ名の横にある省略記号メニューをクリックします。

![マシン グループのメニュー](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>ロール アイコン

特定のプロセスは、マシンで特定のロール (Web サーバー、アプリケーション サーバー、データベースなど) を担います。 サービス マップでは、プロセスとマシン ボックスにロール アイコンで注釈を付け、プロセスまたはサーバーが担っているロールがひとめでわかるようにします。

| ロール アイコン | 説明 |
|:--|:--|
| ![Web サーバー](media/service-map/role-web-server.png) | Web サーバー |
| ![アプリ サーバー](media/service-map/role-application-server.png) | アプリケーション サーバー |
| ![データベース サーバー](media/service-map/role-database.png) | データベース サーバー |
| ![LDAP サーバー](media/service-map/role-ldap.png) | LDAP サーバー |
| ![SMB サーバー](media/service-map/role-smb.png) | SMB サーバー |

![ロール アイコン](media/service-map/role-icons.png)


## <a name="failed-connections"></a>失敗した接続

Service Map マップでは、プロセスとコンピューターの失敗した接続が表示されます。赤色の破線によって、クライアント システムがプロセスまたはポートへのアクセスに失敗したことが示されます。 サービス マップ エージェントがデプロイされているシステムで、接続を試みたが失敗した場合、失敗した接続はそのシステムから報告されます。 Service Map は、接続の確立に失敗した TCP ソケットを監視することで、このプロセスを測定します。 この失敗の原因としては、ファイアウォール、クライアントまたはサーバーの構成の問題、またはリモート サービスが利用できない状態であることが考えられます。

![backup.pl プロセスとポート 4475 の間の接続に失敗したことを示す赤い破線が強調表示されている Service Map の一部のスクリーンショット。](media/service-map/failed-connections.png)

失敗した接続を把握すると、トラブルシューティング、移行の検証、セキュリティ分析、および全体的なアーキテクチャの理解に役立ちます。 失敗した接続は無害な場合もありますが、多くの場合は問題を端的に示しています。たとえば、突然アクセスできなくなるフェールオーバー環境、クラウド移行後に通信できなくなった 2 つのアプリケーション層などです。

## <a name="client-groups"></a>クライアント グループ

クライアント グループは、Dependency Agent がないクライアント マシンを表す、マップ上のボックスです。 1 つのクライアント グループは、個々のプロセスまたはマシンのクライアントを表します。

![クライアント グループ](media/service-map/client-groups.png)

クライアント グループ内のサーバーの IP アドレスを表示するには、グループを選択します。 グループの内容が、 **[Client Group Properties]\(クライアント グループのプロパティ\)** ウィンドウに一覧表示されます。

![クライアント グループのプロパティ](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>サーバー ポート グループ

サーバー ポート グループは、Dependency Agent がないサーバー上のサーバー ポートを表すボックスです。 このボックスには、サーバー ポートと、そのポートに接続しているサーバーの数が表示されます。 ボックスを展開すると、個々 のサーバーと接続が表示されます。 ボックス内にサーバーが 1 つしかない場合は、名前または IP アドレスが表示されます。

![サーバー ポート グループ](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>コンテキスト メニュー

サーバーの右上にある省略記号 (...) をクリックすると、そのサーバーのコンテキスト メニューが表示されます。

![Service Map のサーバーに対して開かれているコンテキスト メニューを示すスクリーンショット。 メニューには、[サーバー マップを読み込む] と [サーバー マップを読み込む] オプションがあります。](media/service-map/context-menu.png)

### <a name="load-server-map"></a>サーバー マップを読み込む

**[サーバー マップを読み込む]** をクリックすると、新しいマップが表示され、選択したサーバーが対象の新しいマシンとなります。

### <a name="show-self-links"></a>自己リンクを表示する

**[自己リンクを表示する]** をクリックすると、自己リンクを含むサーバー ノードが再描画されます。これは、サーバー内のプロセスを開始および終了する TCP 接続です。 自己リンクが表示されると、このメニュー コマンドは **[自己リンクを非表示にする]** に変わります。これを使用して表示をオフにできます。

## <a name="computer-summary"></a>コンピューターの概要

**[Machine Summary]\(マシンの概要\)** ウィンドウには、サーバーのオペレーティング システム、依存関係の数、その他のソリューションから取得したデータの概要が表示されます。 このようなデータには、パフォーマンス メトリック、サービス デスク チケット、変更の追跡、セキュリティ、更新プログラムがあります。

![[Machine Summary]\(マシンの概要\) ウィンドウ](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>コンピューターとプロセスのプロパティ

Service Map マップを見ている間、マシンとプロセスを選択して、それらのプロパティに関する追加情報を取得できます。 コンピューターは、DNS 名、IPv4 アドレス、CPU とメモリ容量、VM の種類、オペレーティング システムとバージョン、最後の再起動時刻、OMS と Service Map エージェントの ID に関する情報を提供します。

![[Machine Properties]\(マシンのプロパティ\) ウィンドウ](media/service-map/machine-properties.png)

実行プロセスに関するオペレーティング システム メタデータからプロセスの詳細を収集できます。この詳細には、プロセス名、プロセスの説明、ユーザー名とドメイン (Windows 上)、会社名、製品名、製品バージョン、作業ディレクトリ、コマンド ライン、プロセス開始時刻が含まれます。

![[Process Properties]\(プロセスのプロパティ\) ウィンドウ](media/service-map/process-properties.png)

**[プロセスの概要]** ウィンドウでは、バインドされているポート、受信と送信の接続、失敗した接続を含む、そのプロセスの接続に関する追加情報が示されます。

![[Process Summary]\(プロセスの概要\) ウィンドウ](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>アラートの統合

Service Map は Azure アラートと統合されて、選択された時間範囲中に、選択されたサーバーで発生したアラートを表示します。 進行中のアラートがある場合はサーバーにアイコンが表示され、 **[Machine Alerts]\(マシンのアラート\)** ウィンドウにアラートが一覧表示されます。

![[Machine Alerts]\(マシンのアラート\) ウィンドウ](media/service-map/machine-alerts.png)

Service Map で関連するアラートを表示できるようにするには、特定のコンピューターに対して動作するアラート ルールを作成します。 適切なアラートを作成するには:
- コンピューター別にグループ化する句を含めます (例: **by Computer interval 1 minute**)。
- メトリックの測定に基づいてアラートを生成することを選択します。

## <a name="log-events-integration"></a>ログ イベントの統合

サービス マップは、ログ検索と統合して、選択したサーバーで選択した時間の範囲中に発生したすべてのログ イベントの数を表示します。 イベント数の一覧の任意の行をクリックしてログ検索に移動し、個別のログ イベントを表示することができます。

![[Machine Log Events]\(マシン ログ イベント\) ウィンドウ](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>サービス デスクとの統合

Service Map と IT Service Management Connector との統合は、両方のソリューションが有効であり、Log Analytics ワークスペースに構成されているときは自動的に行われます。 Service Map での統合に、"サービス デスク" というラベルが付けられます。 詳細については、[IT Service Management Connector を使用した ITSM 作業項目の一元管理](../alerts/itsmc-overview.md)に関するページを参照してください。

**[Machine Service Desk]\(マシン サービス デスク\)** ウィンドウでは、選択した時間範囲内で選択したサーバーの IT Service Management イベントすべてが一覧表示されます。 進行中の項目がある場合はサーバーにアイコンが表示され、[Machine Service Desk]\(マシン サービス デスク\) ウィンドウにはそれらの項目が一覧表示されます。

![[Machine Service Desk]\(マシン サービス デスク\) ウィンドウ](media/service-map/service-desk.png)

接続されている ITSM ソリューション内の項目を開くには、 **[作業項目の表示]** をクリックします。

ログ検索で項目の詳細を表示するには、 **[ログ検索で表示]** をクリックします。
接続メトリックは、Log Analytics 内の 2 つの新しいテーブルに書き込まれます 

## <a name="change-tracking-integration"></a>Change Tracking との統合

Service Map と Change Tracking との統合は、両方のソリューションが有効であり、Log Analytics ワークスペースに構成されているときは自動的に行われます。

**[Machine Change Tracking]\(マシン変更の追跡\)** ウィンドウには、すべての変更が新しい順に一覧表示されており、ログ検索へのリンクで追加の詳細情報を探すことができます。

![Service Map の [Machine Change Tracking]\(マシン変更の追跡\) ウィンドウのスクリーンショット。](media/service-map/change-tracking.png)

次の図は、 **[Show in Log Analytics]\(Log Analytics で表示\)** を選択すると表示される ConfigurationChange イベントの詳細なビューです。

![ConfigurationChange イベント](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>パフォーマンスの統合

**[Machine Performance]\(マシンのパフォーマンス\)** ウィンドウには、選択したサーバーの標準的なパフォーマンス メトリックが表示されます。 このメトリックには、CPU 使用率、メモリ使用率、送受信したネットワークのバイト数、ネットワークで送受信されたバイト数による上位プロセスの一覧が含まれます。

![[Machine Performance]\(マシンのパフォーマンス\) ウィンドウ](media/service-map/machine-performance.png)

パフォーマンス データを表示するには、[適切な Log Analytics パフォーマンス カウンターを有効](../agents/data-sources-performance-counters.md)にする必要があります。  有効にするカウンターは次のとおりです。

Windows:
- Processor(*)\\% Processor Time
- Memory\\% Committed Bytes In Use
- Network Adapter(*)\\Bytes Sent/sec
- Network Adapter(*)\\Bytes Received/sec

Linux:
- Processor(*)\\% Processor Time
- Memory(*)\\% Used Memory
- Network Adapter(*)\\Bytes Sent/sec
- Network Adapter(*)\\Bytes Received/sec

ネットワークのパフォーマンス データを取得するには、ワークスペースで Wire Data 2.0 ソリューションを有効にしておく必要もあります。
 
## <a name="security-integration"></a>セキュリティの統合

Service Map と Security and Audit との統合は、両方のソリューションが有効であり、Log Analytics ワークスペースに構成されているときは自動的に行われます。

**[Machine Security]\(マシンのセキュリティ\)** ウィンドウには、選択したサーバーの Security and Audit ソリューションからのデータが表示されます。 このウィンドウには、選択した時間範囲における、サーバーの未解決のセキュリティ問題の概要が一覧表示されます。 いずれかのセキュリティ問題をクリックすると、それらの詳細がログ検索でドリルダウンされます。

![[Machine Security]\(マシンのセキュリティ\) ウィンドウ](media/service-map/machine-security.png)

## <a name="updates-integration"></a>更新の統合

Service Map と Update Management との統合は、両方のソリューションが有効であり、Log Analytics ワークスペースに構成されているときは自動的に行われます。

**[Machine Updates]\(マシンの更新\)** ウィンドウには、選択したサーバーの Update Management ソリューションからのデータが表示されます。 このウィンドウには、選択した時間範囲における、サーバーに不足している更新プログラムの概要が一覧表示されます。

![Service Map の [[Machine Updates]\(マシンの更新\)] ウィンドウのスクリーンショット。](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics のレコード

Service Map のコンピューターとプロセスのインベントリ データは、Log Analytics で[検索](../logs/log-query-overview.md)できます。 このデータは、移行計画、容量の分析、探索、必要に応じたパフォーマンスのトラブルシューティングといったシナリオに適用できます。

プロセスまたはコンピューターが起動されたとき、あるいは Service Map にオンボードされたときに生成されるレコードに加え、一意のコンピューターとプロセスごとに 1 時間あたり 1 つのレコードが生成されます。 これらのレコードは、次の表に示したプロパティを持ちます。 ServiceMapComputer_CL イベントのフィールドと値は、ServiceMap Azure Resource Manager API のマシン リソースのフィールドにマップされます。 ServiceMapProcess_CL イベントのフィールドと値は、ServiceMap Azure Resource Manager API のプロセス リソースのフィールドにマップされます。 ResourceName_s フィールドは、対応する Resource Manager リソースの名前フィールドと一致します。 

>[!NOTE]
>Service Map 機能の拡張に伴い、これらのフィールドは変更される可能性があります。

個々のプロセスとコンピューターの識別に使用できる、内部生成されたプロパティがあります。

- コンピューター:*ResourceId* または *ResourceName_s* を使用して、Log Analytics ワークスペース内のコンピューターを一意に識別します。
- プロセス:*ResourceId* を使用して、Log Analytics ワークスペース内のプロセスを一意に識別します。 *ResourceName_s* は、プロセスが実行中のマシンのコンテキスト内で一意です (MachineResourceName_s) 

指定の時間範囲にある指定のプロセスとコンピューターについては、複数のレコードが存在できるため、クエリは、同じコンピューターまたはプロセスに対して複数のレコードを返すことがあります。 最新のレコードのみが返されるようにするには、"| dedup ResourceId" をクエリに追加します。

### <a name="connections"></a>接続

接続メトリックは、Log Analytics 内の新しいテーブルに書き込まれます (VMConnection)。 このテーブルは、マシンの接続 (受信および送信) に関する情報を提供します。 接続メトリックは、時間枠の間に特定のメトリックを取得する手段を提供する API と共に公開されています。  リスニング ソケットで受諾することで得られる TCP 接続は受信ですが、所定の IP とポートに接続することで作成される接続は送信です。 接続の方向は Direction プロパティで表され、**受信** または **送信** のいずれかに設定できます。 

これらのテーブル内のレコードは、Dependency エージェントによって報告されるデータから生成されます。 いずれの記録も、1 分の時間間隔での観測を表します。 TimeGenerated プロパティは、時間間隔の開始を示します。 各レコードには、エンティティに関連付けられたメトリックに加えて、接続またはポートなど、それぞれのエンティティを識別する情報が含まれています。 現在のところ、TCP over IPv4 を使用することで発生するネットワーク アクティビティのみが報告されます。

コストと複雑さを管理するため、接続レコードは個々の物理ネットワーク接続を表すものではありません。 複数の物理ネットワーク接続は、論理接続にグループ化され、その後それぞれのテーブルに反映されます。  つまり、*VMConnection* テーブル内のレコードは、論理グルーピングを表しており、観察されている個々の物理接続を表していません。 所定の 1 分間隔で次の属性に同じ値を共有する物理ネットワーク接続は、*VMConnection* 内で 1 つの論理レコードに集約されます。 

| プロパティ | 説明 |
|:--|:--|
| `Direction` |接続の方向であり、値は *受信* または *送信* です |
| `Machine` |コンピューターの FQDN |
| `Process` |プロセスまたはプロセスのグループの ID、接続の開始/受諾 |
| `SourceIp` |送信元の IP アドレス |
| `DestinationIp` |送信先の IP アドレス |
| `DestinationPort` |送信先のポート番号 |
| `Protocol` |接続に使用されるプロトコル。  値は *tcp* です。 |

グループ化の影響を考慮するため、グループ化された物理接続の数に関する情報は、レコードの次のプロパティに提示されます。

| プロパティ | 説明 |
|:--|:--|
| `LinksEstablished` |報告時間枠の間に確立された物理ネットワーク接続の数 |
| `LinksTerminated` |報告時間枠の間に切断された物理ネットワーク接続の数 |
| `LinksFailed` |報告時間枠の間に失敗した物理ネットワーク接続の数。 現在のところ、この情報は送信接続に対してのみ使用できます。 |
| `LinksLive` |報告時間枠の終了時点で開いていた物理ネットワーク接続の数|

#### <a name="metrics"></a>メトリック

接続数メトリックに加えて、所定の論理接続またはネット ワークポートで送受信されるデータ量に関する情報も、レコードの次のプロパティに加えられています。

| プロパティ | 説明 |
|:--|:--|
| `BytesSent` |報告時間枠の間に送信された合計バイト数 |
| `BytesReceived` |報告時間枠の間に受信された合計バイト数 |
| `Responses` |報告時間枠の間に観測された応答の数。 
| `ResponseTimeMax` |報告時間枠の間に観測された最長応答時間 (ミリ秒)。  値がない場合、プロパティは空欄です。|
| `ResponseTimeMin` |報告時間枠の間に観測された最短応答時間 (ミリ秒)。  値がない場合、プロパティは空欄です。|
| `ResponseTimeSum` |報告時間枠の間に観測された全応答時間の合計 (ミリ秒)。  値がない場合、プロパティは空欄です|

報告される第 3 のタイプのデータは応答時間です。これは、発信者が接続を介して送信した要求が処理されて、リモート エンドポイントによって応答されるのを待機する時間です。 報告される応答時間は、内在するアプリケーション プロトコルの実際の応答時間の推定値です。 これは、物理ネットワーク接続の送信元と送信先の間のデータ フローの観察に基づき、経験則を使用して計算されます。 これは、概念上、要求の最後のバイトが送信者を離れる時間と、応答の最後のバイトが送信者に返される時間の差です。 これらの 2 つのタイムスタンプは、所定の物理接続で要求イベントと応答イベントを明確化するために使用されます。 これらの差は、1 つの要求の応答時間を表します。 

この機能の最初のリリースでは、当社のアルゴリズムは推定であり、所定のネットワーク接続に使用される実際のアプリケーション プロトコルに応じて、さまざまな成功の度合いで動作する可能性があります。 たとえば、現在のアプローチは、HTTP(S) のような要求応答ベースのプロトコルでは正しく動作しますが、一方向またはメッセージ キュー ベースのプロトコルでは動作しません。

考慮すべき重要な点は次のとおりです。

1. プロセスが同じ IP アドレスでも複数のネットワーク インターフェイスで接続を受け入れる場合、インターフェイスごとに別のレコードが報告されます。 
2. ワイルドカード IP 付きレコードにはアクティビティはありません。 これらは、マシン上のポートが受信トラフィックにオープンであるという事実を表すために加えられています。
3. 冗長性とデータ量を減らすために、ワイルドカード IP 付きレコードは、特定の IP アドレスと一致するレコード (同じプロセス、ポート、プロトコル) がある場合は省略されます。 ワイルドカード IP レコードが省略される場合、特定の IP アドレス付き IsWildcardBind レコード プロパティは、ポートが報告マシンのあらゆるインターフェイスで公開されていることを示すために、"True" に設定されます。
4. 特定のインターフェイスにのみバインドされているポートでは、IsWildcardBind は "False" に設定されます。

#### <a name="naming-and-classification"></a>命名と分類

便宜上、接続のリモート側の IP アドレスは RemoteIp プロパティに加えられています。 受信接続の場合、RemoteIp は SourceIp と同じですが、送信接続の場合は DestinationIp と同じです。 RemoteDnsCanonicalNames プロパティは、RemoteIp 向けにマシンにより報告される DNS 正規名を表します。 RemoteDnsQuestions プロパティと RemoteClassification プロパティは、将来の使用のために予約されています。 

#### <a name="geolocation"></a>地理的位置情報

*VMConnection* では、レコードの次のプロパティに、各接続レコードのリモート エンドの地理的位置情報も加えられています。 

| プロパティ | 説明 |
|:--|:--|
| `RemoteCountry` |RemoteIp をホストしている国や地域の名前。  例: *United States* |
| `RemoteLatitude` |地理的位置情報の緯度。  例: *47.68* |
| `RemoteLongitude` |地理的位置情報の経度。  例: *-122.12* |

#### <a name="malicious-ip"></a>悪意のある IP

*VMConnection* テーブル内のすべての RemoteIp プロパティは、一連の IP に対して、知られている悪意のあるアクティビティがチェックされます。 RemoteIp が悪意のあると識別される場合、レコードの以下のプロパティに設定されます (IP が悪意のあるとみなされない場合、これらは空です)。

| プロパティ | 説明 |
|:--|:--|
| `MaliciousIp` |RemoteIp アドレス |
| `IndicatorThreadType` |検出される脅威のインジケーターは、*Botnet*、*C2*、*CryptoMining*、*Darknet*、*DDos*、*MaliciousUrl*、*Malware*、*Phishing*、*Proxy*、*PUA*、*Watchlist* のいずれかの値です。   |
| `Description` |観察対象の脅威の説明。 |
| `TLPLevel` |Traffic Light Protocol (TLP) レベルは、定義済みの値、*White*、*Green*、*Amber*、*Red* のいずれかです。 |
| `Confidence` |値は "*0 から 100*" です。 |
| `Severity` |値は "*0 から 5*" です。ここで、*5* は最も重大で、*0* はまったく重大ではありません。 既定値は *3* です。  |
| `FirstReportedDateTime` |プロバイダーが初めてインジケーターをレポートした時間。 |
| `LastReportedDateTime` |Interflow によってインジケーターが最後に表示された時間。 |
| `IsActive` |インジケーターが *True* または *False* の値で非アクティブ化されていることを示します。 |
| `ReportReferenceLink` |特定の観測可能な脅威に関連するレポートにリンクします。 |
| `AdditionalInformation` |該当する場合は、観察対象の脅威についての追加情報を提供します。 |

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL レコード

*ServiceMapComputer_CL* 型のレコードには、サービス マップ エージェントがインストールされているサーバーのインベントリ データが含まれます。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 |
|:--|:--|
| `Type` | *ServiceMapComputer_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | ワークスペース内のマシンに対する一意識別子 |
| `ResourceName_s` | ワークスペース内のマシンに対する一意識別子 |
| `ComputerName_s` | コンピューターの FQDN |
| `Ipv4Addresses_s` | サーバーの IPv4 アドレスの一覧 |
| `Ipv6Addresses_s` | サーバーの IPv6 アドレスの一覧 |
| `DnsNames_s` | DNS 名の配列 |
| `OperatingSystemFamily_s` | Windows または Linux |
| `OperatingSystemFullName_s` | オペレーティング システムのフル ネーム  |
| `Bitness_s` | マシンのビット数 (32 ビットまたは 64 ビット)  |
| `PhysicalMemory_d` | 物理メモリ (MB) |
| `Cpus_d` | CPU の数 |
| `CpuSpeed_d` | CPU 速度 (MHz)|
| `VirtualizationState_s` | "*不明*"、"*物理*"、"*仮想*"、"*ハイパーバイザー*" |
| `VirtualMachineType_s` | *hyperv*、*vmware* など |
| `VirtualMachineNativeMachineId_g` | ハイパーバイザーによって割り当てられた VM ID |
| `VirtualMachineName_s` | VM の名前 |
| `BootTime_t` | ブート時間 |

### <a name="servicemapprocess_cl-type-records"></a>ServiceMapProcess_CL 型のレコード

*ServiceMapProcess_CL* 型のレコードには、サービス マップ エージェントがインストールされているサーバーの TCP 接続プロセスのインベントリ データが含まれています。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | ワークスペース内のプロセスに対する一意識別子 |
| `ResourceName_s` | 実行中のマシン内のプロセスに対する一意識別子|
| `MachineResourceName_s` | マシンのリソース名 |
| `ExecutableName_s` | プロセスの実行可能ファイルの名前 |
| `StartTime_t` | プロセス プールの開始時刻 |
| `FirstPid_d` | プロセス プール内の最初の PID |
| `Description_s` | プロセスの説明 |
| `CompanyName_s` | 会社の名前 |
| `InternalName_s` | 内部名 |
| `ProductName_s` | 製品の名前 |
| `ProductVersion_s` | 製品バージョン |
| `FileVersion_s` | ファイル バージョン |
| `CommandLine_s` | コマンド ライン |
| `ExecutablePath _s` | 実行可能ファイルのパス |
| `WorkingDirectory_s` | 作業ディレクトリ |
| `UserName` | プロセスが実行されているアカウント |
| `UserDomain` | プロセスが実行されているドメイン |

## <a name="sample-log-searches"></a>サンプル ログ検索

### <a name="list-all-known-machines"></a>既知のコンピューターを一覧表示

`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>すべてのマネージド コンピューターの物理メモリ容量を一覧表示。

`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`

### <a name="list-computer-name-dns-ip-and-os"></a>コンピューター名、DNS、IP、および OS を一覧表示。

`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`

### <a name="find-all-processes-with-sql-in-the-command-line"></a>"sql" でコマンドラインのすべてのプロセスを検索

`ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>リソース名でコンピューター (最新のレコード) を検索

`search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>IP アドレスでマシン (最新のレコード) を検索

`search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-known-processes-on-a-specified-machine"></a>指定のマシンにある既知のプロセスすべてを一覧表示

`ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-computers-running-sql"></a>SQL を実行しているすべてのコンピューターを一覧表示

`ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>データセンター内にあるすべての製品バージョンの curl を一覧表示

`ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>CentOS を実行しているすべてのコンピューターのコンピューター グループを作成

`ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>マシンのグループからの送信接続を要約します

```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="rest-api"></a>REST API

サービス マップのすべてのサーバー、プロセス、および依存関係データは、[サービス マップ REST API](/rest/api/servicemap/) を使用して取得できます。

## <a name="diagnostic-and-usage-data"></a>診断と使用状況データ

マイクロソフトは、お客様によるサービス マップ サービスの使用を通して、使用状況とパフォーマンス データを自動的に収集します。 Microsoft はこのデータを使用して、提供する Service Map サービスの品質、セキュリティ、整合性の向上に努めています。 正確で効果的なトラブルシューティングの機能を提供するために、このデータにはソフトウェアの構成に関する情報が含まれています。たとえば、オペレーティング システムとバージョン、IP アドレス、DNS 名、ワークステーション名などです。 名前や住所などの連絡先情報は収集されません。

データの収集と使用の詳細については、「[Microsoft オンライン サービスのプライバシーに関する声明](https://go.microsoft.com/fwlink/?LinkId=512132)」を参照してください。

## <a name="next-steps"></a>次のステップ

Log Analytics の[ログ検索](../logs/log-query-overview.md)の詳細を確認して、Service Map によって収集されたデータを取得します。

## <a name="troubleshooting"></a>トラブルシューティング

Service Map のインストールまたは実行で問題が発生した場合は、こちらのセクションを参照してください。 それでも問題が解決しない場合は、Microsoft サポートにお問い合わせください。

### <a name="dependency-agent-installation-problems"></a>Dependency Agent のインストールに関する問題

#### <a name="installer-prompts-for-a-reboot"></a>インストーラーが再起動を要求する
*通常*、Dependency Agent のインストールまたは削除時に再起動が要求されることはありません。 ただし、まれに、インストールを続行するために Windows Server が再起動を要求することがあります。 これは、依存関係 (通常は Microsoft Visual C++ 再頒布可能ライブラリ) のあるファイルがロックされているため、再起動が要求される場合に生じます。

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--code_number-appears"></a>"Dependency Agent をインストールできません: Visual Studio ランタイム ライブラリが (code = [code_number]) をインストールできませんでした" というメッセージが表示される

Microsoft Dependency Agent は、Microsoft Visual Studio ランタイム ライブラリ上にビルドされます。 ライブラリのインストール中に問題が発生した場合は、メッセージが表示されます。 

ランタイム ライブラリのインストーラーは、%LOCALAPPDATA%\temp フォルダー内にログを作成します。 このファイルは `dd_vcredist_arch_yyyymmddhhmmss.log` で、*arch* は `x86` または `amd64` に、*yyyymmddhhmmss* はログが作成された日時 (24 時間形式) になります。 このログでは、インストールをブロックしている問題の詳細が示されます。

最初に[最新のランタイム ライブラリ](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)をインストールしておくことをお勧めします。

次の表に、コード番号と推奨される解決策を示します。

| コード | 説明 | 解決方法 |
|:--|:--|:--|
| 0x17 | ライブラリのインストーラーは、まだインストールされていない Windows Update を要求しています。 | 最新のライブラリ インストーラー ログを確認してください。<br><br>`Windows8.1-KB2999226-x64.msu` への参照の後に `Error 0x80240017: Failed to execute MSU package,` という行が続いている場合、KB2999226 をインストールするための前提条件が揃っていません。 [Windows での汎用の C ランタイム](https://support.microsoft.com/kb/2999226)に関する記事の前提条件セクションに記載の手順に従ってください。 前提条件をインストールするためには、Windows Update の実行と再起動が複数回必要になることがあります。<br><br>Microsoft Dependency Agent インストーラーをもう一度実行します。 |

### <a name="post-installation-issues"></a>インストール後の問題

#### <a name="server-doesnt-appear-in-service-map"></a>サーバーが Service Map に表示されない

Dependency Agent のインストールに成功しても、Service Map ソリューションにはご利用のマシンが表示されません。
* Dependency Agent は正しくインストールされているのでしょうか? これについては、サービスがインストールされているか、実行中かを確認することで検証できます。<br><br>
**Windows**:**Microsoft Dependency Agent** というサービスを探します。
**Linux**:**microsoft-dependency-agent** という実行中のプロセスを探します。

* [Log Analytics Free レベル](https://azure.microsoft.com/pricing/details/monitor/)ですか? Free プランでは、一意の Service Map マシンを最大 5 台まで使用できます。 それ以降のマシンは、前の 5 台でデータを送信していない場合でも、サービス マップに表示されません。

* ご利用のサーバーからログとパフォーマンス データが Azure Monitor ログに送信されていますか? Azure Monitor\Logs に移動し、お使いのコンピューターに対して次のクエリを実行します。 

    ```kusto
    Usage | where Computer == "admdemo-appsvr" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

結果としてさまざまなイベントを取得しましたか? そのデータは最近のものですか? そうである場合は、Log Analytics エージェントは正しく動作しており、ワークスペースと通信しています。 そうでない場合は、ご利用のマシン上でエージェントを確認してください。([Windows 用 Log Analytics エージェントのトラブルシューティング](../agents/agent-windows-troubleshoot.md) または [Linux 用 Log Analytics エージェントのトラブルシューティング](../agents/agent-linux-troubleshoot.md)に関するページを参照)。

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Service Map にサーバーは表示されるがプロセスが表示されない

Service Map にマシンは表示されるがプロセスまたは接続データは表示されないという場合は、Dependency Agent がインストールされて実行されているがカーネル ドライバーが読み込まれなかったということを意味しています。 

`C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file` (Windows) または `/var/opt/microsoft/dependency-agent/log/service.log file` (Linux) を確認します。 ファイルの最後の行に、カーネルがロードされなかった原因が示されています。 たとえば、カーネルを更新した場合にそのカーネルが Linux でサポートされないことがあります。

## <a name="suggestions"></a>検索候補

サービス マップやこのドキュメントについてフィードバックはありますか。  [UserVoice ページ](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)では、機能を提案したり、既存の提案に投票することができます。