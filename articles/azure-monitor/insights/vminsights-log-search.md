---
title: Azure Monitor for VMs (プレビュー) からログを照会する方法 | Microsoft Docs
description: Azure Monitor for VMs ソリューションは、メトリックとログ データを収集します。この記事では、レコードについて説明し、サンプル クエリを紹介します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2019
ms.author: magoedte
ms.openlocfilehash: 8b6745a2b9afe8d3101585e3f7a13f2fc978c84a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59492090"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>Azure Monitor for VMs (プレビュー) からログを照会する方法
VM 用 Azure Monitor は、パフォーマンスと接続のメトリック、コンピューターとプロセスのインベントリ データ、および正常性状態の情報を収集し、Azure Monitor 内の Log Analytics ワークスペースにこれらを転送します。  このデータは、Azure Monitor で[クエリ](../../azure-monitor/log-query/log-query-overview.md)用に使用できます。 このデータは、移行計画、容量の分析、探索、必要に応じたパフォーマンスのトラブルシューティングといったシナリオに適用できます。

## <a name="map-records"></a>Map レコード
プロセスまたはコンピューターが起動するとき、または VM 用 Azure Monitor の Map 機能に搭載されている場合に生成されるレコードのほかに、個々のコンピューターとプロセスごとに、1 時間に 1 つのレコードが生成されます。 これらのレコードは、次の表に示したプロパティを持ちます。 ServiceMapComputer_CL イベントのフィールドと値は、ServiceMap Azure Resource Manager API のマシン リソースのフィールドにマップされます。 ServiceMapProcess_CL イベントのフィールドと値は、ServiceMap Azure Resource Manager API のプロセス リソースのフィールドにマップされます。 ResourceName_s フィールドは、対応する Resource Manager リソースの名前フィールドと一致します。 

個々のプロセスとコンピューターの識別に使用できる、内部生成されたプロパティがあります。

- コンピューター:*ResourceId* または *ResourceName_s* を使用して、Log Analytics ワークスペース内のコンピューターを一意に識別します。
- プロセス:*ResourceId* を使用して、Log Analytics ワークスペース内のプロセスを一意に識別します。 *ResourceName_s* は、プロセスが実行中のマシンのコンテキスト内で一意です (MachineResourceName_s) 

指定の時間範囲にある指定のプロセスとコンピューターについては、複数のレコードが存在できるため、クエリは、同じコンピューターまたはプロセスに対して複数のレコードを返すことがあります。 最新のレコードのみが返されるようにするには、"| dedup ResourceId" をクエリに追加します。

### <a name="connections-and-ports"></a>接続とポート
接続メトリック機能により、Azure Monitor ログに 2 つの新しいテーブル (VMConnection と VMBoundPort) が導入されました。 これらのテーブルは、マシンへの接続 (受信と送信) と、それらに対する開いているまたはアクティブなサーバー ポートに関する情報を提供します。 接続メトリックは、時間枠の間に特定のメトリックを取得する手段を提供する API を介して公開されています。 リスニング ソケットで*受諾*することで得られる TCP 接続は受信ですが、所定の IP とポートに*接続*することで作成される接続は送信です。 接続の方向は Direction プロパティで表され、**受信**または**送信**のいずれかに設定できます。 

これらのテーブル内のレコードは、Dependency Agent によって報告されるデータから生成されます。 いずれの記録も、1 分の時間間隔での観測を表します。 TimeGenerated プロパティは、時間間隔の開始を示します。 各レコードには、エンティティに関連付けられたメトリックに加えて、接続またはポートなど、それぞれのエンティティを識別する情報が含まれています。 現在のところ、TCP over IPv4 を使用することで発生するネットワーク アクティビティのみが報告されます。 

#### <a name="common-fields-and-conventions"></a>共通するフィールドと規則 
次のフィールドと規則は、VMConnection と VMBoundPort の両方に適用されます。 

