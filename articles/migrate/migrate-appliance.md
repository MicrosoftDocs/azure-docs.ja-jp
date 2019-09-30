---
title: Azure Migrate アプライアンスのアーキテクチャ | Microsoft Docs
description: Azure Migrate アプライアンスの概要を示します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: e2faa47a58283623747ae569de22e1c57df1a51f
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231132"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate アプライアンス

この記事では、Azure Migrate アプライアンスについて説明します。 Azure Migrate Assessment および Migration ツールを使用して、アプリ、インフラストラクチャ、およびワークロードを検出、評価し、それらを Microsoft Azure に移行する場合、アプライアンスをデプロイします。 

[Azure Migrate](migrate-services-overview.md) は、オンプレミスのアプリとワークロード、およびプライベート/パブリック クラウド VM の検出、評価、およびそれらの Azure への移行を追跡するための中央ハブとなります。 このハブには、評価および移行のための Azure Migrate ツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが用意されています。



## <a name="appliance-overview"></a>アプライアンスの概要

Azure Migrate アプライアンスの種類と使用方法を次に示します。

**以下としてデプロイ** | **用途** | **詳細**
--- | --- |  ---
VMware VM | Azure Migrate Assessment ツールを使用した VMware VM の評価。<br/><br/> Azure Migrate Server Migration ツールを使用した VMware VM のエージェントレス移行。 | OVA テンプレートをダウンロードし、それを vCenter Server にインポートして、アプライアンス VM を作成します。
Hyper-V VM | Azure Migrate Assessment ツールを使用した Hyper-V VM の評価。 | Zip 形式の VHD をダウンロードし、それを Hyper-V にインポートして、アプライアンス VM を作成します。

## <a name="appliance-access"></a>アプライアンスへのアクセス

アプライアンスを構成した後、TCP ポート 3389 を介してアプライアンス VM にリモート アクセスできます。 また、URL: `https://<appliance-ip-or-name>:44368` を使用して、ポート 44368 でアプライアンスの Web 管理アプリにリモート アクセスすることもできます。

## <a name="appliance-license"></a>アプライアンスのライセンス
アプライアンスには、180 日間有効な Windows Server 2016 評価版ライセンスが付属します。 評価期間が期限切れ間近の場合は、新しいアプライアンスをダウンロードしてデプロイするか、アプライアンス VM のオペレーティング システムのライセンスをアクティブ化することをお勧めします。

## <a name="appliance-agents"></a>アプライアンスのエージェント
アプライアンスには、これらのエージェントがインストールされています。

**エージェント** | **詳細**
--- | ---
検出エージェント | オンプレミスの仮想マシンの構成データを収集します
評価エージェント | VM のパフォーマンス データを収集するために、オンプレミス環境をプロファイルします。
移行アダプター | VM のレプリケーションを調整し、VM と Azure 間の通信を調整します。
移行ゲートウェイ | レプリケートされた VM のデータを Azure に送信します。


## <a name="appliance-deployment-requirements"></a>アプライアンスのデプロイ要件

