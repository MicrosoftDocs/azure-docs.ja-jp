---
title: VM insights からログをクエリする方法
description: VM insights ソリューションでは、メトリックとログ データが収集されます。この記事では、レコードについて説明し、サンプル クエリを紹介します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 28ee7f3d327c09f5837c7dc9e2f39c0f2ca4d888
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046535"
---
# <a name="how-to-query-logs-from-vm-insights"></a>VM insights からログをクエリする方法

VM insights では、パフォーマンスと接続のメトリック、コンピューターとプロセスのインベントリ データ、および正常性状態の情報の収集と、Azure Monitor 内の Log Analytics ワークスペースへの転送が行われます。  このデータは、Azure Monitor で[クエリ](../logs/log-query-overview.md)用に使用できます。 このデータは、移行計画、容量の分析、探索、必要に応じたパフォーマンスのトラブルシューティングといったシナリオに適用できます。

## <a name="map-records"></a>Map レコード

プロセスまたはコンピューターが起動されたとき、あるいは VM insights Map 機能にオンボードされたときに生成されるレコードに加え、一意のコンピューターとプロセスごとに 1 時間あたり 1 つのレコードが生成されます。 これらのレコードは、次の表に示したプロパティを持ちます。 ServiceMapComputer_CL イベントのフィールドと値は、ServiceMap Azure Resource Manager API のマシン リソースのフィールドにマップされます。 ServiceMapProcess_CL イベントのフィールドと値は、ServiceMap Azure Resource Manager API のプロセス リソースのフィールドにマップされます。 ResourceName_s フィールドは、対応する Resource Manager リソースの名前フィールドと一致します。 

個々のプロセスとコンピューターの識別に使用できる、内部生成されたプロパティがあります。

- コンピューター:*ResourceId* または *ResourceName_s* を使用して、Log Analytics ワークスペース内のコンピューターを一意に識別します。
- プロセス:*ResourceId* を使用して、Log Analytics ワークスペース内のプロセスを一意に識別します。 *ResourceName_s* は、プロセスが実行中のマシンのコンテキスト内で一意です (MachineResourceName_s) 

指定の時間範囲にある指定のプロセスとコンピューターについては、複数のレコードが存在できるため、クエリは、同じコンピューターまたはプロセスに対して複数のレコードを返すことがあります。 最新のレコードのみが返されるようにするには、`| summarize arg_max(TimeGenerated, *) by ResourceId` をクエリに追加します。

### <a name="connections-and-ports"></a>接続とポート

接続メトリック機能により、Azure Monitor ログに 2 つの新しいテーブル (VMConnection と VMBoundPort) が導入されました。 これらのテーブルは、マシンへの接続 (受信と送信) と、それらに対する開いているまたはアクティブなサーバー ポートに関する情報を提供します。 接続メトリックは、時間枠の間に特定のメトリックを取得する手段を提供する API を介して公開されています。 リスニング ソケットで *受諾* することで得られる TCP 接続は受信ですが、所定の IP とポートに *接続* することで作成される接続は送信です。 接続の方向は Direction プロパティで表され、**受信** または **送信** のいずれかに設定できます。 

これらのテーブル内のレコードは、Dependency Agent によって報告されるデータから生成されます。 いずれの記録も、1 分の時間間隔での観測を表します。 TimeGenerated プロパティは、時間間隔の開始を示します。 各レコードには、エンティティに関連付けられたメトリックに加えて、接続またはポートなど、それぞれのエンティティを識別する情報が含まれています。 現在のところ、TCP over IPv4 を使用することで発生するネットワーク アクティビティのみが報告されます。 

#### <a name="common-fields-and-conventions"></a>共通するフィールドと規則 

次のフィールドと規則は、VMConnection と VMBoundPort の両方に適用されます。 

- コンピューター:レポート マシンの完全修飾ドメイン名 
- AgentId:Log Analytics エージェントがインストールされているマシンの一意識別子  
- マシン:ServiceMap によって公開されているマシンの Azure Resource Manager リソースの名前。 これは *m-{GUID}* という形式です。この *GUID* は AgentId と同じ GUID です。  
- プロセス:ServiceMap によって公開されているプロセスの Azure Resource Manager リソースの名前。 これは *p-{hex string}* という形式です。 プロセスはマシンのスコープ内で一意です。また、マシン全体で一意のプロセス ID を生成するには、Machine フィールドと Process フィールドを結合します。 
- ProcessName:レポート プロセスの実行可能ファイルの名前
- すべての IP アドレスは、IPv4 正規形式の文字列です (例: *13.107.3.160*)。 

