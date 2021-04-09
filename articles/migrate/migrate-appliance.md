---
title: Azure Migrate アプライアンス
description: Azure Migrate アプライアンスに対するサポートの概要について説明します。
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 08cd0e9d33dd88b9bdc418f3d1bbd382b2d80632
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038766"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate アプライアンス

この記事では、Azure Migrate アプライアンスの前提条件とサポート要件の概要を説明します。

## <a name="deployment-scenarios"></a>デプロイメント シナリオ

Azure Migrate アプライアンスは、次のシナリオで使用します。

**シナリオ** | **ツール** | **用途**
--- | --- | ---
**VMware 環境で実行されているサーバーの検出と評価** | Azure Migrate:Server Assessment | VMware 環境で実行されているサーバーを検出します<br/><br/> インストールされているアプリケーションの検出、エージェントレスの依存関係分析、SQL Server インスタンスとデータベースの検出を実行します。<br/><br/> 評価のためにサーバー構成とパフォーマンス メタデータを収集します。
**VMware 環境で実行されているサーバーのエージェントレス移行** | Azure Migrate:Server Migration | VMware 環境で実行されているサーバーを検出します。 <br/><br/> エージェントをインストールせずにサーバーをレプリケートします。
**Hyper-V 環境で実行されているサーバーの検出と評価** | Azure Migrate:Server Assessment | Hyper-V 環境で実行されているサーバーを検出します。<br/><br/> 評価のためにサーバー構成とパフォーマンス メタデータを収集します。
**オンプレミスの物理または仮想化されたサーバーの検出と評価** |  Azure Migrate:Server Assessment |  オンプレミスの物理または仮想化されたサーバーを検出します。<br/><br/> 評価のためにサーバー構成とパフォーマンス メタデータを収集します。

## <a name="deployment-methods"></a>デプロイ方法

このアプライアンスは、次のいくつかの方法を使用してデプロイできます。

- アプライアンスは、VMware または Hyper-V 環境で実行されているサーバー用のテンプレート ([VMware の場合は OVA テンプレート](how-to-set-up-appliance-vmware.md)、[Hyper-V の場合は VHD](how-to-set-up-appliance-hyper-v.md)) を使用してデプロイできます。
- テンプレートを使用しない場合は、[PowerShell インストーラー スクリプト](deploy-appliance-script.md)を使って VMware または Hyper-V 環境用のアプライアンスをデプロイできます。
- Azure Government では、PowerShell インストーラー スクリプトを使用してアプライアンスをデプロイする必要があります。 デプロイの手順については、[こちら](deploy-appliance-script-government.md)を参照してください。
- オンプレミスまたはその他のクラウドの物理あるいは仮想化されたサーバーの場合は、常に PowerShell インストーラー スクリプトを使用してアプライアンスをデプロイします。デプロイの手順については、[こちら](how-to-set-up-appliance-physical.md)を参照してください。
- ダウンロード リンクは、以下の表にあります。


## <a name="appliance---vmware"></a>アプライアンス - VMware

次の表は、VMware の Azure Migrate アプライアンス要件をまとめたものです。