- VMware アプライアンスのデプロイ要件と、アプライアンスがアクセスする必要がある URL を[確認](migrate-support-matrix-vmware.md#assessment-appliance-requirements)します。
- Hyper-V アプライアンスのデプロイ要件と、アプライアンスがアクセスする必要がある URL を[確認](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements)します。


## <a name="collected-performance-data-vmware"></a>収集対象のパフォーマンス データ - VMware

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


## <a name="collected-metadata-vmware"></a>収集対象のメタデータ - VMware

> [!NOTE]
> Azure Migrate アプライアンスによって検出されたメタデータは、アプリケーションを Azure に移行し、Azure 適合性分析、アプリケーション依存関係分析、およびコスト計画を実行するときに、アプリケーションを適切なサイズにするのに役立ちます。 Microsoft では、ライセンスのコンプライアンス監査に関してこのデータを使用しません。

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



## <a name="collected-performance-data-hyper-v"></a>収集対象のパフォーマンス データ - Hyper-V

> [!NOTE]
> Azure Migrate アプライアンスによって検出されたメタデータは、アプリケーションを Azure に移行し、Azure 適合性分析、アプリケーション依存関係分析、およびコスト計画を実行するときに、アプリケーションを適切なサイズにするのに役立ちます。 Microsoft では、ライセンスのコンプライアンス監査に関してこのデータを使用しません。

アプライアンスが収集して Azure に送信する VMware VM のパフォーマンス データを次に示します。

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

## <a name="collected-metadata-hyper-v"></a>収集対象のメタデータ - Hyper-V

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




## <a name="discovery-and-collection-process"></a>検出と収集のプロセス

アプライアンスは、次のプロセスを使用して、vCenter サーバーおよび Hyper-V ホスト/クラスターと通信します。


1. **検出を開始する**:
    - Hyper-V アプライアンスで検出を開始すると、WinRM ポート 5985 (HTTP) および 5986 (HTTPS) で Hyper-V ホストとの通信が行われます。
    - VMware アプライアンスで検出を開始すると、既定では TCP ポート 443 で vCenter サーバーとの通信が行われます。 vCenter サーバーが別のポートでリッスンしている場合は、それをアプライアンス Web アプリで構成できます。
2. **メタデータとパフォーマンス データを収集する**:
    - アプライアンスは、Common Information Model (CIM) セッションを使用して、ポート 5985 および 5986 で Hyper-V ホストから Hyper-V VM のデータを収集します。
    - アプライアンスは、既定ではポート 443 を使用して通信を行い、vCenter サーバーから VMware VM のデータを収集します。
3. **データを送信する**:アプライアンスは、SSL ポート 443 を介して、収集したデータを Azure Migrate Server Assessment と Azure Migrate Server Migration に送信します。
    - パフォーマンス データの場合、アプライアンスはリアルタイムの使用状況データを収集します。
        - パフォーマンス データは、各パフォーマンス メトリックについて、VMware では 20 秒ごとに収集され、Hyper-V では 30 秒ごとに収集されます。
        - 収集されたデータは、10 分間に 1 つのデータ ポイントを作成するためにロールアップされます。
        - ピーク時の使用率の値は、すべての 20/30 秒のデータ ポイントから選択され、評価計算のために Azure に送信されます。
        - 評価のプロパティで指定されたパーセンタイル値 (50/90/95/99) に基づいて、10 分間のポイントが昇順に並べ替えられ、適切なパーセンタイル値を使用して評価が計算されます。
    - Server Migration の場合、アプライアンスは VM データの収集を開始し、それを Azure にレプリケートします。
4. **評価および移行する**:これで、Azure Migrate Server Assessment を使用して、アプライアンスによって収集されたメタデータから評価を作成できます。 また、Azure Migrate Server Migration を使用して VMware VM の移行を開始し、エージェントレスの VM レプリケーションを調整することもできます。


![アーキテクチャ](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>アプライアンスのアップグレード

アプライアンスで実行されている Azure Migrate エージェントが更新されると、アプライアンスがアップグレードされます。

- アプライアンスでは既定で自動更新が有効になっているため、これは自動的に行われます。
- エージェントを手動で更新するために、この既定の設定を変更できます。
- 自動更新を無効にするには、レジストリ エディターで HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance にアクセスし、レジストリ キー "AutoUpdate" を 0 (DWORD) に設定します。
 
### <a name="set-agent-updates-to-manual"></a>エージェントの更新を手動に設定する

手動で更新する場合は、アプライアンスの古いエージェントごとに **[更新]** ボタンを使用して、アプライアンスのすべてのエージェントを同時に更新してください。 更新設定は、いつでも自動更新に戻すことができます。

## <a name="next-steps"></a>次の手順

VMware 用にアプライアンスを設定する[方法を確認する](tutorial-assess-vmware.md#set-up-the-appliance-vm)。
Hyper-V 用にアプライアンスを設定する[方法を確認する](tutorial-assess-hyper-v.md#set-up-the-appliance-vm)。