コストと複雑さを管理するため、接続レコードは個々の物理ネットワーク接続を表すものではありません。 複数の物理ネットワーク接続は、論理接続にグループ化され、その後それぞれのテーブルに反映されます。  つまり、*VMConnection* テーブル内のレコードは、論理グルーピングを表しており、観察されている個々の物理接続を表していません。 所定の 1 分間隔で次の属性に同じ値を共有する物理ネットワーク接続は、*VMConnection* 内で 1 つの論理レコードに集約されます。 

| プロパティ | 説明 |
|:--|:--|
|Direction |接続の方向であり、値は *受信* または *送信* です |
|Machine |コンピューターの FQDN |
|Process |プロセスまたはプロセスのグループの ID、接続の開始/受諾 |
|SourceIp |送信元の IP アドレス |
|DestinationIp |送信先の IP アドレス |
|DestinationPort |送信先のポート番号 |
|Protocol |接続に使用されるプロトコル。  値は *tcp* です。 |

グループ化の影響を考慮するため、グループ化された物理接続の数に関する情報は、レコードの次のプロパティに提示されます。

| プロパティ | 説明 |
|:--|:--|
|LinksEstablished |報告時間枠の間に確立された物理ネットワーク接続の数 |
|LinksTerminated |報告時間枠の間に切断された物理ネットワーク接続の数 |
|LinksFailed |報告時間枠の間に失敗した物理ネットワーク接続の数。 現在のところ、この情報は送信接続に対してのみ使用できます。 |
|LinksLive |報告時間枠の終了時点で開いていた物理ネットワーク接続の数|

#### <a name="metrics"></a>メトリック

接続数メトリックに加えて、所定の論理接続またはネット ワークポートで送受信されるデータ量に関する情報も、レコードの次のプロパティに加えられています。

| プロパティ | 説明 |
|:--|:--|
|BytesSent |報告時間枠の間に送信された合計バイト数 |
|BytesReceived |報告時間枠の間に受信された合計バイト数 |
|Responses |報告時間枠の間に観測された応答の数。 
|ResponseTimeMax |報告時間枠の間に観測された最長応答時間 (ミリ秒)。 値がない場合、プロパティは空欄です。|
|ResponseTimeMin |報告時間枠の間に観測された最短応答時間 (ミリ秒)。 値がない場合、プロパティは空欄です。|
|ResponseTimeSum |報告時間枠の間に観測された全応答時間の合計 (ミリ秒)。 値がない場合、プロパティは空欄です。|

報告される第 3 のタイプのデータは応答時間です。これは、発信者が接続を介して送信した要求が処理されて、リモート エンドポイントによって応答されるのを待機する時間です。 報告される応答時間は、内在するアプリケーション プロトコルの実際の応答時間の推定値です。 これは、物理ネットワーク接続の送信元と送信先の間のデータ フローの観察に基づき、経験則を使用して計算されます。 これは、概念上、要求の最後のバイトが送信者を離れる時間と、応答の最後のバイトが送信者に返される時間の差です。 これらの 2 つのタイムスタンプは、所定の物理接続で要求イベントと応答イベントを明確化するために使用されます。 これらの差は、1 つの要求の応答時間を表します。 

この機能の最初のリリースでは、当社のアルゴリズムは推定であり、所定のネットワーク接続に使用される実際のアプリケーション プロトコルに応じて、さまざまな成功の度合いで動作する可能性があります。 たとえば、現在のアプローチは、HTTP(S) のような要求応答ベースのプロトコルでは正しく動作しますが、一方向またはメッセージ キュー ベースのプロトコルでは動作しません。

考慮すべき重要な点は次のとおりです。

1. プロセスが同じ IP アドレスでも複数のネットワーク インターフェイスで接続を受け入れる場合、インターフェイスごとに別のレコードが報告されます。 
2. ワイルドカード IP 付きレコードにはアクティビティはありません。 これらは、マシン上のポートが受信トラフィックにオープンであるという事実を表すために加えられています。
3. 冗長性とデータ量を減らすために、ワイルドカード IP 付きレコードは、特定の IP アドレスと一致するレコード (同じプロセス、ポート、プロトコル) がある場合は省略されます。 ワイルドカード IP レコードが省略される場合、特定の IP アドレス付き IsWildcardBind レコード プロパティは、ポートが報告マシンのあらゆるインターフェイスで公開されていることを示すために、"True" に設定されます。
4. 特定のインターフェイスにのみバインドされているポートでは、IsWildcardBind は *False* に設定されます。

