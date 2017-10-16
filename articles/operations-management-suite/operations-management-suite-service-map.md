---
title: "Operations Management Suite の Service Map ソリューションの使用 | Microsoft Docs"
description: "サービス マップは、Windows および Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップする Operations Management Suite のソリューションです。 この記事では、サービス マップを環境に展開して、さまざまなシナリオで使用する場合の詳細について説明します。"
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/22/2016
ms.author: daseidma;bwren;dairwin
ms.openlocfilehash: 2e5475a0563549ddfaa2c146e4acf94c019841ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-service-map-solution-in-operations-management-suite"></a>Operations Management Suite の Service Map ソリューションの使用
サービス マップは、Windows および Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 Service Map を使用すると、サーバーを重要なサービスを提供する相互接続されたシステムとして表示することができます。 Service Map は、TCP 接続アーキテクチャ全体におけるサーバー、プロセス、ポートの間の接続を表示します。エージェントのインストール以外の構成は必要ありません。

この記事では、サービス マップの使い方を詳しく説明します。 Service Map の構成とエージェントのオンボードについては、[Operations Management Suite の Service Map ソリューションの構成](operations-management-suite-service-map-configure.md)に関するページを参照してください。


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>使用例: IT プロセスの依存関係を明確にする

### <a name="discovery"></a>探索
サービス マップは、サーバー、プロセス、およびサード パーティのサービス間での依存関係について、共通の参照マップを自動的に作成します。 すべての TCP 依存関係を検出してマップし、予想外の接続、依存しているリモートのサードパーティ システム、Active Directory などの従来型のネットワークの暗領域の依存関係を特定します。 Service Map は、管理システムが試みたネットワーク接続の失敗を検出し、サーバー構成に関する潜在的な問題、サービスの停止、ネットワークの問題を特定する手助けをします。

### <a name="incident-management"></a>インシデント管理
サービス マップでは、システムがどのように接続され互いに影響しているかを示すので、当て推量で問題を特定することが少なくなります。 失敗した接続を特定するだけでなく、ロード バランサー構成の問題、重要なサービスへの予想外または過剰な負荷、許可されていないクライアント (実稼働システムと通信する開発者マシンなど) を特定するのに役立ちます。 また、Operations Management Suite の Change Tracking と統合されたワークフローを使用すれば、バックエンド マシンの変更イベントとサービスのどちらがインシデントの根本原因であるのかがわかります。

### <a name="migration-assurance"></a>確実な移行
Service Map を使用すると、Azure の移行を効果的に計画、迅速化、検証することができるため、必要な作業を網羅して予想外の障害が起きないようにすることができます。 同時に移行する必要のある、相互依存しているシステムをすべて検出し、システム構成と容量を評価し、稼働中のシステムがまだユーザーにサービス提供しているのか、あるいは移行せず使用中止する対象となるのかを特定することができます。 移行完了後は、クライアントの負荷と ID をチェックして、テスト システムと顧客が接続していることを確認できます。 サブネットの計画とファイアウォールの定義に問題がある場合は、サービス マップにある失敗した接続を見れば、接続が必要なシステムがわかります。

### <a name="business-continuity"></a>ビジネス継続性
Azure Site Recovery を使用していて、お使いのアプリケーション環境における復元シーケンスの定義が必要な場合、サービス マップは、どのようにシステムが相互依存しているかを自動的に表示して、信頼性の高い復旧計画を立てる手助けをします。 重要なサーバーまたはグループを選択してそのクライアントを表示することで、そのサーバーが復元され使用可能になった後に復旧するフロントエンド システムを特定できます。 逆に、重要なサーバーのバックエンドの依存関係を調べることで、焦点となるシステムが復元される前に復旧するシステムを特定できます。

### <a name="patch-management"></a>更新プログラムの管理
Service Map は、Operations Management Suite のシステム更新評価の使用を強化します。他のどのチームとサーバーがサービスに依存しているかが示されるため、更新プログラム適用のためにシステムを停止する前に事前通知することができます。 また、Service Map は、更新プログラムを適用して再起動した後にそのサービスが使用可能で正常に接続されているかどうかを示すことによって、Operations Management Suite における更新プログラム管理を強化します。


