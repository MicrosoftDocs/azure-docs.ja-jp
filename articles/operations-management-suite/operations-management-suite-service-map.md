---
title: "Operations Management Suite (OMS) のサービス マップ| Microsoft Docs"
description: "サービス マップは、Windows および Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップする Operations Management Suite (OMS) のソリューションです。  この記事では、サービス マップを環境に展開して、さまざまなシナリオで使用する場合の詳細について説明します。"
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
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: aca6cd69647406d0140d8b0d0cba8e4690b04ae1
ms.lasthandoff: 03/18/2017


---

# <a name="using-service-map-solution-in-operations-management-suite-oms"></a>Operations Management Suite (OMS) のサービス マップ ソリューションの使用
サービス マップは、Windows および Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 これを使用すると、サーバーを重要なサービスを提供する相互接続されたシステムとして表示することができます。  サービス マップは、TCP 接続アーキテクチャ全体のサーバー、プロセス、ポート間の接続を表示します。エージェントのインストール以外の構成は必要ありません。

この記事では、サービス マップの使い方を詳しく説明します。  サービス マップの構成とエージェントの利用方法については、「[Operations Management Suite (OMS) のサービス マップの構成](operations-management-suite-service-map-configure.md)」をご覧ください。


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>使用例: IT プロセスの依存関係を明確にする

### <a name="discovery"></a>探索
サービス マップは、サーバー、プロセス、およびサード パーティのサービス間での依存関係について、共通の参照マップを自動的に作成します。  すべての TCP 依存関係を探索してマップし、予想外の接続、依存しているリモートのサード パーティ システム、Active Directory などの従来型のネットワークの暗領域の依存関係を識別します。  サービス マップは、管理システムが行おうとしたネットワーク接続の失敗を検出し、サーバー構成に関する潜在的な問題、サービスの停止、およびネットワークの問題を識別する手助けをします。

### <a name="incident-management"></a>インシデント管理
サービス マップでは、システムがどのように接続され互いに影響しているかを示すので、当て推量で問題を特定することが少なくなります。  失敗した接続だけでなく、接続されているクライアントに関する情報を提供するので、ロード バランサー構成の問題、重要なサービスへの予想外なまたは過剰な負荷、実稼働システムと通信する開発者のコンピューターなどの悪意のあるクライアントを特定するのに役立ちます。  また、OMS の変更の追跡との統合ワークフローでは、バックエンド コンピューターの変更イベントとサービスのどちらがインシデントの根本原因なのかがわかります。

### <a name="migration-assurance"></a>確実な移行
サービス マップを使用すると、Azure の移行を効果的に計画、迅速化、および検証することができ、必要な作業を網羅して予想外の障害が起きないようにできます。  同時に移行する必要のある、相互依存しているシステムをすべて検出し、システム構成と容量を評価し、稼働中のシステムがまだユーザーにサービス提供しているのか、あるいは移行せず使用中止する対象となるのかを特定することができます。  移行完了後は、クライアントの負荷と ID をチェックして、テスト システムと顧客が接続していることを確認できます。  サブネットの計画とファイアウォールの定義に問題がある場合は、サービス マップにある失敗した接続を見れば、接続が必要なシステムがわかります。

### <a name="business-continuity"></a>ビジネス継続性
Azure Site Recovery を使用していて、お使いのアプリケーション環境における復元シーケンスの定義が必要な場合、サービス マップは、どのようにシステムが相互依存しているかを自動的に表示して、信頼性の高い復旧計画を立てる手助けをします。  重要なサーバーを選択してそのクライアントを表示することで、そのサーバーが復元され使用可能になった後にのみ回復すべきフロント エンドのシステムを識別できます。  逆に、重要なサーバーのバック エンドの依存関係を調べることで、重点的システムを復元するより先に復元しなければいけないシステムを識別することができます。

### <a name="patch-management"></a>更新プログラムの管理
サービス マップは、OMS System Update Assessment の使用を強化します。どの他部署とサーバーが自分たちのサービスに依存しているかを示すので、更新プログラム適用のためにシステム停止する前に、事前に通知することができるのです。  また、サービス マップは、更新プログラムを適用して再起動した後にそのサービスが使用可能で正常に接続されているかを示すことにより、OMS における更新プログラム管理を強化します。


