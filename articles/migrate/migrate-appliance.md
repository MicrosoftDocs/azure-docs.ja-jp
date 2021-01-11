---
title: Azure Migrate アプライアンス
description: サーバーの評価と移行に使用される Azure Migrate アプライアンスの概要について説明します。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: a313d7b964e6ea849acb5b034ed55975b8c5b524
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927513"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate アプライアンス

この記事では、Azure Migrate アプライアンスの前提条件とサポート要件の概要を説明します。 

## <a name="deployment-scenarios"></a>デプロイメント シナリオ

Azure Migrate アプライアンスは、次のシナリオで使用します。

**シナリオ** | **ツール** | **用途** 
--- | --- | ---
**VMware VM の評価** | Azure Migrate:Server Assessment | VMware VM を検出する<br/><br/> マシンのアプリと依存関係を検出する<br/><br/> 評価のためにマシン メタデータとパフォーマンス メタデータを収集する。
**VMware VM のエージェントレス移行** | Azure Migrate:Server Migration | VMware VM を検出する <br/><br/> エージェントレス移行で VMware VM を移行する。
**Hyper-V VM の評価** | Azure Migrate:Server Assessment | Hyper-V VM を検出する<br/><br/> 評価のためにマシン メタデータとパフォーマンス メタデータを収集する。
**物理マシンの評価** |  Azure Migrate:Server Assessment |  物理サーバー (または物理サーバーとして扱う VM) を検出します。<br/><br/> 評価のためにマシン メタデータとパフォーマンス メタデータを収集する。

## <a name="deployment-methods"></a>デプロイ方法

このアプライアンスは、次のいくつかの方法を使用してデプロイできます。

- アプライアンスは、VMware VM と Hyper-V VM 用のテンプレート (VMware の場合は OVA テンプレート、Hyper-V の場合は VHD) を使用してデプロイできます。
- テンプレートを使用しない場合は、PowerShell スクリプトを使用して VMware または Hyper-V 用のアプライアンスをデプロイできます。
- Azure Government では、スクリプトを使用してアプライアンスをデプロイする必要があります。
- 物理サーバーの場合は、必ずスクリプトを使用してアプライアンスをデプロイします。
- ダウンロード リンクは、以下の表にあります。


## <a name="appliance---vmware"></a>アプライアンス - VMware 

次の表は、VMware の Azure Migrate アプライアンス要件をまとめたものです。