## <a name="mapping-overview"></a>マッピングの概要
サービス マップ エージェントは、それらがインストールされているサーバー上のすべての TCP 接続プロセスに関する情報に加え、各プロセスの受信および送信接続の詳細も収集します。 左側のウィンドウの一覧では、Service Map エージェントがインストールされているマシンまたはグループを選択し、指定の時間範囲でその依存関係を視覚化できます。 マシンの依存関係マップは、特定のマシンに焦点を当て、そのマシンの直接 TCP クライアントまたはサーバーであるすべてのマシンを表示します。  マシン グループのマップでは、一連のサーバーとその依存関係が示されます。

![サービス マップの概要](media/oms-service-map/service-map-overview.png)

マシンはマップ上で展開表示することができ、選択した期間内にアクティブなネットワーク接続で実行しているプロセスを表示することができます。 Service Map エージェントがインストールされているリモート マシンを展開してプロセスの詳細を表示すると、対象のマシンと通信するプロセスのみが表示されます。 対象のマシンに接続するエージェントなしのフロントエンド マシンの数が、その接続先となるプロセスの左側に示されます。 対象のマシンがエージェントのないバックエンド マシンに接続している場合、そのバックエンド サーバーは、同じポート番号への他の接続と共に、サーバー ポート グループに含められます。

既定では、サービス マップは、直近 30 分間の依存関係情報を表示します。 左上にある時間コントロールを使用すると、過去の時間範囲 (最長 1 時間まで) のマップを照会し、過去における依存関係を表示することができます (例: インシデントの発生中や変更が発生する前)。 サービス マップのデータは、有料のワークスペースで 30 日間、無料のワークスペースでは 7 日間のあいだ保存されます。

## <a name="status-badges-and-border-coloring"></a>状態バッジと枠線の色分け
マップ内の各サーバーの下部には、サーバーの状態情報を示す状態バッジの一覧が表示されます。 このバッジは、いずれかの Operations Management Suite ソリューション統合から取得したサーバーに関する情報があることを示します。 バッジをクリックすると、状態に関する詳細情報が右側のウィンドウに直接表示されます。 現在使用できる状態バッジには、アラート、サービス デスク、変更、セキュリティ、更新があります。

状態バッジの重大度に応じて、マシン ノードの枠線を赤 (重大)、黄 (警告)、青 (情報) に色分けすることができます。 この色は、状態バッジの中で最も重大な状態を表します。 灰色の枠線は、ノードに状態インジケーターがないことを示します。

![状態バッジ](media/oms-service-map/status-badges.png)

## <a name="machine-groups"></a>マシン グループ
マシン グループを使用すると、単独のサーバーではなく一連のサーバーを中心としたマップを表示できます。これにより、多層アプリケーションまたはサーバー クラスターのすべてのメンバーを 1 つのマップに表示できます。

ユーザーは、同じグループに属するサーバーを選択し、そのグループに名前を付けます。  その後、グループと共にそのプロセスと接続をすべて表示するか、そのグループの他のメンバーに直接関連するプロセスと接続のみを表示するかを選択できます。