- コンピューター:レポート マシンの完全修飾ドメイン名 
- AgentID:Log Analytics エージェントがインストールされているマシンの一意識別子  
- マシン:ServiceMap によって公開されているマシンの Azure Resource Manager リソースの名前。 これは *m-{GUID}* という形式です。この *GUID* は AgentID と同じ GUID です。  
- プロセス:ServiceMap によって公開されているプロセスの Azure Resource Manager リソースの名前。 これは *p-{hex string}* という形式です。 プロセスはマシンのスコープ内で一意です。また、マシン全体で一意のプロセス ID を生成するには、Machine フィールドと Process フィールドを結合します。 
- ProcessName:レポート プロセスの実行可能ファイルの名前
- すべての IP アドレスは、IPv4 正規形式の文字列です (例: *13.107.3.160*)。 

コストと複雑さを管理するため、接続レコードは個々の物理ネットワーク接続を表すものではありません。 複数の物理ネットワーク接続は、論理接続にグループ化され、その後それぞれのテーブルに反映されます。  つまり、*VMConnection* テーブル内のレコードは、論理グルーピングを表しており、観察されている個々の物理接続を表していません。 所定の 1 分間隔で次の属性に同じ値を共有する物理ネットワーク接続は、*VMConnection* 内で 1 つの論理レコードに集約されます。 

| プロパティ | Description |
|:--|:--|
|Direction |接続の方向であり、値は*受信*または*送信*です |
|Machine |コンピューターの FQDN |
|Process |プロセスまたはプロセスのグループの ID、接続の開始/受諾 |
|SourceIp |送信元の IP アドレス |
|DestinationIp |送信先の IP アドレス |
|DestinationPort |送信先のポート番号 |
|Protocol |接続に使用されるプロトコル。  値は *tcp* です。 |

グループ化の影響を考慮するため、グループ化された物理接続の数に関する情報は、レコードの次のプロパティに提示されます。

| プロパティ | Description |
|:--|:--|
|LinksEstablished |報告時間枠の間に確立された物理ネットワーク接続の数 |
|LinksTerminated |報告時間枠の間に切断された物理ネットワーク接続の数 |
|LinksFailed |報告時間枠の間に失敗した物理ネットワーク接続の数。 現在のところ、この情報は送信接続に対してのみ使用できます。 |
|LinksLive |報告時間枠の終了時点で開いていた物理ネットワーク接続の数|

#### <a name="metrics"></a>メトリック

接続数メトリックに加えて、所定の論理接続またはネット ワークポートで送受信されるデータ量に関する情報も、レコードの次のプロパティに加えられています。

| プロパティ | Description |
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
便宜上、接続のリモート側の IP アドレスは RemoteIp プロパティに加えられています。 受信接続の場合、RemoteIp は SourceIp と同じですが、送信接続の場合は DestinationIp と同じです。 RemoteDnsCanonicalNames プロパティは、RemoteIp 向けにマシンにより報告される DNS 正規名を表します。 RemoteDnsQuestions プロパティと RemoteClassification プロパティは、将来の使用のために予約されています。 

#### <a name="geolocation"></a>地理的位置情報
*VMConnection*では、レコードの次のプロパティに、各接続レコードのリモート エンドの地理的位置情報も加えられています。 

| プロパティ | Description |
|:--|:--|
|RemoteCountry |RemoteIp をホストしている国の名前。  例: *United States* |
|RemoteLatitude |地理的位置情報の緯度。 例: *47.68* |
|RemoteLongitude |地理的位置情報の経度。 例: *-122.12* |

#### <a name="malicious-ip"></a>悪意のある IP
*VMConnection* テーブル内のすべての RemoteIp プロパティは、一連の IP に対して、知られている悪意のあるアクティビティがチェックされます。 RemoteIp が悪意のあると識別される場合、レコードの以下のプロパティに設定されます (IP が悪意のあるとみなされない場合、これらは空です)。