#### <a name="naming-and-classification"></a>命名と分類

便宜上、接続のリモート側の IP アドレスは RemoteIp プロパティに加えられています。 受信接続の場合、RemoteIp は SourceIp と同じですが、送信接続の場合は DestinationIp と同じです。 RemoteDnsCanonicalNames プロパティは、RemoteIp 向けにマシンにより報告される DNS 正規名を表します。 RemoteDnsQuestions プロパティは、RemoteIp のマシンによって報告された DNS の質問を表します。 RemoveClassification プロパティは、将来に使用するために予約されています。 

#### <a name="geolocation"></a>地理的位置情報

*VMConnection* では、レコードの次のプロパティに、各接続レコードのリモート エンドの地理的位置情報も加えられています。 

| プロパティ | 説明 |
|:--|:--|
|RemoteCountry |RemoteIp をホストしている国や地域の名前。  例: *United States* |
|RemoteLatitude |地理的位置情報の緯度。 例: *47.68* |
|RemoteLongitude |地理的位置情報の経度。 例: *-122.12* |

#### <a name="malicious-ip"></a>悪意のある IP

*VMConnection* テーブル内のすべての RemoteIp プロパティは、一連の IP に対して、知られている悪意のあるアクティビティがチェックされます。 RemoteIp が悪意のあると識別される場合、レコードの以下のプロパティに設定されます (IP が悪意のあるとみなされない場合、これらは空です)。

| プロパティ | 説明 |
|:--|:--|
|MaliciousIp |RemoteIp アドレス |
|IndicatorThreadType |検出される脅威のインジケーターは、*Botnet*、*C2*、*CryptoMining*、*Darknet*、*DDos*、*MaliciousUrl*、*Malware*、*Phishing*、*Proxy*、*PUA*、*Watchlist* のいずれかの値です。   |
|説明 |観察対象の脅威の説明。 |
|TLPLevel |Traffic Light Protocol (TLP) レベルは、定義済みの値、*White*、*Green*、*Amber*、*Red* のいずれかです。 |
|Confidence |値は "*0 から 100*" です。 |
|重大度 |値は "*0 から 5*" です。ここで、*5* は最も重大で、*0* はまったく重大ではありません。 既定値は *3* です。  |
|FirstReportedDateTime |プロバイダーが初めてインジケーターをレポートした時間。 |
|LastReportedDateTime |Interflow によってインジケーターが最後に表示された時間。 |
|IsActive |インジケーターが *True* または *False* の値で非アクティブ化されていることを示します。 |
|ReportReferenceLink |特定の観測可能な脅威に関連するレポートにリンクします。 |
|AdditionalInformation |該当する場合は、観察対象の脅威についての追加情報を提供します。 |

### <a name="ports"></a>Port 

受信トラフィックを積極的に受け入れるマシン、または潜在的にトラフィックを受け入れることができても報告期間中はアイドルであるマシン上のポートは、VMBoundPort テーブルに書き込まれます。  

VMBoundPort のすべてのレコードは、以下のフィールドで識別されます。 

| プロパティ | 説明 |
|:--|:--|
|Process | ポートが関連付けられているプロセス (または複数プロセスのグループ) の ID。|
|Ip | ポートの IP アドレス (IP はワイルドカード *0.0.0.0* で指定できます) |
|Port |ポート番号 |
|Protocol | プロトコル。  たとえば、*tcp* または *udp* です (現在サポートされているのは *tcp* のみです)。|
 
ポートの ID は上記の 5 つのフィールドから派生し、PortId プロパティに格納されます。 このプロパティを使用すると、時間の経過と共に特定のポートのレコードをすばやく見つけることができます。 

#### <a name="metrics"></a>メトリック 

ポート レコードには、それらに関連付けられている接続を表すメトリックが含まれています。 現在、以下のメトリックが報告されています (各メトリックの詳細については前のセクションを参照してください)。 