## <a name="mapping-overview"></a>マッピングの概要
サービス マップ エージェントは、それらがインストールされているサーバー上のすべての TCP 接続プロセスに関する情報に加え、各プロセスの受信および送信接続の詳細も収集します。  サービス マップソリューションの左側にあるマシン一覧を使用して、サービス マップエージェントがインストールされているコンピューターを選択し、選択した時間範囲での依存関係を視覚化することができます。  マシンの依存関係マップは、特定のコンピューターに焦点を当て、そのコンピューターの直接 TCP クライアントまたはサーバーであるすべてのコンピューターを表示します。

![サービス マップの概要](media/oms-service-map/service-map-overview.png)

マシンはマップ上で展開表示することができ、選択した期間内にアクティブなネットワーク接続で実行しているプロセスを表示することができます。  サービス マップ エージェントがインストールされているリモート コンピューターを展開してプロセスの詳細を表示すると、そのコンピューターと通信するプロセスのみが表示されます。  コンピューターに接続するエージェントレスのフロント エンド コンピューターの数が、接続しているプロセスの左側に示されます。  フォーカス マシンがエージェントのないバック エンド マシンに接続している場合、そのバック エンド サーバーは、同じポート番号への他の接続と共に、サーバー ポート グループに含められます。

既定では、サービス マップは、直近 10 分間の依存関係情報を表示します。  左上にある時間コントロールを使用して、マップで過去の時間範囲 (最長 1 時間まで) を照会すると、インシデントの発生中や変更が発生する前などの過去における依存関係を表示することができます。    サービス マップのデータは、有料のワークスペースで 30 日間、無料のワークスペースでは 7 日間のあいだ保存されます。

## <a name="status-badges-and-border-coloring"></a>状態バッジと枠線の色分け
マップ内の各サーバーの下部には、サーバーの状態情報を示す状態バッジの一覧が表示されます。  このバッジは、OMS ソリューション統合のいずれかに、サーバー関連情報がいくつかあることを示します。  バッジをクリックすると、右側のパネルに、状態に関する詳細情報が直接表示されます。  現在の可用性の状態バッジには、アラート、変更、セキュリティ、および更新が含まれています。

状態バッジの重要度に基づいて、マシン ノードの枠線を赤 (重大)、黄 (警告)、青 (情報) に色分けすることができます。  この色は、状態バッジの中で最も重大な状態を表します。  現在の状態のインジケーターを持たないノードでは、灰色の枠線が表示されます。

![状態バッジ](media/oms-service-map/status-badges.png)

## <a name="role-icons"></a>ロール アイコン
特定のプロセスは、コンピューターで特定のロール (Web サーバー、アプリケーション サーバー、データベースなど) を担います。サービス マップでは、プロセスとマシン ボックスにロール アイコンで注釈を付け、プロセスまたはサーバーが担っているロールがひとめでわかるようにします。

| ロール アイコン | Description |
|:--|:--|
| ![Web サーバー](media/oms-service-map/role-web-server.png) | Web サーバー |
| ![アプリ サーバー](media/oms-service-map/role-application-server.png) | アプリケーション サーバー |
| ![データベース サーバー](media/oms-service-map/role-database.png) | データベース サーバー |
| ![LDAP サーバー](media/oms-service-map/role-ldap.png) | LDAP サーバー |
| ![SMB サーバー](media/oms-service-map/role-smb.png) | SMB サーバー |

![ロール アイコン](media/oms-service-map/role-icons.png)


## <a name="failed-connections"></a>失敗した接続
プロセスやコンピューターの失敗した接続は、クライアント システムがプロセスまたはポートのどちらのアクセスに失敗したかを示す赤色の破線でサービス マップに表示されます。  サービス マップ エージェントがデプロイされているシステムで、接続を試みたが失敗した場合、失敗した接続はそのシステムから報告されます。  サービス マップは、接続の確立に失敗した TCP ソケットを監視することで、これを測定します。  原因としては、ファイアウォール、クライアントまたはサーバーの構成の問題、あるいはリモート サービスが利用不可の状態であること、が考えられます。

![失敗した接続](media/oms-service-map/failed-connections.png)

失敗した接続を把握すると、トラブルシューティング、移行の検証、セキュリティ分析、および全体的なアーキテクチャの理解に役立ちます。  失敗した接続は無害な場合もありますが、多くの場合は問題を端的に示しています。たとえば、突然アクセスできなくなるフェールオーバー環境、クラウド移行後に通信できなくなった 2 つのアプリケーション層などです。

## <a name="client-groups"></a>クライアント グループ
クライアント グループは、Dependency Agent がないクライアント マシンを表す、マップ上のボックスです。  1 つのクライアント グループは、1 つのプロセスのクライアントを表します。

![クライアント グループ](media/oms-service-map/client-groups.png)