| プロパティ | Description |
|:--|:--|
|MaliciousIp |RemoteIp アドレス |
|IndicatorThreadType |検出される脅威のインジケーターは、*Botnet*、*C2*、*CryptoMining*、*Darknet*、*DDos*、*MaliciousUrl*、*Malware*、*Phishing*、*Proxy*、*PUA*、*Watchlist* のいずれかの値です。   |
|Description |観察対象の脅威の説明。 |
|TLPLevel |Traffic Light Protocol (TLP) レベルは、定義済みの値、*White*、*Green*、*Amber*、*Red* のいずれかです。 |
|Confidence |値は "*0 から 100*" です。 |
|Severity |値は "*0 から 5*" です。ここで、*5* は最も重大で、*0* はまったく重大ではありません。 既定値は *3* です。  |
|FirstReportedDateTime |プロバイダーが初めてインジケーターをレポートした時間。 |
|LastReportedDateTime |Interflow によってインジケーターが最後に表示された時間。 |
|IsActive |インジケーターが *True* または *False* の値で非アクティブ化されていることを示します。 |
|ReportReferenceLink |特定の観測可能な脅威に関連するレポートにリンクします。 |
|AdditionalInformation |該当する場合は、観察対象の脅威についての追加情報を提供します。 |

### <a name="ports"></a>Port 
受信トラフィックを積極的に受け入れるマシン、または潜在的にトラフィックを受け入れることができても報告期間中はアイドルであるマシン上のポートは、VMBoundPort テーブルに書き込まれます。  

>[!NOTE]
>Azure Monitor for VMs では、次のリージョンの Log Analytics ワークスペースでのポート データの収集と記録をサポートしていません。  
>- 米国東部  
>- 西ヨーロッパ
>
> このデータの収集は、Azure Monitor for VMs の他の[サポートされているリージョン](vminsights-onboard.md#log-analytics)では有効になっています。 

VMBoundPort のすべてのレコードは、以下のフィールドで識別されます。 

| プロパティ | Description |
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

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL レコード
*ServiceMapComputer_CL* 型があるレコードには、Dependency エージェントを有するサーバー用のインベントリ データがあります。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | Description |
|:--|:--|
| Type | *ServiceMapComputer_CL* |
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
*ServiceMapProcess_CL* 型があるレコードには、Dependency エージェントを有するサーバー上での TCP 接続プロセス用のインベントリ データがあります。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | Description |
|:--|:--|
| Type | *ServiceMapProcess_CL* |
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
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="when-was-the-vm-last-rebooted"></a>VM が最後にいつ再起動したか
```kusto
let Today = now(); ServiceMapComputer_CL | extend DaysSinceBoot = Today - BootTime_t | summarize by Computer, DaysSinceBoot, BootTime_t | sort by BootTime_t asc`
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>イメージ、場所、および SKU 別の Azure VM の概要
```kusto
ServiceMapComputer_CL | where AzureLocation_s != "" | summarize by ComputerName_s, AzureImageOffering_s, AzureLocation_s, AzureImageSku_s`
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>すべてのマネージド コンピューターの物理メモリ容量を一覧表示。
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`
```

### <a name="list-computer-name-dns-ip-and-os"></a>コンピューター名、DNS、IP、および OS を一覧表示。
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>"sql" でコマンドラインのすべてのプロセスを検索
```kusto
ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>リソース名でコンピューター (最新のレコード) を検索
```kusto
search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>IP アドレスでマシン (最新のレコード) を検索
```kusto
search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>指定のマシンにある既知のプロセスすべてを一覧表示
```kusto
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-computers-running-sql-server"></a>SQL Server を実行しているすべてのコンピューターを一覧表示
```kusto
ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>データセンター内にあるすべての製品バージョンの curl を一覧表示
```kusto
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>CentOS を実行しているすべてのコンピューターのコンピューター グループを作成
```kusto
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`
```

### <a name="bytes-sent-and-received-trends"></a>送受信したバイトのトレンド
```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart`
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>どの Azure VM が最大バイト数を送信しているか
```kusto
VMConnection | join kind=fullouter(ServiceMapComputer_CL) on $left.Computer == $right.ComputerName_s | summarize count(BytesSent) by Computer, AzureVMSize_s | sort by count_BytesSent desc`
```

### <a name="link-status-trends"></a>リンクの状態のトレンド
```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart`
```

### <a name="connection-failures-trend"></a>接続エラーのトレンド
```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart`
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

## <a name="next-steps"></a>次の手順
* Azure Monitor でログ クエリを初めて作成する場合は、Azure portal で [Log Analytics の使用方法](../../azure-monitor/log-query/get-started-portal.md)に関するページを参照してログ クエリを作成してください。
* [検索クエリの記述方法](../../azure-monitor/log-query/search-queries.md)を参照してください。