![マシン グループ](media/oms-service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>マシン グループの作成
グループを作成するには、[マシン] の一覧で 1 つまたは複数のマシンを選択し、**[グループに追加]** をクリックします。

![グループの作成](media/oms-service-map/machine-groups-create.png)

ここで、**[新規作成]** を選択すると、グループに名前を付けることができます。

![グループの名前付け](media/oms-service-map/machine-groups-name.png)

>[!NOTE]
>現在、マシン グループは 10 台のサーバーに制限されていますが、この上限は間もなく引き上げられる予定です。

### <a name="viewing-a-group"></a>グループの表示
グループを作成した後、[グループ] タブを選択すると、そのグループが表示されます。

![[グループ] タブ](media/oms-service-map/machine-groups-tab.png)

次に、グループ名を選択して、そのマシン グループのマップを表示します。
![マシン グループ](media/oms-service-map/machine-group.png) グループに属するマシンは、マップ内で白い枠線に囲まれています。

グループを展開すると、そのマシン グループを構成するマシンが一覧表示されます。

![マシン グループ内のマシン](media/oms-service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>プロセスによるフィルター
マップ ビューは、グループ内のプロセスと接続をすべて表示するか、マシン グループに直接関連するプロセスと接続のみを表示するかを切り替えることができます。  既定のビューでは、すべてのプロセスが表示されます。  ビューを変更するには、マップの上部にあるフィルター アイコンをクリックします。

![グループのフィルター](media/oms-service-map/machine-groups-filter.png)

**[すべてのプロセス]** を選択すると、グループ内の各マシンのプロセスと接続がすべてマップに表示されます。

![マシン グループのすべてのプロセス](media/oms-service-map/machine-groups-all.png)

**グループ関連プロセス**のみが表示されるようビューを変更すると、マップの表示が、グループの他のマシンに直接関連するプロセスと接続のみに絞られ、簡略化されたビューになります。

![マシン グループのフィルター処理されたプロセス](media/oms-service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>グループへのマシンの追加
既存のグループにマシンを追加するには、追加したいマシンの横にあるチェック ボックスをオンにし、**[グループに追加]** をクリックします。  次に、マシンの追加先となるグループを選択します。
 
### <a name="removing-machines-from-a-group"></a>グループからのマシンの削除
[グループ] の一覧でグループ名を展開し、マシン グループ内のマシンを一覧表示します。  次に、削除したいマシンの横にある省略記号メニューをクリックし、**[削除]** を選択します。

![グループからのマシンの削除](media/oms-service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>グループの削除または名前の変更
グループの一覧にあるグループ名の横にある省略記号メニューをクリックします。

![マシン グループのメニュー](media/oms-service-map/machine-groups-menu.png)


## <a name="role-icons"></a>ロール アイコン
特定のプロセスは、マシンで特定のロール (Web サーバー、アプリケーション サーバー、データベースなど) を担います。 サービス マップでは、プロセスとマシン ボックスにロール アイコンで注釈を付け、プロセスまたはサーバーが担っているロールがひとめでわかるようにします。

| ロール アイコン | Description |
|:--|:--|
| ![Web サーバー](media/oms-service-map/role-web-server.png) | Web サーバー |
| ![アプリ サーバー](media/oms-service-map/role-application-server.png) | アプリケーション サーバー |
| ![データベース サーバー](media/oms-service-map/role-database.png) | データベース サーバー |
| ![LDAP サーバー](media/oms-service-map/role-ldap.png) | LDAP サーバー |
| ![SMB サーバー](media/oms-service-map/role-smb.png) | SMB サーバー |

![ロール アイコン](media/oms-service-map/role-icons.png)


## <a name="failed-connections"></a>失敗した接続
Service Map マップでは、プロセスとコンピューターの失敗した接続が表示されます。赤色の破線によって、クライアント システムがプロセスまたはポートへのアクセスに失敗したことが示されます。 サービス マップ エージェントがデプロイされているシステムで、接続を試みたが失敗した場合、失敗した接続はそのシステムから報告されます。 Service Map は、接続の確立に失敗した TCP ソケットを監視することで、このプロセスを測定します。 この失敗の原因としては、ファイアウォール、クライアントまたはサーバーの構成の問題、またはリモート サービスが利用できない状態であることが考えられます。

![失敗した接続](media/oms-service-map/failed-connections.png)

失敗した接続を把握すると、トラブルシューティング、移行の検証、セキュリティ分析、および全体的なアーキテクチャの理解に役立ちます。 失敗した接続は無害な場合もありますが、多くの場合は問題を端的に示しています。たとえば、突然アクセスできなくなるフェールオーバー環境、クラウド移行後に通信できなくなった 2 つのアプリケーション層などです。

## <a name="client-groups"></a>クライアント グループ
クライアント グループは、Dependency Agent がないクライアント マシンを表す、マップ上のボックスです。 1 つのクライアント グループは、個々のプロセスまたはマシンのクライアントを表します。

![クライアント グループ](media/oms-service-map/client-groups.png)

クライアント グループ内のサーバーの IP アドレスを表示するには、グループを選択します。 グループの内容が、**[Client Group Properties]\(クライアント グループのプロパティ\)** ウィンドウに一覧表示されます。

![クライアント グループのプロパティ](media/oms-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>サーバー ポート グループ
サーバー ポート グループは、Dependency Agent がないサーバー上のサーバー ポートを表すボックスです。 このボックスには、サーバー ポートと、そのポートに接続しているサーバーの数が表示されます。 ボックスを展開すると、個々 のサーバーと接続が表示されます。 ボックス内にサーバーが 1 つしかない場合は、名前または IP アドレスが表示されます。

![サーバー ポート グループ](media/oms-service-map/server-port-groups.png)

## <a name="context-menu"></a>コンテキスト メニュー
サーバーの右上にある省略記号 (...) をクリックすると、そのサーバーのコンテキスト メニューが表示されます。

![失敗した接続](media/oms-service-map/context-menu.png)

### <a name="load-server-map"></a>サーバー マップを読み込む
**[サーバー マップを読み込む]** をクリックすると、新しいマップが表示され、選択したサーバーが対象の新しいマシンとなります。

### <a name="show-self-links"></a>自己リンクを表示する
**[自己リンクを表示する]** をクリックすると、自己リンクを含むサーバー ノードが再描画されます。これは、サーバー内のプロセスを開始および終了する TCP 接続です。 自己リンクが表示されると、このメニュー コマンドは **[自己リンクを非表示にする]** に変わります。これを使用して表示をオフにできます。

## <a name="computer-summary"></a>コンピューターの概要
**[Machine Summary]\(マシンの概要\)** ウィンドウには、サーバーのオペレーティング システム、依存関係の数、その他の Operations Management Suite ソリューションから取得したデータの概要が表示されます。 このようなデータには、パフォーマンス メトリック、サービス デスク チケット、変更の追跡、セキュリティ、更新プログラムがあります。

![[Machine Summary]\(マシンの概要\) ウィンドウ](media/oms-service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>コンピューターとプロセスのプロパティ
Service Map マップを見ている間、マシンとプロセスを選択して、それらのプロパティに関する追加情報を取得できます。 マシンは、DNS 名、IPv4 アドレス、CPU とメモリ容量、VM の種類、オペレーティング システムとバージョン、最後の再起動時刻、Operations Management Suite と Service Map エージェントの ID に関する情報を提供します。

![[Machine Properties]\(マシンのプロパティ\) ウィンドウ](media/oms-service-map/machine-properties.png)

実行プロセスに関するオペレーティング システム メタデータからプロセスの詳細を収集できます。この詳細には、プロセス名、プロセスの説明、ユーザー名とドメイン (Windows 上)、会社名、製品名、製品バージョン、作業ディレクトリ、コマンド ライン、プロセス開始時刻が含まれます。

![[Process Properties]\(プロセスのプロパティ\) ウィンドウ](media/oms-service-map/process-properties.png)

**[Process Summary]\(プロセスの概要\)** ウィンドウでは、接続しているポート、受信と送信の接続、失敗した接続を含む、そのプロセスの接続に関する追加情報が示されます。

![[Process Summary]\(プロセスの概要\) ウィンドウ](media/oms-service-map/process-summary.png)

## <a name="operations-management-suite-alerts-integration"></a>Operations Management Suite のアラートの統合
Service Map は、選択した時間範囲内で選択したサーバーに対して発生したアラートを表示するために、Operations Management Suite アラートと統合されます。 進行中のアラートがある場合はサーバーにアイコンが表示され、**[Machine Alerts]\(マシンのアラート\)** ウィンドウにアラートが一覧表示されます。

![[Machine Alerts]\(マシンのアラート\) ウィンドウ](media/oms-service-map/machine-alerts.png)

Service Map で関連するアラートを表示できるようにするには、特定のコンピューターに対して動作するアラート ルールを作成します。 適切なアラートを作成するには:
- コンピューター別にグループ化する句を含めます (例: **by Computer interval 1minute**)。
- メトリックの測定に基づいてアラートを生成することを選択します。

![アラートの構成](media/oms-service-map/alert-configuration.png)


## <a name="operations-management-suite-log-events-integration"></a>Operations Management Suite のログ イベントの統合
サービス マップは、ログ検索と統合して、選択したサーバーで選択した時間の範囲中に発生したすべてのログ イベントの数を表示します。 イベント数の一覧の任意の行をクリックしてログ検索に移動し、個別のログ イベントを表示することができます。

![[Machine Log Events]\(マシン ログ イベント\) ウィンドウ](media/oms-service-map/log-events.png)

## <a name="operations-management-suite-service-desk-integration"></a>Operations Management Suite のサービス デスクの統合
Service Map と IT Service Management Connector の統合は、両方のソリューションが有効で、Operations Management Suite ワークスペースで構成されているときに自動的に行われます。 Service Map での統合に、"サービス デスク" というラベルが付けられます。 詳細については、[IT Service Management Connector を使用した ITSM 作業項目の一元管理](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview)に関するページを参照してください。

**[Machine Service Desk]\(マシン サービス デスク\)** ウィンドウでは、選択した時間範囲内で選択したサーバーの IT Service Management イベントすべてが一覧表示されます。 進行中の項目がある場合はサーバーにアイコンが表示され、[Machine Service Desk]\(マシン サービス デスク\) ウィンドウにはそれらの項目が一覧表示されます。

![[Machine Service Desk]\(マシン サービス デスク\) ウィンドウ](media/oms-service-map/service-desk.png)

接続されている ITSM ソリューション内の項目を開くには、**[作業項目の表示]** をクリックします。

ログ検索で項目の詳細を表示するには、**[ログ検索で表示]** をクリックします。


## <a name="operations-management-suite-change-tracking-integration"></a>Operations Management Suite の Change Tracking の統合
Service Map と Change Tracking の統合は、両方のソリューションが有効であり、Operations Management Suite ワークスペースで構成されているときに自動的に行われます。

**[Machine Change Tracking]\(マシン変更の追跡\)** ウィンドウには、すべての変更が新しい順に一覧表示されており、ログ検索へのリンクで追加の詳細情報を探すことができます。

![[Machine Change Tracking]\(マシン変更の追跡\) ウィンドウ](media/oms-service-map/change-tracking.png)

次の図は、**[Show in Log Analytics]\(Log Analytics で表示\)** を選択すると表示される ConfigurationChange イベントの詳細なビューです。

![ConfigurationChange イベント](media/oms-service-map/configuration-change-event.png)


## <a name="operations-management-suite-performance-integration"></a>Operations Management Suite のパフォーマンスの統合
**[Machine Performance]\(マシンのパフォーマンス\)** ウィンドウには、選択したサーバーの標準的なパフォーマンス メトリックが表示されます。 このメトリックには、CPU 使用率、メモリ使用率、送受信したネットワークのバイト数、ネットワークで送受信されたバイト数による上位プロセスの一覧が含まれます。 ネットワークのパフォーマンス データを取得するには、Operations Management Suite で Wire Data 2.0 ソリューションを有効にしておく必要もあります。

![[Machine Performance]\(マシンのパフォーマンス\) ウィンドウ](media/oms-service-map/machine-performance.png)


## <a name="operations-management-suite-security-integration"></a>Operations Management Suite のセキュリティの統合
Service Map と Security and Audit の統合は、両方のソリューションが有効であり、Operations Management Suite ワークスペースで構成されているときに自動的に行われます。

**[Machine Security]\(マシンのセキュリティ\)** ウィンドウには、Operations Management Suite Security and Audit ソリューションから取得した、選択したサーバーに関するデータが表示されます。 このウィンドウには、選択した時間範囲における、サーバーの未解決のセキュリティ問題の概要が一覧表示されます。 いずれかのセキュリティ問題をクリックすると、それらの詳細がログ検索でドリルダウンされます。

![[Machine Security]\(マシンのセキュリティ\) ウィンドウ](media/oms-service-map/machine-security.png)


## <a name="operations-management-suite-updates-integration"></a>Operations Management Suite の更新プログラムの統合
Service Map と Update Management の統合は、両方のソリューションが有効であり、Operations Management Suite ワークスペースで構成されているときに自動的に行われます。

**[Machine Updates]\(マシンの更新プログラム\)** ウィンドウには、Operations Management Suite Update Management ソリューションから取得した、選択したサーバーに関するデータが表示されます。 このウィンドウには、選択した時間範囲における、サーバーに不足している更新プログラムの概要が一覧表示されます。

![[Machine Change Tracking]\(マシン変更の追跡\) ウィンドウ](media/oms-service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics のレコード
Service Map のコンピューターとプロセスのインベントリ データは、Log Analytics で[検索](../log-analytics/log-analytics-log-searches.md)できます。 このデータは、移行計画、容量の分析、探索、必要に応じたパフォーマンスのトラブルシューティングといったシナリオに適用できます。

プロセスまたはコンピューターが起動されたとき、あるいは Service Map にオンボードされたときに生成されるレコードに加え、一意のコンピューターとプロセスごとに 1 時間あたり 1 つのレコードが生成されます。 これらのレコードは、次の表に示したプロパティを持ちます。 ServiceMapComputer_CL イベントのフィールドと値は、ServiceMap Azure Resource Manager API のマシン リソースのフィールドにマップされます。 ServiceMapProcess_CL イベントのフィールドと値は、ServiceMap Azure Resource Manager API のプロセス リソースのフィールドにマップされます。 ResourceName_s フィールドは、対応する Resource Manager リソースの名前フィールドと一致します。 

>[!NOTE]
>Service Map 機能の拡張に伴い、これらのフィールドは変更される可能性があります。

個々のプロセスとコンピューターの識別に使用できる、内部生成されたプロパティがあります。

- コンピューター: ResourceId または ResourceName_s を使用して、Operations Management Suite ワークスペース内のコンピューターを一意に識別します。
- プロセス: ResourceId を使用して、Operations Management Suite ワークスペース内のプロセスを一意に識別します。 ResourceName_s は、プロセスが実行されているコンピューターのコンテキスト内で一意です (MachineResourceName_s) 

指定の時間範囲にある指定のプロセスとコンピューターについては、複数のレコードが存在できるため、クエリは、同じコンピューターまたはプロセスに対して複数のレコードを返すことがあります。 最新のレコードのみが返されるようにするには、"| dedup ResourceId" をクエリに追加します。

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL レコード
*ServiceMapComputer_CL* 型のレコードには、サービス マップ エージェントがインストールされているサーバーのインベントリ データが含まれます。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 |
|:--|:--|
| 型 | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | ワークスペース内のマシンに対する一意識別子 |
| ResourceName_s | ワークスペース内のマシンに対する一意識別子 |
| ComputerName_s | コンピューターの FQDN |
| Ipv4Addresses_s | サーバーの IPv4 アドレスの一覧 |
| Ipv6Addresses_s | サーバーの IPv6 アドレスの一覧 |
| DnsNames_s | DNS 名の配列 |
| OperatingSystemFamily_s | Windows または Linux |
| OperatingSystemFullName_s | オペレーティング システムのフル ネーム  |
| Bitness_s | マシンのビット数 (32 ビットまたは 64 ビット)  |
| PhysicalMemory_d | 物理メモリ (MB) |
| Cpus_d | CPU の数 |
| CpuSpeed_d | CPU 速度 (MHz)|
| VirtualizationState_s | "*不明*"、"*物理*"、"*仮想*"、"*ハイパーバイザー*" |
| VirtualMachineType_s | *hyperv*、*vmware* など |
| VirtualMachineNativeMachineId_g | ハイパーバイザーによって割り当てられた VM ID |
| VirtualMachineName_s | VM の名前 |
| BootTime_t | ブート時間 |



### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL 型のレコード
*ServiceMapProcess_CL* 型のレコードには、サービス マップ エージェントがインストールされているサーバーの TCP 接続プロセスのインベントリ データが含まれています。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 |
|:--|:--|
| 型 | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | ワークスペース内のプロセスに対する一意識別子 |
| ResourceName_s | 実行中のマシン内のプロセスに対する一意識別子|
| MachineResourceName_s | マシンのリソース名 |
| ExecutableName_s | プロセスの実行可能ファイルの名前 |
| StartTime_t | プロセス プールの開始時刻 |
| FirstPid_d | プロセス プール内の最初の PID |
| Description_s | プロセスの説明 |
| CompanyName_s | 会社の名前 |
| InternalName_s | 内部名 |
| ProductName_s | 製品の名前 |
| ProductVersion_s | 製品バージョン |
| FileVersion_s | ファイル バージョン |
| CommandLine_s | コマンド ライン |
| ExecutablePath _s | 実行可能ファイルのパス |
| WorkingDirectory_s | 作業ディレクトリ |
| UserName | プロセスが実行されているアカウント |
| UserDomain | プロセスが実行されているドメイン |


## <a name="sample-log-searches"></a>サンプル ログ検索

### <a name="list-all-known-machines"></a>既知のコンピューターを一覧表示
Type=ServiceMapComputer_CL | dedup ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>すべての管理されたコンピューターの物理メモリ容量を一覧表示。
Type=ServiceMapComputer_CL | select PhysicalMemory_d, ComputerName_s | Dedup ResourceId

### <a name="list-computer-name-dns-ip-and-os"></a>コンピューター名、DNS、IP、および OS を一覧表示。
Type=ServiceMapComputer_CL | select ComputerName_s, OperatingSystemFullName_s, DnsNames_s, IPv4Addresses_s  | dedup ResourceId

### <a name="find-all-processes-with-sql-in-the-command-line"></a>"sql" でコマンドラインのすべてのプロセスを検索
Type=ServiceMapProcess_CL CommandLine_s = \*sql\* | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>リソース名でコンピューター (最新のレコード) を検索
Type=ServiceMapComputer_CL "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>IP アドレスでマシン (最新のレコード) を検索
Type=ServiceMapComputer_CL "10.229.243.232" | dedup ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>指定のマシンにある既知のプロセスすべてを一覧表示
Type=ServiceMapProcess_CL MachineResourceName_s="m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="list-all-computers-running-sql"></a>SQL を実行しているすべてのコンピューターを一覧表示
Type=ServiceMapComputer_CL ResourceName_s IN {Type=ServiceMapProcess_CL \*sql\* | Distinct MachineResourceName_s} | dedup ResourceId | Distinct ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>データセンター内にあるすべての製品バージョンの curl を一覧表示
Type=ServiceMapProcess_CL ExecutableName_s=curl | Distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>CentOS を実行しているすべてのコンピューターのコンピューター グループを作成
Type=ServiceMapComputer_CL OperatingSystemFullName_s = \*CentOS\* | Distinct ComputerName_s


## <a name="rest-api"></a>REST API
サービス マップのすべてのサーバー、プロセス、および依存関係データは、[サービス マップ REST API](https://docs.microsoft.com/rest/api/servicemap/) を使用して取得できます。


## <a name="diagnostic-and-usage-data"></a>診断と使用状況データ
マイクロソフトは、お客様によるサービス マップ サービスの使用を通して、使用状況とパフォーマンス データを自動的に収集します。 Microsoft はこのデータを使用して、提供する Service Map サービスの品質、セキュリティ、整合性の向上に努めています。 正確で効果的なトラブルシューティングの機能を提供するために、このデータにはソフトウェアの構成に関する情報が含まれています。たとえば、オペレーティング システムとバージョン、IP アドレス、DNS 名、ワークステーション名などです。 名前や住所などの連絡先情報は収集されません。

データの収集と使用の詳細については、「[Microsoft オンライン サービスのプライバシーに関する声明](https://go.microsoft.com/fwlink/?LinkId=512132)」を参照してください。


## <a name="next-steps"></a>次のステップ
Log Analytics の[ログ検索](../log-analytics/log-analytics-log-searches.md)の詳細を確認して、Service Map によって収集されたデータを取得します。


## <a name="troubleshooting"></a>トラブルシューティング
[サービス マップの構成に関するドキュメントの「トラブルシューティング」](operations-management-suite-service-map-configure.md#troubleshooting)をご覧ください。


## <a name="feedback"></a>フィードバック
サービス マップやこのドキュメントについてフィードバックはありますか。  [UserVoice ページ](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)では、機能を提案したり、既存の提案に投票することができます。