> [!Note]
> VMware 環境で実行されている SQL Server インスタンスおよびデータベースの検出と評価は、現在プレビュー段階にあります。 この機能を試すには、[**このリンク**](https://aka.ms/AzureMigrate/SQL)を使用して、**オーストラリア東部** リージョンにプロジェクトを作成します。 オーストラリア東部に既にプロジェクトがあり、この機能を試したい場合は、ポータルでこれらの [**前提条件**](how-to-discover-sql-existing-project.md)が完了していることを確認してください。

**要件** | **VMware** 
--- | ---
**アクセス許可** | アプライアンス構成マネージャーにローカルまたはリモートでアクセスするには、アプライアンス サーバーの管理者特権を持つローカルまたはドメインのユーザー アカウントが必要です。
**アプライアンス サービス** | アプライアンスには次のサービスがあります。<br/><br/> - **アプライアンス構成マネージャー**: これは Web アプリケーションであり、サーバーの検出と評価を開始するためにソースの詳細で構成できます。<br/> - **VMware 検出エージェント**: このエージェントにより、オンプレミスの評価として作成するために使用できるサーバー構成メタデータが収集されます。<br/>- **VMware 評価エージェント**: このエージェントにより、パフォーマンスベースの評価を作成するために使用できるサーバー パフォーマンス メタデータが収集されます。<br/>- **自動更新サービス**: このサービスにより、アプライアンス上で実行されているすべてのエージェントが最新の状態に保たれます。 24 時間ごとに自動的に実行されます。<br/>- **DRA エージェント**: サーバーのレプリケーションを調整し、レプリケートされたサーバーと Azure 間の通信をコーディネートします。 エージェントレスの移行を使用して、サーバーを Azure にレプリケートする場合にのみ使用されます。<br/>- **ゲートウェイ**:レプリケートされたデータを Azure に送信します。 エージェントレスの移行を使用して、サーバーを Azure にレプリケートする場合にのみ使用されます。<br/>- **SQL 検出および評価エージェント**: SQL Server インスタンスとデータベースの構成およびパフォーマンス メタデータを Azure に送信します。
**プロジェクトの制限** |  単一のプロジェクトにのみアプライアンスを登録することができます。<br/> 単一のプロジェクトに複数の登録済みアプライアンスを含めることができます。
**検出の制限** | 1 つのアプライアンスによって、vCenter Server 上で実行されているサーバーを最大 10,000 台検出できます。<br/> 1 つのアプライアンスは、1 つの vCenter Server に接続できます。
**サポートされるデプロイ** | OVA テンプレートを使用して、vCenter Server で実行されている新しいサーバーとしてデプロイします。<br/><br/> PowerShell インストーラー スクリプトを使用して、Windows Server 2016 を実行している既存のサーバーにデプロイします。
**OVA テンプレート** | ポータルから、または[こちら](https://go.microsoft.com/fwlink/?linkid=2140333)からダウンロードします<br/><br/> ダウンロード サイズは 11.9 GB です。<br/><br/> ダウンロードしたアプライアンス テンプレートには、180 日間有効な Windows Server 2016 評価版ライセンスが付属します。<br/>評価期間が期限切れ間近の場合は、OVA テンプレートを使用して新しいアプライアンスをダウンロードしてデプロイするか、アプライアンス サーバーのオペレーティング システムのライセンスをアクティブ化することをお勧めします。
**OVA の確認** | ハッシュ値を確かめて、プロジェクトからダウンロードした OVA テンプレートを[確認](tutorial-discover-vmware.md#verify-security)します。
**PowerShell スクリプト** | PowerShell インストーラー スクリプトを使用してアプライアンスをデプロイする方法については、こちらの[記事](./deploy-appliance-script.md#set-up-the-appliance-for-vmware)を参照してください。<br/><br/> 
**ハードウェアとネットワークの要件** |  アプライアンスは、Windows Server 2016、32 GB の RAM、8 つの vCPU、約 80 GB のディスク記憶域、外部仮想スイッチを搭載したサーバーで実行する必要があります。<br/> アプライアンスは、直接またはプロキシを介してインターネットにアクセスできる必要があります。<br/><br/> OVA テンプレートを使用してアプライアンスをデプロイする場合は、ハードウェア要件を満たすサーバーを作成するために、vCenter Server に十分なリソースが必要です。<br/><br/> 既存のサーバーでアプライアンスを実行する場合は、Windows Server 2016 を実行しており、ハードウェアの要件を満たしていることを確認します。<br/>_(現在のところ、アプライアンスは Windows Server 2016 でのみデプロイできます。)_
**VMware の要件** | アプライアンスを vCenter Server のサーバーとしてデプロイする場合は、5.5、6.0、6.5、または 6.7 を実行している vCenter Server と、バージョン 5.5 以降を実行している ESXi ホストにデプロイする必要があります。<br/><br/> 
**VDDK (エージェントレス移行)** | アプライアンスを利用してエージェントレスでサーバーを移行するには、VMware vSphere VDDK をアプライアンス サーバーにインストールする必要があります。

## <a name="appliance---hyper-v"></a>アプライアンス - Hyper-V

**要件** | **Hyper-V** 
--- | ---
**アクセス許可** | アプライアンス構成マネージャーにローカルまたはリモートでアクセスするには、アプライアンス サーバーの管理者特権を持つローカルまたはドメインのユーザー アカウントが必要です。
**アプライアンス サービス** | アプライアンスには次のサービスがあります。<br/><br/> - **アプライアンス構成マネージャー**: これは Web アプリケーションであり、サーバーの検出と評価を開始するためにソースの詳細で構成できます。<br/> - **検出エージェント**: このエージェントにより、オンプレミスの評価として作成するために使用できるサーバー構成メタデータが収集されます。<br/>- **評価エージェント**: このエージェントにより、パフォーマンスベースの評価を作成するために使用できるサーバー パフォーマンス メタデータが収集されます。<br/>- **自動更新サービス**: このサービスにより、アプライアンス上で実行されているすべてのエージェントが最新の状態に保たれます。 24 時間ごとに自動的に実行されます。
**プロジェクトの制限** |  単一のプロジェクトにのみアプライアンスを登録することができます。<br/> 単一のプロジェクトに複数の登録済みアプライアンスを含めることができます。
**検出の制限** | 1 つのアプライアンスによって、Hyper-V 環境で実行されているサーバーを最大 5,000 台検出できます。<br/> アプライアンスは、最大 300 個の Hyper-V ホストに接続できます。
**サポートされるデプロイ** | VHD テンプレートを使用して Hyper-V ホストで実行されているサーバーとしてデプロイします。<br/><br/> PowerShell インストーラー スクリプトを使用して、Windows Server 2016 を実行している既存のサーバーにデプロイします。
**VHD テンプレート** | VHD を含む zip ファイル。 プロジェクトから、または[こちら](https://go.microsoft.com/fwlink/?linkid=2140422)からダウンロードします。<br/><br/> ダウンロード サイズは 8.91 GB です。<br/><br/> ダウンロードしたアプライアンス テンプレートには、180 日間有効な Windows Server 2016 評価版ライセンスが付属します。<br/> 評価期間が期限切れ間近の場合は、新しいアプライアンスをダウンロードしてデプロイするか、アプライアンス サーバーのオペレーティング システムのライセンスをアクティブ化することをお勧めします。
**VHD の確認** | ハッシュ値を確かめて、プロジェクトからダウンロードした VHD テンプレートを[確認](tutorial-discover-hyper-v.md#verify-security)します。
**PowerShell スクリプト** | PowerShell インストーラー スクリプトを使用してアプライアンスをデプロイする方法については、こちらの[記事](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v)を参照してください。<br/>
**ハードウェアとネットワークの要件**  |  アプライアンスは、Windows Server 2016、16 GB の RAM、8 つの vCPU、約 80 GB のディスク記憶域、外部仮想スイッチを搭載したサーバーで実行する必要があります。<br/> アプライアンスには、静的または動的 IP アドレスが必要であり、直接またはプロキシを介したインターネット アクセスが必要です。<br/><br/> Hyper-V ホスト上で実行されているサーバーとしてアプライアンスを実行する場合は、ハードウェア要件を満たすサーバーを作成するために、ホスト上に十分なリソースが必要です。<br/><br/> 既存のサーバーでアプライアンスを実行する場合は、Windows Server 2016 を実行しており、ハードウェアの要件を満たしていることを確認します。<br/>_(現在のところ、アプライアンスは Windows Server 2016 でのみデプロイできます。)_
**Hyper-V の要件** | VHD テンプレートを使用してアプライアンスをデプロイする場合、Azure Migrate によって提供されるアプライアンスは、Hyper-V VM バージョン 5.0 です。<br/><br/> Hyper-V ホストで Windows Server 2012 R2 以降が実行されている必要があります。

## <a name="appliance---physical"></a>アプライアンス - 物理

**要件** | **物理** 
--- | ---
**アクセス許可** | アプライアンス構成マネージャーにローカルまたはリモートでアクセスするには、アプライアンス サーバーの管理者特権を持つローカルまたはドメインのユーザー アカウントが必要です。
**アプライアンス サービス** | アプライアンスには次のサービスがあります。<br/><br/> - **アプライアンス構成マネージャー**: これは Web アプリケーションであり、サーバーの検出と評価を開始するためにソースの詳細で構成できます。<br/> - **検出エージェント**: このエージェントにより、オンプレミスの評価として作成するために使用できるサーバー構成メタデータが収集されます。<br/>- **評価エージェント**: このエージェントにより、パフォーマンスベースの評価を作成するために使用できるサーバー パフォーマンス メタデータが収集されます。<br/>- **自動更新サービス**: このサービスにより、アプライアンス上で実行されているすべてのエージェントが最新の状態に保たれます。 24 時間ごとに自動的に実行されます。
**プロジェクトの制限** |  単一のプロジェクトにのみアプライアンスを登録することができます。<br/> 単一のプロジェクトに複数の登録済みアプライアンスを含めることができます。<br/> 
**検出の制限** | 1 つのアプライアンスで最大 1000 台の物理サーバーを検出できます。
**サポートされるデプロイ** | PowerShell インストーラー スクリプトを使用して、Windows Server 2016 を実行している既存のサーバーにデプロイします。
**PowerShell スクリプト** | プロジェクトから、または[こちら](https://go.microsoft.com/fwlink/?linkid=2140334)から zip ファイルのスクリプト (AzureMigrateInstaller.ps1) をダウンロードします。 [詳細については、こちらを参照してください](tutorial-discover-physical.md)。<br/><br/> ダウンロード サイズは 85.8 MB です。
**スクリプトの確認** | ハッシュ値を確かめて、プロジェクトからダウンロードした PowerShell インストーラー スクリプトを[確認](tutorial-discover-physical.md#verify-security)します。
**ハードウェアとネットワークの要件** |  アプライアンスは、Windows Server 2016、16 GB の RAM、8 つの vCPU、約 80 GB のディスク記憶域を搭載したサーバーで実行する必要があります。<br/> アプライアンスには、静的または動的 IP アドレスが必要であり、直接またはプロキシを介したインターネット アクセスが必要です。<br/><br/> 既存のサーバーでアプライアンスを実行する場合は、Windows Server 2016 を実行しており、ハードウェアの要件を満たしていることを確認します。<br/>_(現在のところ、アプライアンスは Windows Server 2016 でのみデプロイできます。)_


## <a name="url-access"></a>URL アクセス

Azure Migrate アプライアンスには、インターネットへの接続が必要です。

- アプライアンスをデプロイすると、必要な URL への接続チェックが Azure Migrate によって行われます。
- リスト内のすべての URL へのアクセスを許可する必要があります。 評価のみ行う場合は、VMware エージェントレスの移行で必須とマークされている URL をスキップできます。
-  URL ベースのプロキシを使用してインターネットに接続している場合は、URL の探索中に受信されたすべての CNAME レコードがプロキシによって解決されるようにします。

### <a name="public-cloud-urls"></a>パブリック クラウドの URL

**URL** | **詳細**  
--- | --- |
*.portal.azure.com  | Azure Portal に移動します。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com | Azure サブスクリプションにサインインします。
*.microsoftonline.com <br/> *.microsoftonline-p.com | アプライアンスで Azure Migrate と通信するための Azure Active Directory (AD) アプリを作成します。
management.azure.com | アプライアンスで Azure Migrate と通信するための Azure AD アプリを作成します。
*.services.visualstudio.com | 内部監視に使用するアプライアンス ログをアップロードします。
*.vault.azure.net | Azure Key Vault でシークレットを管理します。<br/> 注: レプリケートするサーバーの、ここへのアクセス権を確保します。
aka.ms/* | aka リンクへのアクセスを許可します。アプライアンス サービスの最新の更新プログラムをダウンロードしてインストールするために使用されます。
download.microsoft.com/download | Microsoft ダウンロード センターからのダウンロードを許可します。
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
*.services.visualstudio.com | 内部監視に使用するアプライアンス ログをアップロードします。
*.vault.usgovcloudapi.net | Azure Key Vault でシークレットを管理します。
aka.ms/* | aka リンクへのアクセスを許可します。アプライアンス サービスの最新の更新プログラムをダウンロードしてインストールするために使用されます。
download.microsoft.com/download | Microsoft ダウンロード センターからのダウンロードを許可します。
*.servicebus.usgovcloudapi.net  | アプライアンスと Azure Migrate サービスの間の通信。
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Azure Migrate サービスの URL に接続します。
*.hypervrecoverymanager.windowsazure.us | **VMware のエージェントレス移行のために使用**<br/><br/> Azure Migrate サービスの URL に接続します。
*.blob.core.usgovcloudapi.net  |  **VMware のエージェントレス移行のために使用**<br/><br/>移行のためにストレージにデータをアップロードします。
*.applicationinsights.us | 内部監視に使用するアプライアンス ログをアップロードします。


## <a name="collected-data---vmware"></a>収集されるデータ - VMware

アプライアンスによって、構成メタデータ、パフォーマンス メタデータ、およびサーバーの依存関係データ (エージェントレスの[依存関係分析](concepts-dependency-visualization.md)が使用されている場合) が収集されます。

### <a name="metadata"></a>Metadata

Azure Migrate アプライアンスによって検出されたメタデータは、サーバーを Azure に移行する準備ができているかどうかを判断し、サーバーを適切にサイズ設定して、コストを計画し、アプリケーションの依存関係を分析するために役立ちます。 Microsoft では、ライセンスのコンプライアンス監査でこのデータを使用しません。

アプライアンスによって収集され、Azure に送信されるサーバーのメタデータの完全な一覧を以下に示します。

**データ** | **カウンター**
--- | --- 
**サーバーの詳細** | 
サーバー ID | vm.Config.InstanceUuid 
サーバー名 | vm.Config.Name
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


### <a name="installed-applications-data"></a>インストールされているアプリケーションのデータ

アプライアンスにより、サーバー上のインストールされているアプリケーション、ロールおよび機能に関するデータが収集されます。

#### <a name="windows-server-application-data"></a>Windows サーバー アプリケーションのデータ

VMware 環境で検出された各 Windows サーバーからアプライアンスによって収集されるアプリケーション データを以下に示します。

**データ** | **レジストリの場所** | **キー**
--- | --- | ---
アプリケーション名  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Version  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion 
プロバイダー  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-server-features-data"></a>Windows サーバー機能のデータ

VMware 環境で検出された各 Windows サーバーからアプライアンスによって収集される機能データを以下に示します。

**データ**  | **PowerShell コマンドレット** | **プロパティ**
--- | --- | ---
名前  | Get-WindowsFeature  | 名前
特徴の種類 | Get-WindowsFeature  | FeatureType
Parent  | Get-WindowsFeature  | Parent

#### <a name="sql-server-metadata"></a>SQL Server のメタデータ

VMware 環境で検出された各 Windows サーバーからアプライアンスによって収集される SQL Server データを以下に示します。

**データ**  | **レジストリの場所**  | **キー**
--- | --- | ---
名前  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Edition  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | Edition 
Service Pack  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | SP
Version  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | Version 

#### <a name="windows-server-operating-system-data"></a>Windows サーバー オペレーティング システムのデータ

VMware 環境で検出された各 Windows サーバーからアプライアンスによって収集されるオペレーティング システム データを以下に示します。

**データ**  | **WMI クラス**  | **WMI クラスのプロパティ**
--- | --- | ---
名前  | Win32_operatingsystem  | Caption
Version  | Win32_operatingsystem  | Version
Architecture  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-application-data"></a>Linux サーバー アプリケーションのデータ

以下に、VMware 環境で検出された各 Linux サーバーからアプライアンスによって収集されるアプリケーション データを示します。 サーバーのオペレーティング システムに基づいて、1 つまたは複数のコマンドが実行されます。

**データ**  | **コマンド**
--- | --- 
名前 | rpm、dpkg-query、snap
Version | rpm、dpkg-query、snap
プロバイダー | rpm、dpkg-query、snap

#### <a name="linux-server-operating-system-data"></a>Linux サーバー オペレーティング システムのデータ

VMware 環境で検出された各 Linux サーバーからアプライアンスによって収集されるオペレーティング システム データを以下に示します。

**データ**  | **コマンド**
--- | --- 
名前 <br/> version | 次の 1 つ以上のファイルから収集されます。<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
Architecture | uname

### <a name="sql-server-instances-and-databases-data"></a>SQL Server インスタンスおよびデータベースのデータ

アプライアンスにより、SQL Server インスタンスとデータベースに関するデータが収集されます。

> [!Note]
> VMware 環境で実行されている SQL Server インスタンスおよびデータベースの検出と評価は、現在プレビュー段階にあります。 この機能を試すには、[**このリンク**](https://aka.ms/AzureMigrate/SQL)を使用して、**オーストラリア東部** リージョンにプロジェクトを作成します。 オーストラリア東部に既にプロジェクトがあり、この機能を試したい場合は、ポータルでこれらの [**前提条件**](how-to-discover-sql-existing-project.md)が完了していることを確認してください。

#### <a name="sql-database-metadata"></a>SQL データベースのメタデータ

**データベースのメタデータ** | **ビューまたは SQL Server のプロパティ**
--- | ---
データベースの一意識別子 | sys.databases
サーバー定義データベースの ID | sys.databases
データベースの名前 | sys.databases
データベースの互換性レベル | sys.databases
データベースの照合順序名 | sys.databases
データベースの状態 | sys.databases
データベースのサイズ (MB 単位) | sys.master_files
データ ファイルを含む場所のドライブ文字 | SERVERPROPERTY、および Software\Microsoft\MSSQLServer\MSSQLServer
データベース ファイルの一覧 | sys.databases、sys.master_files
Service Broker が有効になっているかどうか | sys.databases
データベースで変更データ キャプチャが有効になっているかどうか | sys.databases

#### <a name="sql-server-metadata"></a>SQL Server のメタデータ

**サーバーのメタデータ** | **ビューまたは SQL Server のプロパティ**
--- | ---
サーバー名 |SERVERPROPERTY 
FQDN | インストールされているアプリケーションの検出から派生した接続文字列
インストール ID | sys.dm_server_registry
サーバーのバージョン | SERVERPROPERTY
サーバーのエディション | SERVERPROPERTY
サーバー ホスト プラットフォーム (Windows または Linux) | SERVERPROPERTY
サーバーの製品レベル (RTM SP CTP) | SERVERPROPERTY 
既定のバックアップ パス | SERVERPROPERTY
データ ファイルの既定のパス | SERVERPROPERTY、および Software\Microsoft\MSSQLServer\MSSQLServer
ログ ファイルの既定のパス | SERVERPROPERTY、および Software\Microsoft\MSSQLServer\MSSQLServer
いいえ。 サーバー上のコアの | sys.dm_os_schedulers、sys.dm_os_sys_info
サーバーの照合順序名 | SERVERPROPERTY
いいえ。 VISIBLE ONLINE 状態のサーバー上のコアの | sys.dm_os_schedulers
一意のサーバー ID | sys.dm_server_registry
HA が有効になっているかどうか | SERVERPROPERTY
バッファー プール拡張機能が有効になっているかどうか | sys.dm_os_buffer_pool_extension_configuration
フェールオーバー クラスターが構成されているかどうか | SERVERPROPERTY
Windows 認証モードのみを使用するサーバー | SERVERPROPERTY 
サーバーにより PolyBase がインストールされる | SERVERPROPERTY 
いいえ。 システム上の論理 CPU の | sys.dm_server_registry、sys.dm_os_sys_info
論理または物理コア数の比率。このコアは 1 つの物理プロセッサ パッケージによって公開されます | sys.dm_os_schedulers、sys.dm_os_sys_info
システム上の物理 CPU の数 | sys.dm_os_schedulers、sys.dm_os_sys_info
サーバーが最後に起動した日時 | sys.dm_server_registry
最大サーバーメモリ使用量 (MB 単位) | sys.dm_os_process_memory
すべての データベース全体のユーザーの総数 | sys.databases、sys.logins
すべてのユーザー データベースの合計サイズ | sys.databases
一時データベースのサイズ | sys.master_files、sys.configurations、sys.dm_os_sys_info
いいえ。 ログインの | sys.logins
リンク サーバーの一覧 | sys.servers
エージェント ジョブの一覧 | [msdb].[dbo].[sysjobs]、[sys].[syslogins]、[msdb].[dbo].[syscategories]

### <a name="performance-metadata"></a>パフォーマンス メタデータ

**パフォーマンス** | **ビューまたは SQL Server のプロパティ** | **評価の影響**
--- | --- | ---
SQL Server の CPU 使用率| sys.dm_os_ring_buffers| 推奨される SKU サイズ (CPU ディメンション)
SQL の論理 CPU 数| sys.dm_os_sys_info| 推奨される SKU サイズ (CPU ディメンション)
使用中の SQL 物理メモリ| sys.dm_os_process_memory| 未使用
SQL のメモリ使用率| sys.dm_os_process_memory | 未使用
データベースの CPU 使用率| sys.dm_exec_query_stats、sys.dm_exec_plan_attributes| 推奨される SKU サイズ (CPU ディメンション)
使用中のデータベース メモリ (バッファー プール)| sys.dm_os_buffer_descriptors| 推奨される SKU サイズ (メモリ ディメンション)
ファイルの読み取り/書き込み IO| sys.dm_io_virtual_file_stats、sys.master_files| 推奨される SKU サイズ (IO ディメンション)
読み取り/書き込みのファイル数| sys.dm_io_virtual_file_stats、sys.master_files| 推奨される SKU サイズ (スループット ディメンション)
ファイル IO 停止の読み取り/書き込み (ms)| sys.dm_io_virtual_file_stats、sys.master_files| 推奨される SKU サイズ (IO 待機時間ディメンション)
ファイル サイズ| sys.master_files| 推奨される SKU サイズ (ストレージ ディメンション)


### <a name="application-dependency-data"></a>アプリケーションの依存関係データ

エージェントレスの依存関係分析では、接続およびプロセス データを収集します。

#### <a name="windows-server-dependencies-data"></a>Windows サーバーの依存関係データ

エージェントレスの依存関係分析が有効になっている各 Windows サーバーから、アプライアンスによって収集される接続データを以下に示します。

**データ** | **コマンド** 
--- | --- 
ローカル ポート | netstat
ローカル IP アドレス | netstat
リモート ポート | netstat
リモート IP アドレス | netstat
TCP 接続の状態 | netstat
プロセス ID | netstat
アクティブな接続の数 | netstat

エージェントレスの依存関係分析が有効になっている各 Windows サーバーから、アプライアンスによって収集される接続データを以下に示します。

**データ** | **WMI クラス** | **WMI クラスのプロパティ**
--- | --- | ---
[処理名] | Win32_Process | ExecutablePath
プロセスの引数 | Win32_Process | CommandLine
アプリケーション名 | Win32_Process | ExecutablePath プロパティの VersionInfo.ProductName パラメーター

#### <a name="linux-server-dependencies-data"></a>Linux サーバーの依存関係データ

エージェントレスの依存関係分析が有効になっている各 Linux サーバーから、アプライアンスによって収集される接続データを以下に示します。

**データ** | **コマンド** 
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

アプライアンスにより、Hyper-V 環境で実行されているサーバーから構成およびパフォーマンス メタデータが収集されます。

### <a name="metadata"></a>Metadata
Azure Migrate アプライアンスによって検出されたメタデータは、サーバーを Azure に移行する準備ができているかどうかを判断し、サーバーを適切にサイズ設定して、コストを計画するのに役立ちます。 Microsoft では、ライセンスのコンプライアンス監査でこのデータを使用しません。

アプライアンスによって収集され、Azure に送信されるサーバーのメタデータの完全な一覧を以下に示します。

**データ** | **WMI クラス** | **WMI クラスのプロパティ**
--- | --- | ---
**サーバーの詳細** | 
BIOS のシリアル番号 | Msvm_BIOSElement | BIOSSerialNumber
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

アプライアンスによって収集され、Azure に送信されるサーバーのパフォーマンス データを以下に示します。

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

アプライアンスにより、オンプレミスで実行されている物理または仮想サーバーから構成およびパフォーマンス メタデータが収集されます。

### <a name="metadata"></a>Metadata

Azure Migrate アプライアンスによって検出されたメタデータは、サーバーを Azure に移行する準備ができているかどうかを判断し、サーバーを適切にサイズ設定して、コストを計画するのに役立ちます。 Microsoft では、ライセンスのコンプライアンス監査でこのデータを使用しません。

### <a name="windows-server-metadata"></a>Windows サーバーのメタデータ

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

### <a name="linux-server-metadata"></a>Linux サーバーのメタデータ

アプライアンスが収集して Azure に送信する Linux サーバーのメタデータの全一覧を次に示します。

**データ** | **コマンド** 
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

**データ** | **コマンド** 
--- | --- 
CPU 使用率 | cat /proc/stat/| grep 'cpu' /proc/stat
メモリ使用量 | free \| grep Mem \| awk '{print $3/$2 * 100.0}'
NIC 数 | lshw -class network \| grep eth[0-60] \| wc -l
NIC ごとに受信したデータ | cat /sys/class/net/eth$nic/statistics/rx_bytes
NIC ごとに送信したデータ | cat /sys/class/net/eth$nic/statistics/tx_bytes
ディスク数 | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
ディスクの詳細 | cat /proc/diskstats


## <a name="appliance-upgrades"></a>アプライアンスのアップグレード

アプライアンスで実行されている Azure Migrate サービスが更新されると、アプライアンスがアップグレードされます。 アプライアンスでは既定で自動更新が有効になっているため、これは自動的に行われます。 アプライアンス サービスを手動で更新するために、この既定の設定を変更できます。

### <a name="turn-off-auto-update"></a>自動更新をオフにする

1. アプライアンスを実行しているサーバーで、レジストリ エディターを開きます。
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

いずれかのサービスに対して古いバージョンを実行している場合は、サービスをアンインストールし、最新バージョンに手動で更新する必要があります。

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