- BytesSent と BytesReceived 
- LinksEstablished、LinksTerminated、LinksLive 
- ResposeTime、ResponseTimeMin、ResponseTimeMax、ResponseTimeSum 

考慮すべき重要な点は次のとおりです。

- プロセスが同じ IP アドレスでも複数のネットワーク インターフェイスで接続を受け入れる場合、インターフェイスごとに別のレコードが報告されます。  
- ワイルドカード IP 付きレコードにはアクティビティはありません。 これらは、マシン上のポートが受信トラフィックにオープンであるという事実を表すために加えられています。 
- 冗長性とデータ量を減らすために、ワイルドカード IP 付きレコードは、特定の IP アドレスと一致するレコード (同じプロセス、ポート、プロトコル) がある場合は省略されます。 ワイルドカードの IP レコードが省略されると、特定の IP アドレスを持つレコードの *IsWildcardBind* プロパティは *True* に設定されます。  これは、ポートがレポート マシンのすべてのインターフェイスに公開されていることを示します。 
- 特定のインターフェイスにのみバインドされているポートでは、IsWildcardBind は *False* に設定されます。 

### <a name="vmcomputer-records"></a>VMComputer レコード

*VMComputer* 型があるレコードには、Dependency エージェントを有するサーバー用のインベントリ データがあります。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 |
|:--|:--|
|TenantId | ワークスペースの一意識別子 |
|SourceSystem | *分析情報* | 
|TimeGenerated | レコードのタイムスタンプ (UTC) |
|Computer | コンピューターの FQDN | 
|AgentId | Log Analytics エージェントの一意の ID |
|Machine | ServiceMap によって公開されているマシンの Azure Resource Manager リソースの名前。 これは *m-{GUID}* という形式です。この *GUID* は AgentId と同じ GUID です。 | 
|DisplayName | Display name | 
|FullDisplayName | 完全表示名 | 
|HostName | ドメイン名のないコンピューターの名前 |
|BootTime | コンピューターの起動時刻 (UTC) |
|TimeZone | 正規化されたタイム ゾーン |
|VirtualizationState | *仮想*、*ハイパーバイザー*、*物理* |
|Ipv4Addresses | IPv4 アドレスの配列 | 
|Ipv4SubnetMasks | IPv4 サブネット マスクの配列 (Ipv4Addresses と同じ順序)。 |
|Ipv4DefaultGateways | IPv4 ゲートウェイの配列 | 
|Ipv6Addresses | IPv6 アドレスの配列 | 
|MacAddresses | MAC アドレスの配列 | 
|DnsNames | コンピューターに関連付けられている DNS 名の配列。 |
|DependencyAgentVersion | コンピューター上で実行されている Dependency エージェントのバージョン。 | 
|OperatingSystemFamily | *Linux*、*Windows* |
|OperatingSystemFullName | オペレーティング システムのフル ネーム | 
|PhysicalMemoryMB | 物理メモリ (メガバイト) | 
|Cpus | プロセッサの数 | 
|CpuSpeed | CPU 速度 (MHz) | 
|VirtualMachineType | *hyperv*、*vmware*、*xen* |
|VirtualMachineNativeId | ハイパーバイザーによって割り当てられた VM ID | 
|VirtualMachineNativeName | VM の名前 |
|VirtualMachineHypervisorId | VM をホストしているハイパーバイザーの一意識別子 |
|HypervisorType | *hyperv* |
|HypervisorId | ハイパーバイザーの一意の ID | 
|HostingProvider | *azure* |
|_ResourceId | Azure リソースの一意識別子 |
|AzureSubscriptionId | サブスクリプションを識別するグローバル一意識別子 | 
|AzureResourceGroup | コンピューターが属する Azure リソース グループの名前。 |
|AzureResourceName | Azure リソースの名前 |
|AzureLocation | Azure リソースの場所 |
|AzureUpdateDomain | Azure 更新ドメインの名前 |
|AzureFaultDomain | Azure 障害ドメインの名前 |
|AzureVmId | Azure 仮想マシンの一意の ID |
|AzureSize | Azure VM のサイズ |
|AzureImagePublisher | Azure VM 発行元の名前 |
|AzureImageOffering | Azure VM オファーの種類の名前 | 
|AzureImageSku | Azure VM イメージの SKU | 
|AzureImageVersion | Azure VM イメージのバージョン | 
|AzureCloudServiceName | Azure クラウド サービスの名前 |
|AzureCloudServiceDeployment | クラウド サービスのデプロイ ID |
|AzureCloudServiceRoleName | クラウド サービス ロールの名前 |
|AzureCloudServiceRoleType | クラウド サービス ロールの種類: *worker* または *web* |
|AzureCloudServiceInstanceId | クラウド サービス ロールのインスタンス ID |
|AzureVmScaleSetName | 仮想マシン スケール セットの名前 |
|AzureVmScaleSetDeployment | 仮想マシン スケール セットのデプロイ ID |
|AzureVmScaleSetResourceId | 仮想マシン スケール セットのリソースの一意の識別子。|
|AzureVmScaleSetInstanceId | 仮想マシン スケール セットの一意の識別子 |
|AzureServiceFabricClusterId | Azure Service Fabric クラスターの一意の識別子 | 
|AzureServiceFabricClusterName | Azure Service Fabric クラスターの名前 |