**要件** | **VMware** 
--- | ---
**アクセス許可** | アプライアンス Web アプリにローカルまたはリモートでアクセスするには、ドメイン管理者であるか、またはアプライアンス マシンのローカル管理者である必要があります。
**アプライアンスのコンポーネント** | アプライアンスには、次のコンポーネントがあります。<br/><br/> - **管理アプリ**:これは、アプライアンスのデプロイ時にユーザーが入力する Web アプリです。 Azure への移行用にマシンを評価するときに使用されます。<br/> - **検出エージェント**:このエージェントは、マシン構成データを収集します。 Azure への移行用にマシンを評価するときに使用されます。<br/>- **コレクター エージェント**:このエージェントはパフォーマンス データを収集します。 Azure への移行用にマシンを評価するときに使用されます。<br/>- **自動更新サービス**:アプライアンス コンポーネントを更新します (24 時間ごとに実行されます)。<br/>- **DRA エージェント**:VM のレプリケーションを調整し、レプリケートされたマシンと Azure 間の通信を調整します。 エージェントレスの移行を使用して VMware VM を Azure にレプリケートする場合にのみ使用されます。<br/>- **ゲートウェイ**:レプリケートされたデータを Azure に送信します。 エージェントレスの移行を使用して VMware VM を Azure にレプリケートする場合にのみ使用されます。
**サポートされるデプロイ** | OVA テンプレートを使用して VMware VM としてデプロイする。<br/><br/> PowerShell インストール スクリプトを使用して、VMware VM または物理マシンとしてデプロイする。
**プロジェクトのサポート** |  単一のプロジェクトにアプライアンスを関連付けることができます。 <br/> 任意の数のアプライアンスを 1 つのプロジェクトに関連付けることができます。<br/> 
**検出の制限** | 1 つのアプライアンスで、vCenter Server 上の VMware VM を最大 10,000 台検出できます。<br/> 1 つのアプライアンスは、1 つの vCenter Server に接続できます。
**OVA テンプレート** | ポータルから、または[こちら](https://go.microsoft.com/fwlink/?linkid=2140333)からダウンロードします<br/><br/> ダウンロード サイズは 11.6 GB です。<br/><br/> ダウンロードしたアプライアンス テンプレートには、180 日間有効な Windows Server 2016 評価版ライセンスが付属します。 評価期間が期限切れ間近の場合は、新しいアプライアンスをダウンロードしてデプロイするか、アプライアンス VM のオペレーティング システムのライセンスをアクティブ化することをお勧めします。
**PowerShell スクリプト** | この[記事](./deploy-appliance-script.md#set-up-the-appliance-for-vmware)を参照してください。<br/><br/> 
**ソフトウェア/ハードウェア** |  アプライアンスは、Windows Server 2016、32 GB の RAM、8 つの vCPU、約 80 GB のディスク記憶域、外部仮想スイッチを搭載したコンピューターで実行する必要があります。<br/> アプライアンスは、直接またはプロキシを介してインターネットにアクセスできる必要があります。<br/><br/> アプライアンスを VMware VM で実行する場合、vCenter Server に、要件を満たす VM を割り当てるのに十分なリソースが必要です。<br/><br/> 物理マシンでアプライアンスを実行する場合は、Windows Server 2016 を実行しており、ハードウェアの要件を満たしていることを確認します。
**VMware の要件** | アプライアンスを VMware VM としてデプロイする場合は、バージョン 5.5 以降を実行している ESXi ホストにデプロイする必要があります。<br/><br/> 5\.5、6.0、6.5、または 6.7 を実行する vCenter Server。
**VDDK (エージェントレス移行)** | アプライアンスを VMware VM としてデプロイし、エージェントレスの移行を実行している場合は、VMware vSphere VDDK をアプライアンス VM にインストールする必要があります。
**ハッシュ値 - OVA** | OVA テンプレートのハッシュ値を[確認](tutorial-assess-vmware.md#verify-security)します。
**ハッシュ値 - PowerShell スクリプト** | PowerShell スクリプトのハッシュ値を[確認](deploy-appliance-script.md#verify-file-security)します。




## <a name="appliance---hyper-v"></a>アプライアンス - Hyper-V

**要件** | **Hyper-V** 
--- | ---
**アクセス許可** | アプライアンス Web アプリにローカルまたはリモートでアクセスするには、ドメイン管理者であるか、またはアプライアンス マシンのローカル管理者である必要があります。
**アプライアンスのコンポーネント** | アプライアンスには、次のコンポーネントがあります。<br/><br/>- **管理アプリ**:これは、アプライアンスのデプロイ時にユーザーが入力する Web アプリです。 Azure への移行用にマシンを評価するときに使用されます。<br/> - **検出エージェント**:このエージェントは、マシン構成データを収集します。 Azure への移行用にマシンを評価するときに使用されます。<br/>- **コレクター エージェント**:このエージェントはパフォーマンス データを収集します。 Azure への移行用にマシンを評価するときに使用されます。<br/>- **自動更新サービス**:アプライアンス コンポーネントを更新します (24 時間ごとに実行されます)。
**サポートされるデプロイ** | VHD テンプレートを使用して Hyper-V VM としてデプロイする。<br/><br/> PowerShell インストール スクリプトを使用して、Hyper-V VM または物理マシンとしてデプロイする。
**プロジェクトのサポート** |  単一のプロジェクトにアプライアンスを関連付けることができます。 <br/> 任意の数のアプライアンスを 1 つのプロジェクトに関連付けることができます。<br/> 
**検出の制限** | アプライアンスは、最大 5000 個の Hyper-V ホストを検出できます。<br/> アプライアンスは、最大 300 個の Hyper-V ホストに接続できます。
**VHD テンプレート** | VHD を含む zip 形式のフォルダー。 ポータルから、または[こちら](https://go.microsoft.com/fwlink/?linkid=2140422)からダウンロードします。<br/><br/> ダウンロード サイズは 10.4 GB です。<br/><br/> ダウンロードしたアプライアンス テンプレートには、180 日間有効な Windows Server 2016 評価版ライセンスが付属します。 評価期間が期限切れ間近の場合は、新しいアプライアンスをダウンロードしてデプロイするか、アプライアンス VM のオペレーティング システムのライセンスをアクティブ化することをお勧めします。
**PowerShell スクリプト** | この[記事](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v)を参照してください。<br/><br/> 
**ソフトウェア/ハードウェア***   |  アプライアンスは、Windows Server 2016、16 GB の RAM、8 つの vCPU、約 80 GB のディスク記憶域、外部仮想スイッチを搭載したコンピューターで実行する必要があります。<br/> アプライアンスには、静的または動的 IP アドレスが必要であり、直接またはプロキシを介したインターネット アクセスが必要です。<br/><br/> アプライアンスを Hyper-V VM として実行する場合、Hyper-V ホスト上に、アプライアンス VM 用の 16 GB の RAM、8 つの vCPU、約 80 GB の記憶域スペース、外部スイッチを割り当てるための十分なリソースが必要です。<br/><br/> 物理マシンでアプライアンスを実行する場合は、Windows Server 2016 を実行しており、ハードウェアの要件を満たしていることを確認します。 
**Hyper-V の要件** | VHD テンプレートを使用してアプライアンスをデプロイする場合、Azure Migrate によって提供されるアプライアンス VM は、Hyper-V VM バージョン 5.0 です。<br/><br/> Hyper-V ホストで Windows Server 2012 R2 以降が実行されている必要があります。 
**ハッシュ値 - VHD** | VHD テンプレートのハッシュ値を[確認](tutorial-assess-hyper-v.md#verify-security)します。
**ハッシュ値 - PowerShell スクリプト** | PowerShell スクリプトのハッシュ値を[確認](deploy-appliance-script.md#verify-file-security)します。


## <a name="appliance---physical"></a>アプライアンス - 物理

**要件** | **物理** 
--- | ---
**アクセス許可** | アプライアンス Web アプリにローカルまたはリモートでアクセスするには、ドメイン管理者であるか、またはアプライアンス マシンのローカル管理者である必要があります。
**アプライアンスのコンポーネント** | アプライアンスには、次のコンポーネントがあります。 <br/><br/> - **管理アプリ**:これは、アプライアンスのデプロイ時にユーザーが入力する Web アプリです。 Azure への移行用にマシンを評価するときに使用されます。<br/> - **検出エージェント**:このエージェントは、マシン構成データを収集します。 Azure への移行用にマシンを評価するときに使用されます。<br/>- **コレクター エージェント**:このエージェントはパフォーマンス データを収集します。 Azure への移行用にマシンを評価するときに使用されます。<br/>- **自動更新サービス**:アプライアンス コンポーネントを更新します (24 時間ごとに実行されます)。
**サポートされるデプロイ** | PowerShell インストール スクリプトを使用して、専用物理マシンまたは VM としてデプロイする。 スクリプトはポータルからダウンロードできます。
**プロジェクトのサポート** |  単一のプロジェクトにアプライアンスを関連付けることができます。 <br/> 任意の数のアプライアンスを 1 つのプロジェクトに関連付けることができます。<br/> 
**検出の制限** | 1 つのアプライアンスで最大 1000 台の物理サーバーを検出できます。
**PowerShell スクリプト** | ポータルから、または[こちら](https://go.microsoft.com/fwlink/?linkid=2140334)から zip 形式のフォルダーでスクリプト (AzureMigrateInstaller.ps1) をダウンロードします。 [詳細については、こちらを参照してください](tutorial-assess-physical.md#set-up-the-azure-migrate-appliance)。<br/><br/> ダウンロード サイズは 85 MB です。
**ソフトウェア/ハードウェア** |  アプライアンスは、Windows Server 2016、16 GB の RAM、8 つの vCPU、約 80 GB のディスク記憶域、外部仮想スイッチを搭載したコンピューターで実行する必要があります。<br/> アプライアンスには、静的または動的 IP アドレスが必要であり、直接またはプロキシを介したインターネット アクセスが必要です。<br/><br/> 物理マシンでアプライアンスを実行する場合は、Windows Server 2016 を実行しており、ハードウェアの要件を満たしていることを確認します。<br/> Windows Server 2019 を搭載したコンピューターでのアプライアンスの実行はサポートされていません。
**ハッシュ値** | PowerShell スクリプトのハッシュ値を[確認](deploy-appliance-script.md#verify-file-security)します。

## <a name="url-access"></a>URL アクセス

Azure Migrate アプライアンスには、インターネットへの接続が必要です。

- アプライアンスをデプロイすると、必要な URL への接続チェックが Azure Migrate によって行われます。
- リスト内のすべての URL へのアクセスを許可する必要があります。 評価のみ行う場合は、VMware エージェントレスの移行でのみ必須とマークされている URL をスキップできます。
-  URL ベースのプロキシを使用してインターネットに接続している場合は、URL の探索中に受信されたすべての CNAME レコードがプロキシによって解決されるようにします。

### <a name="public-cloud-urls"></a>パブリック クラウドの URL

**URL** | **詳細**  
--- | --- |
*.portal.azure.com  | Azure Portal に移動します。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Azure サブスクリプションにサインインします。
*.microsoftonline.com <br/> *.microsoftonline-p.com | アプライアンスで Azure Migrate と通信するための Azure Active Directory (AD) アプリを作成します。
management.azure.com | アプライアンスで Azure Migrate サービスと通信するための Azure AD アプリを作成します。
*.services.visualstudio.com | 内部監視に使用するアプリ ログをアップロードします。
*.vault.azure.net | Azure Key Vault でシークレットを管理します。 注:レプリケートするマシンに、ここへのアクセス権があることを確認してください。
aka.ms/* | aka リンクへのアクセスを許可します。 Azure Migrate アプライアンスの更新に使用されます。
download.microsoft.com/download | Microsoft ダウンロードからのダウンロードを許可します。
*.servicebus.windows.net | アプライアンスと Azure Migrate サービスの間の通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Azure Migrate サービスの URL に接続します。
*.hypervrecoverymanager.windowsazure.com | **VMware のエージェントレス移行のために使用**<br/><br/> Azure Migrate サービスの URL に接続します。
*.blob.core.windows.net |  **VMware のエージェントレス移行のために使用**<br/><br/>移行のためにストレージにデータをアップロードします。

### <a name="government-cloud-urls"></a>Government クラウドの URL

**URL** | **詳細**  
--- | --- |
*.portal.azure.us  | Azure Portal に移動します。
graph.windows.net | Azure サブスクリプションにサインインします。
login.microsoftonline.us  | アプライアンスで Azure Migrate と通信するための Azure Active Directory (AD) アプリを作成します。
management.usgovcloudapi.net | アプライアンスで Azure Migrate サービスと通信するための Azure AD アプリを作成します。
dc.services.visualstudio.com | 内部監視に使用するアプリ ログをアップロードします。
*.vault.usgovcloudapi.net | Azure Key Vault でシークレットを管理します。
aka.ms/* | aka リンクへのアクセスを許可します。 Azure Migrate アプライアンスの更新に使用されます。
download.microsoft.com/download | Microsoft ダウンロードからのダウンロードを許可します。
*.servicebus.usgovcloudapi.net  | アプライアンスと Azure Migrate サービスの間の通信。
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Azure Migrate サービスの URL に接続します。
*.hypervrecoverymanager.windowsazure.us | **VMware のエージェントレス移行のために使用**<br/><br/> Azure Migrate サービスの URL に接続します。
*.blob.core.usgovcloudapi.net  |  **VMware のエージェントレス移行のために使用**<br/><br/>移行のためにストレージにデータをアップロードします。
*.applicationinsights.us | 内部監視に使用するアプリ ログをアップロードします。





## <a name="collected-data---vmware"></a>収集されるデータ - VMware

アプライアンスでは、メタ―データ、パフォーマンス データ、依存関係分析データ (エージェントレスの[依存関係分析](concepts-dependency-visualization.md)が使用されている場合) が収集されます。

### <a name="metadata"></a>Metadata

Azure Migrate アプライアンスによって検出されたメタデータは、コンピューターとアプリを Azure に移行する準備ができているかどうかを判断し、コンピューターとアプリを適切にサイズ設定して、コストを計画し、アプリケーションの依存関係を分析するために役立ちます。 Microsoft では、ライセンスのコンプライアンス監査でこのデータを使用しません。

アプライアンスが収集して Azure に送信する VMware VM のメタデータの全一覧を次に示します。

**データ** | **カウンター**
--- | --- 
**マシンの詳細** | 
VM ID | vm.Config.InstanceUuid 
VM 名 | vm.Config.Name
vCenter Server ID | VMwareClient.Instance.Uuid
VM の説明 | vm.Summary.Config.Annotation
ライセンス プロダクト名 | vm.Client.ServiceContent.About.LicenseProductName
オペレーティング システムの種類 | vm.SummaryConfig.GuestFullName
ブートの種類 | vm.Config.Firmware
コア数 | vm.Config.Hardware.NumCPU
メモリ (MB) | vm.Config.Hardware.MemoryMB
ディスクの数 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
ディスク サイズのリスト | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
ネットワーク アダプターのリスト | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
CPU 使用率 | cpu.usage.average
メモリ使用率 |mem.usage.average
**各ディスクの詳細** | 
ディスク キーの値 | disk.Key
Dikunit 番号 | disk.UnitNumber
ディスク コントローラー キーの値 | disk.ControllerKey.Value
プロビジョニングされたギガバイト数 | virtualDisk.DeviceInfo.Summary
ディスク名 | disk.UnitNumber、disk.Key、disk.ControllerKey.VAlue を使用して生成される値
1 秒あたりの読み取り操作 | virtualDisk.numberReadAveraged.average
1 秒あたりの書き込み操作 | virtualDisk.numberWriteAveraged.average
読み取りのスループット (MB/秒) | virtualDisk.read.average
書き込みのスループット (MB/秒) | virtualDisk.write.average
**各 NIC の詳細** | 
ネットワーク アダプターの名前 | nic.Key
MAC アドレス | ((VirtualEthernetCard)nic).MacAddress
IPv4 アドレス | vm.Guest.Net
IPv6 アドレス | vm.Guest.Net
読み取りのスループット (MB/秒) | net.received.average
書き込みのスループット (MB/秒) | net.transmitted.average
**インベントリ パスの詳細** | 
名前 | container.GetType().Name
子オブジェクトの型 | container.ChildType
参照の詳細 | container.MoRef
親の詳細 | Container.Parent
各 VM のフォルダーの詳細 | ((Folder)container).ChildEntity.Type
各 VM のデータセンターの詳細 | ((Datacenter)container).VmFolder
各ホスト フォルダーのデータセンターの詳細 | ((Datacenter)container).HostFolder
各ホストのクラスターの詳細 | ((ClusterComputeResource)container).Host
各 VM のホストの詳細 | ((HostSystem)container).VM

### <a name="performance-data"></a>パフォーマンス データ


アプライアンスが収集して Azure に送信する VMware VM のパフォーマンス データを次に示します。

**データ** | **カウンター** | **評価の影響**
--- | --- | ---
CPU 使用率 | cpu.usage.average | 推奨される VM サイズ/コスト
メモリ使用率 | mem.usage.average | 推奨される VM サイズ/コスト
ディスク読み取りのスループット (MB/秒) | virtualDisk.read.average | ディスク サイズ、ストレージ コスト、VM サイズの計算
ディスク書き込みのスループット (MB/秒) | virtualDisk.write.average | ディスク サイズ、ストレージ コスト、VM サイズの計算
1 秒あたりのディスク読み取り操作 | virtualDisk.numberReadAveraged.average | ディスク サイズ、ストレージ コスト、VM サイズの計算
1 秒あたりのディスク書き込み操作 | virtualDisk.numberWriteAveraged.average  | ディスク サイズ、ストレージ コスト、VM サイズの計算
NIC 読み取りのスループット (MB/秒) | net.received.average | VM サイズの計算
NIC 書き込みのスループット (MB/秒) | net.transmitted.average  |VM サイズの計算


### <a name="installed-apps-metadata"></a>インストール済みアプリのメタデータ

アプリケーションの検出は、インストール済みのアプリケーションとオペレーティング システム データを収集します。

#### <a name="windows-vm-apps-data"></a>Windows VM のアプリケーション データ

アプリケーションの検出が有効になっている各 VM から、アプライアンスが収集するインストール済みのアプリケーション データを次に示します。 このデータは Azure に送信されます。

**データ** | **レジストリの場所** | **キー**
--- | --- | ---
アプリケーション名  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Version  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion 
プロバイダー  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-vm-features-data"></a>Windows VM の特徴量データ

アプリケーションの検出が有効になっている各 VM から、アプライアンスが収集する特徴量データを次に示します。 このデータは Azure に送信されます。

**データ**  | **PowerShell コマンドレット** | **プロパティ**
--- | --- | ---
名前  | Get-WindowsFeature  | 名前
特徴の種類 | Get-WindowsFeature  | FeatureType
Parent  | Get-WindowsFeature  | Parent

#### <a name="windows-vm-sql-server-metadata"></a>Windows VM の SQL Server メタデータ

アプリケーションの検出が有効になっている Microsoft SQL Server が実行中の VM から、アプライアンスが収集する SQL Server メタデータを次に示します。 このデータは Azure に送信されます。

**データ**  | **レジストリの場所**  | **キー**
--- | --- | ---
名前  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Edition  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | Edition 
Service Pack  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | SP
Version  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | Version 

#### <a name="windows-vm-operating-system-data"></a>Windows VM オペレーティング システム データ

アプリケーションの検出が有効になっている各 VM から、アプライアンスが収集するオペレーティング システム データを次に示します。 このデータは Azure に送信されます。

Data  | WMI クラス  | WMI クラスのプロパティ
--- | --- | ---
名前  | Win32_operatingsystem  | Caption
Version  | Win32_operatingsystem  | Version
アーキテクチャ  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-vm-apps-data"></a>Linux VM のアプリケーション データ

アプリケーションの検出が有効になっている各 VM から、アプライアンスが収集するインストール済みのアプリケーション データを次に示します。 VM のオペレーティング システムに基づいて、1 つまたは複数のコマンドが実行されます。 このデータは Azure に送信されます。

Data  | コマンド
--- | --- 
名前 | rpm、dpkg-query、snap
Version | rpm、dpkg-query、snap
プロバイダー | rpm、dpkg-query、snap

#### <a name="linux-vm-operating-system-data"></a>Linux VM オペレーティング システム データ

アプリケーションの検出が有効になっている各 VM から、アプライアンスが収集するオペレーティング システム データを次に示します。 このデータは Azure に送信されます。

**データ**  | **コマンド** 
--- | --- | ---
名前 <br/> version | 次の 1 つ以上のファイルから収集されます。<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
アーキテクチャ | uname


### <a name="app-dependencies-metadata"></a>アプリの依存関係メタデータ

エージェントレスの依存関係分析では、接続データとプロセス データを収集します。

#### <a name="windows-vm-app-dependencies-data"></a>Windows VM のアプリの依存関係データ

エージェントレスの依存関係分析が有効になっている各 VM から、アプライアンスが収集する接続データを次に示します。 このデータは Azure に送信されます。

**データ** | **使用されるコマンド** 
--- | --- 
ローカル ポート | netstat
ローカル IP アドレス | netstat
リモート ポート | netstat
リモート IP アドレス | netstat
TCP 接続の状態 | netstat
プロセス ID | netstat
アクティブな接続の数 | netstat


エージェントレスの依存関係分析が有効になっている各 VM から、アプライアンスが収集するプロセス データを次に示します。 このデータは Azure に送信されます。

**データ** | **WMI クラス** | **WMI クラスのプロパティ**
--- | --- | ---
[処理名] | Win32_Process | ExecutablePath
プロセスの引数 | Win32_Process | CommandLine
アプリケーション名 | Win32_Process | ExecutablePath プロパティの VersionInfo.ProductName パラメーター

#### <a name="linux-vm-app-dependencies-data"></a>Linux VM のアプリの依存関係データ

エージェントレスの依存関係分析が有効になっている各 Linux VM から、アプライアンスが収集する接続データとプロセス データを次に示します。 このデータは Azure に送信されます。

**データ** | **使用されるコマンド** 
--- | ---
ローカル ポート | netstat 
ローカル IP アドレス | netstat 
リモート ポート | netstat 
リモート IP アドレス | netstat 
TCP 接続の状態 | netstat 
アクティブな接続の数 | netstat
プロセス ID  | netstat 
[処理名] | ps
プロセスの引数 | ps
アプリケーション名 | dpkg または rpm



## <a name="collected-data---hyper-v"></a>収集されるデータ - Hyper-V

アプライアンスでは、メタ―データ、パフォーマンス データ、依存関係分析データ (エージェントレスの[依存関係分析](concepts-dependency-visualization.md)が使用されている場合) が収集されます。

### <a name="metadata"></a>Metadata
Azure Migrate アプライアンスによって検出されたメタデータは、コンピューターとアプリを Azure に移行する準備ができているかどうかを判断し、コンピューターとアプリを適切にサイズ設定して、コストを計画し、アプリケーションの依存関係を分析するために役立ちます。 Microsoft では、ライセンスのコンプライアンス監査でこのデータを使用しません。

アプライアンスが収集して Azure に送信する Hyper-V VM のメタデータの全一覧を次に示します。

**データ** | **WMI クラス** | **WMI クラスのプロパティ**
--- | --- | ---
**マシンの詳細** | 
BIOS _ Msvm_BIOSElement のシリアル番号 | BIOSSerialNumber
VM の種類 (Gen 1 または 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
VM の表示名 | Msvm_VirtualSystemSettingData | ElementName
VM のバージョン | Msvm_ProcessorSettingData | VirtualQuantity
メモリ (バイト) | Msvm_MemorySettingData | VirtualQuantity
VM が使用できる最大メモリ | Msvm_MemorySettingData | 制限
動的メモリの有効化 | Msvm_MemorySettingData | DynamicMemoryEnabled
オペレーティング システムの名前/バージョン/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems 名前データ
VM の電源状態 | Msvm_ComputerSystem | EnabledState
**各ディスクの詳細** | 
ディスク識別子 | Msvm_VirtualHardDiskSettingData | VirtualDiskId
仮想ハード ディスクの種類 | Msvm_VirtualHardDiskSettingData | Type
仮想ハード ディスクのサイズ | Msvm_VirtualHardDiskSettingData | MaxInternalSize
仮想ハード ディスクの親 | Msvm_VirtualHardDiskSettingData | ParentPath
**各 NIC の詳細** | 
IP アドレス (統合 NIC) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP の有効化 (統合 NIC) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID (統合 NIC) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC アドレス (統合 NIC) | Msvm_SyntheticEthernetPortSettingData | Address
NIC ID (レガシ NIC) | MsvmEmulatedEthernetPortSetting データ | InstanceID
NIC MAC ID (レガシ NIC) | MsvmEmulatedEthernetPortSetting データ | Address

### <a name="performance-data"></a>パフォーマンス データ

アプライアンスが収集して Azure に送信する Hyper VM のパフォーマンス データを次に示します。

**パフォーマンス カウンター クラス** | **カウンター** | **評価の影響**
--- | --- | ---
Hyper-V のハイパーバイザー仮想プロセッサ | ゲスト実行時間 (%) | 推奨される VM サイズ/コスト
Hyper-V の動的メモリ VM | 現在の負荷 (%)<br/> ゲストの可視物理メモリ (MB) | 推奨される VM サイズ/コスト
Hyper-V の仮想ストレージ デバイス | 読み取りバイト数/秒 | ディスク サイズ、ストレージ コスト、VM サイズの計算
Hyper-V の仮想ストレージ デバイス | 書き込みバイト数/秒 | ディスク サイズ、ストレージ コスト、VM サイズの計算
Hyper-V の仮想ネットワーク アダプター | 受信バイト数/秒 | VM サイズの計算
Hyper-V の仮想ネットワーク アダプター | 送信バイト数/秒 | VM サイズの計算

- CPU 使用率は、VM にアタッチされているすべての仮想プロセッサのすべての使用量の合計です。
- メモリ使用率は、(現在の負荷 * ゲストの可視物理メモリ) / 100 です。
- ディスクとネットワークの使用率の値は、一覧にある Hyper-V パフォーマンス カウンターから収集されます。


## <a name="collected-data---physical"></a>収集されるデータ - 物理

アプライアンスでは、メタ―データ、パフォーマンス データ、依存関係分析データ (エージェントレスの[依存関係分析](concepts-dependency-visualization.md)が使用されている場合) が収集されます。

### <a name="windows-metadata"></a>Windows メタデータ

Azure Migrate アプライアンスによって検出されたメタデータは、コンピューターとアプリを Azure に移行する準備ができているかどうかを判断し、コンピューターとアプリを適切にサイズ設定して、コストを計画し、アプリケーションの依存関係を分析するために役立ちます。 Microsoft では、ライセンスのコンプライアンス監査でこのデータを使用しません。

アプライアンスが収集して Azure に送信する Windows サーバーのメタデータの全一覧を次に示します。

**データ** | **WMI クラス** | **WMI クラスのプロパティ**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domain、Name、PartOfDomain
プロセッサ コア数 | Win32_PRocessor | NumberOfCores
割り当てられたメモリ | Win32_ComputerSystem | TotalPhysicalMemory
BIOS のシリアル番号 | Win32_ComputerSystemProduct | IdentifyingNumber
BIOS の GUID | Win32_ComputerSystemProduct | UUID
ブートの種類 | Win32_DiskPartition | EFI/BIOS について Type = **GPT:System** のパーティションを確認します
OS 名 | Win32_OperatingSystem | Caption
OS バージョン |Win32_OperatingSystem | Version
OS アーキテクチャ | Win32_OperatingSystem | OSArchitecture
ディスク数 | Win32_DiskDrive | Model、Size、DeviceID、MediaType、Name
ディスク サイズ | Win32_DiskDrive | サイズ
NIC の一覧 | Win32_NetworkAdapterConfiguration | Description、Index
NIC の IP アドレス | Win32_NetworkAdapterConfiguration | IPAddress
NIC の MAC アドレス | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Linux メタデータ

アプライアンスが収集して Azure に送信する Linux サーバーのメタデータの全一覧を次に示します。

**データ** | **LINUX** 
--- | --- 
FQDN | cat /proc/sys/kernel/hostname, hostname -f
プロセッサ コア数 |  /proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
割り当てられたメモリ | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
BIOS のシリアル番号 | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
BIOS の GUID | cat /sys/class/dmi/id/product_uuid
ブートの種類 | [ -d /sys/firmware/efi ] && echo EFI \|\| echo BIOS
OS の名前/バージョン | OS のバージョンと名前について、以下のファイルにアクセスします。<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
OS アーキテクチャ | Uname -m
ディスク数 | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
ブート ディスク | df /boot \| sed -n 2p \| awk '{print $1}'
ディスク サイズ | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
NIC の一覧 | ip -o -4 addr show \| awk '{print $2}'
NIC の IP アドレス | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
NIC の MAC アドレス | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Windows パフォーマンス データ

アプライアンスが収集して Azure に送信する Windows サーバーのパフォーマンス データを次に示します。

**データ** | **WMI クラス** | **WMI クラスのプロパティ**
--- | --- | ---
CPU 使用率 | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
メモリ使用量 | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
NIC 数 | Win32_PerfFormattedData_Tcpip_NetworkInterface | ネットワーク デバイス数を取得します。
NIC ごとに受信したデータ | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
NIC ごとに送信したデータ | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
ディスク数 | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | ディスク数
ディスクの詳細 | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec、Diskwritespersec、DiskReadsPerSec、Diskwritespersec。

### <a name="linux-performance-data"></a>Linux のパフォーマンス データ

アプライアンスが収集して Azure に送信する Linux サーバーのパフォーマンス データを次に示します。

**データ** | **Linux** 
--- | --- 
CPU 使用率 | cat /proc/stat/| grep 'cpu' /proc/stat
メモリ使用量 | free \| grep Mem \| awk '{print $3/$2 * 100.0}'
NIC 数 | lshw -class network \| grep eth[0-60] \| wc -l
NIC ごとに受信したデータ | cat /sys/class/net/eth$nic/statistics/rx_bytes
NIC ごとに送信したデータ | cat /sys/class/net/eth$nic/statistics/tx_bytes
ディスク数 | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
ディスクの詳細 | cat /proc/diskstats


## <a name="appliance-upgrades"></a>アプライアンスのアップグレード

アプライアンスで実行されている Azure Migrate エージェントが更新されると、アプライアンスがアップグレードされます。 アプライアンスでは既定で自動更新が有効になっているため、これは自動的に行われます。 アプライアンス サービスを手動で更新するために、この既定の設定を変更できます。

### <a name="turn-off-auto-update"></a>自動更新をオフにする

1. アプライアンスを実行しているマシンで、レジストリ エディターを開きます。
2. **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance** に移動します。
3. 自動更新をオフにするには、DWORD 値が 0 のレジストリ キー **AutoUpdate** キーを作成します。

    ![レジストリ キーを設定する](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>自動更新を有効にする

自動更新を有効にするには、次のいずれかの方法を使用します。

- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance から AutoUpdate レジストリ キーを削除することによって。
- **[前提条件の設定]** ウィンドウで最新の更新チェックから **[View appliance services]\(アプライアンス サービスを表示\)** をクリックして、自動更新をオンにします。

レジストリ キーを削除するには、次の手順を実行します。

1. アプライアンスを実行しているマシンで、レジストリ エディターを開きます。
2. **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance** に移動します。
3. 自動更新を無効にするために作成したレジストリ キー **AutoUpdate** を削除します。

検出の完了後、Appliance Configuration Manager で有効にするには、以下を行います。

1. Appliance Configuration Manager で、 **[前提条件の設定]** パネルに移動します
2. 最新の更新プログラムのチェックで、 **[View appliance services]\(アプライアンス サービスを表示\)** をクリックし、リンクをクリックして自動更新をオンにします。

    ![自動更新をオンにする](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>アプライアンス サービスのバージョンを確認する

次のいずれかの方法を使用して、アプライアンス サービスのバージョンを確認できます。

- アプライアンス構成マネージャーで、 **[前提条件の設定]** パネルに移動します。
- アプライアンス マシンの **[コントロールパネル]**  >  **[プログラムと機能]** で。

Appliance Configuration Manager で確認するには以下を行います。

1. Appliance Configuration Manager で、 **[前提条件の設定]** パネルに移動します
2. 最新の更新プログラムのチェックで、 **[View appliance services]\(アプライアンス サービスを表示\)** をクリックします。

    ![バージョンの確認](./media/migrate-appliance/versions.png)

コントロール パネルで確認するには以下を行います。

1. アプライアンスで、 **[開始]**  >  **[コントロール パネル]**  >  **[プログラムと機能]** をクリックします
2. リストのアプライアンス サービスのバージョンを確認します。

    ![コントロール パネルでバージョンを確認する](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>古いバージョンを手動で更新する

いずれかのコンポーネントに対して古いバージョンを実行している場合は、サービスをアンインストールし、最新バージョンに手動で更新する必要があります。

1. 最新のアプライアンス サービス バージョンを確認するには、LatestComponents.json ファイルを[ダウンロード](https://aka.ms/latestapplianceservices)します。
2.    ダウンロードが完了したら、メモ帳で LatestComponents.json ファイルを開きます。
3. ファイル内の最新のサービス バージョンとそのファイルのダウンロード リンクを探します。 次に例を示します。

    "Name":"ASRMigrationWebApp", "DownloadLink": "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi", "Version":"6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.    ファイルのダウンロード リンクを使用して、古いサービスの最新バージョンをダウンロードします。
5. ダウンロードした後、管理者コマンド ウィンドウで次のコマンドを実行して、ダウンロードした MSI の整合性を確認します。

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ```次に例を示します。C:\>CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. コマンドの出力が、ファイル内のサービスのハッシュ値のエントリ (上記の MD5 ハッシュ値など) と一致することを確認します。
6. 次に MSI を実行して、サービスをインストールします。 これはサイレント インストールであり、完了後にインストール ウィンドウが閉じます。
7. インストールが完了したら、 **[コントロール パネル]**  >  **[プログラムと機能]** でサービスのバージョンを確認します。 これで、サービスのバージョンが json ファイルに示されている最新のバージョンにアップグレードされているはずです。



## <a name="next-steps"></a>次のステップ

- VMware 用にアプライアンスを設定する[方法を確認する](how-to-set-up-appliance-vmware.md)。
- Hyper-V 用にアプライアンスを設定する[方法を確認する](how-to-set-up-appliance-hyper-v.md)。
- 物理サーバーのためのアプライアンスを設定する[方法を確認](how-to-set-up-appliance-physical.md)します。