クライアント グループ内のサーバーの IP アドレスを表示するには、グループを選択します。  グループの内容は、プロパティ パネルに表示されます。

![クライアント グループのプロパティ](media/oms-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>サーバー ポート グループ
サーバー ポート グループは、Dependency Agent がないサーバー上のサーバー ポートを表すボックスです。  ボックスには、サーバー ポートと、そのポートに接続しているサーバーの数が表示されます。  ボックスを展開すると、個々 のサーバーと接続が表示されます。  ボックス内にサーバーが 1 つしかない場合は、名前または IP アドレスが表示されます。

![サーバー ポート グループ](media/oms-service-map/server-port-groups.png)

## <a name="context-menu"></a>コンテキスト メニュー
サーバーの右上にある 3 つのドットをクリックすると、そのサーバーのコンテキスト メニューが表示されます。

![失敗した接続](media/oms-service-map/context-menu.png)

### <a name="load-server-map"></a>サーバー マップの読み込み
[Load Server Map (サーバー マップの読み込み)] を選択すると、新しいマップと、選択したサーバーが新しい対象コンピューターとして表示されます。

### <a name="showhide-self-links"></a>自己リンクを表示/非表示
[Show Self Links (自己リンクを表示)] を選択すると、自己リンクを含むサーバー ノードが再描画されます。これは、サーバー内のプロセスを開始および終了する TCP 接続です。  自己リンクが表示されている場合、このメニューは [Hide Self Links (自己リンクを非表示)] に変わります。これにより、ユーザーが自己リンクの描画を切り替えることができます。

## <a name="computer-summary"></a>コンピューターの概要
[マシンの概要] パネルには、サーバーのオペレーティング システムの概要と依存関係の数に加え、他の OMS ソリューションからのさまざまなデータ (パフォーマンス メトリック、変更の追跡、セキュリティ、更新プログラムなど) が含まれます。

![マシンの概要](media/oms-service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>コンピューターとプロセスのプロパティ
サービス マップを見るときは、コンピューターとプロセスを選択して、それらのプロパティに関する追加情報を得ることができます。  コンピューターは、DNS 名、IPv4 アドレス、CPU とメモリ容量、VM の種類、オペレーティング システムのバージョン、最後の再起動時刻、OMS と When navigating aサービス マップエージェントの ID に関する情報を提供します。

![マシンのプロパティ](media/oms-service-map/machine-properties.png)

プロセスの詳細は、実行プロセスに関するオペレーティング システム メタデータから収集されます。データには、プロセス名、プロセスの説明、ユーザー名とドメイン (Windows 上)、会社名、製品名、製品バージョン、作業ディレクトリ、コマンドライン、およびプロセス開始時刻が含まれます。

![プロセスのプロパティ](media/oms-service-map/process-properties.png)

[プロセスの概要] パネルでは、接続しているポート、受診と送信の接続、失敗した接続を含む、そのプロセスの接続に関する追加情報を提供します。

![プロセスの概要](media/oms-service-map/process-summary.png)

## <a name="oms-alerts-integration"></a>OMS のアラートの統合
サービス マップは、選択されたサーバーで選択された時間範囲中に発生したアラートを表示するために、OMS のアラートと統合されます。  サーバーに進行中のアラートがある場合は、アイコンが表示され、コンピューターのアラート パネルにアラートが一覧表示されます。

![コンピューターのアラート パネル](media/oms-service-map/machine-alerts.png)

関連するアラートをサービス マップで表示するには、アラート ルールを作成して特定のコンピューターで使用されるようにする必要があります。  適切なアラートを作成するには:
- コンピューター別にグループ化する句を含めます ("by Computer interval 1minute")
- メトリックの測定に基づいてアラートを生成することを選択します

![アラートの構成](media/oms-service-map/alert-configuration.png)


## <a name="oms-log-events-integration"></a>OMS のログ イベントとの統合
サービス マップは、ログ検索と統合して、選択したサーバーで選択した時間の範囲中に発生したすべてのログ イベントの数を表示します。  イベント数の一覧の任意の行をクリックしてログ検索に移動し、個別のログ イベントを表示することができます。

![ログ イベント](media/oms-service-map/log-events.png)

## <a name="oms-change-tracking-integration"></a>OMS の変更の追跡との統合
サービス マップと変更の追跡との統合は、両方のソリューションが有効であり、OMS ワークスペースに構成されているときは自動的に行われます。

[Machine Change Tracking] \(コンピューター変更の追跡) パネルには、すべての変更の一覧が新しい順で表示されており、ログ検索へのリンクで追加情報を探すことができます。
![[Machine Change Tracking] (コンピューター変更の追跡) パネル](media/oms-service-map/change-tracking.png)

下記では、**[Show in Log Analytics] (Log Analytics で表示)** を選択して、構成の変更イベントの詳細を表示しています。
![構成の変更イベント](media/oms-service-map/configuration-change-event.png)


## <a name="oms-performance-integration"></a>OMS のパフォーマンスの統合
コンピューターのパフォーマンス パネルには、選択したサーバーの標準的なパフォーマンス メトリックが表示されます。  メトリックには、CPU 使用率、メモリ使用率、送受信したネットワークのバイト数、およびネットワークで送受信されたバイト数による上位プロセスの一覧が含まれます。  ネットワークのパフォーマンス データを取得するには、OMS で Wire Data 2.0 ソリューションを有効にしておく必要もあります。
![[Machine Change Tracking] (コンピューター変更の追跡) パネル](media/oms-service-map/machine-performance.png)


## <a name="oms-security-integration"></a>OMS Security の統合
サービス マップとセキュリティと監査との統合は、両方のソリューションが有効であり、OMS ワークスペースに構成されているときは自動的に行われます。

コンピューターのセキュリティ パネルには、選択したサーバーの OMS のセキュリティと監査ソリューションからのデータが表示されます。  パネルには、選択した時間範囲について、サーバーの未解決のセキュリティ問題の概要が一覧表示されます。  セキュリティ問題のいずれかをクリックすると、そのセキュリティの問題の詳細が Log Search でドリルダウンされます。
![[Machine Change Tracking] (コンピューター変更の追跡) パネル](media/oms-service-map/machine-security.png)


## <a name="oms-updates-integration"></a>OMS の更新の統合
サービス マップと更新管理との統合は、両方のソリューションが有効であり、OMS ワークスペースに構成されているときは自動的に行われます。

コンピューターの更新パネルには、選択したサーバーの OMS のセキュリティと監査ソリューションからのデータが表示されます。  パネルには、選択した時間範囲のサーバーに不足している更新プログラムの概要が一覧表示されます。
![[Machine Change Tracking] (コンピューター変更の追跡) パネル](media/oms-service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics のレコード
サイト マップのコンピューターとプロセスのインベントリ データは、Log Analytics で[検索](../log-analytics/log-analytics-log-searches.md)できます。  これは、移行計画、容量の分析、探索、暫定的なパフォーマンスのトラブルシューティングといった作業に適用できます。

プロセスまたはコンピューターが起動されたとき、またはサービス マップにオンボードされたときに生成されるレコードに加え、一意のコンピューターとプロセスごとに 1 時間あたり 1 つのレコードが生成されます。  これらのレコードは、次の表に示したプロパティを持ちます。  ServiceMapComputer_CL イベントのフィールドと値は、ServiceMap ARM API のマシン リソースのフィールドにマップされます。  ServiceMapProcess_CL イベントのフィールドと値は、ServiceMap ARM API のプロセス リソースのフィールドにマップされます。  ResourceName_s フィールドは、対応する ARM リソースの名前フィールドと一致します。 注: サービス マップ機能を拡張すると、こうしたフィールドは変更される可能性があります。


個々のプロセスとコンピューターの識別に使用できる、内部生成されたプロパティがあります。

- コンピューター - ResourceId または ResourceName_s を使用して、OMS ワークスペース内のコンピューターを一意に識別します。
- プロセス - ResourceId を使用して、OMS ワークスペース内のプロセスを一意に識別します。 ResourceName_s は、プロセスが実行されているコンピューターのコンテキスト内で一意です (MachineResourceName_s) 

特定の時間範囲にある特定のプロセスおよびコンピューターについては、複数のレコードが存在できるため、クエリが、同じコンピューターまたはプロセスに対して複数のレコードを返すことがあります。 最新のレコードのみが返されるようにするには、"| dedup ResourceId" をクエリに追加します。

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL レコード
**ServiceMapComputer_CL** 型のレコードには、サービス マップ エージェントがインストールされているサーバーのインベントリ データが含まれます。  これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 |
|:--|:--|
| 型 | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | ワークスペース内のマシンに対する一意の識別子 |
| ResourceName_s | ワークスペース内のマシンに対する一意の識別子 |
| ComputerName_s | コンピューターの FQDN |
| Ipv4Addresses_s | サーバーの IPv4 アドレスの一覧 |
| Ipv6Addresses_s | サーバーの IPv6 アドレスの一覧 |
| DnsNames_s | DNS 名の配列 |
| OperatingSystemFamily_s | Windows または Linux |
| OperatingSystemFullName_s | オペレーティング システムの完全な名前  |
| Bitness_s | マシン (32 ビットまたは 64 ビット) のビット数 |
| PhysicalMemory_d | 物理メモリ (MB) |
| Cpus_d | CPU の数 |
| CpuSpeed_d | CPU 速度 (MHz)|
| VirtualizationState_s | "不明"、"物理"、"仮想"、"ハイパーバイザー" |
| VirtualMachineType_s | "hyperv"、"vmware" など |
| VirtualMachineNativeMachineId_g | ハイパーバイザーによって割り当てられた VM ID |
| VirtualMachineName_s | VM 名 |
| BootTime_t | ブート時間 |



### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL 型のレコード
**ServiceMapProcess_CL** 型のレコードには、サービス マップ エージェントがインストールされているサーバーの TCP 接続プロセスのインベントリ データが含まれています。  これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 |
|:--|:--|
| 型 | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | ワークスペース内のプロセスに対する一意の識別子 |
| ResourceName_s | 実行中のコンピューター内のプロセスに対する一意の識別子|
| MachineResourceName_s | コンピューター リソース名 |
| ExecutableName_s | 実行可能プロセス名 |
| StartTime_t | プロセス プールの開始時刻 |
| FirstPid_d | プロセス プール内の最初の PID |
| Description_s | プロセスの説明 |
| CompanyName_s | 会社名 |
| InternalName_s | 内部名 |
| ProductName_s | 製品名 |
| ProductVersion_s | 製品バージョン |
| FileVersion_s | ファイル バージョン |
| CommandLine_s | コマンド ライン |
| ExecutablePath _s | 実行可能ファイルへのパス |
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

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>IP アドレスでコンピューター (最新のレコード) を検索
Type=ServiceMapComputer_CL "10.229.243.232" | dedup ResourceId

### <a name="list-all-known-processes-on-a-given-machine"></a>特定のコンピューターにある既知のプロセスを一覧表示
Type=ServiceMapProcess_CL MachineResourceName_s="m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="list-all-computers-running-sql"></a>SQL を実行しているすべてのコンピューターを一覧表示
Type=ServiceMapComputer_CL ResourceName_s IN {Type=ServiceMapProcess_CL \*sql\* | Distinct MachineResourceName_s} | dedup ResourceId | Distinct ComputerName_s

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>自分のデータ センター内にあるすべての製品バージョンのCurl を一覧表示する
Type=ServiceMapProcess_CL ExecutableName_s=curl | Distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>CentOS を実行しているすべてのコンピューターのコンピューター グループを作成する
Type=ServiceMapComputer_CL OperatingSystemFullName_s = \*CentOS\* | Distinct ComputerName_s


## <a name="rest-api"></a>REST API
サービス マップのすべてのサーバー、プロセス、および依存関係データは、[サービス マップ REST API](https://docs.microsoft.com/rest/api/servicemap/) を使用して取得できます。


## <a name="diagnostic-and-usage-data"></a>診断と使用状況データ
マイクロソフトは、お客様によるサービス マップ サービスの使用を通して、使用状況とパフォーマンス データを自動的に収集します。 Microsoft はこのデータを使用して、提供するサービス マップサービスの品質、セキュリティ、および整合性の向上に努めています。 データには、オペレーティング システムとバージョンのような、ソフトウェアの構成に関する情報が含まれています。また、正確で効果的なトラブルシューティングの機能を提供するために、IP アドレス、DNS 名、およびワークステーション名も含まれています。 名前や住所などの連絡先情報は収集されません。

データの収集と使用状況の詳細については、[Microsoft オンライン サービスのプライバシーに関する声明](https://go.microsoft.com/fwlink/?LinkId=512132)を参照してください。


## <a name="next-steps"></a>次のステップ
- Log Analytics の[ログ検索](../log-analytics/log-analytics-log-searches.md)の詳細を確認して、サービス マップによって収集されたデータを取得します。


## <a name="troubleshooting"></a>トラブルシューティング
- [サービス マップの構成に関するドキュメントの「トラブルシューティング」](operations-management-suite-service-map-configure.md#troubleshooting)をご覧ください。


## <a name="feedback"></a>フィードバック
サービス マップやこのドキュメントについてフィードバックはありますか。  [User Voice ページ](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)では、機能を提案したり、既存の提案に投票することができます。