### <a name="vmprocess-records"></a>VMProcess レコード

*VMProcess* 型があるレコードには、Dependency エージェントを有するサーバー上での TCP 接続プロセス用のインベントリ データがあります。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 |
|:--|:--|
|TenantId | ワークスペースの一意識別子 |
|SourceSystem | *分析情報* | 
|TimeGenerated | レコードのタイムスタンプ (UTC) |
|Computer | コンピューターの FQDN | 
|AgentId | Log Analytics エージェントの一意の ID |
|Machine | ServiceMap によって公開されているマシンの Azure Resource Manager リソースの名前。 これは *m-{GUID}* という形式です。この *GUID* は AgentId と同じ GUID です。 | 
|Process | Service Map プロセスの一意の識別子。 形式は *p-{GUID}* です。 
|ExecutableName | プロセスの実行可能ファイルの名前 | 
|DisplayName | プロセス表示名 |
|Role | プロセス ロール: *webserver*、*appServer*、*databaseServer*、*ldapServer*、*smbServer* |
|グループ | プロセス グループ名。 同じグループのプロセスは論理的に関連しています。たとえば、同じ製品またはシステム コンポーネントに属しています。 |
|StartTime | プロセス プールの開始時刻 |
|FirstPid | プロセス プール内の最初の PID |
|説明 | プロセスの説明 |
|CompanyName | 会社の名前 |
|InternalName | 内部名 |
|ProductName | 製品の名前 |
|ProductVersion | 製品のバージョン |
|FileVersion | ファイルのバージョン |
|ExecutablePath |実行可能ファイルのパス |
|CommandLine | コマンド ライン |
|WorkingDirectory | 作業ディレクトリ |
|サービス | プロセスが実行されているサービスの配列 |
|UserName | プロセスが実行されているアカウント |
|UserDomain | プロセスが実行されているドメイン |
|_ResourceId | ワークスペース内のプロセスに対する一意識別子 |


## <a name="sample-map-queries"></a>サンプルのマップのクエリ

### <a name="list-all-known-machines"></a>既知のコンピューターを一覧表示

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>VM が最後にいつ再起動したか

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>イメージ、場所、および SKU 別の Azure VM の概要

```kusto
VMComputer | where AzureLocation != "" | summarize by Computer, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>すべてのマネージド コンピューターの物理メモリ容量を一覧表示

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>コンピューター名、DNS、IP、OS を一覧表示

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>"sql" でコマンドラインのすべてのプロセスを検索

```kusto
VMProcess | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>リソース名でコンピューター (最新のレコード) を検索

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>IP アドレスでマシン (最新のレコード) を検索

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>指定のマシンにある既知のプロセスすべてを一覧表示

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>SQL Server を実行しているすべてのコンピューターを一覧表示

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "*sql*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>データセンター内にあるすべての製品バージョンの curl を一覧表示

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>CentOS を実行しているすべてのコンピューターのコンピューター グループを作成

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct Computer
```

### <a name="bytes-sent-and-received-trends"></a>送受信したバイトのトレンド

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>どの Azure VM が最大バイト数を送信しているか

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>リンクの状態のトレンド

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>接続エラーのトレンド

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>バインドされているポート

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>マシン全体の開かれているポート数

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>開いているポートの数でワークスペース内のプロセスにスコアを付ける

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>各ポートの集計動作

このクエリは、アクティビティごとにポートにスコアを付けるために使用できます。たとえば、最も送受信トラフィックが多いポート、最も接続が多いポートなどです。
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>マシンのグループからの送信接続を要約します

```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
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

## <a name="performance-records"></a>パフォーマンス レコード
*InsightsMetrics* 型のレコードには、仮想マシンのゲスト オペレーティング システムのパフォーマンス データが含まれています。 これらのレコードは、次の表に示したプロパティを持ちます。


| プロパティ | 説明 |
|:--|:--|
|TenantId | ワークスペースの一意識別子 |
|SourceSystem | *分析情報* | 
|TimeGenerated | 値が収集された時刻 (UTC) |
|Computer | コンピューターの FQDN | 
|出発地 | *vm.azm.ms* |
|名前空間 | パフォーマンス カウンターのカテゴリ | 
|名前 | パフォーマンス カウンターの名前 |
|Val | 収集される値 | 
|Tags | レコードに関する関連の詳細。 さまざまなレコードの種類で使用されるタグについては、次の表を参照してください。  |
|AgentId | コンピューターのエージェントごとの一意の識別子 |
|Type | *InsightsMetrics* |
|_ResourceId_ | 仮想マシンのリソース ID |

現在 *InsightsMetrics* テーブルに収集されているパフォーマンス カウンターを次の表に示します。

| 名前空間 | 名前 | 説明 | ユニット | Tags |
|:---|:---|:---|:---|:---|
| Computer    | Heartbeat             | コンピューターのハートビート                        | | |
| メモリ      | AvailableMB           | 使用可能なメモリ (バイト)                    | メガバイト      | memorySizeMB - 合計メモリ サイズ|
| ネットワーク     | WriteBytesPerSecond   | ネットワーク書き込みバイト/秒            | BytesPerSecond | NetworkDeviceId - デバイスの ID<br>bytes - 合計送信バイト数 |
| ネットワーク     | ReadBytesPerSecond    | ネットワーク読み取りバイト/秒             | BytesPerSecond | networkDeviceId - デバイスの ID<br>bytes - 合計受信バイト数 |
| プロセッサ   | UtilizationPercentage | プロセッサ使用率          | Percent        | totalCpus-CPU 合計 |
| LogicalDisk | WritesPerSecond       | 論理ディスクの書き込み秒数            | CountPerSecond | mountId - デバイスのマウント ID |
| LogicalDisk | WriteLatencyMs        | 論理ディスクの書き込み遅延 (ミリ秒)    | MilliSeconds   | mountId - デバイスのマウント ID |
| LogicalDisk | WriteBytesPerSecond   | 1 秒あたりの論理ディスク書き込みバイト数       | BytesPerSecond | mountId - デバイスのマウント ID |
| LogicalDisk | TransfersPerSecond    | 論理ディスクの転送秒数         | CountPerSecond | mountId - デバイスのマウント ID |
| LogicalDisk | TransferLatencyMs     | 論理ディスクの転送遅延 (ミリ秒) | MilliSeconds   | mountId - デバイスのマウント ID |
| LogicalDisk | ReadsPerSecond        | 論理ディスクの 1 秒あたりの読み取り回数             | CountPerSecond | mountId - デバイスのマウント ID |
| LogicalDisk | ReadLatencyMs         | 論理ディスクの読み取り遅延 (ミリ秒)     | MilliSeconds   | mountId - デバイスのマウント ID |
| LogicalDisk | ReadBytesPerSecond    | 1 秒あたりの論理ディスク読み取りバイト数        | BytesPerSecond | mountId - デバイスのマウント ID |
| LogicalDisk | FreeSpacePercentage   | 論理ディスクの空き領域比率        | Percent        | mountId - デバイスのマウント ID |
| LogicalDisk | FreeSpaceMB           | 論理ディスクの空き領域 (バイト)             | メガバイト      | mountId - デバイスのマウント ID<br>diskSizeMB - 合計ディスク サイズ |
| LogicalDisk | BytesPerSecond        | 1 秒間の論理ディスクのバイト数             | BytesPerSecond | mountId - デバイスのマウント ID |


## <a name="next-steps"></a>次のステップ

* Azure Monitor でログ クエリを初めて作成する場合は、Azure portal で [Log Analytics の使用方法](../logs/log-analytics-tutorial.md)に関するページを参照してログ クエリを作成してください。

* [検索クエリの記述方法](../logs/get-started-queries.md)を参照してください。